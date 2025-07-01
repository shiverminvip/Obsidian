http://121.36.216.27:800


好的，完全理解。来回翻页确实很不方便。

我将为您整理一份最终的、完整的一站式操作手册。这份手册将包含从关闭现有服务开始，到成功部署游戏服务端和Web注册页面的所有步骤、所有文件的最终正确内容。

您只需要跟着这份手册一步步操作即可。

最终完整部署手册 (从零到启动)
前提
您已经将 maplestory-server-079 的所有源文件放入了 ~/maple079-docker/server/ 目录。

您已经将数据库 .sql 文件放入 ~/maple079-docker/mysql-init/ 目录并重命名为 init.sql。

第一步：关闭并清理现有环境
我们从一个完全干净的状态开始。

在您的项目根目录 ~/maple079-docker 下，执行以下命令：

Bash

docker-compose down -v
-v 参数会一并删除数据卷，确保数据库也会被全新地初始化，避免任何旧数据干扰。

第二步：确认/创建所有必需的文件和目录
现在，我们来一次性创建或确认所有需要的文件都已就位并且内容正确。

1. 最终目录结构确认
请确保您的 ~/maple079-docker 目录结构如下：

maple079-docker/
├── docker-compose.yml             <-- (我们将创建/覆盖这个文件)
│
├── mysql-init/
│   └── init.sql
│
├── server/
│   ├── Dockerfile                 <-- (我们将创建/覆盖这个文件)
│   ├── start.sh                   <-- (我们将创建/覆盖这个文件)
│   └── config/
│       └── server.properties      <-- (您需要修改这个文件)
│
└── web-reg-app/
    ├── Dockerfile                 <-- (我们将创建/覆盖这个文件)
    ├── app.py                     <-- (我们将创建/覆盖这个文件)
    ├── requirements.txt           <-- (我们将创建/覆盖这个文件)
    └── templates/
        └── register.html          <-- (我们将创建/覆盖这个文件)
2. 文件内容清单
文件 1: ~/maple079-docker/docker-compose.yml (根目录)
作用: 定义和链接所有服务（数据库、游戏、Web注册）。

最终内容:

YAML

version: '3.8'

services:
  # MySQL 数据库服务
  db:
    image: mysql:5.7
    container_name: maplestory-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 'FFee@@861027'
      MYSQL_DATABASE: 'maplestory_079'
    volumes:
      - ./mysql-init:/docker-entrypoint-initdb.d
      - mysql-data:/var/lib/mysql
    ports:
      - "3306:3306"
    networks:
      - default

  # MapleStory 服务端应用
  app:
    build:
      context: ./server
    container_name: maplestory-app
    restart: always
    depends_on:
      - db
    ports:
      - "9595:9595"
      - "8600:8600"
      - "2525-2530:2525-2530"
    volumes:
      - ./server-configs/server.properties:/app/config/server.properties
    environment:
      DB_HOST: 'db'
      DB_USER: 'root'
      DB_PASS: 'FFee@@861027'
    networks:
      - default

  # Flask Web注册应用服务
  web-reg:
    build:
      context: ./web-reg-app
    container_name: maplestory-reg-web
    restart: always
    depends_on:
      - db
    ports:
      - "800:5000" # Web注册页面将通过服务器的800端口访问
    environment:
      DB_HOST: 'db'
      DB_USER: 'root'
      DB_PASS: 'FFee@@861027'
      DB_DATABASE: 'maplestory_079'
      DB_PORT: '3306'
    networks:
      - default

volumes:
  mysql-data:

networks:
  default:
    driver: bridge
文件 2: ~/maple079-docker/server/Dockerfile (游戏服务的Dockerfile)
作用: 构建游戏服务端镜像，并安装网络工具。

最终内容:

Dockerfile

# 使用一个包含 JDK 1.7 的轻量级官方基础镜像
FROM openjdk:7-jdk-slim

# 因为 Debian 8 "Jessie" 已停止支持(EOL), 将软件源修改为官方的存档(archive)地址
RUN echo "deb http://archive.debian.org/debian/ jessie main" > /etc/apt/sources.list \
    && echo "deb http://archive.debian.org/debian-security/ jessie/updates main" >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y --force-yes net-tools tcpdump \
    && rm -rf /var/lib/apt/lists/*

# 在容器内部创建一个工作目录
WORKDIR /app

# 将当前目录(即server文件夹)的所有内容复制到容器的/app目录
COPY . .

# 赋予启动脚本可执行权限
RUN chmod +x ./start.sh

# 容器启动时执行的默认命令
CMD ["./start.sh"]
文件 3: ~/maple079-docker/server/start.sh (游戏服务的启动脚本)
作用: 动态修改数据库配置，并启动Java程序。

最终内容:

Bash

#!/bin/sh

# 动态修改db.properties文件
sed -i "s/127.0.0.1:3306/${DB_HOST}:3306/" config/db.properties
sed -i "s/^username *= *root/username = ${DB_USER}/" config/db.properties
sed -i "s/^password *= *.*/password = ${DB_PASS}/" config/db.properties

# 启动服务端
echo "
+----------------------------------------------------------------------
|        冒险岛079 FOR CentOS/Ubuntu/Debian (Running in Docker)
+----------------------------------------------------------------------
"
java -cp ./bin/maple.jar -server -DhomePath=./config/ -DscriptsPath=./scripts/ -DwzPath=./scripts/wz -Xms512m -Xmx2048m -XX:PermSize=256m -XX:MaxPermSize=512m -XX:MaxNewSize=512m server.Start
文件 4: ~/maple079-docker/web-reg-app/Dockerfile (Web应用的Dockerfile)
作用: 构建Web注册页面的镜像。

最终内容:

Dockerfile

# 使用一个包含Python 3的官方镜像
FROM python:3.9-slim

# 设置工作目录
WORKDIR /app

# 复制需求文件并安装依赖库
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制应用代码到容器中
COPY . .

# 声明容器将监听5000端口
EXPOSE 5000

# 容器启动时执行的命令
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
文件 5: ~/maple079-docker/web-reg-app/app.py (Web应用的核心逻辑)
作用: 提供Web服务和处理注册请求。

最终内容:

Python

from flask import Flask, render_template, request, redirect, url_for, flash
import mysql.connector
import hashlib
import os

app = Flask(__name__)
app.secret_key = os.urandom(24)

DB_CONFIG = {
    'host': os.getenv('DB_HOST', 'localhost'),
    'user': os.getenv('DB_USER', 'root'),
    'password': os.getenv('DB_PASS', 'your_default_password'),
    'database': os.getenv('DB_DATABASE', 'maplestory_079'),
    'port': os.getenv('DB_PORT', 3306)
}

def get_db_connection():
    try:
        conn = mysql.connector.connect(**DB_CONFIG)
        return conn
    except mysql.connector.Error as err:
        print(f"数据库连接失败: {err}")
        return None

def hash_password_sha1(password):
    return hashlib.sha1(password.encode('utf-8')).hexdigest()

@app.route('/')
def index():
    return render_template('register.html', title='账号注册')

@app.route('/register', methods=['POST'])
def register():
    username = request.form.get('username')
    password = request.form.get('password')
    confirm_password = request.form.get('confirm_password')

    if not username or not password or not confirm_password:
        flash('所有字段都必须填写！', 'danger')
        return redirect(url_for('index'))
    if password != confirm_password:
        flash('两次输入的密码不一致！', 'danger')
        return redirect(url_for('index'))
    if len(username) < 4 or len(username) > 12:
        flash('账号长度必须在4到12个字符之间。', 'danger')
        return redirect(url_for('index'))
    if len(password) < 6:
        flash('密码长度至少需要6个字符。', 'danger')
        return redirect(url_for('index'))

    conn = get_db_connection()
    if not conn:
        flash('数据库连接失败，请联系管理员！', 'danger')
        return redirect(url_for('index'))

    cursor = conn.cursor()
    try:
        cursor.execute("SELECT name FROM accounts WHERE name = %s", (username,))
        if cursor.fetchone():
            flash(f"账号 '{username}' 已经被注册了，请换一个。", 'warning')
            return redirect(url_for('index'))

        hashed_password = hash_password_sha1(password)
        sql = "INSERT INTO accounts (name, password) VALUES (%s, %s)"
        cursor.execute(sql, (username, hashed_password))
        conn.commit()
        flash(f"恭喜！账号 '{username}' 注册成功！", 'success')
        return redirect(url_for('index'))
    except mysql.connector.Error as err:
        flash(f'注册时发生未知错误: {err}', 'danger')
        return redirect(url_for('index'))
    finally:
        cursor.close()
        conn.close()

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
文件 6: ~/maple079-docker/web-reg-app/requirements.txt
作用: 定义Web应用的Python依赖库。

最终内容:

Plaintext

Flask
mysql-connector-python
gunicorn
文件 7: ~/maple079-docker/web-reg-app/templates/register.html
作用: 注册页面的前端HTML。

最终内容:

HTML

<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ title }} - 冒险岛079</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen font-sans">
    <div class="w-full max-w-md p-8 space-y-6 bg-white rounded-xl shadow-2xl">
        <h1 class="text-3xl font-bold text-center text-gray-800">冒险岛账号注册</h1>
        {% with messages = get_flashed_messages(with_categories=true) %}
            {% if messages %}
                {% for category, message in messages %}
                    <div class="p-4 rounded-md text-sm
                        {% if category == 'success' %} bg-green-100 text-green-700
                        {% elif category == 'danger' %} bg-red-100 text-red-700
                        {% elif category == 'warning' %} bg-yellow-100 text-yellow-700
                        {% else %} bg-blue-100 text-blue-700
                        {% endif %}">
                        {{ message }}
                    </div>
                {% endfor %}
            {% endif %}
        {% endwith %}
        <form action="{{ url_for('register') }}" method="POST" class="space-y-6">
            <div>
                <label for="username" class="block text-sm font-medium text-gray-700">游戏账号</label>
                <input type="text" name="username" id="username" required minlength="4" maxlength="12"
                       class="w-full px-4 py-2 mt-2 text-gray-700 bg-gray-100 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent transition">
                <p class="text-xs text-gray-500 mt-1">4-12个字符，仅限字母和数字。</p>
            </div>
            <div>
                <label for="password" class="block text-sm font-medium text-gray-700">登录密码</label>
                <input type="password" name="password" id="password" required minlength="6"
                       class="w-full px-4 py-2 mt-2 text-gray-700 bg-gray-100 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent transition">
                <p class="text-xs text-gray-500 mt-1">至少6个字符。</p>
            </div>
            <div>
                <label for="confirm_password" class="block text-sm font-medium text-gray-700">确认密码</label>
                <input type="password" name="confirm_password" id="confirm_password" required minlength="6"
                       class="w-full px-4 py-2 mt-2 text-gray-700 bg-gray-100 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent transition">
            </div>
            <div>
                <button type="submit"
                        class="w-full px-4 py-3 font-semibold text-white bg-purple-600 rounded-lg hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-500 transition-transform transform hover:scale-105">
                    立即注册
                </button>
            </div>
        </form>
    </div>
</body>
</html>
第三步：配置云服务器防火墙
登录您的云服务商控制台（华为云），进入安全组管理。请确保入方向规则已经开放了以下所有端口的访问：

游戏登录: TCP 9595 (源地址: 0.0.0.0/0)

游戏商城: TCP 8600 (源地址: 0.0.0.0/0)

游戏频道: TCP 2525-2530 (可以设置为一个端口范围) (源地址: 0.0.0.0/0)

Web注册页面: TCP 800 (源地址: 0.0.0.0/0)

数据库远程管理(可选，但推荐设为您的IP): TCP 3306 (源地址: 您的本地公网IP/32)

第四步：最终构建与启动
万事俱备！回到您服务器的 ~/maple079-docker 根目录下，执行最终的启动命令：

Bash

docker-compose up -d --build
这个命令会：

根据 server/Dockerfile 构建一个包含网络工具的游戏服务镜像。

根据 web-reg-app/Dockerfile 构建一个包含Flask和依赖库的Web应用镜像。

根据 docker-compose.yml 的定义，启动并链接所有三个服务（数据库、游戏、Web注册）。

第五步：验证与使用
游戏服务端：等待启动完成后，您可以用客户端连接 您的服务器公网IP (端口9595) 进行游戏。

Web注册页面：在浏览器中访问 http://您的服务器公网IP:800，即可看到注册页面，方便地添加账号。

这份手册包含了从清理到最终运行的全部细节和所有文件的最终正确版本。祝您操作顺利！