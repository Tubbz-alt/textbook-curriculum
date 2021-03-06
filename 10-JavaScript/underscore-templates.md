# Underscore for HTML Generation

## Motivation
- One of the best things about Rails was using ERB to generate HTML.
- Underscore provides similar functionality for JavaScript.
- Backbone relies on Underscore, and the two libraries are often mixed together. Learning Underscore first will help us understand which is which.

## Learning Goals
- Use Underscore templates to generate HTML in JavaScript
- Compare and contrast Underscore templating and ERB

## Underscore
Underscore is a nifty utility library that provides a lot of functionality missing from core JavaScript. It has a lot of features, but today we'll be focusing on only one: templating.

Think back to Rails, and ERB. ERB gave us a straightforward way to generate complex HTML structures, by embedding Ruby code right in the document. This helped keep our view code DRY, and kept us from generating tons of HTML by hand in our controllers. Underscore gives us similar functionality in JavaScript.

Similar to jQuery's `$`, all Underscore functions are accessed through the global `_` object.

### The Three Steps to Underscore Templating:

- Define the template in your HTML file
- Compile the template once in your JavaScript
- Invoke the compiled template to generate HTML

### Running Example
The data we'll be working with is a TODO list. Here is some sample data:

```javascript
const todoData = [
  {
    title: 'Mow the lawn',
    description: 'Must be finished before BBQ on Sat afternoon',
    assignedTo: ['Kari', 'Charles'],
  },
  {
    title: 'Go to the Bank',
    description: 'Need to make a transfer',
    assignedTo: ['Dan', 'Jamie', 'Chris'],
  },
];
```

Our goal is to have an easy way to generate the HTML for one of these tasks, something like:

```html
<section>
  <h2>
    Mow the lawn
  </h2>
  <p>
    <strong>Description:</strong>
    Must be finished before BBQ on Sat afternoon
  </p>
  <p>
    <strong>Assigned To:</strong>
    Kari Charles
  </p>
</section>
```

### Defining a Template
Template definitions live in your HTML. They're wrapped in a `<script>` tag that looks something like this:

```html
<script id="todo-item-template" type="text/template">
</script>
```

The `<script>` tag has two attributes. First is `id` - this is what we'll use to find the template later. Second is `type`, which is set to `text/template`. Turns out `text/template` is a made up type. The browser doesn't know what to do with it, so it ignores it. This suits us perfectly, since we don't want the template to render until we're ready.

Right now our template is empty, so our next step is to add some contents. Underscore's syntax is inspired by ERB, so this ought to look familiar.

```html
<script id="todo-item-template" type="text/template">
  <section>
    <h2>
      <%- title %>
    </h2>
    <p>
      <strong>Description:</strong>
      <%- description %>
    </p>
    <p>
      <strong>Assigned To:</strong>
      <% assignedTo.forEach((person) => { %>
        <%- person %>
      <% }); %>
    </p>
  </section>
</script>
```

Like ERB, freestanding HTML and text will be copied verbatim. Several different special tags can be used to wrap JavaScript:
- `<%= %>` Execute whatever JavaScript is inside the tags, turn the result into a string, and insert it into the HTML
- `<% %>`  Executes JavaScript without adding anything to the HTML
- `<%- %>` Acts like `<%=`, except the string is HTML-escaped before being inserted

Any JavaScript variables accessed in any of these tags will need to be passed in later, when we _invoke_ the template.

What's the difference between `<%=` and `<%-`? Consider the following template:

```javascript
<%= '<em>italic?</em>' %>
<%- '<em>italic?</em>' %>
```

For the first line, the string will be inserted into the document as-is, so that the browser will render it as "_italic?_".

For the second line, the string is HTML-escaped before being added, which means things like angle brackets will print out. The browser would render it as "&lt;em&gt;italic?&lt;em&gt;".

**Question:** Which should you make a habit of using, and why?

### Compiling the Template
The next step is to _compile_ the template. This is accomplished using Underscore's `template` method. The template only needs to be compiled once, and can be used to generate HTML many times, so put the compilation somewhere that only happens once.

```javascript
$(document).ready(() => {
  const todoTemplate = _.template($('#todo-item-template').html());
});
```

It's only one line but there's a lot going on here, so let's break it down.

First, notice that we've put this bit of code in `$(document).ready`. Even though our template is being ignored by the browser, it's still part of the document, which means we can't be sure we'll have access to it unless we wait for `$(document).ready`.

Next, lets look at the big structure of the line:

```javascript
const todoTemplate = _.template(/* some stuff */);
```

Here we're calling `_.template`, Underscore's template compiler, and saving the result in a variable named `todoTemplate`.

**Question:** Why do we make the compiled template `const`?

Last but not least, the arguments to `_.template`:

```javascript
$('#todo-item-template').html()
```

We've seen this before - it's not Underscore at all, but good old jQuery. We select the element with ID `todo-item-template`, and get its contents.

### Using the Template
Turns out the compiled template is a function. Underscore uses some fancy closure work to make sure it has access to everything it needs, but we don't need to worry about that. The basic pattern is: call the generated function, get back some HTML.

The template function takes one argument, a hash of variables to make available in the template. Invoking the template looks like this:

```javascript
todoData.forEach((todo) => {
  const generatedHtml = todoTemplate(todo);
  $('#todo-list').append($(generatedHtml));
});
```

Note that Underscore doesn't assume we have access to jQuery. That means the return value from the template is a vanilla JavaScript DOM object. If you want to do fancy jQuery things with it, you'll have to pass it through `$`, like we do here.

Here, we're appending our generated HTML to some element we're targetting with jQuery. Let's create a blank element that we can target with `#todo-list` in our HTML.

```html
<div id="todo-list"></div>
```

Now we should be able to see that in JavaScript we iterate through our data, generate some HTML using a function that uses `_.template()`, and add that generated HTML to some (empty) element on our HTML.

### Putting It All Together
[Here is a CodePen](https://codepen.io/adadev/pen/zPLJzw?editors=1011) that does all the things we just talked about!

### Adding
Note that we've added the Underscore library to our CodePen via the CodePen library add-ins. To add the Underscore library to your JavaScript file you'll need to add a `<script>` tag to your HTML file which will link to the [Underscore CDN](https://cdnjs.com/libraries/underscore.js/).

## What We've Accomplished
- Define, compile and use an underscore template

## Additional Resources
- [Underscore documentation](http://underscorejs.org/)
- [SitePoint Underscore tutorial](https://www.sitepoint.com/getting-started-with-underscore-js/)
- [Stackoverflow example of using an if-statement in a template](http://stackoverflow.com/questions/7230470/how-to-use-if-statements-in-underscore-js-templates)
- [Handlebars: An alternative templating engine](http://handlebarsjs.com/)
