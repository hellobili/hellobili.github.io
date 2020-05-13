---
title: gitPages-hexo-travis
date: 2020-05-13 14:00:13
tags:
---

### 前提操作
1.注册github账号
2.创建 `<你的 GitHub 用户名>.github.io` 仓库
3.全局安装 `hexo` `npm install -g hexo-cli`
4.运行 `hexo init <文件名>` , `cd <文件名>`
5.运行 `hexo s` 启动服务，查看是否正常
6.运行 `hexo g` 生成静态文件
7.推送到 `github` 仓库，这里需要配置仓库地址和分支
打开 `_config.yml` 文件，修改
```yml
    deploy:
        type: git
        repo: 你的github地址 （例如：git@github.com:hellobili/hellobili.github.io.git）
        branch: master
```
ps：使用 `<你的 GitHub 用户名>.github.io` 格式命名的仓库，html文件会自动部署到 `master` 上，所以生成的文件应该放在 `master` 分支上
配置好后，需要安装 `hexo-deployer-git` 才能部署到 `github` 上， 运行： `npm install hexo-deployer-git --save`
安装好插件后， 运行： `hexo d` 部署

8.初始化`git`
   a.初始化 `git`： `git init`
   b.关联仓库 `git remote add origin 仓库地址` （origin 为仓库别名 ，可自定义）, 先提交初始化内容 `git add .`, `git commit -m 'init'`
   c.`master` 分支为静态文件分支， 先创建一个源文件分支 (我这里为 `source` ) `git checkout -b source`
   d.推送到仓库 `git push -u origin source`
这里源码已经推送到仓库， 接下来配置自动化部署
9.自动化部署配置 `Travis CI`
    a.注册 [`Travis CI`](https://travis-ci.com/) 账号, 使用 `github` 账号登录
    b.添加需要自动化的仓库,你可以在左上角找到My Repositories一旁的加号“+”，点击它，它就会列出你所有的仓库，你只需要找到刚才的 hexo.github.io 并把它左侧的开关打开就可以了。
    c.新建一个`Token`，前往 `GitHub` 新建 `Personal Access Token`，只勾选 `repo` 的权限并生成一个新的 `Token`。`Token` 生成后请复制并保存好。
    d.回到 `Travis CI`，前往你的 `repository` 的设置页面，在 `Environment Variables` 下新建一个环境变量，`Name` 为 `GH_TOKEN`，`Value` 为刚才你在 `GitHub` 生成的 `Token`。确保 `DISPLAY VALUE IN BUILD LOG` 保持 不被勾选 避免你的 Token 泄漏。点击 `Add` 保存。
    e.在你的 `Hexo` 站点文件夹中新建一个 `.travis.yml` 文件：
```yml
    sudo: false
    language: node_js
    node_js:
    - 10 # use nodejs v10 LTS
    cache: npm
    branches:
    only:
        - source # build source branch only
    before_install: 
    - npm install -g hexo-cli # hexo环境
    - npm install --save hexo-deployer-git # hexo deploy插件
    install:
    - npm install # 安装package.json文件中配置的hexo插件
    # script:
    # - hexo generate # generate static files
    script:
    - chmod +x ./build.sh
    - ./build.sh > /dev/null
```
    我把脚本写在 `build.sh` 文件里面：
```sh
hexo generate #生成静态整站
cd ./public #生成的静态页面会存储在public目录下
git init
git config --global push.default matching
git config --global user.email "你的github邮箱" # 修改为自己的github邮箱
git config --global user.name "你的github用户名" # 修改为自己的github用户名
git add --all .
git commit -m "Travis CI Auto Builder" #自动构建后的内容将全部以此信息提交
git push --quiet --force https://${GH_TOKEN}@github.com/用户名/仓库名.git master  #自动构建后的内容将全部以此信息提交 需要修改用户名
```
创建完成后，把文件推送到仓库 `sources` 分支下，`Travis CI` 会开始开启监测 `source` 分支提交并自动部署

10.测试自动部署是否成功
`hexo new 'test'` 创建一个新的文档
`git add .`, `git commit -m 'test'`
`git push origin source`
`git` 远程提交一次，查看 `Travis CI` 是否开始部署，并部署正常。前往 `https://<你的 GitHub 用户名>.github.io` 查看你的站点是否可以访问。这可能需要一些时间
        
>到此结束