page_description: Get started using PhantomJS to query search engines - The search engine Spider
page_title: Using PhantomJS

PhantomJS HTTP Client
=====================

<img class="frameless-image" alt="PhantomJS logo" src="../../images/phantomjs.png"/>

<center>[PhantomJS](http://phantomjs.org/) is a webkit implementation that helps to simulate the real browser.</center>

---

By using this client you will execute the inner javascript code and make the DOM as real as in the true browser,
that can be required for some search engines to work properly.

!!! info "This is an evaluating adapter"
    When you use phantomJS client and you submit a request from it the resulting DOM 
    might be different of the source code returned by a server because
    javascript is executed before returning the DOM.

!!! danger "Known bug"
    PhantomJS scrapping might be affected by [this bug](https://github.com/ariya/phantomjs/issues/12750)

!!! warning "Notice about cookies"
    At the current state phantomJS adapter does not support internal cookieJar usage.

Installation
------------

The client is available with the package 
[serps/http-client-phantomjs](https://packagist.org/packages/serps/http-client-phantomjs): 

``$ composer require 'serps/http-client-phantomjs'``

### Additional requirement

**[PhantomJS](http://phantomjs.org/) binaries have to be installed** to use the client. The process for installing
it depends on your environment, you will find further guides on the internet.

!!! note
    The package [jakoch/phantomjs-installer](https://github.com/jakoch/phantomjs-installer) 
    can help you to manage phantomJS as a dependency of your project.

## Usage

```php
use Serps\HttpClient\PhantomJsClient;

// The client needs the path to the binary.
// By default it will use 'phantomjs'
$client = new PhantomJsClient(__DIR__ . '/bin/phantomjs');

$response = $client->query($request);

// you can optionally add a proxy and a cookie as a second and third parameters
// NOTE: though cookieJar is available, it is not implemented yet
$response = $client->query($request, $proxy, $cookieJar);
```
