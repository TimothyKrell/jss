## Dynamic stylesheets for web components.

Modern web applications need expressive language for styles description. This project takes a fresh look at the idea of writing stylesheets in javascript. It solves some major problems with css:

1. Cascading style sheets do not scale. There are some solutions like [bem](http://bem.info/) which solve this problem, however they introduce an overhead of writing long class names. Here is how [true namespaces](http://kof.github.io/jss/examples/namespace/index.html) can look like in jss.

1. We often need to calculate layouts in javascript. For this we often need to access properties defined in stylessheets. Jss allows us to do it easy and without DOM round trip by giving us a [direct access](http://kof.github.io/jss/examples/commonjs/index.html) to values and variables from javascript allows

1. Its up to you whether to put some styles via stylesheet or to [apply them directly](http://kof.github.io/jss/examples/jquery/index.html) to the element.

1. Optimize your app performance by [detaching](http://kof.github.io/jss/examples/simple/index.html) unused stylesheets from render tree.

1. Use full power of expressive full featured language. Any features you might know from [stylus](http://learnboost.github.io/stylus/) or [sass](http://sass-lang.com/) and some more are already available.

1. Evil mixins are not a problem any more.

1. No need to precompile, but you can if you want to.

Take a look at [examples](http://kof.github.io/jss/examples/index.html) directory.

## Built in preprocessors

Jss styles are just plain javascript objects. They map 1:1 to css rules, except of those modified by preprocessors.

### Numeric values

Numeric values will get 'px' suffix.

```javascript
{
    '.container': {
        padding: 20
    }
}
```
```css
.container {
    padding: 20px;
}
```

### Nested Rules

Put an ampersand before a selector within a rule and it will be converted to a separate rule with a [nested selector.](http://kof.github.io/jss/examples/nested/index.html)


```javascript
{
    '.container': {
        padding: 20,
        // Will result in .container.clear
        '&.clear': {
            clear: 'both'
        },
        // Will result in .container .button
        '& .button': {
            background: 'red'
        }
    }
}
```
```css
.container {
    padding: 20px;
}
.container.clear {
    clear: both;
}
.container .button {
    background: red;
}
```

### Extend keyword

Add `extend` keyword to a rule and set any style as value to extend some style definition with the current style object. [See example.](http://kof.github.io/jss/examples/extend/index.html)


```javascript
var rules = {}

rules['.button-1'] = {
    padding: 20,
    background: 'blue'
}

rules['.button-2'] = {
    extend: rules['.button-1'],
    padding: 30
}
```
```css
.button-1 {
    padding: 20px;
    background: blue;
}

.button-2 {
    padding: 30px;
    background: blue;
}
```

## API

### Access the jss namespace

```javascript
// Pure js
var jss = window.jss

// Commonjs
var jss = require('jss')
```

### Create stylesheet `jss.createStylesheet([rules], [attributes])`

Create stylesheet, optionally pass style element attributes, returns a Stylesheet instance.

```javascript
var stylesheet = jss.createStylesheet({
    '.selector': {
        width: 100
    }
}, {media: 'print'})
```

### Attach stylesheet `stylesheet.attach()`

Insert stylesheet into render tree.

```javascript
stylesheet.attach()
```

### Detach stylesheet `stylesheet.detach()`

Remove stylesheet from render tree for performance optimization.

```javascript
stylesheet.detach()
```

### Add a rule `stylesheet.addRule([selector], rule)`

#### You might want to add rules dynamically.

```javascript
var button = stylesheet.addRule('.my-button', {
    padding: 20,
    background: 'blue'
})
```
#### Generated namespace.

In case you have an element reference or you create elements in javascript you might want to write styles and attach them later to the element using a generated class name.

```javascript
var button = stylesheet.addRule({
    padding: 20,
    background: 'blue'
})

document.body.innerHTML = '<button class="' + button.className + '">Button</button>'
```

### Get a rule `stylesheet.getRule(selector)`

```javascript
var rule = stylesheet.getRule('.my-button')
```

### Create a rule `jss.createRule([selector], rule)`

In case you want to create a rule without to add it to a stylesheet.

```javascript
var rule = jss.createRule({
    padding: 20,
    background: 'blue'
})

$('.container').css(rule.style)
```
