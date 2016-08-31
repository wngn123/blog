<!--
author: wangn123
head: head.png
date: 2016-08-31
title: git使用
tags: git
category: Other
status: publish
summary:git使用，常用命令
-->

## git config ## 
使用Git的第一件事就是设置你的名字和email,这些就是你在提交commit时的签名
```
git config --global user.name "wngn123"
git config --global user.email "wngn123@163.com"
```
执行了上面的命令后,会在你的主目录(home directory)建立一个叫~/.gitconfig 的文件. 内容一般像下面这样:
```
[user]
name = wngn123
email = wngn123@163.com
```
git config 文档
```
git config -h
git config --help 
```

Git使用一系列的配置文件来存储你定义的偏好，它首先会查找/etc/gitconfig文件，该文件含有对系统上所有用户及他们所拥有的仓库都生效的配置值，如果传递--system选项给git config命令， Git 会读写这个文件。

接下来Git会查找每个用户的~/.gitconfig文件，你能传递--global选项让Git读写该文件。

最后Git会查找由用户定义的各个库中Git目录下的配置文件（.git/config），该文件中的值只对该git库有效。如果传递 --local选项给git config命令，Git会读写这个文件。 --local  选项是默认选项

以上阐述的三层配置从一般到特殊层层推进，如果定义的值有冲突，以后面层中定义的为准，例如：在.git/config和/etc/gitconfig的较量中，.git/config取得了胜利。虽然你也可以直接手动编辑这些配置文件，但是运行git config命令将会来得简单些。

-l, --list 用于列出我们已经设置了的git的配置信息。
```
git config --global -l

name = wngn123
email = wngn123@163.com
```
```
git config --global color.ui true  打开所有的默认终端着色
git config --global core.autocrlf true 如果是在Windows系统上，把它设置成true，这样当签出代码时，LF会被转换成CRLF
git config --global core.autocrlf false  仅运行在Windows上的项目，可以设置false取消此功能
git config --global core.autocrlf input Linux或Mac CRLF转换成LF，签出时不转换
```


## git clone ## 
克隆git项目到本地，（第一次创建项目可现在github上创建一个空的项目，任何克隆到本地，在提交）
```
git clone https://github.com/wngn123/test.git
```
在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系(tracking)。比如，在git clone的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的master分支自动”追踪”origin/master分支。

## git add ## 
克隆到本地的项目被修改后要添加到索引区（index）,使用git add命令

主要用于把我们要提交的文件的信息添加到索引库中
```
git add file  添加指定变动文件
git add --all 添加所有变动文件
```

## git commit ## 
提交改的代码到本地git仓库，必须填写注释，
```
git commit -m 'git study 2016-07-20'
```
我的注释：descripe date 如果 git study 2016-07-20

## git push ## 
将当前分推送到github服务器。

 **git push remote_host localbranch:remotebranch**
 
**git pull <远程主机名> <远程分支名>:<本地分支名>**

``` 
git push origin master:master
git push origin develop:develop
```
如果省略远程分支名，则表示将本地分支推送与之存在”追踪关系”的远程分支(通常两者同名)，如果该远程分支不存在，则会被新建。

```git push origin master```
将本地的master分支推送到origin主机的master分支。如果后者不存在，则会被新建。

```git push origin :master```
如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。等同于```git push origin --delete master```

```git push origin```
如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

```git push```
如果当前分支只有一个追踪分支，那么主机名都可以省略。

## git pull ## 
取回远程主机某个分支的更新，再与本地的指定分支合并

**git pull <远程主机名> <远程分支名>:<本地分支名>**

```git pull origin next:master``` 
取回origin主机的next分支，与本地的master分支合并

```git pull origin next``` 
取回origin主机的next分支，与本地的当前分支合并,相当于
```
git fetch origin
git merge origin/next
```
```git pull origin``` 
本地的当前分支自动与对应的origin主机”追踪分支”(remote-tracking branch)进行合并。

```git pull``` 
前分支自动与唯一一个追踪分支进行合并。

## git fetch ## 
从远程获取最新版本到本地，不会自动merge
```
git fetch origin master
git log -p master..origin/master
git merge origin/master
```
首先从远程的origin的master主分支下载最新的版本到origin/master分支上,然后比较本地的master分支和origin/master分支的差别,最后进行合并
```
git fetch origin master:tmp
git diff tmp 
git merge tmp
```
从远程获取最新的版本到本地的test分支上,之后再进行比较合并

## git branch ## 

```git branch new_branch``` 
创建分支

```git checkout new_branch``` 
切换分支
```git checkout -b test_branch``` 
创建和切换分支

```git branch -D test_branch``` 
删除分支

```git branch -m new_branch wchar_support``` 
重命名分支

```git branch -r```
显示分支的远程追踪关系
  
```git branch -a```
显示当前分支和分支的远程追踪关系
  
```git branch --track feature1 origin/master``` 
设置分支的远程追踪关系
  
```git branch --set-upstream temp origin/xxx```
设置分支的远程追踪关系

```git checkout -b temp --track origin/xxx```
设置分支的远程追踪关系
 
## git tag ## 

```git tag -l ```
查看标签

```git show Release_1_0 ```
查看标签详细信息

```git tag -d Release_1_0 ```
删除标签

```git tag -a 'Release_1_0' -m 'Tagged basic string operation code' HEAD ```
创建标签

```git push origin tag Release_1_0 ```
创建标签 

#### git status
列出当前目录所有还没有被git管理的文件和被git管理且被修改但还未提交(git commit)的文件

```git status -s```
-s表示short, -s的输出标记会有两列,第一列是对staging区域而言,第二列是对working目录而言.

```git status -uno```
可以只列出所有已经被git管理的且被修改但没提交的文件。

## git diff

比较working tree(工作目录)同git index(Git索引)之间，git index和git directory(GIT库目录)之间，working tree和git directory之间，git directory中不同commit之间的差异

**本地:** 没有执行git add之前，代码在本地存在  ***working tree(工作目录)***

**暂存区：** 执行git add后但是没有执行git commit，代码存在于暂存区，本地 ***git index(Git索引)***

**提交列表：** 执行git commit后，代码存在于提交列表，暂存区，本地 ***git directory(GIT库目录)***
```
git diff  本地和暂存区相比
git dif HEAD 本地和提交列表相比
git dif --cache/--staged 暂存区和提交列表相比
```
```
git diff [<path>...]
```
这个命令最常用，在每次add进入index前会运行这个命令，查看即将add进入index时所做的内容修改，即working directory和index的差异。
```
git diff --cached [<path>...]
```
这个命令初学者不太常用，却非常有用，它表示查看已经add进入index但是尚未commit的内容同最后一次commit时的内容的差异。即index和git directory的差异。
```
git diff --cached [<commit>] [<path>...]
```
这个命令初学者用的更少，也非常有用，它表示查看已经add进入index但是尚未commit的内容同指定的<commit>之间的差异，和上面一条很相似，差别仅仅<commit>，即index和git directory中指定版本的差异。
```
git diff <commit> [<path>...]
```
这个命令用来查看工作目录和指定<commit>的commit之间的差别，如果要和Git directory中最新版比较差别，则<commit>=HEAD。如果要和某一个branch比较差别，<commit>=分支名字
```
git diff <commit> <commit> [<path>...]
```
这个命令用来比较git directory中任意两个<commit>之间的差别，如果想比较任意一个<commit>和最新版的差别，把其中一个<commit>换成HEAD即可。

## git merge ## 
把一个分支或或某个commit的修改合并现在的分支上	
```
git merge [options] <msg> HEAD <commit> 
```
这里的HEAD其实就是分支名，用于说明把HEAD分支合并到当前分支。

```
git merge -h
git merge --help

git merge develop   develop分支合并到当前分支
git merge --no-ff develop develop分支快速合并到当前分支（当前分支直接链接到develop分支）
```
## git log ## 

用于查看提交记录

**-n 查看最近n次的提交信息** 

n是一个正整数
```
git log -2    查看最近2次的提交历史记录
```
**-- fileName查看指定文件的提交信息** 

fileName为任意文件名 (注：文件名应该放到参数的最后位置，通常在前面加上--并用空格隔开表示是文件。)
```
git log file1 file2   查看file1文件file2文件的提交记录
git log file/         查看file文件夹下所有文件的提交记录
```

**--branchName查看莫个分支上的提交记录** 

branchName为任意一个分支名字，同上，需要放到参数中的最后位置处。(注：如果分支名与文件名相同，系统会提示错误，可通过--选项来指定给定的参数是分支名还是文件名。)例：在当前分支中有一个名为v1的文件，同时还存在一个名为v1的分支,则：
```
git log v1 -- 此时的v1代表的是分支名字
git log -- v1 此时的v1代表的是名为v1的文件
git log v1 -- v1
```

**tagName或branchame查询指定标签/分支中的提交记录信息**  
```
git log v1.0..        查询从v1.0以后的提交历史记录(不包含v1.0)
git log test..master  查询master分支中的提交记录但不包含test分支记录
git log master..test  查询test分支中的提交记录但不办含master分支记录
git log master...test 查询master或test分支中的提交记录。
git log test --not master　　屏蔽master分支
```

**根据commit查询日志** 

其中，commit可以是提交哈希值的简写模式，也可以使用HEAD代替。HEAD代表最后一次提交，HEAD^为最后一个提交的父提交，等同于HEAD～1，HEAD～2代表倒数第二次提交
```
git log commit  　　查询commit之前的记录，包含commit
git log commit1 commit2 查询commit1与commit2之间的记录，包括commit1和commit2
git log commit1..commit2 同上，但是不包括commit1
```



**--pretty按指定格式显示日志信息**

可选项有：oneline,short,medium,full,fuller,email,raw以及format:<string>,默认为medium，可以通过修改配置文件来指定默认的方式。
```
git log (--pretty=)oneline
常见的format选项：
选项     说明
%H      提交对象（commit）的完整哈希字串 
%h      提交对象的简短哈希字串 
%T      树对象（tree）的完整哈希字串 
%t      树对象的简短哈希字串 
%P      父对象（parent）的完整哈希字串 
%p      父对象的简短哈希字串 
%an     作者（author）的名字 
%ae     作者的电子邮件地址 
%ad     作者修订日期（可以用 -date= 选项定制格式） 
%ar     作者修订日期，按多久以前的方式显示 
%cn     提交者(committer)的名字 
%ce     提交者的电子邮件地址 
%cd     提交日期 
%cr     提交日期，按多久以前的方式显示 
%s      提交说明 
注：作者是指最后一次修改文件的人；而提交者是指提交该文件的人。
git log --pretty=format:"%an %ae %ad %cn %ce %cd %cr %s" --graph
```
**--mergs 查看所有合并过的提交历史记录**

**--no-merges     查看所有未被合并过的提交信息**

**--author=someonet       查询指定作者的提交记录**
```
git log --author=gbyukg
```
**--since，--affter       仅显示指定时间之后的提交(不包含当前日期)**

**--until，--before       仅显示指定时间之前的提交(包含当前日期)**
```
git log --before={3,weeks,ago} --after={2010-04-18}
```
**--grep  通过提交说明信息过滤提交日志**
```
git log --grep=hotfix 该命令会列出所有包含hotfix字样的提交信息说明的提交记录
注意：如果想同时使用--grep和--author，必须在附加一个--all-match参数。
```
**-S      通过查询文件的变更内容来检索出指定提交日志**

注：-S后没有"="，与查询内容之间也没有空格符
```
git log --Snew
```
**-p      查看提交时的补丁信息**
```
git log -p --no-merges -2
```
**--stat  列出文件的修改行数**

**--sortstat      只显示--stat中最后行数修改添加移除的统计**

**--graph 以简单的图形方式列出提交记录**

**--abbrev-commit 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。**

**--relative-date 使用较短的相对时间显示（比如，“2 weeks ago”）。**

**--name-only 仅在提交信息后显示已修改的文件清单。**

**--name-status 显示新增、修改、删除的文件清单。**


## git rm ## 
```git rm file```
从staging区移除文件,同时也移除出工作目录.

```git rm --cached```
 从staging区移除文件,但留在工作目录中.
 
```git rm --cached```
从功能上等同于git reset HEAD,清除了缓存区,但不动工作目录树.
     
## git远程追踪

在Git中‘追踪分支’是用与联系本地分支和远程分支的. 如果你在’追踪分支'(Tracking Branches)上执行推送(push)或拉取(pull)时,　它会自动推送(push)或拉取(pull)到关联的远程分支上.
如果你经常要从远程仓库里拉取(pull)分支到本地,并且不想很麻烦的使用"git pull "这种格式; 那么就应当使用‘追踪分支'(Tracking Branches).
‘git clone‘命令会自动在本地建立一个'master'分支，它是'origin/master'的‘追踪分支’. 而'origin/master'就是被克隆(clone)仓库的'master'分支.
译者注: origin一般是指原始仓库地址的别名.
你可以在使用'git branch'命令时加上'--track'参数, 来手动创建一个'追踪分支'.
git branch --track experimental origin/experimental
当你运行下命令时:
$ git pull experimental
它会自动从‘origin'抓取(fetch)内容，再把远程的'origin/experimental'分支合并进(merge)本地的'experimental'分支.
当要把修改推送(push)到origin时, 它会将你本地的'experimental'分支中的修改推送到origin的‘experimental'分支里,　而无需指定它(origin).