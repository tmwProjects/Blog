+++
title = 'CREATE AND DEPLOY HACKSIDE WITH HUGO'
date = 2023-12-11
draft = true
author = "tmwProjects"
description = "How HackSide was created and published with Hugo on Github."
tags = [
    "Hugo",
    "Blog",
    "HackSide",
    "Go-Lang"
]
+++

I'm excited to share with you how I created HackSide with the help of Hugo. My adventure began on a rainy day when I 
decided to turn my ideas and technical skills into a unique digital project.

My journey was a fascinating mix of creativity and technical challenge. I came across Hugo and was immediately impressed 
by its simplicity and efficiency. It was like discovering a new tool that was tailored to my exact needs. The beauty of 
Hugo is that it is written in Go, a language known for its speed and power. This made the whole process not only faster, 
but also more enjoyable.

In this post, I'll take you on a little journey through the steps I took to create HackSide. From the first installation 
of Hugo to the initialization of the HackSide project to the final configuration. You'll see how I used commands in Bash, 
how I managed my repository on GitHub and how I finally brought my content to life with Hugo.

[![License](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-003366)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

***

## Contents

* [Installation and setup](#installation-and-setup)
* [Configuration](#configuration)
* [Create posts and build site](#create-posts-and-build-site)
* [Deployment](#deployment)
* [References](#references)
* [License](#license)

***

### Installation and setup

With the following command I download and install Hugo on your Linux system - it's like opening a door to a room full 
of creative possibilities for web projects.

```Bash
sudo apt install hugo
```

This command lays the foundation for the "HackSide" project. It is the first step in bringing the website ideas into a 
structured form.

```Bash
hugo new site Blog
```

Now we switch to the newly created HackSide directory to work on the development of our website.

```Bash
cd Blog
```

Here we initialize a git repository in our project directory. This allows us to effectively track all changes to the project.

```Bash
git init
```

This command connects our Hugo project to a module on GitHub, giving us additional management options and a clear 
project structure.

```Bash
hugo mod init github.com/tmwProjects/Blog
```

Here we add an external Hugo module, the "Risotto" theme, to our project. This gives our website a specific design and layout.

```Bash
hugo mod get github.com/serkodev/holy
```

With this command, we enter the configuration for our selected theme in the Hugo configuration file to determine the 
appearance of our website.

```Bash
echo "theme = 'github.com/serkodev/holy'" >> hugo.toml
```

[Back to content](#contents)

### Configuration

Now we create and edit the configuration files for our GitHub workflows to ensure seamless integration and automation.
There is a direct configuration in the Hugo documentation, which only needs to be copied into the file.

```Bash
mkdir .github/workflows

cd .github/workflows

echo "# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.121.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v3" >> hugo.yaml
```

This command adds the .hugo_build.lock file to our .gitignore so that it is not included in our repository.

```Bash
echo '.hugo_build.lock' >> .gitignore
```

This is where we create our first post for our website, which marks the beginning of our content production.

[Back to content](#contents)

### Create posts and build site

```Bash
hugo new content posts/my-first-post.md
```

We start our local Hugo server to preview our website and check changes in real time.

```Bash
hugo server
```

These commands allow us to view drafts of our website so we can work on unfinished content.

```Bash
hugo server --buildDrafts 
hugo server -D
```

This command gives us detailed information about the build process of our website and helps us identify any issues.

```Bash
hugo -v --debug -D
```

[Back to content](#contents)

### Deployment

This command sequence is essential for our work with Git. We check the status, add changes, save them with a commit and upload everything to GitHub.

```Bash
git status
git add .
git commit -m 'Initial commit'
git status
git push
```

Finally, the source had to be set to "GitHub Actions" for "Building and Deployment" on Github and only the workflow 
"Deploy Hugo site to Pages" had to be started for actions in the repository. Within a few seconds, the process was 
complete and the raw version of HackSide was online. The specific documentation can be found [here](https://gohugo.io/hosting-and-deployment/hosting-on-github/).

[Back to content](#contents)

***

## References

[1] **Git** - Chacon, S., Straub, B., et al. (2023). "Git Documentation: A Comprehensive Guide for Version Control." Git Community. https://git-scm.com/doc

[2] **GitHub** - GitHub, Inc. (2023). "GitHub: A Platform for Version Control and Collaboration." GitHub.

[3] **Mike F. Robbins** - Building and Deploying a Blog with Hugo and GitHub Pages (26.10.2023). https://mikefrobbins.com/2023/10/26/building-and-deploying-a-blog-with-hugo-and-github-pages/

[4] **Hugo** - Hugo is a fast and modern static site generator written in Go, and designed to make website creation fun again. (2023) https://gohugo.io/

[5] **Hugo Documentation** - https://gohugo.io/documentation/

[6] **Risotto** - A minimalist, responsive theme inspired by terminal ricing aesthetics. https://themes.gohugo.io/themes/risotto/

[Back to content](#contents)

***

### License

**CC BY-NC-SA 4.0 Licence**

With this licence, you may use, modify and share the work as long as you credit the original author. However, you may 
not use it for commercial purposes, i.e. you may not make money from it. And if you make changes and share the new work, 
it must be shared under the same conditions.

[Back to content](#contents)
