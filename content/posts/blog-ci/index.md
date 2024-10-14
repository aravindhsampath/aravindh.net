+++
author = "Aravindh Sampathkumar"
categories = ["Computers"]
date = 2024-01-15T12:00:00Z
summary = "CI setup of my personal website using Github Actions"
tags = ["website", "hugo", "performance"]
title = "Poor mans CICD for personal website"
type = "post"
images = ["img/lcicd.jpg"]
draft=true
+++

## Goal
Have a seamless way to write blog posts on my Mac and have the website transparently build and deploy upon saving the file locally.

## How?
![cicd-workflow](cicd-workflow.excalidraw.svg)


## Noteshub.app

Finally!
An easy to use (local) Markdown editor that directly works with Github repository, as in transparently saves your files as a commit to the repository upon saving. Bonus - It has excalidraw built-in for making scgs like above.

[Noteshub](https://about.noteshub.app/)

![image](noteshub-screenshot.png)

## Github Actions
Here is a simple Github Actions workflow that does the following:
1. Clone the contents of the repository
2. Install hugo
3. Build the static website using hugo
4. Commit the newly built website to the same github repo 

```
name: Build and Deploy Hugo Website

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Install dependencies
        run: |
          sudo apt-get update && \
          sudo apt-get install -y hugo

      - name: Build website
        env:
          HUGO_ENV: production
        run: |
          hugo --minify

      - name: Commit and push changes
        uses: devops-infra/action-commit-push@master
        with:
          github_token: ${{ secrets.COMMIT_TOKEN }}
          commit_message: hugo_build_by_Actions
```
