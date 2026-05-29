# Hermes Agent 服务器部署全记录

> 腾讯云上海服务器 | Ubuntu | Docker 部署 | 微信网关

---

## 目录

1. [服务器环境](#一服务器环境)
2. [Docker 部署 Hermes](#二docker-部署-hermes)
3. [多容器管理（给朋友用）](#三多容器管理给朋友用)
4. [微信网关配置](#四微信网关配置)
5. [Git 笔记同步方案](#五git-笔记同步方案)
6. [常用操作命令](#六常用操作命令)
7. [踩坑记录](#七踩坑记录)
8. [使用技巧](#八使用技巧)

---

## 一、服务器环境

| 项目 | 说明 |
|------|------|
| 云服务商 | 腾讯云 |
| 地域 | 上海 |
| IP | 111.229.133.120 |
| 系统 | Ubuntu（root + ubuntu 用户） |
| 网络环境 | 位于 GFW 后，GitHub 访问受限 |
| 运行方式 | Docker 容器 |

### 端口说明

Hermes 使用 `--network host` 模式，不绑定具体端口，通过 iLinkAI 外连微信服务。

---

## 二、Docker 部署 Hermes

### 2.1 下载镜像

```bash
docker pull nousresearch/hermes-agent:latest
```

### 2.2 创建数据目录

```bash
mkdir -p ~/.hermes
```

### 2.3 运行设置向导（首次必做）

```bash
docker run -it --rm \
  -v ~/.hermes:/opt/data \
  nousresearch/hermes-agent setup
```

设置向导会依次询问：
- API Key（推荐 DeepSeek，便宜够用）
- 是否配置微信
- 是否允许群聊
- 其他可选配置

### 2.4 启动网关（后台运行）

```bash
docker run -d \
  --name hermes \
  --restart unless-stopped \
  --network host \
  -v ~/.hermes:/opt/data \
  nousresearch/hermes-agent gateway run
```

### 2.5 查看日志

```bash
docker logs hermes -f
```

首次启动会输出二维码/配对码，微信扫码绑定。

---

## 三、多容器管理（给朋友用）

### 3.1 创建独立数据目录

```bash
mkdir -p ~/.hermes-friend
```

### 3.2 运行设置向导（不能复制已有配置！）

```bash
docker run -it --rm \
  -v ~/.hermes-friend:/opt/data \
  nousresearch/hermes-agent setup
```

> ⚠️ **不要**把已有的 `.hermes` 目录复制过去当新配置，微信凭证会冲突。

### 3.3 启动第二个容器

```bash
docker run -d \
  --name hermes-friend \
  --restart unless-stopped \
  --network host \
  -v ~/.hermes-friend:/opt/data \
  nousresearch/hermes-agent gateway run
```

### 3.4 授权用户

容器启动后，让朋友在微信上给机器人发消息，日志里会出现配对码：

```bash
docker logs hermes-friend -f
```

看到配对码后执行授权：

```bash
docker exec -it hermes-friend /opt/hermes/.venv/bin/hermes pairing approve weixin <配对码>
```

### 3.5 查看所有容器

```bash
docker ps -a
```

### 3.6 查看容器挂载目录

```bash
docker inspect hermes hermes-friend --format '容器: {{.Name}}\n数据目录: {{range .Mounts}}{{.Source}}{{"\n"}}{{end}}'
```

---

## 四、微信网关配置

### 4.1 工作原理

Hermes 通过 iLinkAI 服务连接微信，容器内运行 gateway 进程，通过 WebSocket 与 iLinkAI 通信。

### 4.2 环境变量

在 `.env` 文件中配置：

```
WEIXIN_ACCOUNT_ID=xxx@im.bot
WEIXIN_TOKEN=xxx
WEIXIN_BASE_URL=https://ilinkai.weixin.qq.com
WEIXIN_DM_POLICY=pairing
WEIXIN_HOME_CHANNEL=用户ID@im.wechat
```

### 4.3 允许所有用户（省去授权步骤）

```bash
echo 'GATEWAY_ALLOW_ALL_USERS=true' >> ~/.hermes/.env
docker restart hermes
```

### 4.4 终端测试聊天

```bash
docker exec -it hermes /opt/hermes/.venv/bin/hermes
```

---

## 五、Git 笔记同步方案

### 5.1 架构

```
Windows (Obsidian)
    ↓ git push
  GitHub
    ↓ 自动镜像
  Gitee
    ↓ 定时拉取
  服务器 Hermes
```

### 5.2 服务器仓库配置

```bash
# 克隆仓库（从 Gitee，因为 GitHub 直连不稳定）
git clone https://cccxht:token@gitee.com/cccxht/my-nodesob.git

# 配置双远程推送（一次推送同步到 GitHub + Gitee）
git remote set-url origin https://cccxht:token@gitee.com/cccxht/my-nodesob.git
git remote set-url --push origin https://username:token@github.com/username/repo.git
git remote set-url --add --push origin https://cccxht:token@gitee.com/cccxht/repo.git
```

### 5.3 定时任务（每天凌晨拉取）

使用 cronjob 自动拉取：

```bash
# 每整点拉取
0 * * * * cd /path/to/repo && git pull origin main

# 或每天凌晨
0 0 * * * cd /path/to/repo && git pull origin main
```

### 5.4 GitHub 直连优化

服务器在腾讯云国内，GitHub 默认走 HTTP/2 会被限速。
**解决方案：强制 Git 使用 HTTP/1.1**

```bash
git config --global http.version HTTP/1.1
```

配置后 `git push/pull` GitHub 恢复正常。

---

## 六、常用操作命令

### 容器管理

```bash
# 查看运行中的容器
docker ps

# 查看所有容器（包括已停止的）
docker ps -a

# 查看日志
docker logs hermes -f

# 重启容器
docker restart hermes

# 停止容器
docker stop hermes

# 删除容器
docker rm hermes

# 进入容器终端
docker exec -it hermes bash
```

### 配置管理

```bash
# 进入设置向导
docker exec -it hermes /opt/hermes/.venv/bin/hermes setup

# 查看配置
docker exec -it hermes /opt/hermes/.venv/bin/hermes config

# 修改配置项
docker exec -it hermes /opt/hermes/.venv/bin/hermes config set KEY value

# 直接编辑配置文件
vim ~/.hermes/.env
vim ~/.hermes/config.yaml
```

### 数据查看

```bash
# 查看对话记录
ls ~/.hermes/sessions/

# 查看记忆
ls ~/.hermes/memories/
```

### 修复权限

```bash
# 文件权限导致 config.yaml 无法读取时
sudo chown -R 10000:10000 ~/.hermes-friend/
sudo chmod 644 ~/.hermes-friend/config.yaml
sudo chmod 755 ~/.hermes-friend/
docker restart hermes-friend
```

---

## 七、踩坑记录

### 🕳️ 坑 1：GitHub 直连不通

**现象：** `git clone/pull/push` 到 GitHub 一直超时，但 `curl` 偶尔能通。

**原因：** GFW 对 GitHub 的 TCP 连接进行限速，Git 默认使用 HTTP/2 协议，更容易被限。

**解决：** 全局配置 Git 改用 HTTP/1.1

```bash
git config --global http.version HTTP/1.1
```

### 🕳️ 坑 2：不能装代理工具

**现象：** 尝试安装 Clash、frp、cloudflared 等工具后被腾讯云安全告警标记为恶意文件。

**原因：** 腾讯云主机安全监控会自动扫描并查杀代理/隧道类工具。

**教训：** 不要尝试在服务器上安装任何 VPN/代理工具，走 Gitee 中转方案更安全。

### 🕳️ 坑 3：复制配置导致微信冲突

**现象：** 创建第二个 Hermes 容器时直接复制了 `.hermes` 目录，结果两个容器都用同一个微信 Bot。

**解决：** 新容器必须重新跑 `setup` 向导，生成独立的微信 Bot 凭证。

### 🕳️ 坑 4：config.yaml 权限不足

**现象：** 容器日志报错 `Permission denied: '/opt/data/config.yaml'`，配置全部失效。

**原因：** 文件所有者与容器内用户 UID 不匹配（容器内使用 UID 10000）。

**解决：**
```bash
sudo chown -R 10000:10000 ~/.hermes-friend/
sudo chmod 644 ~/.hermes-friend/config.yaml
```

### 🕳️ 坑 5：iLink 无法加入普通微信群

**现象：** 微信 Bot 扫码绑定后，无法被拉进普通微信群，只能接收私聊和指定的群。

**原因：** iLink 的 `@im.bot` 账号类型限制，普通微信群不会向 Bot 推送消息。

**解决：** 默认 DM 私聊模式，不要尝试拉 Bot 进群。

### 🕳️ 坑 6：Notion API 限制

**现象：** `ntn_` 格式的 API token 无法使用 `@notionhq/notion-mcp-server`（报 401），也不能用官方 Node SDK。

**解决：** 只能用 `curl` 直接调 Notion REST API。

### 🕳️ 坑 7：Bilibili 视频提取

**现象：** `yt-dlp` 下载 B 站视频报 412 错误。

**原因：** B 站反爬强。

**解决：** 使用 B 站官方 API 获取视频流地址：
```
https://api.bilibili.com/x/web-interface/view?bvid=xxx
https://api.bilibili.com/x/player/playurl?bvid=xxx&cid=xxx&qn=80
```
需要加 `Referer: https://www.bilibili.com` 和 `User-Agent` 头。

---

## 八、使用技巧

### 💡 技巧 1：终端直接聊天测试

不用等微信回复，终端直接开聊：

```bash
docker exec -it hermes /opt/hermes/.venv/bin/hermes
```

### 💡 技巧 2：双远程推送

Git 配好双 push URL 后，一次 `git push` 同时推送到 GitHub 和 Gitee：

```bash
git remote set-url --push origin https://github.com/user/repo.git
git remote set-url --add --push origin https://gitee.com/user/repo.git
```

### 💡 技巧 3：快速查看容器挂载路径

```bash
docker inspect hermes --format '{{range .Mounts}}{{.Source}}{{end}}'
```

### 💡 技巧 4：容器资源限制

给 Hermes 限制内存和 CPU，防止占满服务器：

```bash
docker run -d \
  --name hermes \
  --restart unless-stopped \
  --memory=4g --cpus=2 \
  --network host \
  -v ~/.hermes:/opt/data \
  nousresearch/hermes-agent gateway run
```

### 💡 技巧 5：直接修改文件配置

改配置不用每次都跑 setup 向导，直接编辑文件更快捷：

```bash
# 修改 API Key
vim ~/.hermes/.env

# 修改模型、人格等
vim ~/.hermes/config.yaml

# 修改 Agent 人格
vim ~/.hermes/SOUL.md

# 改完重启生效
docker restart hermes
```

### 💡 技巧 6：查看谁在用容器

```bash
docker inspect hermes --format '{{.Name}} → {{.State.Status}}'
```

### 💡 技巧 7：对话记录位置

所有聊天记录存在 `sessions/` 目录，按时间排序：

```bash
ls -lt ~/.hermes/sessions/ | head -5
```

### 💡 技巧 8：安全注意事项

- 微信 Bot 的 Token 不要泄露
- GitHub/Gitee Token 不要提交到公开仓库
- GATEWAY_ALLOW_ALL_USERS=true 只在可信环境使用
- Docker 容器不要挂载 Docker socket（会失去隔离性）
- 服务器防火墙只开放必要端口

---

> **最后更新：** 2026-05-30
> **作者：** 许小北的 Hermes 助理
