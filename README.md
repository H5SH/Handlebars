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

### Partials

Handlebars partials allow for code reuse by creating shared templates. You can register a partial using the registerPartial-method:

```javascript
Handlebars.registerPartial(
    "person",
    "{{person.name}} is {{person.age}} years old.\n"
)
```

The following template and input:

```handlebars
{{#each persons}}
    {{>person person=.}}
{{/each}}
```

#### Example Context

```javascript
{
    persons:[
        {name: 'Hasham', age: 23},
        {name: 'Teddy', age: 10},
        {name: 'Nelson', age: 40}
    ]
}
```

#### result

Nils is 20 years old.
Teddy is 10 years old.
Nelson is 40 years old.

### FULL FILE (Intro)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/handlebars@latest/dist/handlebars.js"></script>
    <script>

        let source = null
        let template = null

        var context = {

            people: [
                { firstname: "John", lastname: "Doe" },
                { firstname: "Jane", lastname: "Smith" },
                { firstname: "Chris", lastname: "Evans" }
            ],

            persons: [
                { name: 'Hasham', age: 23 },
                { name: 'Teddy', age: 10 },
                { name: 'Nelson', age: 40 }
            ]
        };

        Handlebars.registerHelper('list', function (items, options) {
            let itemsAsHtml = items.map(item => `<li>${options.fn(item)}</li>`);
            return `<ul>\n${itemsAsHtml.join("\n")}\n</ul>`;
        });

        Handlebars.registerPartial(
            "person",
            "{{person.name}} is {{person.age}} years old.\n"
        )

        document.addEventListener('DOMContentLoaded', () => {
            source = document.getElementById('handlebar-template').innerHTML
            template = Handlebars.compile(source)
            const html = template(context)
            document.getElementById('content').innerHTML = html;
        });

    </script>
</head>

<body>
    <script id="handlebar-template" type="text/x-handlebars-template">
        {{#list people}}
            {{firstname}} {{lastname}}
        {{/list}}

        {{#each persons}}
            {{>person person=.}}
        {{/each}}
    </script>

    <div id="content"></div>
</body>
</html>
```

## Built-in Helpers

### if

falsy values: false, undefined, null, "", 0 or []

#### demo context

```javascript
{
    author: true,
    firstName: "Yehuda",
    lastName: "Katz"
}
```

#### Syntax

```html
<div class='entry'>
    {{#if author}}
    <h1>{{firstName}} {{lastName}}</h1>
    {{/if}}
</div>
```

#### includeZero

```html
{{#if 0 includeZero=true}}
<h1>Does Render</h1>
{{/if}}
```

#### Sub-expression using helper

```handlebar
{{#if (isdefined value)}}true{{else}}false{{/if}}
```

### unless

You can use the unless helper as the inverse of the if helper. Its block will be rendered if the expression returns a falsy value.

#### Syntax

```html
<div class='entry'>
    {{#unless license}}
    <h3 class='warning'>WARNING: This entry does not have a license!</h3>
    {{/unless}}
</div>
```

### with

The with-helper dives into an object-property, giving you access to its properties

```handlebars
{{#with person}}
{{firstname}} {{lastname}}
{{/with}}
```

Also

```handlebars
{{#with city as | city |}}
    {{#with city.location as | loc |}}
        {{city.name}}: {{loc.north}} {{loc.east}}
    {{/with}}
{{/with}}
```

### each

The each-helper iterates an array, allowing you to access the properties of each object via simple handlebars expressions.

```html
<ul class='people_list'>
{{#each people}}
<li>{{this}}</li>
{{/each}}
</ul>
```

#### options

|   option   |           function            |
|------------|-------------------------------|
| {{@index}} |       current loop index      |
|  {{@key}}  | reference of current key name |
| {{@first}} |    first step of iteration    |
| {{@last}}  |    last step of iteration     |

### lookup

The lookup helper allows for dynamic parameter resolution using Handlebars variables.
This is useful for resolving values for array indexes.

```handlebars
{{#each people}}
    {{.}} lives in {{lookup ../cities @index}}
{{/each}}
```

More Complex

```handlebars
{{#each persons as | person |}}
    {{name}} lives in {{#with (lookup ../cities [resident-in])~}}
        {{name}} ({{country}})
    {{/with}}
{{/each}}
```

### lookup

The lookup helper allows for dynamic parameter resolution using Handlebars variables.

```handlebars
{{#each people}}
    {{.}} lives in {{lookup ../cities @index}}
{{/each}}
```

#### More Complex and tested example

##### Demo Data

```javascript
const context = {
            cities: [
                {
                    name: 'Lahore',
                    code: 'LAH'
                },
                {
                    name: 'Karachi',
                    code: 'KAR'
                },
                {
                    name: 'Islamabad',
                    code: 'ISL'
                },
                {
                    name: 'Sialkot',
                    code: 'SKT'
                },
                {
                    name: 'Faislabad',
                    code: 'FSD'
                }
            ],
            
            users: [
                {
                    name: 'John Doe',
                    email: 'john.doe@example.com',
                    cart: [
                        { name: 'Laptop', price: 1200 },
                        { name: 'Smartphone', price: 800 }
                    ],
                    wishlist: [
                        { name: 'Smartwatch', price: 300 }
                    ],
                    orders: [
                        {
                            items: [
                                { name: 'Tablet', price: 400 }
                            ],
                            total: 400,
                            date: '2024-09-01',
                            status: 'Delivered'
                        }
                    ],
                    preferences: {
                        notifications: ['orderUpdates', 'promoEmails'],
                        currency: 'USD'
                    },
                    "resident-in": 1
                },
                ...
            ]

}
```

```html
{{#each users as | user |}}
    {{#with user}}
        {{#with (lookup ../cities [resident-in])~}}
                <p>City: {{name}} Code: {{code}}</p>
        {{/with}}
    {{/with}}
{{/each}}
```

Output

```output
City: Karachi Code: KAR
```

### log

The log helper allows for logging of context state while executing a template.

```html
{{log 'firstname' firstname 'lastname' name}}
```

Logging is conditional based on the level and to value set in Handlebars.logger.level, which defaults to info. All log statements at or above the current level will be output.

```html
{{log "debug logging" level="debug"}}
{{log "info logging" level="info"}}
{{log "info logging is the default"}}
{{log "logging a warning" level="warn"}}
{{log "logging an error" level="error"}}
```

```javascript
Handlebars.logger.level = 'error'
console.log('Current log level: ', Handlebars.logger.level, '\n---')
```

## FULL FILE

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/handlebars@latest/dist/handlebars.js"></script>
    <script>

        let source = null
        let template = null

        var context = {

            people: [
                { firstname: "John", lastname: "Doe" },
                { firstname: "Jane", lastname: "Smith" },
                { firstname: "Chris", lastname: "Evans" }
            ],

            persons: [
                { name: 'Hasham', age: 23 },
                { name: 'Teddy', age: 10 },
                { name: 'Nelson', age: 40 }
            ],

            testingIf: {
                author: false,
                firstName: "Yehuda",
                lastName: "Katz"
            },

            cities: [
                {
                    name: 'Lahore',
                    code: 'LAH'
                },
                {
                    name: 'Karachi',
                    code: 'KAR'
                },
                {
                    name: 'Islamabad',
                    code: 'ISL'
                },
                {
                    name: 'Sialkot',
                    code: 'SKT'
                },
                {
                    name: 'Faislabad',
                    code: 'FSD'
                }
            ],

            users: [
                {
                    name: 'John Doe',
                    email: 'john.doe@example.com',
                    cart: [
                        { name: 'Laptop', price: 1200 },
                        { name: 'Smartphone', price: 800 }
                    ],
                    wishlist: [
                        { name: 'Smartwatch', price: 300 }
                    ],
                    orders: [
                        {
                            items: [
                                { name: 'Tablet', price: 400 }
                            ],
                            total: 400,
                            date: '2024-09-01',
                            status: 'Delivered'
                        }
                    ],
                    preferences: {
                        notifications: ['orderUpdates', 'promoEmails'],
                        currency: 'USD'
                    },
                    "resident-in": 1
                },
                {
                    name: 'Jane Smith',
                    email: 'jane.smith@example.com',
                    cart: [
                        { name: 'Camera', price: 900 }
                    ],
                    wishlist: [
                        { name: 'Drone', price: 1200 },
                        { name: 'Lens', price: 400 }
                    ],
                    orders: [
                        {
                            items: [
                                { name: 'Smartphone', price: 700 }
                            ],
                            total: 700,
                            date: '2024-09-15',
                            status: 'Processing'
                        }
                    ],
                    preferences: {
                        notifications: ['promoEmails'],
                        currency: 'EUR'
                    },
                    "resident-in": 2
                },
                {
                    name: 'Michael Johnson',
                    email: 'michael.johnson@example.com',
                    cart: [
                        { name: 'Gaming Console', price: 500 },
                        { name: 'Controller', price: 60 }
                    ],
                    wishlist: [
                        { name: '4K TV', price: 1500 }
                    ],
                    orders: [],
                    preferences: {
                        notifications: ['orderUpdates'],
                        currency: 'GBP'
                    },
                    "resident-in": 1
                },
                {
                    name: 'Emily Davis',
                    email: 'emily.davis@example.com',
                    cart: [],
                    wishlist: [
                        { name: 'Headphones', price: 200 },
                        { name: 'Bluetooth Speaker', price: 150 }
                    ],
                    orders: [
                        {
                            items: [
                                { name: 'Laptop', price: 1000 }
                            ],
                            total: 1000,
                            date: '2024-08-28',
                            status: 'Shipped'
                        }
                    ],
                    preferences: {
                        notifications: ['orderUpdates', 'productRecommendations'],
                        currency: 'AUD'
                    },
                    "resident-in": 4
                },
                {
                    name: 'David Lee',
                    email: 'david.lee@example.com',
                    cart: [
                        { name: 'Electric Guitar', price: 1200 }
                    ],
                    wishlist: [],
                    orders: [
                        {
                            items: [
                                { name: 'Acoustic Guitar', price: 800 }
                            ],
                            total: 800,
                            date: '2024-09-10',
                            status: 'Delivered'
                        }
                    ],
                    preferences: {
                        notifications: ['promoEmails', 'orderUpdates'],
                        currency: 'CAD'
                    },
                    "resident-in": 5
                }
            ]
        };

        Handlebars.registerHelper('list', function (items, options) {
            let itemsAsHtml = items.map(item => `<li>${options.fn(item)}</li>`);
            return `<ul>\n${itemsAsHtml.join("\n")}\n</ul>`;
        });

        Handlebars.registerPartial(
            "person",
            "{{person.name}} is {{person.age}} years old.\n"
        )

        Handlebars.registerPartial(
            "itemrow",
            "<strong>{{item.name}}</strong>: {{item.price}},"
        )

        Handlebars.registerPartial(
            "orderrow",
            "<p>{{order.total}}, Ordered On: {{order.date}} <strong>{{order.status}}</strong></p>"
        )

        Handlebars.registerPartial(
            "currency",
            "Currency: {{currency}}"
        )

        Handlebars.registerPartial(
            "city",
            "City: {{lookup ../cities index}}"
        )

        Handlebars.registerHelper('length', (list)=> list.length)

        document.addEventListener('DOMContentLoaded', () => {
            source = document.getElementById('handlebar-template').innerHTML
            template = Handlebars.compile(source)
            const html = template(context)
            document.getElementById('content').innerHTML = html;
        });

    </script>
</head>

<body>
    <script id="handlebar-template" type="text/x-handlebars-template">
        {{#list people}}
            {{firstname}} {{lastname}}
        {{/list}}

        {{#each persons}}
            {{>person person=.}}
        {{/each}}

        {{#if testingIf.author}}
            {{#with testingIf}}
                <h3>{{firstName}} {{lastName}}</h3>
            {{/with}}
            {{else}}
            <h1>No Author</h1>
        {{/if}}

        {{#if 0 includeZero=true}}
            <h1>Does Render</h1>
        {{/if}}

        {{#each users as | user |}}
            {{#with user}}
                <h1>{{name}}</h1>
                <h2>{{email}}</h2>
                <h3>Cart:</h3>
                {{#each cart}}
                    {{>itemrow item=.}}
                {{/each}}
                <h3>Wishlist:</h3>
                {{#each wishlist}}
                    {{>itemrow item=.}}
                {{/each}}
                <h3>{{#if orders}}Orders{{/if}}</h3>
                {{#each orders as | order |}}
                    {{#each order.items}}
                        {{>itemrow item=.}}
                    {{/each}}
                    {{>orderrow order=order}}
                {{/each}}
                <ul>
                    {{#each preferences.notifications}}
                        <li>{{this}}</li>
                    {{/each}}
                </ul>
                {{>currency currency=preferences.currency}}
                    {{#with (lookup ../cities [resident-in])~}}
                        <h5>City: {{name}} Code: {{code}}</h5>
                    {{/with}}
                {{/with}}
            {{/each}}
        {{log "users" users}}
    </script>

    <div id="content"></div>
</body>

</html>
```