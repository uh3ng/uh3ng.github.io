---
title: Hexo搭建可以随处迁移的博客
date: 2018-04-16 20:33:41
tags:
---
**踩了无数的坑，终于弄成了**

## 大体思路
之前弄的博客如果换了电脑或操作系统就很难更新文章，有两个思路可以解决这个问题，一个是在GitHub上面另外建一个仓库，把整个博客的文件夹给push上去，换了电脑后只要有git就能clone进行修改和更新，然后再部署。

还有一种方案是用分支来解决问题，即用现在这个仓库，另外开一个分支出来如：hexo来存放你的站点配置文件等等，另一个分支master依然保存着编译后的静态页面文件。

我采用的第二种方案。这种方案需要在创建的时候就考虑到。

## 安装Git, nodejs
安装并配置好Git、nodejs和npm，这里假设你已经配置好了

操作系统我还是觉得Linux方便些，本来在Linux上好好的，我在Windows下弄了两个晚上才弄好nodejs的问题。

## 创建项目文件夹
选好把项目放在哪个目录，然后从终端（cmd）打开，Win10不要用powershell，在powershell下npm会出现路径问题，我不知道是不是我没配置好。
有两种方法：
### 第一种
```shell
cd myBlog
hexo init
```
### 第二种
```shell
hexo init myBlog
```
如果一种不行的话尝试另外一种方法，和hexo的版本有关。 

## 安装其他包
进入项目文件夹，执行
```
npm install
```
这个命令对应着该目录下package.json文件，会安装里面列出的包。
然后执行
```
npm install hexo-deployer-git --save
```
这条命令会安装git部署相关的东西，安装以后才可以通过hexo d部署到github上面。
## 在GitHub上创建仓库
仓库命名为xxx.github.io  其中xxx为GitHub用户名
## 添加Git仓库
在myBlog下面，执行git init来初始化git仓库，此时文件夹里有上面生成的文件，但是此时他们都还没有进入Git仓库，只有你执行过git add 和git commit后才会进入git仓库。

## 创建分支
### 首先创建master分支
```
git add --all
git commit -m"master build"
```
这样直接提交的话就是主分支master
### 然后创建hexo分支
```
git checkout -b hexo
```
此时的你是在hexo分支下面操作，所做的操作均与master分支无关
### 切换回master分支
```
git checkout master
```
把该分支下面出来.git文件全删掉，因为这个master分支是以后用来存放网页文件的分支，不需要这些文件

## 进行站点配置
再次切换到hexo分支，此时master分支应该是没有东西了，今后所有的操作都是在hexo分支下面执行的，master分支里的东西都是由hexo自动生成的。
然后就可以配置_config.yml文件，设置远端git地址，分支，主题等等。。。

## 本地测试
依次执行
```
hexo n "new article"
hexo g
hexo s
```
打开localhost:4000查看页面是否显示成功，如果成功了再考虑远端部署。

## 托管项目文件
为了实现随处可以更新文章，需要把项目文件托管到github。
```
git remote add origin https://github.com/xxx/xxx.github.io.git
添加远端库
git add --all
git commit -m"xxxx"
git push origin hexo
```
执行首次push，这样在GitHub上面就能看到我们的项目文件了，并且是有两个分支，然后到分支管理里面把hexo设置为默认分支，以后git clone的时候就不必来回换分支了，只需要在hexo分支下操作就行了。

## 部署到github
我觉得这个步骤和上面那一步不能颠倒，否则可能会push不上。

执行
```
hexo g -d
或者
hexo d
```
部署到github，之后就可以通过https://xxx.github.io访问页面了。

## 日常更新维护

每次做了修改以后都要先git push修改，然后hexo g -d部署，这样能保证在其他设备或系统上clone到最近的更新。

当从其他设备上写博客时，首先需要有hexo和git环境，然后git clone这个仓库，进去以后
```
npm install
```
安装所有需要的包，之后就可以进行编辑和提交了。