---
title: "git使用笔记"
date: 2021-10-01T19:29:19+08:00
draft: true
typora-root-url: ../../content/
categories: 开源软件
---

## Configuring Git

* Settings

    * Name
    * Email
    * Default Editor
    * Line Ending

* Levels

    * System: All users
    * Global: All repositories of the current user
    * Local: The current repository

```sh
$ git config --global user.name "hackcraker"
$ git config --global user.email mgyuchen@163.com
$ git config --global core.editor vim
# 用于多平台协作
$ git config --global core.autocrlf input
```

## Creating Snapshots

**Initializing a repository**

* `git init`

**Staging files**

* Stages a single file: 
    `git add file1.js `
* Stages multiple files: 
    `git add file1.js file2.js`
* Stages with a pattern: 
    `git add *.js`
* Stages the current directory and all its content: 
    `git add .`

**Viewing the status** 

* Full status: 
    `git status`
* Short status: 
    `git status -s`

**Committing the staged files**

* Commits with a one-line message: 
    `git commit -m "Message" `
* Opens the default editor to type a long message: 
    `git commit`

**Skipping the staging area** 

* 只对modified的文件有效，对untracked的文件还是需要`git add`
    只要加入staging area的文件，git才会去track
    `git commit -am "Message"`

**Removing files**

* Removes from working directory and staging area: 
    `git rm file1.js`
* Removes from staging area only: 
    `git rm --cached file1.js`

**Renaming or moving files**

* 移动文件或者重命名文件时只需要一条命令就搞定
    `git mv file1.js file1.txt`

**Ignoring files**

* 在repo的根目录创建一个`.gitignore`的文件，在里面标注不想被git追踪的文件
    这样在改动相关文件时git并不会显示有内容改动

    但这个只对git从未跟踪的文件上生效，否则还是会显示有内容改动
    可以通过`git ls-files`查看跟踪的文件

    >  Show information about files in the index and the working tree

    为了解决这个问题，只需将相关文件从staging area删除，而不需从disk上真正的删除

    `git rm --cached file1.js`

**Short status**

* 更简洁的查看文件状态
    `git status -s`

    ![image-20211003234649105](/posts/git%E4%BD%BF%E7%94%A8%E7%AC%94%E8%AE%B0/image-20211003234649105.png)

    左边是staging area 右边是working directory

    * 右M：本地有修改
    * 左M：本地有修改并提交到了暂存区
    * ??：新加的文件
    * 左A：新加的文件并提交到了暂存区

**Viewing the staged/unstaged changes** 

* Shows unstaged changes
    `git diff`
* Shows staged changes
    `git diff --staged`
* Same as the above
    `git diff --cached`

 ![image-20211004004722916](/posts/git%E4%BD%BF%E7%94%A8%E7%AC%94%E8%AE%B0/image-20211004004722916.png)

含义：*@@ -start line, count +start line, count @@*



## Branching & Merging

**Managing branches**

* Creates a new branch called bugfix
    `git branch bugfix`
* Switches to the bugfix branch
    `git checkout bugfix`
* Same as the above
    `git switch bugfix`
* Creates and switches
    `git switch -C bugfix`
* Rename branch
    `git branch -m bugfix bugfix/signup`
* Deletes the bugfix branch
    `git branch -d bugfix`

**Comparing branches**

* Lists the commits in the bugfix branch not in master
    `git log master..bugfix`

* Shows the summary of changes
    `git diff master..bugfix`
    如果当前已经在master分支，可省略`master..`
    `git diff bugfix`

    增加一些可选项
    `git diff --name-only bugfix`
    `git diff --name-status bugfix`

**Stashing**

* Creates a new stash
    `git stash push -m "New stash"`
    默认不会stash untracked的file，需要加参数`-a`
    `git stash push -am "New stash"`
* Lists all the stashes
    `git stash list`
* Shows the given stash
    `git stash show stash@{1}`
    shortcut for stash@{1}
    `git stash show 1`
* Applies the given stash to the working dir
    `git stash apply 1`
* Deletes the given stash
    `git stash drop 1` 

* Deletes all the stashes
    `git stash` clear

## Collaboration

**Workflow**

* centralized：每个人都有push权限
* integration-manager：
    * maintainer拥有push权限：pull fork repo → merge → push
    * contributor：fork → clone → commit & push to fork repo → pull request

**Cloning a repository**

* clone and rename: git clone url newName

    `git clone https://github.com/hackcraker/Mars.git MarsProject`

* 在git log中查看到的信息 `(HEAD -> main, origin/main, origin/HEAD)`
    origin指的是什么呢
    
    > `origin` is an **alias** *on your system* for a particular remote repository. It's not actually a property of that repository.
    
    远端仓库在本地的名字是origin
    orgin/master: a remote tracking branch
    
* check remote repositories
    `git remote`

    `git remote -v`

**Syncing with remotes**

* Fetches master from origin
    `git fetch origin master`

* Fetches all objects from origin
    `git fetch origin`

* Shortcut for "git fetch origin"
    将远程内容拉取到本地，但HEAD并没有改变
    `git fetch`
    `git branch -vv`
    同步HEAD指针
    `git merge origin/main`

* Fetch + merge

    有可能会导致commit记录非线性
    `git pull`

    已经出现了非线性的解决方法:

    1. 回滚到上次提交`git reset --hard HEAD~1`
    2. ``git pull --rebase`

* Fetch + rebase
    不产生新的节点，提交树一直向前
    `git pull --rebase`

* Pushes master to origin
    `git push origin master`

* Shortcut for "git push origin master"
    `git push`

**Sharing tags**

* Pushes tag v1.0 to origin
    `git push origin v1.0`
* Delete tag v1.0 from origin
    `git push origin --delete v1.0`
* 本地删除tag v1.0
    `git tag -d v1.0`

**Sharing branches**

* Shows remote tracking branches
    `git branch -r`
* Shows local & remote tracking branches
    `git branch -vv`
* Pushes bugfix to origin
    `git push -u origin bugfix`
* Removes bugfix from origin
    `git push -d origin bugfix`
* Removes bugfix from local
    `git branch -d bugfix`

## Github

* [配置公私钥](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh)
    配好后本地通过ssh协议传输就不需要输入账号密码了
    如果已经有repo的remote配置为HTTPS协议，需要更改相关地址如下：
    `git remote set-url origin git@github.com:USERNAME/REPOSITORY.git`

* [配置gpg key](https://gist.github.com/alopresto/b8d940197b4c314e27188a6852198d2d)

* 配置ssh代理

    ```sh
    Host github.com
    HostName github.com
    User git
    # 走 HTTP 代理
    ProxyCommand socat - PROXY:127.0.0.1:%h:%p,proxyport=7890
    # 走 socks5 代理（如 Shadowsocks ）
    # ProxyCommand nc -v -x 127.0.0.1:7890 %h %p
    ```

* [Commit Message Format](https://gist.github.com/develar/273e2eb938792cf5f86451fbac2bcd51)

* [Unfork a repository](https://ralphjsmit.com/unfork-github-repo/)

* Configure Git to use a proxy

    ```sh
    git config --global http.proxy 'http://127.0.0.1:7890'
    git config --global https.proxy 'http://127.0.0.1:7890'

## Reference 

* https://git-scm.com/



22

