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

* on the top of bundle.js an array is created. the array contains 2 functions. each function wraps the code that was in each file.
* after the array a variable called  entryPointIndex is created with initial value of 1. this variable points to the index in the array of our entrypoint module.(index.js)
* webpack then lokks into the module array with the index to invoke the code of the entry file
* now the 2nd function is executed. we see that the inmport of the index.js file is represented as a reference to the array element that contains the code of the imported file (sum.js), the code gets executed.
* all webpack code is vanilla js to run on all browsers.

### Lecture 13 - Running the App

* now we will see how to run the app in the browser.
* to run our code in the browser we will create an html file with the sole purpose of running our bundle.js bundle code file.
* we call the file index.html in root project folder.we put html5 boilerplate with a script tag poiting to the bundle.js
* we can just opent he fil ein browser or launch a webserver like live-server.

## Section 3 - Handling Project Assets

### Lecture 14 - Intro to Loaders

* module loaders are used to do pre-processing on our files before they are added to bundle.js
* loaders are commonly used to implement transpiling from ES2015 code, setyp the use of Babel, load images, CSS code and the like.
* first we will add the Babel loader for transpilling JS from ES2015 to ES5
* we will add Babel to the project and wire it up as module loader (we will need 3 different modules for that):
	* babel-loader : teaches babel how to work with webpack
	* babel-core : knows how to take up code, parse it and generate some output files
	* babel-preset-env : ruleset for telling babel exactly what pieces of ES2015/6/7 syntax to look for, and how to turn it into ES5 code.
* babel is very modular and has support for many build systems.
* we npm install them all as dev dependencies in our project `npm install --save-dev babel-loader babel-core babel-preset-env`

### Lecture 15 - Babel Setup for ES2015

* loaders are used to enhance the behaviour of webpack. they are individual libs that can run on different files in our project.
* now that we installed babel we have to tell webapack how to use it.
* to add the loader we first going to add it in our webpack config file and then tell webpack on which files it should use this loader on. most of the time we bind a loader witha type of file
* babel shoudl operate only on js files
* we add a loader as follows: we add a new property object in our config object called modules. there we add an array of rules. each rule (loader) is an object which is in fact a preprocessor for files added in the bundle. each rule/loader config object has a set of properties.
	* use: sets the loader module to be used for th rule
	* test: sets a regex to filter the project files where the loader will be applied to 
* babel loader loads babel to webpack but babel to be configured needs a config file of its own in project root folder called `.babelrc`
* this file uses json syntax and sets presets: property as an array of modules to babel to enhance its transpiling capabilities. these extensions are also installed as npm modules in the project

```
{
	"presets": ["babel-preset-env"]
}
```
* to test babel loader we convert some code in our source files to ES6
* we sould see in the bundle.js the conversion to common JS (ES5)

### Lecture 17 - Refactor to ES2015 Modules

* nothing new. change-build-run

### Lecture  18 - Handling CSS with Webpack

* The next loader we will look at is one that is equally important for many projects. Styles and CSS loaders (SCSS???) it will allow to use CSS style in our project in a modular way bundled with Webpack
* modularity in CSS helps the developer
* for testing we add an image_vviewer.js file to the project to show an image on the html doc. we will syle it with an image_viewer.css file.
* the code in image_viewer.js is vanill js dom manipulation code (create a new DOM element and append it to body)
* we import image_view.js in index with `import './image_viewer';` as we dont want to use any code , just to invoke it.
* importing js files is the way to tell webpack to put them in the bundle through the  execution chain
*  there are ways to run the build script automatically. but as we do wenbpack config changes we have to respawn the build to use them. if we modify just our codebase we run webpack build in auto

### Lecture 19 - THe Style and CSS Loaders

* we have th module that puts the imag ein the page ready. now we will add CS to syle it and find a way to use the loader to put the CSS in our bundle with webpack
* we will impor css file in our js code.
* we make a new dir called styles inour project troot folder. we add a image_viewer.css file in there.
* in React its common to put css in the same folder as js files.
* we import the css in our js file as a js module. we just have to add the filetype `import '../styles/image_viewer.css';` and use the relativ epath to the file
* to use css in out bundle through webpack we will install 2 loaders with npm
	* css-loader: knows how to deal with css imports
	* style-loader: takes css imports and adds them to the HTML document
* `npm install --save-dev css-loader style-loader`
* we add another rule in the module rules section of our webpack config file. webpack rules can support multiple loaders in one step
* loaders are added to the rule right to left. so order matters `use: ['style-loader','css-loader']` output of css goes to style loader. we also add the test prop with the regex file filter.

### Lecture 20 - Loaders are Tricky

* we run npm run build and run our werver to see the styles on screen. we inspect the page and we see that styles are added in the html head tag as a styles tag inline without a file import.
* we look in bundle.js to find out how css was injected in the html head tag.
* we see that css code is injected in bungle.js as a string, there are also methods like addSytlesToDom. so styles loader manual injects css code from modules into html
* it is questionable if we want to load our css in this way

### Lecture 21 - THe Extract Text Plugin

* loading css in a separate file is lot faster than load js and css in one file.
* we install a new lib `npm install --save-dev extract-text-webpack-plugin@2.0.0-beta.4`
* we will make a rule in config using this loader/plugin that will take all files specified wwiht test: and bundle them in one file in our outpust directory
* this llibrary gets added up in config diferently than the others
* we import the library in config `const ExtractTextPlugin = require('extract-text-webpack-plugin');`
* we replace use property in our css rule with the legacy property loader passing a library function extract() which gets a config object argument with the property loader which has value of css-loader

```
			{
				// use: ['style-loader','css-loader'],
				loader: ExtractTextPlugin.extract({
					loader: 'css-loader'
				}),
				test: /\.css$/
			}
```

* this method of adding rules is  a webpack plugin. loaders act as a preprocessor before bundling a file. plugins work outside the webpack pipeline preventinf files from being added to the bundle. we want this here as we want to create a separate file
* under modules section we add another section called plugins. there in the array we create new instance of ExtractTextPlyugin passing the param 'style.css' which is the name of the file we want to create. what it does is that takes whichever files the loader grabbed ans saves them into this file
* we run npm run build. style.css is created. we add it in html with a link tag. `<link rel="stylesheet" href="./build/style.css">`. run the server ans we see the style taking effect.

### Lecture 22 - Handling Images with Webpack

* we will use another loader to process images in our project. in fact we will use 2 loaders chained to implement the followinf pipeline for images in webpack. `IMAGE -> resize the image w/ imae-webpack-loader -> [is the image small?] -> url-loader -> [is the image small? => include the image in bundle.js as  raw data] [is the image big? => include the raw image in the output directory]`
* to add an image to a file we will use 2 separate loaders. the first compresses the image. then depending on the compacted size the second loader chooses what to do. 
* we this approach we have performance benefits for small images but we need to figure out how to include the image if its a larger side so existing as a separate file.

we npm install them as  dev dependencies `npm install --save-dev image-webpack-loader url-loader`
* we add a new rule for images in config file. we add a type property (which filetypes the loader will look for) with a test regex. the regex is more complex to handle various image filetypes. we the add the use property like in css forst approach with 2 loaders keeping the order of execution right to left
* this time we have to configure the loaders as url-loader must have a threshold to decide which images go to bundle.js and which are kept as files. to configure a loader we replace the string with an object. the object is the following

```
					{
						loader: 'url-loader',
						options: { limit: 40000 }
					}
```

*  we add the options prop to se tthe limit

### Lecture 23 - Automatic Image Compression

* we get some images and we include them in the project. we add a folder *assets* in project root and add 2 images
* we import both images in image_viewer.js using ES6 import syntax (like css) `import small from '../assets/small.jpg';` this time we import the image as a js object
* we run npm run build. we get error so we check github. we downgrade image-webpack-loader to 3.6.0 small passes but big throws error. it asks for file-loader so we install it `npm install --save-dev file-loader` and run again.
* success . we also see a new compressed jpg in build dir. we check and is the large image compressed. 
* we see in bundle.js to see what happened to the small image we see thje code `module.exports = "data:image/jpeg;base64,/9j/4AAQSk...` which contains the image binary. it is encoded as a base64 string.
to test we replace remote image fetch wtith the small and run our server. success. if we replace small with big we dont see the image (external resource). 
* we see =in chrome console and see that it cannot find the big image. the hash name it searches is correct but in the wrong folder (root).
* a hack is to use string interpolation to add the build folder in the path `image.src = './build/'+big;` but this is not  the way to do it. we should have auniform way to refer images in the code (no prefixes)

### Lecture 24 - Public Paths

* to fix the issue with the bi pickture path we go t webpack config
* in the output section we will add one more property named *publicPath* given the va; 'public/'
* we rebuild our project and start the server. now the big picture appears on screen an if we inspect we will see that without hardoding the path it has the correct path of the image in thee build folder we jist added in teh config.
* what publicPath does? the url-loaders purpose is to take the image and copy it to our build folder. url-loader emits the url of the file with 'output.publicPath' prepended to the URL. also copies the image from dev folder to the build folder.

## Section 4 - Building for Performance with Webpack

### Lecture 25 - Intro to Code Splitting

* in the simple use case that our up contains a login form and a dashboard we would like not to load the complete bundle.js when we come to the site. code splitting in webapck splits the bundle.js in multiple files and allows us to perogramaticaly decide when to load the individual files in our codebase. this improves the performance o fthe app as what is needed gets loaded just in time. 

### Lecture 26 - Codesplitting in practice

*  we refactor the code of the small sample project to implement a scaledd own version of the use case we discussed to showcase the concept of codesplitting
	* index.js : entry point. render a button with click handler. imports 'image_viewer' after button gets clicked
	* image_viewer.js : renders the image
* we implment the index.js adding a button and its handler.

### Lecture 27 - On-Demand Code Loading
	
* in the button onclick handler we add `System.import('./image_viewer');`
* this method is ES2015 js method. it fetches js modules on demand at runtime from the server and it moves execution to that module. so this breaks the typical approach that all js is loaded at client at init
* if the imported module we are exectuing with he system method jas its own imports these are lpulled to client aswell
* the system import method is asynchronous call as it goes babk to the server so it return a promise.
* to show the interaction we export the code that puts the image on screen in image_viewer.js

### Lecture 28 - System Import Calls

* we build our project and run the server. we luch console and hit button. we console loged the resolve object of the system call promise which we called module.
* we see that is an object with a default() method as single accessible property. this default() is the default export we implemented puting the image rendering code inside. so we expect that if we call module.default(); in the console the image will appear
* we refresh the page and look into sources tab. when we launch index.js only bundle.js gets loaded. when we click the mouse 0.bundle.js is loaded as well
* we look into these 2files in sublime.
* bundle.js is smaller and a JSONP callback for chunk liading used
* SO whe we use code splitting with SYstem.import() method inour code webpack adds logic to go and fetch modules from server.
* the code of image rendering is in 0.bundel.js
* we replace console.log(module) to module.default(); an we see the image on screen.

## Section 5 - Applying Webpack to a Real Project

### Lecture 29 - A Real World Project

* 