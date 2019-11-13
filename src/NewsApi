<?php
namespace SapiStudio\NewsApi;
use GuzzleHttp\Client as HttpClient;

class NewsApi
{
    private $apiKey;
    protected $defaultPageSize = 100;
    protected $url_core = 'https://newsapi.org/v2/';
    protected $country = 'us';
    protected $category = 'general';
    protected $type = 'top-headlines';
    protected $availbleCategories = ["business", "entertainment", "general","health", "science", "sports", "technology"];
    protected static $countries = [
		'ae', 'ar', 'at', 'au', 'be', 'bg', 'br', 'ca', 'ch', 'cn', 'co', 'cu', 'cz', 'de', 'eg', 'fr', 'gb', 'gr',
		'hk', 'hu','id','ie','il','in','it','jp','kr','lt','lv','ma','mx','my','ng','nl','no','nz','ph','pl', 'pt',
		'ro','rs','ru','sa','se','sg','si','sk','th','tr','tw','ua','us','ve','za'];
    protected $payloads = [];

    /** NewsApi::__construct()*/
    public function __construct($api_key)
    {
        $this->apiKey = $api_key;
        $this->request_header = ['Accept' => 'application/json', 'Content-type' => 'application/json', 'Authorization' => $this->apiKey];
        $this->client = new HttpClient(['timeout' => 30]);
        $this->resetPayload();
    }

    /** NewsApi::resetPayload()*/
    protected function resetPayload()
    {
        $this->payloads = [];
        $this->addPayload(['country' => $this->country, 'category' => $this->category]);
        $this->setPageSize($this->defaultPageSize);
    }

    /** NewsApi::addPayload()*/
    protected function addPayload($payload = [])
    {
        $this->payloads = array_merge($this->payloads, $payload);
        return $this;
    }

    /** NewsApi::getPayload() */
    protected function getPayload()
    {
        return $this->payloads;
    }

    /** NewsApi::removePayload()*/
    protected function removePayload($payload = null)
    {
        if ($payload) {
            if (is_array($payload)) {
                foreach ($payload as $index => $loadKey)
                    unset($this->payloads[$loadKey]);
            } else
                unset($this->payloads[$payload]);
        }
        return $this;
    }

    /** NewsApi::payloadExists()*/
    protected function payloadExists($payload = null)
    {
        return in_array($payload, $this->payloads);
    }

    /** NewsApi::buildUrl()*/
    public function buildUrl()
    {
        return $this->url_core . $this->type;
    }

    /** NewsApi::setSources()*/
    public function setSources($sources = null)
    {
        (!is_null($sources)) ? $this->addPayload(['sources' => $sources]) : $this->
            removePayload('sources');
        return $this;
    }

    /** NewsApi::setDomains()*/
    public function setDomains($domains = null)
    {
        (!is_null($domains)) ? $this->addPayload(['domains' => $domains]) : $this->
            removePayload('domains');
        return $this;
    }

    /** NewsApi::excludeDomains()*/
    public function excludeDomains($exclude_domains = null)
    {
        (!is_null($exclude_domains)) ? $this->addPayload(['excludeDomains' => $exclude_domains]) :
            $this->removePayload('excludeDomains');
        return $this;
    }

    /** NewsApi::setFrom()*/
    public function setFrom($from = null)
    {
        if (!is_null($from)) {
            if (strlen($from) < 10) {
                throw new \Exception('from argument must be YYYY-MM-DD format');
            } else {
                $this->addPayload(['from' => $from]);
            }
        } else
            $this->removePayload('from');
        return $this;
    }

    /** NewsApi::setTo()*/
    public function setTo($to = null)
    {
        if (!is_null($to)) {
            if (strlen($to) < 10) {
                throw new \Exception('to argument must be YYYY-MM-DD format');
            } else {
                $this->addPayload(['to' => $to]);
            }
        } else
            $this->removePayload('to');
        return $this;
    }

    /** NewsApi::setPage()*/
    public function setPage($page = null)
    {
        (!is_null($page)) ? $this->addPayload(['page' => $page]) : $this->removePayload('page');
        return $this;
    }

    /** NewsApi::setPageSize()*/
    public function setPageSize($pagesize = 1)
    {
        if (!is_null($pagesize)) {
            if ($pagesize >= 1 && $pagesize <= 100) {
                $this->addPayload(['pageSize' => $pagesize]);
            } else {
                throw new \Exception("Invalid Page_size Value Provided");
            }
        } else
            $this->removePayload('pageSize');
        return $this;
    }

    /** NewsApi::getCountry()*/
    public function getCountry()
    {
        return $this->country;
    }

    /** NewsApi::setCountry() */
    public function setCountry($country)
    {
        $this->addPayload(['country' => $country]);
        $this->country = $country;
    }

    /** NewsApi::getCategory()*/
    public function getCategory()
    {
        return $this->category;
    }

    /** NewsApi::setCategory()*/
    public function setCategory($category)
    {
        $this->addPayload(['category' => $category]);
        $this->category = $category;
    }

    /** NewsApi::getType()*/
    public function getType()
    {
        return $this->type;
    }

    /** NewsApi::setType()*/
    public function setType($type)
    {
        $this->type = $type;
    }

    /** NewsApi::getAvailbleCategories()*/
    public function getAvailbleCategories()
    {
        return $this->availbleCategories;
    }

    /** NewsApi::getTopHeadLines()*/
    public function getTopHeadLines()
    {
        $this->setType('top-headlines');
        if ($this->payloadExists('sources') && ($this->payloadExists('country') || $this->payloadExists('category'))) {
            throw new \Exception("You Cannot Use Sources with Country or Category at the same time.");
        }
        return $this->sendRequest();
    }

    /** NewsApi::getEverything()*/
    public function getEverything($queryText = null)
    {
        if (is_null($queryText))
            throw new \Exception("In order to use everything type you must provide a search query text");
        $this->addPayload(['q' => $queryText]);
        $this->setType('everything');
        $this->removePayload(['country', 'category']);
        return $this->sendRequest();
    }

    /** NewsApi::getSources() */
    public function getSources()
    {
        $this->setType('sources');
        return $this->sendRequest();
    }

    /** NewsApi::sendRequest() */
    private function sendRequest()
    {
        try {
            $response = $this->client->request('GET', $this->buildUrl(), ['headers' => $this->request_header, 'query' => $this->getPayload()]);
            $this->resetPayload();
            if ($response->getStatusCode() == 200) {
                return json_decode($response->getBody()->__toString());
            } else {
                $response_body = json_encode($response->getBody());
                throw new \Exception($response_body->message);
            }
        }
        catch (exception $e) {
            throw new \Exception($e->getMessage());
        }
    }
}
