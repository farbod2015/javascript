# Building a JavaScript Development Environment

## Starter Kit

* Steps:
    1. install git from git-scm.com
    1. sign up for a github account (at github.com)
    1. create a repository on github (select `add .gitignore: Node` to avoid checking in the Node folder)
    1. clone the repository to your computer:
        * click the `clone or download` green button and copy the URL
        * in command prompt: git clone https://github.com/farbod2015/pluralsight-js-dev-env.git

* Helpful git commands:
  * stage all the files you changed: `git add .`
  * commit your changes locally: `git commit -m "added new feature"`
  * push your work to Github: `git push`
  * see all the changes: `git status`

* Other Commands:
  * to run a code: `node filename`
  * to transpile and run: `babel-node filename`

## Editors and Configuration

* Atom, WebStorm, Brackets, VSCode
* Create `.editorconfig` in the root consistence spacing, character set, etc. regardless of the editor
    * download the plugin for VS Code from editorconfig.org

## Package Management

* You need a Package Manager because every language benefits from a standardized method for sharing code
* NPM: most popular now
* install Node.js
    * Node.js is an open source server framework.
    * Node.js allows you to run JavaScript on the server.
    * when you install node, npm, node's package manager comes bundled along with it

* `package.json`:
    * is the manifest that NPM uses
    * It stores the list of npm packages that we are using as well as the npm scripts (download from bit.ly/jsdevpackagejson)
* to install the packages in package.json do `npm install` in command line

* **Note:**
  * `npm install` installs the dependencies in the local `node_modules` folder.
  * In global mode (with `-g` or `--global` appended to the command), it installs the current package context (the current working directory) as a global package.
  * By default, `npm install` will install all modules listed as dependencies in `package.json`. With the `--production` flag (or when the `NODE_ENV` environment variable is set to production), npm will not install modules listed in `devDependencies`.

## Package Security

* Packages can be published npm by anyone. `Reire.js` and the `Node Security Platform` (preferred at this time) are two ways that you can check your project's dependencies for known vulnerabilities.
* Install Node Security Platform to automate security checks for our dependencies:
  * to run nsp directly on the command line, install it globally: `npm install -g nsp`
  * we can call this from an npm script and thus avoid having to install it globally (we'll do later)
* to check for vulnerabilities, in command line: `nsp check`

## Development Webservers

* web server can be configured to start automatically as part of our development process
* it opens the application so we can view our results immediately every time that we hit save.
* Development Webservers:
  * **http-server**
  * **live-server**
  * **Express** (we use in this course)
  * **budo**
  * **webpack dev server**
  * **Browsersync**
* express is already installed as part of `package.json`
* create `buildScripts` folder for all build tools
* create `srcServer.js` inside `buildScripts` folder
  * this file will configure a web server that will serve up the files in our source directory

## Sharing Work-in-progress with Customer

* tools: localtunnel, ngrok, Surge, now
* example:
  * install: `npm install localtunnel -git`
  * `node buildScripts/srcServer.js`
  * in a new terminal: `lt --port 3000`
  * copy the URL and give it to the customer

## Automation

* Popular tools:
  * **Grunt**
  * **Gulp**
  * **npm Scripts** (we use this)
* add npm scripts to the `scripts` section of `package.json`:
  * `start: "node buildScripts/srcServer"` (use "npm start" to run this and use -s flag to silent the noise)
  * generally if there is `script` (e.g. `start`), you can define `prescript` (e.g. `prestart`) and `postscript` (e.g. `poststart`) that automatically will run before `script`
  * type `npm run scriptname` to run a script. `start` and `test` don't need the keyword `run`
  * don't need to install packages globally if run them by npm scripts. for example we won't need to install nsp globally if we write an script for running it (e.g. `"security-check": "nsp check"`).
  * the reason is that all packages are installed in `node_modules/.bin` which is in path when we call them from npm
* use `npm-run-all` package to run multiple scripts at the same time:
  * example:

    ```json
    "open:src": "node buildScripts/srcServer.js",
    "security-check": "nsp check",
    "start": " npm-run-all --parallel security-check open:src",
    ```

## Transpiling

* Since we've decided to use the latest version of JavaScript in this project we need to transpile down to ES5 to assure that it runs in environments that don't yet fully support the latest versions of JavaScript.
* Popular transpilers:
  * **Babel**: transpiles the latest version of JavaScript down to ES5 so that you can use all of these new features, but run them everywhere that ES5 is supported. But it actually does a lot more than that. Babel plugins are responsible for converting JSX (inline HTML in JavaScript, made popular by React) into plain old Javascript in the browser.
  * **TypeScript**
  * **Elm**
* can configure babel in `.babelrc` or `package.json` files.
  * add `.babelrc` to root (use babel-node instead of node to run the code):

    ```json
    {
      "presets": [
        "latest"
      ]
    }
    ```

## Bundling

* Bundlers take all your JavaScript files, and intelligently package them for a target environment, such as a browser or node, and some bundlers add a variety of additional features on top of that.
* Bundlers to choose from:
  * **Browserify**
  * **Webpack**
  * **Rollup**
  * **JSPM**
* we use Webpack because:
  * it is much more than just JS (CSS, images, fonts, html)
  * bundle splitting
  * hot module reloading (depending on the library or framework that you are using)
  * tree shaking: dead-code elimination (webpack 2 offers this)

* **Note:**
  * **React hot reloading:** As we know in Meteor, every time we save, Meteor rebundles your entire app and reloads the page, and this takes a bit of time depending on the size of your project. React hotloading is slightly different, it leverages HMR (see below) and without reloading the page, replaces changed components in-place, preserving state. That means that, your change will be visible in about half a second; if it was a dialog, you wouldn't need to re-open it, your text inputs remain filled, etc. In short, it's a massive boon to iterative development where you spend much less time waiting for your app to reload.
  * **Code splitting:** As we know in Meteor, the client loads all client files on the first load. E.g. not admins will still be served all the admin templates, which will never be used, and this creates a slower load time. With code splitting, you can divide your app into different pieces. You could, e.g. load the main code straight away, and load code for particular sections if a user visits them (or load everything else after the initial load is complete and the user can start using your app).

* Steps:
  1. create `webpack.config.dev.js` in root and export an object for configuration
  1. configure a dev server (we used webpack with express in `srcServer.js` for this)
  1. configure the build to automatically generate sourcemaps for debugging as part of the bundling process
      * it is set as devtool in webpack
      * there are many different options depending on the quality and speed that you are looking (we used `inline-source-map`).
      * use `debugger;` as breakpoint in the javascript code. the inspector in the developer tool of the browser will show the original js code instead the bundled

## Linting

* why do you need a linter? I see two core reasons:
  1. it programmatically enforces consistency to make it easy to read (e.g. enforcing the position of curly braces)
  1. it helps avoid mistakes (e.g. adding an extra parenthesis or overwriting a function). It does rapid feedback when hit save.
* Linters:
  * **JSLint**
  * **JSHint**
  * **ESLint** (most popular and powerful)
* Core decisions for using a linter:
  * **config format?** choose one of these file names for configuration:
    * `.enlintrc.js`
    * `.enlintrc.yaml`
    * `.enlintrc.yml`
    * `.enlintrc.json`
    * `.enlintrc`
    * `package.json`
  * **which built-in rules?** no rules are enabled by default (see ESLint docs for a full list of rules)
  * **warnings or errors?** if you set the rule as a `warning` it doesn't break the build while `error` does
  * **which plugins?** e.g. use `eslint-plugin-react` for React specific linting rules for ESLint
  * **use preset instead?** ways to handle your ESLint rules:
    * start from scratch
    * use ESLint preset (recommended)
    * other presets: XO, airbnb, standardJS
* helpful link: github.com/dustinspecker/awesome-eslint
* use npm package called `eslint-watch` that adds file watching capability to ESLint
* use `babel-eslint` instead if you wanted to use other experimental JavaScript features 
* other than the required packages (`eslint`, `eslint-plugin-import` and `eslint-watch`), these lines added to the scripts section of the `package.json`:

  ```json
  "lint": "esw webpack.config.* src buildScripts --color",
  "lint:watch": "npm run lint -- --watch",
  "start": " npm-run-all --parallel security-check open:src lint:watch",
  ```

## Testing and Continuous Integration

* JavaScript Testing Styles:
  * **Unit Testing:** focuses on testing a single function or module in an automated fashion (covered in this course)
  * **Integration Testing:** focuses on testing the interactions between multiple modules
  * **Automated UI Testing:** tests the application by automating clicks and key strokes within the actual UI
  * etc.
* Unit Testing Decisions:
  * **Framework**
    * **Mocha:** the most popular (we use this)
    * **Jasmine**
    * **tape**
    * **QUnit**
    * **AVA**
    * **Jest**
  * **Assertion Library:** is a way to declare what you expect:
    * Example: `expect(2+2).to.equal(4)`
    * Most frameworks include their own assertions built in, but since `Mocha` doesn't, we need to choose one (we'll use Chai for assertions)
  * **Helper Libraries**
    * **JSDOM:** it is useful when you want to write tests that involve HTML and interactions in the browser using Node (we use this)
    * **Cheerio:** it is as jQuery for the server
  * **Where to run tests?**
    * **Browser:** run the test on real browser (Karma, Testem)
    * **Headless Browser:** run the test on an invisible browser (PhantomJS)
    * **In-memory DOM:** simulating a DOM in memory (JSDOM)
  * **Where to place tests?**
    * **Centralized:** centralize all your tests within a folder called tests or something similar
    * **Alongside:** place tests alongside the file under test (we prefer)
      * easy import
      * clear visibility
      * convenient to open
      * no recreating folder structure
  * **When to run tests?**
    * unit test should run any time we hit save
    * integration test: often run on demand, or in QA
  * Decision summary for this course:
    * Framework: **Mocha**
    * Assertion Library: **Chai**
    * Helper Libraries: **JSDOM**
    * Where to run tests? **Node**
    * Where to place tests? **Alongside**
    * When to run tests? **Upon save**

  **Note:** did test setup in `testSetup.js` and wrote two test cases in `index.test.js`

  **Note:** if there is an asynchronous call (e.g. `function` in the following example) use `done()` so that `Mocha` knows that it is safe to evaluate what you expect is true or false:

    ```javascript
    describe('index.html', () => {
      it('should say hello', (done) => {
        const index = fs.readFileSync('./src/index.html', "utf-8");
        jsdom.env(index, function(err, window) {
          const h1 = window.document.getElementsByTagName('h1')[0];
          expect(h1.innerHTML).to.equal("Hello World!");
          done();
          window.close();
        });
      })
    })
  ```

* To run the test automatically every time that we hit save add the following to scripts section of `package.json`:

  ```json
  "test:watch": "npm run test -- --watch"
  ```

## Continuous Integration

* with continuous integration server we can find out right away when someone else has broken the build or when you made a bad commit that has broken the build on someone else's machine
* CI server catches (quickly) a number of potential mistakes:
  * Forgot to commit new file
  * Forgot to update `package.json`
  * Commit doesn't run cross-platform
  * Node version conflicts
  * Bad merge
  * Didn't run tests
* What does a CI server do?
  * Run automated build
  * Run your tests
  * Check code coverage
  * Automate deployment
* Common CIs:
  * Travis (Linux)
  * Appveyor (Windows)
  * Jenkins
  * CircleCI
  * Semaphore
  * SnapCI

  **Note:** you can run multiple CI servers. For example, set up Travis and Appveyor to make sure that our build process runs on Mac, Linux, and Windows.

* How to set up travis:
  * log into travis-ci.org using your github account and activate the repository
  * create `.travis.yml` under root and add:

    ```json
    language: node_js
    node_js:
      - "6"
    ```

* How to set up Appveyor:
  * log into appveyor.com using your github account and add the repository
  * create `appveyor.yml` under root and add the desired settings

## HTTP Calls

* HTTP call approaches:
  * Node: http, request (recommended)
  * Browser: XMLHttpRequest, JQuery, Fetch (recommended)
  * Node & Browser: isomorphic-fetch, xhr, SuperAgent, Axios
* Key: Centralize API calls:
  * Handle API calls in a single spot
  * Why?
    * You can configure all calls in a single place
    * Handle preloader login: show preloader until all asynchronous calls are complete
    * Handle errors in one place
    * Sinlge seam for mocking API
* Why Mock HTTP?
  * Unit Testing
  * Instant response
  * Keep working when services are down
  * Rapid prototyping
  * Avoid inter-team bottlenecks
  * Work offline
* How to Mock HTTP?
  * Nock: a handy way for unit test (specify the URL to mock and what it should return)
  * Static JSON
  * Create development webserver
    * api-mock
    * JSON server (creates realistic API using a static JSON)
    * JSON Schema Faker (creates different fake JSON data every time app starts). Libraries:
      * `faker.js`
      * `chance.js`
      * `regexp.js`
    * Browsersync
    * Express, etc.

  **Note:** In this course we used JSON Schema faker to dynamically generate a mock data set and point JSON server to that.

## Project Structure

* Create a demo app with the starting kit:
  * This gives everyone clarity on recommended approaches for directory structure, file naming, framework usage, testing, API calls, deployment, and more.
  * It provides an interactive example of what it's like to work on your team.
* **Tips:**
  1. JS belongs in a .js file (no inline js code in HTML files)
  1. organize by feature instead of file type
  1. extract logic to POJOs (minimizes the impacts of switching to a different framework):
      * Plain Old JavaScript Objects
      * Pure logic
      * No framework-specific code

## Production Build

* create an automated production build to benefit from:
  * minification: to speed loads:
    * Shortens variable and function names
    * Removes comments
    * Removes whitespace and new lines
    * Dead code elimination / Tree-shaking
    * Debug via sourcemap
  * Sourcemaps: generated to support debugging in production
  * Dynamic HTML: for production specific concerns
  * Cache busting: to ensure that users receive the latest version of our code upon deployment
  * Bundle splitting: so that users don't have to download the entire application when just part of it changes
  * Error logging: so that we know when bugs sneak their way into production.
    * Error Logging Services:
      * TrackJS (we used this)
      * Sentry
      * New Relic
      * Raygun
    * Things to consider when choosing an error logging service:
      * Error Metadata
        * Browser
        * Stack trace
        * Previous actions
        * Custom API for enhanced tracking
      * Notifications & integrations
      * Analytics and filtering
      * Pricing

## Production Deployment

* Cloud Hosting:
  * amazon web services
  * Microsoft Azure
  * HEROKU
  * Firebase
  * Google Cloud Platform
  * Netlify (only static files)
  * GitHub Pages (only static files)
  * Surge (only static files)
* We used HEROKU for API and Surge for UI
