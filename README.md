# IP Address API

[![GitHub stars](https://img.shields.io/github/stars/NikolaiT/IP-Address-API?style=flat)](https://github.com/NikolaiT/IP-Address-API)
[![License: MIT](https://img.shields.io/github/license/NikolaiT/IP-Address-API)](LICENSE)
[![API: 1,000 free requests a day](https://img.shields.io/badge/API-1%2C000%20free%20requests%20a%20day-brightgreen)](https://ipapi.is/pricing.html?ref=github)

The [ipapi.is](https://ipapi.is/?ref=github) API tells you who owns an IPv4 or IPv6 address, where it is, and what kind of address it is: a datacenter, a VPN (and which one), a proxy, a Tor exit node or a known abuser. One request, one JSON object. A free account includes 1,000 requests a day, commercial use included.

- **VPN detection that names the service.** ipapi.is connects to 73 VPN services around the clock and records the addresses their traffic leaves from, so it knows exit nodes from observation and can say whose they are.
- **Hosting detection beyond the big clouds.** More than 29,000 hosting providers are tracked, thousands of them small hosts that publish no range list.
- **The owner down to the customer.** Nearly 8 million WHOIS networks, each with the organization, its type (hosting, ISP, business, education, government, banking) and the abuse contact.
- **A free tier you can build on.** 1,000 requests a day with the complete response, for commercial products too. More than two billion requests are served a month.

```bash
curl "https://api.ipapi.is/?q=187.13.197.43&key=your_api_key&v=1"
```

```json
{
  "ip": "187.13.197.43",
  "rir": "RIPE",
  "is_bogon": false,
  "is_mobile": false,
  "is_satellite": false,
  "is_anycast": false,
  "is_crawler": false,
  "is_datacenter": true,
  "is_tor": false,
  "is_proxy": false,
  "is_vpn": true,
  "is_abuser": false,
  "vpn": {
    "service": "NordVPN",
    "type": "exit_node",
    "last_seen_str": "2026-11-13T14:55:05.677Z",
    "exit_node_region": "France #1168"
  },
  "datacenter": {
    "datacenter": "PacketHub S.A.",
    "network": "187.13.197.0 - 187.13.197.255"
  },
  "company": {
    "name": "PacketHub S.A.",
    "abuser_score": "0.0117 (Elevated)",
    "type": "hosting"
  },
  "abuse": {
    "email": "abuse@packethub.tech"
  },
  "asn": {
    "asn": 212238,
    "org": "Datacamp Limited",
    "type": "hosting"
  },
  "location": {
    "country": "France",
    "city": "Marseille 01",
    "timezone": "Europe/Paris",
    "accuracy": "MEDIUM"
  }
}
```

The response is shortened here; [all fields](#the-response) are listed below.

## Quick start

1. [Create a free account](https://ipapi.is/app/signup?ref=github) and copy the API key from the dashboard.
2. Send the address as `q` and the key as `key`. Leave out `q` to look up the address the request comes from.
3. Send `v=1` with them. It names the response format described here, so your code keeps getting it when a newer format appears.

JavaScript (Node.js 18 or newer). The key travels in a POST body, which keeps it out of URLs and logs:

```js
const res = await fetch('https://api.ipapi.is', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ q: '187.13.197.43', key: process.env.IPAPI_KEY, v: 1 }),
});
const info = await res.json();
console.log(info.is_vpn, info.vpn.service, info.location.country); // true NordVPN France
```

Python:

```python
import os
import requests

def lookup(ip):
    res = requests.post('https://api.ipapi.is',
                        json={'q': ip, 'key': os.environ['IPAPI_KEY'], 'v': 1}, timeout=5)
    res.raise_for_status()
    return res.json()

info = lookup('187.13.197.43')
print(info['is_vpn'], info['vpn']['service'], info['location']['country'])  # True NordVPN France
```

Keep the key on your server. A key in browser JavaScript or in a public repository will be found and spent by someone else.

### Without a key

The API also answers without a key: 30 requests a day per client address, with the owner and the location, and without the detection flags.

```bash
curl "https://api.ipapi.is/?q=187.13.197.43"
```

```json
{
  "ip": "187.13.197.43",
  "is_bogon": false,
  "company": "PacketHub S.A.",
  "asn": "AS212238 Datacamp Limited",
  "city": "Marseille 01",
  "region": "Provence-Alpes-Côte d'Azur",
  "country": "France",
  "lat": 43.2951,
  "lon": 5.3861,
  "timezone": "Europe/Paris",
  "docs": "https://ipapi.is/free-tier.html?ref=api"
}
```

## What you can do with it

**Refuse signups from VPNs, proxies and Tor.** An Express route:

```js
const express = require('express');

const app = express();
app.set('trust proxy', 1); // behind a load balancer, so that req.ip is the visitor

app.post('/signup', async (req, res) => {
  const ip = await fetch('https://api.ipapi.is', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ q: req.ip, key: process.env.IPAPI_KEY, v: 1 }),
  }).then((r) => r.json()).catch(() => ({})); // if the lookup fails, let people in

  if (ip.is_vpn || ip.is_proxy || ip.is_tor) {
    return res.status(403).json({ error: `Signups through ${ip.vpn?.service ?? 'a VPN or proxy'} are not accepted.` });
  }
  res.json({ ok: true }); // create the account
});
```

[ipapi.is-node](https://github.com/NikolaiT/ipapi.is-node) packs this into a middleware, `guard()`, with caching and a choice of what to block.

**Find the datacenter traffic in an access log.** A bulk request takes up to 100 addresses; this prints the busiest clients that are servers, with their hosting provider:

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head -100 | awk '{print $2}' \
  | jq -R . | jq -s '{ips: ., key: env.IPAPI_KEY, v: 1}' \
  | curl -s https://api.ipapi.is -H 'Content-Type: application/json' -d @- \
  | jq -r 'to_entries[] | select(.value.is_datacenter?) | "\(.key)\t\(.value.datacenter.datacenter)"'
```

```
85.10.199.76	Hetzner Online GmbH
3.5.140.2	Amazon AWS
165.227.0.0	DigitalOcean
```

**Find out where to report an attacker.**

```bash
curl -s "https://api.ipapi.is/?q=107.174.138.172&key=$IPAPI_KEY&v=1" | jq .abuse
```

```json
{
  "name": "VPS ACE",
  "address": "350 Main Street, Buffalo, NY, 14202, US",
  "email": "support@vpsace.com",
  "phone": "+1-855-587-7223"
}
```

**List every prefix of a network.** An AS number as `q` returns the autonomous system with all the routes it announces:

```bash
curl -s "https://api.ipapi.is/?q=AS16509&key=$IPAPI_KEY&v=1" | jq '{org, type, prefixes: (.prefixes | length)}'
```

```json
{
  "org": "Amazon.com, Inc.",
  "type": "hosting",
  "prefixes": 17305
}
```

**Give it to an LLM.** `https://api.ipapi.is/toon?q=…` returns the same data as [TOON](https://github.com/toon-format/toon), which takes fewer tokens than JSON. There is also `/html` for reading in a browser, `/csv` and `/text`.

## The response

| Field | Meaning |
|---|---|
| `ip`, `rir` | The address, and the registry its WHOIS record comes from |
| `is_bogon` | A private, reserved or special address |
| `is_datacenter`, `datacenter` | It belongs to a hosting, cloud or CDN provider; the object names the provider and its range |
| `is_vpn`, `vpn` | It is a VPN exit node or server; the object names the service and says when the node was last seen and where it is |
| `is_proxy` | A known open proxy |
| `is_tor` | A Tor exit node |
| `is_abuser` | Recently seen attacking, spamming or scanning, by blocklists or our own honeypots |
| `is_crawler` | `false`, or the name of the bot that uses the address: `GoogleBot`, `BingBot`, `ClaudeBot` and several hundred more |
| `is_mobile`, `is_satellite` | It belongs to a mobile carrier or to a satellite ISP such as Starlink |
| `is_anycast` | It answers from many places at once, like `1.1.1.1` |
| `egress_service` | Its traffic comes from a managed service, not from its owner: a secure web gateway such as Zscaler, iCloud Private Relay, a public DNS resolver, an email gateway |
| `company` | The organization the address is registered to: name, domain, type, network and the share of the network seen in abuse |
| `abuse` | Where to report abuse: name, address, email and phone |
| `asn` | The autonomous system that announces the address: number, organization, type, route |
| `location` | Country, state, city, postal code, coordinates, timezone, local time, currency, and how accurate the location is |

Every field is described in the [documentation](https://ipapi.is/developers.html?ref=github), and the API has an [OpenAPI description](https://ipapi.is/openapi.json) and an [llms.txt](https://ipapi.is/llms.txt) for AI agents.

Errors are JSON with a message in `error` and a stable `error_code`, such as `ERR_INVALID_IP_OR_ASN` (400), `ERR_FORBIDDEN_INVALID_API_KEY` (403) or `ERR_QUOTA_EXCEEDED` (429). A request without a key that goes over the daily limit gets `429 ERR_FREE_TIER_EXHAUSTED` with a `Retry-After` header; stop there, because a client that keeps sending is blocked for 24 hours.

## Plans

| | Requests | Response |
|---|---|---|
| Without a key | 30 a day per client address | Owner and location |
| [Free account](https://ipapi.is/app/signup?ref=github) | 1,000 a day, commercial use included | Complete |
| One-time credits | 30,000 per dollar, they never expire | Complete |
| Subscription | A fixed daily amount, billed monthly | Complete |

The free tier is not a trial: if 1,000 requests a day are enough, you never pay. [Pricing](https://ipapi.is/pricing.html?ref=github). The [Business plan](https://ipapi.is/business-subscription.html?ref=github) includes every database and a single Linux binary that serves the whole API from your own infrastructure.

The API runs on servers in Germany, the United States and Singapore. `api.ipapi.is` sends each request to the nearest healthy one; the [status page](https://ipapi.is/status.html?ref=github) shows them all.

## Databases

The data behind the API can be downloaded, for lookups without a network call.

- **Free IP geolocation database**: 4.8 million IPv4 and 1.4 million IPv6 ranges with country, state, city, coordinates and timezone, as CSV under CC BY-SA 4.0. [IPv4](databases/geolocationDatabaseIPv4.csv.zip), [IPv6](databases/geolocationDatabaseIPv6.csv.zip), and [how to use it](https://github.com/ipapi-is/ipapi#free-ip-geolocation-database), including the conversion to MMDB.
- **Samples of the commercial databases** in [`databases/`](databases): hosting ranges, VPN exit nodes, ASNs, companies, abusive addresses and full geolocation, each in CSV and MMDB. Their formats are documented in [ipapi-is/ipapi](https://github.com/ipapi-is/ipapi#the-commercial-databases).

## How the data is made

- [Detecting VPN services](https://ipapi.is/blog/systematic-vpn-detection.html?ref=github) by enumerating their exit nodes
- [An algorithm to detect hosting providers](https://ipapi.is/blog/detecting-hosting-providers.html?ref=github) and their IP ranges
- [Crawling the complete IPv4 reverse DNS space](https://ipapi.is/blog/crawling-the-complete-ipv4-reverse-dns-space.html?ref=github)
- [Scraping the complete IPv4 WHOIS address space](https://ipapi.is/blog/scraping-whois-ipv4-space.html?ref=github)
- [IP geolocation accuracy benchmark](https://ipapi.is/blog/ip-geolocation-accuracy-benchmark.html?ref=github): 18 providers against GPS positions, live

Found wrong data? Report it on the [corrections page](https://ipapi.is/corrections.html?ref=github). Questions go to [info@ipapi.is](mailto:info@ipapi.is).

## License

The code and documentation in this repository are under the [MIT license](LICENSE). The free geolocation database is under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/): use it for any purpose, including commercial ones, if you credit ipapi.is with a link, as described in [databases/LICENSE-free-geolocation-database.txt](databases/LICENSE-free-geolocation-database.txt). The sample files show the format of the commercial databases, which are licensed under the [ipapi.is terms](https://ipapi.is/terms.html?ref=github).
