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
