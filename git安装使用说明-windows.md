# 公用机器 Git 使用说明（Windows）

> 多人共用这台 Windows 电脑，每人用自己的仓库。第一次照「一」做一遍，之后每天只用「二」那五行。
>
> 麒麟 V10 用《git安装使用说明-麒麟v10.md》。

## 一、第一次使用

**1. 装 Git**：双击同目录下的 `Git-2.56.1-64-bit.exe`，一路默认。

**2. 克隆自己的仓库：

```powershell
mkdir C:\git\zhangsan
cd C:\git\zhangsan
git clone <你的仓库链接>
cd zhangsan-project
```

**3. 设置身份**（只对这个仓库生效）：

```powershell
git config --local user.name "张三"
git config --local user.email "zhangsan@company.com"
git config --local credential.helper ''

```

> `--%` 不能省。PowerShell 会吞掉空参数，不加就报 `error: wrong number of arguments`。

也可以直接跑脚本（把 `git-set-identity.ps1` 放在仓库目录）：

```powershell
.\git-set-identity.ps1 -Name "张三" -Email "zhangsan@company.com" -NoCacheCredential
```

提示"未对文件进行数字签名"时，先执行下面这行，再重跑：

```powershell
Set-ExecutionPolicy -Scope Process Bypass -Force
```

**检查**：`git config --local --list` 里能看到 `user.name`、`user.email`、`credential.helper=` 三行。

## 二、日常操作

```powershell
git pull
git status
git add -A
git commit -m "改了什么"
git push
```

第一次推送：`git push -u origin main`。

## 三、三条规矩

1. 身份只配 `--local`，**不要用 `--global`**——共用账号时全局身份是别人的。
2. 每个仓库都关掉凭据缓存（上面那条命令）。push 时输自己的用户名 + token，不保存到机器上。
3. 一人一个目录；离开前 `git status` 确认没有你的未提交改动，否则会被下一个人 `git add -A` 一起提交。

## 四、常见问题

| 现象 | 处理 |
|---|---|
| push 报 `403` / `Permission denied` | 用到了别人的缓存凭据：`cmdkey /list` 里找到 `git:` 开头的条目，用 `cmdkey /delete:<Target>` 删掉，重输自己账号 |
| 提交作者是别人 | `git config --global --unset user.name`、`--unset user.email`，再按「一」重配；没推送的可 `git commit --amend --reset-author --no-edit` |
| 作者名显示成 `寮犱笁` | 不是错误，是控制台编码：`chcp 65001` 或改用 Git Bash |
| `SSL certificate problem: self signed certificate` | 内网 HTTPS 证书：`git config --global http.sslCAInfo "C:/certs/company-ca.crt"` |
| `407` / `502` / 连内网 Git 超时 | 走了外网代理：`git config --global http."http://内网地址/".proxy ""` |
| `Updates were rejected ... fetch first` | 远程有别人的提交：`git pull --rebase` 后重推 |
| `--local can only be used inside a git repository` | 先 `cd` 进克隆下来的仓库目录 |
| `fatal: Authentication failed` | token 过期，或把登录密码当 token：去平台重新生成 |
