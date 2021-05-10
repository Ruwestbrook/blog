---
title: git使用记录
date: 2019-06-26 11:25:31
tags:
---


# git 分支提交


#查看当前所有分支
git brach


#查看远程所有分支
git branch -r

#查看所有分支 (包括远程和当前)
git branch -a


# git版本回退

刚来公司,用的是码云 git的方式管理项目,然后发现自己提交错了,还好可以回退

```git
git reset --hard <版本号>
// 注意使用 --hard 参数会抛弃当前工作区的修改
// 使用 --soft 参数的话会回退到之前的版本，但是保留当前工作区的修改，可以重新提交


git push origin <分支名> --force 远程仓库就回退版本了

ps:虽然回退了版本,但是推送记录中还是存在你之前推送的记录


```



 # git stash 工作状态切换 

  (PS:公司的项目结构比较复杂,模块分的很多,负责了模块后,发现每个模块还根据版本切换了不同分支,所以需要在不通过分支上开发,然后切换工作状态,所以很有必要学会stash的用法) 

git stash 保存当前工作状态,然后恢复到上一次提交

git stash save " " 跟git stash功能一样,但是多了一个当前工作状态的描述

git stash pop 删除最后一次的git stash记录,然后把这次的stash恢复到当前文件夹

git stash drop "stash id" 删除最后一次的git stash记录,但是并不会恢复到当前工作状态 如果跟了id,那么就删除某一个git stash

git stash clear 删除所有的git stash记录  但是不会恢复任何文件

git stash list  查看所有的stash工作状态列表   可以跟--date=local 输出保存的时间

git stash apply "stash id" 切换到某个工作状态 后面跟的id可以用上面的list查询 一般都为stash@{0}  但是不会删除这个记录  如果不跟id,那么就恢复最近的一次提交

git stash show "stash id" 查看stash工作状态和当前工作状态的对比  可以跟-u 所有文件的差异



# git  revert    
```


git  revert  回退到指定版本, 需要解决冲突

 删除某一个commit

```

 实例: 文件夹中存在三个文件分三此提交到git 使用git log 可以看到提交记录 现在使用
git revert commit-id(提交a.text的commit-id) 然后发现文件夹中a.txt消失,也就是这次的commit被取消了
然后使用git push 提交 返现远程仓库也删除了这个文件,但是保留了全部的commit


# git reset 

 git reset -- hard commit-id

--hard :会删除commit-id 到本地的提交记录
--soft :保存所有记录到工作区
 
回退到某个commit
 还是上面的例子,然后commit之后使用log查看日志 然后reset当提交c.txt的commit-id,会发现a.txt已经回来了,然后使用
git push -f 强制提交,然后之前的revert的commit的记录已经被删除了 本地使用git log发现本地的记录也被删除了


git reset 适用于本地有多个commit但是不想提交了,需要回撤commit