---
title: "HTML and CSS basics - Part 2"
date: 2022-10-10T16:56:14+03:00
draft: false
tags: ["html", "web"]
---

Second article of our FE series. Time to cover some CSS basics and how we can style our webpages.

<!--more-->

In this article we'll create a gift card using html and css code.

## CSS File

- Create ```style.css``` file inside ```app``` folder, created in the [first article](https://andreirad.ro/blog/2022/html-and-css-basics/)
  - here we'll store all styles used in our html.

### Add style in ```index.html``` file

- You have to use the link tag to include your style file inside your HTML file

```html {linenos=yes}
<head>
    <meta charset="utf-8" />
    <title>Birthday Gift Card</title>
    <link rel="stylesheet" type="text/css" href="style.css" />
</head>
```

- Now let's add body for our Birthday Card

```html {linenos=yes}
<body>
    <div class="card">
        <h1 class="quote">You're the CSS to my HTML</h1>
        <h1 class="message">Happy Birthday Day</h1>
    </div>
</body>
```

- What is important to note here is ```classes``` we added to our HTML so that we can style them later

### Add first syle

- We're using ```.card``` - class selector to grab the card DIV and style it
- Here we are just setting a red ```border: 10px solid #E53038;```
- ```height: 100vh;``` is done to match out body tag's height - which is the full view-port height.
- ```display: flex;``` makes this ```card``` DIV a flex-box.
  - We are just making all our ```flex-children``` align in vertically and horizontally center position in one column.
  - NOTE: We will learn about flex-box in the later section.

### Selectors

![](/images/2022/css_selector.png)

There are multiple type of selectors we'll cover below.

- selectors are used to grab a DOM element to apply styles to it.
- the image shows a ```tag``` selector
  - We are grabbing all the ```<p>``` tags in our HTML document and applying it red color and margin of 3px.

#### .class

- Selects all elements with given class name

```html {linenos=yes}
<p class="myClass">This is a paragraph</p>

.myClass {
    background-color: yellow;
}
```

### child .class

- You can target child element using class hierarchy

```html {linenos=yes}
.parent .child {
    background-color: yellow;
}
```

- Below example will add ```background-color: yellow``` to the paragraph

```html {linenos=yes}
<div class="parent">
    <p class="child">This is a paragraph</p>
</div>
```

### #id

- Syle the element with given ```id``` attribute
- Select the elements by adding ```#``` followed by the ```id``` attribute of the element

```html {linenos=yes}
<p id="myParagraph">This is a paragraph</p>

#myParagraph {
    background-color: yellow;
}
```

### element tag

- You can directly select an element by its tag name and apply the style
- In this case you don't have to mention ```id``` or the ```class``` - simply write the element name in your styles and add properties to it
  
```html {linenos=yes}
<p>This is a paragraph</p>

p {
    background-color: yellow;
}
```

### Mix n match

- You can also mix and match any of the above selectors to apply the styles

#### Id and Class

- ```id="myParagraph"``` forms the Id selector
- ```class="myClass"``` forms the class selector

```html {linenos=yes}
<p id="myParagraph" class="myClass">This is a paragraph</p>

#myParagraph.myClass {
    background-color: yellow;
}
```

#### Element and Class

- ```p``` is used as element selector
- ```class="myClass"``` forms the class selector

```html {linenos=yes}
<p class="myClass">This is a paragraph</p>

p.myClass {
    background-color: yellow;
}
```

## Backgrounds

- You can set different background of your elements
- Background of an element is the total size of the element, including padding and border (but not the margin)
- Background properties
  - background-color
  - background-image
  - background-position
  - background-size
  - background-repeat
  - background-origin
  - background-clip
  - background-attachment

```html {linenos=yes}
body { 
    background: lightblue url("myImage.png") no-repeat center;
}
```

## Colors

- The color property speci􀁻es the color of the text
- You can specify the color property on diferent elements by using diferent types of selectors
- You can specify colors by its name, hex value or RGB value

```css {linenos=yes}
h1 {
    color: red;
}

h1.myClass {
    color: #02af00;
}

h1#myId {
    color: rgb(111,111,111);
}
```

## Borders

- You can add borders to your HTML elements
- Below is the list of all the border properties
  - border-width
  - border-style (required)
  - border-color

```html {linenos=yes}
h1 {
    border: 5px solid red;
}
```

- ```5px``` is the border width
- ```solid``` is the border style
  - Other options are ```dotted```, ```double```, ```dashed```
- ```red``` is the border-color
