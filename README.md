# Puppeteer Cheat Sheet
Puppeteer Cheat Sheet with the most needed stuff..

# DOCS
- https://github.com/puppeteer/puppeteer/blob/v5.2.1/docs/api.md




<br><br>

## Missing dependencies
In some cases maybe those dependencies are missing on debian and you can not start chrome. To solve install (https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#chrome-headless-doesnt-launch-on-unix):
```bash
sudo apt-get install ca-certificates fonts-liberation libappindicator3-1 libasound2 libatk-bridge2.0-0 libatk1.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgbm1 libgcc1 libglib2.0-0 libgtk-3-0 libnspr4 libnss3 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 lsb-release wget xdg-utils
```



## Performance
In some cases you maybe have extremly long loading times in headless mode specially on server. To fix this you must change the user-agent:
```javascript
await page.setUserAgent('Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3851.0 Safari/537.36');
```




































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>



## Kill all processes

```bash
# debian
pkill chrome
```

























<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>



# screenshot (https://github.com/puppeteer/puppeteer/blob/v5.2.1/docs/api.md#pagescreenshotoptions)

<br><br>

## Take screenshot as buffer and then save the buffer
```bash
const image = await page.screenshot()
await fs.writeFile('test.jpg', image, 'binary')
```
<br><br>

## Take screenshot and directly save the image
```bash
const image = await page.screenshot({path: 'test.jpg'})
```

































































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>


# import function as string to DOM and execute
```javascript
  /**
   * Execute script inside of DOM by creating new function and run it
   * @param {string} script - Script we want to execute
   * @param {object} page - PPTR page
  */
  async evalScript(script, page) {
    return await page.evaluate(async script=>{
      return new Function('return ' + script)()();
    }, script); // return await page.evaluate(async script=>{
  };
```











































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>



# Iterating

```javascript
// method 1 (cheerio)
const $ = cheerio.load( await page.evaluate(() => document.querySelector('body').outerHTML) );

let ar = [];
$( 'li.s-item' ).each(function(){  
    ar.push( $( this ).find( '.s-item__link' ).attr( 'href' ) );
})



// method 2 (puppeteer)
const hrefs1 = await page.evaluate(() => Array.from(
               document.querySelectorAll('.zp_PrhFA a[href]'),
               a => a.getAttribute('href')
));

   
   
// method 3 (puppeteer)
const elementHandles = await page.$$('.zp_PrhFA a');
const propertyJsHandles = await Promise.all(  elementHandles.map(handle => handle.getProperty('href'))  );	  
const hrefs2 = await Promise.all(  propertyJsHandles.map(handle => handle.jsonValue())  );


// method 4 (puppeteer)
const scrappedSingleItemURLs_AR = await page.evaluate(() => {

       const all = document.querySelectorAll('.s-item__link');
       var ar = [];

       if(all){
          for( const d of all ){
	   ar.push(d.getAttribute('href')?.replace( /\?_trkparms=(.*)/gmi, '' ));
	   } return ar;
       }

});
```















































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>



# Browser

<br><br>

## .disconnect()
- https://github.com/puppeteer/puppeteer/blob/v5.2.1/docs/api.md#browserdisconnect
- Disconnects Puppeteer from the browser, but leaves the Chromium process running. After calling disconnect, the Browser object is considered disposed and cannot be used anymore.
```javascript
await browser.disconnect()
```  






























































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>



# Page

## Open new page
```javascript
var newTab = await client.newPage();
await newTab.goto(link, {waitUntil: 'load', timeout: 35000});
```  



<br><br>

# Close current Page
```javascript
await page.close();        
```  


<br><br>

# Close all Pages
```javascript
/**
*
* @returns {array} - All open pages from PPTR.
*/
async getPages() {
  return await this.browser.pages()
}

/**
* Close all pages
*/
async closePages() {
  const ar = await this.getPages()
  for(const page of ar) {
    await page.close()
  }
} 
```  


<br><br>

# Stop loading of current page
```javascript
page._client.send('Page.stopLoading');      
```  

<br><br>

## Get all pages (https://github.com/puppeteer/puppeteer/blob/v5.2.1/docs/api.md#browsercontextpages)
- returns: <Promise<Array<Page>>> Promise which resolves to an array of all open pages. Non visible pages, such as "background_page", will not be listed here. You can find them using target.page().
```javascript
var allPages = await browser.pages);
```  

<br><br>

## Wait until new link loaded after click on submit as example
```javascript
await page.waitForNavigation();   
```  

<br><br>

## Get current url
```javascript
// https://github.com/puppeteer/puppeteer/blob/main/docs/api.md#pagewaitfornavigationoptions
console.log( page.url() );      
```  

<br><br>

## Get url of redirect
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





















































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>

## Cheerio

```javascript
// You can use body or :root
let css = await page.evaluate(() => document.querySelector('body').outerHTML);
var $ = cheerio.load(css);
let errorMessage = $(css).find('.ytp-error-content-wrap-reason > span').text();
log( 'errorMessage:' + errorMessage + '\n\n' );
```  










































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>

# Pause
```javascript
await page.waitFor(1000);         
```  

<br><br>

# Close Session
```javascript
await client.close();        
```  

              
<br><br>

# If State
```javascript
if ( await page.$('#confirm-button') ) await page.click('#confirm-button');
if ( !await page.$('#confirm-button') ) await page.click('#confirm-button');  
if (   await page.evaluate(() => document.querySelector('.zp_1FMXH span.zp_22lW4')?.innerText?.includes('Account'))   ){        
```  

<br><br>

# Delete cookies
```javascript
// does not work in headless
let client = await page.target().createCDPSession();
await client.send('Network.clearBrowserCookies');
await client.send('Network.clearBrowserCache');  
```  

<br><br>

# Incognito window
```javascript
// When you close context at the end and you want to create a new session after you must create again a new context
const context = await browser.createIncognitoBrowserContext();
const page = await context.newPage();

// Execute your code
await page.goto('...');
// ...

await context.close(); // clear history
```  



# Copy text from clipboard
```javascript
//method #1
const context = await browser.defaultBrowserContext()
await context.overridePermissions('https://example.com', ['clipboard-read'])
const copiedText = await page.evaluate(`(async () => await navigator.clipboard.readText())()`)

//method #2 (copy the clipboard from your os - Not working in headless because most clipboards are only browser internal)
const clipboardy = require('clipboardy');
clipboardy.writeSync('🦄');
clipboardy.readSync();
//=> '🦄'
```  

# Fix for Javascript on website stops when window not in focus
```javascript
const session = await page.target().createCDPSession();
await session.send('Page.enable');
await session.send('Page.setWebLifecycleState', {state: 'active'});
```  



# Emulate timezone
```javascript
await page.emulateTimezone( 'Europe/berlin' );
console.log( await page.evaluate(() => date.toString()) );
```  















































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>


# Viewport


## Check if visible
```javascript
// check currently if element is visble in viewport
let playButton = await page.$('.ytp-large-play-button.ytp-button');
if (await playButton.isIntersectingViewport()) console.log( 'Large play button was found.. Video did not started itself' );


//method 1
// wait until element gets visible.. timeout 0 means wait forever until visible
// return JSHandle@node if true or undefenied if false
let visible = await page.waitForSelector('#user-panel', {visible: true, timeout:0});

// method 2
try {
  await page.waitForSelector('.zp_PrhFA a', {visible: true, timeout:30000});
  singleItemURL_JSON.contactemail = true;
} catch(e) {
  log( 'email error: ' + e.message );
}	
```


<br><br>



## Change Viewport of page
```javascript
const viewport = {width: 1920, height: 1080}
await page.setViewport({...viewport})
```  


































































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>


# waitFor

## Wait until element got specific text
```javascript
await page.waitFor((name) => {
 return document.querySelector('.top .name')?.textContent == name;
}, {timeout: 60000}, test_client2.name);
```







































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>


# Try
```javascript
/* this works with any puppeteer command.. You can check if something is visible as example and if not you get error and can work wit ith

#waitUntil
- networkidle0 comes handy for SPAs that load resources with fetch requests. consider navigation to be finished when there are no more than 0 network connections for at least 500 ms
- networkidle2 comes handy for pages that do long-polling or any other side activity. consider navigation to be finished when there are no more than 2 network connections for at least 500 ms.
- load = will also wait until page is fully loaded but this can be also used for redirects inside.


timeout = time until we wait before we stop waiting..

*/
async function openLink(page, link){
log( 'openLink() - link: ' + link );

  try { await page.goto(link, {waitUntil: 'networkidle0', timeout: 35000});
  } catch(e) { log( 'openLink() - Error while open link.. Error: ' + e?.message );

    if( e?.message.match('Navigation timeout of') ) log( '#2 - Navigation timeout was found we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_EMPTY_RESPONSE' ) ) log( '#2 - net::ERR_EMPTY_RESPONSE was found we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_NETWORK_CHANGED' ) ) log( '#2 - net::ERR_NETWORK_CHANGED was found we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_NAME_NOT_RESOLVED' ) ) log( '#2 - net::ERR_NAME_NOT_RESOLVED was found we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_CONNECTION_CLOSED' ) ) log( '#2 - net::ERR_CONNECTION_CLOSED was found we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_PROXY_CONNECTION_FAILED' ) ) log( '#2 - net::ERR_PROXY_CONNECTION_FAILED was found.. Maybe your proxy is offline? Maybe change your proxy.. However we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_CONNECTION_REFUSED' ) ) log( '#2 - net::ERR_CONNECTION_REFUSED was found we reload page in 30 seconds..\n\n' );
    if( e?.message.match( 'net::ERR_CONNECTION_TIMED_OUT' ) ) log( '#2 - net::ERR_CONNECTION_TIMED_OUT was found we reload page in 30 seconds..\n\n' );

    // optional timeout
    await new Promise(resolve => setTimeout(resolve, 30000));

  }; return true;

} // async function openLink(page, link){
```  















































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>

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
















































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>

# Get Data


## Get text
```javascript
//method #1
let videoDuration = await page.evaluate(element => element.textContent, await page.$(".ytp-time-duration") );

//method #2
let reason = await page.evaluate(() => document.querySelector('#reason').textContent);

//method #3 (cheerio) - You can use aswell :root instead of body
let css = await page.evaluate(() => document.querySelector('body').outerHTML);
let $ = cheerio.load(css);
let errorMessage = $(css).find('.ytp-error-content-wrap-reason > span').text();
console.log( 'errorMessage:' + errorMessage + '\n\n' );  
```  

## Get attribute
```javascript
//method #1
let contactlinkedin = await page.evaluate(() => document.querySelector('.zp_3_fnL').getAttribute('href') );
```  


## Find element with specific text
```javascript
// METHOD 1 (WARNING - Does not work when multiple elements with the same css selector exist. It will always match the first result)
let el = await page.evaluate(() => document.querySelector('div')?.innerText?.includes('Share URL')  );

// METHOD 2
let emailcheck = await page.evaluate(() => {  if (Array.from(document.querySelectorAll('a.zp_3_fnL')).find(el => el.textContent === 'Access Email')) return true;  });
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

// method #3
await page.evaluate(() => { document.querySelector('textarea').value = 'sample_message123'; });
```


















































<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>


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
  
  
  
  
  /* You can use the executablePath like above to use specific browser binary or you can use the product value to choose chrome/firefox.


  On linux terminals you should be able to use:
  - PUPPETEER_PRODUCT=firefox npm install puppeteer

  After this run:
  npm install puppeteer
  
  
  
  
  If not you must set PUPPETEER_PRODUCT as environment variable by using:
  npm config set PUPPETEER_PRODUCT firefox npm i puppeteer

  Then you can can run after this inside of your project:
  npm i puppeteer

  To install chrome just change again to and then install again:
  npm config set PUPPETEER_PRODUCT chrome npm i puppeteer
  
  Alternative you can try it aswell for your project only  (https://github.com/CyberT33N/nodejs-cheat-sheet/blob/master/README.md#environment-variable)
  
  _________________

  If your firefox is not starting use:
  
    args: [
	  '-wait-for-browser'
	  ]
  
  */
  product: 'chrome',
  
  
  
  
  
  headless: false,
  devtools: true, // <-- open dev tools and also force headfull mode
  ignoreHTTPSErrors: true,
  
  
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

# iFrames

## Interact with iFrames
```javascript
const elementHandle = await page.$('#iframe');
const frame = await elementHandle.contentFrame();
await frame.click('#introAgreeButton');
```


<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>

# .exposeFunction
- Insert function from Node APP to DOM
```javascript
await page.exposeFunction('config', ()=>{
    return JSON.parse(  fs.readFileSync('./admin/config.json', 'utf8')  );
});

await page.evaluate(async () => {
  // window.myConfig = await config(); // <-- alternative you can create global variable too
  const myConfig = await config();
  console.log(`config is ${JSON.stringify(myConfig)}`);
});
```

<br><br>
 _____________________________________________________
 _____________________________________________________
<br><br>


# Devices

## Simulate Devices (https://github.com/puppeteer/puppeteer/blob/v1.18.1/docs/api.md#puppeteerdevices)
```javascript
const puppeteer = require('puppeteer');
const iPhone = puppeteer.devices['iPhone 6'];

puppeteer.launch().then(async browser => {
  const page = await browser.newPage();
  await page.emulate(iPhone);
  await page.goto('https://www.google.com');
  // other actions...
  await browser.close();
});
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

