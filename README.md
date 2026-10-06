# AIIM MCP interop — client metadata documents

Client ID Metadata Documents (CIMD) for Curity's participation in the
[OpenID AIIM CG](https://openid.net/) MCP Security Interoperability Event,
results to be presented at Gartner IAM Summit, December 2026.

In CIMD, a client's `client_id` *is* the HTTPS URL of its metadata document.
An authorization server dereferences that URL at authorization time instead of
relying on prior registration. These documents are therefore published rather
than configured, and each one's `client_id` must equal the URL it is served
from — a mismatch is rejected.

Draft: [`draft-ietf-oauth-client-id-metadata-document`](https://datatracker.ietf.org/doc/draft-ietf-oauth-client-id-metadata-document/)

## The documents

Served from GitHub Pages at `https://iggbom.github.io/aiim-interop/`.

| File | Client authentication | Keys | Exercises |
|---|---|---|---|
| `cimd-inspector.json` | `none` | — | Public client with PKCE, driven by the MCP Inspector |
| `cimd-jwt.json` | `private_key_jwt` | `jwks_uri` | Key retrieval from a separate JWKS document |
| `cimd-jwt-inline.json` | `private_key_jwt` | inline `jwks` | Key resolved from the document itself, no second fetch |
| `cimd-cc.json` | `private_key_jwt` | `jwks_uri` | Client credentials, no user, no redirect |
| `client-jwks.json` | — | — | The JWKS referenced by `jwks_uri` above |

No document uses `client_secret_basic` or `client_secret_post`. The CIMD draft
prohibits shared symmetric secret methods for these clients, since the document
is public and a secret in it would not be one.

## The negative case

`cimd-notallowed.json` is a valid document that an authorization server should
**refuse**, because it is served from an origin outside the allowlist. It is
referenced by its `raw.githubusercontent.com` URL rather than its Pages URL:

    https://raw.githubusercontent.com/iggbom/aiim-interop/main/cimd-notallowed.json

That distinction is the whole point. Allowlists are matched per domain, and
GitHub Pages gives every user the same `github.io` domain — so allowlisting
`iggbom.github.io` to permit the documents above would also permit anything
else published on Pages by anyone. The denial document has to live on a
genuinely different domain for a refusal to prove enforcement rather than a
broken fetch.

## Keys

All documents share one RSA key, published as `kid`
`3GrF6y-weAJtK8YBDfze84FrVDRjl2dUStn7EWdDaRs`. The private half is not in this
repository and must not be.

The documents are generated rather than hand-edited, so the `client_id`, the
inline `jwks` and `client-jwks.json` cannot drift apart.
