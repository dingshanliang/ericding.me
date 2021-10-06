---
title: 免费搭建个人博客：hugo+netlify
date: 2021-10-06 
lastmod: 
draft: false
tags: ["个人博客","免费","hugo","netlify","github"]
categories: ["博客搭建"]
author: "Eric Ding"
---
很久之前，我曾写过好多个博客。有独立博客，也有在其他平台上。因为种种原因，都没有坚持下来。

最近一直在琢磨着再搞一个。写写东西。主要是为自己的学习、思考做个记录，同时也能分享出去，也期待着能和其他人交流。算是自己的一个个人成长和交流平台吧。

今天折腾了大半天，终于算是大功告成。
总体采用hugo+netlify+github，免费搭建个人博客。最终实现了如下的工作流：
1. 本地用`obsidian`编辑文章；
2. 使用git将更新的文章推送到github；
3. netlify自动同步github上的更新，从而最终更新网站；

整个测试下来，更新速度很快。基本上这边push完，那边等个几秒钟，就可以快速更新。体验不错。（可以使用command + R 强制刷新网页）

**说明：**搭建环境基于macos完成。

# 本地 搭建步骤：
首先是本地搭建hugo环境，能在本机上把博客跑起来。
1. 安装hugo : `brew install hugo`
2. 创建网站 : `hugo new site ericding.me`
> 此处需要将`ericding.me`替换成你自己的网站名，如果没有可用临时随便起一个。
3.  添加主题theme
将github上的主题文件夹拷贝到本地的themes文件夹下。
	``` shell
	$ git clone https://github.com/olOwOlo/hugo-theme-even themes/even
	```
> 说明：
> 主题详情：https://opensourcelibs.com/lib/hugo-theme-even 
> 主题demo: https://hugo-theme-even.netlify.app/

4.  修改 `config.toml ` 文件，使主题生效
	> 说明：以上面的 `even` 主题为例，可直接将 `exampleSite ` 下的 `config.toml` 文件拷贝到root目录下，替换root目录下的 `config.toml` 文件
5. 添加文章 : `hugo new posts/my-first-post.md`
6. 启动hugo服务 : `hugo server -D`
7. 查看结果：http://localhost:1313/
# 使用github托管
完成本地搭建后，如果想将博客托管到网上，可以考虑采用免费的github平台。将文章随时更新保存到云端。
1. 创建代码仓库
2. 本地网站的文件夹： `git init`
3. 推送本地内容到远端仓库
	  ``` shell
	  git remote add origin git@github.com:dingshanliang/ericding.me.git
	  git remote set-url origin git@github.com:dingshanliang/ericding.me.git
	  git branch -M main
	  git push -u origin main
	  ```
	  >  注意：
	  >  此处要使用SSH进行推送，不要用https。由于国内网络环境原因，会有443错误。
	  > 关于如何使用SSH连接github，参考： [Connecting to GitHub with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

4. 更新文章后的操作：
	  ``` shell
	  git add .
	  git commit -m "update"
	  git push
	  ```
# 使用Netlify部署个人博客
进一步的，可以使用Netlify进行自动化部署网站。这样，别人就可以通过网址访问到我们的博客了。
1. 在 [Netlify](https://www.netlify.com/) 上注册账号
> 注意：ericding@yeah.net不能注册，改用的gmail邮箱可以。

2. github授权，并选择site所在的库进行安装 Netlify;
3. 配置文件 `.gitmodules `
	```
	[submodule "themes/even"]
	path = themes/even
	url = https://github.com/olOwOlo/hugo-theme-even.git

	```
	> 注意：需要将无用的theme删除，不然需要将他们的path和url全部配置到上面的文件中去。
1. 网站部署成功： [我的blog](https://infallible-kepler-6f0889.netlify.app/)
