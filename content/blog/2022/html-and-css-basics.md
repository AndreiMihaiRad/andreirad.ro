---
title: "HTML and CSS basics - Part 1"
date: 2022-10-08T16:56:14+03:00
draft: false
tags: ["html", "web"]
---


This is my first article related to front-end tehnologies.
I will start a new series of articles where we will go from very basics concepts of HTML to advanced web tehnologies as ReactJS.

<!--more-->

## HTML Basics

### First index.html file

- Create a file named ```index.html``` inside a folder
- Fill the file with your first html code

```html {linenos=yes}
<!DOCTYPE html>
<html>
    <head>
        <title>My First HTML Page</title>
    </head>
    <body>
        <h1>My First Web Page</h1>
    </body>
</html>
```

- Open this file in your browser, because is a HTML file you are telling your browser to render it as an HTML website.
- ```head``` tag is where you declare meta-data, title and your stile files.
- ```body``` tag is where you actually start writing your web pages codes. The visible part of HTML.
- ```title``` tag is used to define your page title.
- ```h1``` tag is used to render a heading on your page.

### Document object model (DOM)

![](/images/2022/Dom%20Tree.png)

Every document is represented as a tree, and every tree node is an object.

A few details about DOM:

- ```document``` object represents the DOM tree.
- ```<html>``` node is at the root and ```<head>``` and ```<body>``` are its children.
- The leaf nodes contain text of the document.

### section, article, div

You should use:

- ```<section>```
  - group content with related single theme
  - normally has heading and footer

```html {linenos=yes}
<section>
    <h2>Subtitle</h2>
    <p>My long paragraph</p>
</section>
```

- ```<article>```
  - represents complete, self-contained content of a page
  - can be a forum post, newspaper article, blog entry
  - its independend item of a content

```html {linenos=yes}
<article>
    <h2>Subtitle</h2>
    <p>My long paragraph</p>
</article>
```

- ```<div>```
  - does not convey any meaning
  - use it when no other element is suitable

```html {linenos=yes}
<div>
    <h2>Subtitle</h2>
    <p>My long paragraph</p>
</div>
```

### Headings

- Used to define headings
- They go from ```h1``` to ```h6```

```html {linenos=yes}
<h1>My Heading 1</h1>
<h2>My Heading 2</h2>
<h3>My Heading 3</h3>
<h4>My Heading 4</h4>
<h5>My Heading 5</h5>
<h6>My Heading 6</h6>
```

- Group content with related single theme.
- Like a subsection of long

### Paragraph

- ```<p>``` element is used for writing a paragraph of texts in HTML
- You can include ```<p>``` inside other elements like div, section, article

```html {linenos=yes}
<div>
    <p style="font-size:12px;">This is a paragraph.</p>
</div>
```

### Links / Anchor elements

- Links allow user to navigate from one page to another or from one section to another.

```html {linenos=yes}
<a href="https://www.google.com">Google</a>
```

### Images

- HTML allows you to add images on your website
- ```src``` attribute is where you specify the location of your image (either from internet or from local machine)

```html {linenos=yes}
<img src="https://via.placeholder.com/150">
```

- ```alt``` attributes provide a way to show an alternative text in case image is not available.

#### Images sizes

- You can size your image using ```width``` and ```height``` property or the ```style``` property

```html {linenos=yes}
<img src="https://via.placeholder.com/150" alt="placeholder" width="150" height="150">
<img src="https://via.placeholder.com/150" alt="placeholder" style="width:150px; height:150px;">
```

#### Image as links

- You can make image clickable using ```anchor``` tags around them.
- This can be used to navigate to another place by clicking on the image.

```html {linenos=yes}
<a href="https://www.google.com">
    <img src="https://via.placeholder.com/150" alt="placeholder" width="150" height="150">
</a>
```