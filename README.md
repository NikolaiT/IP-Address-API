# IP Address API

This IP address API returns useful meta-information for IP addresses. For example, the API response includes the organization of the IP address, ASN information and geolocation intelligence and WHOIS data.

Furthermore, the API response allows to derive **security information** for each IP address, for example whether an IP address belongs to a hosting provider (`is_datacenter`), is a TOR exit node (`is_tor`), if an IP address is a proxy (`is_proxy`) or VPN (`is_vpn`) or belongs to an abuser (`is_abuser`).

This API strongly emphasizes **hosting detection** and **VPN Enumeration**. 

A complicated hosting detection algorithm was developed to achieve a high detection rate. Whois records, public hosting IP ranges from hosting providers and a proprietary hosting discovery algorithm are used to decide whether an IP address belongs to a hosting provider or not.

Furthermore, many prominent VPN providers are constantly enumerated to collect high quality VPN Enumeration data. To learn more, read the [IP to VPN Database Page](https://ipapi.is/vpn-detection.html).

## Quickstart

Lookup any IP address: [https://api.ipapi.is/?q=3.5.140.2](https://api.ipapi.is/?q=3.5.140.2)

Or ASN: [https://api.ipapi.is/?q=as396356](https://api.ipapi.is/?q=as396356)

Lookup your own IP address: [https://api.ipapi.is/](https://api.ipapi.is/)

Usage with JavaScript:

```JavaScript
fetch('https://api.ipapi.is/?q=23.236.48.55')
  .then(res => res.json())
  .then(res => console.log(res));
```

Usage with `curl`:

```bash
curl 'https://api.ipapi.is/?q=32.5.140.2'
```

## API Limitations

It is very costly to maintain the API, because it takes a lot of time to update the data and improve the data quality. The API runs on 4 servers across the globe and hosting alone costs 150$ per month.

For that reason, the free plan is limited to 1000 requests per day and there exists a pro version at [https://ipapi.is/](https://ipapi.is/)

In order to increase you daily request volume, consider subscribing to a paid plan at [https://ipapi.is/pricing.html](https://ipapi.is/pricing.html) to help out the project. The API runs on several servers across the globe and currently handles millions of daily requests.

## IP Address Databases Download

This repository contains various free databases:

+ Geolocation Database - free - Contains millions of rows that associate geolocation intelligence with IPv4 and IPv6 networks
  + [Download the free IPv4 Geolocation Database](databases/geolocationDatabaseIPv4.csv.zip)
  + [Download the free IPv6 Geolocation Database](databases/geolocationDatabaseIPv6.csv.zip)
+ ASN Database - sample only - This database includes rich meta data for all active ASN's of the Internet (Around 85.000 active ASN's). You can get the [full database here](https://ipapi.is/).
+ Hosting IP Ranges Database - sample only - Contains IP addresses that belong to hosting providers or cloud services such as Amazon AWS or Microsoft Azure. Contains very small and niche hosting providers. You can get the [full database here](https://ipapi.is/).

## Commercial Databases

If you want to use accurate and frequently updated IP address data for commercial projects, consider using the commercial databases at [ipapi.is](https://ipapi.is)

+ [IP to Hosting Database](https://ipapi.is/hosting-detection.html) - **Commercial** -  Contains IP addresses that belong to hosting providers or cloud services such as Amazon AWS or Microsoft Azure. The database contains very small and niche hosting providers.
+ [IP to Geolocation Database](https://ipapi.is/geolocation.html) - **Free & Commercial** - Contains the geographical location (Including coordinates, city name and country) of millions of unique IPv4 and IPv6 networks.
+ [IP to ASN Database](https://ipapi.is/asn.html) - **Commercial** - This database includes rich meta data for all active and inactive ASNs of the Internet. Currently, there are around 85.000 active ASNs and hundreds of thousands inactive/unassigned ASNs.
+ [IP to VPN Database](https://ipapi.is/vpn-detection.html) - **Commercial** - The IP to VPN Database is a database that contains VPN IP addresses from well-known providers like ExpressVPN and NordVPN. Additionally, the database contains IP ranges from other VPN providers from which the provider's name is not known.
+ [IP to Abuser Database](https://ipapi.is/ip-to-abuser.html) - **Commercial** - Contains IP addresses that are known to be involved in malicious activities such as hacking attempts, spam, and DDoS attacks.
+ [IP to Company Database](https://ipapi.is/ip-to-company.html) - **Commercial** - Maps IP addresses to the companies that own them, providing detailed information about the organization behind each IP.

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
{
  "asn": 50673,
  "abuser_score": "0.0013 (Low)",
  "descr": "SERVERIUS-AS, NL",
  "country": "nl",
  "active": true,
  "org": "Serverius Holding B.V.",
  "domain": "serverius.net",
  "abuse": "abuse@serverius.net",
  "type": "hosting",
  "created": "2010-09-07",
  "updated": "2024-07-11",
  "rir": "RIPE",
  "whois": "https://api.ipapi.is/?whois=AS50673",
  "prefixes": [
    "5.178.64.0/21",
    "5.178.64.0/24",
    "5.188.12.0/22",
    "5.188.12.0/24",
    "5.188.13.0/24",
    "5.188.14.0/24",
    // many more
  ],
  "prefixesIPv6": [
    "2001:67c:b0::/48",
    "2a00:1ca8::/32",
    "2a00:1ca8:77::/48",
    "2a00:1caa::/32",
    "2a02:1680::/32",
    "2a03:3f40::/32",
    "2a09:e40::/32",
    "2a09:4d41::/32",
    "2a09:aa80::/32",
    "2a0a:3f40::/32",
    "2a0e:c9c0::/29"
  ],
  "elapsed_ms": 0.51
}
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

A [proprietary algorithm](https://ipapi.is/blog/detecting-hosting-providers.html) was developed to determine if a network belongs to a hosting provider or not. The database contains more than 556k IPv4 networks and more than 554k IPv6 networks and is constantly growing.

The file format of the database is CSV, where each line of the file contains the following fields:

+ `ipVersion` - Determines the IP type of the network. Either `4` or `6`.
+ `startIp` - The start IP address of the network range.
+ `endIp` - The end IP address of the network range.
+ `datacenter` - The name of the hosting / datacenter provider.
+ `domain` - The domain name of the hosting provider's website.

Example excerpt of the database (CSV):

```csv
startIp,endIp,ipVersion,datacenter,domain
92.222.187.0,92.222.187.255,4,OVH VPS ES,ovh.net
153.120.81.0,153.120.81.255,4,SAKURA Internet Inc.,sakura.ad.jp
185.39.138.85,185.39.138.85,4,MissDomain Group AB,missdomain.com
86.66.23.232,86.66.23.239,4,Internet Services,isi.ch
51.77.100.128,51.77.100.143,4,OVH Ltd,ovh.co.uk
38.142.65.248,38.142.65.255,4,"Imperva, Inc",www.imperva.com
185.103.97.252,185.103.97.255,4,UK Dedicated Servers Ltd,uksrv.co.uk
103.64.80.0,103.64.83.255,4,"Guangdong Aofei Data Technology Co., Ltd.",ofidc.com
194.218.29.104,194.218.29.111,4,Iver Sverige AB,iver.com
135.125.25.152,135.125.25.159,4,OVH SAS,ovhcloud.com
46.165.192.0,46.165.255.255,4,Leaseweb Deutschland GmbH,leaseweb.com
5.198.151.0,5.198.151.127,4,Xidras GmbH,xidras.com
78.41.202.254,78.41.202.254,4,Snel.com B.V.,snel.com
217.57.169.16,217.57.169.23,4,ASAS.R.L.,lir.ae
94.46.126.31,94.46.126.31,4,MissDomain Group AB,missdomain.com
133.32.45.0,133.32.45.255,4,"GMO Internet,Inc.",gmo.jp
5.9.232.240,5.9.232.255,4,Hetzner Online GmbH,hetzner.com
176.9.127.64,176.9.127.95,4,Hetzner Online GmbH,hetzner.com
94.130.41.176,94.130.41.183,4,Hetzner Online GmbH,hetzner.com
159.148.115.56,159.148.115.63,4,SIA Latnet,bite.lv
51.91.67.0,51.91.67.255,4,OVH SAS,ovhcloud.com
89.151.66.192,89.151.66.255,4,Pulsant Limited,pulsant.com
156.240.103.0,156.240.103.255,4,Bunny Technology LLC,bunny.net
149.6.42.4,149.6.42.7,4,Netwise Hosting Ltd,netwise.co.uk
```

## IP to VPN Database

The IP to VPN Database is a database that contains VPN IP addresses from well-known providers like ExpressVPN and NordVPN. Additionally, the database contains IP ranges from other VPN providers from which the provider's name is not known. The database is updated on a regular basis and is available for purchase in CSV or MMDB format.

Despite the fact that VPN services bring a lot of benefits to its users, they also have a dark side. VPN services are often used by cybercriminals to hide their real IP address and to bypass geo-restrictions and commit fraud. The IP to VPN Database is a valuable tool for businesses that want to detect VPN usage on their website or app and to take appropriate measures to protect their services.

The IP to VPN Database is provided as large CSV file with the following fields:

+ `ipVersion` - Either `4` or `6`. Determines the IP type of the network. Example: `"4"`
+ `startIp` - The first IP address of the network range. Example: `45.86.210.31`
+ `endIp` - The last IP address of the network range. Example: `45.86.210.31`
+ `serviceName` - The name of the VPN provider. Example: `NordVPN`

### How to use the IP to VPN Database?

This example shows how to work with the IP to VPN Database in MMDB format. First, you have to download the database sample:

```
curl -O https://ipapi.is/data/VPN-Database-Sample.mmdb
```

And then you can read the database with [mmdbctl](https://github.com/ipinfo/mmdbctl):

```
mmdbctl read -f json-pretty 169.150.203.171 VPN-Database-Sample.mmdb
```

which outputs:

```
{
  "endIp": "169.150.203.171",
  "ipVersion": "4",
  "network": "169.150.203.171/32",
  "serviceName": "NordVPN"
}
```

## IP to Abuser Database

The IP to Abuser Database is a database that contains IP addresses known for abusive behavior. The database is updated on a regular basis and is available for purchase in CSV or MMDB format.

Abusive IP addresses can be used for various malicious activities, including spam, hacking attempts, and DDoS attacks. The IP to Abuser Database is a valuable tool for businesses that want to detect and prevent abuse on their website or app and to take appropriate measures to protect their services.

The IP to Abuser Database is provided as a CSV or MMDB file with the following fields:

+ `ipVersion` - Either `4` (IPv4) or `6` (IPv6), determining the IP type of the network.
+ `startIp` - The start IP address of the range in string format. Example: `45.86.210.31`
+ `endIp` - The end IP address of the range in string format. Example: `45.86.210.31`
+ `isAbuser` - A boolean value indicating whether the IP is known for abusive behavior. Example: `true`

## IP to Company Database

The IP to Company Database contains the name, domain and type for every company / organization in the Internet that owns an IP address or IP network. IP ownership of companies is obtained by querying the five major WHOIS registries responsible for number resources: ARIN, RIPE, APNIC, LACNIC, and AFRINIC. The IP to Company Database is updated in regular intervals to ensure the correctness of the firmographic data.

The IP to Company Database allows you to accurately classify traffic according to different criteria, such by company name or type. For example, traffic from an organization classified as hosting tends to have a lower reputation compared to traffic from a government or education network.

The file format of the IP to Company Database is either in CSV or MMDB format and contains the following fields:

+ `startIp` - The start IP address of the range in string format. Example: `117.200.64.0`
+ `endIp` - The end IP address of the range in string format. Example: `117.200.239.255`
+ `ipVersion` - Either `4` (IPv4) or `6` (IPv6), determining the IP type of the network.
+ `company` - The name of the company. Example: `Broadband Networks`
+ `abuser_score` - The field `abuser_score` represents the quota of abusive IP addresses of the network belonging to the organization (company). The higher this number is, the more abusive the whole network is. `abuser_score = Number of Abusive IPs / Number of Total IPs in the Network`
  - Example: `0.0183 (Elevated)`
  - `Very High` - More than 20% of all IPs of the network are abusive
  - `High` - Between 3% to 20% of all IPs of the network are abusive
  - `Elevated` - Between 0.85% to 3% of all IPs of the network are abusive
  - `Low` - Between 0.85% to 0.05% of all IPs of the network are abusive
  - `Very Low` - Less than 0.05% of all IPs of the network are abusive
+ `domain` - The domain of the Company. Example: `bsnl.co.in`
+ `type` - The field `type` contains the type of the company. The following company types exist:
  - `hosting` - The company is a hosting provider (Example: `5.161.56.240`)
  - `education` - The company is a university or other kind of educational institution (Example: `129.114.137.255`)
  - `government` - The company is a governmental institution (Example: `137.75.167.171`)
  - `banking` - The company is a banking/financial institution (Example: `199.67.175.0`)
  - `isp` - The company is an Internet Service Provider (ISP) (Example: `117.200.226.44`)
  - `business` - If the type is not one of the above, the type is the generic business type (Example: `17.133.85.230`)
+ `rir` - The Regional Internet Registry responsible for the IP range. Example: `APNIC`
+ `abuseName` - The abuse contact name, the name of the identity that receives abuse complaints about the network provided in `company.network`. The abuse contact name is mostly the same name as in `company.name`. Example: `ABUSE BSNLIN`
+ `abuseAddress` - The abuse contact address. This is the physical address of abuse contact of the entity that owns the network specified in `company.network`. Example: `Internet Cell, Bharat Sanchar Nigam Limited., 8th Floor,148-B Statesman House, Barakhamba Road, New Delhi - 110 001`
+ `abuseEmail` - The abuse contact email. This email address takes abuse complaints about incidents associated with the network specified in `company.network`. Example: `abuse1@bsnl.co.in`
+ `abusePhone` - The abuse contact phone number. This phone number takes abuse complaints about incidents associated with the network specified in `company.network`. Example: `+000000000`
