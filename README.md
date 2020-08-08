# Puppeteer Cheat Sheet
Puppeteer Cheat Sheet with the most needed stuff..



## URL

#### Wait until new link loaded after click on submit as example
```javascript
await page.waitForNavigation();   
```  

#### Get current url
```javascript
// https://github.com/puppeteer/puppeteer/blob/main/docs/api.md#pagewaitfornavigationoptions
console.log( page.url() );      
```  

#### Get url of redirect
```javascript
/*

import array with redirect urls and get back as return an array which the final redirected urls

*WARNING* This will be executed parallel so only import small range of links because for each link we open a new tab for redirect check. If you open too many tabs parallel you may crash your Browser. Alternative build in any wait method to prevent overload of tabs.
*/

async function getRedirectURL(importedArray){
log( 'ENTER getRedirectURL() - Imported Array: ' + importedArray );


  let arrayRedirectsURLS = [];
  if( importedArray[0] ){



        await Promise.all(
              importedArray.map(async d => {
              log( 'Current array item we process: ' + d );

                let pageTMP = await client.newPage();
                await pageTMP.goto(d, {waitUntil: 'load', timeout: 35000});

                let currenturl = pageTMP.url()
                log( 'Final url after redirect: ' + currenturl );

                pageTMP.close();
                arrayRedirectsURLS.push(currenturl);

              }) // tmpAR.map(async process => {
        ); // await Promise.all(



  } //    if( importedArray[0] ){
  log( 'redirect loop done..' );

  await page.bringToFront();
  return arrayRedirectsURLS;


} //  async function getRedirectURL(importedArray){




```  



<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />

## Cheerio

```javascript
// You can use body or :root
let css = await page.evaluate(() => document.querySelector('body').outerHTML);
var $ = cheerio.load(css);
let errorMessage = $(css).find('.ytp-error-content-wrap-reason > span').text();
log( 'errorMessage:' + errorMessage + '\n\n' );
```  

<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />

# Pause
```javascript
await page.waitFor(1000);         
```  


# Close Session
```javascript
await client.close();        
```  



# If State
```javascript
// true
if ( await page.$('#confirm-button') ) await page.click('#confirm-button');
//false
if ( !await page.$('#confirm-button') ) await page.click('#confirm-button');    
```  


# Delete cookies
```javascript
let client = await page.target().createCDPSession();
await client.send('Network.clearBrowserCookies');
await client.send('Network.clearBrowserCache');  
```  


# Copy text from clipboard
```javascript
//method #1
const context = await browser.defaultBrowserContext()
await context.overridePermissions('https://example.com', ['clipboard-read'])
const copiedText = await page.evaluate(`(async () => await navigator.clipboard.readText())()`)
```  

# Fix for Javascript on website stops when window not in focus
```javascript
const session = await page.target().createCDPSession();
await session.send('Page.enable');
await session.send('Page.setWebLifecycleState', {state: 'active'});
```  

<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />


# Viewport
```javascript
// check currently if element is visble in viewport
let playButton = await page.$('.ytp-large-play-button.ytp-button');
if (await playButton.isIntersectingViewport()) console.log( 'Large play button was found.. Video did not started itself' );

// wait until element gets visible.. timeout 0 means wait forever until visible
await page.waitForSelector('#user-panel', {visible: true, timeout:0});
console.log( 'User Panel visble..' );                             
```  


<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />


# Try
```javascript
/* this works with any puppeteer command.. You can check if something is visible as example and if not you get error and can work wit ith

#waitUntil
networkidle0 comes handy for SPAs that load resources with fetch requests.
networkidle2 comes handy for pages that do long-polling or any other side activity.
load = will also wait until page is fully loaded but this can be also used for redirects inside.

timeout = time until we wait before we stop waiting..

*/
try {
 await page.goto('https://github.com/CyberT33N', {waitUntil: 'networkidle0', timeout: 35000});
} catch(e) {
console.log( 'error: ' + e.message );


 if( e.message.match('Navigation timeout of') ){
      console.log( '#2 - timeout was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } // else from if( e.match('Navigation timeout of') ){

 if ( e.message.match( 'net::ERR_EMPTY_RESPONSE' ) ){
      console.log( '#2 - net::ERR_EMPTY_RESPONSE was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } // if ( e.match( 'net::ERR_EMPTY_RESPONSE' ) ){

 if ( e.message.match( 'net::ERR_NETWORK_CHANGED' ) ){
      console.log( '#2 - net::ERR_NETWORK_CHANGED was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } //    if ( e.message.match( 'net::ERR_NETWORK_CHANGED' ) ){

 if ( e.message.match( 'net::ERR_NAME_NOT_RESOLVED' ) ){
      console.log( '#2 - net::ERR_NAME_NOT_RESOLVED was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } // if ( e.match( 'net::ERR_EMPTY_RESPONSE' ) ){

 if ( e.message.match( 'net::ERR_CONNECTION_CLOSED' ) ){
      console.log( '#2 - net::ERR_CONNECTION_CLOSED was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } // if ( e.match( 'net::ERR_EMPTY_RESPONSE' ) ){

  if ( e.message.match( 'net::ERR_PROXY_CONNECTION_FAILED' ) ){
       console.log( '#2 - net::ERR_PROXY_CONNECTION_FAILED was found.. Maybe your proxy is offline? Maybe change your proxy.. However we reload page in 30 seconds..' );
       setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
  } // if ( e.match( 'net::ERR_EMPTY_RESPONSE' ) ){

 if ( e.message.match( 'net::ERR_CONNECTION_REFUSED' ) ){
      console.log( '#2 - net::ERR_CONNECTION_REFUSED was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } // if ( e.match( 'net::ERR_EMPTY_RESPONSE' ) ){

 if ( e.message.match( 'net::ERR_CONNECTION_TIMED_OUT' ) ){
      console.log( '#2 - net::ERR_CONNECTION_TIMED_OUT was found we reload page in 30 seconds..' );
      setTimeout(() => { process.nextTick(startYoutTube) }, 30000);
 } //   if ( e.match( 'net::ERR_EMPTY_RESPONSE' ) ){

 return;

} // catch(e) {

```  

<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />

# Key presses

## Enter
```javascript
// In most cases the field you want to use enter must be focus. You can do this by click as example
await page.click('input[role="combobox"');

//method #1
page.keyboard.press('Enter');

//method #2
await page.type(String.fromCharCode(13));
```  

<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />

# Get Text
```javascript
//method #1
let videoDuration = await page.evaluate(element => element.textContent, await page.$(".ytp-time-duration") );

//method #2
await page.evaluate(() => document.querySelector('#reason').textContent);

//method #3 (cheerio) - You can use aswell :root instead of body
let css = await page.evaluate(() => document.querySelector('body').outerHTML);
let $ = cheerio.load(css);
let errorMessage = $(css).find('.ytp-error-content-wrap-reason > span').text();
console.log( 'errorMessage:' + errorMessage + '\n\n' );  
```  

## Find element with specific text
```javascript



         // extract the Page class
         const { Page } = require("puppeteer/lib/Page");

         /**
          * @name elementContains
          * @param {String} selector specific selector globally search and match
          * @param {String} text filter the elements with the specified text
          * @returns {Promise} elementHandle
          */
         Page.prototype.elementContains = function elementContains(...args) {
           return this.evaluateHandle((selector, text) => {
             // get all selectors for this specific selector
             const elements = [...document.querySelectorAll(selector)];
             // find element by text
             const results = elements.filter(element => element.innerText.includes(text));
             // get the last element because that's how querySelectorAll serializes the result
             return results[results.length-1];
           }, ...args);
         };



         /**
          * Replicate the .get function
          * gets an element from the executionContext
          * @param {String} selector
          * @returns {Promise}
          */
         const { JSHandle } = require("puppeteer/lib/JSHandle");
         JSHandle.prototype.get = function get(selector) {
           // get the context and evaluate inside
           return this._context.evaluateHandle(
             (element, selector) => {
               return element.querySelector(selector);
             },
             // pass the JSHandle which is itself
             this,
             selector
           );
         };



         await quoteActive();
async function quoteActive(){
log( 'quoteActive()' );

let el = await page.elementContains('div', 'Loading quote');
log( 'el: ' + el );

// if true it will be JSHandle@node if false it will be JSHandle:undefined
if ( el == 'JSHandle@node' ) {
     log( 'Still loading quotes..' );
     await quoteActive();
} else {
    log( 'quotes are loaded..' );
    return true;
}

} // function quoteActive(){
log( 'quoteActive() done..' );
``` 




# Enter Text
```javascript
// In most cases method #1 is not working cause the input val cant be found by the website at validation


// method #1 (not recommended for most cases)
await page.$eval('input[name=search]', (el, value) => el.value = value, 'text here..');


// method #2 (human like typing - most viable for all inputs)
// you must set a delay or you may get type errors and wrong characters
// Some input boxes require to verify the input by press enter. In order to be able to press enter you must click or focus the input before this

//await page.click('input[data-ng-model="file.title"]');
await page.type('input[data-ng-model="file.title"]', 'namehere', { delay: 10 });
//await page.keyboard.press('Enter');
```


<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />


# Hover
```javascript
await page.hover('.ytp-progress-bar-container');                       
```

# Focus
```javascript
const searchInput = await page.$('input[name=q]');
await searchInput.focus();
```

# Blur
```javascript
await page.$eval('input[id="search"]', e => e.blur());
```

# Click
```javascript
// method #1
await page.click('#dropDown > li[data-production-type="example"]');

// method #2
let item = '.ytp-play-button.ytp-button';
await page.evaluate((item) => {
  document.querySelector(item).click();
}, item);                          
```

#### Click on element with specific text

```javascript
// method #1
let selector = 'button';
    await page.$$eval(selector, anchors => {
        anchors.map(anchor => {
            if(anchor.textContent == '+ Add Dropoff Notes') {
                anchor.click();
                return
            }
        })
    });
    
// method #2
await page.evaluate(() => {
  [...document.querySelectorAll('button')].find(element => element.textContent === '+ Add Dropoff Notes').click();
});
```

<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />

# Proxy/Socks
```javascript
/*
Use --proxy-server at the launch option as example:
--proxy-server='server:port'

and then later authenticate with the code from below..

for some reasons socks5 not working with this method.. In order to use proxy and socks in more viable way use:
- https://github.com/Cuadrix/puppeteer-page-proxy
*/
await page.authenticate({'username': username, 'password': password});
```


<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />


# Upload
```javascript
await page.waitForSelector('input[type=file]');
await page.waitFor(1000);
let input = await page.$('input[type="file"]');
await input.uploadFile(filepath);           
```  

<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />


# Tabs
```javascript
// switch between tabs/bring them in focus
await page1.bringToFront();
await page2.bringToFront();                   
```  


<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />


# Launch
```javascript
client = await puppeteer.launch({
  //executablePath: '/snap/bin/chromium',
  //executablePath: '/usr/bin/google-chrome',
  //executablePath: '/home/user/Downloads/Linux_x64_749751_chrome-linux/chrome-linux/chrome',
  //executablePath: '/home/user/Downloads/firefox-78.0a1.en-US.linux-x86_64/firefox/firefox',
  headless: false,
  userDataDir: '../../../../../lib/browserProfiles/' + config_browser_profile,
  args: [
  
'--disable-extensions-except=../../../../../lib/chromeextension/webrtc_anti_leak_prevent/eiadekoaikejlgdbkbdfeijglgfdalml/1.0.14_0,../../../../../lib/chromeextension/script_safe/oiigbmnaadbkfbmpbfijlflahbdbdgdf/1.0.9.3_0',

'--load-extension=../../../../../lib/chromeextension/webrtc_anti_leak_prevent/eiadekoaikejlgdbkbdfeijglgfdalml/1.0.14_0',
'--load-extension=../../../../../lib/chromeextension/script_safe/oiigbmnaadbkfbmpbfijlflahbdbdgdf/1.0.9.3_0',


  '--window-size=' + windowWidth + ',' + windowHeight;
  '--disable-gpu'
  '--disable-flash-3d',
  '--no-sandbox',
  // '--disable-setuid-sandbox',

  '--disable-popup-blocking',
  '--disable-notifications',
  '--disable-dev-shm-usage',
  '--force-webrtc-ip-handling-policy=disable-non-proxied-udp',
  '--disable-flash-stage3d',
  '--disable-java',
  '--disable-internal-flash',
  '--disable-cache',
  '--disable-webgl', // webgl
  '--disable-3d-apis', // webgl
  //'--disable-extensions',
  '--disable-webgl-image-chromium',
  //'--disable-reading-from-canvas', // <-- youtube videos not playing with this enabled

  '--lang=en'

  ]


});

page = await client.newPage();
await page.waitFor(5000);
await page.bringToFront();
await page.setViewport({width:windowWidth, height:windowHeight});

```


<br />
<br />


 _____________________________________________________
 _____________________________________________________


<br />
<br />

# Third Party Links

#### Chromium Browser extension
- Screen Video recorder: https://chrome.google.com/webstore/detail/nimbus-screenshot-screen/bpconcjcammlapcogcnnelfmaeghhagj?hl=es

