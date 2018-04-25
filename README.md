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
    lighthouse https://tollmanz.github.io/lighthouse-bug-repro/page/ --verbose --disable-network-throttling
    ```
