---
title: "Deployment mit Gitlab Pages"
linkTitle: "Deployment mit Gitlab Pages"
date: 2023-01-10T21:57:00+01:00
weight: 20
description: >
  Kurze Beschreibung, wie das Deployent mit Gitlab Pages funktioniert (WIP).
---

* Branch `gh-pages` anlegen (https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
* Github Workflow `.github/workflows/gh-pages.yml` anlegen (https://gohugo.io/hosting-and-deployment/hosting-on-github/)
* Docsy spezifische Dinge einbauen (https://github.com/marketplace/actions/hugo-setup#%EF%B8%8F-workflow-for-autoprefixer-and-postcss-cli)
* `npm install` ausfuehren, um `package-lock.json` zu erzeugen
* `.gitignore` anpassen (`package-lock.json` entfernen)
* baseURL = "https://k4cg.github.io/homeassi" in `config.toml` eintragen
