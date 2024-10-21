# Handlebars

## Introduction

### Installation Tag

```html
<script src="https://cdn.jsdelivr.net/npm/handlebars@latest/dist/handlebars.js"></script>
```

### Common Example

```html
<script>
        var template = Handlebars.compile("Handlebars <b>{{ doesWhat }}</b>")

        console.log(template({doesWhat: "rocks!"}))
</script>
```

### with and each

#### with

The with-helper dives into an object-property, giving you access to its properties

```handlebars
{{#with person}}
{{firstname}} {{lastname}}
{{/with}}
```

#### each

The each-helper iterates an array, allowing you to access the properties of each object via simple handlebars expressions.

```html
<ul class='people_list'>
{{#each people}}
<li>{{this}}</li>
{{/each}}
</ul>
```

### Custom Helpers

Handlebars helpers can be accessed from any context in a template. You can register a helper with the Handlebars.registerHelper method.

```handlebars
{{firstname}} {{loud lastname}}
```

```javascript
Handlebars.registerHelper('loud', (aString)=>{
    return aString.toUpperCase()
})
```

### Block Helpers

Block expressions allow you to define helpers that will invoke a section of your template with a different context than the current. These block helpers are identified by a # preceeding the helper name and require a matching closing mustache, /, of the same name. Let's consider a helper that will generate an HTML list:

```javascript
Handlebars.registerHelper("list", (items, options)=>{
    const itemsAsHtml = items.map(item=> `<li>${options.fn(item)}</li>`);
    return `<ul>\n${itemsAsHtml.join("\n")}\n</ul>`;
})
```

#### Example Context

```javascript
    var template = Handlebars.compile(source);

    var context = {
        people: [
            { firstname: "John", lastname: "Doe" },
            { firstname: "Jane", lastname: "Smith" },
            { firstname: "Chris", lastname: "Evans" }
        ]
    };

    var html = template(context);
```

#### Inside Options 

```javascript
{
    data: {
        root: {
            people: [
                {firstname: 'John', lastname: 'Doe'},
                {firstname: 'Jane', lastname: 'Smith'},
                {firstname: 'Chris', lastname: 'Evans'}
            ],
            length: 3
        },
        fn: ƒ prog(context),
        hash: {},
        inverse: ƒ noop(),
        loc: {start: {line: 4, column: 17}, end: {line: 2, column: 8}},
        lookupProperty: ƒ lookupProperty(parent, propertyName),
        name: "list",
    }
}
```

#### body 

```html
<body>
    <script id="list-template" type="text/x-handlebars-template">
        {{#list people}}
            {{firstname}} {{lastname}}
        {{/list}}
    </script>

    <div id="content"></div>
</body>
```

#### full file

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/handlebars@latest/dist/handlebars.js"></script>
    <script>
        Handlebars.registerHelper('list', function(items, options) {
            console.log(items, options)
            let itemsAsHtml = items.map(item => `<li>${options.fn(item)}</li>`);
            return `<ul>\n${itemsAsHtml.join("\n")}\n</ul>`;
        });

        document.addEventListener('DOMContentLoaded', function() {
            var source = document.getElementById("list-template").innerHTML;
            
            var template = Handlebars.compile(source);

            var context = {
                people: [
                    { firstname: "John", lastname: "Doe" },
                    { firstname: "Jane", lastname: "Smith" },
                    { firstname: "Chris", lastname: "Evans" }
                ]
            };

            var html = template(context);

            document.getElementById("content").innerHTML = html;
        });
    </script>
</head>
<body>
    <script id="list-template" type="text/x-handlebars-template">
        {{#list people}}
            {{firstname}} {{lastname}}
        {{/list}}
    </script>

    <div id="content"></div>
</body>
</html>
```

### HTML Escaping

If you don't want Handlebars to escape a value, use the "triple-stash", {{{

```handlebars
raw: {{{specialChars}}}
html-escaped: {{specialChars}}
```

The special characters in the second line will be escaped:

```handlebars
raw: $ < > " ' ` =
html-escaped: &amp; &lt; &gt; &quot; &#x27; &#x60; &#x3D;
```

Handlebars.SafeString. If you write a helper that generates its own HTML, you will usually want to return a new Handlebars.SafeString(result). In such a circumstance, you will want to manually escape parameters.

```javascript
Handlebars.registerHelper('bold', (text)=>{
    var result = `<b>${Handlebars.escapeExpression(text)}</b>`;
    return new Handlebars.SafeString(result)
})
```
