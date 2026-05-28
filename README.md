# Vidai — the AI control plane

[![Latest release](https://img.shields.io/github/v/release/vidaiUK/vidai-quickstart?include_prereleases&sort=semver&label=latest%20release&color=0070F3)](https://github.com/vidaiUK/vidai-quickstart/releases/latest)
[![Signed: cosign + Rekor](https://img.shields.io/badge/signed-cosign%20%2B%20Rekor-0070F3)](https://vidai.uk/cosign)
[![Licence: Commercial](https://img.shields.io/badge/licence-Commercial%20(Community%20free)-64748B)](https://vidai.uk/pricing)
[![Built in Scotland](https://img.shields.io/badge/built%20in-Scotland-64748B)](https://vidai.uk/about)

**Govern, cost and secure every enterprise LLM request, from within your own
network. Nothing leaves.**

Vidai is the AI control plane that sits in your traffic. It sees, controls
and records every request your applications send to AI providers — pricing
each one as it returns, attributing spend to the team, model and project
that incurred it, and enforcing the policies your auditors will ask about
later. Self-hosted. Scotland-based. Built by Vidai UK Limited.

This repository is where you download the production release. The source
for Vidai itself is private; everything you need to install, run and
upgrade is in the release tarballs below.

[**Latest release ↓**](https://github.com/vidaiUK/vidai-quickstart/releases/latest)
&nbsp;·&nbsp;
[Vidai.uk](https://vidai.uk)
&nbsp;·&nbsp;
[Documentation](https://docs.vidai.uk)
&nbsp;·&nbsp;
[Pricing](https://vidai.uk/pricing)
&nbsp;·&nbsp;
[Request a demo](https://vidai.uk/contact)



---

## Proof, not adjectives

- **25 MB** binary — the whole engine that sits in your AI traffic.
- **21,803 RPS** on a single node, verified peak.
- **1.95 ms** median latency under load.
- **0** bytes leave your network boundary.

Full methodology on the [Performance page](https://vidai.uk/performance).

---

## Editions — one bundle, three tiers

The same release tarball runs every edition. Your `vidai.license` file
decides which features unlock at runtime. There is no separate download.

| Edition    | What you get                                                                                                          | How you receive the licence                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Community  | Multi-provider routing with fallback, cost engine, regex guardrails, teams, rate limits, request logs. Free, no expiry. Includes free public rate-card server (best-effort SLA — credentials in the portal alongside your licence). | **Self-serve.** Sign in at [portal.vidai.uk](https://portal.vidai.uk), generate a licence, download `vidai.license`.    |
| Scale      | Everything in Community, plus the full audit-grade ledger, policy-based compliance routing, BI Tables, HMAC webhooks. | **Email.** Start at [vidai.uk/contact](https://vidai.uk/contact). Your `vidai.license` and rate-card server details arrive once licensing is scoped. |
| Enterprise | Everything in Scale, plus VidaiGuard ML guardrails, enterprise identity (SSO / LDAP), priority support and SLA.       | **Email.** Start at [vidai.uk/contact](https://vidai.uk/contact). Your `vidai.license` and rate-card server details arrive once licensing is scoped. |

Commercial use of Community is by approval — your licence acknowledgement
covers that. Full edition feature breakdown:
[vidai.uk/pricing](https://vidai.uk/pricing).

---

## Install — four steps

The release tarball runs as a small set of Docker containers. You bring
the host, Docker, a `vidai.license` file. Vidai brings everything else.

```sh
# 1. Download the latest release tarball and signature
VERSION=0.8.0-beta.4
curl -LO https://github.com/vidaiUK/vidai-quickstart/releases/download/v${VERSION}/vidai-quickstart-${VERSION}.tar.gz
curl -LO https://github.com/vidaiUK/vidai-quickstart/releases/download/v${VERSION}/vidai-quickstart-${VERSION}.tar.gz.bundle

# 2. (Optional but recommended) Verify the signature
curl -o cosign.pub https://vidai.uk/.well-known/cosign.pub
cosign verify-blob --key cosign.pub \
    --bundle vidai-quickstart-${VERSION}.tar.gz.bundle \
    vidai-quickstart-${VERSION}.tar.gz
# → expected output: "Verified OK"

# 3. Extract, drop your licence file, run setup
tar xzf vidai-quickstart-${VERSION}.tar.gz
cd vidai-quickstart-${VERSION}
cp /path/to/your/vidai.license ./
./setup.sh

# 4. Bring the stack up
./vidai start
# → dashboard at http://localhost
```

Default first-boot login: `admin@example.com` / `changeme123`. **Change
this on first sign-in** — `setup.sh` prints the same reminder at the end.

Full installation, upgrade and operational reference:
[the bundled README inside the tarball](https://github.com/vidaiUK/vidai-quickstart/releases/latest)
and the [admin guide on docs.vidai.uk](https://docs.vidai.uk).

---

## After first sign-in

Two things to do before you start sending real traffic through. Both
take a couple of minutes.

**1. Change the admin password.** The default `admin@example.com` /
`changeme123` is documented in this file — anyone who reads the README
knows it. Change it as the first action you take in the dashboard.

**2. Configure the rate-card server.** Vidai prices every request as it
returns, but only if it knows what each model costs. The rate-card
server is the data source. Until it's configured, request logs still
flow, but the cost-attribution columns stay empty.

- **Community** — sign in at [portal.vidai.uk](https://portal.vidai.uk).
  Your free rate-card server URL and API credentials are shown alongside
  your licence download. Paste them into the dashboard:
  *Settings → Rate cards → Configure*. Best-effort SLA — public rate-card
  service, refreshed on a standard cadence, no service guarantee. This is
  enough for cost visibility, budgets, per-team attribution and
  spend-circuit alerts.

- **Scale / Enterprise** — your rate-card server URL and credentials are
  in the same email that delivered your `vidai.license`. Paste them into
  *Settings → Rate cards → Configure*. Enterprise customers can swap in
  their own self-hosted rate-card server later if they prefer.

Once configured, the full cost engine activates: pricing-at-return,
team / model / project attribution, budgets, the works. The
[admin guide](https://docs.vidai.uk) walks through what each surface does.

---

## Verifying what you ran

Every release artefact in this repository is signed by Vidai UK Limited.
The public key is published at https://vidai.uk/.well-known/cosign.pub and
also linked from https://vidai.uk/cosign. Signatures are recorded on the
public Sigstore transparency log (Rekor), so nothing about the signing
can be repudiated after the fact.

Three things you can verify:

1. **The release tarball** — `cosign verify-blob` against the `.bundle`
   sidecar in this release.
2. **The four runtime docker images** — `cosign verify` against
   `ghcr.io/vidaiuk/{server,bff,frontend,docs}:<version>`. The images are
   public and anonymously pullable.
3. **The Server binary inside the Server image** — extracted, then
   `cosign verify-blob` against its embedded `.bundle`.

Step-by-step instructions in the tarball's `VERIFY.md`. Verification is
optional; the product runs identically whether or not you verify.

---

## Architecture, in one line

Your applications keep their existing OpenAI / Anthropic / Bedrock /
Gemini client calls. They point them at Vidai instead of the provider.
Vidai sees the request, applies your policy, prices it as it returns,
records it to your audit trail, and forwards to the provider you
chose — all inside your network boundary. Nothing leaves.

If that one line is intriguing, the
[architecture page](https://vidai.uk/architecture) walks through the rest.

---

## Versions

Releases are listed on the [Releases page](https://github.com/vidaiUK/vidai-quickstart/releases).
Each release tag is `vX.Y.Z`. Pre-release versions
(`-alpha`, `-beta`, `-rc`) are marked accordingly and are not promoted to
the "latest" pointer.

Vidai is a **commercial** product. Free for Community use, contact-led
for Scale and Enterprise. See [vidai.uk/pricing](https://vidai.uk/pricing)
for the full terms and tier comparison.

---

## Support

| Topic                          | Where                          |
| ------------------------------ | ------------------------------ |
| Documentation                  | https://docs.vidai.uk          |
| Bug reports for a release      | Open an issue on this repo     |
| Licensing — Scale / Enterprise | https://vidai.uk/contact       |
| Security disclosures           | security@vidai.uk              |
| General                        | support@vidai.uk               |

---

Vidai is a product of **Vidai UK Limited**, Scotland-based.
Companies House SC863190.
