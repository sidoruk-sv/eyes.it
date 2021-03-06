### Why do I need this?

Easily integrate protractor with [eyes](https://applitools.com/) when using jasmine2 as testing framework. All you have to do is use `eyes.it` in your tests instead of `it` and it will run the test with screenshots sent to eyes after each `browser.get()` and at the end of the test.

### Installing

```sh
npm install --save-dev eyes.it
```

### Usage

Add environment variable with your eyes api key (key here is only example, get your own!):
```sh
export EYES_API_KEY=6QGH9IA5nkK1wRt60I1EWybFMWTJ2R1kcwu07y41lYh0LNWu3r
```

#### Default usage
In your protractor tests:
```js
const eyes = require('eyes.it');
// or import in TS or JS ES6:
import eyes from 'eyes.it';

eyes.it('should run tests with eyes', async () => {
    await browser.get('/');
    await $('input').sendKeys('123');
    await $('button').click();
    expect(await $('span').text()).toBe('123');
});
```

This will take a 2 snapshots by default:
 - Immediately After the `browser.get()`
 - At the end of the test.

#### Configure (disable) default snapshots
You can disable both default snapshots, and take a snapshot manually using `eyes.checkWindow()`.
- Disabling `browser.get` snapshot - usefull when you are getting the same page in multiple tests.
- Disabling `end` snapshot - When you have multiple `eyes.checkWindow` calls in a test (with propper descriptions), you might want a meaningful description for the last `eyes.checkWindow` call. (The default snapshot's description is simply "end")
```js
const eyes = require('eyes.it');

eyes.it('should run tests with eyes', async () => {
    await browser.get('/');

    await $('input').sendKeys('123');
    await $('button').click();
    expect(await $('span').text()).toBe('123');
    await eyes.checkWindow('should be 123');

    await $('input').sendKeys('456');
    await $('button').click();
    expect(await $('span').text()).toBe('456');
    await eyes.checkWindow('should be 456');
}, {enableSnapshotAtBrowserGet: false, enableSnapshotAtEnd: false});
```

#### Set default window size

```js
const eyes = require('eyes.it');

eyes.defaultWindowSize = {width: 1024, height: 768};

eyes.it('should run tests with eyes', async () => {
    await browser.get('/');
    await $('input').sendKeys('123');
    await $('button').click();
    expect(await $('span').text()).toBe('123');
});
```

#### Set window size for a single spec

```js
const eyes = require('eyes.it');

eyes.it('should run tests with eyes', async () => {
    await browser.get('/');
    await $('input').sendKeys('123');
    await $('button').click();
    expect(await $('span').text()).toBe('123');
}, {width: 1024, height: 768});
```

#### Debugging (Focused tests)
You can also use `eyes.fit` in case you need to use focused tests.

#### Running locally
If you do not have `EYES_API_KEY` environment variable, `eyes.it` will behave just like regular `it`.

You can simulate an Applitools Github integration for pull requests (see [here](https://applitools.com/docs/topics/integrations/github-integration.html])), by adding an `APPLITOOLS_BATCH_ID` environment variable. `APPLITOOLS_BATCH_ID` should be the commit hash of the branch HEAD. This will be set as the batch id of tests.

For instance you can add this to you `package.json`:
```json
{
  "scripts": {
    "test": "APPLITOOLS_BATCH_ID=<your-HEAD-hash> yoshi test"
  }
}
```

If you are running with few browser instances, you can get all running tests grouped together by setting `process.env.EYES_BATCH_UUID = require('uuid').v4()'` in your grunt file (or other node process that runs the build), you can also define it as an environment variable (you have to make sure that each run will set a different value to distinguish between runs).
Notice this will not work if you're using the `APPLITOOLS_BATCH_ID` environment variable.


## Useful Links

- Enabling eyes.sdk [verbose logging](https://help.applitools.com/hc/en-us/articles/360006914932-How-can-I-enable-Applitools-Eyes-SDK-traces-logs-)
