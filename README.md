# itsnick.app

Personal landing page for Nick Harris. One static HTML file, no framework, no build
step, no JavaScript. Hosted on Vercel.

## Layout

| Path | Purpose |
| --- | --- |
| `index.html` | The entire page: markup, inline CSS, and one JSON-LD `@graph` block |
| `robots.txt` | Crawler policy, sitemap and llms.txt pointers |
| `sitemap.xml` | Single URL; `lastmod` is maintained by hand |
| `llms.txt` | Plain-text bio and link set for LLM-native discovery |
| `og-image.png` | 1200x630 social preview card |
| `nick.webp` | 320x320 portrait, the LCP element |
| `*.png`, `favicon.*`, `site.webmanifest` | Icon set |
| `vercel.json` | Security headers and cache policy |
| `.github/workflows/indexnow.yml` | Pings IndexNow when page content changes |

## Local preview

Any static server works, since there is nothing to compile:

```
python -m http.server 3000
```

Then open <http://localhost:3000>. Note that `vercel.json` headers and `cleanUrls`
are not applied by a plain static server; use `vercel dev` to exercise those.

## Deploying

`main` deploys to production on push. To deploy by hand:

```
vercel --prod --scope skylight-studio
```

If a push to `main` does not produce a deployment, check that the Git
connection is still live before debugging anything else:

```
vercel project inspect itsnick.app --scope skylight-studio
```

This repository was transferred between GitHub owners, which silently
invalidated Vercel's stored Git credential: the project still reported the
correct repository and production branch, but no push produced a deployment
for roughly three months. `vercel git disconnect` followed by
`vercel git connect` issues a fresh credential and restores it.

## Editing notes

- **Structured data** lives in a single `@graph` with three nodes (`Person`,
  `WebSite`, `ProfilePage`) cross-referenced by `@id`. Edit the `Person` node
  only; the others point at it. Validate at
  <https://validator.schema.org/> after changes.
- **The bio appears in four places** and they should stay in sync: the `<meta
  name="description">`, the `og:description`, the `Person.description` in
  JSON-LD, and `llms.txt`.
- **Dark mode** is driven entirely by CSS custom properties under
  `@media (prefers-color-scheme: dark)`. Add new colours as tokens on `:root`
  rather than as literals, or they will not theme.
- **`sitemap.xml` `lastmod` is manual.** Bump it when the page content changes.
- **The social card** is generated, not hand-drawn. Regenerate it if the portrait
  or the tagline changes, then bump `lastmod` so IndexNow re-pings.
- **CSP is strict** (`script-src 'self'`). Any inline or third-party script will
  be blocked until `vercel.json` is updated to match.
