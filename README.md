# react-i18nliner

react-i18nliner brings [I18nliner](https://github.com/jenseng/i18nliner-js)
to React via the [html `translate` attribute](http://www.w3.org/International/questions/qa-translate-flag). I18n doesn't get any easier than this.

## TL;DR

react-i18nliner lets you do this:

```html
<p translate="yes">
  Hey {this.props.user.name}!
  Although I am <Link to="route">linking to something</Link> and
  have some <strong>bold text</strong>, the translators will see
  <strong><em>absolutely no markup</em></strong> and will only have a
  single string to translate :o
</p>
```

Write your components as you normally would, and just put a
`translate="yes"` attribute on any element/component that needs to be
localized.

Best of all, you don't need to maintain translation files anymore;
I18nliner will do it for you.

## How does it work?

react-i18nliner preprocesses your JSX, transforming it into something
truly localizable. It infers placeholders for expressions and
[wrappers](https://github.com/jenseng/i18nliner-js)
for elements/components, and separates the localizable string. At runtime,
it localizes the string, interpolating the wrappers and placeholders into
the correct locations.

react-i18nliner also adds an extractor to I18nliner, so that you can
extract all translatable strings from your codebase. Once you get them
translated, just put them on `window.I18n.translations` and everything
will Just Work™.

## Installation

### 1. get i18n-js and i18nliner

Get i18n-js and i18nliner installed [per these instructions](https://github.com/jenseng/i18nliner-js#installation).

### 2. add react-i18nliner

```bash
npm install react-i18nliner --save
```

And make sure your `.i18nrc` file has:

```json
{
  "plugins": [
    "react-i18nliner"
  ]
}
```

This will ensure that when you export strings for translation, all of your
new `translate="yes"` stuff will get picked up.

### 3. preprocess your all your js files with react-i18nliner

How you hook up the preprocessor will depend on how you bundle your assets:

#### webpack

Add [this loader](https://github.com/jenseng/react-i18nliner/blob/master/webpack-loader.js)
to your config, e.g.

```js
{
  module: {
    loaders: [
      { test: /\.js$/, loader: "react-i18nliner/webpack-loader" }
      ...
    ],
  },
  ...
}
```

#### browserify

Use [this transform](https://github.com/jenseng/react-i18nliner/blob/master/browserify-transform.js),
e.g.

```bash
$ browserify -t react-i18nliner/browserify-transform app.js > bundle.js
```

#### something else?

It's not too hard to roll your own; as you can see in the loader and
transform above, the heavy lifting is done by `preprocess`. So whether
you use ember-cli, sprockets, grunt concat, etc., it's relatively
painless to add a little glue code that runs preprocess on each
source file.

### 4. add the react-i18nliner runtime extensions to i18n-js

Assuming you have a cjs-style app, do something like this:

```js
var I18n = require("./path/to/cjs'd/i18n");
require("i18nliner/dist/lib/extensions/i18n_js")["default"](I18n);
require("react-i18nliner/extensions/i18n_js")(I18n);
```

If you're using AMD/`<script>`/something else, see the [i18nliner-js README](https://github.com/jenseng/i18nliner-js#installation)
for hints; these extensions can be set up exactly the same way as
i18nliner-js's.

## Working with translations

Since react-i18nliner is just an i18nliner plugin, you can use the
i18nliner bin / grunt task to extract translations from your codebase;
it will pick up normal `I18n.t` usage, as well as your new
`translate="yes"` components.

Once you've gotten all your translations back from the translators,
simply stick them the giant blob 'o json on `I18n.translations`; it
expects the translations to be of the format:

```js
I18n.translations = {
  "en": {
    "some_key": "Hello World",
    "another_key": "What's up?"
  }
  "es": {
    "some_key": "Hola mundo",
    "another_key": "¿Qué tal?"
  },
  ...
}
```

## Related Projects

* [i18nliner (ruby)](https://github.com/jenseng/i18nliner)
* [i18nliner-js](https://github.com/jenseng/i18nliner-js)
* [i18nliner-handlebars](https://github.com/fivetanley/i18nliner-handlebars)

## License

Copyright (c) 2015 Jon Jensen, released under the MIT license
