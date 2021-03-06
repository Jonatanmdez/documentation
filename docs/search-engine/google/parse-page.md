page_description: Accurate guide about how to parse a google page with SERPS - the Search Engine Spider.

# Parse a Google Page

<img class="frameless-image" alt="Google logo" src="../../../images/logo-google.png"/>

<center>The necessary documentation about parsing a google page</center>

---

Back to the [**general google documentation**](../google.md).

---


!!! warning "Important notice about google update"
    The current documentation can change at any time. As soon as google changes its page structure
    the following example may stop to work correctly.
    
    We place efforts to monitor the changes but we cannot guarantee that everything will be available at any time.
    
    Remember to tune your composer.json correctly to make sure to bring new changes as they come. 
    We use the [semantic versioning](http://semver.org/) 
    and the best practise is to [use the tilde operator](https://getcomposer.org/doc/articles/versions.md#tilde). 


![Classical Results](images/serp.png)

A google SERP can contain different type of result.
Firstly they are divided in three distinct regions: **natural** (organic), **paid** (adwords) and **graph results** and each of them
has its own results types. Graph result are currently **not supported** by the library.

Through there is a great diversity of results the library gives you the api to work with them, here we document
what are their differences.


## Natural Results

Natural results (aka organic results) are main results of the page.

Each natural result has a position and some available data. You can access them the following way (see the foreach loop):

```php
    use Serps\SearchEngine\Google\GoogleClient;
    use Serps\SearchEngine\Google\GoogleUrl;

    $googleClient = new GoogleClient($httpClient);

    $googleUrl = new GoogleUrl();
    $google->setSearchTerm('simpsons');

    $response = $googleClient->query($googleUrl);

    $results = $response->getNaturalResults();

    foreach($results as $result){
        // Here we iterate over the result list
        // Each result will have different data based on its type
    }
```

Each of the result from the loop will have the following methods available:

- ``getTypes()``: the types of the result
- ``is($type)``: check if the result is of the given type
- ``getDataValue($type)``: Get the given data from the result
- ``getData()``: Get the all the data of the result
- ``getOnPagePosition()``: Get the position of the result on the page (not aware of the pagination)
- ``getRealPosition()``: Get the global position of the result (aware of the pagination)

The difference between each result type is the list of data available with ``getDataValue($type)`` and ``getData()``.
See bellow for all available data per result type.

### Natural Result Types

**Result types** can be accessed through the class ``NaturalResultType``,

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    if($result->is(NaturalResultType::CLASSICAL)){
        // Do stuff
    }

    // You can also check many types at once
    // Here we check if the result is classical or image group

    if($result->is(NaturalResultType::CLASSICAL, NaturalResultType::IMAGE_GROUP)){
        // Do stuff
    }
```

From the **resultSet** you can also access all the results matching one of the given type:

```php
    // Get all the results that are either classical or image_group
    $results = $results->getResultsByType(NaturalResultType::CLASSICAL, NaturalResultType::IMAGE_GROUP);
```



#### Classical

These results are the common natural results that have always existed in google.

![Classical Results](images/result-types/classical.png)


**Available with**

- ``NaturalResultType::CLASSICAL``

**Data**

- ``title`` <small>**string**</small> [**A**]
- ``url`` <small>**string**</small>: the url targeted on clicking the title
- ``destination`` <small>**string**</small> [**B**]: either a url or a breadcrumb-like destination
- ``description`` <small>**string**</small> [**C**]

**Example**

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    $results = $response->getNaturalResults();
    
    foreach($results as $result){
        if($result->is(NaturalResultType::CLASSICAL)){
            $title = $result->getDataValue('title');
            $url   = $result->getDataValue('url');
        }
    }
```

#### Classical Video

This type an extension of the [classical result](#classical), but it refers to a video result.

The video result can be illustrated with either a thumbnail or a large image.

![Video Results](images/result-types/classical_video.png)


**Available with**

- ``NaturalResultType::CLASSICAL_VIDEO``
- ``NaturalResultType::CLASSICAL``

**Data**

- ``title`` <small>**string**</small> [**A**]
- ``url`` <small>**string**</small>: the url targeted on clicking the title
- ``destination`` <small>**string**</small> [**B**]: either a url or a breadcrumb-like destination
- ``description`` <small>**string**</small> [**C**]
- ``videoLarge`` <small>**bool**</small>: true if the video is image is large (usually first result)
- ``videoCover`` <small>**string**</small>: the video picture as given by google - either an image url or a base64 encoded image

**Example**

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    
    $results = $response->getNaturalResults();
    
    foreach($results as $result){
        if($result->is(NaturalResultType::CLASSICAL_VIDEO)){
            $title = $result->getDataValue('title');
            if($result->getDataValue('videoLarge'){
                // ...
            }
        }
    }
```


#### Image Group

Images that appear as a group of results.


![Image Group Result](images/result-types/image_group.png)


**Available with**

- ``NaturalResultType::IMAGE_GROUP``

**Data**

- ``images`` <small>**array**</small>: the list of images that compose the image group, each image contains:
    - ``sourceUrl`` <small>**Url**</small>: the url where the image was found
    - ``targetUrl``<small>**Url**</small>: the url reached on clicking the image
    - ``image`` <small>**string**</small>: the image data as specified by google (either an image url or a base64 encoded image)
- ``moreUrl`` <small>**Url**</small>: The url corresponding to the google image search

**Example**

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    
    $results = $response->getNaturalResults();
    
    foreach($results as $result){
        if($result->is(NaturalResultType::IMAGE_GROUP)){
            foreach($result->getDataValue('images') as $image){
                $sourceUrl = $image->getDataValue('sourceUrl');
            }
        }
    }
```


#### Map

A result illustrated by a map and that contains sub-results.


![Map Result](images/result-types/map.png)


**Available with**

- ``NaturalResultType::MAP``

**Data**

- ``localPack`` <small>**array**</small>: The sub results for the map:
    - ``title`` <small>**string**</small> **[A]**: Name of the place
    - ``url``<small>**Url**</small> **[B]**: Website of the sub-result
    - ``street`` <small>**string**</small> **[C]**: The address of the sub-result
    - ``stars`` <small>**string**</small> **[D]**: The rating of the result as a number
    - ``review`` <small>**string**</small> **[E]**: The review string as specified by google (e.g '1 review')
    - ``phone`` <small>**string**</small> **[G]**: The phone number
- ``mapUrl`` <small>**Url**</small> **[F]**: The url to access the map search

**Example**

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    
    $results = $response->getNaturalResults();
    
    foreach($results as $result){
        if($result->is(NaturalResultType::MAP)){
            foreach($result->getDataValue('localPack') as $place){
                $website = $place->getDataValue('website');
            }
        }
    }
```


#### Tweet Carousel

Recent tweet list from an user matching the search keywords.


![Tweets Carousel](images/result-types/tweet_carousel.png)


**Available with**

- ``NaturalResultType::TWEETS_CAROUSEL``

**Data**

- ``title`` <small>**string**</small> **[A]**
- ``url`` <small>**string**</small>: The url reach when clicking the title
- ``user``<small>**Url**</small>: The author of the tweets

**Example**

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    
    $results = $response->getNaturalResults();
    
    foreach($results as $result){
        if($result->is(NaturalResultType::TWEETS_CAROUSEL)){
            $user = $result->getDataValue('user');
        }
    }
```

#### In the News

Recent news results.


![In the News](images/result-types/in_the_news.png)


**Available with**

- ``NaturalResultType::IN_THE_NEWS``

**Data**

- ``news`` <small>**array**</small>
    - ``title`` <small>**string**</small> **[A]**
    - ``description`` <small>**Url**</small> **[B]**
    - ``url``<small>**string**</small>: The url reached when clicking the title

**Example**

```php
    use Serps\SearchEngine\Google\NaturalResultType;

    
    $results = $response->getNaturalResults();
    
    foreach($results as $result){
        if($result->is(NaturalResultType::IN_THE_NEWS)){
            $title = $result->getDataValue('title');
        }
    }
```





## Adwords Results


The google client offers an Adwords parser.

```php
    $adwordsResults = $response->getAdwordsResults();
    
    foreach($results as $result){
        // do stuff
    }
```

### Adwords sections

Adwords results are composed from 3 distinct sections. These sections can be at the top, at the right or at the bottom
of the natural results. See the schema:

![Adwords positions](images/adwords-positions.jpg)


By default all results are available in the result set, if you need 
to get results from a section, you can use the section as a type filter:

```php
    use Serps\SearchEngine\Google\AdwordsResultType;

    $adwordsResults = $response->getAdwordsResults();
    
    $topResults = $adwordsResults->getResultsByType(AdwordsResultType::SECTION_TOP);
    $rightResults = $adwordsResults->getResultsByType(AdwordsResultType::SECTION_RIGHT);
    $bottomResults = $adwordsResults->getResultsByType(AdwordsResultType::SECTION_BOTTOM);
    
    foreach($topResults as $result){
        // Do stuff...
    }
```

### Adwords Types

#### Ad


Ads results are the basics results from adwords.


![Adwords ads](images/result-types/adwords_ad.png)


**Available with**

- ``AdwordsResultType::AD``

**Data**

- ``title`` <small>**string**</small> **[A]**
- ``url`` <small>**url**</small>: The url reach when clicking the title
- ``visurl`` <small>**string**</small> **[B]**: The visual url
- ``description``<small>**string**</small> **[C]**

**Example**

```php
    use Serps\SearchEngine\Google\AdwordsResultType;

    
    $results = $response->getAdwordsResults();
    
    foreach($results as $result){
        if($result->is(AdwordsResultType::AD)){
            $url = $result->getDataValue('url');
        }
    }
```

#### Shopping


These are the result from google shopping/merchant.


![Google shopping](images/result-types/adwords_shopping.png)


**Available with**

- ``AdwordsResultType::SHOPPING_GROUP``

**Data**

- ``products`` <small>**array**</small>: The product list. Each product contains the following items:
    - ``title`` <small>**string**</small> **[A]**
    - ``image`` <small>**string**</small> **[B]**
    - ``url`` <small>**url**</small>: The url reached when clicking the title
    - ``target`` <small>**string**</small> **[C]**: The target website as shown by google
    - ``price``<small>**string**</small> **[D]**: The price as show by google

**Example**

```php
    use Serps\SearchEngine\Google\AdwordsResultType;
    
    $results = $response->getAdwordsResults();
    
    foreach($results as $result){
        if($result->is(AdwordsResultType::SHOPPING_GROUP)){
            foreach($result->getDataValue('products') as $item){
                $title = $item->getDataValue('title');
            }
        }
    }
```


## Related searches

Not implemented yet.