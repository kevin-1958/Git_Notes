# Git 速查表

> 一页覆盖日常 90% 的 Git 操作。配套详解：[Git 学习笔记](Git.md)
> 约定：`ID` = commit ID（`git log --oneline` 每行开头那串字符，如 `a1b2c3d`）。

---

## 一、30 秒速查

| 我想…… | 命令 |
| :--- | :--- |
| 初始化仓库 | `git init` |
| 下载别人的仓库 | `git clone 地址` |
| 看当前状态 | `git status` |
| 暂存所有改动 | `git add .` |
| 提交 | `git commit -m "说明"` |
| 看提交历史 | `git log --oneline --graph --all` |
| 看改了什么 | `git diff` / `git diff --staged` |
| 拉取远程最新 | `git pull` |
| 推送 | `git push` |
| 新建并切到分支 | `git switch -c dev` |
| 切换分支 | `git switch dev` |
| 合并分支 | `git merge dev` |
| 撤销暂存 | `git restore --staged 文件` |
| 回退提交 | `git reset --soft ID` |
| 后悔了 | `git reflog` |
| 大文件走 LFS | `git lfs track "*.psd"` |
| 看 LFS 文件清单 | `git lfs ls-files` |
| 补下载 LFS 文件 | `git lfs pull` |

---

## 二、日常标准流程

**第一次使用（只做一次）**

```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
git config --global --list          # 验证
```

**开始一个本地项目**

```shell
git init                            # 初始化仓库
git add .                           # 把所有改动放入暂存区
git commit -m "首次提交"             # 提交到本地仓库
git remote add origin 远程地址       # 关联远程仓库
git push -u origin main             # 首次推送并绑定（之后直接 git push）
```

**拉取别人的项目**

```shell
git clone 远程地址                   # HTTPS 或 SSH 地址均可
cd 仓库名
```

**每天循环**

```shell
git pull                            # 1. 先拉，同步远程最新
# ...写代码...
git status                          # 2. 看看改了哪些文件
git add .                           # 3. 暂存
git commit -m "完成xx功能"           # 4. 提交
git push                            # 5. 推送
```

> 一句话：**推之前先拉，冲突手动解，解完再推。**

---

## 三、配置

| 命令 | 作用 |
| :--- | :--- |
| `git config --global user.name "名字"` | 设置用户名（全局） |
| `git config --global user.email "邮箱"` | 设置邮箱（全局） |
| `git config --global --list` | 查看全部全局配置 |
| `git config --global user.name` | 查看单项配置 |
| `git config --list` | 查看当前仓库生效的全部配置 |
| `git config --global core.editor "code --wait"` | 设置默认编辑器（示例为 VS Code） |
| `git config --global init.defaultBranch main` | 设置新仓库默认分支名为 `main` |
| `git config --global --unset user.name` | 删除某项配置 |
| `git help 命令` / `git 命令 -h` | 查看命令帮助（如 `git help commit`） |

**配置层级：**

| 参数 | 作用范围 | 配置文件 |
| :--- | :--- | :--- |
| `--global` | 当前用户的所有仓库 | 用户目录下的 `.gitconfig` |
| `--local`（默认） | 仅当前仓库 | 仓库内 `.git/config` |
| `--system` | 本机所有用户 | 系统级配置文件 |

---

## 四、创建与克隆

| 命令 | 作用 |
| :--- | :--- |
| `git init` | 在当前目录创建本地仓库 |
| `git init 目录名` | 新建目录并初始化为仓库 |
| `git clone 远程地址` | 克隆仓库（生成同名文件夹） |
| `git clone 远程地址 自定义目录名` | 克隆到指定目录 |
| `git clone -b dev 远程地址` | 只克隆指定分支 |
| `git clone --depth 1 远程地址` | 浅克隆（只取最近一次提交，网慢时用） |
| `git branch -M main` | 把当前分支重命名为 `main` |

---

## 五、暂存与提交

| 命令 | 作用 |
| :--- | :--- |
| `git status` | 查看工作区 / 暂存区状态 |
| `git add 文件` | 把指定文件加入暂存区 |
| `git add .` | 把当前目录所有改动加入暂存区 |
| `git add -A` | 加入全部改动（含删除的文件） |
| `git add -p` | 交互式逐块选择要暂存的改动 |
| `git commit -m "说明"` | 提交暂存区内容到本地仓库 |
| `git commit -a -m "说明"` | 跳过 `add`，提交**已追踪**文件的改动 |
| `git commit -m "标题" -m "正文"` | 提交信息带正文 |
| `git commit --amend -m "新说明"` | 修改上一次提交的信息（不新增提交） |
| `git commit --amend --no-edit` | 把新暂存的改动追加到上一次提交 |
| `git rm --cached 文件` | 从 Git 移除追踪，**保留本地文件** |
| `git rm 文件` | 删除文件并记录这次删除 |
| `git mv 旧名 新名` | 重命名（等价于 mv + add） |

> ⚠️ `-a` 只对已被追踪的文件生效，**新建的文件仍然需要先 `git add`**。
> ⚠️ `--amend` 会改写历史，只建议在**未推送之前**使用。

---

## 六、查看与对比

| 命令 | 作用 |
| :--- | :--- |
| `git status` | 整体状态 |
| `git diff` | 工作目录 ↔ 暂存区（还没 add 的改动） |
| `git diff --staged` | 暂存区 ↔ 本地仓库（已经 add 的改动） |
| `git diff 文件` | 只看某个文件的差异 |
| `git diff ID1 ID2` | 比较两次提交的差异 |
| `git show` | 最近一次提交的详情 |
| `git show ID` | 指定提交的详情 |
| `git show ID:文件路径` | 查看某次提交里某个文件的内容 |
| `git log` | 完整提交历史 |
| `git log --oneline` | 每个提交一行 |
| `git log --graph` | 图形化展示分支 |
| `git log --all --graph --oneline` | 全部分支 + 图形 + 单行（最常用） |
| `git log -n 5` | 只看最近 5 条 |
| `git log 文件` | 某个文件的修改历史 |
| `git log --author="名字"` | 按提交人筛选 |
| `git log -p` | 显示每次提交的具体改动 |
| `git blame 文件` | 逐行显示最后修改人和提交 |
| `git shortlog -sn` | 统计每个人的提交次数 |

---

## 七、撤销与回退

### 7.1 还没提交：撤销工作区 / 暂存区

| 命令 | 作用 |
| :--- | :--- |
| `git restore 文件` | 丢弃工作区里该文件的修改 ⚠️ 不可恢复 |
| `git restore .` | 丢弃所有工作区修改 ⚠️ |
| `git restore --staged 文件` | 把文件移出暂存区（**保留**修改） |
| `git reset HEAD 文件` | 同上，旧版本写法 |
| `git restore --source=ID 文件` | 把文件恢复到指定提交的版本 |

### 7.2 已经提交：回退版本

| 命令 | 本地仓库 | 暂存区 | 工作目录 | 适用场景 |
| :--- | :--- | :--- | :--- | :--- |
| `git reset --soft ID` | 回退 | 保留 | 不动 | 想重新提交 |
| `git reset --mixed ID` | 回退 | 清空 | 不动 | 想重新 add（默认） |
| `git reset --hard ID` | 回退 | 清空 | 清空 | 彻底放弃修改 ⚠️ 慎用 |

> 口诀：**soft 只动仓库，mixed 再动暂存区，hard 连工作目录一起清空。**

常用写法：

```shell
git reset --hard HEAD^      # 回到上一次提交（丢一次提交的改动）
git reset --hard HEAD~2     # 回到前两次提交
git reset --hard HEAD       # 丢弃所有未提交改动（提交不动）
```

### 7.3 撤销已推送的提交 / 找回丢失的提交

| 命令 | 作用 |
| :--- | :--- |
| `git revert ID` | 生成一次反向提交抵消该提交（**不改写历史，已推送也能用**） |
| `git revert HEAD` | 撤销最近一次提交 |
| `git reflog` | 查看 HEAD 操作历史，找回被 reset 掉的提交 |
| `git reset --hard ID` | 配合 `reflog` 里的 ID 回到任意历史状态 |

---

## 八、分支

| 命令 | 作用 |
| :--- | :--- |
| `git branch` | 查看本地分支（`*` 为当前分支） |
| `git branch -r` | 查看远程分支 |
| `git branch -a` | 查看本地 + 远程分支 |
| `git branch -vv` | 查看分支及其绑定的远程分支 |
| `git branch dev` | 新建分支 `dev`（仍停留在当前分支） |
| `git switch dev` | 切换分支（新版命令） |
| `git switch -c dev` | 创建并切换 |
| `git checkout dev` | 切换分支（旧版命令，仍通用） |
| `git checkout -b dev` | 创建并切换（旧版写法） |
| `git branch -m 新名` | 重命名当前分支 |
| `git branch -d dev` | 删除已合并分支 |
| `git branch -D dev` | 强制删除未合并分支 |
| `git branch --merged` | 查看已合并到当前分支的分支 |

> `git switch` / `git restore` 需要 **Git 2.23+**；版本较旧时用 `git checkout`。

### 8.1 合并与变基

| 命令 | 作用 |
| :--- | :--- |
| `git merge dev` | 把 `dev` 合并到当前分支 |
| `git merge --no-ff dev` | 合并时强制生成合并提交（保留分支痕迹） |
| `git merge --abort` | 放弃本次合并，回到合并前 |
| `git rebase main` | 把当前分支的提交"搬"到 `main` 最新提交之后 |
| `git rebase --continue` | 解决冲突后继续变基 |
| `git rebase --abort` | 放弃变基 |
| `git cherry-pick ID` | 把其他分支的某次提交"复制"到当前分支 |
| `git cherry-pick ID1 ID2` | 一次复制多个提交 |

**merge 与 rebase：**

| 对比项 | `git merge` | `git rebase` |
| :--- | :--- | :--- |
| 历史形态 | 保留分叉，多一个合并提交 | 变成一条直线 |
| commit ID | 不变 | 被改写（产生新 ID） |
| 是否安全 | 安全，可用在公共分支 | **只用于本地分支** |

---

## 九、远程仓库与同步

| 命令 | 作用 |
| :--- | :--- |
| `git remote -v` | 查看远程仓库地址 |
| `git remote add origin 地址` | 添加远程仓库并命名为 `origin` |
| `git remote set-url origin 新地址` | 修改远程地址 |
| `git remote remove origin` | 删除远程关联 |
| `git remote rename 旧名 新名` | 重命名远程 |
| `git fetch origin` | 抓取远程更新，**不合并** |
| `git fetch -p` | 抓取并清理本地失效的远程分支记录 |
| `git pull` | 抓取 + 合并当前分支（= `fetch` + `merge`） |
| `git pull origin dev` | 拉取指定远程分支 |
| `git pull --rebase` | 用变基方式拉取，历史更直 |
| `git push` | 推送当前分支到已绑定的远程分支 |
| `git push -u origin dev` | 推送新分支并绑定上游（之后直接 `git push`） |
| `git push origin main:test` | 把本地 `main` 推送到远程 `test` |
| `git push --force-with-lease` | 强制推送（比 `--force` 安全，会检查远程是否被他人更新） |
| `git push origin --delete dev` | 删除远程分支 |
| `git remote prune origin` | 清理本地已失效的远程分支记录 |

**fetch 与 pull：**

| 命令 | 作用 | 是否自动合并 |
| :--- | :--- | :--- |
| `git fetch` | 只把远程更新抓到本地 | ❌ 不合并 |
| `git pull` | 抓取 + 合并到当前分支 | ✅ 自动合并 |

---

## 十、忽略文件（.gitignore）

```gitignore
# 忽略所有 .log、.txt 文件
*.log
*.txt

# ! 表示开例外：不忽略这个文件
!important.txt

# 忽略整个目录
build/
test/

# 只忽略本目录下的 txt（不含子目录）
xxx/*.txt

# 忽略 xxx 目录（含任意层级子目录）中的 txt
xxx/**/*.txt

# 以 / 开头：只匹配仓库根目录下的 config
/config
```

| 命令 | 作用 |
| :--- | :--- |
| `git check-ignore -v 文件` | 查看是哪条规则忽略了该文件 |
| `git rm --cached 文件` | 让**已追踪**的文件不再被追踪（本地文件保留） |

---

## 十一、协作与冲突处理

**推送被拒绝**（`! [rejected] ... (fetch first)`）：说明远程有别人的新提交，先拉再推。

```shell
git pull                # 拉取并合并
# 解决冲突（如有）
git push                # 再次推送
```

**冲突文件的标记含义：**

```
<<<<<<< HEAD
当前分支的内容
=======
要合并进来的内容
>>>>>>> origin/main
```

**解决冲突三步：**

```shell
# 1. 打开冲突文件，只保留想要的内容，删掉 <<<<<<< ======= >>>>>>>
# 2. 标记为已解决
git add 冲突文件
# 3. 提交
git commit -m "解决合并冲突"
git push
```

**想放弃这次合并：**

```shell
git merge --abort       # 合并冲突时
git rebase --abort      # 变基冲突时
git cherry-pick --abort # 优选冲突时
```

---

## 十二、危险操作清单

| 命令 | 风险 | 说明 |
| :--- | :--- | :--- |
| `git reset --hard ID` | ⚠️⚠️⚠️ | 丢弃工作区与暂存区所有未提交改动，无法找回 |
| `git restore 文件` | ⚠️⚠️ | 丢弃该文件的未提交修改，无法找回 |
| `git clean -fd` | ⚠️⚠️⚠️ | 删除所有未追踪的文件和目录 |
| `git push --force` | ⚠️⚠️⚠️ | 覆盖远程历史，可能抹掉他人提交；优先用 `--force-with-lease` |
| `git rebase` / `git commit --amend` | ⚠️⚠️ | 改写历史，**不要对已推送并被人使用的分支使用** |
| `git branch -D dev` | ⚠️ | 强删未合并分支，提交可能丢失 |
| `git rm -r --cached .` | ⚠️⚠️ | 清空整个索引的追踪，容易误伤 |

> 只要提交过（`git commit`），即使 `reset --hard` 也能用 `git reflog` 找回；
> **从未提交过的改动，被 `restore` / `clean` 删掉就真的没了。**

---

## 十三、常见报错速查

| 报错 | 原因 | 解决 |
| :--- | :--- | :--- |
| `! [rejected] ... (fetch first)` | 远程有别人的新提交 | `git pull` 后重新 `git push` |
| `CONFLICT (content): Merge conflict in 文件` | 双方改了同一处 | 手动解决冲突 → `git add` → `git commit` |
| `Please tell me who you are` | 没配置用户名邮箱 | `git config --global user.name/user.email` |
| `fatal: not a git repository` | 当前目录不是仓库 | `cd` 到仓库目录，或先 `git init` |
| `Authentication failed` | 令牌过期 / 权限不足 / 用了 SSH 地址 | 重新生成令牌（勾选 `repo`），或改用 SSH |
| `Support for password authentication was removed` | 输的是账号密码 | 改用 Personal Access Token |
| `Permission denied (publickey)` | SSH 公钥没配好 | 添加公钥到 GitHub，检查 SSH 地址格式 |
| `Host key verification failed` | 本机不信任 GitHub 主机 | `ssh-keyscan github.com >> ~/.ssh/known_hosts` |
| `Updates were rejected because the tip ... is behind` | 本地落后于远程 | `git pull --rebase` 或 `git pull` |
| `error: Your local changes would be overwritten` | 切换/拉取会覆盖未提交改动 | 先 `git stash` 或提交后再操作 |
| `fatal: refusing to merge unrelated histories` | 两个仓库历史无关 | 确认无误后加 `--allow-unrelated-histories` |
| `nothing to commit, working tree clean` | 没有改动可提交（不是错误） | 检查是否漏了 `git add` |

---

## 十四、进阶小技巧

| 命令 | 作用 |
| :--- | :--- |
| `git stash` | 临时收起未提交改动（切分支前很实用） |
| `git stash pop` | 恢复并删除最近一次 stash |
| `git stash list` | 查看 stash 列表 |
| `git tag v1.0.0` | 给当前提交打标签 |
| `git tag -a v1.0.0 -m "说明"` | 打附注标签 |
| `git push origin --tags` | 推送所有标签 |
| `git -C 路径 命令` | 在指定目录执行 Git 命令（无需 cd） |
| `git config alias.st status` | 设置别名，之后可用 `git st` |
| `git log --oneline --grep="关键词"` | 按提交信息搜索 |
| `git diff --stat` | 只看改动文件与行数统计 |
| `git ls-files` | 列出所有被追踪的文件 |

**推荐的别名配置：**

```shell
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --all --graph --oneline --decorate"
```

---

## 十五、Git LFS（大文件，增删改查）

> LFS 把大文件存成"指针"，真实内容放到 LFS 存储区，避免仓库被撑爆（GitHub 单文件上限 100 MB）。
> 前置：`git lfs install` 装一次；**协作者没装 LFS，克隆下来只会得到一个指针文件**。
> 下面按 **增 → 查 → 改 → 删 → 日常使用** 排列。

### 15.1 安装与启用

| 命令 | 作用 |
| :--- | :--- |
| `winget install GitHub.GitLFS` | Windows 安装 LFS（或去 git-lfs.com 下载） |
| `brew install git-lfs` | macOS 安装 |
| `sudo apt install git-lfs` | Ubuntu / Debian 安装 |
| `git lfs version` | 确认 LFS 已安装 |
| `git lfs install` | 全局启用 LFS（推荐） |
| `git lfs install --local` | 只对当前仓库启用 |

### 15.2 增：添加追踪规则

| 命令 | 作用 |
| :--- | :--- |
| `git lfs track "*.psd"` | 按后缀追踪（最常用） |
| `git lfs track "*.mp4" "*.zip"` | 一次追踪多种类型 |
| `git lfs track "data/model.bin"` | 追踪某个具体文件 |
| `git lfs track "assets/**"` | 追踪某个目录下的所有文件 |
| `git add .gitattributes` | **必须提交规则文件**，否则别人拿不到规则 |
| `git commit -m "配置 LFS"` | 提交规则 |

顺序不能错：**先 `track` → 再 `add` 大文件 → 再 commit**。
规则写在仓库根目录的 `.gitattributes` 里，形如 `*.psd filter=lfs diff=lfs merge=lfs -text`。

### 15.3 查：查看状态

| 命令 | 作用 |
| :--- | :--- |
| `git lfs ls-files` | 列出当前版本被 LFS 追踪的文件 |
| `git lfs ls-files -s` | 附带标记与文件大小 |
| `git lfs ls-files -n` | 只显示文件名 |
| `git lfs ls-files --all` | 列出所有历史版本中出现过的 LFS 文件 |
| `git lfs track` | 查看当前生效的追踪规则 |
| `git lfs status` | 查看 LFS 相关的待提交改动 |
| `git lfs env` | 查看 LFS 端点、版本等运行环境 |
| `git show HEAD:文件` | 输出 `version https://git-lfs...` 说明该文件走了 LFS |

### 15.4 改：修改规则 / 改写历史

| 命令 | 作用 |
| :--- | :--- |
| `git lfs track "*.iso"` | 追加追踪规则 |
| `git lfs untrack "*.zip"` | 取消某后缀的追踪 |
| 编辑 `.gitattributes` 删行 | 与 `untrack` 效果相同 |
| `git lfs migrate import --include="*.psd,*.mp4"` | 把**已提交的历史**整体改写成 LFS |
| `git lfs migrate import --include="*.zip" --include-ref=refs/heads/main` | 只处理指定分支 |
| `git lfs migrate export --include="*.mp4" --everything` | 把某类型改回普通 Git 存储 |
| `git lfs push --all origin main` | 补传 LFS 对象（报 missing objects 时用） |

> ⚠️ `migrate` 会重写历史（commit ID 全变），执行前先用 `--dry-run` 确认并通知团队，推送需要 `--force-with-lease`。

### 15.5 删：删除与清理

| 命令 | 作用 |
| :--- | :--- |
| `git rm --cached 大文件.mp4` | 只从 Git 移除追踪，本地文件保留 |
| `git rm 大文件.mp4` | 删除文件并记录这次删除 |
| `git lfs prune --dry-run` | 查看能清理多少本地 LFS 缓存（先干跑一次） |
| `git lfs prune` | 清理本地不再引用的 LFS 缓存，回收磁盘 |

> 删除提交**不会**让历史里的旧大文件消失，仓库体积也不会变小。

### 15.6 日常使用与克隆

| 命令 | 作用 |
| :--- | :--- |
| `git add 大文件` | 配好规则后自动走 LFS，无需额外命令 |
| `git clone 远程地址` | 克隆时自动下载 LFS 文件（需已装 LFS） |
| `git lfs pull` | 补下载当前版本的 LFS 文件内容 |
| `git lfs fetch origin main` | 只抓取内容，不检出 |
| `git lfs checkout` | 把已抓取的内容替换到工作目录 |
| `git lfs pull --include="assets/*.psd"` | 只下载部分文件 |
| `GIT_LFS_SKIP_SMUDGE=1 git clone 地址` | 克隆时跳过 LFS 文件（网慢时用） |

**PowerShell 跳过 LFS 的写法：**

```powershell
$env:GIT_LFS_SKIP_SMUDGE=1; git clone 远程地址
```

### 15.7 常见问题

| 报错 / 现象 | 原因 | 解决 |
| :--- | :--- | :--- |
| 大文件只有几十字节，内容是 `version https://git-lfs...` | 没装 LFS 客户端 | `git lfs install` 后执行 `git lfs pull` |
| `This exceeds GitHub's file size limit of 100.00 MB` | 大文件没走 LFS 就提交了 | `git lfs track` 后重新提交；已提交的用 `migrate import` |
| 别人拿到的还是指针 | `.gitattributes` 没提交 | `git add .gitattributes` 并推送 |
| `track` 后文件仍进普通 Git | 先 `add` 了文件，后加规则 | `git rm --cached 文件` 后重新 `add` |
| `LFS upload missing objects` | 本地 LFS 对象没推全 | `git lfs push --all origin 分支` |
| `batch response: Authentication required` | 令牌没有 LFS 权限 | 重新生成令牌并勾选 `repo` |

---

## 附：commit ID 的简写

| 写法 | 含义 |
| :--- | :--- |
| `a1b2c3d` | 完整或部分 commit ID（一般取前 7 位即可） |
| `HEAD` | 当前提交 |
| `HEAD^` / `HEAD~1` | 上一个提交 |
| `HEAD~2` | 前两个提交 |
| `分支名` | 该分支的最新提交 |
