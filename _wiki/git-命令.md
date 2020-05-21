---
layout: wiki
title: git命令总结
description:  不积跬步,无以至千里;不积小流,无以成江海
date: 2019-12-19
categories: Git
prism: [git]
---

# git 使用总结

## git初始化、提交

    git init 初始化版本库
    git add <file> ,注意可反复多次使用，添加多个文件(把提交的所有修改放到暂存区)
    git commit -m "...(描述信息)"  (把暂存区的所有修改提交到分支)   
## git版本回退

    git reset --hard  HEAD^ 回退到上个版本，HEAD表示当前版本，"^"的个数表示前几版，太多可用git reset --hard HEAD~100 回退到上100个版本
    git reset --hard 1094a 回退到指定版本号的版本，版本号可以通过log查询
    git reflog 查看命令历史，以便确定要回到未来的那个版本
    git status 查看工作区、暂存区的状态，"干净了"表示没有需要提交的内容
    git diff 比较的是工作区和暂存区，git diff HEAD 比较的是工作区和分支
    git checkout  <file> 撤销对工作区的修改，回到add或commit之后的状态
    git reset HEAD <file> 可以把暂存区的修改撤销掉，重新回到工作区
    git rm <file> 用于删除一个文件，然后在 git commit -m"  "提交。如果删错了，可以git checkout <file>撤销删除
## git分支 查看、创建、切换、重命名

    git branch 查看分支
    git branch <name> 创建分支，如：git branch uat ,创建分支名为uat的分支
    git checkout <name>/git switch <name> 切换分支，新版本可以用switch
    git checkout -b <name>/git switch -c <name>创建并git切换分支
    git merge<name> 合并某分支到当前分支
    git branch -d <name>删除该分支(该分支已经合并到当前分支后，直接删除)
    git branch -m oldName newName 重命名本地分支
    git push --delete origin oldName 删除远程分支
    git push origin newName 上传新命名的本地分支
    git branch --set-upstream-to origin/newName 把修改后的本地分支与远程分支关联

## git查看日志

    git log --pretty=oneline 精简显示从近到远的提交日志
    git log --graph 查看分支合并图
    git log --graph --pretty=oneline --abbrev-commit  简洁版
    git merge --no-ff -m "..." 加上参数 --no-ff就可以看到曾经做过的合并

## bug分支

    修复bug时，我们会通过创建bug分支进行修复，然后合并，最后删除；当手头上的工作没有完成时，先保存工作场git stash，
    然后切换到bug分支去修复bug,修复完毕后再将修复后的代码合并到主分支上，记下当前提交修复bug的id如:4c805e2，
    接着切换到主分支再删除bug分支。然后再切换到工作分支，git stash pop,恢复工作场；在master分支上修复的bug,想要合并到当前的分支，可以用git cherry-pick 4c805e2命令，把bug提交到主分支上的修改“复制"到当前的分支上。
    git stash 将当前的工作场储存起来。比如临时需要修改主分支上的bug,但是当前分支的需求需要很久做完以至于不能提交当前分支的代码，需要存储起来
    git stash list 列出工作场 如：stash@{0}:WIP on dev :f52c... add merge
    git stash apply stash@{0} 恢复工作场的指定位置的内容
    git stash drop stash@{0}  删除工作场的指定位置的内容
    git stash pop 恢复工作场并删除工作场的内容
    git cherry-pick 4c805e2 合并到当前分支。
    git branch -D <name> 删除该分支。该分支已经add并提交，还未合并到当前分支  

## 远程仓库 
    
    git remote add origin git@server-name:path/repo-name.git 关联远程仓库
    git push -u origin master 第一次推送master分支的所有内容
    git push origin master 推送最新修改
    git clone git@github.com:prettyant/....git 克隆远程仓库
    git remote 查看远程库信息
    git remote -v 显示更详细的远程库信息
    git push origin <name> 从本地推送分支到远程
    git checkout -b <name> origin/<name> 在本地创建和远程对应的分支
    git branch --set-upstream <name> origin/<name>建立本地分支和远程关联
    git pull 从远程分支拉取。如果有冲突，要先处理冲突  git pull origin master --allow-unrelated-histories
    git rebase 可以把本地未push的分叉提交历史整理成直线，使我们在查看历史提交的变化时更容易
    git push --set-upstream origin uat1217  在远程仓库创建对应分支
    git remote set-url origin url 重新设置远程仓库地址

## git 合并时忽略某个文件(不是添加忽略文件)

    先切换到需要被合并的分支
    a、git config --global merge.ours.driver true  创建自定义的merge driver
    b、echo '(添加的不需要合并的文件)  merge=ours' >> .gitattributes
    c、git add . 
    d、git commit -m"..."   
    然后在切换到合并的分支，git merge <被合并的分支>

## git打tag标签

### 列出tag

    a、git tag 列出已有的tag
    b、git tag  -l "v1.1*" 加上-l 命令可以使用通配符来过滤tag

### 新建tag

    git tag v1.0直接新建tag   或者 git tag -a <tagName> -m"tag描述信息" 创建带备注的tag

### 给指定的历史log打tag

    git tag -a v1.2 9fcb02 -m "备注信息"

### 查看tag的详情信息

    git show 
    git show v1.2 查看指定tag的信息、备注

### 将tag同步到远程服务器

    git push origin <tagName> 推送单个tag
    git push origin --tags 推送本地所有tag

### 切换到某个tag

    git checkout v1.1 跟分支一样，可以直接切换到某个tag上去，这时候不位于任何分支，处于游离状态，可以考虑基于这个tag创建一个分支

### 删除某个tag

    git tag -d v1.1 删除本地tag
    git push origin :refs/tags/<tagName>删除远程tag








