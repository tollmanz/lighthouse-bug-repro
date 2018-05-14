# Headless Chrome Bug

A repo for reproducing a [Chrome Headless issue](https://bugs.chromium.org/p/chromium/issues/detail?id=842679) on CentOS 7 and OS X 10.13.3.

## Synopsis

When loading a page with a large image using Headless Chrome on CentOS 7.4.x, Chrome crashes with an `Abnormal renderer termination` error. Similarly, when running Headless Chrome on OS X, the same page crashes Chrome only when remote debugging is enabled.

*Note that this started as a [Lighthouse bug](https://github.com/GoogleChrome/lighthouse/issues/5044), but after further investigation, it appears to be a Chrome bug.*

## Lighthouse environment and test page

**Environments**

* CentOS 7

    ```
    [root@b2d22f2aa175 /]# cat /etc/*-release
    CentOS Linux release 7.4.1708 (Core)
    NAME="CentOS Linux"
    VERSION="7 (Core)"
    ID="centos"
    ID_LIKE="rhel fedora"
    VERSION_ID="7"
    PRETTY_NAME="CentOS Linux 7 (Core)"
    ANSI_COLOR="0;31"
    CPE_NAME="cpe:/o:centos:centos:7"
    HOME_URL="https://www.centos.org/"
    BUG_REPORT_URL="https://bugs.centos.org/"

    CENTOS_MANTISBT_PROJECT="CentOS-7"
    CENTOS_MANTISBT_PROJECT_VERSION="7"
    REDHAT_SUPPORT_PRODUCT="centos"
    REDHAT_SUPPORT_PRODUCT_VERSION="7"

    CentOS Linux release 7.4.1708 (Core)
    CentOS Linux release 7.4.1708 (Core)
    ```

    google-chrome-stable (66.x at time of creation)

    ```
    [root@9da8e76601b6 /]# google-chrome-stable --version
    Google Chrome 66.0.3359.170
    ```

* OS X

    Version 10.13.3

    ```
    ❯❯❯ /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --version
    Google Chrome 66.0.3359.139
    ```

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

## Steps to reproduce

There's two variants of this bug. Below are steps to reproduce on Debian and OS X.

**Debian**

1. Clone repo `git clone https://github.com/tollmanz/lighthouse-bug-repro.git --branch headless-chrome`
1. Install Docker
1. Build image

    ```
    cd lighthouse-bug-repro/env && docker build -t lighthouse-bug .
    ```

1. Attach to image

    ```
    docker run -it lighthouse-bug /bin/bash
    ```

1. Load test page with big image using Headless Chrome

    ```
    google-chrome-stable --headless --enable-logging --v=99 --no-sandbox --disable-gpu https://tollmanz.github.io/lighthouse-bug-repro/page/index.html
    ```

    Sample output:

    ```
    [root@00335566dbd5]# google-chrome-stable --headless --no-sandbox --disable-gpu https://tollmanz.github.io/lighthouse-bug-repro/page/index.html
    [0514/020857.234627:ERROR:gpu_process_transport_factory.cc(1007)] Lost UI shared context.
    Fontconfig warning: "/etc/fonts/fonts.conf", line 146: blank doesn't take any effect anymore. please remove it from your fonts.conf
    [0514/020857.693097:ERROR:crash_handler_host_linux.cc(432)] Failed to write crash dump for pid 1534
    Cannot upload crash dump: failed to open
    [0514/020857.744852:ERROR:headless_shell.cc(348)] Abnormal renderer termination.
    --2018-05-14 02:08:57--  https://clients2.google.com/cr/report
    Resolving clients2.google.com (clients2.google.com)... 172.217.9.142, 2607:f8b0:4000:813::200e
    Connecting to clients2.google.com (clients2.google.com)|172.217.9.142|:443... connected.
    HTTP request sent, awaiting response... 403 Forbidden
    2018-05-14 02:08:57 ERROR 403: Forbidden.


    Unexpected crash report id length
    Failed to get crash dump id.
    Report Id:
    ```

**OS X**

Headless Chrome on OS X appears to also crash when loading this page; however, it only occurs when remote debugging is enabled.

1. Run the following, which should produce an unknown error and attempt to send a bug report:

    ```
    /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9223 --headless --enable-logging --v=99 https://tollmanz.github.io/lighthouse-bug-repro/page/index.html
    ```

    Adjust the path to Chrome as needed.

    Sample output:

    ```
    ❯❯❯ chrome --remote-debugging-port=9223 --headless --enable-logging --v=99 https://tollmanz.github.io/lighthouse-bug-repro/page/index.html
    [0514/083619.799527:ERROR:xattr.cc(64)] setxattr org.chromium.crashpad.database.initialized on file /var/folders/62/yj6q_lss5yqc6h0sjhm4cyc00000gn/T/: Operation not permitted (1)
    [0514/083619.799005:ERROR:xattr.cc(64)] setxattr org.chromium.crashpad.database.initialized on file /var/folders/62/yj6q_lss5yqc6h0sjhm4cyc00000gn/T/: Operation not permitted (1)
    [0514/083619.799820:INFO:crashpad_client_mac.cc(292)] restarting handler in 0.982s
    [0514/083619.848508:ERROR:gpu_process_transport_factory.cc(1007)] Lost UI shared context.
    [0514/083619.848719:VERBOSE1:webrtc_internals.cc(125)] Could not get the download directory.

    DevTools listening on ws://127.0.0.1:9223/devtools/browser/c558873f-e657-4fa5-8518-580cfcd291d9
    [0514/083619.901851:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Argon2018' log
    [0514/083619.901936:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Argon2019' log
    [0514/083619.901981:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Argon2020' log
    [0514/083619.902021:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Argon2021' log
    [0514/083619.902061:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Aviator' log
    [0514/083619.902101:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Icarus' log
    [0514/083619.902140:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Pilot' log
    [0514/083619.902203:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Rocketeer' log
    [0514/083619.902248:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Google 'Skydiver' log
    [0514/083619.902289:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Cloudflare 'Nimbus2018' Log
    [0514/083619.902330:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Cloudflare 'Nimbus2019' Log
    [0514/083619.902370:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Cloudflare 'Nimbus2020' Log
    [0514/083619.902414:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Cloudflare 'Nimbus2021' Log
    [0514/083619.902450:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: DigiCert Log Server
    [0514/083619.902484:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: DigiCert Log Server 2
    [0514/083619.902520:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Symantec log
    [0514/083619.902556:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Symantec 'Vega' log
    [0514/083619.902591:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Symantec 'Sirius' log
    [0514/083619.902627:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Venafi Gen2 CT log
    [0514/083619.902663:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: CNNIC CT log
    [0514/083619.902718:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Comodo 'Sabre' CT log
    [0514/083619.902760:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Comodo 'Mammoth' CT log
    [0514/083619.902797:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: StartCom log
    [0514/083619.902832:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: WoSign log
    [0514/083619.902867:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Izenpe log
    [0514/083619.902903:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Venafi log
    [0514/083619.902954:VERBOSE1:multi_log_ct_verifier.cc(75)] Adding CT log: Certly.IO log
    [0514/083619.911137:VERBOSE1:network_delegate.cc(30)] NetworkDelegate::NotifyBeforeURLRequest: https://tollmanz.github.io/lighthouse-bug-repro/page/index.html
    [0514/083619.969329:INFO:cpu_info.cc(50)] Available number of cores: 4
    [0514/083620.012332:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.013276:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.013688:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.014066:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.014427:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.014789:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.017301:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.020293:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.021149:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.022970:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.038670:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.039776:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.040166:VERBOSE2:ThreadState.cpp(533)] [state:0x10b9ba090] ScheduleGCIfNeeded
    [0514/083620.048355:VERBOSE2:ThreadState.cpp(496)] [state:0x10b9ba090] SchedulePageNavigationGCIfNeeded: estimatedRemovalRatio=0.75
    [0514/083620.053295:VERBOSE1:network_delegate.cc(30)] NetworkDelegate::NotifyBeforeURLRequest: https://tollmanz.github.io/lighthouse-bug-repro/page/panda-bear-sleeping-on-the-ground.jpg
    [0514/083620.804956:ERROR:xattr.cc(64)] setxattr org.chromium.crashpad.database.initialized on file /var/folders/62/yj6q_lss5yqc6h0sjhm4cyc00000gn/T/: Operation not permitted (1)
    [0514/083620.810235:INFO:crashpad_client_mac.cc(292)] restarting handler in 0.974s
    [0514/083621.803483:ERROR:xattr.cc(64)] setxattr org.chromium.crashpad.database.initialized on file /var/folders/62/yj6q_lss5yqc6h0sjhm4cyc00000gn/T/: Operation not permitted (1)
    [0514/083621.804158:INFO:crashpad_client_mac.cc(292)] restarting handler in 0.981s
    [0514/083622.803439:ERROR:xattr.cc(64)] setxattr org.chromium.crashpad.database.initialized on file /var/folders/62/yj6q_lss5yqc6h0sjhm4cyc00000gn/T/: Operation not permitted (1)
    [0514/083622.804323:INFO:crashpad_client_mac.cc(292)] restarting handler in 0.981s
    ```

1. Run the following, which is the same as the first step, but without remote debugging enabled. It should not produce an error:

    ```
    /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --headless --enable-logging --v=99 https://tollmanz.github.io/lighthouse-bug-repro/page/index.html
    ```
