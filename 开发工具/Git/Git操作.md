## Git 操作
### 如何撤销某次提交 git revert -m
* `git revert -n 1ab3sbs1023`
* `git revert -n [id] -m [1|2]`

#### 修改 commit 信息
* ` git commit --amend`

### 回滚某次提交 

`git reset --hard 版本号`

###  git 多户配置
```
[user]
    name = Garrit Franke
    email = garrit@slashdev.space

[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

[includeIf "gitdir:~/work/client2/"]
    path = ~/.gitconfig-client2

[includeIf "gitdir:~/sources/"]
    path = ~/.gitconfig-personal

# ...
```

### 重命名分支
1. 重命名本地分支
`git branch -m oldName newName`
2. 删除远程分支
`git branch --delete origin oldName`
3. 将新分支推送到远程
` git push --set-upstream origin newName`

### 撤回最近commit
```bash
git reset --soft HEAD^
```
