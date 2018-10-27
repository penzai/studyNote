## 合并提交
```
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```


## 分支
```
git branch
git branch 新分支名字
git checkout 分支名字
git branch -b 新分支名字 
```
分支有更改时，不能直接切。需要提交或者暂存
```
git stash
```
暂存后此时的代码的会变成未修改时。
切换回来时，需要弹出
```
git stash pop
```
合并分支
```
git merge
```
所以一般操作流程为。
创建主干，在主干基础上添加一个分支，在分支上进行提交，切换到主干合并分支

## 撤销
### 合并
第一次提交了，发现还有几个文件没提交
```
git commit --amend
```
### 放弃文件的修改
```
git checkout -- 文件
```
### 放弃提交
保留文件
```
git reset SHA号
```
不保留工作区的文件
```
git reset --hard SHA号
## 查看日志
### 查看细节变动
```
git log -p -2
```
### 查看文件变动
```
git log --stat -2
```