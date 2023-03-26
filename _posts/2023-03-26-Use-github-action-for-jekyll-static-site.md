---
layout: post
title: Use Github Action for Jekyll Static Site
tags: [Github Action, Jekyll, Blog]
---

I used to use github pages for my jekyll blog post. There was a _site folder which contains the generated artifacts of my site. 

I think have the artifacts added to the version control is not a good practice:
1. The artifact is genrated, so I have duplications in my repo.
2. If I change a template, all the pages in the artifacts will be changed. It will be hard for me to track my changes. 

After read about the jekyll document. I decided to use [Jekyll-aciton](https://github.com/helaili/jekyll-action) to generate my site with github action.<!--break--> The genrated files will be pushed to `gh-pages` branch of my repo. And then I have to change the settings of my repo so it will use that branch for the static website:

![gh-pages](/images/jekyll-action/gh-page.png)

The jekyll offical document about jekyll-aciton [here](https://jekyllrb.com/docs/continuous-integration/github-actions/) is slightly outdated, I have to update my `.github/jekyll-action.yml` file with the information from the README of the jekyll-action [repo](https://github.com/helaili/jekyll-action).


Here is the file I am currently using:

{% highlight yaml %}
name: Build and deploy Jekyll site to GitHub Pages

on:
  push:
    branches:
      - master # or master before October 2020

jobs:
  github-pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/cache@v2
        with:
          path: vendor/bundle
          key: ${{ '{{' }} runner.os }}-gems-${{ '{{' }} hashFiles('**/Gemfile') }}
          restore-keys: |
            ${{ runner.os }}-gems-
      - uses: helaili/jekyll-action@v2       # Choose any one of the Jekyll Actions
        with:                                # Some relative inputs of your action
          token: ${{ secrets.GITHUB_TOKEN }}
          target_branch: 'gh-pages'
{% endhighlight %}

The difference between this and the code from the offical website are:
1. This uses v2 version of jekyll-action
2. The target_branch is set to gh-pages

