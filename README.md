# MPull — Docker 容器管理面板

MPull 是一个自用的 Docker 管理 Web 面板，支持镜像源管理、镜像拉取、容器 / 镜像 / 网络 / 卷 / 端口管理，以及模板一键部署。

它适合部署在自用服务器、NAS 或内网环境中，尤其适合在访问 Docker Hub 不稳定的网络环境下使用。

## 主要功能

### 1. 🔐 __Web 网页登录保护__
支持自定义账号密码，防止面板裸奔。
默认账号为：
- 用户名：`admin`
- 密码：`mpull`

强烈建议首次部署时修改默认密码。登录后可在「系统设置 → 账号管理」中直接修改用户名和密码，无需重建容器。

出于安全考虑，登录会话密钥在每次容器启动时重新生成，因此容器重启（或重新部署）后所有浏览器登录状态会失效，需要重新输入账号密码。

### 2. 🧩 __镜像源管理__
支持管理多个 Docker 镜像源地址，支持连通性测试与可用性检测。

首次运行会创建一个默认 Docker Hub 镜像源，用户可以按目标仓库继续添加或删除镜像源。

拉取镜像时原始仓库直连优先，失败后按顺序自动转镜像源重试。

### 3. 🏠 __主页总览__
以卡片形式展示所有容器，实时显示运行状态，顶部汇总运行 / 已停止 / 未部署 / 镜像数量。

每个容器自动抓取站点图标作为卡片头像（内置常见品牌图标兜底，也可上传自定义图标）。

支持配置内网 / 公网 / 穿透三种访问地址，容器卡片会自动生成可点击的快捷访问链接；卡片支持拖拽排序。

自动检测容器镜像是否有新版本可用，定期比对已部署镜像与远端仓库的版本是否最新，当发现可用更新时，对应容器卡片上会显示醒目的黄色更新箭头提示，一目了然。

### 4. 📦 __容器管理__
提供三栏布局的容器管理界面：
- 左侧：容器列表（支持搜索）
- 中间：容器详情 + 日志
- 右侧：Compose 配置

支持查看容器状态、端口、创建时间、镜像、运行信息等详情。
支持常用容器操作：
- 启动 / 停止 / 重启容器
- 网页终端：一键进入容器执行 `sh` 命令
- 容器日志：随时查看，支持实时跟踪
- 查看 / 编辑 Compose 配置，支持保存与重新部署更新
- 新建 Stack（Compose 编排），支持把 `docker run` 命令一键转换为 Compose
- 检测到新版本时一键升级容器（自动拉取新镜像并重建）

### 5. 🗑️ __镜像管理__
支持查看本地 Docker 镜像列表。

可以区分正在使用中的镜像，并删除不需要的镜像，方便清理磁盘空间；支持一键清理悬空镜像。

支持镜像重命名（重新打标签），以及镜像的导入与导出，方便在离线环境或不同主机之间迁移镜像。

### 6. 📥 __智能拉取镜像__
支持常见镜像名格式，例如：
```text
nginx
nginx:latest
library/nginx:latest
ghcr.io/example/image:latest
```
支持自动补全 latest 标签，并自动处理 Docker 官方镜像的 library/ 前缀。

内置拉取历史记录，一键回填镜像名；拉取任务可随时取消。

通过任务轮询实时显示 docker pull 分层进度和任务日志。

镜像拉取成功后，会自动重打标签为原始镜像名，方便后续直接使用。

### 7. 🛍️ __模板中心__
内置常用容器模板，分类浏览 + 搜索，支持最新上架 / 上架顺序 / 首字母 / 手动拖动四种排序方式。

模板数据来自 GitHub 模板仓库（[chaomarks/mpull-appstore](https://github.com/chaomarks/mpull-appstore)），支持在面板中添加自定义模板源。

- 首次使用需在设置中配置「容器安装目录」，未配置时会自动引导跳转
- 一键部署：选择模板 → 预览 Compose → 确认安装，全程网页完成
- 模板中的路径等参数使用 `{{占位符}}` 标注，部署时按提示填写实际值即可
- 每个模板附带部署说明，已安装的模板可统一查看和卸载

### 8. 🌐 __网络 / 卷 / 端口管理__
- 网络：查看所有 Docker 网络，支持新建网络、清理未使用的网络
- 卷：查看所有存储卷，支持清理未使用的卷、批量删除
- 端口：汇总展示所有容器的端口占用，支持卡片 / 表格两种视图切换

### 9. 📝 __运行日志__
独立的运行日志页面，记录面板的关键操作和失败请求，支持刷新、清空和查看完整日志。

### 10. 🔄 __版本更新与自升级__
- 可设置容器更新检测间隔（0 = 关闭）
- 面板自身支持检查更新（关于页点击版本号），检测到新版本后可一键自升级：自动拉取新镜像并重建自身容器，全程无需命令行

### 11. ⚙️ __系统设置__
- HTTP 代理：为面板自身访问外部网络（检查更新、镜像源探测、图标抓取等）配置代理，支持连通性测试和排除地址（可按镜像源勾选）；镜像拉取走宿主机 Docker，不受此设置影响
- 更新检测间隔：主页容器卡片的镜像更新检测频率
- 语言：简体中文 / English 界面切换
- 账号管理：修改用户名和密码

### 12. 🎨 __多主题与多端适配__
四套主题（紫色 / 亮色 / 暗色 / 海洋）顶栏一键切换。

手机端自适应：抽屉式侧栏、列表卡片化、工具栏单行横滑，桌面端保持三栏布局与拖拽操作。

数据会持久化到宿主机映射目录中。

## 推荐运行方式

### Docker Run 示例

```bash
docker run -d \
  --name mpull \
  --restart unless-stopped \
  -p 8857:8857 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ~/mpull:/app/data \
  -v ~/mpull/stacks:/app/data/stacks \
  -e WEB_USERNAME=admin \
  -e WEB_PASSWORD=你的密码 \
  mark8857857/mpull:latest
```

### Docker Compose 示例

```yaml
services:
  mpull:
    image: mark8857857/mpull:latest
    container_name: mpull
    restart: unless-stopped
    ports:
      - "8857:8857"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ~/mpull:/app/data
      - ~/mpull/stacks:/app/data/stacks
    environment:
      - WEB_USERNAME=admin
      - WEB_PASSWORD=你的密码
```

## 访问地址

部署完成后，浏览器访问：

```text
http://服务器IP:8857
```

例如：

```text
http://192.168.1.100:8857
```

## 默认登录信息

- 默认用户名：`admin`
- 默认密码：`mpull`

强烈建议首次部署时通过环境变量 `WEB_PASSWORD` 修改默认密码，或者登录后在「系统设置 → 账号管理」中修改。

## 环境变量说明

| 变量名 | 默认值 | 说明 |
|---|---|---|
| `WEB_USERNAME` | `admin` | Web 登录用户名 |
| `WEB_PASSWORD` | `mpull` | Web 登录密码 |

## 数据目录说明

| 宿主机路径 | 容器路径 | 说明 |
|---|---|---|
| `~/mpull` | `/app/data` | MPull 数据目录 |
| `~/mpull/stacks` | `/app/data/stacks` | Compose 配置存储目录 |
| `/var/run/docker.sock` | `/var/run/docker.sock` | 用于控制宿主机 Docker |

## 更新镜像

```bash
docker pull mark8857857/mpull:latest

docker stop mpull
docker rm mpull

docker run -d \
  --name mpull \
  --restart unless-stopped \
  -p 8857:8857 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ~/mpull:/app/data \
  -v ~/mpull/stacks:/app/data/stacks \
  -e WEB_USERNAME=admin \
  -e WEB_PASSWORD=你的密码 \
  mark8857857/mpull:latest
```

如果你使用 Docker Compose，可以执行：

```bash
docker compose pull
docker compose up -d
```

也可以直接在面板里完成：关于页点击版本号检查更新，一键自升级。

## 注意事项

MPull 需要挂载：

```text
/var/run/docker.sock
```

这意味着 MPull 可以控制宿主机 Docker，能够执行 `pull`、`tag`、`rmi`、`start`、`stop`、`restart` 等操作，具备较高权限。

因此建议：

- 仅部署在自用服务器、NAS 或内网环境中
- 不要直接暴露到公网
- 如需公网访问，建议配合反向代理、HTTPS、访问控制或 VPN 使用
- 首次部署后请立即修改默认密码

## 适用场景

- NAS Docker 管理辅助工具
- Docker Hub 访问不稳定环境下的镜像拉取工具
- 内网服务器上的轻量 Docker 管理页面
- 通过模板中心快速搭建常用服务（下载工具、面板、监控等）
