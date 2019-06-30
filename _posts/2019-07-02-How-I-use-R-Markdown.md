---
layout: post
title: Using R Markdown in my day to day
subtitle: Financial report of my condominium
gh-repo: cauancardoso/cauancardoso.github.io
gh-badge: [star, fork, follow]
tags: [R, R Markdown]
comments: true
---

This year, I've been responsible for the monthly financial report of the condominium where I live in Brasilia. Here I will show how I've automated this task using [**R Markdown**](https://rmarkdown.rstudio.com/) and [**R Studio**](https://www.rstudio.com/).

First things first, I'm using R version 3.6.0 and R Studio version 1.2.1335.

**R version:** 3.6.0  
**R Studio version:** 1.2.1335  
{: .box-note}

I'm also using [Basic Miktex](https://miktex.org/) (version 2.9) to generate my PDF documents. 

If you're experiencing trouble generating PDF documents in R Markdown using Miktex, [maybe this solution will work for you](https://tex.stackexchange.com/questions/27138/how-can-i-fix-the-error-gui-framework-cannot-be-initialized-with-texniccenter).  
{: .box-warning}

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

**Here is some bold text**

## Here is a secondary heading

Here's a useless table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |


How about a yummy crepe?

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
