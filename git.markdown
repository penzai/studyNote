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
#新建并切换到该分支
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
> 不同祖先合并分支时，需要输入一次"合并commit"

## 撤销
### 放弃文件的修改
```
git checkout -- 文件
```
### 放弃提交
保留文件
```
git reset SHA号
```
> 此类更新不更改工作区，所以如果你更新了版本3，然后回到版本1，版本3的内容还在工作区，只是变成了修改状态
不保留工作区的文件
```
git reset --hard SHA号
```
### 回退
回退到某个版本后，后面的不要
```
# hard覆盖本地工作区，使之与版本号1内的文件一样
git reset --hard 版本号1
# 不加-f报错，工作区版本比远程库旧
git push -f
```
> 你回退了版本，当其他地方进行git pull时，可能对方的HEAD指针指向比版本号1更前的地方，这时可使用git reset变更指针未知


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