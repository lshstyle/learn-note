[TOC]

#### 1.克隆远程版本库

git clone <url>

#### 2.当前分支创建新分支，并切换到新分支

git checkout -b <newbranch>

git checkout -b <newbranch> origin/branch (基于某个分支建新分支，适用于当前分支不是需要的分支)

#### 3.密码一次输入

git config —global credential.helper store

#### 4.删除远程分支

git push origin —delete <branch>

#### 5.删除本地分支

git branch -d <branch>

6.