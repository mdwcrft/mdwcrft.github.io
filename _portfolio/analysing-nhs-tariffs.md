---
title: "Analysing NHS Tariff Changes"
excerpt: "Analysing NHS Tariff Changes"
collection: portfolio
author_profile: false
classes: wide
mathjax: true
---

As part of the [Kubrick](https://kubrickgroup.com/) training scheme I completed a project based on NHS data which used both SQL and Tableau. 

The first part of the project involved combining some dummy admissions data with real NHS tariff data to determine the real costs for each theoretical admission. The repositry containing all SQL code for part 1 can be found [here](https://github.com/mdwcrft/NHS-Tariff-Project).  

Secondly, we were to create a Tableau dashboard visualising the changes in tariff from 2017-18 to 2018-19 across all diagnosis and admission types. The final dashboard for part 2 can be found [here](https://public.tableau.com/shared/PR5KGZDXB?:display_count=yes) and is embedded at the end of this post.   
  
  
---
# Data
- [NHS Tariffs 2017/18 & 2018/19](https://improvement.nhs.uk/resources/national-tariff-1719/)  
- [HRG Grouper tool](https://digital.nhs.uk/services/national-casemix-office/downloads-groupers-and-tools/payment-hrg4-2017-18-local-payment-grouper)  
- [Hospital activity](https://www.england.nhs.uk/statistics/statistical-work-areas/hospital-activity/quarterly-hospital-activity/qar-data/)  
- Sample episode data can be found on the HRG Grouper page   
  

---
# Part 1: Real Costs by Episode
The aim for this task was to create SQL stored procedures that could be fed information about a hospital episode and return the cost.  

The first stage was to clean and format the tables that would be used. This involves changing column names & data types, trimming, removing commas from numbers, removing columns etc. An example of cleaning the admitted patient tariff data is shown below.  

```sql
SELECT [HRG code] AS [18Code]                                                           
      ,[HRG name] AS [18Name]
      ,CAST(REPLACE(NULLIF([Outpatient procedure tariff (£)], '-'),',','') AS INT) AS [18OutpatientTariff]
      ,ISNULL([Combined day case / ordinary elective spell tariff (£)], CAST(REPLACE(NULLIF([Day case spell tariff (£)], '-'),',','') AS INT) + CAST(REPLACE(NULLIF([Ordinary elective spell tariff (£)], '-'), ',','') AS INT)) AS [18CombinedTariff]
      ,CAST(REPLACE(NULLIF([Day case spell tariff (£)], '-'),',','') AS INT) AS [18DayCaseTariff]
      ,CAST(REPLACE(NULLIF([Ordinary elective spell tariff (£)], '-'), ',','') AS INT) AS [18ElectiveTariff]
      ,[Ordinary elective long stay trim point (days)] AS [18ElectiveTrim]
      ,[Non-elective spell tariff (£)] AS [18NonElectiveTariff]
      ,[Non-elective long stay trim point (days)] AS [18NonElectiveTrim]
      ,[Per day long stay payment (for days exceeding trim point) (£)] AS [18LongStayTariff]
      ,[% applied in calculation of reduced short stay emergency tariff ] AS [18ShortStayPer]
      ,[Reduced short stay emergency tariff (£)] AS [18ShortStayTariff]
INTO dbo.apc18
FROM [stage].[apc18]
GO
```

The second stage was to create a stored procedure that would accept a table of admissions and output that table with a new calculated tariff column. To do this I used a [Table Valued Parameter](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters)(TVP), a type of temporary table that can be read by a stored procedure.  

The TVP have a user defined 'type', this must contain all the columns from the admissions data that the procedure needs to calculate tariff:  
```sql
CREATE TYPE dbo.HesTableType18 AS TABLE( 
	 EpisodeID BIGINT NOT NULL
	,newnhsno VARCHAR(50)  
	,spell INT
	,episode INT
	,epidur INT
	,diag VARCHAR(10)
	,HRG VARCHAR(10)
	,classpat INT
	,admimeth VARCHAR(10)
)
```

The next stage is to create a stored procedure containing all of the logic to calculate a tariff.  

```sql
CREATE PROCEDURE dbo.usp_tariffs18
	@TVP HesTableType18 READONLY
	AS
	SET NOCOUNT ON
	CREATE TABLE FINALTariff18(EpisodeID INT, NHSNo VARCHAR(50), Spell INT, Episode INT, Epidur INT, Diag VARCHAR(10), HRG VARCHAR(20), Tariff MONEY)

	INSERT INTO FINALTariff18(EpisodeID, NHSNo, Spell, Episode, Epidur, Diag, HRG, Tariff)
	SELECT
		 EpisodeID
		,newnhsno
		,spell
		,episode
		,EPIDUR
		,diag
		,HRG
		,CASE
			WHEN EPIDUR <2 AND [emergency_t] IS NOT NULL THEN CAST([emergency_t] AS MONEY)
			WHEN [EPIDUR] > [trim] THEN CAST((([EPIDUR] - [trim]) * [longstay_t]) + CAST([base_price] AS INT) AS MONEY)
			ELSE CAST([base_price] AS MONEY)
		END
	FROM
	(
        -- GET PRICE & TRIM DAYS FOR CATEGORIES
		SELECT
			 EpisodeID
			,newnhsno
			,spell
			,episode
			,EPIDUR
			,diag
			,HRG
			,CASE
				WHEN cat = 'outpatient' THEN CAST(REPLACE([outpatient_t],',','') AS INT)
				WHEN cat = 'day-case' THEN 
					CASE 
						WHEN [daycase_t] IS NOT NULL THEN CAST(REPLACE([daycase_t],',','') AS INT)
						ELSE CAST(REPLACE([comb_t],',','') AS INT)
					END
				WHEN cat = 'elective' THEN
					CASE 
						WHEN [elective_t] IS NOT NULL THEN CAST(REPLACE([elective_t],',','') AS INT)
						ELSE CAST(REPLACE([comb_t],',','') AS INT)
					END
				WHEN cat = 'non-elective' THEN [non-elective_t]
			 END AS base_price
			,CASE
				WHEN cat = 'elective' THEN [elective_trim]
				WHEN cat = 'non-elective' THEN [non-elective_trim]
			 END AS trim
			,[longstay_t]
			,[emergency_t]
		FROM 
		(
			SELECT 
				 g.EpisodeID AS EpisodeID
				,g.newnhsno
				,g.spell
				,g.episode
				,CAST(g.[EPIDUR] AS INT) AS EPIDUR
				,g.diag
				,g.HRG AS HRG
				,CASE 
					WHEN apc.[HRG name] IS NOT NULL THEN apc.[HRG name]
					WHEN apc.[HRG name] IS NULL AND nop.[HRG name] IS NOT NULL THEN nop.[HRG name]
					WHEN apc.[HRG name] IS NULL AND nop.[HRG name] IS NULL AND ae.[HRG name] IS NOT NULL THEN ae.[HRG name]
					WHEN apc.[HRG name] IS NULL AND nop.[HRG name] IS NULL AND ae.[HRG name] IS NULL AND mat.[HRG name] IS NOT NULL THEN mat.[HRG name]
				 END AS HRG_name
				,NULLIF(apc.[Outpatient procedure tariff (£)], '-') AS [outpatient_t]
				,CASE 
					WHEN apc.[Combined day case / ordinary elective spell tariff (£)] IS NOT NULL THEN apc.[Combined day case / ordinary elective spell tariff (£)]
					WHEN apc.[Combined day case / ordinary elective spell tariff (£)] IS NULL AND nop.[HRG with no national price across all settings?] = 'Y' THEN 0
					WHEN apc.[Combined day case / ordinary elective spell tariff (£)] IS NULL AND nop.[HRG with no national price across all settings?] IS NULL AND ae.[Type 1 and 2 Departments] IS NOT NULL THEN ae.[Type 1 and 2 Departments]
					WHEN apc.[Combined day case / ordinary elective spell tariff (£)] IS NULL AND nop.[HRG with no national price across all settings?] IS NULL AND ae.[Type 1 and 2 Departments] IS NULL AND mat.[Combined day case / ordinary elective spell tariff (£)] IS NOT NULL THEN mat.[Combined day case / ordinary elective spell tariff (£)]
				 END AS [comb_t]
				,CASE
					WHEN NULLIF(apc.[Day case spell tariff (£)], '-') IS NOT NULL THEN apc.[Day case spell tariff (£)]
					WHEN NULLIF(apc.[Day case spell tariff (£)], '-') IS NULL AND NULLIF(mat.[Day case spell tariff (£)], '-') IS NOT NULL THEN mat.[Day case spell tariff (£)]
					ELSE NULL
				 END AS [daycase_t]
				,CASE
					WHEN NULLIF(apc.[Ordinary elective spell tariff (£)], '-') IS NOT NULL THEN apc.[Ordinary elective spell tariff (£)]
					WHEN NULLIF(apc.[Ordinary elective spell tariff (£)], '-') IS NULL AND NULLIF(mat.[Ordinary elective spell tariff (£)], '-') IS NOT NULL THEN mat.[Ordinary elective spell tariff (£)]
					ELSE NULL
				 END AS [elective_t]
				,CASE	
					WHEN apc.[Non-elective spell tariff (£)] IS NOT NULL THEN apc.[Non-elective spell tariff (£)]
					WHEN apc.[Non-elective spell tariff (£)] IS NULL AND mat.[Non-elective spell tariff (£)] IS NOT NULL THEN mat.[Non-elective spell tariff (£)]
				 END AS [non-elective_t]
				,CASE 
					WHEN apc.[Ordinary elective long stay trim point (days)] IS NOT NULL THEN apc.[Ordinary elective long stay trim point (days)]
					WHEN apc.[Ordinary elective long stay trim point (days)] IS NULL AND mat.[Ordinary elective long stay trim point (days)] IS NOT NULL THEN mat.[Ordinary elective long stay trim point (days)]
				 END AS [elective_trim]
				,CASE 
					WHEN apc.[Non-elective long stay trim point (days)] IS NOT NULL THEN apc.[Non-elective long stay trim point (days)] 
					WHEN apc.[Non-elective long stay trim point (days)] IS NULL AND mat.[Non-elective long stay trim point (days)] IS NOT NULL THEN mat.[Non-elective long stay trim point (days)]
				 END AS [non-elective_trim]
				,CASE 
					WHEN apc.[Per day long stay payment (for days exceeding trim point) (£)] IS NOT NULL THEN apc.[Per day long stay payment (for days exceeding trim point) (£)] 
					WHEN apc.[Per day long stay payment (for days exceeding trim point) (£)] IS NULL AND mat.[Per day long stay payment (for days exceeding trim point) (£)] IS NOT NULL THEN mat.[Per day long stay payment (for days exceeding trim point) (£)]
				 END AS [longstay_t]
				,apc.[Reduced short stay emergency tariff (£)] AS [emergency_t]
				,CASE
					WHEN CAST(EPIDUR AS INT) = 0 THEN
						CASE
							WHEN CLASSPAT = '3' THEN 'outpatient'
							WHEN CLASSPAT = '2' THEN 'day-case'
							WHEN CLASSPAT = '1' THEN
								CASE 
									WHEN NULLIF(apc.[Outpatient procedure tariff (£)], '-') IS NOT NULL THEN 'outpatient'
									ELSE 'day-case'
								END
							END
					WHEN CAST(EPIDUR AS INT) > 0 THEN
						CASE
							WHEN ADMIMETH IN ('11', '12', '13') THEN 'elective'
							WHEN ADMIMETH IN ('21', '22', '23', '24', '25','2A', '2B', '2C', '2D', '28', '31', '32', '81', '82', '83') THEN 'non-elective'
						END
					ELSE 'NA'
					END AS cat
			FROM @TVP g
			LEFT JOIN stage.apc18 apc
				ON g.HRG = apc.[HRG code]
			LEFT JOIN stage.noprice nop
				ON g.HRG = nop.[HRG code]
			LEFT JOIN stage.AE18 ae
				ON g.HRG = ae.[HRG code]
			LEFT JOIN stage.maternity18 mat
				ON g.HRG = mat.[HRG code]
		) t
) tt

```

This procedure does the following in logical order:  
1. Read admissions input data & create `FINALTariff18` table  
2. Calculate admission type based on `ADMIMETH` and `CLASSPAT`  
3. Fill in null tariff values based on other columns  
4. Get prices for each admission type  
5. Insert admissions data into table  
6. Insert tariff based on whether admission was a 'long_stay' and/or an emergency  
 
Nesting three select statements like this means multiple condition levels can be applied to the admission data to get the specific tariff required for that case. The stored procedure can then be executed with the admissions TVP as input. The output is the `FINALTariff18` table.   

```sql
DECLARE @HESTVP AS HesTableType18
INSERT INTO @HESTVP(EpisodeID, newnhsno, spell, episode, epidur, diag, HRG, classpat, admimeth)  
    SELECT 
		 EpisodeID
		,newnhsno
		,CAST(spell AS INT)
		,CAST(episode AS INT)
		,CAST(epidur AS INT)
		,diag_01
		,HRG_code
		,CAST(classpat AS INT)
		,admimeth
    FROM dbo.HES  --<--< CHANGE THIS TO CHANGE INPUT DATA
EXEC dbo.usp_tariffs18 @HESTVP
```

A stored procedure of this format was made for the 2017-18 tariffs and for the 2018-19 tariffs seperately.  
  

---
# Part 2: Visualise Tariff Changes
The second part of the project was to visualise tariff changes by diagnoses, chapter, and patient type. I chose a simple approach where the user can select diagnoses chapter and patient type to see the average change in costs, change in costs by diagnoses, and patient density by UK county.  

The dashboard is on [Tableau Public](https://public.tableau.com/views/NHSTariffs/Dashboard?:embed=y&:display_count=yes) and a screenshot is below. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/p8-dashboard.png" alt="NHS Tariff Changes Dashboard">