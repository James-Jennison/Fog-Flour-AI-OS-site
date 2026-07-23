# Milestone 20C — Webuzo domain and isolated document root

Status: **Complete; domain exists with an empty origin-only document root**

Date: July 23, 2026

## Model and reasoning checkpoint

| Field                     | Decision                                                                                                      |
| ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Current model             | GPT-5.6 Sol                                                                                                   |
| Current reasoning         | Medium                                                                                                        |
| Required reasoning        | Medium                                                                                                        |
| Model or reasoning change | None                                                                                                          |
| Why                       | The work was a tightly scoped, reversible Webuzo domain operation using an already validated hosting pattern. |
| Expected usage impact     | Low to moderate                                                                                               |

## Approved scope

- Create only `fog-flour.jamesjennison.net` as a Webuzo subdomain.
- Assign it to Webuzo user `jamesjen`.
- Use the isolated document root
  `/home/jamesjen/fog-flour.jamesjennison.net`.
- Keep the document root empty.
- Validate Webuzo, Apache, ownership, backups, and unaffected sites.

This milestone did not deploy the site artifact, create public DNS, modify
Cloudflare, enroll Automatic SSL, add redirects, alter the master website,
change GitHub Pages, create a process or scheduled task, or edit a
Webuzo-generated VirtualHost.

## Verified pre-change state

- Webuzo 4.7.4 and its panel service were active.
- Apache 2.4.68 was the active public site server on ports 80 and 443.
- Webuzo's separate Nginx listener served the control panel on ports 2003 and
  2005; it was not a project-site reverse proxy.
- `fog-flour.jamesjennison.net` was absent from the Webuzo domain inventory.
- `/home/jamesjen/fog-flour.jamesjennison.net` did not exist.
- No matching Webuzo certificate or ACME directory existed.
- Public A, AAAA, and CNAME answers for the hostname were absent.
- The Webuzo local zone contained no stale Fog & Flour records.
- Apache syntax and all master, Player, QuireForge, webmail, autoconfiguration,
  and autodiscovery baseline probes passed.
- The Webuzo backup and maintenance timers were active.

Restic snapshot `42d911bb` records the pre-domain state. A 10 percent repository
data check passed, and five streamed restores matched the live database,
master, Player, QuireForge, and Webuzo control files.

## Supported Webuzo operation

The domain was created through Webuzo's root-local end-user API for `jamesjen`.
The request used:

- domain type `subdomain`;
- parent domain `jamesjennison.net`;
- subdomain label `fog-flour`;
- home-relative path `fog-flour.jamesjennison.net`;
- wildcard disabled; and
- Let's Encrypt issuance disabled.

Webuzo returned `The Domain was added successfully.` The separately queried
domain inventory then confirmed the exact final mapping.

## Result

| Surface             | Verified state                                            |
| ------------------- | --------------------------------------------------------- |
| Hostname            | `fog-flour.jamesjennison.net`                             |
| Webuzo owner        | `jamesjen`                                                |
| Domain type         | Subdomain of `jamesjennison.net`                          |
| Document root       | `/home/jamesjen/fog-flour.jamesjennison.net`              |
| Root ownership      | `jamesjen:nobody`                                         |
| Root mode           | `0750`                                                    |
| Root content        | Empty                                                     |
| Public DNS          | Absent                                                    |
| Public status       | Not live                                                  |
| Artifact deployment | Not performed                                             |
| Apache              | Generated HTTP and HTTPS VirtualHosts; syntax valid       |
| Webuzo aliases      | Dormant `www.fog-flour` and `mail.fog-flour` aliases only |

Webuzo created one local-zone A record for the canonical subdomain. Cloudflare
remains authoritative publicly, so that local record does not make the
hostname publicly resolvable.

## Certificate side effect

Although public certificate issuance was disabled, Webuzo generated its
standard self-signed placeholder certificate and matching private key. The
certificate:

- has the canonical hostname as its common name;
- contains no Subject Alternative Name extension;
- is self-issued;
- matches its stored private key; and
- is valid from July 23, 2026 through July 23, 2027.

No certificate was issued, installed, repaired, replaced, renewed, or revoked
outside that Webuzo-controlled create-domain side effect. The placeholder is
not suitable for Cloudflare Full (Strict) production traffic. Automatic SSL
enrollment remains a separate approval gate.

## Validation

- Webuzo reports the exact approved hostname, owner, type, and document root.
- The document root exists, is empty, and does not overlap another site.
- Webuzo generated the expected HTTP and HTTPS VirtualHosts.
- Direct-origin HTTP and placeholder HTTPS return HTTP 403 for the empty root.
- Apache configuration syntax passes.
- Webuzo, MariaDB, and both Restic timers remain active.
- The master apex, canonical `www` redirect, Player, QuireForge, and webmail
  retain their expected success responses.
- Autoconfiguration and autodiscovery retain their expected HTTP 403 boundary.
- Public A, AAAA, and CNAME records remain absent.

Restic snapshot `0eb44192` records the completed domain state, including Webuzo
configuration, the generated Apache configuration, database export, empty
document root, ACME state, and certificate state. A 10 percent repository data
check passed. Five streamed restores and the empty-root inventory matched the
live state.

## Rollback

Rollback is not required because all validation passed.

If later approved, rollback must:

1. confirm public DNS is still absent and the document root is still empty;
2. take another exact pre-rollback snapshot;
3. delete only `fog-flour.jamesjennison.net` through Webuzo's supported
   `domainmanage` end-user API;
4. verify removal of only its generated VirtualHosts, local DNS record,
   placeholder certificate state, and empty root;
5. validate Apache syntax and every unaffected site; and
6. use snapshot `42d911bb` only if supported deletion does not return the exact
   pre-change state.

Estimated rollback execution time is under 15 minutes after approval.

## Remaining approval gates

- Deploy the reviewed static artifact for origin-only staging
- Enroll and validate Webuzo Automatic SSL
- Create the exact Cloudflare DNS record
- Activate public production traffic
- Update the master website from planned to live
- Publish the GitHub Pages transition and later disable Pages
