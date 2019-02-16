## 概览图

![](img/git/git.png)

## 规范提交

### 一键

安装后，即可使用 git cz 进行快速提交

```
npm i -g commitizen
commitizen init cz-conventional-changelog --save --save-exact
```

### 检查提交是否符合规定

```
npm i -D validate-commit-msg husky
```

安装后在 package.json 中的 scripts 字段中添加下列字段，提交时，不符合规定的 commit msg 不给与提交

```
"commitmsg": "validate-commit-msg"
```

## 比较异同

```
# 比较工作区与暂存区
git diff

# 比较暂存区与版本库
git diff --cached

# 比较工作区与版本库
git diff HEAD
```

## 用户信息

### 查看

```
git config user.name
git config user.email
```

### 更改

```
git config --global user.name '修改后用户名'
git config --global user.email '修改后邮箱'
```

## 分支

```
# 查看本地分支
git branch

# 新建分支
git branch 新分支名字

# 切换分支
git checkout 分支名字

# 新建并切换到该分支
git checkout -b 新分支名字

# 推送未关联的分支到远程主机
git push --set-upstream origin 本地分支

```
### 拉取一个远程分支
```
# 查看远程分支
git branch -r
# 拉取
git checkout -b 新的本地分支名 origin/远程分支
# (拉取后可用git branch -vv检查是否已关联上)
```

### 暂存分支

分支有更改时，不能直接切。需要提交或者暂存

```
git stash
```

暂存后此时的代码的会变成未修改时。
切换回来时，需要弹出

```
git stash pop
```

### 合并分支

```
git merge
```

合并分支时，有时会产生冲突，这时需要你手动更改下列类似文件（冲突文件），提交后即合并成功

> <<< 指工作区的文件，>>> 指传入的更改, === 用于分割彼此

```
<<<<<<< HEAD
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
  please contact us at support@github.com
</div>
>>>>>>> iss53
```

### 开发流程

一般开发为。创建主干，在主干基础上添加一个分支，在分支上进行提交，切换到主干合并分支

> 不同祖先合并分支时，需要输入一次"合并 commit"

## 回退

### 回退单个文件

不加 commit 即为最新的提交记录

```
git checkout <commit> -- <filename>
```

### 放弃  后面的提交 reset

保留工作区文件

```
git reset SHA号
```

> 此类更新不更改工作区，所以如果你更新了版本 3，然后回到版本 1，版本 3 的内容还在工作区，只是变成了修改状态

不保留工作区的文件

```
git reset --hard SHA号
```

此时提交可能会产生错误，需要强制提交。因为你回退了版本，当其他地方进行 git pull 时，可能对方的 HEAD 指针指向比版本号 1 更前的地方，这时可使用 git reset 变更指针位置

```
git reset --hard 版本号1
git push -f
```

### 需要保留提交历史的回退 revert
针对一次或多次的提交进行回退，过去的提交历史会保留
```
# 回退指定版本
git revert 版本号

# 回退多个版本[3,1)
git revert master~3..master~1

# 不产生新的”回退记录“
git revert -n
```


### 修改 commit 注释

修改最后一次 commit 注释

```
git commit --amend
```

#### 修改历史

1.运行命令弹出历史提交信息，

```
git rebase -i master~5
```

2.修改历史那一条的 pick 字段为 edit 字段，此时修改那一条历史变为最近一次提交，然后修改 3.回到如初

```
git rebase --continue
```
> 灵活运用git stash与git stash pop更方便对历史commit进行修改

## 查看日志

### 查看细节变动

```
git log -p -2
```

### 查看文件变动

```
git log --stat -2
```

## 更新

取回远程主机的某个分支更新

```
git fetch origin 分支名
```

把远程分支取到另一个本地分支里

```
git checkout -b 新的本地分支 主机名/远程分支
```

建立追踪关系，在现有分支与指定的远程分支之间

```
git branch --set-upstream-to=origin/<branch> 本地分支
```
