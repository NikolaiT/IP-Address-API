# IP Address API

The IP Address API gives you meta information for each IP address such as company and ASN details. Furthermore, it allows you to find out security information for each IP address, for example whether an IP address belongs to a hosting provider, is a proxy or belongs to an abuser.

This API tries uses the following sources:

+ huge whois records from regional Internet address registries such as RIPE NCC, APNIC, ARIN and so on
+ public BGP information
+ public blocklists such as [firehol/blocklist-ipset](https://github.com/firehol/blocklist-ipsets)

Learn more about how the API works: Visit the [API page](https://incolumitas.com/pages/Datacenter-IP-API/) for more information!

## Core API Features

+ **Ready for Production:** This API can be used in production and is stable
+ **Many datacenters supported:** [Thousands of different hosting providers and counting](https://incolumitas.com/pages/Hosting-Providers-List/) - From *Huawei Cloud Service* to *ServerMania Inc.*
+ **Regularely updated:** The API database is automatically updated every day. IP data is gathered from many sources: 
  + Self published IP ranges from large cloud providers
  + Public whois data
  + And other sources
+ **Pretty fast:** The API is very performant. On average, an IP lookup takes `0.042ms` (server side time consumed)
+ **Bulk IP Lookups:** You can lookup up to 100 IP addresses per API call

## Datacenter IP Address API Endpoints

### GET Endpoint - [https://api.incolumitas.com/datacenter?ip=142.250.186.110](https://api.incolumitas.com/datacenter?ip=142.250.186.110)

This GET endpoint allows to lookup a single IPv4 or IPv6 IP address by specifying the query parameter `ip`. Example: `ip=142.250.186.110`.

The JSON response will always include the keys (Even if the looked-up IP address was not a match):

+ `ip` - `string` - the IP address that was looked up
+ `is_datacenter` - `boolean` - whether the IP address belongs to a datacenter
+ `elapsed_ms` - `float` - how much internal processing time was spent in ms (Example: `1.71`)

If there is a match, the API response will always include the following keys:

+ `datacenter` - `string` - to which datacenter the IP address belongs. For a full list of datacenters, [check the api.incolumitas.com/info endpoint](https://api.incolumitas.com/info) (Example: `"Amazon AWS"`)
+ `cidr` - `string` - the CIDR range that this IP address belongs to (Example: `"13.34.52.96/27"`)

For example, if you set the parameter `ip=13.34.52.117`, the API request looks like this: [https://api.incolumitas.com/datacenter?ip=13.34.52.117](https://api.incolumitas.com/datacenter?ip=13.34.52.117). The API response for this request looks like this:

```json
{
  "ip": "13.34.52.117",
  "is_datacenter": true,
  "is_tor": false,
  "is_proxy": false,
  "is_abuser": false,
  "cidr": "13.34.52.96/27",
  "region": "eu-west-2",
  "datacenter": "Amazon AWS",
  "service": "AMAZON",
  "network_border_group": "eu-west-2",
  "rir": "arin",
  "company": {
    "name": "Amazon Technologies Inc.",
    "domain": "amazon.com",
    "network": "13.24.0.0 - 13.59.255.255"
  },
  "asn": null,
  "location": {
    "country": "us"
  },
  "elapsed_ms": 0.23
}
```

You can of course also lookup IPv6 addresses:

[https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000](https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000). 

The response for this API request will be:

```json
{
  "ip": "2600:1F18:7FFF:F800:0000:ffff:0000:0000",
  "is_datacenter": true,
  "is_tor": false,
  "is_proxy": false,
  "is_abuser": false,
  "cidr": "2600:1f18:7fff:f800::/56",
  "region": "us-east-1",
  "datacenter": "Amazon AWS",
  "service": "ROUTE53_HEALTHCHECKS",
  "network_border_group": "us-east-1",
  "rir": "ARIN",
  "asn": {
    "asn": 14618,
    "route": "2600:1f18:6000::/35",
    "descr": "AMAZON-AES, US",
    "country": "us",
    "active": true,
    "website": "https://amazon.com",
    "org": "Amazon.com, Inc.",
    "abuse": "abuse@amazonaws.com"
  },
  "location": {
    "country": "not found"
  },
  "elapsed_ms": 5.26
}
```

### POST Endpoint - [https://api.incolumitas.com/datacenter?](https://api.incolumitas.com/datacenter?)

You can also make a bulk API lookup with up to 100 IP addresses (Either IPv4 or IPv6) in one single request. **Please note:** The API will return only matches for those IP addresses that belong to a datacenter. This approach saves networking bandwith.

For example, in order to lookup the IP addresses

+ `162.158.0.0`
+ `2406:dafe:e0ff:ffff:ffff:ffff:dead:beef`
+ `162.88.0.0`
+ `20.41.193.225`

you can use the following POST API request with `curl`:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"ips": ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"]}' \
  https://api.incolumitas.com/datacenter
```

which will return this response (Note that the IP `162.88.0.0` did not return a match):

```json
{
  "162.158.0.0": {
    "ip": "162.158.0.0",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "datacenter": "Cloudflare",
    "cidr": "162.158.0.0 - 162.159.255.255",
    "rir": "arin",
    "company": {
      "name": "Cloudflare, Inc.",
      "domain": "cloudflare.com",
      "network": "162.158.0.0 - 162.159.255.255"
    },
    "asn": {
      "asn": 13335,
      "route": "162.158.0.0/22",
      "descr": "CLOUDFLARENET, US",
      "country": "us",
      "active": true,
      "website": "https://cloudflare.com",
      "org": "Cloudflare, Inc.",
      "abuse": "abuse@cloudflare.com"
    },
    "location": {
      "country": "us"
    },
    "elapsed_ms": 0.33
  },
  "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef": {
    "ip": "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "cidr": "2406:dafe:e000::/40",
    "region": "ap-east-1",
    "datacenter": "Amazon AWS",
    "service": "AMAZON",
    "network_border_group": "ap-east-1",
    "rir": "APNIC",
    "asn": null,
    "location": {
      "country": "not found"
    },
    "elapsed_ms": 0.37
  },
  "162.88.0.0": {
    "ip": "162.88.0.0",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "datacenter": "Oracle Cloud",
    "cidr": "162.88.0.0 - 162.88.255.255",
    "rir": "arin",
    "company": {
      "name": "Oracle Corporation",
      "domain": "oracle.com",
      "network": "162.88.0.0 - 162.88.255.255"
    },
    "asn": null,
    "location": {
      "country": "us"
    },
    "elapsed_ms": 0.28
  },
  "20.41.193.225": {
    "ip": "20.41.193.225",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "cidr": "20.41.193.224/27",
    "name": "AzurePortal.SouthIndia",
    "datacenter": "Microsoft Azure",
    "region": "southindia",
    "regionId": 22,
    "platform": "Azure",
    "systemService": "AzurePortal",
    "rir": "arin",
    "company": {
      "name": "Microsoft Corporation",
      "domain": "microsoft.com",
      "network": "20.33.0.0 - 20.128.255.255"
    },
    "asn": {
      "asn": 8075,
      "route": "20.40.0.0/13",
      "descr": "MICROSOFT-CORP-MSN-AS-BLOCK, US",
      "country": "us",
      "active": true,
      "website": "https://microsoft.com",
      "org": "Microsoft Corporation",
      "abuse": "abuse@microsoft.com"
    },
    "location": {
      "country": "us"
    },
    "elapsed_ms": 0.09
  },
  "total_elapsed_ms": 1.17
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

