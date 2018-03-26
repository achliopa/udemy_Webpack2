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

* we download a react project to play with and npm install in it and run the server to see it. if we build it breaks (missing loader) if we run we see nothing

### Lecture 30 - Setting Up Babel

* we see similar project structure. in package.json we have the loaders for babel and css but ebpackconfig is empty
* we add the babel loader in our config creating a modules section and a set of rules

```
  		{
  			use: 'babel-loader',
  			test: /\.js$/,
  			exclude:  /node_modules/
  		}
```

* the xclude property is added to NOT aplly babel on node modules. as it is not needed and wastes resources.

### Lecture 31 - Minimum Webpack Config

* we add inproject root folder .babelrc file to config babel. in the json file we add the presets of babel: babel-preset-env and react to traspile react and jsx
* we add a rule for css. we dont use extractTextPlugin like in last project but css loader that injects css into bundle.js

```
  		{
  			use: ['style-loader','css-loader'],
  			test: /\.css$/
  		}
```

* we get size limit warning from webpack. we run our server and SUCCESS the app launches
* bundle.js is 3.5MB. we have to reduce it.
* this app has distinct parts.

### Lecture 32 - Vendor Asset Caching

* webpack helps control how we serve js to the client, it cannot speed up the app natively.
* in the give app we can split our code base into 2 chunks: Our Code (Index.js, SearchList.js ) and Vendor Code/libs:(React.js,Lodash.js)
* We will use resource caching, ask the browser if he has a resource stored before downloading. for common vendor packages (like React) chances are he has them (everybody uses Facebook)
* This is asset caching

### Lecture 33 - More on Vendor Caching

* In our app we do Vendor Dependency Updates (vendors do fixes in resleases, new feats). But these updates are done infrequently and in a controlled way
* Our code is updated much more frequently
* We take this into consideration when deciding what to cache
	* First Visit: we downlad all(bundle.js (only our code) and vendor.js)
	* Second Visit: we download only bundle.js (only our code) not vendor.js
* Browser decides what to download depending on his cache and the versioning of the bundles (webpack takes care of it)

### Lecture 34 - Refactoring for Code Splitting

* up to now we did code splitting with System.import() call
* now we will do it with webpack so that before it parses code it knows what to do
* we do it through System.import to split our code base
* we start by deleting entry param from webpack config. we replace it with an object as we wont have a single entry point
* we replace entry with an array of two entry points. bundle which uses the same entry point for our own codebase and vendor for the vendor libraries. 
* the vendor libraries is an array with the libs we want in the second bundle. here we cp all the libs from our package.json dependencies.
* to create the 2 bundles we do ke interpolation in the output objectfilename propery to enter bundle or vendor.

```
const VENDOR_LIBS = [
	"faker",
    "lodash",
    "react",
    "react-dom",
    "react-input-range",
    "react-redux",
    "react-router",
    "redux",
    "redux-form",
    "redux-thunk"
];
...
  entry: {
  	bundle: './src/index.js',
  	vendor: VENDOR_LIBS
  },
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].js'
  },
```

### Lecture 35 - Effect of Code Splitting

* in our new build we see that bundle and vendor are big in size
* this happens because common libs are imported in out source files. webpack does not know to distinguish them so it adds them in both bundles.
* to tell webpack to remove them from our code bundle even if they are imported in the source files we use CommonsChunkPlugin a webpack plugin
* we add it as aplugin as we want to affect the whole input not some type of files like loaders do. we add a new section to the config file called plugins and add this plugi passing a config object with the name: vendor. so it exludes from bundle what inide vendor

```
  plugins: [
  	new webpack.optimize.CommonsChunkPlugin({
  		name: 'vendor'
  	})
  ]
```

* the result is to reduce size of bundle to 300kB

### Lecture 36 - TroubleShooting Vendor Bundles

* we run our up but the app does not load. we get an error in console complaining for missing webpackJsonp
* this happends because we have not included vendor.js in out html script tags. we can do it but anytime we modify our code splitting or create new bundles we have to maintain this dependency. we would like this to happen automatically. for this reason we use html-webpack -plugin
* we install it `npm install --save-dev html-webpack-plugin`
* we add it into webpac vconfig plugin section

```
  	new HtmlWebpackPlugin({
  		template: 'src/index.html'
  	})
```

* we move index.html into src and use it as a template. we remove all scripts as the plugin will add them in the build
* we run build, a new index.html is createdin dist/ with the script tags added. we run it and it WORKS

### Lecture 37 - Chunk Hashing for Cache Busting

* we need to make clear to the browser when our bundles have cahne so as to falilitate caching. we do that by add ing a hash to the name. this is done by adding a second part to their name, the chunkhash

```
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].[chunkhash].js'
  },
```

* to facilitate vendor bundle hashing and the lib exclusion from bundle.js we need to modify the commonschunkplugin config

```
  	new webpack.optimize.CommonsChunkPlugin({
  		name: ['vendor','manifest']
  	}),
```

* this will create an extra manifest.js file in our dist.
* in our devetools we should disable caching to facilitate development

### Lecture 38 - Cache Busting Wrapup

* hash depends on the contents of the file. so if it does not change contents it stays the same
* to test it we change our project codebase and rebuild. bundle has changes and vendor hash stays the same
* html webpack plugin is smart enough to include the latest bundle hashes to the script tags of our index.html
* the only issue left to remove old hashed bundles from dist

### Lecture 39 - Cleaning Project Files

* we see that bundle files keep piling up in each build. 
* we add an ohter module called rimraf `npm i --save-dev rimraf`
* this is a tool of compatibilyt between unix and win. as we will use the unix command of rm to remove old bundles and win cmd does not suppor it. to make our project os agnostic we add this tool. use rimraf which translates to the suitable command in each os/
* we add a script to our package.json `"clean": "rimraf dist"` to clean all dist folder before build. 
* we also change our build script to precede it with the clean command `"build": "npm run clean && webpack"`

## Section 6 - Webpack Dev Server

### Lecture 40 - Intro to Webpack Dev Server

* up to now we run webpack manually to do our build and then start our server to test.
* we will use now the webpack-dev-server. we will start it once and we will monitor our codefor changes. whenever we save achange it will update only that change in the bundle. so builds will be faster
* also we will not manually load our html to the browser. we will connect to the dev-server and get access always to the up-to-date build for testing
*webpack-dev-server is for testing the client side of our single page application. as it is a non configurable server unlike a node backend app with access to apis dbs etc.

### Lecture 41 - Gotchas with Webpack Dev Server

* we install webpack dev server as npm dep. `npm install --save-dev webpack-dev-server@2.2.0-rc.0`
* we add a script in package.json to run the server `"serve": "webpack-dev-server"`
* we run the serve command and lauch the app.
* we clean our project and rerun the server. it serves the app. this is because webpack-dev-server does not save in hard drive. it runs inmemory. so does not need the dist folder. it is a development tool not ofr production. befoire releaseing we have to manualy run our build script.

## Section 7 - React Specific Topics

### Lecture 42 - React Route with Codeplitting

* we will do code-splitting withy react router. this can increase performance with large react projects.
* we will make a main bulndle with the parts the user sees at home page and separate bundle with the pages he sees when navigating.
* to apply codesplitting we will not follow jsx but use plain js. essentialy we will work on plain js in way that React-route works with jsx translating it to plain js

### Lecture 43 - Plain Routes with React Route Codeplitting

* we end up replacing routes jsx

```
      <Route path="/" component={Home}>
        <IndexRoute component={ArtistMain} />
        <Route path="artists/new" component={ArtistCreate} />
        <Route path="artists/:id" component={ArtistDetail} />
        <Route path="artists/:id/edit" component={ArtistEdit} />
      </Route>
```

* with a js object which we pass to the router. this object is what reactrouter buils from jsx. we do to pass system.import() in the getComponent function. this function gets a call back which will be called once the component is loaded. thats why we use system.import promise resolve (.then()) to call the callback. note that the callback expectes an error first and then the component. as component gets exported as default we use the module.default() method. module is the object returned when the system call promise resolves.

```
const componentRoutes = {
  component: Home,
  path: '/',
  indexRoute: {component: ArtistMain},
  childRoutes: [
    {
      path: 'artists/new',
      getComponent(location, cb) {
        System.import('./components/artists/ArtistCreate')
        .then(module => cb(null, module.default));
      }
    },
    {
      path: 'artists/:id',
      getComponent(location, cb) {
        System.import('./components/artists/ArtistDetail')
        .then(module => cb(null, module.default));
      }
    },
    {
      path: 'artists/:id/edit',
      getComponent(location, cb) {
        System.import('./components/artists/ArtistEdit')
        .then(module => cb(null, module.default));
      }
    }
  ]
};

const Routes = () => {
  return (
    <Router history={hashHistory} routes={componentRoutes} />
  );
};
```

* we cannot use dry code on this as the webpack tool looks for literaly system.import() calls to on demand do code splitting. it is a static analysis tool after all 

## Section 8 - Webpack-Based Deployment for Static Sites

### Lecture 44 - Deployment Options

* when we deploy an app using webpack we should consider if have just a front end app (static) or a frontend+backend app (dynamic). webpack bundles and uses static assets only.
	* Static Asset Providers: Github Pages, Amazon S3, Digital Ocean, MS Azure, Surge
	* Server-Based Providers: Amazon EC2, Amazon ELB, Digital Ocean, Heroku, MS Azure

### Lecture 45 - Getting Production Ready

* we will tweak webpack to ge in line with deployment based or productionbased practices.
* we add another plugin 

```
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
    });
```

* this plugin takes the proces.env>NODE_ENV setting we define in our window and inserti it in the bunddle so that react will now it is runnign in production or development environemnt. this will spped up react in production and add all the addtional debug info in development.

* we modify our build script adding `    "build": "NODE_ENV=production npm run clean && webpack -p",` so that we build for production as we explicityly se the NODE_ENV. also we add the -p to tell webpack we want a production version of build. this minifies our output to compact our bundle

### Lecture 46 - Deployment with [Surge](http://surge.sh/)

* we globally install surge cli `npm install -g surge`
* inside our project root we write `surge -p dist`
* it deploys instantly
* we can use custom names with CNAME param

### Lecture 47 - Deployment with Github Pages

* it requires a git repository and a master branch in the repo
* if in our project we make a branch named *gh-pages* and we push our code to it, it will automatically be served at *https://<username>.github.io/<RepoName>*
* github supports custom domains
* the github repo must be public
* we init git in the porject / add - commit the code/ add remote origin the github repo
we checkout to a gh-pages branch `git checkout -b gh-pages`
* push dist folder from this branch to github `git subtree push --prefix dist origin gh-pages`
* we visit the page and our app is online
* to automate deploy to github we add a script to our package.json `"deploy": "npm run build && git subtree push --prefix dist origin gh-pages"`

### Lecture 48 - Deployment to AWS S3

* AWS API keys are extremely security sensitive. DO NOT SHARE THESE KEYS
* we will create API keys to delete them after
* we install yet another global npm lib `npm install -g s3-website`
* we register and enter aws dashboard, under our name we go to my security credentials-> access keys-> createnew access key
-> show the keys (DO NOT _ DO NOT Publish them on GITHUB)
* once we finish the course we will delete them
* we creTE A .ENV FILE IN PROJECT ROOT
* aws will in this file to find the keys
* add the two keys

```
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
```
* we will use now the cli tool to creae a bucket at aws `s3-website create <projectname>`
* to actually deploy we run `s3-website deploy dist` specifying the folder to deploy
* we cp the link to chrome and see our app live
* to redeploy after build we have to run the build script and the s3 deploy command
* we clean up our project to avoid unecessary charging

## Section 9 - Webpack Based Deployment for Dynamic Sites

### Lecture 49 - Deployment on Servers

* now our app will have allso a backend part
* one approach is to have one url for the frontend and one url for the backend. our frontend will access the backend to get data
* the frontend then is considered a static site as we have seen
* many large applications are built this way
* a second approach is a single server to host static assets and api logic. in that case node server will wrap all app. good for smaller apps. easier to deploy and maintain.
* in second case node server is responsible for all actions
* this is in conflict with the core of webpack. as webpack up to now was the centrel of all deployment and serving the app for development. to make webpack work with node we need to glue them together.
* now to access the app we need to go to node and node will go to webpack to get the bundle

### Lecture 50 - Node and Webpack Integration

* node behaves differently if it runs on a local machine or in a deployment environment.
* in development for app viewing or access of static resources node will use webpack middleware to pass request to webpack so that we dont have to run 2 servers
* in production envirohment we dont run webpack. we tell node to serve these assets from the dist directory
* this change of config betrween development and production is not optimal
* we install express `npm install --save express`
* we add a server.js file in project root
* we add expres boilerplate code and run node server.js to start the server

### Lecture 51 - Webpack Middleware in Development

* middleware to webpack is an express middleware. we install it `npm install --save-dev webpack-dev-middleware`
* we add in server.js

```
const webpackMiddleware = require('webpack-dev-middleware');
const webpack = require('webpack');
const webpackConfig = require('./webpack.config.js');
```

* webpack is the lib doint the bundling and translation in dev and config is its configuration

* we integrate all to express with `app.use(webpackMiddleware(webpack(webpackConfig)));`

* we run our server (use version 1.9.0 of middleware). our log looks like it runs webpack in the background. we access our localhost:3050 and the app is running ..

### Lecture 52 - Webpack Middleware in Production

* we want to avoid the middleware in production so we check the NODE_ENV environment param)
* in developemnt we set the NODE_ENV in package.json. depending on the deployment env this var might need to be set in the environment params of the host
* in production we dont eeven wan to load webpack related modules

```
if (process.env.NODE_ENV !== 'production') {
	const webpackMiddleware = require('webpack-dev-middleware');
	const webpack = require('webpack');
	const webpackConfig = require('./webpack.config.js');
	app.use(webpackMiddleware(webpack(webpackConfig)));
}
```

* if we run in production we want to serve the dist folder assets ONLY. the second line is for security. if anyone tries to acces any route on our server serve the singlepage app instead. it is fully compatible with react router. only react router can play with routes

```
else {
	app.use(express.static('dist'));
	app.get('*', (req,res) => {
		res.sendFile(path.join(__dirname, 'dist/index.html'));
	});
}
```

* we emulate rpoduction run with ` NODE_ENV=production node server.js
`

* app runs ok.
* we need to compile and build before we deploy
* when we expand our server we MUST put our server.js routes ABOVE the if statement, otherwise we will never see them because * overrules all
* heroku and aws will want to set their port not 3050, so we need to make it parametrical `process.env.PORT || 3050`

### Lecture 53 - Deploy to Heroku

* we add a file named Procfile in project root there we add the command that will start our app once deployed to Heroku `web: node server.js`
* we use the same git we used to deploy with github but we will work on master branch
* we have heroku cli
* we run heroku create in the project and then push master to heroku. our git must be only for the project. heroku sets env to production automatically
* we run heroku open and see our app in browser
* our deploy flow is

```
npm run build
git add .
git commit -m 'release xxxx'
git push heroku master
```

### Lecture 54 - Deployment to AWS 

* we will deploy to AWS elastic beanstalk service
* we need to install elastic beanstalk cli to make deployments to aws elastic beanstalk
* for linux
	* install python 3 (if not available) `sudo apt-get install python3.4 or so`
	* get installation script ` curl -O https://bootstrap.pypa.io/get-pip.py` preferably in a local dir
	* run the script with python `python3 get-pip.py --user`
	* add ~/.local/bin to the PATH . check for dir in ~
	* add `export PATH=LOCAL_PATH:$PATH` in profile (no need we have it)
	* check installation of pip with pip --version
	* install cli `pip install awsebcli --upgrade --user`
	* verify installation `eb --version`
	* to upgrade `pip install awsebcli --upgrade --user`
* we will start with `eb init`
* insert aws login credentials (again createa anew access key in aws dashboard)
* name your project, set node yes codcommit no and ssh yes
* we need to make an app environment `eb create`
* in our case it didn;t ask for git.

### Lecture 55 - More on AWS Deployment

* deploy takes a lot of time...
* we open our up with `eb-open` ERROR
* in aws console -> select the region we deployed -> select elastic benastalk -> find ow app -> see the error -> see the logs
* it looks for webpack-dev-server .... so it thinks it runs in dev AKA NODE_ENV is not set properly
* in command line we se it properly `eb setenv NODE_ENV=production`
* in dashboard we see many ionfo about our service. instances used etc.
* OUR APP is LIVE in AWS
* we now run `eb terminate` to delete it to avoid unnecessary charges
* we also delete our keys to the app