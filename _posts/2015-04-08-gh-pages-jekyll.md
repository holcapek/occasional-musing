---
layout: post
title: GitHub Pages & Jekyll
tags: github rvm ruby gem jekyll
---
[back]({{ site.baseurl }})
# {{ page.title }}

-   Create github repository
-   Create `gh-pages` branch
-   Install [Ruby Version Manager](https://rvm.io/)
-   Install reasonably recent ruby

    ```
    rvm list known
    rvm install ruby-2.1.4
    ```

-   Install `bundler` gem

    ```
    gem install bundler
    ```

-   Create `Gemfile` as specified [here](https://help.github.com/articles/using-jekyll-with-pages/#installing-jekyll)

    ```
    source 'https://rubygems.org'
    gem 'github-pages'
    ```

-   Install `github-pages` gem as per `Gemfile`

    ```
    bundle install
    ```

-   Customize `_config.yml`

    ```yaml
    # redcarpet renders code blocks correctly 
    markdown: redcarpet
    ```

