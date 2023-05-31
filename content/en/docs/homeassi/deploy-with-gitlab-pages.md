---
title: "Deployment mit Gitlab Pages"
linkTitle: "Deployment mit Gitlab Pages"
date: 2023-01-10T21:57:00+01:00
weight: 20
description: >
  Kurze Beschreibung, wie das Deployent mit Gitlab Pages funktioniert (WIP).
---

## Schritte fuer Deployment mit Github Actions auf Github Pages

* Branch `gh-pages` anlegen (https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
* Github Workflow `.github/workflows/gh-pages.yml` anlegen (https://gohugo.io/hosting-and-deployment/hosting-on-github/)
* Docsy spezifische Dinge einbauen (https://github.com/marketplace/actions/hugo-setup#%EF%B8%8F-workflow-for-autoprefixer-and-postcss-cli)
* `npm install` ausfuehren, um `package-lock.json` zu erzeugen
* `.gitignore` anpassen (`package-lock.json` entfernen)
* baseURL = "https://k4cg.github.io/homeassi" in `config.toml` eintragen

## Fuckup mit Links auf Dateien im /static Ordner

tl;dr: im Root Verzeichnis gibt es den Ordner `static`. Auf die darin abgelegten Dateien kann mit `/filename.drawio` oder `/foldername/filename.png` verlinkt werden. Das wird dann zu <https://name.tld/> bereitgestellt (z.B. <https://name.tld/filename.drawio>). Siehe [Hugo Doku](https://gohugo.io/content-management/static-files/)
Falls die `baseURL` in der `config.toml` aber noch einen "Unterordner" hat (z.B. <https://k4cg.github.io/homeassi>), funktioniert das nicht mehr. Die relativen Links im Markdown werden in der erzeugten Webseite statt zu <https://k4cg.github.io/homeassi/filename.drawio> zu <https://k4cg.github.io/filename.drawio> umgewandelt und sind kaputt.
Moegliche Loesung: Verwendung von [Page Bundles](https://gohugo.io/content-management/page-bundles/) statt Dateien im Ordner `static` abzulegen.

Linksammlung zum Thema:

* [When using a baseURL with path, links to static resources don't include path](https://github.com/gohugoio/hugo/issues/8078)
* [GitHub Actions Deployment and BaseURL?](https://discourse.gohugo.io/t/github-actions-deployment-and-baseurl/40570)
* [Publication on GitHub pages and BaseURL](https://discourse.gohugo.io/t/publication-on-github-pages-and-baseurl/43631)
* [How to handle URLs when in a subfolder?](https://discourse.gohugo.io/t/how-to-handle-urls-when-in-a-subfolder/42260/4?u=jmooring)
* [Clearly document how to compute URLs that are baseURL aware...](https://github.com/gohugoio/hugo/issues/10606)
* [Figure shortcode cannot find image in static folder in site.com/subsite baseURL](https://discourse.gohugo.io/t/figure-shortcode-cannot-find-image-in-static-folder-in-site-com-subsite-baseurl/33357)
* [Docsy theme not rendering using GitHub Pages](https://discourse.gohugo.io/t/docsy-theme-not-rendering-using-github-pages/28109/2)
