# Surau Raudhatul Salam

Official website of Surau Raudhatul Salam, Taman Iringan Bayu, Seremban, Negeri Sembilan.

Live site: **https://surauraudhatulsalam.com**

---

## Overview

| Item | Detail |
|---|---|
| Type | Static HTML site (no build step) |
| Template | [Arsha](https://bootstrapmade.com/arsha-free-bootstrap-html-template-corporate/) by BootstrapMade, Bootstrap 5 |
| Hosting | GitHub Pages, deployed from `main` branch, root directory |
| Domain registrar | AWS Route 53 Domains (account `AFIQ CLOUD`) |
| DNS | AWS Route 53 hosted zone `Z05081721WHMX7MHLMGKH` |
| TLS | Let's Encrypt, issued and auto-renewed by GitHub Pages, HTTPS enforced |
| Domain verification | Verified on GitHub Pages (takeover protection) |

## Repository layout

```
.
├── index.html            Home page (about, activities, team, Facebook feed, map)
├── Page2/
│   └── galeri.html       Photo gallery
├── assets/
│   ├── css/style.css     Site styles
│   ├── js/main.js        Site scripts
│   ├── img/              Images (team photos, gallery, logo)
│   └── vendor/           Bootstrap, AOS, Swiper, GLightbox, Boxicons, Remixicon
├── CNAME                 Custom domain for GitHub Pages (do not delete)
└── .github/workflows/    label.yml (PR labeler, no deployment logic)
```

## How deployment works

1. Push (or merge) to `main`.
2. GitHub Pages automatically runs **pages build and deployment** (visible under the Actions tab).
3. The site is live within one to two minutes. No secrets, workflows or manual steps are required.

The `CNAME` file tells GitHub Pages which custom domain to serve. Keep it in the repository root with the single line `surauraudhatulsalam.com`.

## Making changes

| Task | Where |
|---|---|
| Edit text or sections on the home page | `index.html` |
| Add or replace gallery photos | `Page2/galeri.html` and `assets/img/` |
| Update AJK / team photos | `assets/img/team/` (use `.webp`, roughly 400x400) |
| Change colours or fonts | `assets/css/style.css` |
| Facebook feed | `index.html`, search for `facebook.com/plugins/page.php` |
| Google Maps embed | `index.html`, search for `google.com/maps/embed` |

Preview locally by opening `index.html` in a browser, or run `python3 -m http.server 8000` from the repo root and visit http://localhost:8000.

## DNS records (Route 53)

| Name | Type | Value | Purpose |
|---|---|---|---|
| `surauraudhatulsalam.com` | A | `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` | GitHub Pages IPv4 |
| `surauraudhatulsalam.com` | AAAA | `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153` | GitHub Pages IPv6 |
| `www.surauraudhatulsalam.com` | CNAME | `amiruafiq.github.io` | www redirects to apex |
| `_github-pages-challenge-amiruafiq.surauraudhatulsalam.com` | TXT | (verification code) | GitHub domain verification, keep permanently |
| `surauraudhatulsalam.com` | NS / SOA | Managed by Route 53 | Do not edit |

The current GitHub Pages IP addresses are published in the [GitHub Pages custom domain documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).

## Domain and renewal

| Setting | Value |
|---|---|
| Registered | 2 June 2026 |
| Expires | 2 June 2027 |
| Auto-renew | On |
| Transfer lock | On |
| Privacy protection | On |

The domain lapsed once in 2026 and had to be re-registered. Keep auto-renew enabled and make sure the payment method and contact email on the AWS account stay valid. AWS emails renewal notices to the registrant contact starting 45 days before expiry.

## Runbook: site is down

Work through these in order.

**1. Is DNS resolving?**

```bash
dig +short surauraudhatulsalam.com A
dig +short www.surauraudhatulsalam.com CNAME
```

Expected: the four `185.199.x.153` addresses and `amiruafiq.github.io.`. If empty, the A / AAAA / CNAME records are missing from the hosted zone. Re-create them with the table above (AWS CLI example):

```bash
aws route53 change-resource-record-sets \
  --hosted-zone-id Z05081721WHMX7MHLMGKH \
  --change-batch file://dns-records.json
```

**2. Does the registrar point to the right hosted zone?**

The name servers under Route 53 > Registered domains must match the NS record set inside hosted zone `Z05081721WHMX7MHLMGKH`. If the domain was re-registered, a second empty hosted zone may have been created; keep the one with records and update the registrar name servers to match.

**3. Is GitHub Pages healthy?**

Repository > Settings > Pages. Expected: source `main` / root, custom domain `surauraudhatulsalam.com` with a green check, Enforce HTTPS ticked.

**4. Certificate error in the browser?**

The certificate is served for `*.github.io` instead of the domain. In Settings > Pages, remove the custom domain, save, re-add it, save. GitHub reissues the certificate within a few minutes; then re-tick Enforce HTTPS.

**5. Domain expired?**

Route 53 > Registered domains shows the status. Within roughly 30 days of expiry the domain can be restored through an AWS Support case (restoration fee applies). After that it is released to the public.

## Credits

Template by [BootstrapMade](https://bootstrapmade.com/) under their [free licence](https://bootstrapmade.com/license/). Maintained by Afiq Kurshid.
