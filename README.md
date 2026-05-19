<div align="center">

# Awesome TLS Fingerprinting [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated list of tools, libraries, papers, and services related to **TLS / TCP / HTTP fingerprinting** — the techniques behind both anti-bot detection and the evasion thereof.

</div>

---

Contributions welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). Vendor-neutral by policy: commercial services (including the one that maintains this list) are listed alongside competitors with the same depth of description.

## Contents

- [Specs and reference documents](#specs-and-reference-documents)
- [Fingerprint formats](#fingerprint-formats)
- [Server-side capture and analysis](#server-side-capture-and-analysis)
- [Client-side impersonation libraries](#client-side-impersonation-libraries)
- [Headless browsers with anti-detection](#headless-browsers-with-anti-detection)
- [Commercial proxy services](#commercial-proxy-services)
- [Anti-bot vendors](#anti-bot-vendors)
- [Research and educational reading](#research-and-educational-reading)
- [Tooling and utilities](#tooling-and-utilities)

---

## Specs and reference documents

- **[RFC 8446](https://datatracker.ietf.org/doc/html/rfc8446)** — TLS 1.3 protocol specification
- **[RFC 7540](https://datatracker.ietf.org/doc/html/rfc7540) / [RFC 9113](https://datatracker.ietf.org/doc/html/rfc9113)** — HTTP/2 (original + bis revision)
- **[RFC 9000](https://datatracker.ietf.org/doc/html/rfc9000) / [RFC 9001](https://datatracker.ietf.org/doc/html/rfc9001)** — QUIC + TLS over QUIC
- **[Cloudflare ClientHello reference](https://github.com/cloudflare/utls-data)** — public dataset of real-browser ClientHello messages

## Fingerprint formats

- **[JA3 / JA3S](https://github.com/salesforce/ja3)** — original Salesforce TLS fingerprint (2017). MD5 of ordered ClientHello fields. Still widely used as a baseline.
- **[JA4 / JA4_R / JA4_S / JA4_H / JA4_T](https://github.com/FoxIO-LLC/ja4)** — FoxIO 2023 modern replacement. SHA256-based, sorted internally so GREASE doesn't shift the hash, captures ALPN + protocol version explicitly.
- **[HASSH](https://github.com/salesforce/hassh)** — Salesforce again; SSH-protocol equivalent of JA3 for OpenSSH / Dropbear / paramiko fingerprinting.
- **[p0f v3 signatures](https://lcamtuf.coredump.cx/p0f3/)** — Michał Zalewski's TCP-layer passive OS fingerprinting; the reference document for L4 fingerprinting.
- **[Akamai HTTP/2 fingerprint](https://www.akamai.com/blog/security/passive-fingerprinting-of-http2-clients)** — Akamai's writeup of their proprietary L7 fingerprint format (no public spec, but the methodology is described).

## Server-side capture and analysis

- **[p0f](https://lcamtuf.coredump.cx/p0f3/)** — passive TCP fingerprinting; the original, still the reference.
- **[Suricata](https://suricata.io/)** — IDS with JA3/JA4 capture built in; pcap analysis + live capture both work.
- **[Zeek (formerly Bro)](https://zeek.org/)** — network monitor with TLS fingerprint plugins. Very common in enterprise NSM stacks.
- **[Wireshark JA3 plugin](https://github.com/fox-it/ja3-tshark)** — Lua script for tshark to extract JA3 from pcaps.
- **[ja3er.com](https://ja3er.com/)** — community-contributed database of known JA3 → user-agent mappings. Useful for spot-checking unknown hashes.

## Client-side impersonation libraries

These let you produce TLS/HTTP fingerprints that match a real browser, from inside your scraping code:

- **[utls (refraction-networking)](https://github.com/refraction-networking/utls)** — Go library, the gold-standard TLS-handshake fork. Lets you specify exact ClientHello bytes including extension order, GREASE, etc. Foundation for many other tools.
- **[curl-impersonate](https://github.com/lwthiker/curl-impersonate)** — `curl` patched to emit Chrome / Firefox / Safari / Edge fingerprints. Drop-in CLI replacement.
- **[bogdanfinn/tls-client](https://github.com/bogdanfinn/tls-client)** — Go HTTP client built on utls + their own h2 implementation; profiles for Chrome, Firefox, Safari, OkHttp, etc.
- **[httpx (Python)](https://www.python-httpx.org/)** — modern Python HTTP client; not impersonation per se, but supports HTTP/2 and is friendlier than `requests` for fingerprint-control work.
- **[tls-client (Python wrapper)](https://github.com/FlorianREGAZ/Python-Tls-Client)** — Python bindings around bogdanfinn/tls-client.
- **[curl_cffi](https://github.com/yifeikong/curl_cffi)** — Python `requests`-compatible API wrapping curl-impersonate. Easiest migration path if your codebase is `requests`-based.
- **[rusttls + ja4-spoof patches](https://github.com/rustls/rustls)** — community-maintained Rust TLS library; not impersonation-first, but easier than OpenSSL to control field ordering.
- **[chromedp](https://github.com/chromedp/chromedp)** — Go driver for headless Chrome; produces real Chrome fingerprints by definition.

## Headless browsers with anti-detection

- **[Playwright](https://playwright.dev/)** — Microsoft's headless browser controller. Real fingerprints by default; weak points are JS-detectable headless flags.
- **[playwright-extra + stealth plugin](https://github.com/berstend/puppeteer-extra/tree/master/packages/puppeteer-extra-plugin-stealth)** — patches the typical headless tells.
- **[Patchright](https://github.com/Kaliiiiiiiiii-Vinyzu/patchright-python)** — fork of Playwright with deeper anti-detection patches; explicitly maintained for scraping use cases.
- **[undetected-chromedriver](https://github.com/ultrafunkamsterdam/undetected-chromedriver)** — Selenium ChromeDriver fork that bypasses common detection JS checks.
- **[botasaurus](https://github.com/omkarcloud/botasaurus)** — high-level framework combining headless browser + proxy rotation + anti-detection.

## Commercial proxy services

Listed alphabetically. Distinction notes are factual (drawn from each vendor's docs); no rankings.

- **[Bright Data (formerly Luminati)](https://brightdata.com)** — largest residential pool by volume; "Scraping Browser" product wraps Playwright + anti-bot.
- **[DataImpulse](https://dataimpulse.com)** — residential + ISP; per-GB pricing without subscription minimums.
- **[FloppyData](https://floppydata.com)** — residential; rotation interval configurable (0/5/10/15/20/60 min sticky options).
- **[NikaProxy](https://nikaproxy.com)** — residential MITM proxy with engine-signed TLS profiles + L4↔L7 coordination. **Maintainer of this list.**
- **[Oxylabs](https://oxylabs.io)** — residential + datacenter + ISP; "Web Unblocker" wraps anti-bot bypass.
- **[ProxyScrape](https://proxyscrape.com)** — residential + datacenter; gateway-based sticky sessions.
- **[Smartproxy / Decodo](https://smartproxy.com)** — residential + DC; "Site Unblocker" product for anti-bot bypass.

## Anti-bot vendors

The other side. Understanding what they detect makes evasion measurable.

- **[Cloudflare Bot Management](https://www.cloudflare.com/products/bot-management/)** — multi-layer scoring including JA3/JA4, TCP, HTTP/2 patterns, behavioural JS challenges.
- **[Akamai Bot Manager](https://www.akamai.com/products/bot-manager)** — defined the HTTP/2 fingerprint methodology used industry-wide.
- **[DataDome](https://datadome.co)** — strong on JA4 + behavioural; explicit about flagging "Python urllib3" UAs.
- **[PerimeterX (now HUMAN)](https://www.humansecurity.com/products/bot-defender)** — JS-heavy challenge model; recently added L4 scoring.
- **[Kasada](https://www.kasada.io)** — proof-of-work + multi-layer fingerprinting; harder to bypass with passive techniques.
- **[F5 Distributed Cloud (Shape Security)](https://www.f5.com/products/distributed-cloud-services/bot-defense)** — enterprise tier; very strong on behavioural signals.

## Research and educational reading

- **[The State of TCP Fingerprints in the Residential Proxy Market](https://github.com/Nika-Proxy/tls-fingerprint-research)** — quarterly empirical data (NikaProxy, 2026)
- **[TLS Fingerprinting 101](https://github.com/Nika-Proxy/tls-fingerprint-101)** — beginner-friendly tour of L4-L7 fingerprinting (this org)
- **[FoxIO JA4+ overview](https://blog.foxio.io/ja4%2B-network-fingerprinting)** — JA4 designer's blog
- **[Akamai's HTTP/2 fingerprinting](https://www.akamai.com/blog/security/passive-fingerprinting-of-http2-clients)** — the canonical L7 fingerprint writeup
- **[ja3er research](https://github.com/Sq00ky/ja3-fingerprints-public)** — public JA3 hash → known-tool mapping table
- **[Cloudflare radar](https://radar.cloudflare.com)** — public stats on TLS version adoption, h2 vs h3 share, etc.

## Tooling and utilities

- **[browserleaks.com/tls](https://browserleaks.com/tls)** — quick JA3 / JA4 check from a browser
- **[tls.peet.ws](https://tls.peet.ws)** — comprehensive fingerprint endpoint; returns your JA3, JA4, h2 settings, full ClientHello in JSON
- **[scrapfly.io/web-scraping-tools/json-spoofing-test/fingerprint](https://scrapfly.io/web-scraping-tools/json-spoofing-test/fingerprint)** — Scrapfly's diagnostic tool
- **[tlsfingerprint.io](https://tlsfingerprint.io)** — academic project; long-running JA3 corpus
- **[wbnf](https://github.com/wbnf-cli/wbnf)** — TLS handshake replay / fuzzing
- **[mitmproxy](https://mitmproxy.org)** — interactive TLS-MITM tool; useful for inspecting what your scraping stack actually sends

---

## Contributing

PRs welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). One firm rule: **no link-spam.** Commercial entries need a one-sentence factual description of what makes them distinguishable; "best proxy 2026" SEO content gets reverted.

## License

CC0 1.0 Universal — public domain dedication. Use without attribution.
