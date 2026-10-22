# internship-website-one — Halcyon Grid

Website One of the internship project **"Deploy Two Websites on One Server Using Apache Virtual Hosts and Jenkins Pipelines"**.

A responsive landing page for *Halcyon Grid*, a **fictional** platform engineering studio. Plain HTML and CSS: no frameworks, build tools or external requests.

| | |
|---|---|
| Repository | https://github.com/smokizn/internship-website-one |
| Branch | `main` |
| Jenkins job | `Deploy-Website-One` (Pipeline script from SCM → `Jenkinsfile`) |
| Served by | Apache HTTPD VirtualHost `*:8091` (container `internship-apache`) |
| Document root | `/var/www/website-one/current` |
| Local URL | http://127.0.0.1:8091/ |

## Files

| File | Purpose |
|---|---|
| `index.html` | Page markup: header/nav, hero, about, services, process, contact, footer |
| `style.css` | All styling, responsive breakpoints at 960px and 760px, CSS-only mobile menu |
| `Jenkinsfile` | Declarative pipeline: Checkout → Validate → Deploy → Verify, with post-build reporting |
| `README.md` | This file |

## Pipeline

1. **Checkout**: `checkout scm`. The job definition owns the repository URL, so it isn't duplicated here.
2. **Validate**: `index.html` and `style.css` must exist and be non-empty, the page must link `style.css`, and HTML Tidy must report no errors.
3. **Deploy**: Packs only `index.html`, `style.css` and a generated `build-info.txt`, then streams them over SSH using the `deploy-website-one` credential. That key is bound by a forced command to `deploy-site website-one`, so it can't write to Website Two. The receiving end stages, validates and atomically swaps the release.
4. **Verify**: Fetches the page from Apache on port 8091 and checks it is byte-identical to the committed `index.html`.
5. **Post**: Success or failure message. On failure the previous release stays live.

New commits on `main` are picked up by SCM polling (every ~2 minutes), or by running **Build Now** in Jenkins.

## Preview locally

```bash
xdg-open index.html
```

No secrets are stored in this repository.
