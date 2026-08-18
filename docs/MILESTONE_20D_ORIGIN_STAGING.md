# Milestone 20D — Webuzo origin-only staging

Status: **Complete; superseded by the production activation recorded in Milestone 20E**

Date: July 23, 2026

Source commit: `35ca85f95c907e1b502a7320f1ee7d9c340672bd`

## Model and reasoning checkpoint

| Field                     | Decision                                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------------------ |
| Current model             | GPT-5.6 Sol                                                                                            |
| Current reasoning         | Medium                                                                                                 |
| Required reasoning        | Medium                                                                                                 |
| Model or reasoning change | None                                                                                                   |
| Why                       | The deployment used a deterministic static artifact and the already validated isolated Webuzo pattern. |
| Expected usage impact     | Moderate                                                                                               |

## Approved scope

- Rebuild and validate the reviewed 14-file static artifact.
- Take a fresh restore-tested pre-deployment backup.
- Transfer the artifact to a non-public candidate directory.
- Verify inventory, ownership, permissions, and sensitive-file exclusions.
- Atomically promote it into the approved Webuzo document root.
- Validate the origin, canonical-host isolation, unaffected sites, and
  rollback.
- Take and restore-test a post-deployment backup.

This milestone did not create public DNS, modify Cloudflare, enroll Automatic
SSL, push Git, transition GitHub Pages, change the master website, add a
process, add a scheduled task, or edit a Webuzo-generated VirtualHost.

## Artifact

| Field                    | Value                                                              |
| ------------------------ | ------------------------------------------------------------------ |
| Build command            | `npm run validate`                                                 |
| Output                   | `dist/`                                                            |
| Files                    | 14                                                                 |
| Size                     | 264.1 KiB                                                          |
| Portable manifest digest | `509b09d1eaa87a072550e758c8332f692b9adc74e455a3f117aff2a3794d875f` |
| Symlinks                 | None                                                               |
| Runtime                  | None                                                               |
| Database                 | None                                                               |
| Background process       | None                                                               |

The local and remote manifests matched. The candidate contained no `.git`,
`.env`, private key, PEM, source map, dependency, log, or private documentation
files.

## Pre-deployment protection

The Webuzo mapping, empty document root, ownership, Apache syntax, services,
backup timers, and absent public DNS were revalidated immediately before the
transfer.

Restic snapshot `5c22fb2e` records the exact empty-root pre-deployment state. A
10 percent repository data check passed. Four streamed restores and the empty
root inventory matched the live state.

## Transfer and promotion

The artifact was transferred into a unique candidate directory outside every
public document root. Candidate directories used `0755`, files used `0644`,
artifact entries were owned by `jamesjen:jamesjen`, and the candidate root was
prepared as `jamesjen:nobody` mode `0750`.

Activation used a same-filesystem directory swap. The retained prior root is:

`/home/jamesjen/.fog-flour-previous-m20d-20260723T130551Z`

It remains empty and retains the exact pre-deployment Webuzo root state.

## Validation-harness rollback record

The first activation attempt encountered a harness-control defect after a
validation error: rollback correctly restored the empty root, but the error
handler disabled shell exit handling and subsequently printed a false success
line. The deployment was correctly treated as failed.

The corrected harness added:

- an explicit validation-step marker;
- a non-returning error handler;
- exact rollback confirmation; and
- no success output until every check completed.

The second controlled attempt identified the substantive expectation mismatch:
Webuzo's dormant `www.fog-flour` alias returns HTTP 403 through the artifact's
canonical-host policy, while its dormant `mail.fog-flour` alias returns HTTP
404 before exposing project content. Both behaviors safely prevent duplicate
site content. The corrected expectation was verified in a diagnostic swap,
which also restored the empty root afterward.

The final promotion then passed every check. The two earlier artifact roots
were reused through the controlled retries; no candidate or failed-release
directory was abandoned.

## Origin validation

- Webuzo still reports the exact approved document root.
- The deployed root is `jamesjen:nobody` mode `0750`.
- The root contains exactly 14 regular deployment files and no symlinks.
- The remote manifest matches the reviewed local artifact.
- Apache syntax passes.
- HTTP returns 308 and preserves the request path and query string.
- Placeholder HTTPS returns HTTP 200 for the homepage when certificate trust
  is intentionally bypassed for origin staging.
- Canonical metadata uses only `https://fog-flour.jamesjennison.net/`.
- The roadmap is present in static HTML.
- Robots, sitemap, manifest, and social preview assets return HTTP 200.
- The custom error page returns HTTP 404 with project-specific content.
- CSP, HSTS, referrer, content-type, frame, permissions, opener, resource, and
  no-transform cache headers are present.
- `www.fog-flour` returns HTTP 403.
- `mail.fog-flour` returns HTTP 404.
- Public A, AAAA, and CNAME records remain absent.
- Webuzo, MariaDB, backup, and maintenance services remain healthy.
- The master apex, canonical `www` redirect, Player, QuireForge, webmail,
  autoconfiguration, and autodiscovery retain their expected responses.

The Webuzo-generated self-signed certificate was staging-only. Milestone 20E
replaced it with dedicated Webuzo Automatic SSL coverage and activated the
approved proxied Cloudflare record.

Chromium correctly refused a direct-origin Lighthouse run because the
placeholder is self-signed and has no Subject Alternative Name. The audit was
not bypassed further and no origin score is claimed. The byte-identical local
artifact retains the 100/100/100/100 mobile and desktop Lighthouse results
recorded in Milestone 20B. Origin and edge browser audits are deferred until
Automatic SSL and public activation are approved.

## Post-deployment protection

Restic snapshot `9f1cab04` records the deployed artifact, retained empty prior
root, Webuzo state, generated Apache configuration, database export, ACME
state, and certificate state. A 10 percent repository data check passed. Five
streamed restores and the retained-root inventory matched the live state.

## Rollback

Content rollback is immediately available without deleting the Webuzo domain:

1. verify public DNS is still absent;
2. move the deployed root to a uniquely named failed-release directory;
3. atomically restore
   `/home/jamesjen/.fog-flour-previous-m20d-20260723T130551Z`;
4. validate Apache syntax, the expected empty-root HTTP 403 response, and all
   unaffected sites; and
5. retain the failed release for diagnosis.

Snapshot `5c22fb2e` is the secondary pre-deployment recovery point. Estimated
rollback execution time is under five minutes after approval.

## Subsequent milestone

Milestone 20E completed Automatic SSL enrollment, the exact Cloudflare DNS
record, and public production validation. The remaining gates are the
master-site live-state update, the GitHub Pages transition, and the separately
identified Cloudflare managed-robots SEO decision.
