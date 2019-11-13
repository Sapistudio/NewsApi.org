# NewsApi.org
retrieve news from newsapi.org

## Init
```php
use SapiStudio\NewsApi\NewsApi;

$api = new NewsApi(api_key);
```
## get headlines
```php
$api->getTopHeadLines();
```
## get everything
```php
$api->getEverything($searchText);
```
## get sources
```php
$api->getSources($searchText);
```
