page_description: Get started using CURL to query search engines - The search engine Spider
page_title: Using Curl

Curl HTTP Client
================

Curl adapter for a simple scraping implementation

---

Curl Adapter will allow to use the built in php CURL extension. This adapter **fully supports proxies and cookies**

Installation
------------

The client is available with the package 
[serps/http-client-curl](https://packagist.org/packages/serps/http-client-curl): 

``$ composer require 'serps/http-client-curl'``

### Additional requirement

This http client requires you to [install the Curl php extension](http://php.net/manual/fr/curl.installation.php)

## Usage

```php
use Serps\HttpClient\PhantomJsClient;

$client = new CurlClient();

$response = $client->query($request);

// you can optionally add a proxy and a cookie as a second and third parameters
$response = $client->query($request, $proxy, $cookieJar);
```
