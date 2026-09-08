# BlueScale Security — marketing site (static drafts)

Draft static site for **bluescalesecurity.com**. Audience: oil & gas, agriculture, and West Texas SMBs. Tone: credible, plain-spoken, defensive only. No offensive content, fake logos, invented metrics, or client testimonials.

## Stack recommendation

**Now:** plain static HTML + one shared `styles.css` + minimal `js/main.js` (mobile nav only). No build step.

**Hosting (chosen):** [GitHub Pages](https://docs.github.com/en/pages).

**Why not Next/Astro yet**

- Host cheaply on GitHub Pages (or Cloudflare Pages / Netlify later if needed).
- Tools scorecard runs **offline / backend-gated** — not live in the public browser.
- Justin can edit HTML/CSS directly.

**Graduate later**

- Move to **Astro** only if we need components/islands for Tools embeds or shared layouts that become painful to maintain by hand.
- Avoid a full **Next.js** app unless real app features appear (auth, dashboards, server APIs). Marketing alone does not need it.

## Repo layout for GitHub Pages (preferred)

Treat **this `web/` folder’s contents as the Git repo root** (not a monorepo with `web/` as a subfolder).

```text
(repo root = these files)
  index.html
  styles.css
  contact.html
  CNAME                 # bluescalesecurity.com
  .nojekyll             # keep underscore/dot paths; skip Jekyll
  README.md
  js/
  services/
  tools/
```

That way Pages → **Deploy from a branch** → `main` → **/ (root)** works with **zero GitHub Actions**.

Do **not** create the GitHub repo from this box until CoS coordinates `gh` auth with Justin.

### Monorepo alternative (avoid unless needed)

If the site must live under `web/` inside a larger repo, either:

- publish from `/docs` after copying build output there, or
- use a tiny Actions workflow that publishes `web/` — more moving parts; prefer root-as-`web/` contents.

## Deploy steps (exact)

1. CoS/Justin: create a GitHub repo (public, or private with Pages enabled on a paid plan). **Do not invent the repo name here.**
2. Push **these files** (contents of `web/`) to `main` at the **repository root**.
3. Repo → **Settings** → **Pages**:
   - Source: **Deploy from a branch**
   - Branch: `main` / folder: **/ (root)**
4. Under **Custom domain**, enter `bluescalesecurity.com` and Save.
   - GitHub may rewrite/confirm the `CNAME` file (already present in this tree).
5. Wait for DNS + TLS (see below). Optionally enable **Enforce HTTPS** once the certificate is ready (can take up to ~24h).

### Local preview

```bash
cd /path/to/web   # or repo root after push layout
python3 -m http.server 8080
```

Open `http://localhost:8080/`.

## Custom domain DNS (registrar)

Official guide: [Managing a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).

**Order matters:** add the custom domain in the repo Pages settings **before** (or carefully alongside) DNS changes, and prefer [verifying the domain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages) to reduce takeover risk. DNS can take up to 24 hours.

### Apex: `bluescalesecurity.com`

Create **A** records (and **AAAA** for IPv6) pointing `@` at GitHub Pages’ published addresses (from GitHub’s docs — re-check the link above if anything drifts):

| Type | Name | Value |
|------|------|-------|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| AAAA | `@` | `2606:50c0:8000::153` |
| AAAA | `@` | `2606:50c0:8001::153` |
| AAAA | `@` | `2606:50c0:8002::153` |
| AAAA | `@` | `2606:50c0:8003::153` |

Some providers support `ALIAS`/`ANAME` to `USERNAME.github.io` or `ORG.github.io` instead of A/AAAA.

### www: `www.bluescalesecurity.com`

Recommended alongside the apex. Create a **CNAME**:

| Type | Name | Value |
|------|------|-------|
| CNAME | `www` | `USERNAME.github.io` **or** `ORG.github.io` |

Point `www` at the account’s **`.github.io` host**, not at a `username.github.io/repo` path. Replace `USERNAME`/`ORG` with Justin’s GitHub user or org once the repo exists.

With both apex and `www` configured, GitHub Pages can redirect between them automatically.

### Verify

```bash
dig bluescalesecurity.com +noall +answer -t A
dig www.bluescalesecurity.com +nostats +nocomments +nocmd
```

Do **not** use wildcard DNS (`*.bluescalesecurity.com`) — GitHub warns this enables subdomain takeover risk.

## Relative links

All asset and nav links are **relative** (`styles.css`, `services/…`, `../contact.html`, etc.). They work when this tree is the site root on Pages (custom domain + publish from `/`). Prefer that over a project-site subpath.

## Scorecard / `#scorecard-embed` (v1)

CLI tool lives at `/workspace/bluescale/tools/posture-scorecard/` (not in this Pages tree).

**Hard rule:** never run live checks from the public browser. Prospect submits an owned domain → backend auth gate → `python scorecard.py … --i-am-authorized` → serve JSON/HTML artifacts.

Until hosting has a backend, the public page is a **“Request a scan”** CTA (contact form) — not an iframe live scanner. `#scorecard-embed` remains a reserved empty slot for a later offline/handout display if needed.

## File map

```text
web/
  README.md
  CNAME
  .nojekyll
  index.html
  styles.css
  contact.html
  js/main.js
  services/
    index.html
    assessment.html
    hardening.html
    ransomware-readiness.html
    field-site-hygiene.html
    networking.html
    backups.html
    business-continuity.html
    identity-access.html
  tools/
    index.html
    posture-scorecard.html   # request-a-scan CTA + #scorecard-embed slot
```

## Hard rules (content)

- Defensive cybersecurity only — assessments, hardening, ransomware readiness, OT/IT hygiene, networking, backups, BCP, identity & access.
- Zero offensive / exploit content or links.
- No fake client logos, metrics, or case studies.

## Open questions for CoS / Lead

1. **Form backend** — Formspree, Cloudflare Workers, Netlify Forms, or something else? Replace `mailto:` when chosen. (GH Pages has no server forms.)
2. **GitHub repo** — live at `BlueScaleSecurity/bluescalesecurity.com` (Pages from `main` `/`).
3. **Brand colors / logo** — Current palette is provisional blue/slate.
4. **Legal entity name for footer** — Exact DBA / LLC string.
5. **Contact email** — ~~placeholder~~ now `justin@bluescalesecurity.com` (confirmed).
6. **Scorecard backend later** — who hosts the authorized CLI runner after Pages v1.
