# AGENTS.md

Rules for changing the Kurafuto website.

## site/

The static website served at **https://getkurafuto.app** via GitHub Pages (`.github/workflows/pages.yml`). Plain HTML and one stylesheet in `site/`; no build step. This repository is public; the app's source lives in the private repository `miquno/kurafuto` (closed source, its ADR 0013).

| File | Purpose |
|---|---|
| `index.html` | Landing page |
| `privacy/index.html` | Privacy policy – the App Store privacy policy URL |
| `datenschutz/index.html` | German translation of the privacy policy |
| `impressum/index.html` | Legal notice (Impressum, § 5 DDG), in German; linked from every page's footer and from Settings in the app |
| `404.html` | Not-found page |
| `styles.css` | Shared style |
| `icon.svg`, `apple-touch-icon.png` | The app icon as site icon: the SVG is generated in the app repository with `swift scripts/make-app-icon.swift icon.svg`, the 192 px PNG from `AppIcon-1024.png` without smoothing |
| `screenshots/` | App screenshots (600 × 1300 JPEG) from the demo mode in the simulator, status bar at 9:41 |
| `CNAME` | Custom domain for GitHub Pages |

## Rules

- `privacy/index.html` and its German translation `datenschutz/index.html` mirror `PRIVACY.md` in the app repository. Change them together with it.
- The controller address in the privacy policy and the address in `impressum/index.html` must stay identical.
- No JavaScript, cookies, analytics, web fonts, CDNs, embeds or images from other hosts. Every page keeps the CSP meta tag (`default-src 'none'`) and `referrer: no-referrer`.
- Light and dark mode via `prefers-color-scheme`; pages must stay readable at 320 px width and 200 % zoom.
- Animations are CSS only and live inside `@media (prefers-reduced-motion: no-preference)`; without them every page must look finished. Japanese ornaments use system Mincho fonts and are `aria-hidden`.
- Adding anything that loads from another origin needs an ADR in the app repository and updates to `PRIVACY.md` and the threat model there.
- Never link the app's private source repository.
- Screenshots show the demo mode only, never a real panel. Give every one alt text and `width`/`height`; all but the hero load lazily.

## Git

- Conventional Commits. Commit identity: `miquno <35491275+miquno@users.noreply.github.com>`. Never commit a private email address.
- Ask before pushing: every push to `main` publishes the site.

## GitHub

- `.github/workflows/pages.yml` deploys `site/` on pushes to `main` that touch `site/`. It needs `pages: write` and `id-token: write` for `actions/deploy-pages`.

## Workflow rules

- Top-level `permissions: {}`; each job requests only `contents: read`. A job that needs more states why in a comment.
- Actions are pinned to full commit SHAs with the version in a comment. Update them deliberately, like any dependency.
- `actions/checkout` always uses `persist-credentials: false`.
- Never use `pull_request_target` or `workflow_run` with untrusted code, and never interpolate `${{ github.event.* }}` text into `run:` scripts – pass it through `env:`.
- No Dependabot, Renovate or other automated update bots.

## Settings (to apply on GitHub)

- Pages: source "GitHub Actions", custom domain `getkurafuto.app`, "Enforce HTTPS" on. DNS at the registrar: `A`/`AAAA` records for the apex pointing to GitHub Pages, and verify the domain under the `miquno` account's Pages settings (TXT record `_github-pages-challenge-miquno`). The domain is verified for the account (protects it from takeover; a domain verified for an organization only works for that organization's repositories); the certificate covers `getkurafuto.app` and `www.getkurafuto.app`, but only the apex has DNS records.

