# Lighthouse Bug

A repo for reproducing a Lighthouse bug on CentOS 7.

## Lighthouse server and test page

**Server**

* CentOS 7 from official Docker package
* Node v8.11.1 (latest LTS at time of creation)
* NPM v5.6.0
* Lighthouse
* google-chrome-stable (66.x at time of creation)

**Test page**

* Basic HTML
* No script
* No CSS
* 1, 4.35 MB JPG

```html
<!doctype html>
<html lang="">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="x-ua-compatible" content="ie=edge">
        <title>Test Page</title>
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    </head>
    <body>
        <p>A Test Page</p>
        <img src="panda-bear-sleeping-on-the-ground.jpg" />
    </body>
</html>
```

## Reproduce bug

1. Install Docker
1. Build image

    ```
    cd env && docker build -t lighthouse-bug .
    ```

1. Attach to image

    ```
    docker run -it lighthouse-bug /bin/bash
    ```

1. Run Lighthouse against test page

    ```
    lighthouse https://tollmanz.github.io/lighthouse-bug-repro/page/ --verbose --disable-network-throttling --chrome-flags="--headless --disable-gpu --no-sandbox"
    ```

    A few notes about this config:

    * You can pass `--disable-network-throttling` or not. I find that the bug is more reproducible with it. Without it, you sometimes get other network errors. Also, it's a lot faster :)
    * The `--chrome-flags` used seem to be [necessary](https://tollmanz.github.io/lighthouse-bug-repro/page/) to run Chrome via the Chrome Launcher on CentOS
    * The `--verbose` flag will show the `Inspector.targetCrashed {}` error message when the bug is produced
    * The `google-chrome-stable` package is used as that seems to be the recommendation from others using CentOS and it seems to work well other than with this case

## Sample bug output

```
[root@b2d22f2aa175 /]# lighthouse https://tollmanz.github.io/lighthouse-bug-repro/page/ --verbose --chrome-flags="--headless --disable-gpu --no-sandbox" --disable-network-throttling
which: no chromium-browser in (/root/.nvm/versions/node/v8.11.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin)
which: no chromium in (/root/.nvm/versions/node/v8.11.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin)
  ChromeLauncher:verbose created /tmp/lighthouse.8nM6yZh +0ms
  ChromeLauncher:verbose Launching with command:
  ChromeLauncher:verbose "/usr/bin/google-chrome-stable" --disable-translate --disable-extensions --disable-background-networking --safebrowsing-disable-auto-update --disable-sync --metrics-recording-only --disable-default-apps --mute-audio --no-first-run --remote-debugging-port=35351 --user-data-dir=/tmp/lighthouse.8nM6yZh --disable-setuid-sandbox --headless --disable-gpu --no-sandbox about:blank +8ms
  ChromeLauncher:verbose Chrome running with pid 795 on port 35351. +7ms
  ChromeLauncher Waiting for browser. +1ms
  ChromeLauncher Waiting for browser... +0ms
  ChromeLauncher Waiting for browser..... +518ms
  ChromeLauncher Waiting for browser.....✓ +5ms
  Driver:verbose Network.enable +279ms
  method => browser:verbose Network.enable {} +1ms
  method <= browser OK:verbose Network.enable {} +26ms
  Driver:verbose Page.enable +3ms
  method => browser:verbose Page.enable {} +1ms
  method => browser:verbose Emulation.setScriptExecutionDisabled {"value":false} +0ms
  method => browser:verbose Page.navigate {"url":"about:blank"} +2ms
  method <= browser OK:verbose Page.enable {} +8ms
  method <= browser OK:verbose Emulation.setScriptExecutionDisabled {} +0ms
  method <= browser OK:verbose Page.navigate {"frameId":"36E1CF1675C3EF036F321961E2234804","loaderId":"7BE69C4CFDF5F6514E62D383C1619056"} +0ms
  <= event:verbose Page.frameStartedLoading {"frameId":"36E1CF1675C3EF036F321961E2234804"} +11ms
  <= event:verbose Page.frameNavigated {"frame":{"id":"36E1CF1675C3EF036F321961E2234804","loaderId":"7BE69C4CFDF5F6514E62D383C1619056","url":"about:blank","securityOrigin":"://","mimeType":"text/html"}} +4ms
  <= event:verbose Page.loadEventFired {"timestamp":542686.906666} +4ms
  <= event:verbose Page.frameStoppedLoading {"frameId":"36E1CF1675C3EF036F321961E2234804"} +1ms
  <= event:verbose Page.domContentEventFired {"timestamp":542686.907286} +1ms
  status Initializing… +279ms
  listen once for event =>:verbose ServiceWorker.workerRegistrationUpdated  +2ms
  Driver:verbose ServiceWorker.enable +0ms
  method => browser:verbose ServiceWorker.enable {} +0ms
  method <= browser OK:verbose ServiceWorker.enable {} +2ms
  <= event:verbose ServiceWorker.workerRegistrationUpdated {"registrations":[]} +1ms
  Driver:verbose ServiceWorker.disable +0ms
  method => browser:verbose ServiceWorker.disable {} +1ms
  <= event:verbose ServiceWorker.workerVersionUpdated {"versions":[]} +0ms
  method <= browser OK:verbose ServiceWorker.disable {} +2ms
  listen for event =>:verbose ServiceWorker.workerVersionUpdated  +1ms
  Driver:verbose ServiceWorker.enable +0ms
  method => browser:verbose ServiceWorker.enable {} +0ms
  <= event:verbose ServiceWorker.workerRegistrationUpdated {"registrations":[]} +1ms
  <= event:verbose ServiceWorker.workerVersionUpdated {"versions":[]} +0ms
  Driver:verbose ServiceWorker.disable +1ms
  method => browser:verbose ServiceWorker.disable {} +0ms
  method <= browser OK:verbose ServiceWorker.enable {} +1ms
  method <= browser OK:verbose ServiceWorker.disable {} +0ms
  method => browser:verbose Runtime.evaluate {"expression":"(function wrapInNativePromise() {\n          const __nativePromise = window.__nativePromise || Promise;\n          return new __nativePromise(function (resolve) {\n  +4ms
  method <= browser OK:verbose Runtime.evaluate {"result":{"type":"string","value":"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/66.0.3359.117 Safari/537.36"}} +6ms
  method => browser:verbose Emulation.setDeviceMetricsOverride {"mobile":true,"screenWidth":412,"screenHeight":732,"width":412,"height":732,"positionX":0,"positionY":0,"scale":1,"deviceScaleFactor":2.625,"fitWindow":false,"sc +3ms
  method => browser:verbose Network.setUserAgentOverride {"userAgent":"Mozilla/5.0 (Linux; Android 6.0.1; Nexus 5 Build/MRA58N) AppleWebKit/537.36(KHTML, like Gecko) Chrome/66.0.3359.30 Mobile Safari/537.36"} +1ms
  method => browser:verbose Emulation.setTouchEmulationEnabled {"enabled":true,"configuration":"mobile"} +1ms
  method => browser:verbose Page.addScriptToEvaluateOnLoad {"scriptSource":"(function () {\n    const touchEvents = ['ontouchstart', 'ontouchend', 'ontouchmove', 'ontouchcancel'];\n    const recepients = [window.__proto__, do +1ms
  <= event:verbose Page.frameResized {} +2ms
  <= event:verbose Page.frameResized {} +3ms
  method <= browser OK:verbose Emulation.setDeviceMetricsOverride {} +2ms
  method <= browser OK:verbose Network.setUserAgentOverride {} +0ms
  method <= browser OK:verbose Emulation.setTouchEmulationEnabled {} +4ms
  method <= browser OK:verbose Page.addScriptToEvaluateOnLoad {"identifier":"1"} +0ms
  method => browser:verbose Emulation.setCPUThrottlingRate {"rate":4} +1ms
  method => browser:verbose Network.emulateNetworkConditions {"latency":0,"downloadThroughput":0,"uploadThroughput":0,"offline":false} +1ms
  method <= browser OK:verbose Emulation.setCPUThrottlingRate {} +2ms
  method <= browser OK:verbose Network.emulateNetworkConditions {} +5ms
  Driver:verbose Runtime.enable +2ms
  method => browser:verbose Runtime.enable {} +1ms
  <= event:verbose Runtime.executionContextCreated {"context":{"id":1,"origin":"://","name":"","auxData":{"isDefault":true,"frameId":"36E1CF1675C3EF036F321961E2234804"}}} +10ms
  method <= browser OK:verbose Runtime.enable {} +1ms
  method => browser:verbose Page.addScriptToEvaluateOnLoad {"scriptSource":"window.__nativePromise = Promise;\n        window.__nativeError = Error;"} +0ms
  method <= browser OK:verbose Page.addScriptToEvaluateOnLoad {"identifier":"2"} +3ms
  method => browser:verbose Page.addScriptToEvaluateOnLoad {"scriptSource":"(function registerPerformanceObserverInPage() {\n  window.____lastLongTask = window.performance.now();\n  const observer = new window.PerformanceObse +0ms
  method <= browser OK:verbose Page.addScriptToEvaluateOnLoad {"identifier":"3"} +2ms
  listen for event =>:verbose Page.javascriptDialogOpening  +0ms
  method => browser:verbose Storage.clearDataForOrigin {"origin":"https://tollmanz.github.io","storageTypes":"appcache,file_systems,indexeddb,local_storage,shader_cache,websql,service_workers,cache_storage"} +2ms
  method <= browser OK:verbose Storage.clearDataForOrigin {} +15ms
  method => browser:verbose Emulation.setCPUThrottlingRate {"rate":4} +1ms
  method => browser:verbose Network.emulateNetworkConditions {"latency":0,"downloadThroughput":0,"uploadThroughput":0,"offline":false} +1ms
  method <= browser OK:verbose Emulation.setCPUThrottlingRate {} +2ms
  method <= browser OK:verbose Network.emulateNetworkConditions {} +10ms
  method => browser:verbose Emulation.setScriptExecutionDisabled {"value":false} +2ms
  method => browser:verbose Page.navigate {"url":"about:blank"} +0ms
  method <= browser OK:verbose Emulation.setScriptExecutionDisabled {} +9ms
  method <= browser OK:verbose Page.navigate {"frameId":"36E1CF1675C3EF036F321961E2234804","loaderId":"3E0DDE7D81501752D3D71BBDD26CE0EB"} +2ms
  <= event:verbose Page.frameStartedLoading {"frameId":"36E1CF1675C3EF036F321961E2234804"} +47ms
  <= event:verbose Runtime.executionContextDestroyed {"executionContextId":1} +1ms
  <= event:verbose Runtime.executionContextsCleared {} +0ms
  <= event:verbose Page.frameNavigated {"frame":{"id":"36E1CF1675C3EF036F321961E2234804","loaderId":"3E0DDE7D81501752D3D71BBDD26CE0EB","url":"about:blank","securityOrigin":"://","mimeType":"text/html"}} +1ms
  <= event:verbose Runtime.executionContextCreated {"context":{"id":2,"origin":"://","name":"","auxData":{"isDefault":true,"frameId":"36E1CF1675C3EF036F321961E2234804"}}} +0ms
  <= event:verbose Page.loadEventFired {"timestamp":542687.350925} +0ms
  <= event:verbose Page.frameStoppedLoading {"frameId":"36E1CF1675C3EF036F321961E2234804"} +1ms
  <= event:verbose Page.domContentEventFired {"timestamp":542687.354573} +0ms
  method => browser:verbose Network.setBlockedURLs {"urls":[]} +241ms
  method <= browser OK:verbose Network.setBlockedURLs {} +4ms
  listen for event =>:verbose Runtime.exceptionThrown  +1ms
  listen for event =>:verbose Log.entryAdded  +0ms
  Driver:verbose Log.enable +1ms
  method => browser:verbose Log.enable {} +0ms
  method <= browser OK:verbose Log.enable {} +3ms
  method => browser:verbose Log.startViolationsReport {"config":[{"name":"discouragedAPIUse","threshold":-1}]} +0ms
  method <= browser OK:verbose Log.startViolationsReport {} +4ms
  method => browser:verbose Page.addScriptToEvaluateOnLoad {"scriptSource":"(function installMediaListener() {\n  window.___linkMediaChanges = [];\n  Object.defineProperty(HTMLLinkElement.prototype, 'media', {\n    set: funct +2ms
  method <= browser OK:verbose Page.addScriptToEvaluateOnLoad {"identifier":"4"} +5ms
  status Loading page & waiting for onload URL, Scripts, CSSUsage, Viewport, ViewportDimensions, ThemeColor, Manifest, RuntimeExceptions, ChromeConsoleMessages, ImageUsage, Accessibility, EventListeners, AnchorsWithNoRelNoopener, AppCacheManifest, DOMStats, JSLibraries, OptimizedImages, PasswordInputsWithPreventedPaste, ResponseCompression, TagsBlockingFirstPaint, WebSQL, MetaDescription, FontSize, CrawlableLinks, MetaRobots, Hreflang, EmbeddedContent, Canonical, RobotsTxt, Fonts +1ms
  method => browser:verbose Network.clearBrowserCache {} +1ms
  method <= browser OK:verbose Network.clearBrowserCache {} +3ms
  method => browser:verbose Network.setCacheDisabled {"cacheDisabled":true} +0ms
  method <= browser OK:verbose Network.setCacheDisabled {} +3ms
  method => browser:verbose Network.setCacheDisabled {"cacheDisabled":false} +0ms
  method <= browser OK:verbose Network.setCacheDisabled {} +5ms
  listen once for event =>:verbose Tracing.tracingComplete  +4ms
  method => browser:verbose Tracing.end {} +0ms
  method <= browser ERR:verbose Tracing.end  +2ms
  method => browser:verbose Tracing.start {"categories":"-*,toplevel,v8.execute,blink.console,blink.user_timing,benchmark,loading,latencyInfo,devtools.timeline,disabled-by-default-devtools.timeline,disabled-by-default-devtool +4ms
  method <= browser OK:verbose Tracing.start {} +25ms
  method => browser:verbose Emulation.setScriptExecutionDisabled {"value":false} +1ms
  method => browser:verbose Page.navigate {"url":"https://tollmanz.github.io/lighthouse-bug-repro/page/"} +1ms
  listen once for event =>:verbose Page.loadEventFired  +1ms
  listen once for event =>:verbose Page.domContentEventFired  +1ms
  method <= browser OK:verbose Emulation.setScriptExecutionDisabled {} +5ms
  <= event:verbose Network.requestWillBeSent {"requestId":"0049638A7402D1E6138C3E6E7ED5FC96","loaderId":"0049638A7402D1E6138C3E6E7ED5FC96","documentURL":"https://tollmanz.github.io/lighthouse-bug-repro/page/","request":{"url" +5ms
  NetworkRecorder:verbose network semi-quiet +10ms
  <= event:verbose Network.responseReceived {"requestId":"0049638A7402D1E6138C3E6E7ED5FC96","loaderId":"0049638A7402D1E6138C3E6E7ED5FC96","timestamp":542687.794354,"type":"Document","response":{"url":"https://tollmanz.github. +104ms
  method <= browser OK:verbose Page.navigate {"frameId":"36E1CF1675C3EF036F321961E2234804","loaderId":"0049638A7402D1E6138C3E6E7ED5FC96"} +2ms
  <= event:verbose Page.frameStartedLoading {"frameId":"36E1CF1675C3EF036F321961E2234804"} +8ms
  <= event:verbose Network.dataReceived {"requestId":"0049638A7402D1E6138C3E6E7ED5FC96","timestamp":542687.806458,"dataLength":393,"encodedDataLength":256} +46ms
  <= event:verbose Runtime.executionContextDestroyed {"executionContextId":2} +3ms
  <= event:verbose Runtime.executionContextsCleared {} +1ms
  <= event:verbose Page.frameNavigated {"frame":{"id":"36E1CF1675C3EF036F321961E2234804","loaderId":"0049638A7402D1E6138C3E6E7ED5FC96","url":"https://tollmanz.github.io/lighthouse-bug-repro/page/","securityOrigin":"https://to +0ms
  <= event:verbose Runtime.executionContextCreated {"context":{"id":3,"origin":"https://tollmanz.github.io","name":"","auxData":{"isDefault":true,"frameId":"36E1CF1675C3EF036F321961E2234804"}}} +0ms
  <= event:verbose Network.loadingFinished {"requestId":"0049638A7402D1E6138C3E6E7ED5FC96","timestamp":542687.801822,"encodedDataLength":591,"blockedCrossSiteDocument":false} +0ms
  NetworkRecorder:verbose network fully-quiet +2ms
  <= event:verbose Network.requestWillBeSent {"requestId":"847.2","loaderId":"0049638A7402D1E6138C3E6E7ED5FC96","documentURL":"https://tollmanz.github.io/lighthouse-bug-repro/page/","request":{"url":"https://tollmanz.github.i +16ms
  NetworkRecorder:verbose network semi-quiet +1ms
  <= event:verbose Page.domContentEventFired {"timestamp":542687.871965} +0ms
  <= event:verbose Network.resourceChangedPriority {"requestId":"847.2","newPriority":"High","timestamp":542687.889696} +34ms
  <= event:verbose Network.responseReceived {"requestId":"847.2","loaderId":"0049638A7402D1E6138C3E6E7ED5FC96","timestamp":542687.908868,"type":"Image","response":{"url":"https://tollmanz.github.io/lighthouse-bug-repro/page/p +5ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542687.917635,"dataLength":10873,"encodedDataLength":10882} +10ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542687.933432,"dataLength":16375,"encodedDataLength":16384} +17ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542687.938314,"dataLength":5348,"encodedDataLength":0} +3ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542687.969847,"dataLength":49125,"encodedDataLength":54500} +29ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.001427,"dataLength":65500,"encodedDataLength":65536} +35ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.006303,"dataLength":36,"encodedDataLength":0} +13ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.013134,"dataLength":16339,"encodedDataLength":16384} +34ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.024063,"dataLength":32768,"encodedDataLength":0} +3ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.036729,"dataLength":16357,"encodedDataLength":0} +13ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.052857,"dataLength":5958,"encodedDataLength":96079} +29ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.094785,"dataLength":32768,"encodedDataLength":65536} +17ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.111288,"dataLength":32768,"encodedDataLength":0} +1ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.118934,"dataLength":65536,"encodedDataLength":57344} +24ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.145754,"dataLength":65536,"encodedDataLength":81920} +10ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.152394,"dataLength":49017,"encodedDataLength":0} +4ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.155495,"dataLength":16375,"encodedDataLength":16384} +1ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.191189,"dataLength":43609,"encodedDataLength":32768} +47ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.192691,"dataLength":21927,"encodedDataLength":0} +8ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.213678,"dataLength":65536,"encodedDataLength":131072} +10ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.229216,"dataLength":65536,"encodedDataLength":49152} +42ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.294806,"dataLength":65536,"encodedDataLength":163840} +36ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.307707,"dataLength":65536,"encodedDataLength":32768} +33ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.33743,"dataLength":65536,"encodedDataLength":163840} +15ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.35309,"dataLength":65536,"encodedDataLength":32768} +9ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.353685,"dataLength":65536,"encodedDataLength":0} +1ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.354047,"dataLength":43609,"encodedDataLength":0} +1ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.354107,"dataLength":21927,"encodedDataLength":0} +1ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.354582,"dataLength":16051,"encodedDataLength":0} +34ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.409278,"dataLength":49125,"encodedDataLength":49152} +22ms
  <= event:verbose Network.dataReceived {"requestId":"847.2","timestamp":542688.421952,"dataLength":32768,"encodedDataLength":81920} +10ms
  <= event:verbose Inspector.targetCrashed {} +128ms
  ```
