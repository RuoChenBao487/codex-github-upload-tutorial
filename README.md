# 让 Codex 帮你把内容上传到 GitHub：通用教程

> 适用对象：第一次使用 Git/GitHub 的用户，以及准备把上传任务交给 Codex 的用户。  
> 适用系统：Windows PowerShell 为主，Linux/macOS 命令基本通用。  
> 核心原则：用户负责账号、仓库和授权；Codex 负责检查、整理、提交、推送和验证，但不会擅自上传敏感信息。

## 1. 先理解 Codex 能做什么

Codex 可以：

- 检查项目目录是不是 Git 仓库。
- 初始化本地 Git 仓库。
- 查看当前修改、未跟踪文件和分支状态。
- 创建或修改 `.gitignore`。
- 检查明显的密码、Token、私钥和本地存档风险。
- 执行 `git add`、`git commit`。
- 配置或修改 GitHub 远程地址。
- 测试 SSH 连接。
- 执行 `git push`。
- 创建新分支并推送。
- 在配置了 GitHub CLI 的情况下创建 Pull Request。
- 推送后核对远程分支、提交哈希和仓库状态。

Codex 不能代替用户完成的事情，通常包括：

- 登录用户的 GitHub 账号。
- 代替用户通过手机验证码、2FA 或人机验证。
- 在不了解内容的情况下判断项目是否可以公开。
- 替用户决定仓库应该公开还是私有。
- 读取用户的 GitHub 密码。
- 安全地替用户保管长期 Token。
- 在未授权的情况下创建仓库、上传代码或修改远程历史。

Codex 可以生成 SSH 密钥，但通常需要用户自己把公钥添加到 GitHub 网页。  
Codex 可以使用已经配置好的 SSH 密钥，也可以使用已登录的 GitHub CLI。

## 2. 用户需要先准备什么

### 2.1 安装 Git

Windows 用户安装：

```text
https://git-scm.com/download/win
```

安装后在 PowerShell 检查：

```powershell
git --version
```

正常会显示类似：

```text
git version 2.xx.x.windows.1
```

### 2.2 创建 GitHub 账号

访问：

```text
https://github.com
```

注册并登录。建议提前设置好用户名和头像。

### 2.3 决定仓库名称、公开性和许可证

用户需要提前决定：

- 仓库名，例如 `my-python-tool`。
- 公开仓库还是私有仓库。
- 是否添加 MIT、Apache-2.0 等许可证。
- 项目描述。
- 默认分支名，推荐 `main`。

不要只说“帮我上传 GitHub”。最好明确给出：

```text
本地目录：D:\Projects\my-tool
GitHub 用户名：your-name
仓库名：my-tool
公开性：Private
分支：main
提交说明：Initial release of my-tool
是否新建仓库：是
使用 SSH：是
```

### 2.4 创建 GitHub 仓库

有两种方式。

#### 方式 A：用户在网页创建，Codex 负责推送

1. 登录 GitHub。
2. 点击右上角 `+`。
3. 选择 `New repository`。
4. 输入仓库名。
5. 选择 Public 或 Private。
6. 如果本地已经有 README 和 `.gitignore`，不要勾选自动生成这些文件。
7. 点击 `Create repository`。
8. 复制 SSH 地址。

SSH 地址通常类似：

```text
git@github.com:用户名/仓库名.git
```

如果 22 端口不可用，也可以使用：

```text
ssh://git@ssh.github.com:443/用户名/仓库名.git
```

#### 方式 B：使用 GitHub CLI 创建

如果电脑已经安装 GitHub CLI：

```powershell
gh --version
gh auth status
```

未登录时，由用户在自己的终端执行：

```powershell
gh auth login
```

登录后 Codex 可以执行：

```powershell
gh repo create 用户名/仓库名 --private --source . --remote origin
```

是否公开、仓库名和用户名必须由用户明确提供。

### 2.5 选择认证方式

推荐使用 SSH：

```text
用户把公钥添加到 GitHub
Codex 使用对应私钥推送
```

不推荐把 GitHub Token 直接粘贴到聊天中。  
如果必须使用 HTTPS Token，优先使用系统 Git Credential Manager，让用户在本机弹窗或凭据管理器中输入。

## 3. 用户应该如何向 Codex 提任务

### 3.1 最简提示词

```text
请把这个目录上传到 GitHub：

本地目录：D:\Projects\my-tool
GitHub 用户名：your-name
仓库名：my-tool
仓库类型：Private
分支：main
提交说明：Initial release of my-tool

请先检查敏感文件和大文件，创建或更新 .gitignore，
再初始化 Git、提交并推送。不要上传任何 Token 或私钥。
如果需要我登录 GitHub、创建仓库或添加 SSH 公钥，请明确告诉我。
```

### 3.2 上传到已有仓库的提示词

```text
请检查 D:\Projects\my-tool 并更新到已有 GitHub 仓库：

远程仓库：git@github.com:your-name/my-tool.git
分支：main
提交说明：Add new feature

请先查看 git status 和 diff；
只提交本项目中与本功能有关的文件；
不要覆盖或回退我已有的未提交修改；
不要上传 secrets、runtime、profiles、存档或构建产物；
推送后告诉我提交哈希和远程状态。
```

### 3.3 只准备、不推送的提示词

```text
请只做本地准备，不要 push：

1. 检查仓库状态；
2. 创建合适的 .gitignore；
3. 检查敏感文件；
4. 把需要上传的文件加入暂存区；
5. 给我看 git diff --cached --stat；
6. 等我确认后再 commit 和 push。
```

### 3.4 推荐直接提供的附加信息

```text
项目用途：
是否允许公开：
是否已有 .git：
是否已有 GitHub 仓库：
是否有未提交修改：
是否使用 SSH：
是否使用 443 端口：
是否允许创建新分支：
是否允许创建 Pull Request：
```

## 4. Codex 执行上传时应该做的完整流程

本节既是给 Codex 的指导，也是用户检查 Codex 是否做对了的清单。

### 4.1 第一步：确认路径和目标

Codex 首先应确认：

- 用户给出的本地目录存在。
- 要上传的是目录本身，不是整个磁盘或上级大目录。
- GitHub 用户名、仓库名、公开性已经明确。
- 是新建仓库、更新已有仓库，还是创建 Pull Request。

不要贸然对 `D:\D`、用户主目录或包含大量无关文件的目录执行 `git add .`。

### 4.2 第二步：检查是否已经是 Git 仓库

```powershell
git -C "项目路径" status --short --branch
git -C "项目路径" remote -v
git -C "项目路径" branch -vv
git -C "项目路径" log --oneline -10
```

如果提示：

```text
fatal: not a git repository
```

说明需要初始化或选择正确的子目录。

如果 Codex 在 Windows 沙箱下遇到：

```text
fatal: detected dubious ownership in repository
```

可以只读检查：

```powershell
git -c safe.directory="D:/项目路径" -C "D:\项目路径" status
```

用户确认目录属于自己后，也可以：

```powershell
git config --global --add safe.directory "D:/项目路径"
```

不要为了方便，把整个磁盘加入 `safe.directory`。

### 4.3 第三步：检查工作区

Codex 应执行：

```powershell
git status
git diff
git diff --cached
```

必须分清：

- 哪些是用户之前已有的修改。
- 哪些是 Codex 本次修改。
- 哪些是生成文件或缓存。
- 哪些文件不应该提交。

Codex 不能使用：

```powershell
git reset --hard
git checkout --
```

来清除用户的未提交修改，除非用户明确要求并理解后果。

### 4.4 第四步：检查敏感信息

至少搜索：

```powershell
rg -n -i "password|passwd|token|api[_-]?key|secret|private key"
```

还应检查：

- `.env`
- `secrets.json`
- SSH 私钥
- GitHub Token
- 数据库连接字符串
- 云服务 Access Key
- 本地个人配置
- 游戏或软件存档

如果发现疑似密钥，不要直接把内容打印给用户，也不要提交。  
应告诉用户文件位置，并建议撤销已泄露的密钥。

### 4.5 第五步：检查大文件

PowerShell 示例：

```powershell
Get-ChildItem -Recurse -File |
    Sort-Object Length -Descending |
    Select-Object -First 30 FullName, Length
```

需要重点排查：

- 视频
- 数据集
- 模型权重
- 游戏安装包
- `.exe`、`.msi`
- 虚拟环境
- 压缩包

GitHub 普通仓库通常不接受超过 100 MB 的单个文件。  
大文件应使用 Release、外部存储、对象存储或 Git LFS。

### 4.6 第六步：创建或修改 `.gitignore`

常见 Python 项目：

```gitignore
__pycache__/
*.py[cod]
.venv/
venv/
build/
dist/
*.egg-info/
*.log
*.tmp
.env
secrets/
runtime/
profiles/
```

常见数据和模型：

```gitignore
data/
datasets/
checkpoints/
models/
*.pt
*.pth
*.onnx
*.ckpt
```

游戏或桌面应用：

```gitignore
save_data.json
run_save.json
*.bak
logs/
```

注意：

- `.gitignore` 对已经提交过的文件不会自动生效。
- 要从 Git 跟踪中移除但保留本地文件，可使用：

```powershell
git rm --cached 文件名
```

不要使用 `git rm` 删除用户的本地文件。

### 4.7 第七步：初始化仓库

如果项目还没有 `.git`，Codex 可以执行：

```powershell
git init -b main
```

老版本 Git：

```powershell
git init
git branch -M main
```

设置仓库本地身份：

```powershell
git config user.name "GitHub 用户名"
git config user.email "GitHub noreply 邮箱"
```

推荐使用 GitHub 的 noreply 邮箱，避免公开真实邮箱。

### 4.8 第八步：选择文件并检查暂存区

小型、检查过内容的项目可以使用：

```powershell
git add .
```

大型项目或用户已有大量未提交修改时，应逐个添加：

```powershell
git add README.md
git add src tests
```

随后必须执行：

```powershell
git status
git diff --cached --stat
```

如果文件多了、少了或包含生成文件，应回到 `.gitignore` 或暂存步骤修正。

移出暂存区但保留本地文件：

```powershell
git restore --staged 文件路径
```

### 4.9 第九步：创建提交

```powershell
git commit -m "Initial commit"
```

提交说明应简洁、明确：

```text
Initial release of my-tool
Add GitHub upload tutorial
Fix enemy aggro and forest rewards
Update configuration pages
```

不要用：

```text
update
fix
changes
```

这种无法说明内容的提交信息。

查看提交：

```powershell
git log --oneline -10
```

### 4.10 第十步：配置远程仓库

查看：

```powershell
git remote -v
```

第一次添加：

```powershell
git remote add origin "git@github.com:用户名/仓库名.git"
```

如果 SSH 22 端口不可用：

```powershell
git remote add origin "ssh://git@ssh.github.com:443/用户名/仓库名.git"
```

已有远程但地址错误：

```powershell
git remote set-url origin "新的远程地址"
```

修改前先给用户看当前远程地址，避免推错仓库。

### 4.11 第十一步：SSH 认证

如果已有 SSH 密钥，先测试：

```powershell
ssh -T -i "$HOME\.ssh\id_ed25519" git@github.com
```

443 端口：

```powershell
ssh -T -p 443 -i "$HOME\.ssh\id_ed25519" git@ssh.github.com
```

成功时通常显示：

```text
Hi 用户名! You've successfully authenticated,
but GitHub does not provide shell access.
```

如果还没有密钥，可以生成：

```powershell
ssh-keygen -t ed25519 -C "GitHub noreply 邮箱" -f "$HOME\.ssh\id_ed25519_github"
```

随后让用户自己把 `.pub` 公钥添加到：

```text
GitHub
→ Settings
→ SSH and GPG keys
→ New SSH key
→ Authentication Key
```

不要上传私钥。不要读取私钥内容并发送到聊天。

给单个仓库指定密钥：

```powershell
git config core.sshCommand 'ssh -i "C:/Users/用户名/.ssh/id_ed25519_github" -o IdentitiesOnly=yes'
```

### 4.12 第十二步：推送

第一次推送：

```powershell
git push -u origin main
```

以后推送：

```powershell
git push
```

如果在推送前发现远程已有新提交：

```powershell
git fetch origin
git status
git log --oneline --graph --all -10
```

需要合并时优先考虑：

```powershell
git pull --rebase origin main
```

遇到冲突时：

1. 打开冲突文件。
2. 查找 `<<<<<<<`、`=======`、`>>>>>>>`。
3. 手工保留正确内容。
4. 删除冲突标记。
5. 执行：

```powershell
git add 冲突文件
git rebase --continue
```

不要强制推送，除非用户明确了解它会改写远程历史。

### 4.13 第十三步：推送后验证

Codex 应执行：

```powershell
git status --short --branch
git branch -vv
git log --oneline -5
git remote -v
```

成功状态示例：

```text
## main...origin/main
```

还应告诉用户：

- 推送到哪个仓库。
- 哪个分支。
- 最新提交哈希。
- 推送了多少文件。
- 是否还有未提交修改。
- GitHub 页面链接。

## 5. 新建分支并创建 Pull Request

如果用户不希望直接修改 `main`：

```powershell
git switch -c feature/my-change
```

提交并推送：

```powershell
git add .
git commit -m "Add my change"
git push -u origin feature/my-change
```

如果已经安装并登录 GitHub CLI：

```powershell
gh pr create --base main --head feature/my-change --title "Add my change" --body "说明本次修改"
```

Codex 可以准备和创建 PR，但不能替用户完成网页登录、2FA 或仓库权限授权。

## 6. Codex 必须遵守的安全规则

### 6.1 不上传凭据

禁止提交：

- GitHub Token
- OpenAI API Key
- SSH 私钥
- 云服务 Access Key
- 数据库密码
- Cookie
- 浏览器登录数据

不要把秘密内容直接打印出来。  
发现秘密后，只报告文件名和风险，并建议撤销。

### 6.2 不覆盖用户修改

Codex 不应主动执行：

```powershell
git reset --hard
git checkout -- 文件
git clean -fd
```

除非用户明确要求，并清楚会丢失哪些内容。

### 6.3 不强制推送

禁止未经明确授权执行：

```powershell
git push --force
git push --force-with-lease
```

强制推送可能覆盖其他人的提交。

### 6.4 不删除仓库

除非用户明确要求并再次确认，不应执行：

```powershell
gh repo delete
```

### 6.5 不带 Token 写进远程地址

错误示例：

```text
https://用户名:Token@github.com/用户名/仓库.git
```

这样会把 Token 写进 `.git/config`，容易泄露。  
应使用 GitHub CLI、Credential Manager 或 SSH。

## 7. 常见错误与修复

### 7.1 `not a git repository`

说明当前目录或上级目录没有 `.git`。

先确认正确目录：

```powershell
Get-Location
Get-ChildItem -Force
```

再决定初始化还是进入子目录。

### 7.2 `Author identity unknown`

```powershell
git config user.name "用户名"
git config user.email "GitHub noreply 邮箱"
```

### 7.3 `Permission denied (publickey)`

检查：

```powershell
ssh -vT -i "$HOME\.ssh\id_ed25519_github" git@github.com
git remote -v
git config --local --get core.sshCommand
```

常见原因：

- 公钥没有添加到 GitHub。
- 测试了错误的私钥。
- 远程地址写成 HTTPS，但当前在测试 SSH。
- Windows 私钥权限错误。
- 22 端口被阻断，未使用 `ssh.github.com:443`。

### 7.4 `Connection timed out`

改用：

```text
ssh.github.com:443
```

### 7.5 `failed to push some refs`

通常是远程有本地没有的提交：

```powershell
git fetch origin
git status
git log --oneline --graph --all -10
```

确认后：

```powershell
git pull --rebase origin main
git push
```

### 7.6 `src refspec main does not match any`

通常还没有提交，或者当前分支不叫 `main`：

```powershell
git branch
git log --oneline
```

### 7.7 文件被 `.gitignore` 忽略但确实要上传

先检查：

```powershell
git check-ignore -v 文件路径
```

修改 `.gitignore` 后再执行：

```powershell
git add 文件路径
```

不要使用 `git add -f`，除非明确知道为什么需要强制添加。

### 7.8 二进制大文件导致仓库过大

不要在已有历史中随便删除一个文件就结束。  
如果大文件已经进入历史，需要考虑：

- Git LFS
- 从历史中清理大文件
- 重新初始化仓库

这些操作应先备份，并由用户明确确认。

## 8. 上传完成后的验收清单

用户应检查：

```text
[ ] GitHub 仓库地址正确
[ ] 仓库公开/私有状态正确
[ ] 默认分支是 main
[ ] 文件列表正确
[ ] README 能正常显示
[ ] 没有 Token、私钥、密码和本地存档
[ ] 没有虚拟环境、缓存和构建产物
[ ] 最新提交说明正确
[ ] 本地 git status 显示与 origin/main 同步
```

Codex 最终应报告：

```text
仓库：https://github.com/用户名/仓库名
分支：main
提交：abcdef1
状态：已推送，工作区干净
未提交内容：无
风险：无发现
```

如果存在未提交修改，应明确列出，不要把“部分上传”说成“全部上传”。

## 9. 可以直接复制给 Codex 的总任务模板

```text
请帮我把下面项目上传到 GitHub。

本地目录：
GitHub 用户名：
仓库名：
仓库公开性：Public / Private
分支名：main
提交说明：
是否已有 GitHub 仓库：
远程仓库地址：
认证方式：SSH / GitHub CLI
是否允许创建仓库：
是否允许创建分支：
是否允许创建 Pull Request：

执行前请先：
1. 检查目录和 Git 状态；
2. 查看 remote、branch 和最近提交；
3. 检查敏感文件、大文件和生成文件；
4. 创建或更新 .gitignore；
5. 给我看准备提交的文件列表和 git diff --cached --stat。

确认后请：
1. 初始化 Git（如需要）；
2. 设置本地提交身份；
3. 暂存相关文件并 commit；
4. 配置或核对远程仓库；
5. 测试认证；
6. push；
7. 验证远程分支和最新提交；
8. 告诉我仓库链接、提交哈希和剩余未提交修改。

不要上传任何 Token、私钥、密码、个人存档或无关的大文件。
不要使用 git reset --hard、git clean -fd 或强制推送。
如果需要我创建仓库、登录 GitHub 或添加 SSH 公钥，请停下并明确告诉我。
```

## 10. 最简命令速查

新仓库第一次上传：

```powershell
Set-Location "D:\Projects\my-project"
git init -b main
git config user.name "用户名"
git config user.email "GitHub noreply 邮箱"
git add .
git status
git diff --cached --stat
git commit -m "Initial commit"
git remote add origin "git@github.com:用户名/仓库名.git"
git push -u origin main
```

以后更新：

```powershell
git status
git diff
git add .
git diff --cached --stat
git commit -m "本次修改说明"
git push
```

检查远程：

```powershell
git remote -v
git branch -vv
git status --short --branch
git log --oneline -10
```

