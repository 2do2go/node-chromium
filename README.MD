# chromium-binary [![Build Status](https://travis-ci.org/2do2go/node-chromium.svg?branch=master)](https://travis-ci.org/2do2go/node-chromium) [![XO code style](https://img.shields.io/badge/code_style-XO-blue.svg)](https://github.com/sindresorhus/xo) [![npm version](https://badge.fury.io/js/chromium-binary.svg)](https://badge.fury.io/js/chromium-binary) [![Platforms](https://img.shields.io/badge/platforms-Win/Linux/Mac-lightgrey.svg)](https://github.com/2do2go/node-chromium)
> Chromium binaries for your NodeJS project

This is fork of [node-chromium](https://github.com/dtolstyi/node-chromium) by Dmytro Tolstyi.

**node-chromium** allows you to easily add [Chromium](https://www.chromium.org/) binaries of desired version to your project and use it for automation, testing, web scraping or just for fun.
Chromium version will correspond to package version, i.e. if package version is 60.0.3112-0 then chromium 60.0.3112.0 will be installed.

## Why Chromium?
[Chromium](https://www.chromium.org/) is an open-source web browser developed and maintained by The Chromium Project. Google Chrome, also released in 2008, is a proprietary web browser developed and maintained by Google. The reason why Chrome and Chromium are tied to each other is that Chome borrows Chromium’s source code.  
The main benefit of using Chromium is that it **doesn't** include all the proprietary modifications made by Google, thus it's more lightweight and more suitable for automation purposes.  
You can see full list of differences in [Fossbytes article](https://fossbytes.com/difference-google-chrome-vs-chromium-browser/).

## Usage
Depending on your needs, you can install module into **devDependencies** (`--save-dev`) or production **dependencies** (`--save`).

```
npm install --save chromium-binary
```

During the installation process **node-chromium** will download corresponded chromium version and extract into libraries folder. As soon as installation is finished, you are ready to use Chromium in your project:

```js
const chromiumBinary = require('chromium-binary');
const {execFile} = require('child_process');

execFile(chromiumBinary.path, ['https://google.com'], err => {
	console.log('Hello Google!');
});
```

## Selenium WebDriver Headless (without UI) tests
It's extremely easy to use **node-chromium** with **selenium-webdriver** to perform e2e tests without spawning browser UI.
First, install all dependencies

```
npm install --save chromium-binary@60.0.3112-0 chromedriver@2.33.0 selenium-webdriver@3.6.0
```

After the installation is finished, create simple script that opens Google Search home page and takes it's screenshot in headless mode.

```js
const fs = require('fs');
const webdriver = require('selenium-webdriver');
const chrome = require('selenium-webdriver/chrome');
const chromiumBinary = require('chromium-binary');
require('chromedriver');

async function start() {

    let options = new chrome.Options();
    options.setChromeBinaryPath(chromiumBinary.path);
    options.addArguments('--headless');
    options.addArguments('--disable-gpu');
    options.addArguments('--window-size=1280,960');

    const driver = await new webdriver.Builder()
        .forBrowser('chrome')
        .setChromeOptions(options)
        .build();
		
    await driver.get('http://google.com');
    console.log('Hello Google!');
    await takeScreenshot(driver, 'google-start-page');
    
    await driver.quit();
}

async function takeScreenshot(driver, name) {
	await driver.takeScreenshot().then((data) => {
        fs.writeFileSync(name + '.png', data, 'base64');
        console.log('Screenshot is saved');
    });
}

start();
```

## License
MIT
