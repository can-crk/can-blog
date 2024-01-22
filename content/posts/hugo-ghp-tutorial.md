+++
title = 'Tutorial: Hugo und GitHub Pages'
date = 2024-01-22T11:02:52+01:00
draft = false
+++

1. Hugo installieren
```bash
# Das dnf-Repository hält zZt. nur eine sehr alte Verison von Hugo bereit

curl # aktuelle version von der Seite einfügen: https://github.com/gohugoio/hugo/releases 

tar zxfv hugo....tar.gz
mv hugo /usr/bin

#check
hugo version
```

2. Neue Hugo-Seite erstellen
```bash
hugo new site it-kongress-demo --format yml

cd it-kongress-demo
```

3. Den ersten Blog-Post erstellen
```bash
hugo new docs/test.md

nano content/docs/test.md

# draft auf false setzen und etwas schreiben
```

4. Git und Theme
```bash
git init

# Ein Theme aussuchen (https://themes.gohugo.io/) und die Schritte zur installation befolgen
# Nachfolgend wird PaperMod exemplarisch gezeigt

git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

echo "theme: PaperMod" >> hugo.yml

# Zum testen
hugo server

# Neues Repository auf GitHub erstellen mit dem namen der hugo seite
# wenn es die default gh-page sein soll {username}.github.io

# Branch "gh-pages" erstellen

# Settings > Actions > General > Read and Write Permissions


echo "# it-kongress-demo" >> README.md
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:can-crk/it-kongress-demo.git
git push -u origin main
```

5. GitHub Workflow einrichten
```bash
mkdir -p .github/workflows

nano .github/workflows/deploy.yml

```
In die Datei kommt das:
```bash
name: Publish to GH Pages
on:
  push:
    branches:
      - main
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout source
        uses: actions/checkout@v3
        with:
          submodules: true

      - name: Checkout destination
        uses: actions/checkout@v3
        if: github.ref == 'refs/heads/main'
        with:
          ref: gh-pages
          path: built-site

      - name: Setup Hugo
        run: |
          curl -L -o /tmp/hugo.tar.gz 'https://github.com/gohugoio/hugo/releases/download/v0.121.2/hugo_0.121.2_linux-amd64.tar.gz'
          tar -C ${RUNNER_TEMP} -zxvf /tmp/hugo.tar.gz hugo          
      - name: Build
        run: ${RUNNER_TEMP}/hugo

      - name: Deploy
        if: github.ref == 'refs/heads/main'
        run: |
          cp -R public/* ${GITHUB_WORKSPACE}/built-site/
          cd ${GITHUB_WORKSPACE}/built-site
          git add .
          git config user.name 'can-crk'
          git config user.email 'canc2211@gmail.com'
          git commit -m 'Updated site'
          git push   
```



6. Auf GitHub pushen
```bash
git add .
git commit -m "first commit"
git push -u origin main


```


7. DNS-Einträge anlegen
https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site

```bash
nano hugo.yml

#baseurl auf die eigene domain setzen
