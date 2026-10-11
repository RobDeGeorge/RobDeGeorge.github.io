# CLAUDE.md

This folder is the **robdegeorge.com** website, deployed with GitHub Pages. It is meant to become the
GitHub *user site* repo **`RobDeGeorge/RobDeGeorge.github.io`**. That exact repo name matters:
once the user site has a custom domain, every project Pages site on the account (for example OCTAVE)
is served under that domain at `/<RepoName>/`.

- `public/` is published as-is by `.github/workflows/deploy.yml`, with no build step. This is the
  same pattern as `@XenoXanadu@`.
- `public/CNAME` is `robdegeorge.com`. `public/.nojekyll` turns off Jekyll.
- `public/index.html` is a placeholder homepage on purpose. Keep it small until there is a real plan
  for it.
- `public/octave/index.html` redirects the lowercase `/octave/` to `/OCTAVE/`.

## Where the OCTAVE wiki comes from (not this repo)

The wiki **source stays in `../_OCTAVE/wiki/`**. OCTAVE's rules require wiki updates in the same
commit as code changes, so do not copy the wiki into this folder. `../_OCTAVE/.github/workflows/wiki-pages.yml`
publishes it as the OCTAVE *project* site, which shows up at `robdegeorge.com/OCTAVE/` once this user
site owns the domain.

## Domains

- `robdegeorge.com` is the canonical address and points to GitHub Pages.
- `www.robdegeorge.com` is a CNAME to `robdegeorge.github.io`. GitHub redirects it to the apex.
- `robertdegeorge.com` (and `www.`) **redirect** to `https://robdegeorge.com` using Squarespace domain
  forwarding (301, keep the path). It is not a second copy of the site, because a Pages site can
  only have one custom domain.
- Both domains were bought at **Squarespace Domains**.

## Launch checklist (status as of 2026-10-11: files staged, nothing pushed)

1. **OCTAVE side** (in `../_OCTAVE`): commit and push `.github/workflows/wiki-pages.yml` and the
   `wiki/development.html` note. Turn on Pages with source "GitHub Actions":
   `gh api -X POST repos/RobDeGeorge/OCTAVE/pages -f build_type=workflow`
   Then run the workflow (`gh workflow run wiki-pages.yml -R RobDeGeorge/OCTAVE`) and check
   `https://robdegeorge.github.io/OCTAVE/`.
2. **This repo**: initial commit, then `gh repo create RobDeGeorge/RobDeGeorge.github.io --public --source . --push`.
   Turn on Pages the same way (`gh api -X POST repos/RobDeGeorge/RobDeGeorge.github.io/pages -f build_type=workflow`).
   Then set the custom domain: `gh api -X PUT repos/RobDeGeorge/RobDeGeorge.github.io/pages -f cname=robdegeorge.com`.
3. **Verify the domain on GitHub first** (this blocks domain takeover): github.com → Settings (account)
   → Pages → Add a domain → `robdegeorge.com`. GitHub shows a TXT record
   (`_github-pages-challenge-RobDeGeorge`) to add in Squarespace. Click Verify after it propagates.
4. **Squarespace DNS for robdegeorge.com** (Domains → robdegeorge.com → DNS):
   - Delete the "Squarespace Defaults" preset records, because they conflict.
   - Add custom records:
     - `@` A → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
     - `@` AAAA → `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`
     - `www` CNAME → `robdegeorge.github.io`
     - the TXT verification record from step 3
5. **Squarespace for robertdegeorge.com**: Domain forwarding → `https://robdegeorge.com`, permanent
   (301), forward the path, and include `www`. Confirm that `https://robertdegeorge.com` (HTTPS, not
   only HTTP) forwards without a certificate warning.
6. Once GitHub has issued the certificate (Settings → Pages shows it, which can take up to about an
   hour after DNS), tick **Enforce HTTPS**:
   `gh api -X PUT repos/RobDeGeorge/RobDeGeorge.github.io/pages -F https_enforced=true`
7. Smoke test: `https://robdegeorge.com/`, `/octave/` → `/OCTAVE/`, `/OCTAVE/settings-ui.html`, wiki
   search, `www.` → apex, `robertdegeorge.com/octave/` → `robdegeorge.com/octave/`.

Update this checklist as steps are completed, and delete it once the site is live.
