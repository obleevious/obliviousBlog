---
title: Set up a Hexo Blog with GitHub Pages
date: 2019-05-07 11:41:45
tags: coding
---

Hexo is a open source static blog framework. Here static makes it possible to host your blog on GitHub Pages!

Fancy? Let's start:

## Install Hexo:

Follow instructions from Hexo official website: [https://hexo.io/docs/](https://hexo.io/docs/)

## Init a Hexo blog instance:

Create a new folder for your blog project:

```
mkdir myBlog
cd myBlog
hexo init
```

## Make it an GitHub repo:

Create a new repo on GitHub, clone it to your local machine:

```
cd ..
git clone git@github.com:<UserName>/<RepoName>
mv myBlog/* RepoName/
rm -r myBlog
```

**This is not a GitHub Pages repo**

## Create your GitHub Pages repo:

Follow instructions from the following link: [https://pages.github.com/](https://pages.github.com/)

Whit this you can now fire up a browser and go to https://username.github.io

That is where our static blog will be hosted

## Config Hexo to deploy your blog with a simple command

Hexo allow you to deploy static pages generated to server with a simple command, to setup see [https://hexo.io/docs/deployment](https://hexo.io/docs/deployment)

`hexo generate` or `hexo g` for short will generate your static blog under `myBlog/public` folder.

`hexo deploy` or `hexo d` will deploy that public folder to your destiny repo

Refresh your GitHub Page, Cong!
