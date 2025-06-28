### 使用 Docker 安装 Gitea 的详细步骤

使用 Docker 安装 Gitea 是最推荐和最简单的方式之一。它能确保环境的隔离性，并且部署和管理都非常方便。

以下是详细的步骤：

#### 前提条件

在开始之前，请确保你的系统上已经安装了 Docker 和 Docker Compose。

- **安装 Docker：** 访问 [Docker 官方网站](https://docs.docker.com/get-docker/)，根据你的操作系统（Linux, Windows, macOS）选择对应的安装教程。
    
- **安装 Docker Compose：** Docker Desktop 会自带 Docker Compose。如果你在 Linux 上单独安装 Docker Engine，可能需要额外安装 Docker Compose。访问 [Docker Compose 官方安装指南](https://docs.docker.com/compose/install/)。
    

#### 步骤 1：创建 Gitea 所需的目录

首先，我们需要创建一些目录来持久化 Gitea 的数据和配置，这样即使 Docker 容器被删除，你的数据也不会丢失。

Bash

```
mkdir -p /var/lib/gitea
mkdir -p /etc/gitea
```

- `/var/lib/gitea`: 用于存储 Gitea 的仓库数据、附件、日志等。
    
- `/etc/gitea`: 用于存储 Gitea 的配置文件 `app.ini`。
    

#### 步骤 2：创建 Docker Compose 文件

Docker Compose 允许你定义和运行多容器 Docker 应用程序。我们将创建一个 `docker-compose.yml` 文件来定义 Gitea 和其所需的数据库服务。

Gitea 支持多种数据库，包括 SQLite3、MySQL/MariaDB 和 PostgreSQL。对于大多数个人或小型团队，**SQLite3** 是最简单、最推荐的选择，因为它不需要额外的数据库容器。如果你需要更好的性能或扩展性，可以考虑使用 MySQL/MariaDB 或 PostgreSQL。

**选项 A：使用 SQLite3 (推荐用于简单场景)**

创建一个名为 `docker-compose.yml` 的文件，并粘贴以下内容：

YAML

```
version: "3"

services:
  gitea:
    image: gitea/gitea:latest
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=sqlite3
      - GITEA__database__PATH=/data/gitea.db
    restart: always
    volumes:
      - /var/lib/gitea:/data
      - /etc/gitea:/etc/gitea
    ports:
      - "3000:3000" # Gitea Web 界面端口
      - "222:22"    # Gitea SSH 端口
    networks:
      - gitea-network

networks:
  gitea-network:
    external: true # 如果你已经有一个名为 gitea-network 的网络，可以设置为 true
                   # 否则，请删除这行或将其设置为 false，Docker Compose 会自动创建
```

**说明：**

- `image: gitea/gitea:latest`: 使用 Gitea 官方的最新 Docker 镜像。
    
- `container_name: gitea`: 给容器指定一个名称。
    
- `environment`:
    
    - `USER_UID` 和 `USER_GID`: 建议设置为你的宿主机上运行 Gitea 的用户 ID 和组 ID，以避免权限问题。你可以通过 `id -u` 和 `id -g` 命令获取。如果不知道，可以暂时不设置，但后续可能需要调整 `/var/lib/gitea` 目录的权限。
        
    - `GITEA__database__DB_TYPE=sqlite3`: 指定使用 SQLite3 数据库。
        
    - `GITEA__database__PATH=/data/gitea.db`: 指定 SQLite3 数据库文件的路径，它会映射到宿主机的 `/var/lib/gitea/gitea.db`。
        
- `restart: always`: 确保容器在退出或 Docker 守护进程重启时自动重启。
    
- `volumes`:
    
    - `/var/lib/gitea:/data`: 将宿主机的 `/var/lib/gitea` 目录映射到容器内部的 `/data` 目录，用于持久化数据。
        
    - `/etc/gitea:/etc/gitea`: 将宿主机的 `/etc/gitea` 目录映射到容器内部的 `/etc/gitea` 目录，用于持久化配置文件。
        
- `ports`:
    
    - `3000:3000`: 将宿主机的 3000 端口映射到容器的 3000 端口，用于访问 Gitea 的 Web 界面。
        
    - `222:22`: 将宿主机的 222 端口映射到容器的 22 端口，用于 SSH 访问 Git 仓库。**注意：** 如果你的宿主机 22 端口已经被占用（通常是系统 SSH 服务），请务必使用其他端口，如 2222 或其他未被占用的端口。
        
- `networks`: 定义容器所属的网络。这里创建了一个 `gitea-network`。
    

**选项 B：使用 MySQL/MariaDB 或 PostgreSQL (推荐用于生产环境)**

如果你需要更强大的数据库，可以使用以下配置。这里以 MySQL 为例：

YAML

```
version: "3"

services:
  gitea:
    image: gitea/gitea:latest
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=mysql
      - GITEA__database__HOST=db:3306 # 数据库服务名称:端口
      - GITEA__database__NAME=gitea   # 数据库名称
      - GITEA__database__USER=gitea   # 数据库用户
      - GITEA__database__PASSWD=your_gitea_db_password # 数据库密码
    restart: always
    volumes:
      - /var/lib/gitea:/data
      - /etc/gitea:/etc/gitea
    ports:
      - "3000:3000" # Gitea Web 界面端口
      - "222:22"    # Gitea SSH 端口
    depends_on:
      - db
    networks:
      - gitea-network

  db:
    image: mysql:8.0 # 或者 mariadb:latest, postgres:latest
    container_name: gitea_db
    environment:
      - MYSQL_ROOT_PASSWORD=your_mysql_root_password # MySQL root 用户密码
      - MYSQL_USER=gitea
      - MYSQL_PASSWORD=your_gitea_db_password
      - MYSQL_DATABASE=gitea
    restart: always
    volumes:
      - /var/lib/mysql:/var/lib/mysql # 持久化数据库数据
    networks:
      - gitea-network

networks:
  gitea-network:
    external: true # 如果你已经有一个名为 gitea-network 的网络，可以设置为 true
                   # 否则，请删除这行或将其设置为 false，Docker Compose 会自动创建
```

**注意：**

- 请将 `your_gitea_db_password` 和 `your_mysql_root_password` 替换为强密码。
    
- `depends_on: - db` 确保 `db` 服务在 `gitea` 服务之前启动。
    
- 如果你使用 PostgreSQL，请将 `image` 更改为 `postgres:latest`，并相应调整环境变量（例如 `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB`）。
    

#### 步骤 3：启动 Gitea 容器

在包含 `docker-compose.yml` 文件的目录下，打开终端并运行以下命令：

Bash

```
docker compose up -d
```

- `up`: 启动服务。
    
- `-d`: 在后台运行容器（detached mode）。
    

这将下载 Gitea 镜像（如果本地没有），创建容器，并启动服务。

#### 步骤 4：初始化 Gitea

容器启动后，等待几分钟让 Gitea 完全初始化。然后，在你的浏览器中访问 Gitea 的 Web 界面：

- **`http://你的服务器IP地址或域名:3000`**
    

首次访问时，你将看到 Gitea 的安装页面。你需要配置以下信息：

1. **数据库设置：**
    
    - **数据库类型：** 选择你在 `docker-compose.yml` 中配置的类型（SQLite3, MySQL, PostgreSQL）。
        
    - **主机：**
        
        - 如果使用 SQLite3，保持默认或指定 `/data/gitea.db`。
            
        - 如果使用 MySQL/PostgreSQL，填写 `db:3306` (MySQL) 或 `db:5432` (PostgreSQL)。
            
    - **用户、密码、数据库名称：** 填写你在 `docker-compose.yml` 中为数据库服务设置的凭据。
        
2. **Gitea 基本设置：**
    
    - **仓库根目录：** 保持默认 `/data/git/repositories`。
        
    - **Git LFS 根目录：** 保持默认 `/data/git/lfs`。
        
    - **日志路径：** 保持默认 `/data/log`。
        
    - **运行用户：** 保持默认 `git`。
        
    - **SSH 服务域名：** 填写你的服务器 IP 地址或域名。
        
    - **SSH 端口：** 填写你在 `docker-compose.yml` 中映射的 SSH 端口（例如 `222`）。
        
    - **Gitea HTTP 端口：** 保持默认 `3000`。
        
    - **Gitea 基本 URL：** 填写 `http://你的服务器IP地址或域名:3000/`。
        
    - **日志等级：** 保持默认。
        
3. **可选设置：** 根据需要配置。
    
4. **管理员账号设置：** 这是最重要的部分。填写你的管理员用户名、密码和邮箱。这将是你的第一个 Gitea 用户，拥有最高权限。
    

填写完所有信息后，点击 **“立即安装 Gitea”**。

#### 步骤 5：完成安装

安装过程可能需要一些时间。完成后，Gitea 会自动跳转到登录页面。使用你刚刚设置的管理员账号登录即可。

#### 常用 Docker Compose 命令

- **启动 Gitea：** `docker compose up -d`
    
- **停止 Gitea：** `docker compose down`
    
- **重启 Gitea：** `docker compose restart gitea` (如果你有数据库服务，可能需要先停止所有服务再启动)
    
- **查看容器日志：** `docker compose logs -f gitea`
    
- **进入 Gitea 容器：** `docker compose exec gitea bash`
    

#### 注意事项

- **防火墙：** 确保你的服务器防火墙允许外部访问 3000 端口（Web 界面）和 222 端口（SSH 访问）。
    
- **域名和 HTTPS：** 如果你想通过域名访问 Gitea 并启用 HTTPS，你需要在 Gitea 前面配置一个反向代理（如 Nginx 或 Caddy），并配置 SSL 证书。这超出了 Docker Compose 的范围，但 Gitea 官方文档有详细说明。
    
- **备份：** 定期备份 `/var/lib/gitea` 和 `/etc/gitea` 目录，以防数据丢失。
    

通过以上步骤，你就可以成功地使用 Docker Compose 搭建一个 Gitea 代码托管平台了！