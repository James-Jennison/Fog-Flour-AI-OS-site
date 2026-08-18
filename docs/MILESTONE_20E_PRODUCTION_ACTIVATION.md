# Milestone 20E — Production DNS and Automatic SSL activation

Status: **Complete; canonical project site is live**

Date: July 23, 2026

Deployed source commit: `35ca85f95c907e1b502a7320f1ee7d9c340672bd`

Documentation commit: recorded after production validation

## Model and reasoning checkpoint

| Field                     | Decision                                                                                                              |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Current model             | GPT-5.6 Sol                                                                                                           |
| Approved reasoning        | High                                                                                                                  |
| Milestone use             | Production DNS, TLS, backup, isolation, and exact rollback validation                                                 |
| Model or reasoning change | High was explicitly approved for this milestone only; `xhigh` was not used                                            |
| Expected usage impact     | Moderate to high because the production change required repeated independent origin, edge, DNS, and recovery checks |

## Approved scope

- Revalidate the reviewed origin artifact, certificate readiness, Cloudflare
  pre-state, Webuzo services, and backups.
- Take a fresh restore-tested pre-activation backup.
- Create one proxied Cloudflare A record for the approved canonical hostname.
- Enroll the canonical hostname in Webuzo Automatic SSL.
- Validate origin and edge HTTPS, routing, headers, public assets, DNS
  isolation, unrelated services, and rollback readiness.
- Take and restore-test a post-activation backup.

This milestone did not create `www` or `mail` project aliases, change
zone-wide Cloudflare settings, update the master website, change GitHub Pages,
push Git, add a process, add a scheduled task, or edit a Webuzo-generated
VirtualHost.

## Production result

| Field                  | Verified value                                      |
| ---------------------- | --------------------------------------------------- |
| Canonical URL          | `https://fog-flour.jamesjennison.net/`              |
| DNS                    | One proxied Cloudflare A record                     |
| Public origin exposure | Public DNS returns only Cloudflare edge addresses   |
| Webuzo user            | `jamesjen`                                          |
| Document root          | `/home/jamesjen/fog-flour.jamesjennison.net`        |
| Origin TLS             | Dedicated Webuzo-managed Let's Encrypt certificate |
| Certificate names      | `fog-flour.jamesjennison.net` only                  |
| Certificate expiry     | October 21, 2026                                    |
| Automatic renewal      | Webuzo ACME metadata and renewal schedule present   |
| Runtime                | None; Webuzo-managed Apache serves static files     |
| Background process     | None                                                |
| Database               | None                                                |

The reviewed 14-file, 264.1 KiB artifact remains byte-identical to the
origin-staging release. Its portable manifest is:

`509b09d1eaa87a072550e758c8332f692b9adc74e455a3f117aff2a3794d875f`

## TLS activation sequence

The existing trusted wildcard certificate was temporarily assigned only to
the Fog & Flour VirtualHost through Webuzo's supported certificate interface.
This prevented an edge-to-origin trust failure while DNS became active.
Direct-origin HTTPS passed without disabling certificate verification, and the
apex certificate remained unchanged.

After the single proxied DNS record returned HTTP 200, Webuzo's supported
Automatic SSL command issued and installed a dedicated Let's Encrypt
certificate. Webuzo did not issue coverage for unsupported aliases or a
wildcard: validation correctly rejected the absent
`www.fog-flour.jamesjennison.net` and
`mail.fog-flour.jamesjennison.net` hostnames.

The installed certificate:

- has a Subject Alternative Name for only the canonical project hostname;
- has a matching private key;
- verifies through its installed chain;
- has more than 30 days of validity;
- is served by the origin with the correct SNI hostname; and
- has Webuzo ACME renewal metadata and the existing renewal schedule.

The implementation followed Webuzo's supported
[certificate installation API](https://webuzo.com/docs/api/install-certificate/)
and
[Automatic SSL command](https://webuzo.com/docs/how-tos/how-to-install-an-ssl-certificate-via-command-line/).

## DNS isolation

Immediately before activation, the target record was absent and the 17
unrelated Cloudflare DNS records were reduced to a deterministic canonical
digest:

`2bdf257ba2d9a0b817c8c8ca57e1f5b825fb6a4fd4788d204efd980ac8612e9d`

After activation:

- exactly one target record exists;
- it is an A record for the canonical hostname;
- it points to the approved origin and is proxied;
- public DNS returns only Cloudflare edge addresses;
- no `www.fog-flour` or `mail.fog-flour` record exists; and
- all 17 unrelated records reproduce the exact pre-activation digest.

The exact Cloudflare record identifier was captured during activation for
single-record rollback and was not committed to this public repository.

## Production validation

- Edge HTTPS returns HTTP 200 with a trusted Cloudflare certificate covering
  the hostname.
- Direct-origin HTTPS returns HTTP 200 with the dedicated trusted certificate.
- HTTP permanently redirects to HTTPS while preserving path and query string.
- Homepage, robots, sitemap, manifest, and social-preview assets return HTTP
  200.
- The custom project error page returns HTTP 404.
- Canonical and Open Graph URLs use only the approved hostname.
- CSP, HSTS, referrer, content-type, and no-transform cache headers are
  present.
- The static build and privacy-boundary validator pass.
- The deployment artifact contains no `.git`, `.env`, private key, PEM, source
  map, dependency directory, log, or detected credential pattern.
- Webuzo, Webuzo-managed Apache, MariaDB, and backups remain healthy.
- The master apex, canonical `www` redirect, Player, QuireForge, status,
  webmail, autoconfiguration, and autodiscovery retain their expected
  responses.

### Lighthouse

| Profile | Performance | Accessibility | Best practices | SEO |
| ------- | ----------- | ------------- | -------------- | --- |
| Mobile  | 100         | 100           | 100            | 92  |
| Desktop | 100         | 100           | 100            | 92  |

The only scored SEO failure is the public `robots.txt`. Cloudflare prepends
zone-wide managed AI crawler content containing a `Content-Signal` directive
that this Lighthouse version does not recognize as valid robots syntax. The
origin's own robots file is valid and contains the approved sitemap.

No zone-wide Cloudflare setting was changed because this milestone approved
only the Fog & Flour DNS record. Reaching the target SEO score of at least 95
therefore remains an explicit exception or a separate owner-approved
Cloudflare policy change.

## Backups

Pre-activation Restic snapshot `d159e6fd` contains the reviewed content,
retained empty prior root, Webuzo configuration, generated Apache
configuration, database export, ACME state, and certificate state. A 10 percent
repository data check and five streamed restores passed.

Post-activation Restic snapshot `ffd9ed8e` records the live site, dedicated
certificate and key, ACME renewal state, retained prior root, and supporting
Webuzo state. A 10 percent repository data check and six streamed restores
passed.

## Rollback

DNS rollback is isolated to the one Fog & Flour Cloudflare record:

1. resolve the exact record by its canonical hostname and confirm its type,
   target, and proxy state;
2. delete only that exact record;
3. confirm public DNS no longer returns the project hostname;
4. leave the Webuzo domain and certificate in place for diagnosis; and
5. verify all unrelated DNS records against the recorded digest.

Content rollback remains available by atomically restoring:

`/home/jamesjen/.fog-flour-previous-m20d-20260723T130551Z`

Snapshot `d159e6fd` is the pre-activation recovery point and snapshot `ffd9ed8e`
is the known-good production recovery point. Estimated DNS rollback execution
time is under five minutes after approval, subject to resolver caching.

## Remaining approval gates

- Decide whether to accept the public Lighthouse SEO 92 result or separately
  approve a review of Cloudflare's zone-wide managed robots policy.
- Update the master website from planned to live.
- Publish the GitHub Pages transition and later disable Pages.
- Push the local documentation commits.
