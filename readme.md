# Dject Dynamic Double Loader #

A simple tool for building test doubles. The Dject Dynamic Double Loader allows you to create injectable dependencies which are easy to consume and easy to use in your tests. It's actually harder to write about how this works than to just demonstrate it!

## Installation ##

In your project run this:

`npm i dject-dynamic-double-loader --save-dev`

Done!

## Using it ##

### Writing a Test Double ###

If you use Dject to load your test doubles, the loader works a little like this:

(This example was pulled from a real test double! Sinon usage not required, but it sure makes testing easier!)

```javascript
function textFileServiceFake(
    djectDynamicDoubleLoader,
    sinon
) {
    'use strict';

    // Yes, this is really all you need to create a fully injectible test double!
    return djectDynamicDoubleLoader
        .buildDjectTestDouble('textFileService')
        .appendProperty('readJsonFile', sinon.stub())
        .appendProperty('writeTextFile', sinon.stub());
}

module.exports = textFileServiceFake;
```

If you don't want to do it this way, here's what you can do instead:

```javascript
const djectDynamicDoubleLoader = require('dject-dynamic-double-loader');
const sinon = require('sinon');

module.exports = djectDynamicDoubleLoader
    .buildDjectTestDouble('textFileService')
    .appendProperty('readJsonFile', sinon.stub())
    .appendProperty('writeTextFile', sinon.stub());
```

Seriously, I couldn't make this easier.

### Using your Test Double ###

Once you are down to writing your test, it just looks like this:

```javascript
const container = require('../container');
const testContainer = require('./testContainer');

describe('doing some testing', function () {
    let importDIBuilder;
    let textFileServiceFake;

    beforeEach(function () {
        const appContainer = container.new();

        const someFakeData = testContainer.build('someFakeData');

        // No, seriously, this is it! All the code lives in the test double file
        textFileServiceFake = testContainer.build('textFileServiceFake');
        textFileServiceFake.readJsonFile.callsFake(() => someFakeData);

        appContainer.register(textFileServiceFake);

        importDIBuilder = appContainer.build('importDIBuilder');
    });

    it('connects the dots, la la lala', function() {
        // your test code goeth here.
    });
});
```

## Version History ##

**v1.0.0**

Initial commit - Introduced core functionality:

- `djectDynamicDoubleLoader.buildDjectTestDouble(string)`
- `testDouble.appendProperty(string, any)`