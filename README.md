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

## API Endpoints

Example:

[https://api.incolumitas.com/datacenter?ip=13.34.52.117](https://api.incolumitas.com/datacenter?ip=13.34.52.117)

## Code Examples


### Curl

```bash
curl 'https://api.incolumitas.com/datacenter?ip=2600:1F18:7FFF:F800:0000:ffff:0000:0000'

{
  "ip": "2600:1F18:7FFF:F800:0000:ffff:0000:0000",
  "is_datacenter": true,
  "ip_data_source": "self_published_ip_ranges",
  "cidr": "2600:1f18:7fff:f800::/56",
  "region": "us-east-1",
  "datacenter": "Amazon AWS",
  "service": "ROUTE53_HEALTHCHECKS",
  "network_border_group": "us-east-1",
  "elapsed_ms": 0.13
}
```


### JavaScript

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

