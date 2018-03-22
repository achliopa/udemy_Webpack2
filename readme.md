# Udemy Course: Webpack2: The Complete Developer's Course

* GITHUB Repos
	* [Course Code](https://github.com/StephenGrider/WebpackCasts)
	* [Course Project](https://github.com/StephenGrider/WebpackProject)

## Section 1 - How to Use this Course

* WebpackCasts contains all compeleted code for this course
* WebpackProject will be used later in the course

## Section 2 - What does Webpack Do

### Lecture 3 - Why do we use build tools

* webpack is popular around rich we apps.
* In ServerSIde Tmplating html comes from backend.
* In SinglePageApp backend sends just data throug apis. webpage is built on browser
* SinglePageApp flow => user visits page -> https req to server -> new HTML doc -> react boots up shows page -> link click -> react shows new content.
* IN SinglePage App we have huge amount of JS code to send to client.

### Lecture 4 - JS Modules

* JS code usually it is in few files in traditional apps. when code is huge it is unmanageable. to solve this JS modules were born.
* we strive to have many managable JS files in folders like *components* *api* etc

### Lecture 5 - The Purpose of Webpack

* splitting code creates the question of execution order. we want to control the load order
* loading multiple js files from an http request takes up resources.
* webpack solves these issues. it bundles a multitude of js files in one bundle.js ensuring execution order. CSS and es6 to es5 conversion are side effects

### Lecture 6 - Webpack in Action

* tiny app to demo webpack.
* tasks: make new npm project in termina -> create two js modules -> install and configure webpack -> run webpack
* npm init in our project folder (JS_modules)
* we create src folder for our code and add 2 js files there.

### Lecture 7 - JS Module Syntax

* our 2 js files: index.js=> calls func from Math. and prints results, sum.js=> util funcs for working with math calculations
* index.js requires sum.js (sum.js must be loaded first)
* files are namescopes we need to export what we want to use externally
* Module Systems: 
	* Common JS [Syntax=>  require "import" module.exports "export"] used in nOde.js
	* AMD [Syntax=>  require "import" define "export"] used in async module def 
	* ES2015 [Syntax=>  import "import" export / export default "export"] react

### Lecture 8 - Linking with CommonJS

* imports => Code => exports

### Lecture 9 - Webpack Installation and Configuration

* we install v2 of webpack as a  dev dependency in our project. `npm install --save-dev webpack@2.2.0-rc.0`
* apart from installation we need a configuration file to config how webpack will behave when we run it in our project `webpack.config.js`
* webpack every time it runs it looks for this file inour project. it reads its contents and based on the instructions we provide operates on our codebase.
* we add the minimum boilerplate cofiguration in the file. a cofiguration object with 2 minimum props. 
	* the entry property which point to the root js file of our app (E.g. index.js). this will be the first file to execute in our app. and the root of our import chain. The value of the entry param is the relativepath of the root js file in relation to the project root folder.
	* the second required property is the output property. this property tells webpack where to place the bundled up code of our project. this is an object with two props. path which is an absolute path and a filename. the absolute path is derived byt the current dir project root). we use for this the node path lib which we import and the resolve method to join path parts. `__dirname` returns the current dir. 
* which we export using  common JS syntax.

```
const path = require('path');

const config = {
	entry: './src/index.js',
	output: {
		path: path.resolve(__dirname, 'build') ,
		filename: 'bundle.js'
	}
};

module.exports = config;
```

### Lecture 11 - Running Webpack

* to run webpack we will add a script in package.json

```
  "scripts": {
    "build": "webpack"
  },
```

* we use a script beacuse webpack is not installed globally.
* we want portability. we dont like global dependencies and versioning conflicts
* we run with `npm run build` . the output gives the build time the bundle size. bundle is exploding in size.

### Lecture 12 - The bundle.js file

* we dig in bundle.js to see why it is so large in size.

* to understand what it does we strip it down and leave a non working pseudocode.

```
var myModules = [
	function() {
		const sum = (a,b) => a + b;
		return sum;
	},
	function () {
		const sum = myModules[0]();
		const total = sum(10,10);
		console.log(total);
	}
];

var entryPointIndex = 1;
myModules[entryPointIndex]();
```

* 