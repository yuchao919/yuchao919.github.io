# Git傻瓜备忘录

## 命令太多记不住？概念啥的搞不懂？

多看几遍就会了吧。

* 主机
* 账户
* 仓库
* 版本号*commit-id*
* 分支

## 单主机-单账户-单仓库

在本地主机使用单个账户使用Github远程仓库

1. 安装Git
1. 配置账户
    1. 查看本地账户配置，确认本地信息是空的
        > git config --global -l 
    1. 配置本地账户信息
        > git config --global user.name "*Your Name*"  
        > git config --global user.email "*email@example.com*"
1. 本地仓库
    1. 创建文件夹
        > mkdir *FolderName*  
        > cd *FolderName*
    1. 在文件夹路径下初始化本地仓库
        > git init
    1. 创建文件
        > touch *FileName*
    1. 把文件添加到仓库，可以用 . 来代表添加所有文件(实际上就是把文件修改添加到暂存区)
        > git add *FileName*
    1. 把文件提交到仓库，提交的是上一次add的文件，引号内为本次提交的注释内容(实际上就是把暂存区的所有内容提交到当前分支)
        > git commit -m "*wrote a readme file*"
    1. 查看仓库情况
        > git status
    1. 修改文件后，查看修改的地方
        > git diff FileName
    1. 查看提交记录，修饰符为查看结果显示为单行
        > git log --pretty=oneline
        * 显示结果前面为*commit-id*
    1. 把commit信息和所有修改过的文件退回到上一次修改
        > git reset --hard HEAD^
    1. 把commit信息和修改的文件退回到某一个版本（不一定能恢复，谨慎操作）
        > git reset --hard *commit-id*
    1. 恢复刚刚git reset --hard的误操作
        > git reflog(查看命令历史)  
        > git reset --hard *commit-id*
    1. 把文件撤回到修改前的状态
        > git checkout -- *FileName*
        * 一种是*FileName*自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态
        * 一种是*FileName*已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
        * git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令。
    1. 把文件撤回到当前版本的状态
        > git reset HEAD *FileName*     // 这一步是把暂存区的文件给改了  
        > git checkout -- *FileName*    // 再把当前文件恢复成暂存区状态
    1. 删除本地文件
        > git rm *FileName*

1. 远程仓库
    1. 添加密钥到远程仓库
        * 添加本地密钥
            > ssh-keygen -t rsa -C "*youremail@example.com*"
        * 把~/.ssh/id_rsa.pub的内容上传到Github-->setting-->SSH and GPG keys的SSh Keys中c
    1. 创建远程仓库
    1. 绑定远程仓库
        > git remote add origin git@*server-name(e.g. github.com)*:*Your Name*/*Your Repository Name*.git
        * Git默认远程库的名字就是 origin
    1. 把本地库的所有内容推送到远程库上
        > git push -u origin master
        * 由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
    1. 克隆一个仓库
        > git clone git@*server-name(e.g. github.com)*:*Your Name*/*Your Repository Name*.git  
        > git clone https://*server-name(e.g. github.com)*/*Your Name*/*Your Repository Name*.git
        * 上面二者都可以克隆一个远程仓库，前者用ssh协议，需要已添加过ssh keys，速度较后者快；后者https协议，每次推送都需要口令
        * 可以添加 --depth 1 选项，下载当前最新内容，不包括log信息等冗余信息
1. 分支操作
    1. 创建分支
        > git branch *BranchName*
    1. 切换到分支
        > git checkout *BranchName*
        * 上两步合并在一起 git checkout -b *BranchName*
    1. 查看当前分支
        > git branch
    1. 合并分支
        > git checkout *master* // 先切回到主分支上来  
        > git merge *BranchName*  // 把之前分支合并到master分支上去
        * 要合并的内容也要在分总中add-->commit
        * merge操作包含在master分支做了一次commit操作，实质把分支中的commit合并到master中来了
    1. 删除分支
        > git branch -d *BranchName*
    1. 分支冲突  
        * 当分支和master中都对某个文件做了修改，然后在master使用merge时就会发生冲突
        * 在vscode合并冲突会有修改提示，命令行中没有提示
    1. 强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息
        > git merge --no-ff -m "*merge with no-ff*" dev
        * 因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。
    1. 保护开发工作区  
        场景如下：正在*dev*分支做开发，然后发现了紧急bug，需要修改bug并提交到master分支  
        1. 当前在*dev*分支，隐藏该分支未提交内容
            > git stash
        1. 切换到master
            > git checkout master
        1. 创建修BUG分支*issue-101*
            > git checkout -b *issue-101*
        1. 修复BUG并提交
            > git commit -a -m "*fix bug 101*"
        1. 合并到master并发布
            > git checkout master  
            > git merger --on-ff -m "*merge bug fix 101*" *issue-101*
        1. 回到*dev*继续开发工作
            > git checkout *dev*  
            > git merge master // 把本地master分支修复的bug后的文件合并到当前开发中来
            > git stash list  
            > git stash apply + git stash drop 或者 git stash pop
    1. 强行删掉一个未合并的分支
        > git brand -D *BranchName*
    1. 多人协作
        1. 查看远程仓库
            > git remote -v // -v显示详细信息
        1. 推送远程分支
            > git push *origin* *BranchName* // 远程仓库名字一般默认*origin*，远程默认分支一般为*master*(e.g. git push *origin* *dev*)
        1. 在远程dev分支上开发
            1. 克隆仓库到本地
                > git clone *git*@*Git-Server*:*Your-Name*/*Repo-Name*.git
            1. 创建本地开发分支
                > git checkout -b *dev* *origin/dev* 
            1. 建立本地开发分支和远程开发分支的关系
                > git branch --set-upstream *dev* *origin/dev* 
                * 貌似可以直接 
                > git pull *origin* *dev*
1. 标签管理
    1. 查看标签列表
        > git tag
    1. 创建标签
        > git tag *TagName*
    1. 给以前的*commit-id*打标签
        > git tag *TagName* *commit-id*
    1. 查看标签信息
        > git show *TagName*
    1. 多选项
        > git tag -a *Tagname* -m "*Tag-Comment*" *commit-id*
    1. 可以用PGP签名标签??
        > git tag -s *TagName* -m "blablabla.."
    1. 删除标签
        > git tag -d *TagName*
    1. 推送标签到远程仓库
        > git push *origin* *TagName*
        * 一次性推送全部尚未推送到远程的本地标签
        > git push origin --tags
    1. 删除远程标签
        > git tag -d *TagName* // 先删除本地标签  
        > git push *origin* :refs/tags/*TagName* // 远程删除

## 单主机-单账户-多远程仓库

以码云Gitee为例

1. 删除已关联的名为origin的远程库
    > git remote rm *origin*
1. 关联远程Gitee远程库远程库
    > git remote add *gitee* git@gitee.com:*Your-Name*/*Repo-Name*.git

## 杂项

1. .gitignore文件
