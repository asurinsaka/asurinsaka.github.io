---
layout: post
title: Pre-commit
tags: [git, pre-commit]
---

[pre-commit](https://pre-commit.com/) is a tool to check formatting, obvious syntax error, etc before code it committed to git repo. This tool helps us from paying attention to those small errors. <!--break--> 

The tool itself is written in python. To install the tool, do
1. `pip install pre-commit`
2. Go to the root of your repo, run `pre-commit install`

Then you should create a file `.pre-commit-config.yml` in the root of the repo. This file defines the hooks to be run for this repo. 

The first time you add pre-commit, you may want to run `pre-commit run -a` to run the hooks for all files in your repo. Later it will be automatically run on each commit. 

# hooks

## pre-commit-hooks

[pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks) are offical hooks. The config I am using looks like this:

{% highlight yaml %}
---
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.1.0 # last version to support py36
    hooks:
      - id: check-json
      - id: check-toml
      - id: check-yaml
      - id: end-of-file-fixer
        exclude: .(svg|ipynb)$
      - id: fix-encoding-pragma
        args: [--remove]
      - id: no-commit-to-branch
        args: [--branch, develop, --branch, master, --pattern, release/.*]
      - id: pretty-format-json
        args: [--autofix]
      - id: trailing-whitespace
        args: [--markdown-linebreak-ext=md]
{% endhighlight %}

The one I like most in this hook is the `no-merge-to-branch`, save me a many times to reset my develop or master branch. Note, the check-yaml file will have trouble processing `.gitlab-ci.yml`, it's better to exclude it. You can ignore it by adding `exclude: ^\.gitlab-ci.yml`.


## black and isort

{% highlight yaml %}
  - repo: https://github.com/psf/black
    rev: 22.3.0       # this should be the same with plaster dependency
    hooks:
      - id: black
  - repo: https://github.com/pycqa/isort
    rev: 5.10.1
    hooks:
      - id: isort
        name: isort (python)
        args: [--profile, black]
      - id: isort
        name: isort (cython)
        types: [cython]
        args: [--profile, black]
      - id: isort
        name: isort (pyi)
        types: [pyi]
        args: [--profile, black]
{% endhighlight %}

Those 2 hooks are python specific. [Black](https://github.com/psf/black) is a widely used python formatter. It makes the code base much easier to read. 
[isort](https://pycqa.github.io/isort/) sort python imports. Make the imports much organized and everything easier to find. 


## markdown-toc

{% highlight yaml %}
  - repo: https://github.com/qiaouchicago/markdown-toc
    rev: 0.1.0
    hooks:
      - id: markdown-toc
{% endhighlight %}

I have forked [markdown-github-bear-toc](https://github.com/alexander-lee/markdown-github-bear-toc) and created this hook. It will automatically generate table of contents for markdown files. (I probably should use this for my posts one day)


## yaml format and toml sort
{% highlight yaml %}
  - repo: https://github.com/jumanjihouse/pre-commit-hook-yamlfmt
    rev: 0.2.1
    hooks:
      - id: yamlfmt
        args: [--mapping, '2', --sequence, '4', --offset, '2', --width, '80']
  - repo: https://github.com/pappasam/toml-sort
    rev: v0.19.0 # last version to support py36
    hooks:
      - id: toml-sort
        args: [--in-place]
{% endhighlight %}

As their name indicates, those two hook will format and sort yaml and toml file. 


## detect-secrets
{% highlight yaml %}
  - repo: git@github.com:Yelp/detect-secrets
    rev: v1.2.0
    hooks:
      - id: detect-secrets
        args: [--baseline, .secrets.baseline]
{% endhighlight %}

[detect-secrets](https://github.com/Yelp/detect-secrets) helps to make sure you do not accidentally commit your passwords, secret tokens to your repo, causing a secure breach. 

To create a baseline run:

{% highlight bash %}
detect-secrets scan > .secrets.baseline
{% endhighlight %}

To update a baseline file, run:

{% highlight bash %}
detect-secrets scan --update .secrets.baseline
{% endhighlight %}

To audit your baseline file (check whehter any secrete is staged), run: 

{% highlight bash %}
detect-secrets audit .secrets.baseline
{% endhighlight %}

### --exclude-secretes

A useful flag from detect-secretes is `--exclude-secretes`.

Sometimes, you want to be able to ignore certain secret values in your scan. You can specify a regex rule as such:

{% highlight bash %}
$ detect-secrets scan --exclude-secrets '(fakesecret|\${.*})'
{% endhighlight %}

Or you can specify multiple regex rules as such:

{% highlight bash %}
$ detect-secrets scan --exclude-secrets 'fakesecret' --exclude-secrets '\${.*})'
{% endhighlight %}

There are many more hooks yet to discover, hope you enjoy your journy!


