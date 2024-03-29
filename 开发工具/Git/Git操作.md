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
