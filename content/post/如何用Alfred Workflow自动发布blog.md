---
title: 如何用Alfred Workflow自动发布blog
date: 2021-10-15
lastmod: 2021-10-15 
draft: false
tags: ["Alfred", "obsidian", "hugo", "blog","python","netlify"]
categories: ["效率工具"]
author: "Eric Ding"
---

## 需求
我目前的博客方案是使用hugo+github+netlify搭建的。目前我要发布一篇blog的流程是这样的：
1. 使用`obsidian`编写blog，保存在`Documents/MD files/pages/100 Project/110 Blog` 中；
2. 将blog手动从`Documents/MD files/pages/100 Project/110 Blog` 拷贝到`Documents/myblog/ericding.me/content/post`中；
3. 打开`iTerm`命令行工具，进入`ericding.me`目录；
4. 手动执行git相关命令，将blog同步到github上：
	```shell
	git add .
	git commit -m "update"
	git push
	```

然后就是Netlify会自动拉取github上的更新，重新构建并自动部署。自此，新的blog就算是发布完了。

可以看到，这个步骤比较繁琐，涉及到很多需要手工的重复劳动。

本着能不动手就不动手的懒人原则，那我就在想，能否用alfred workflow将其简化呢？（答案是肯定的）

于是开始动手。

## 动手实现
### Alfred Workflow准备
首先是配置好Alfred Workflow，步骤如下：
1. 点击左下角的加号，添加`script fileter`;
	![[Pasted image 20211015105103.png]]
	![[Pasted image 20211015105152.png]]
2. 基本信息配置； 
	![[Pasted image 20211015105330.png]]
3. 配置script fileter；
	![[Pasted image 20211015105558.png]]
### 安装 Alfred workflow
从 [GitHub releases](https://github.com/deanishe/alfred-workflow/releases)下载`alfred-workflow-X.X.X.zip`，并将其解压到我们自己workflow的根目录里。（在自己workflow上右键打开根目录）
![[Pasted image 20211015110627.png]]
### 编写代码
```python
#!/usr/bin/python
# encoding: utf-8

import sys
import os
import shutil

from workflow import Workflow3

SRC_PATH = os.path.abspath(r'/Users/ericding/Documents/MD files/pages/100 Project/110 Blog')
DEST_PATH = os.path.abspath(r'/Users/ericding/Documents/myblog/ericding.me/content/post')
BLOG_ROOT_PATH = os.path.abspath(r'/Users/ericding/Documents/myblog/ericding.me/')

# 将blog拷贝从obsidian下拷贝到hugo的目录下
def copy_file():
    if os.path.exists(DEST_PATH):
        for root,dirs,files in os.walk(SRC_PATH):
            for file in files:
                src_file = os.path.join(root, file)
                shutil.copy(src_file, DEST_PATH)

# 执行git命令
def exc_git():
    # 切换目录
    os.chdir(BLOG_ROOT_PATH)
    # 执行git命令
    os.system(r"git add .")
    os.system(r"git commit -m 'update'")
    os.system(r"git push")

def main(wf):

    # 1. 拷贝文件
    copy_file()

    # 2. 执行git命令
    exc_git()

    # 3. 返回成功
    # wf.send_feedback()


if __name__ == '__main__':
    wf = Workflow3()
    sys.exit(wf.run(main))


```

### 更新代码到script filter
将代码粘贴到script filter中：
![[Pasted image 20211015115517.png]]

### 成果验证
- 在obsidian的Blog目录下随便写一个测试blog
- 唤起Alfred输入`blogupdate`
- 查看是否更新：

![[Pasted image 20211015115839.png]]
![[Pasted image 20211015115812.png]]

大功告成！