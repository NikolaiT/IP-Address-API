# Datacenter-IP-API
Datacenter / Hosting IP Address API - Find out if an IP address belongs to a hosting provider such as AWS, Azure or Digitalocean.

+ Please read the blog article to understand [how the Hosting API works](https://incolumitas.com/2022/03/09/find-out-if-an-IP-address-belongs-to-a-hosting-provider/#isso-thread)
+ You can visit the [API page](https://incolumitas.com/pages/Datacenter-IP-API/) for more information

## Core API Features

+ **Ready for Production:** This API can be used in production and is stable.
+ **Many datacenters supported:** [159 different hosting providers and counting](https://api.incolumitas.com/info) - From *Huawei Cloud Service* to *ServerMania Inc.*
+ **Regularely updated:** The API database is automatically updated every week. IP data is gathered from many sources: 
  + Self published IP ranges from large cloud providers
  + Public whois data
  + And other sources
+ **Pretty fast:** The API is very performant. On average, an IP lookup takes `0.042ms` (server side time consumed)
+ **Bulk IP Lookups:** You can lookup up to 100 IP addresses per API call

## Datacenter IP Address API Endpoints

### GET Endpoint - [api.incolumitas.com/datacenter?](https://api.incolumitas.com/datacenter?)

This GET endpoint allows to lookup a single IPv4 or IPv6 IP address by specifying the query parameter `ip`. 

The response will always include the keys:

+ `is_datacenter` - boolean - whether the IP address belongs to a datacenter
+ `elapsed_ms` - float - how much time in ms the lookup caused in internal processing (Example: `1.71`)

If there is a match, the API response will always include the following keys:

+ `ip_data_source` - string - the data source that claims that this IP is a datacenter (Example: `"whois_database"`)
+ `datacenter` - string - to which datacenter the IP address belongs. For a full list of datacenters, [check the api.incolumitas.com/info endpoint](https://api.incolumitas.com/info) (Example: `"Amazon AWS"`)
+ `cidr` - string - the CIDR range that this IP address belongs to (Example: `"13.34.52.96/27"`)

In some cases, the API response returns the following keys:

+ `range` - int - the IP address range that this IP address belongs to

For example, if you set the parameter `ip=13.34.52.117`, the API request looks like this: [https://api.incolumitas.com/datacenter?ip=13.34.52.117](https://api.incolumitas.com/datacenter?ip=13.34.52.117). The API response for this request looks like this:

```json
{
  "ip": "13.34.52.117",
  "is_datacenter": true,
  "ip_data_source": "self_published_ip_ranges",
  "cidr": "13.34.52.96/27",
  "region": "eu-west-2",
  "datacenter": "Amazon AWS",
  "service": "AMAZON",
  "network_border_group": "eu-west-2",
  "elapsed_ms": 1.71
}
```

You can of course also lookup IPv6 addresses: [https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000](https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000). The response for this API request:

```json
{
  "ip": "2600:1F18:7FFF:F800:0000:ffff:0000:0000",
  "is_datacenter": true,
  "ip_data_source": "self_published_ip_ranges",
  "cidr": "2600:1f18:7fff:f800::/56",
  "region": "us-east-1",
  "datacenter": "Amazon AWS",
  "service": "ROUTE53_HEALTHCHECKS",
  "network_border_group": "us-east-1",
  "elapsed_ms": 0.24
}
```

### POST Endpoint - [api.incolumitas.com/datacenter?](https://api.incolumitas.com/datacenter?)

You can also make a bulk API lookup with up to 100 IP addresses (Either IPv4 or IPv6) in one single request. **Please note:** The API will return only matches for those IP addresses that belong to a datacenter. This approach saves networking bandwith.

For example, in order to lookup the IP addresses

+ `162.158.0.0`
+ `2406:dafe:e0ff:ffff:ffff:ffff:dead:beef`
+ `162.88.0.0`
+ `20.41.193.225`

you can use the following API request with curl:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"ips": ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"]}' \
  https://api.incolumitas.com/datacenter
```

which will return this response (Note that the IP `162.88.0.0` was not a match):

```json
{
  "162.158.0.0": {
    "ip": "162.158.0.0",
    "is_datacenter": true,
    "datacenter": "Cloudflare",
    "asn": "13335",
    "cidr": "162.158.0.0/22",
    "ip_data_source": "whois_database",
    "other_matches": [
      {
        "ip_data_source": "self_published_ip_ranges",
        "cidr": "162.158.0.0/15",
        "datacenter": "Cloudflare"
      }
    ],
    "elapsed_ms": 0.19
  },
  "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef": {
    "ip": "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef",
    "is_datacenter": true,
    "ip_data_source": "self_published_ip_ranges",
    "cidr": "2406:dafe:e000::/40",
    "region": "ap-east-1",
    "datacenter": "Amazon AWS",
    "service": "AMAZON",
    "network_border_group": "ap-east-1",
    "elapsed_ms": 0.1
  },
  "20.41.193.225": {
    "ip": "20.41.193.225",
    "is_datacenter": true,
    "ip_data_source": "self_published_ip_ranges",
    "cidr": "20.41.193.224/27",
    "name": "AzurePortal",
    "datacenter": "Microsoft Azure",
    "region": "",
    "regionId": 0,
    "platform": "Azure",
    "systemService": "AzurePortal",
    "other_matches": [
      {
        "datacenter": "Microsoft Azure",
        "asn": "8075",
        "cidr": "20.40.0.0/13",
        "ip_data_source": "whois_database"
      }
    ],
    "elapsed_ms": 11.84
  },
  "total_elapsed_ms": 12.73
}
```

## Code Examples

### Curl

Simple IPv6 lookup using `curl`:

```bash
curl 'https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000'
```

Bulk IP lookup using `curl` in POST mode with Content-Type JSON:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"ips": ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"]}' \
  https://api.incolumitas.com/datacenter
```

### JavaScript

Simple lookup in JavaScript:

```JavaScript
fetch('https://api.incolumitas.com/datacenter?ip=23.236.48.55')
  .then(res => res.json())
  .then(res => console.log(res));
```

You can also do a bulk lookup with JavaScript with a POST request:

```JavaScript
const ips = ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"];

fetch('https://api.incolumitas.com/datacenter', {
  method: 'POST',
  headers: {
    'Accept': 'application/json, text/plain, */*',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ips: ips})
}).then(res => res.json())
  .then(res => console.log(res));
```

