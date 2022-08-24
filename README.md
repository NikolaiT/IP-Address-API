# IP Address API

The IP Address API gives you meta information for each IP address such as company and ASN details. Furthermore, it allows you to find out security information for each IP address, for example whether an IP address belongs to a hosting provider (`is_datacenter`), is a TOR exit node (`is_tor`), if a IP address is a proxy (`is_proxy`) or belongs to an abuser (`is_abuser`).

This API tries uses the following sources/algorithms:

+ huge public whois records from regional Internet address registries such as RIPE NCC, APNIC, ARIN and so on
+ public BGP information (in order to find active ASN's and routes)
+ public blocklists such as [firehol/blocklist-ipset](https://github.com/firehol/blocklist-ipsets)
+ my own datacenter/hosting detection algorithm
+ threat data from public honeypots
+ public IP geolocation information (Geolocation is accurate to the country level)

Learn more about how the API works: Visit the [API page](https://incolumitas.com/pages/Datacenter-IP-API/) for more information!

## Core API Features

+ **Ready for Production:** This API can be used in production and is stable
+ **Many datacenters supported:** [Thousands of different hosting providers and counting](https://incolumitas.com/pages/Hosting-Providers-List/) - From *Huawei Cloud Service* to *ServerMania Inc.*
+ **Regularely updated:** The API database is automatically updated every day.
+ **Pretty fast:** The API is very performant. On average, an IP lookup takes `0.042ms` (server side time consumed)
+ **Bulk IP Lookups:** You can lookup up to 100 IP addresses per API call

## API Endpoints

### GET Endpoint - [https://api.incolumitas.com/?ip=142.250.186.110](https://api.incolumitas.com/?ip=142.250.186.110)

This GET endpoint allows to lookup a single IPv4 or IPv6 IP address by specifying the query parameter `ip`.

For example, if you set the parameter `ip=13.34.52.117`, the API request looks like this: [https://api.incolumitas.com/?ip=13.34.52.117](https://api.incolumitas.com/?ip=13.34.52.117). The API response for this request looks like this:

```json
{
  "ip": "13.34.52.117",
  "rir": "arin",
  "is_datacenter": true,
  "is_tor": false,
  "is_proxy": false,
  "is_abuser": false,
  "cidr": "13.34.52.96/27",
  "region": "eu-west-2",
  "datacenter": "Amazon AWS",
  "service": "AMAZON",
  "network_border_group": "eu-west-2",
  "company": {
    "name": "Amazon Technologies Inc.",
    "domain": "amazon.com",
    "network": "13.24.0.0 - 13.59.255.255"
  },
  "asn": null,
  "location": {
    "country": "us"
  },
  "elapsed_ms": 1.59
}
```

You can of course also lookup IPv6 addresses:

[https://api.incolumitas.com/?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000](https://api.incolumitas.com/?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000). 

The response for this API request will be:

```json
{
  "ip": "2600:1F18:7FFF:F800:0000:ffff:0000:0000",
  "rir": "ARIN",
  "is_datacenter": true,
  "is_tor": false,
  "is_proxy": false,
  "is_abuser": false,
  "cidr": "2600:1f18:7fff:f800::/56",
  "region": "us-east-1",
  "datacenter": "Amazon AWS",
  "service": "ROUTE53_HEALTHCHECKS",
  "network_border_group": "us-east-1",
  "company": {
    "name": "Amazon.com, Inc.",
    "domain": "amazon.com",
    "network": "2600:1F00::/24"
  },
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
  "elapsed_ms": 0.83
}
```

#### API Response 

The JSON API response **will always include the keys** (Even if the looked up IP address was not a match):

+ `ip` - `string` - the IP address that was looked up
+ `rir` - `string` - to which [Regional Internet Registry](https://en.wikipedia.org/wiki/Regional_Internet_registry) the looked up IP address belongs
+ `is_datacenter` - `boolean` - whether the IP address belongs to a datacenter
+ `is_tor` - `boolean` - is true if the IP address belongs to the TOR network
+ `is_proxy` - `boolean` - whether the IP address is a proxy
+ `is_abuser` - `boolean` - is true if the IP address committed abuse actions
+ `company` - `object` - Company information for the looked up IP address. The `company` object includes the following attributes:
    + `name` - `string` - The name of the company
    + `domain` - `string` - The domain of the company
    + `network` - `string` - The network for which the company has ownership
+ `asn` - `object` - ASN information for the looked up IP address. The `asn` object includes the following information:
    + `asn` - `int` - The AS number 
    + `cidr` - `string` - The IP range as CIDR within the AS
    + `descr` - `string` - An informational description of the AS
    + `country` - `string` - The country where the AS is situated in
+ `location` - `object` - Geolocation information for the looked up IP address. The `location` object includes the following attributes:
    + `country` - `string` - The ISO 3166-1 alpha-2 country code to which the IP address belongs. This is the country specific geolocation of the IP address.
+ `elapsed_ms` - `float` - how much internal processing time was spent in ms (Example: `1.71`)

If there is a datacenter match, the API response will always include the following keys:

+ `datacenter` - `string` - to which datacenter the IP address belongs. For a full list of datacenters, check the [api.incolumitas.com/info endpoint](https://api.incolumitas.com/info) (Example: `"Amazon AWS"`)
+ `cidr` - `string` - the CIDR range that this IP address belongs to (Example: `"13.34.52.96/27"`)

### POST Endpoint - [https://api.incolumitas.com/](https://api.incolumitas.com/)

You can also make a bulk API lookup with up to 100 IP addresses (Either IPv4 or IPv6) in one single request.

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

which will return this response:

```json
{
  "162.158.0.0": {
    "ip": "162.158.0.0",
    "rir": "arin",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "datacenter": "Cloudflare",
    "cidr": "162.158.0.0 - 162.159.255.255",
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
    "elapsed_ms": 0.78
  },
  "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef": {
    "ip": "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef",
    "rir": "APNIC",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "cidr": "2406:dafe:e000::/40",
    "region": "ap-east-1",
    "datacenter": "Amazon AWS",
    "service": "AMAZON",
    "network_border_group": "ap-east-1",
    "company": {
      "name": "Amazon.com, Inc.",
      "network": "2406:da00::/24"
    },
    "asn": null,
    "location": {
      "country": "not found"
    },
    "elapsed_ms": 0.9
  },
  "162.88.0.0": {
    "ip": "162.88.0.0",
    "rir": "arin",
    "is_datacenter": true,
    "is_tor": false,
    "is_proxy": false,
    "is_abuser": false,
    "datacenter": "Oracle Cloud",
    "cidr": "162.88.0.0 - 162.88.255.255",
    "company": {
      "name": "Oracle Corporation",
      "domain": "oracle.com",
      "network": "162.88.0.0 - 162.88.255.255"
    },
    "asn": null,
    "location": {
      "country": "us"
    },
    "elapsed_ms": 0.82
  },
  "20.41.193.225": {
    "ip": "20.41.193.225",
    "rir": "arin",
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
    "elapsed_ms": 0.17
  },
  "total_elapsed_ms": 2.84
}
```

## Code Examples

### Curl

Simple IPv6 lookup using `curl`:

```bash
curl 'https://api.incolumitas.com/?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000'
```

Bulk IP lookup using `curl` in POST mode with Content-Type JSON:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"ips": ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"]}' \
  https://api.incolumitas.com/
```

### JavaScript

Simple lookup in JavaScript:

```JavaScript
fetch('https://api.incolumitas.com/?ip=23.236.48.55')
  .then(res => res.json())
  .then(res => console.log(res));
```

You can also do a bulk lookup with JavaScript with a POST request:

```JavaScript
const ips = ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"];

fetch('https://api.incolumitas.com/', {
  method: 'POST',
  headers: {
    'Accept': 'application/json, text/plain, */*',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({ips: ips})
}).then(res => res.json())
  .then(res => console.log(res));
```

