### 最常用
``` bash
git init                # 初始化 Git 仓库
git status              # 查看当前文件状态（最常用！）

git add 文件名           # 添加单个文件到缓存
git add .               # 添加所有修改（最常用）

git commit -m "说明"     # 提交并写备注（-m = message）
```
### 分支操作
```bash
# 查看所有本地分支
git branch

# 查看所有分支（含远程）
git branch -a

# 创建新分支
git branch 分支名

# 切换分支
git checkout 分支名
# 或新版命令
git switch 分支名

# 创建并直接切换到新分支
git checkout -b 分支名
git switch -c 分支名

# 合并指定分支到当前分支
git merge 分支名

# 删除本地分支
git branch -d 分支名

# 强制删除未合并的分支
git branch -D 分支名
```
### 存储提交
``` bash
# 查看文件修改状态（红=未暂存，绿=已暂存）
git status

# 查看简洁状态
git status -s

# 查看提交历史
git log

# 查看简洁单行日志
git log --oneline

# 查看所有分支的提交图谱（非常好用）
git log --graph --oneline --all
```

``` bash
# 添加单个文件到暂存区
git add 文件名

# 添加所有修改/新增文件
git add .

# 提交到本地仓库（必须写备注）
git commit -m "提交说明"

# add + commit 一步完成（只适用于已追踪文件）
git commit -am "提交说明"
```
### 撤销 / 回退（非常重要）
``` bash
# 撤销工作区修改（恢复到最近一次提交）
git checkout -- 文件名
git restore 文件名

# 撤销暂存区文件（取消 add）
git reset HEAD 文件名
git restore --staged 文件名

# 回退到上一个提交（保留代码，仅撤销提交）
git reset --soft HEAD^

# 彻底回退到上一个版本（代码也会消失，谨慎！）
git reset --hard HEAD^

# 放弃所有修改，强制和远程保持一致
git reset --hard origin/主分支名
```

### 储藏（临时保存未提交代码）
``` bash
# 储藏当前修改
git stash

# 查看储藏列表
git stash list

# 恢复最近一次储藏
git stash pop

# 删除所有储藏
git stash clear
```
### 设置代理
``` bash
# 使用clash的端口号不加global 只在当前分支生效 如果想要全局生效那么加global
git config http.proxy http://127.0.0.1:7897
git config https.proxy http://127.0.0.1:7897
```

### 远端地址
``` bash
git remote set-url origin 
```

### commit历史修改
#### 邮箱姓名信息修改
``` bash
git filter-branch -f --env-filter '
OLD_EMAIL="原来的邮箱"
CORRECT_NAME="现在的名字"
CORRECT_EMAIL="现在的邮箱"
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```
注意 : 执行之前要清理工作区