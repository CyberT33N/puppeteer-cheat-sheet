# Puppeteer Cheat Sheet
Puppeteer Cheat Sheet with the most needed stuff..



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



# Viewport
```javascript
// check currently if element is visble in viewport
let playButton = await page.$('.ytp-large-play-button.ytp-button');
if (await playButton.isIntersectingViewport()) console.log( 'Large play button was found.. Video did not started itself' );

// wait until element gets visible.. timeout 0 means wait forever until visible
await page.waitForSelector('#user-panel', {visible: true, timeout:0});
console.log( 'User Panel visble..' );                             
```  




# Try
```javascript
/* this works with any puppeteer command.. You can check if something is visible as example and if not you get error and can work wit ith

#waitUntil
networkidle0 = wait until document lead and loading bar is way
load = wait until page is loaded (not fully some elements may still load on same pages not recommend to use if you wait for something or scrap text)

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

 return;

} // catch(e) {

```  


# Get Text
```javascript
//method #1
let videoDuration = await page.evaluate(element => element.textContent, await page.$(".ytp-time-duration") );

//method #2 (cheerio) - You can use aswell :root instead of body
let css = await page.evaluate(() => document.querySelector('body').outerHTML);
let $ = cheerio.load(css);
let errorMessage = $(css).find('.ytp-error-content-wrap-reason > span').text();
console.log( 'errorMessage:' + errorMessage + '\n\n' );  
```  

# Enter Text
```javascript
// method #1  
await page.$eval('input[name=search]', (el, value) => el.value = value, 'text here..');

// method #2 (human like typing)
await page.type('input[data-ng-model="file.title"]', 'namehere', { delay: 100 });
```



# Click
```javascript
// method #1
await page.click('#dropDown > li[data-production-type=example]')

// method #2
let item = '.ytp-play-button.ytp-button';
await page.evaluate((item) => {
  document.querySelector(item).click();
}, item);                          
```



# Proxy/Socks
```javascript
/*
Use --proxy-server at the launch option as example:
--proxy-server='server:port'

and then later authenticate with the code from below..

for some reasons socks5 not working with this method.. In order to use proxy and socks in more viable way use:
- https://github.com/Cuadrix/puppeteer-page-proxy
*/
await page.authenticate({'username': vpn_username, 'password': vpn_password});
```




# Upload
```javascript
await page.waitForSelector('input[type=file]');
await page.waitFor(1000);
let input = await page.$('input[type="file"]');
await input.uploadFile(filepath);           
```  



# Tabs
```javascript
// switch between tabs/bring them in focus
await page1.bringToFront();
await page2.bringToFront();                   
```  




# Launch
```javascript
client = await puppeteer.launch({
  //executablePath: '/snap/bin/chromium',
  //executablePath: '/usr/bin/google-chrome',
  //executablePath: '/home/user/Downloads/Linux_x64_749751_chrome-linux/chrome-linux/chrome',
  //executablePath: '/home/user/Downloads/firefox-78.0a1.en-US.linux-x86_64/firefox/firefox',
  headless: headlessVALUE, // true or false
  userDataDir: '../../../../../lib/browserProfiles/' + config_browser_profile,
  args: [
  
'--disable-extensions-except=../../../../../lib/chromeextension/webrtc_anti_leak_prevent/eiadekoaikejlgdbkbdfeijglgfdalml/1.0.14_0,../../../../../lib/chromeextension/script_safe/oiigbmnaadbkfbmpbfijlflahbdbdgdf/1.0.9.3_0',

'--load-extension=../../../../../lib/chromeextension/webrtc_anti_leak_prevent/eiadekoaikejlgdbkbdfeijglgfdalml/1.0.14_0',
'--load-extension=../../../../../lib/chromeextension/script_safe/oiigbmnaadbkfbmpbfijlflahbdbdgdf/1.0.9.3_0',


  //'--window-size=' + windowWidth + ',' + windowHeight;
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
```
