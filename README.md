# Frank's Blog
This blog based on
 * [hexo](https://hexo.io/docs/) 
 * them [stun](https://theme-stun.github.io/docs/zh-CN/guide/quick-start.html#安装)
 * https://timemachine.icu
## Getting Started
### Branch definition
* hexo: default branch. Create article by this branch.
* master: used for generating files. This is github pages's defined branch. After run helo deploy defined in _config.yaml, will update master branch.
    ```yaml
    deploy:
  - type: git
    repo: git@github.com:shufanhao/shufanhao.github.io.git
    branch: master
    ```
### Clone Repo
```shell script
git clone git@github.com:shufanhao/shufanhao.github.io.git
cd shufanhao.github.io
# Update submodule
git submodule init
git submodule update
```
### Init env
Install hexo and packages
```shell script
npm install hexo-cli -g
npm install --save hexo-renderer-pug
npm install hexo-abbrlink --save
```
Start server:
```shell script
hexo clean && hexo s
```
### Deploy
```shell script
hexo clean && hexo d
```
### New Article
```shell script
hexo new xxx
```
### Todo:
1. update 图床.

