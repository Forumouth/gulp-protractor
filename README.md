# gulp-protractor [![NPM version][version-img]][github] [![Build Status][travis-image]][travis-url] [![Dependency Status][depstat-image]][depstat-url]

> Run your [angular protractor](https://github.com/angular/protractor) tests with [gulp](https://github.com/wearefractal/gulp)

## Usage

First, install `gulp-protractor` AND `protractor` as a development dependency:

```shell
npm install --save-dev gulp-protractor protractor
```

Then, add it to your `gulpfile.js`:

```javascript
var protractor = require("gulp-protractor").protractor;

gulp.src(["./src/tests/*.js"])
  .pipe(protractor({
    configFile: "test/protractor.config.js",
    args: ['--baseUrl', 'http://127.0.0.1:8000']
  }))
  .on('error', function(e) { throw e })
```

### Protractor Webdriver
You have to update and start a standalone selenium server. [Please read the offical instructions](https://github.com/angular/protractor#appendix-a-setting-up-a-standalone-selenium-server).  

You can also ensure that the driver is installed by using the `webdriver_update` task. Have a look at the example folder.

You have 2 options to start the selenium server.  

The first one is to let Protractor handle it automatically, including stopping it once your tests are done.  
To do that, simply point to the selenium jar in the protractor config file (you will need to update the version number accordingly) instead of the address:

```javascript
  // The file path to the selenium server jar ()
  seleniumServerJar: './node_modules/protractor/selenium/selenium-server-standalone-2.45.0.jar',
  // seleniumAddress: 'http://localhost:4444/wd/hub',
```

The second option is to let the gulp task handle it with the built-in webdriver snippet.  
If you decide to start it that way, the task will keep running indefinitely.

```javascript
var webdriver_standalone = require("gulp-protractor").webdriver_standalone;
gulp.task('webdriver_standalone', webdriver_standalone);
```

## API

### protractor(options)

#### options.configFile
Type: `String`  
Default: `null`

The path to your protractor config

#### options.args
Type: `Array`  
Default: `[]`

Arguments get passed directly to the protractor call [Read the docs for more information](https://github.com/angular/protractor/blob/master/docs/getting-started.md#setup-and-config)

#### options.debug
Type: `Boolean`
Default: `false`

Enables Protractor's [debug mode](https://github.com/angular/protractor/blob/master/docs/debugging.md), which can be used to pause tests during execution and to view stack traces.


# Running Protractor without a plugin
If you want to avoid using a plugin at all, here are gulp tasks which install the webdriver and start protractor:

First install the protractor package:
```npm install --save-dev protractor```

Then add these tasks to your ```gulpfile.js```:
```javascript
var gulp = require('gulp');
var path = require('path');
var child_process = require('child_process');

function getProtractorBinary(binaryName){
  var winExt = /^win/.test(process.platform)? '.cmd' : '';
  var pkgPath = require.resolve('protractor');
  var protractorDir = path.resolve(path.join(path.dirname(pkgPath), '..', 'bin'));
  return path.join(protractorDir, '/'+binaryName+winExt);
}

gulp.task('protractor-install', function(done){
  child_process.spawn(getProtractorBinary('webdriver-manager'), ['update'], {
    stdio: 'inherit'
  }).once('close', done);
});

gulp.task('protractor-run', function (done) {
  var argv = process.argv.slice(3); // forward args to protractor
  child_process.spawn(getProtractorBinary('protractor'), argv, {
    stdio: 'inherit'
  }).once('close', done);
});

```

You can add command line arguments for protractor behind the gulp task:

```gulp protractor-run --specs=runOnlyThisFile.js```


## License

[MIT License](http://en.wikipedia.org/wiki/MIT_License)

[github]: https://github.com/forumouth/gulp-protractor
[version-img]: https://badge.fury.io/gh/Forumouth%2Fgulp-protractor.svg

[travis-url]: http://travis-ci.org/Forumouth/gulp-protractor
[travis-image]: https://travis-ci.org/Forumouth/gulp-protractor.svg?branch=master

[depstat-url]: https://david-dm.org/Forumouth/gulp-protractor
[depstat-image]: https://david-dm.org/Forumouth/gulp-protractor.svg
