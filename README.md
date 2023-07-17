Mutual Authentication in Selenium
---

A simple piece of code that uses LittleProxy as a Man-In-The-Middle proxy to be used by e.g. Selenium to connect to 
servers that require client side certificate, working around the problem of WebDriver not exposing any APIs to select 
one. To use it, create a proxy server, feed it a client certificate the server accepts and a server certificate the 
client trusts, then configure WebDriver instance to use it.

See [a full, self-contained example here](src/test/java/proxy/auth/MutualAuthenticationTest.java).  

Basic usage:

    File clientCertFile = ...;        // client cert as .p12
    char[] clientCertPassword = ...;  // key password 
    File serverCertFile = ...;        // PEM file
    File serverKeyFile = ...;         // PEM file

    org.littleshoot.proxy.impl.DefaultHttpProxyServer.bootstrap()
			.withName(clientCertFile.getName())
			.withPort(5555)
			.withAllowLocalOnly(true)
			.withManInTheMiddle(new MutualAuthenticationCapableMitmManager(
					usingPKCS12File(clientCertFile, clientCertPassword), 
					usingPemKeyPair(serverCertFile, serverKeyFile)))
			.start();

    org.openqa.selenium.Proxy proxy = new Proxy();
    proxy.setSslProxy("127.0.0.1:5555");
    proxy.setNoProxy("<-loopback>"); // overwrite the default no-proxy for localhost, 127.0.0.1
    
    FirefoxOptions options = new FirefoxOptions();
    options.setProxy(proxy);
    WebDriver driver = new FirefoxDriver(options);

Selenium OpenShift Templates
===

> OpenShift Templates used for a Scalable Selenium infrastructure and these templates tested on OpenShift 4.5.8.

Usage
===


```bash
$ oc create -f selenium-hub.yaml
$ oc create -f selenium-node-chrome.yaml
$ oc create -f selenium-node-firefox.yaml
$ oc process selenium-hub | oc create -f -
$ oc process selenium-node-chrome | oc create -f -
$ oc process selenium-node-firefox | oc create -f -
```

Once all pods up and running and check the grid status with the following endpoint and you should see following output.
Hub status url: https://OCP-ROUTE/wd/hub/status

**Note:** Please replace OCP-ROUTE based on your environment.

```
{
  "value": {
    "ready": true,
    "message": "Selenium Grid ready.",
    "nodes": [
      {
        "id": "72eaea16-1c0a-44b2-987f-c71cc36d6a77",
        "uri": "http:\u002f\u002f10.254.16.254:5555",
        "maxSessions": 1,
        "stereotypes": [
          {
            "capabilities": {
              "browserName": "chrome"
            },
            "count": 1
          }
        ],
        "sessions": [
        ]
      },
      {
        "id": "ee1d92e8-52cd-40a0-9516-cb4c424da8d8",
        "uri": "http:\u002f\u002f10.254.17.0:5555",
        "maxSessions": 1,
        "stereotypes": [
          {
            "capabilities": {
              "browserName": "firefox"
            },
            "count": 1
          }
        ],
        "sessions": [
        ]
      }
    ]
  }
}
```

In case you want to have VNC access to your chrome node, you need to add node chrome debug and you can follow same steps for firefox node as well.
```
$ oc create -f selenium-node-chrome-debug.yaml
```
and then to view the node via VNC, you use port forwarding to localhost.

First you get the pod of chrome debug
```
$ oc get pods
NAME                                 READY     STATUS      RESTARTS   AGE
selenium-hub-1-b8w96                 1/1       Running     5          4d
selenium-node-chrome-debug-1-2gcqn   1/1       Running     4          3d

```
Run port forwarding
```
$ oc port-forward -p selenium-node-chrome-debug-1-2gcqn 5900:5900
Forwarding from 127.0.0.1:5900 -> 5900
Forwarding from [::1]:5900 -> 5900
Handling connection for 5900
```

```
$ vncviewer 127.0.0.1:5900
```
NB: The default password to access to VNC is `secret`. You can change it by editing the chrome debug Dockerfile following [this](https://github.com/SeleniumHQ/docker-selenium/tree/master/NodeChromeDebug#how-to-use-this-image).


Example
===

![openshift 1 hub 1 node](http://i.imgur.com/Ux3VcE3.png)
![hub 1 node](http://i.imgur.com/FBIDvta.png)
![openshift 1 hub 1 nodes](http://i.imgur.com/JpMkwTP.png)
![hub 2 nodes](http://i.imgur.com/LBqQ0KS.png)

# Install chromedriver
1. Go to your temp folder:
cd /tmp/

2.  Download the latest Linux-based Chromedriver:
wget
https://chromedriver.storage.googleapis.com/114.0.5735.90/chromedriver_linux64.zip

3.  Extract Chromedriver from its archive:
unzip chromedriver_linux64.zip

4. Move Chromedriver to the applications folder:
sudo mv chromedriver /usr/bin/chromedriver

5. Confirm Chromedriver version:
chromedriver --version

# Installing Google-chrome:
1. Use this command to install the newest version of the chrome browser:
curl https://intoli.com/install-google-chrome.sh | bash

2. Rename google-chrome-stable with google-chrome, so that automation tests will be able to identify the Chrome browser before test execution starts:
mv /usr/bin/google-chrome-stable /usr/bin/google-chrome

3. Verify Chrome installation:
google-chrome --version && which google-chrome

Or npm i chrome
