---
title: Git基本命令
date: 2020-01-27 20:09:41
tags: git
---
&nbsp;&nbsp;&nbsp;&nbsp;1.git clone -b <指定分支名> <远程仓库地址>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">克隆指定分支，如：git clone -b blog https://github.com/Will-Du/Will-Du.github.io.git</b>
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;2.git branch <b style="color: #6A6AFF">查看当前分支</b>
&nbsp;&nbsp;&nbsp;&nbsp;3.git branch -r 或者 git branch -a<b style="color: #6A6AFF">查看所有分支</b>
&nbsp;&nbsp;&nbsp;&nbsp;4.git checkout <指定分支名><b style="color: #6A6AFF">切换分支，如：git checkout blog</b>
&nbsp;&nbsp;&nbsp;&nbsp;5.git pull<b style="color: #6A6AFF">拉代码</b>
&nbsp;&nbsp;&nbsp;&nbsp;6.git add -A <b style="color: #6A6AFF">提交所有变化</b>
&nbsp;&nbsp;&nbsp;&nbsp;git add -u <b style="color: #6A6AFF">提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)</b>
&nbsp;&nbsp;&nbsp;&nbsp;git add . <b style="color: #6A6AFF">提交新文件(new)和被修改文件(modified)，不包括被删除(deleted)文件</b>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: #6A6AFF">add到缓存中</b>
&nbsp;&nbsp;&nbsp;&nbsp;7.git commit -m "注释" <b style="color: #6A6AFF">提交代码</b>
&nbsp;&nbsp;&nbsp;&nbsp;8.git push <b style="color: #6A6AFF">推送代码</b>
&nbsp;&nbsp;&nbsp;&nbsp;9.git init <b style="color: #6A6AFF">初始化：创建一个git仓库，创建之后就会在当前目录生成一个.git的文件</b>
&nbsp;&nbsp;&nbsp;&nbsp;10.git add filename <b style="color: #6A6AFF">添加文件：把文件添加到缓冲区</b>
&nbsp;&nbsp;&nbsp;&nbsp;11.git rm filename <b style="color: #6A6AFF">删除文件</b>
&nbsp;&nbsp;&nbsp;&nbsp;12.git status <b style="color: #6A6AFF">查看git库的状态，未提交的文件，分为两种，add过已经在缓冲区的，为add过的</b>
&nbsp;&nbsp;&nbsp;&nbsp;13.git diff filename <b style="color: #6A6AFF">比较：如果文件修改了，还没有提交，就可以比较文件修改前后的差异</b>
&nbsp;&nbsp;&nbsp;&nbsp;14.git log <b style="color: #6A6AFF">查看日志</b>
&nbsp;&nbsp;&nbsp;&nbsp;15.git reset <b style="color: #6A6AFF">版本回退：可以将当前仓库回退到历史的某个版本</b>
&nbsp;&nbsp;&nbsp;&nbsp;git reset \-\-hard HEAD^ <b style="color: #6A6AFF">回退到上一个版本，(HEAD代表当前版本，有一个^代表上一个版本，以此类推)</b>
&nbsp;&nbsp;&nbsp;&nbsp;git reset \-\-hard d7b5 <b style="color：#6A6AFF">回退到指定版本(其中d7b5是想回退的指定版本号的前几位)</b>
&nbsp;&nbsp;&nbsp;&nbsp;16.git reflog <b style="color: #6A6AFF">查看命令历史：查看仓库的操作历史</b>
&nbsp;&nbsp;&nbsp;&nbsp;17.git remote add origin git://127.0.0.1/abc.git <b style="color: #6A6AFF">增加了远程仓库abc</b>
&nbsp;&nbsp;&nbsp;&nbsp;18.git remote remove origin <b style="color: #6A6AFF">移除远端仓库</b>
&nbsp;&nbsp;&nbsp;&nbsp;19.git push -u origin master <b style="color: #6A6AFF">将本地仓库内容推送到远端仓库(-u 表示第一次推送master分支的所有内容，后面再推送就不需要-u了)，跟commit的区别在于一个是提交到本地仓库，一个是提交到远程仓库</b>
&nbsp;&nbsp;&nbsp;&nbsp;20.git commit -m "update .gitignore" <b style="color: #6A6AF">提交到git时，忽略部分IDE产生的文件。在根目录下创建.gitignore文件，注意:</b><b style="color: orangered">新加.gitignore只能忽略那些原来没有被提交的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。</b>
&nbsp;&nbsp;&nbsp;&nbsp;<b style="color: orangered">解决方法就是先把本地缓存删除(改变成未track状态)，然后再提交：</b>
&nbsp;&nbsp;&nbsp;&nbsp;git rm -f \-\-cached .
&nbsp;&nbsp;&nbsp;&nbsp;git add .
&nbsp;&nbsp;&nbsp;&nbsp;git commit -m "update .gitignore"
&nbsp;&nbsp;&nbsp;&nbsp;.gitignore文件内容，举例如下
```
/**/target
/**/.project
/**/.classpath
/**/.settings
```
&nbsp;&nbsp;&nbsp;&nbsp;PS:在使用GIT之后，会发现要比SVN好用得多，从以下几个方面做个简单的比较：
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.GIT为分布式方式，在传统的版本控制里，比如CVS或者SVN，这个是最核心的区别
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.存储的方式不一样。GIT存储的方式是按照元数据的方式进行存储，而传统的CVS或者SVN则是以文件方式存储。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.GIT特别的分支。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CVS和SVN的分支管理比较简单，只是在版本库中另一个目录而已，确认代码是否已合并也相对麻烦，在分支管理方面容易产生遗留和错误。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;GIT分支管理则相对复杂，但是用起来非常有趣，各个分支键可以随意的快速进行切换、合并、还原等操作。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.从完整性上来说，GIT的完整性远远高于SVN
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SVN经常会在不同版本间使用容易出现问题，比如兼容性、网路不稳定带来莫名其妙的异常。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;GIT的内存存储则采用的是哈希算法，不仅能够保障了代码的完整性，而且在网络和磁盘故障方面几乎不受到任何影响。