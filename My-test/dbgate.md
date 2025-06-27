docker run -it --name dbgate-instance --restart always -p 3300:3000 dbgate/dbgate

### **方法一：将 Dbgate 加入到数据库所在的网络中 (推荐)**

这是最稳定、最符合Docker最佳实践的方法。我们只需要在启动Dbgate时，用 `--network` 参数，明确告诉它“你要加入到数据库所在的那个网络里”。

#### **第一步：找到数据库所在的网络名称**

首先，我们需要知道由 `docker-compose` 创建的网络叫什么名字。

1. **进入您的项目目录**：
    
    Bash
    
    ```
    cd ~/maple079-docker
    ```
    
2. **列出当前项目相关的网络**：
    
    Bash
    
    ```
    docker-compose network ls
    ```
    
    您会看到类似这样的输出，通常网络名称是 `项目文件夹名_default`。
    
    ```
    Name                        Driver
    ----------------------------------
    maple079-docker_default     bridge  <-- 这个就是我们要找的网络名称
    ```
    
    请记下这个网络名称，我们这里假设是 `maple079-docker_default`。
    

#### **第二步：停止并移除旧的 Dbgate 容器**

我们需要先删掉您之前启动的那个独立的 Dbgate 容器。

1. **找到 Dbgate 容器的ID或名称**：
    
    Bash
    
    ```
    docker ps
    ```
    
    在列表中找到 Dbgate 容器，并复制它的 `CONTAINER ID` 或 `NAMES`。
    
2. **停止并删除它**：
    
    Bash
    
    ```
    # 假设容器名叫 dbgate_container
    docker stop dbgate_container
    docker rm dbgate_container
    ```
    

#### **第三步：使用新的 `docker run` 命令重新启动 Dbgate**

现在，我们用一个附加了 `--network` 参数的新命令来启动 Dbgate。

**请将下面的命令中 `<您希望的主机端口>` 和 `<网络名称>` 替换为实际值。** Dbgate 的默认端口是 `3000`。

Bash

```
docker run -d \
  --name dbgate \
  -p <您希望的主机端口>:3000 \
  --network <网络名称> \
  dbgate/dbgate-api
```

**实际示例：** 如果我们希望通过服务器的 `8088` 端口访问Dbgate，并且网络名叫 `maple079-docker_default`，那么命令就是：

Bash

```
docker run -d \
  --name dbgate \
  -p 8088:3000 \
  --network maple079-docker_default \
  dbgate/dbgate-api
```

现在，您的Dbgate容器和 `maplestory-db` 容器就在同一个网络里了，可以像邻居一样互相访问。

#### **第四步：在 Dbgate 中配置数据库连接**

1. **访问 Dbgate**： 在您的浏览器中打开 `http://<您的服务器公网IP>:<您设置的主机端口>` (例如: `http://121.36.216.27:8088`)。
    
2. **新建连接**，并填写以下信息：
    
    - **Connection type**: `MySQL`
        
    - **Host**: `maplestory-db` (**关键**：这里直接填写 `docker-compose.yml` 中数据库服务的名称即可！)
        
    - **Port**: `3306` (默认端口)
        
    - **User**: `root`
        
    - **Password**: `FFee@@861027` (您在 `docker-compose.yml` 中设置的密码)
        
    - **Default database**: `maplestory_079`
        
3. 点击“Test”按钮，应该会提示连接成功。然后点击“Connect”即可进入数据库管理界面。
    

---

### **方法二：使用数据库容器的内部IP地址 (不推荐)**

这种方法不推荐，因为容器的内部IP地址在每次重启后都**可能会改变**，导致您需要重新配置。

#### **第一步：找到数据库容器的内部IP**

Bash

```
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' maplestory-db
```

这个命令会输出一个IP地址，通常是 `172.x.x.x` 的格式。例如 `172.18.0.2`。

#### **第二步：在 Dbgate 中配置连接**

在Dbgate的连接设置中：

- **Host**: 填写您上一步查到的那个内部IP地址，例如 `172.18.0.2`。
    
- 其他信息（端口、用户、密码）不变。
    

这种方法可以快速连接，但一旦您用 `docker-compose down` 再 `up`，这个IP就可能失效了。

**总结：强烈建议您使用方法一**，它是一劳永逸的、最稳定的解决方案。在Dbgate中，您就可以看到 `maplestory_079` 数据库里的所有表，可以开始手动添加或管理您的游戏账号了。