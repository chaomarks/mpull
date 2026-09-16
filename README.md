# MPull — Docker 容器管理镜像拉取 Web 面板

MPull 是一个自用的 Docker 管理 Web 面板，支持通过网页管理镜像源、拉取镜像、管理本地容器和镜像。

它适合部署在自用服务器、NAS 或内网环境中，尤其适合在访问 Docker Hub 不稳定的网络环境下使用。

## 主要功能

### 1. 🔐 __Web网页登录保护__
支持自定义账号密码，防止面板裸奔
默认账号为：
- 用户名：`admin`
- 密码：`mpull`
强烈建议首次部署时修改默认密码。
### 2. 🏠 __主页总览__
以卡片形式展示所有容器，实时显示运行状态。

自动检测容器镜像是否有新版本可用，定期比对已部署镜像与远端仓库的版本是否最新，当发现可用更新时，对应容器卡片上会显示醒目的黄色更新箭头提示，一目了然。

### 3. 📦 __容器管理__
提供三栏布局的容器管理界面：
- 左侧：容器列表
- 中间：容器详情
- 右侧：操作面板
支持查看容器状态、端口、创建时间、镜像、运行信息等详情。
支持常用容器操作：
- 启动容器
- 停止容器
- 重启容器
- 查看 Compose 配置
- 编辑 Compose 配置
- 重新部署更新

### 4. 🗑️ __镜像管理__
支持查看本地 Docker 镜像列表。

可以区分正在使用中的镜像，并删除不需要的镜像，方便清理磁盘空间。

支持镜像的导入与导出，方便在离线环境或不同主机之间迁移镜像。

### 5. 📥 __智能拉取镜像__
支持常见镜像名格式，例如：
```text
nginx
nginx:latest
library/nginx:latest
ghcr.io/example/image:latest
```
支持自动补全 latest 标签，并自动处理 Docker 官方镜像的 library/ 前缀。

通过 WebSocket 实时显示 docker pull 进度和任务日志。

镜像拉取成功后，会自动重打标签为原始镜像名，方便后续直接使用

### 6. 🎨 __深紫色系 UI__

采用深色紫色系界面，紧凑布局，侧导航切换页面。

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
  -e AUTH_SECRET=随便一串随机字符 \
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
      - AUTH_SECRET=随便一串随机字符
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

强烈建议首次部署时通过环境变量 `WEB_PASSWORD` 修改默认密码，或者登录后在右上角修改密码。

## 环境变量说明

| 变量名 | 默认值 | 说明 |
|---|---|---|
| `WEB_USERNAME` | `admin` | Web 登录用户名 |
| `WEB_PASSWORD` | `mpull` | Web 登录密码 |
| `AUTH_SECRET` | 无 | 用于登录鉴权的随机密钥，建议设置为一串随机字符 |

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
  -e AUTH_SECRET=随便一串随机字符 \
  mark8857857/mpull:latest
```

如果你使用 Docker Compose，可以执行：

```bash
docker compose pull
docker compose up -d
```

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
