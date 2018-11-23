---
title: "Random Quote Generator"
excerpt: "Random Quote Generator"
collection: portfolio
author_profile: false
classes: wide
mathjax: true
---

This website was developed as part of [FreeCodeCamp](https://www.freecodecamp.org/) as an indroduction to Javascript and API's. The full script can be examined and run from Codepen below, it's also on [Github](https://github.com/mdwcrft/web-pages/tree/master/Random%20Quote%20Machine).  


<p data-height="265" data-theme-id="0" data-slug-hash="KQwamr" data-default-tab="html,result" data-user="mdwcrft" data-pen-title="Random Quote Machine" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/mdwcrft/pen/KQwamr/">Random Quote Machine</a> by Chad (<a href="https://codepen.io/mdwcrft">@mdwcrft</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>  

## HTML
Three main divisions are defined in the HTML text; `<div class="well">`, `<button id="quotebtn" class="btn btn-lg"> Get quote </button>` and `<div id="sharebtn" class="row">`. These divisions are all wrapped in `<div class="centered">` which CSS uses to align the elements and apply padding.  

## CSS
The CSS code applies basic styling to the HTML elements mostly for proper alignment. For example the division with `id=sharebtn` is styled using:  

```CSS
#sharebtn {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
```  

## Javascript
The majority of the website functionality comes through Javascript. Three functions wrap all the code:  

```Javascript
$(document).ready(function(){   // Doc ready
  $("#quotebtn").click(function() {   // Click quote button    
    $.getJSON("https://api.forismatic.com/api/1.0/?method=getQuote&format=jsonp&lang=en&jsonp=?", function(data){   // getJSON data
```  

These lines tell the browser that once `$(document).ready`, wait for `$("#quotebtn").click`, then `$.getJSON`. The API is from [Forismatic](https://forismatic.com/en/) which hosts a library of quotes and their authors.  

The JSON data is then stored in variables, storing `qAuthor = "Anonymous"` if no author is found. The display is then formatted with `var combined = qText.replace(/;/g, ",") + " - " + qAuthor;` which is used to generate links for sharing on Facebook, Twitter, and Tumblr.  

The HTML elements with `class = "message"` and `class = "author"` are emptied and the formatted quote and author are appended:  

```Javascript
$(".message").empty().append(qText.italics());
$(".author").empty().append("-  " + qAuthor);
```  

Sharing links are then sent to the relevant sharing button, defined by their `class`.  