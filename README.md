# Neutrino Svelte preset

[![npm](https://img.shields.io/npm/v/neutrino-preset-svelte.svg)](https://www.npmjs.com/package/neutrino-preset-svelte)
[![npm](https://img.shields.io/npm/dt/neutrino-preset-svelte.svg)](https://www.npmjs.com/package/neutrino-preset-svelte)

`neutrino-preset-svelte` is a [Neutrino](https://neutrino.js.org) preset for creation of [Svelte](https://svelte.technology) applications for web platforms.

## Features

- Zero upfront configuration necessary to start developing and building a Svelte web app
- Modern Babel compilation supporting ES modules, last several major browser versions, async functions, dynamic imports, ES class properties, rest spread operators and automatic polyfills bound to platforms
- Webpack loaders for importing HTML Svelte components, CSS, images, icons, and fonts
- Inheritense of Eslint rules for Svelte components `<script>` tags linting
- Webpack Dev Server during development on "localhost" and local network IP for external devices access
- Automatic creation of HTML pages, no templating of "index.html" necessary
- Hot reloading support
- Tree-shaking to create smaller bundles
- Production-optimized bundles with Babili minification, source maps and easy chunking
- Extensible to customize your project as needed

## Requirements

- Node.js v6.9+
- Neutrino v5

## Installation

`neutrino-preset-svelte` can be installed from NPM. Make sure `neutrino`, `svelte` and `neutrino-preset-svelte` are development dependencies in your project.

```
❯ npm install --save-dev neutrino svelte neutrino-preset-svelte
```

## Project Layout

`neutrino-preset-svelte` follows the standard [project layout](https://neutrino.js.org/project-layout) specified by Neutrino. This means that by default all project source code should live in a directory named `src` in the root of the project. This includes JavaScript files, CSS stylesheets, images, and any other assets that would be available to your compiled project. Only files explicitly imported or lazy loaded to your project will be bundled.

## Quickstart

After installing Neutrino and the Svelte preset, add a new directory named `src` in the root of the project, with a single JS file named `index.js` in it. You can mound you application to the document `<body>`. Edit your `src/index.js` file with the following:

```js
import Main from './main.html'
import './main.css'

new Main({
  target: document.body
})
```

You can change this code base to better match your needs.

Now edit your project's `package.json` to add commands for starting and building the application:

```json
{
  "scripts": {
    "start": "neutrino start",
    "build": "neutrino build"
  },
  "neutrino": {
    "use": [
      "neutrino-preset-svelte"
    ]
  }
}
```

Start the app. 

```
❯ npm start
✔ Development server running on: http://0.0.0.0:5000
✔ Build completed
```

The console shows that application started at "http://0.0.0.0:5000". This is a way how `webpack-dev-server` starts in both "localhost:5000" and local network IP address (e.g. "http://192.168.1.100:5000"). External IP may be usefull for mobile development. The preset tries to automatically start (if not disabled in [custom options](#development-server)) a browser with the URL of the server IP address (if not changed in [custom options](#development-server)).

## Building

`neutrino-preset-svelte` builds static assets to the `build` directory by default when running `neutrino build`. 

```
❯ npm run build
```

You can either serve or deploy the contents of this build directory as a static site.

## Hot Reloading

`neutrino-preset-svelte` supports Hot Reloading of files that was changed. Hot Module Replacement is supported only in CSS files. This means that changing of CSS will rerender only a part of styles, and changing of the rest of modules will reload the page.

<!--Using dynamic imports with `import()` will automatically create split points and hot replace those modules upon modification during development.-->

## Customizing

Consumers may provide their custom configurations for different parts of current preset that will override its defaults. Also if you want to construct your own preset based on `neutrino-preset-svelte` you can use information below.

To override the build configuration, start with the documentation on [customization](https://neutrino.js.org/customization). `neutrino-preset-svelte` creates some conventions to make overriding the configuration easier once you are ready to make changes.

### Entry points

By default the Svelte preset creates these entrypoints to your application:

- `index`: maps to the `index.js` file in the src directory. This value is provided by `neutrino.options.entry`.

### Rules

The following is a list of rules and their identifiers which can be overridden:

- `compile`: Compiles JS files from the src directory using Babel. Contains a single loader named `babel`;
- `svelte`: Compiles Svelte components to JavaScript modules. Contains a single loader named the same `svelte`;
- `style`: Allows importing CSS stylesheets from modules. Contains two loaders named `style` and `css`;
- `img`, `svg`, `ico`: Allows import image files from modules. Each contains a single loader named `url`;
- `woff`, `ttf`: Allows importing WOFF and TTF font files from modules. Each contains a single loader named `url`;
- `eot`: Allows importing EOT font files from modules. Contains a single loader named `file`.

### Plugins

The following is a list of plugins and their identifiers which can be overridden:

- `env`: Injects the value of NODE_ENV into the application as process.env.NODE_ENV;
- `html`: Creates HTML files when building. Has various options that can be configured via package.json;
- `html-defer`: Adds `defer` attribute to `<script>` tags in HTML files when building;
- `chunk`: Defines chunks for manifest and vendor entry points. Can be configured via package.json;
- `hot`: Enables hot module reloading;
- `clean`: Clears the contents of build prior to creating a production bundle.
<!--- `progress`: Displays a progress bar when using neutrino build.-->

### Customization and configuration

By following the [Simple customization guide](https://neutrino.js.org/customization/simple) and [Advanced customization guide](https://neutrino.js.org/customization/advanced) and knowing the rule, loader, and plugin IDs above, you can override and augment the build directly from package.json or from JS module which overrides the config. 

#### Vendoring

By defining an entry point named "vendor" you can split out external dependencies into a chunk separate from your application code. You can do this in 2 ways: by defining an entry point in package.json or by writing a configuration module.

*Simple Example: Put lodash into a separate "vendor" chunk:*

**package.json**
```json
{
  "neutrino": {
    "config": {
      "entry": {
        "vendor": [
          "lodash"
        ]
      }
    }
  },
  "dependencies": {
    "lodash": "*"
  }
}
```

*Edvanced Example: Put lodash into a separate "vendor" chunk:*

**package.json**
```json
{
  "neutrino": {
    "use": [
      "neutrino-preset-svelte",
      "./custom-preset.js"
    ]
  },
  "dependencies": {
    "lodash": "*"
  }
}
```

**custom-preset.js**
```js
module.exports = function (neutrino) {
  neutrino.config
    .entry('vendor')
    .add('lodash')
}
```

#### HTML files

Under the hood `neutrino-preset-svelte` uses [html-webpack-plugin](https://www.npmjs.com/package/html-webpack-plugin) with custom template for generating HTML files. If you wish to override how these files are created, define an object in your package.json at `neutrino.options.html` with options matching the format expected by `html-webpack-plugin` and also with `"mobile"` option.

*Simple Example: Change the application title and othe options:*

**package.json**
```json
{
  "neutrino": {
    "options": {
      "html": {
        "title": "Document title",
        "mobile": true,
		  "filename": "index.html"
      }
    }
  }
}
```

*Edvanced Example: Change the application title and othe options:*

**package.json**
```json
{
  "neutrino": {
    "use": [
      "./custom-settings.js",
      "neutrino-preset-svelte"
    ]
  }
}
```

**custom-preset.js**
```js
module.exports = function (neutrino) {
  neutrino.options.html.title = 'Document title'
  neutrino.options.html.mobile = true
  neutrino.options.html.filename = "index.html"
}
```

Make sure to use `custom-settings.js` before `neutrino-preset-svelte`.

#### Development server

This preset uses `webpack-dev-srever` for development purposes. It is optimized for building speed, error reports and hot module replacement. The basic configuration allows you to redefine `host`, `port`, `https`, `open` (whether to open server URL in a default browser).

*Example: change server options:*

**package.json**
```json
{
  "neutrino": {
    "options": {
      "server": {
        "host": "localhost",
        "port": 3000,
        "https": true,
        "open": false
      }
    }
  }
}
```

If you want to customize more you can use advanced approach where you can redefine all [webpack-dev-server properties](https://webpack.js.org/configuration/dev-server/#components/sidebar/sidebar.jsx).

*Example: configure web server:*

**package.json**
```json
{
  "neutrino": {
    "use": [
      "neutrino-preset-svelte",
      "./custom-preset.js"
    ]
  }
}
```

**custom-preset.js**
```js
module.exports = function (neutrino) {
  neutrino.config.devServer
    .host('localhost')
    .port(3000)
    .https(true)
    .historyApiFallback(true)
    .hot(true)
	 //...

    neutrino.options.server.open = false
}
```

#### Compile targets

This preset uses `babel-preset-env` which by default uses compatibility with 3-4 latest versions of different browser. This can be redefined for custom platforms.

*Esample: change supported browsers to automatically bundle necessary polyfills for them:*

**package.json**
```json
{
  "neutrino": {
    "options": {
      "compile": {
        "targets": {
          "browsers": ["last 2 versions", "ie 9"]
        },
        "include": ["transform-es2015-arrow-functions", "es6.map"],
        "exclude": ["transform-regenerator", "es6.set"]
      }
    }
  }
}
```

This is an example with [browserlist](https://github.com/ai/browserslist) config. Full scope of configurations can be found in [babel-preset-env](https://github.com/babel/babel-preset-env/blob/master/README.md)



