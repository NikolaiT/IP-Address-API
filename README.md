# Datacenter-IP-API
Datacenter / Hosting IP Address API - Find out if an IP address belongs to a hosting provider such as AWS, Azure or Digitalocean.

## Core API Features

+ **Many datacenters supported:** [159 different hosting providers and counting](https://api.incolumitas.com/info) - From *Huawei Cloud Service* to *ServerMania Inc.*
+ **Always updated:** The API database is automatically updated every week. IP data is gathered from many sources: 
  + Self published IP ranges from large cloud providers
  + public whois data
  + many other sources
+ **Pretty fast:** The API is very performant. On average, an IP lookup takes `0.042ms` (server side time consumed)
+ **Bulk IP Lookups:** You can lookup up to 100 IP addresses per API call

## Datacenter IP Address API Endpoints

### Endpoint - GET - [api.incolumitas.com/datacenter?](https://api.incolumitas.com/datacenter?)

This GET endpoint allows to lookup a single IPv4 or IPv6 IP address. Example: [https://api.incolumitas.com/datacenter?ip=13.34.52.117](https://api.incolumitas.com/datacenter?ip=13.34.52.117)

You can of course also lookup IPv6 addresses: [https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000](https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000)

## Code Examples

### Curl

Simple IPv6 lookup:

```bash
curl 'https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000'
```

Bulk IP lookup:

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

