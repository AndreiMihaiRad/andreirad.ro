---
title: "HTML and CSS basics - Part 3"
date: 2022-10-12
draft: false
tags: ["html", "web"]
---

This the third article of the series. It's time to go deeper into CSS and finding out how we can ```display and position elements``` in a web page.

<!--more-->

## Box model

![](/images/2022/css_box_model.png)

- Image above is very descriptive, ```box model``` wraps around very HTML element.
- It has margins, borders, paddings and the actual content.

```html {linenos=yes}
div {
    width: 320px;
    padding: 10px;
    border: 5px solid gray;
}
```

- 20px (left + right padding)
- 10px (left + right border)
- 0px (left + right margin)
    
= 350px

**Total element width** = width + left padding + right padding + left border +
right border + left margin + right margin

**Total element height** = height + top padding + bottom padding + top border + bottom border + top margin + bottom margin

### Margins

- Margins are used to create space around elements - outside its border.

```html {linenos=yes}
div>
    <p class="myParagraph">My Paragraph</p>
</div>

// styles

.myParagraph {
    margin: 20px;
}
```

- in the style above we give to the element ```p``` a margin of ```20px``` around it from all the sides.

- there is possibility to configure margin to the elements on any particular side
  - margin-top
  - margin-right
  - margin-bottom
  - margin-left

### Auto Margin

- ```auto``` value of margin sets the element horizontally center within its container
- Below ```div``` will take ```200px``` width and remaining space will be split equally between left and right margin

```html {linenos=yes}
div {
    width: 200px
    margin: auto;
}
```

### Paddings

- Paddings are used to generate space around the given element's content - inside its border.

```html {linenos=yes}
<div class="myDiv">
    <p>My Paragraph</p>
</div>

// styles
.myDiv {
    padding: 20px;
}
```

- In he example above will be 20px space between p and div on all the sides.
- You can also give padding to the elements on any particular side:
  - padding-top
  - padding-right
  - padding-bottom
  - padding-left

## Display

![](/images/2022/css_display_property.png)

### Block

- This property stretches the element left to right as far as it can.
- Eevery item stretches and uses up the entire row.

### Inline

- Inline element sits in line without disrupting the flow of other elements.
- Every item takes up only the space it needs.
  - Item wraps to the next row if there is no enough space.
- ```span, em, b``` are examples of inline elements.
- They do not honor vertical padding
  - No width
  - No height
- Horizontal margin and padding are respected.
- Vertical margin and padding are ignored.

### Inline-block

- Like inline element **BUT** they will respect the width and height.
- They combine the properties of both block elements and inline elements.
- We can observe in the illustration that if you set width you can fit all the items together in a single row.

### None

- These elements will not appear on the page at all
- But you can still interact with it through DOM
- NO space allocated on the page

### Visibility Hidden

- Space is allocated for it on the page
- Tag is rendered on the DOM
- The element is just no visible

### Flex

- Flex property gives ability to alter its item's width/height to best fill the available space
- It is used to accommodate all kind of display devices and screen sizes.

## Positions

![](/images/2022/css_position.png)

### Static

- Default for every element.
- As shown in the illustrations blocks just falls in their default position.

### Relative

```html {linenos=yes}
<div class=myDiv""></div>

.myDiv{
    position: relative;
    top: 10px;
    left: 10px;
}
```

- The element is relative to itself.
- Now, it will slide down and to the left by 10px from where it would normally be.
- Without that "top" property - it would have just followed position: static.

### Absolute

- It lets you place element exactly where you want
- Using top, left, bottom. and right to set the location
- **IMPORTANT!!** - these values are relative to its parent
  - Where parent is absolute or relative
  - If there is no such parent then it will check back up to HTML tag and place it absolute to the
webpage itself
- Not a􀁸ected by other elements
- And it doesn't a􀁸ect other elements

```html {linenos=yes}
<div class=myDiv""></div>

.myDiv{
    position: absolute;
    top: 10px;
    left: 10px;
}
```

- The above ```div``` will slide down and to the left by ```10px``` from its parent.

### Fixed

- Positioned relative to the viewport
- Useful in 􀁻xed headers or footers
- Please refer the illustration above for better visualization

```html {linenos=yes}
<div class=myDiv""></div>

.myDiv{
    position: fixed;
}
```

## Flexbox - Intro

- It provides eficient way to lay out, align and distribute space among items in a container
- The idea is to give container the ability to alter its items' width/height (and order) to best fill
the available space.

### Flex box container properties

- ```display: flex```
- ```flex-direction: row | row-reverse | column | column-reverse;```
- ```flex-wrap: nowrap | wrap | wrap-reverse;```
- ```justify-content```
- ```align-items```

### Flex box item properties

- ```order: <integer>; /* default is 0 */```
- ```flex-grow: <number>; /* default 0 */```
- ```flex-shrink: <number>; /* default 1 */```
- ```flex-basis: <length> | auto; /* default auto */```
- ```flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]```
- ```align-self```

## Media queries

- It was introduced in ```CSS3```
- It uses ```@media``` rule to include CSS only if a certain condition is true

![](/images/2022/media_queries.png)

- Media queries allows to apply css style based on platform your application is running (mobile, pc, etc). This is how you write responsive styles.

```html {linenos=yes}
// If the browser window is smaller than 500px, the background color will
change to lightblue:

@media only screen and (max-width: 500px) {
    body {
        background-color: lightblue;
    }
}
```

## ```px``` vs ```em``` vs ```rem```

- **!Important:** ```Pixels``` are ignorant, avoid using them.
- ```REMs``` are a way of setting font-sizes based on the font-size of the root HTML element
  - Allows you to quickly scale an entire project by changing the root font-size
- ```em``` is relative to the font size of its direct or nearest parent
  - When you have nested styles it becomes di􀁹cult to track ```ems```
  - This is what REMs solve - the size always refers back to the root
- Both ```pixels``` and ```REMs``` for media queries fail in various browsers when using browser zoom, and ```EMs``` are the best option we have.

