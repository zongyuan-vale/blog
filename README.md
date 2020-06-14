---
title: 基于 hexo 构建的博客
---

部署的过程参考 hexo 的官方文档[将 Hexo 部署到 GitHub Pages](https://hexo.io/zh-cn/docs/github-pages)，但是参照流程到最后一步修改 GitHub Pages 的部署分支为 gh-pages 时会遇到以下提示:

```bash
User pages must be built from the master branch.
```

以上提示描述为 github 指定 page 只访问 master 上的代码，所以与 hexo 文档描述的规则不符合。

首先 hexo 的规则是源代码在 master，并在根目录配置 Travis CI 自动部署，也就是代码推送到远程 master 时会自动打包代码并将打包文件提交到分支 gh-pages。以这个分支上的作为 page 静态文件。

这个问题的解决方式为分支对调，也就是创建一个分支来保存源代码，指定 master 作为打包文件存放的分支。只需要稍微修改文档上的配置即可:

```bash
# sudo: false 官方已不推荐使用，Travis CI 控制台会报错
language: node_js
node_js:
  - 10 # use nodejs v10 LTS
cache: npm
branches:
  only:
    - blog-page # build blog branch only
script:
  - hexo generate # generate static files
deploy:
  provider: pages
  cleanup: true # Travis CI 控制台会提示你要使用这个，而不是 skip_cleanup
  skip_cleanup: true # 请保留这一行，虽然控制台会报错，但是没有配置会导致无法拷贝打包文件的报错
  github-token: $GH_TOKEN
  keep-history: true
  target_branch: master
  on:
    branch: blog-page
  local-dir: public

```
