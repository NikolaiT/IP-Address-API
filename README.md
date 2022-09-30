# IP Address API

This free IP Address API returns useful information for each queried IP address. The response information includes the organization of the IP address, to which ASN the IP address belongs and the geolocation for the IP address.

Furthermore, the API response allows you to derive security information for each IP address, for example whether an IP address belongs to a hosting provider (`is_datacenter`), is a TOR exit node (`is_tor`), if an IP address is a proxy (`is_proxy`) or belongs to an abuser (`is_abuser`).

But why would you use *this* API? Aren't there many other IP Address API's?

This API strongly emphasises datacenter / hosting detection. A complicated hosting detection algorithm is used to achieve a high detection rate. Currently, more than [1566 global hosting providers]({filename}/pages/datacenters.md) are tracked.

The IP adddress API makes use of the following sources:

1. Public whois records from regional Internet address registries such as RIPE NCC, APNIC, ARIN and so on
2. Public BGP information (In order to find ASN information and their associated routes/prefixes)
3. Public blocklists such as [firehol/blocklist-ipset](https://github.com/firehol/blocklist-ipsets)
4. The API uses several proprietary datacenter/hosting detection algorithms
5. The API uses IP threat data from public honeypots
6. IP geolocation information (Geolocation is accurate to the country level)

Learn more about how the API works: Visit the [API page](https://incolumitas.com/pages/IP-API/) for more information!

## Core API Features

+ **Ready for Production**: This API can be used in production and is stable
+ **Many datacenters supported:** [Thousands of different hosting providers and counting]({filename}/pages/datacenters.md) - From Huawei Cloud Service to ServerMania Inc. Find out whether the IP address is hosted by looking at the `is_datacenter` property!
+ **Always updated**: The API database is automatically several times per week. IP data is gathered from many sources:
  + Self published IP ranges from large cloud providers
  + Public whois data from regional internet registries (RIR's) such as RIPE NCC or APNIC
  + Many other data sources such as public [BGP data](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)
  + Open Source IP blocklists
+ **AS (Autonomous System) support**: The API provides autonomous system information for each looked-up IP address
+ **Company Support**: The API provides organisational information for each network of each looked up IP address
+ **Bulk IP Lookups**: You can lookup up to 100 IP addresses per API call

## API Response Format

Let's explain the API output format by walking through an example. Most of the returned information is self-explanatory.

This is how a typical API response looks like:

```json
{
  "ip": "107.174.138.172",
  "rir": "ARIN",
  "is_bogon": false,
  "is_datacenter": true,
  "is_tor": true,
  "is_proxy": false,
  "is_abuser": true,
  "datacenter": {
    "datacenter": "ColoCrossing",
    "cidr": "107.174.138.0/24"
  },
  "company": {
    "name": "ColoCrossing",
    "domain": "colocrossing.com",
    "network": "107.172.0.0 - 107.175.255.255"
  },
  "asn": {
    "asn": 36352,
    "route": "107.174.138.0/24",
    "descr": "AS-COLOCROSSING, US",
    "country": "us",
    "active": true,
    "website": "https://colocrossing.com",
    "org": "ColoCrossing",
    "abuse": "abuse@colocrossing.com",
    "type": "hosting"
  },
  "location": {
    "country": "US",
    "continent": "NA",
    "state": "New York",
    "city": "Buffalo",
    "latitude": "42.8864",
    "longitude": "-78.8784",
    "ipdeny_country": "us",
    "maxmind_geolite2_country": "us",
    "ip2location_country": "us",
    "ipip_country": "us"
  },
  "elapsed_ms": 0.32
}
```

Let's dive into the different parts of the API response. Take a deep breath and fasten your seatbelt :)

### Response Format: Top Level API Information

Let's first look at the top level API information:

```json
{
  "ip": "107.174.138.172",
  "rir": "ARIN",
  "is_bogon": false,
  "is_datacenter": true,
  "is_tor": true,
  "is_proxy": false,
  "is_abuser": true,
  "elapsed_ms": 0.32
}
```

The explanation for those fields is as follows:

+ `ip` - `string` - the IP address that was looked up, here it was `107.174.138.172`
+ `rir` - `string` - to which [Regional Internet Registry](https://en.wikipedia.org/wiki/Regional_Internet_registry) the looked up IP address belongs. Here it belongs to `ARIN`, which is the RIR responsible for North America
+ `is_bogon` - `boolean` - Whether the IP address is bogon. For example, the loopback IP `127.0.0.1` is a special/bogon IP address. The IP address `107.174.138.172` is of course not bogon, hence it is set to `false` here.
+ `is_datacenter` - `boolean` - whether the IP address belongs to a datacenter. Here, we have the value `true`, since `107.174.138.172` belongs to the datacenter provider ColoCrossing.
+ `is_tor` - `boolean` - is true if the IP address belongs to the TOR network. This is the case here!
+ `is_proxy` - `boolean` - whether the IP address is a proxy. This is not the case here.
+ `is_abuser` - `boolean` - is true if the IP address committed abusive actions, which unfortunately was the case with `107.174.138.172`
+ `elapsed_ms` - `float` - how much internal processing time was spent in ms. This lookup only took `0.32ms`, which is quite fast :)

### Response Format: The `datacenter` object

```json
  "datacenter": {
    "datacenter": "ColoCrossing",
    "cidr": "107.174.138.0/24"
  },
```

If the IP address belongs to a datacenter/hosting provider, the API response will include a `datacenter` object with the following attributes:

+ `datacenter` - `string` - to which datacenter the IP address belongs. For a full list of datacenters, check the [api.incolumitas.com/info endpoint](https://api.incolumitas.com/info). In this case, the datacenter's name is `ColoCrossing`.
+ `cidr` - `string` - to which datacenter network the IP address belongs (In the above scenario: `107.174.138.0/24`)

Most IP's don't belong to a hosting provider. In those cases, the `datacenter` object will not be present.

### Response Format: The `company` object

```json
  "company": {
    "name": "ColoCrossing",
    "domain": "colocrossing.com",
    "network": "107.172.0.0 - 107.175.255.255"
  },
```

Most IP addresses can be associated with an organization or company. This API uses public whois database information to infer which organization is the owner of a certain IP address. Most API lookups will have an `company` object with the following attributes:

+ `name` - `string` - The name of the company
+ `domain` - `string` - The domain name of the company
+ `network` - `string` - The network for which the company has ownership

### Response Format: The `asn` object

```json
  "asn": {
    "asn": 36352,
    "route": "107.174.138.0/24",
    "descr": "AS-COLOCROSSING, US",
    "country": "us",
    "active": true,
    "website": "https://colocrossing.com",
    "org": "ColoCrossing",
    "abuse": "abuse@colocrossing.com",
    "type": "hosting"
  },
```

Most IP addresses can be associated with an Autonomeous System (AS). The `asn` object provides the following attributes:

+ `asn` - `int` - The AS number
+ `route` - `string` - The IP route as CIDR in this AS
+ `descr` - `string` - An informational description of the AS
+ `country` - `string` - The country where the AS is situated in
+ `active` - `string` - Whether the AS is active (active = at least one route administred by the AS)
+ `website` - `string` - The website of the organization to which this AS belongs
+ `org` - `string` - The organization responisible for this AS
+ `type` - `string` - The type for this ASN, this is either `hosting`, `education`, `goverment`, `banking`, `business` or `isp`

For inactive Autonomeous Systems, most of the above information is not available.

### Response Format: The `location` object

```json
  "location": {
    "country": "US",
    "continent": "NA",
    "state": "New York",
    "city": "Buffalo",
    "latitude": "42.8864",
    "longitude": "-78.8784",
    "ipdeny_country": "us",
    "maxmind_geolite2_country": "us",
    "ip2location_country": "us",
    "ipip_country": "us"
  },
```

The API provides geolocation information for the looked up IP address. The `location` object includes the following attributes:

+ `country` - `string` - The ISO 3166-1 alpha-2 country code to which the IP address belongs. This is the country specific geolocation of the IP address.
+ `continent` - `string` - The continent to which the IP address can be associated
+ `state` - `string` - The state for the IP address
+ `city` - `string` - The city to which the IP address belongs
+ `latitude` - `string` - The latitude for the IP address
+ `longitude` - `string` -The longitude for the IP address
+ `ipdeny_country` - `string` - The country for this IP address as provided by [ipdeny.com](https://www.ipdeny.com/ipblocks/)
+ `maxmind_geolite2_country` - `string` - The country for this IP address as provided by [maxmind.com geolite2](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en)
+ `ip2location_country` - `string` - The country for this IP address as provided by [ip2location.com](https://www.ip2location.com/database)
+ `ipip_country` - `string` - The country for this IP address as provided by [ipip.net](https://en.ipip.net/product/ip.html)

Please understand that geolocation information can never be 100% accurate. However, country level accuracy is quite good.
You can cross-check with the different `country` attributes from the different geolocation data providers. The more matching country attributes, the better the accuracy.

## API Endpoints

The IP API currently has two endpoints.

### GET - `/` - Lookup a single IP address or ASN

This GET endpoint allows to lookup a single IPv4 or IPv6 IP address by specifying the query parameter `ip`. Example: `ip=142.250.186.110`. You can also lookup **ASN** numbers by specifying the query `ip=AS209103`.

| <!-- -->         | <!-- -->                                           |
|------------------|----------------------------------------------------|
| **Endpoint**       | /                                  |
| **Method**       | `GET`                                  |
| **Parameter**       | `q` - The IP address or ASN to lookup                                 |
| **Example** | [https://api.incolumitas.com/?q=3.5.140.2](https://api.incolumitas.com/?q=3.5.140.2)
| **ASN Example** | [https://api.incolumitas.com/?q=AS42831](https://api.incolumitas.com/?q=AS42831)

### POST - `/` - Lookup up to 100 IP addresses in one API call

You can also make a bulk API lookup with up to 100 IP addresses (Either IPv4 or IPv6) in one single request.

| <!-- -->         | <!-- -->                                           |
|------------------|----------------------------------------------------|
| **Endpoint**       | /                                  |
| **Method**       | `POST`                                  |
| **Content-Type**       | `Content-Type: application/json`                                  |
| **Parameter**       | `ips` - An array of IPv4 and IPv6 addresses to lookup                                 |

For example, in order to lookup the IP addresses

+ `162.158.0.0`
+ `2406:dafe:e0ff:ffff:ffff:ffff:dead:beef`
+ `162.88.0.0`
+ `20.41.193.225`

you can use the following POST API request with curl:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"ips": ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"]}' \
  https://api.incolumitas.com/
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
