layout: '[layout]'
title: Git使用操作整理
date: 2015-11-13 00:00:00
tags: [git]
---

Git基本概念
    a.git跟踪并管理的是文件的修改，而非文件
        修改就是：新增一行，删除一行，更改某个字符甚至创建新文件等
        每次的修改如果不 add 到暂存区，将不会被 commit 提交到仓库的
    b.工作区(Working Directory)：就是当前你的版本库(.git文件夹)所在目录
    c.版本库(Repository)：工作区有个隐藏的".git"目录，这个目录不算工作区，而是Git的版本库
    d.版本库：
        暂存区：称为stage或index
                git add file 即为将文件的修改添加到暂存区
        分支：称为branch，git为我们创建第一个分支是master
              git commit 即为将文件提交到当前HEAD指向的分支
        HEAD: 指向当前分支版本的指针
    e.自己本地创建的分支，别人是看不见的
    f.通常情况下合并分支，Git会用"Fast forward"模式，这种模式下被合并的删除分支后，会丢掉分支信息
        如果，强制禁止"Fast forward"模式，Git就会在merge时生成一个新的commit,这样，从分支历史上就可以看出分支信息。
        强制禁止"Fast forward":
            #git merge --no-ff -m 'merge info' branch_name
    g.禁止GIT自动换行&提交时强制检测换行符
    
        #git config --global core.autocrlf false    // 禁止GIT自动换行
        #git config --global core.safecrlf true     // 提交时强制检测换行符

1.安装完成后的基本设置

```sh
    #git config --global user.name "Your Name"    //设置全局的用户名
    #git config --global user.email "email@example.com"   //设置全局的Email
    #git config --global color.ui true   //让bash显示颜色
    #git config --global core.autocrlf true //提交时将CRLF转换为LF,迁出代码将LF转换为CRLF
```

2.初始化版本库(仓库)

```sh
    #git init
```

3.将文件的修改添加到版本库的暂存区(stage/index)

```sh
    #git add file_name.ext
```
4.提交暂存区(stage/index)的所有修改内容到仓库(当前分支)

```sh
    #git commit -m "本次提交简介,最好是文件或功能的简介,因为会显示在github文件描述栏里"
``` 

5.查看版本库的当前状态

```sh
    #git status
```

6.diff 查看文件

```sh
    #git diff file_name.ext
```

7.查看日志(历史记录)

```sh
    #git log
    #git log --pretty=online : 单行显示
```

8.查看每一次提交或改变版本库的命令(查看历史提交版本)

```sh
    #git reflog
```

9.Git版本号的表示

```sh
    #git用SHA1算法计算出来的非常大的数字,用16进制表示
    #git用HEAD表示当前最新提交的版本
    #git的上一个版本就是HEAD^,上上一个版本就是HEAD^^,依次类推
        也可这样表示往上100个版本: HEAD~100
```

10.版本回退(可以理解为重新设置HEAD指针的指向)

```sh
    #git reset --hard HEAD^
    #git reset --hard commit_id(版本号)
```

11.丢弃工作区(所有文件或某文件)的修改
    分两种情况：
        a.修改未被提交到暂存区(stage/index)，撤销修改将回到和当前branch版本库一模一样的状态
        b.修改已经提交到暂存区(stage/index)后，又进行了部分修改(此修改未提交暂存区)，
            撤销修改将回到添加暂存区后的状态
            总之，就是让这个文件回到最近一次git commit 或 git add 后的状态，即，只是丢弃工作区的修改。其实就是用版本库或暂存区里的文件替换工作区的文件
      
```sh      
    #git checkout -- file_name.ext
```

12.撤销已经提交暂存区的更改，重新放回工作区。
    其实就是将暂存区的文件用当前branch所在的版本库替换
    用HEAD表示当前branch的最新版本

```sh
        #git reset HEAD file_name.ext
```

13.删除文件(一般分三步)

```sh
     step 1：在资源管理器下直接删除文件，或 rm file
     step 2：git rm file
     step 3：git commit -m 'delete info'
```

14.创建分支

```sh
    a. git checkout -b branch_name        创建并切换到新建的分支
    b. git branch branch_name             创建新分支，并未切换
    
    #git checkout -b branch_name //相当于 #git branch branch_name 和 #git checkout branch_name两条命令
```

15.查看分支

```sh
    #git branch
    #git branch -a : -a参数将会同时列出远端和本地分支
```

        //注：当前分支前面会标一个星号(*)
16.切换分支

```sh
    #git checkout branch_name    //切换到branch_name分支
```

17.合并分支

```sh
    #git marge branch_name   //将branch_name分支合并到当前分支
```

    
        //注：合并模式分两种
            a. 快进模式：直接将当前分支的指针指向branch_name分支的当前提交
            b TODO.....
18.删除分支

```sh
        #git branch -d branch_name   //被删除分支已经被合并
        #git branch -D branch-name   //被删除分支未被合并(强行删除)
```

            //注：未合并分支删除后将丢失修改
19.解决冲突

```sh
    a. 当合并分支时，会发生冲突，这样合并分支就不会是“快进模式”合并了
    b. Git用 <<<<<<< HEAD, =======, >>>>>>> branch_name 来标记出不同分支的内容
    c. 然后手工编辑冲突
```

    注：当Git无法自动合并分支时，就必须首先解决冲突，然后再提交，合并才能完成。
20.查看分支合并情况

```sh
    #git log --graph --pretty=oneline --abbrev-commit
```

         //注： --graph 伪图形化查看分支合并图
         //    --pretty=oneline 单行省略显示(将不显示提交者和提交时间信息)
         //    --abbrev-commit  缩略显示版本ID
21.保存当前工作现场(即,保存储藏)
    当有其它分支需要马上开发(比如bug修复分支)，而当前的分支工作并未完成，不能提交时，可以使用保存工作现场，等以后恢复现场后再继续工作
    修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除
    当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场
    
```sh
    #git stash//保存现场：
    #git stash list// 查看现场：
    #git stash apply//恢复现场：此方法恢复现场后stash的内容并不会被删除，需要 git stash drop 手工删除
    #git stash pop //恢复现场：恢复现场的同时删除stash内容
    #git stash drop//删除现场：
        //注：可多次stash，恢复的时候，先用 git stash list 查看，然后指定恢复的stash，用命令：
    #git stash apply stash@{0}
    #git stash pop stash@{0}
```

22.本地仓库关联远程仓库

```sh
     #git remote add origin git@github.com:wangnanphp/learngit.git
```

     //注：origin为远程库的名称，也可以叫别的名子，但origin更见名知意
23.推送本地仓库指定分支到远程仓库

```sh
     #git push
     #git push <远端仓库> <本地仓库的分支>
     #git push -u orgin master
```

         //注：git push 直接将当前分支推送到远程
         //    -u <远端> <分支>   (一般用于第一次推送,以后推送可以简化命令)
         //       推送本地master分支的同时还将本地master和远程master关联
24.从远程库克隆

```sh
    #git clone git@github.com:wangnanphp/learngit.git
```

        //注：默认克隆远程库的情况下，只能看到克隆到的本地master分支
            如果要克隆其他分支，请看27条(分支抓取)
25.查看远程库信息

```sh
    #git remote
    #git remote -v 显示详细信息
```

        //注：详细信息中显示的 "fetch" 代表可以拉取(抓取)的地址，"push" 代表可以推送的地址
            如果没有推送权限，将看不到 "push" 的地址
26.推送本地分支
    将指定分支上的所有本地提交(commit)，推送到远程库
    
```sh
    #git push origin_depository branch_name
```

        //orgin_depository：远程库
        //branch_name：本地分支
        //eg：git push origin master
27.获取(抓取)远程分支修改

```sh
    #git pull
```

        //注：必须指定当前分支与远程分支的链接关系，否则 git pull 会失败
        //    git pull 回来的远端修改在没有冲突的情况下会自动合并到本地分支
        //    如果远端的内容和本地的内容有冲突，则需要手工解决冲突，再commit，再push
28.创建远程分支到本地(分支抓取)
    抓取远程分支到本地，并切换到
    
```sh
    #git checkout -b local_branch origin_depository/origin_branch
```

        //    eg：git checkout -b dev origin/dev
        //注：此操作只会在本地创建和远程分支对应的分支，
29.设置本地分支与远程分支之间的链接关系(注：此参数已经弃用，有两个替代参数)

```sh
    #git branch --set-upstream local_branch origin_depository/branch_name
        //    eg：git branch --set-upstream dev origin/dev
        // 一个替代的：
    #git branch --set-upstream-to origin_depository/branch_name
```

        //关联本地当前分支到远端 origin_depository/branch_name 分支
30.创建标签

```sh
    git tag tag_name
        eg: git tag v0.1
    git tag tag_name commit_id  用指定 commit_id 创建tag
    git tag -a tag_name -m "tag info"   创建带说明信息的tag  -a：tag_name  -m "tag_info"
    git tag -s tag_name -m "tag info" commit_id    -s 用私钥签名一个标签
```

        //注：默认标签是打在当前分支的最新commit上(HEAD)，也可以给指定commit_id打标签
            发布一个版本时，我们通常先在版本库中打一个标签，这样，就唯一确定了打标签时刻的版本。
            -s 签名采用PGP签名，因此，必须首先安装gpg（GnuPG），如果没有找到gpg，或者没有gpg密钥对，就会报错
31.查看所有标签

```sh
    #git tag
        //注：标签不是按时间顺序，而是按字母顺序排列的
```

32.查看标签信息

```sh
    #git show tag_name
```

33.删除标签

```sh
    #git tag -d tag_name
        //注：因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除
```

34.推送本地标签

```sh
    #git push origin tag_name    //推送单个标签
    #git push origin --tags      一次性推送全部尚未推送远程的本地标签
```

35.推送过远程的标签删除

```sh
        #git tag -d tag_name    //a. 先删除本地
        #git push origin : refs/tags/tag_name    //b. 再删除远程
```

36.别名配置

```sh
    #git config --global alias.st status
```