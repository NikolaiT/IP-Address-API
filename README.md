# IP Address API

This IP address API returns useful meta-information for IP addresses. For example, the API response includes the organization of the IP address, ASN information and geolocation intelligence and WHOIS data.

Furthermore, the API response allows to derive **security information** for each IP address, for example whether an IP address belongs to a hosting provider (`is_datacenter`), is a TOR exit node (`is_tor`), if an IP address is a proxy (`is_proxy`) or VPN (`is_vpn`) or belongs to an abuser (`is_abuser`).

This API strongly emphasizes **hosting detection**. A complicated hosting detection algorithm was developed to achieve a high detection rate. Whois records, public hosting IP ranges from hosting providers and a proprietary hosting discovery algorithm are used to decide whether an IP address belongs to a hosting provider or not.

## Quickstart

Lookup any IP address: [https://api.incolumitas.com/?q=3.5.140.2](https://api.incolumitas.com/?q=3.5.140.2)

Lookup your own IP address: [https://api.incolumitas.com/](https://api.incolumitas.com/)

Usage with JavaScript:

```JavaScript
fetch('https://api.incolumitas.com/?q=23.236.48.55')
  .then(res => res.json())
  .then(res => console.log(res));
```

Usage with `curl`:

```bash
curl 'https://api.incolumitas.com/?q=32.5.140.2'
```

## IP Address Databases Download

This repository contains three databases:

+ Geolocation Database - free - Contains millions of rows that associate geolocation intelligence with IPv4 and IPv6 networks
+ ASN Database - sample only - This database includes rich meta data for all active ASN's of the Internet (Around 85.000 active ASN's). You can get the [full database here](https://ipapi.is/).
+ Hosting IP Ranges Database - sample only - Contains IP addresses that belong to hosting providers or cloud services such as Amazon AWS or Microsoft Azure. Contains very small and niche hosting providers. You can get the [full database here](https://ipapi.is/).

## Geolocation Database

The database includes geolocation information for a large part of the IPv4 address space and a many IPv6 networks. The database is updated several times per week. The accuracy of the data is very good on the country level. It is not recommended to rely on geolocation intelligence to be accurate to the city level for critical applications.

+ [Download IPv4 Geolocation Database](databases/geolocationDatabaseIPv4.csv.zip)
+ [Download IPv6 Geolocation Database](databases/geolocationDatabaseIPv6.csv.zip)

The geolocation database is provided as large CSV file with the following header fields:

+ `ipVersion` - Either `ipv4` or `ipv6`. Determines the IP type of the network. Example: `"ipv4"`
+ `network` - The IP network to which the geolocation information applies to. The format for the network is in free form and can be any network format (CIDR or `inetnum` / `NetRange`). Example: `"44.31.140.0/24"`
+ `continent` - The continent as two letter code. Example: `"NA"`
+ `country_code` - The [ISO 3166-1 alpha-2 country code](https://en.wikipedia.org/wiki/ISO_3166-1) to which the IP address belongs. This is the country specific geolocation of the IP address. Example: `"US"`
+ `country` - The full name of the country. Example: `"United States"`
+ `state` - The state / administrative area for the queried IP address. Example: `"California"`
+ `city` - The city to which the IP address belongs. Example: `"Fremont"`
+ `zip` - The postal code for this IP. Example: `"94720"`
+ `timezone` - The timezone for this IP. Example: `"America/Los_Angeles"`
+ `latitude` - The geographical latitude for the IP address. Example: `"37.54827"`
+ `longitude` - The geographical longitude for the IP address. Example: `"-121.98857"`
+ `accuracy` - Geolocation information is not always accurate. For that reason, there is an `accuracy` number that gives an estimate of how accurate the geolocation information is. Entries with `accuracy = 1` have the highest accuracy, rows with `accuracy = 4` have the least accuracy.
  + `accuracy = 1` - Very high geolocation accuracy. You can rely the data to be accurate to the city level.
  + `accuracy = 2` - High geolocation accuracy. You can often rely the data to be accurate to the city level.
  + `accuracy = 3` - Medium geolocation accuracy. You can not rely the data to be accurate to the city level.
  + `accuracy = 4` - Low geolocation accuracy. Usually the data is accurate to the country level.

## ASN Database

For offline ASN data access, the **ASN Database** is provided.

The ASN database includes all assigned and allocated AS numbers by IANA and respective meta information. The database is updated several times per week. For active ASN's (at least one route/prefix assigned to the AS), the database includes rich meta information. For example, the provided information for the ASN `50673` would be:

```JavaScript
"50673": {
  "asn": "50673",
  "org": "Serverius Holding B.V.",
  "domain": "serverius.net",
  "abuse": "abuse@serverius.net",
  "type": "hosting",
  "created": "2010-09-07",
  "updated": "2022-11-15",
  "rir": "ripe",
  "descr": "SERVERIUS-AS, NL",
  "country": "NL",
  "active": true,
  "prefixes": [
    "2.59.183.0/24",
    "5.56.133.0/24",
    // many more IPv4 prefixes ...
  ],
  "prefixesIPv6": [
    "2001:67c:b0::/48",
    "2a00:1ca8::/32",
    // many more IPv6 prefixes ...
  ]
},
```

The database is in JSON format. The key is the ASN as `int` and the value is an object with AS meta information such as the one above.

### Hosting IP Ranges Database

+ [IPv4 Sample (CSV)](https://ipapi.is/data/HostingRangesIPv4-Sample.csv)
+ [IPv6 Sample (CSV)](https://ipapi.is/data/HostingRangesIPv6-Sample.csv)

The database considers all of the following services as hosting providers:

+ Normal hosting providers such as [Hetzner.de](https://www.hetzner.de/) or [Leaseweb.com](https://www.leaseweb.com/)
+ Large cloud providers such as [Amazon AWS](https://aws.amazon.com/) or [Microsoft Azure](https://azure.microsoft.com/)
+ Content Delivery Networks such as [Cloudflare](https://www.cloudflare.com/), [Fastly](https://www.fastly.com/) or [edg.io](https://edg.io/)
+ Anti-DDOS services such as [qrator.net](https://qrator.net/) or [ddos-guard.net](https://ddos-guard.net/)
+ IP leasing organizations such as [ipxo.com](https://ipxo.com/) or [interlir.com](https://interlir.com/)
+ Other SaaS, IaaS, or PaaS organizations such as [fly.io](https://fly.io/) or [Heroku](https://www.heroku.com/)

A [proprietary algorithm](https://ipapi.is/blog/detecting-hosting-providers.html) was developed to determine if a network belongs to a hosting provider or not. The database contains more than 470k IPv4 networks and more than 360k IPv6 networks and is constantly growing.

The file format of the database is CSV, where each line of the file contains the following fields:

+ `ipVersion` - Determines the IP type of the network. Either `4` or `6`.
+ `startIp` - The start IP address of the network range.
+ `endIp` - The end IP address of the network range.
+ `datacenter` - The name of the hosting / datacenter provider.
+ `domain` - The domain name of the hosting provider's website.

Example excerpt of the database (CSV):

```csv
ipVersion,startIp,endIp,datacenter,domain
4,176.9.14.8,176.9.14.15,Hetzner Online GmbH,www.hetzner.com
4,87.252.45.216,87.252.45.223,James Parker,fastnet.co.uk
4,5.35.9.0,5.35.9.255,OOO Network of data-centers Selectel,selectel.ru
4,46.97.71.216,46.97.71.223,Atom Hosting SRL,vodafone.com
4,86.66.36.176,86.66.36.183,Internet Services,isi.ch
4,51.68.216.0,51.68.216.127,FAST SERV INC d.b.a. QHoster.com,ovh.net
4,211.14.25.128,211.14.25.159,"BroadBand Tower, Inc.",www.bbtower.co.jp
4,195.128.178.0,195.128.178.255,MOD Mission Critical LLC,modmc.net
4,185.54.7.0,185.54.7.255,HIDORA SA,hidora.io
4,46.4.41.224,46.4.41.255,Hetzner Online GmbH,www.hetzner.com
4,87.118.220.244,87.118.220.247,Limited liabilities company Atlantic.,www.atlantic.net
4,87.252.39.152,87.252.39.159,James Parker,fastnet.co.uk
4,5.154.33.0,5.154.33.255,SETEGENIL SL,servihosting.es
4,185.139.128.0,185.139.131.255,Miss Hosting AB,misshosting.com
4,103.125.164.0,103.125.167.255,"Beijing Jingliang Cloud Technology Co.,Ltd.",ssvnet.cn
4,128.204.205.112,128.204.205.112,Snel.com B.V.,www.snel.com
4,85.199.246.176,85.199.246.183,M247 UK Ltd,m247.com
4,67.216.96.0,67.216.111.255,"ETEX COMMUNICATIONS, LLC",www.godaddy.com
4,148.252.239.152,148.252.239.155,M247 UK Ltd,m247.com
4,176.9.33.88,176.9.33.95,Hetzner Online GmbH,www.hetzner.com
4,5.133.198.180,5.133.198.181,Internet Vikings International AB,internetvikings.com
```
