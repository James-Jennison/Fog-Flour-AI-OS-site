# Webuzo deployment

Status: **Live in production with proxied DNS and dedicated Automatic SSL**

## Production contract

- Canonical hostname: `https://fog-flour.jamesjennison.net`
- Source repository: `James-Jennison/Fog-Flour-AI-OS-site`
- Webuzo user: `jamesjen`
- Confirmed document root: `/home/jamesjen/fog-flour.jamesjennison.net`
- Output type: static files from `dist/`
- Runtime, process manager, reverse proxy, scheduled task, and database: none
- Edge: Cloudflare proxied DNS with Full (Strict) TLS
- Origin certificate: Webuzo Automatic SSL

## Verified production state

- Webuzo owns the exact hostname and document root.
- The reviewed 14-file static artifact is deployed from commit `35ca85f`.
- Portable manifest:
  `509b09d1eaa87a072550e758c8332f692b9adc74e455a3f117aff2a3794d875f`.
- The empty prior root is retained at
  `/home/jamesjen/.fog-flour-previous-m20d-20260723T130551Z`.
- Origin-staging snapshots: `5c22fb2e` and `9f1cab04`.
- Production pre-activation snapshot: `d159e6fd`.
- Known-good production snapshot: `ffd9ed8e`.
- One Cloudflare-proxied A record serves the canonical hostname.
- Public DNS returns Cloudflare edge addresses rather than the origin.
- Webuzo Automatic SSL owns a dedicated Let's Encrypt certificate for only
  `fog-flour.jamesjennison.net`.
- Automatic renewal metadata and the existing Webuzo renewal schedule are
  present.

Webuzo is authoritative for the exact document root. Milestone 20C confirmed
the domain mapping, `jamesjen:nobody` ownership, `0750` root mode, empty
contents, generated VirtualHosts, and valid Apache syntax. Milestone 20D
deployed the reviewed artifact without public DNS. Milestone 20E activated
production DNS and replaced the placeholder with trusted Automatic SSL
coverage.

## Build contract

```bash
npm run validate
```

Only the resulting `dist/` directory is eligible for deployment. The build uses
an explicit public-file allowlist and pre-renders the owner-curated roadmap.
Source, Git metadata, credentials, private documentation, dependency
directories, logs, and source maps must never enter the document root.

## Promotion contract

Every deployment requires separate owner approval and must:

1. revalidate the exact Webuzo-managed domain, user, and document root;
2. confirm Automatic SSL and Cloudflare Full (Strict) readiness;
3. capture and restore-test the current Webuzo, certificate, ACME, and
   document-root state;
4. rebuild and validate the exact approved source commit;
5. stage the artifact in an isolated candidate path;
6. preserve ownership, permissions, and Webuzo-managed ACME paths;
7. validate Apache syntax before promotion;
8. retain the prior release and arm automatic rollback;
9. verify origin and edge routes, TLS, redirects, headers, metadata, the custom
   404, master/status navigation, and unaffected sites; and
10. capture and restore-test the production state.

Milestone 20D completed these steps for origin-only staging and records two
successful automatic rollback exercises before final promotion. Milestone 20E
then completed production DNS and Automatic SSL activation with exact
single-record DNS rollback.

No generated virtual host, global server configuration, shared service, mail
service, or unrelated domain may be changed.

## GitHub Pages transition

Production validation has passed. Publishing a lightweight `noindex`
transition page at the former GitHub Pages URL for 30 days remains a separate
approval gate. It should link to the new hostname and declare the new URL as
canonical. Disabling Pages, changing repository homepage metadata, and
publishing the transition page each remain separate GitHub approval gates.

## Rollback

Delete only the exact canonical Cloudflare record to withdraw public traffic.
For content rollback, atomically restore the retained prior document root and
rerun the complete origin, edge, mail-boundary, and unaffected-site validation
matrix. Restic snapshot `d159e6fd` is the pre-activation recovery point and
`ffd9ed8e` is the known-good production recovery point.
