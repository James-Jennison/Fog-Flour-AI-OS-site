# Webuzo deployment

Status: **Reviewed artifact deployed for origin-only staging; public DNS and trusted TLS are not active**

## Production contract

- Canonical hostname: `https://fog-flour.jamesjennison.net`
- Source repository: `James-Jennison/Fog-Flour-AI-OS-site`
- Webuzo user: `jamesjen`
- Confirmed document root: `/home/jamesjen/fog-flour.jamesjennison.net`
- Output type: static files from `dist/`
- Runtime, process manager, reverse proxy, scheduled task, and database: none
- Edge: Cloudflare proxied DNS with Full (Strict) TLS
- Origin certificate: Webuzo Automatic SSL

## Verified origin-staging state

- Webuzo owns the exact hostname and document root.
- The reviewed 14-file static artifact is deployed from commit `35ca85f`.
- Portable manifest:
  `509b09d1eaa87a072550e758c8332f692b9adc74e455a3f117aff2a3794d875f`.
- The empty prior root is retained at
  `/home/jamesjen/.fog-flour-previous-m20d-20260723T130551Z`.
- Pre-deployment snapshot: `5c22fb2e`.
- Post-deployment snapshot: `9f1cab04`.
- Public DNS remains absent.
- The Webuzo-generated self-signed placeholder remains unsuitable for
  production trust.

Webuzo is authoritative for the exact document root. Milestone 20C confirmed
the domain mapping, `jamesjen:nobody` ownership, `0750` root mode, empty
contents, generated VirtualHosts, and valid Apache syntax. Public DNS remains
absent.

Webuzo generated a self-signed placeholder during domain creation even though
certificate issuance was disabled. It is not production trust coverage.
Automatic SSL enrollment remains a separate approval gate.

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
3. capture and restore-test the current Webuzo and document-root state;
4. rebuild and validate the exact approved source commit;
5. stage the artifact in an isolated candidate path;
6. preserve ownership, permissions, and Webuzo-managed ACME paths;
7. validate Apache syntax before promotion;
8. retain the prior release and arm automatic rollback;
9. verify origin and edge routes, TLS, redirects, headers, metadata, the custom
   404, master/status navigation, and unaffected sites; and
10. capture and restore-test the production state.

Milestone 20D completed these steps for origin-only staging and records two
successful automatic rollback exercises before final promotion. Neither
exercise affected public traffic because the hostname has no public DNS.

No generated virtual host, global server configuration, shared service, mail
service, or unrelated domain may be changed.

## GitHub Pages transition

After the Webuzo deployment passes production validation, publish a lightweight
`noindex` transition page at the former GitHub Pages URL for 30 days. It should
link to the new hostname and declare the new URL as canonical. Disabling Pages,
changing repository homepage metadata, and publishing the transition page each
remain separate GitHub approval gates.

## Rollback

Atomically restore the retained prior document root and rerun the complete
origin, edge, mail-boundary, and unaffected-site validation matrix. The
pre-deployment Restic snapshot is the secondary recovery path.
