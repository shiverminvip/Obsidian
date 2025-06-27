这是一个非常棒的想法！编写一个本地的Python程序来管理账号，不仅能大大提升效率，也是一个非常好的编程实践。

我们将分两大部分来完成这个任务：

1. **服务端配置**：我们需要对服务器做一点小小的、安全的改动，以允许您的本地电脑连接到数据库。
    
2. **本地Python程序**：我们将编写一个完整的Python脚本，实现添加和删除账号的功能。
    

---

### **第一步：在服务器上安全地开放数据库端口 (关键操作)**

默认情况下，为了安全，我们的数据库容器(`maplestory-db`)只在Docker的内部网络中工作，无法从公网直接访问。为了让您本地的Python程序能连上它，我们需要将它的端口“发布”出来，并用防火墙进行严格的访问控制。

#### **1. 修改 `docker-compose.yml` 文件**

请登录您的服务器，并编辑 `maple079-docker` 目录下的 `docker-compose.yml` 文件。找到 `db` 服务的部分，将其中的 `expose` 修改为 `ports`。

- **修改前:**
    
    YAML
    
    ```
      db:
        # ...
        expose:
          - "3306"
    ```
    
- **修改后 (请替换成以下内容):**
    
    YAML
    
    ```
      db:
        # ...
        ports:
          - "3306:3306"
    ```
    
    这个改动的意思是，将服务器主机的3306端口，映射到 `db` 容器的3306端口。
    

#### **2. 应用配置**

修改并保存好文件后，在 `maple079-docker` 目录下执行以下命令来让配置生效：

Bash

```
docker-compose up -d
```

这个命令会自动重启 `db` 容器并应用新的端口映射。

#### **3. ⚠️ 配置云服务器防火墙 (极其重要！)**

**安全警告**：直接将数据库端口暴露在公网是非常危险的。我们**必须**设置防火墙规则，只允许您自己家里的电脑访问这个端口。

1. **获取您本地电脑的公网IP**： 在您**本地电脑**的浏览器中访问 [https://www.ipip.net/](https://www.ipip.net/) 或搜索“我的IP”，您会看到您当前的公网IP地址，请复制它。
    
2. **登录华为云控制台**：
    
    - 进入您的ECS云服务器管理界面。
        
    - 找到并点击与您服务器绑定的“**安全组**”。
        
    - 在安全组规则中，点击“**添加入方向规则**”。
        
3. **添加入站规则**，并按下图填写：
    
    - **协议 (Protocol)**: `TCP`
        
    - **端口 (Port)**: `3306`
        
    - **源地址 (Source)**: **请务必填写您刚刚查到的、您自己电脑的公网IP地址** (例如 `112.96.x.x/32`)。
        
        - _末尾的 `/32` 表示仅限这一个IP地址，能提供最强的安全性。_
            
    
    这条规则的含义是：“只允许IP地址为 `[您的IP]` 的电脑，访问我服务器的 `3306` 端口”。这样就将风险降到了最低。
    

---

### **第二步：在您的本地电脑上准备Python环境**

现在回到您自己的电脑上进行操作。

1. **安装Python**: 如果您电脑上没有Python，请从官网 [python.org](https://www.python.org/) 下载并安装最新稳定版的Python 3。
    
2. **安装MySQL连接库**: 打开您本地电脑的命令行工具（Windows下的CMD或PowerShell，macOS下的终端），运行以下命令来安装Python连接MySQL所必需的库：
    
    Bash
    
    ```
    pip install mysql-connector-python
    ```
    

---

### **第三步：编写Python账号管理脚本**

在您本地电脑上，创建一个名为 `account_manager.py` 的文件，并将以下所有代码复制进去。

Python

```
import mysql.connector
import hashlib
import getpass

# --- 请在这里修改为您的配置 ---
DB_CONFIG = {
    'host': '您的服务器公网IP',  # 例如: '121.36.216.27'
    'user': 'root',
    'password': 'FFee@@861027',  # 您在docker-compose.yml中设置的密码
    'database': 'maplestory_079',
    'port': 3306
}
# -----------------------------

def hash_password(password):
    """使用SHA-1算法哈希密码"""
    return hashlib.sha1(password.encode('utf-8')).hexdigest()

def add_account(cursor, username, password):
    """添加一个新账号"""
    try:
        hashed_pass = hash_password(password)
        sql = "INSERT INTO accounts (name, password) VALUES (%s, %s)"
        cursor.execute(sql, (username, hashed_pass))
        print(f"\n[成功] 账号 '{username}' 已成功添加！")
        return True
    except mysql.connector.Error as err:
        if err.errno == 1062: # Error code for duplicate entry
            print(f"\n[失败] 错误：账号名 '{username}' 已存在。")
        else:
            print(f"\n[失败] 添加时发生数据库错误: {err}")
        return False

def delete_account(cursor, username):
    """删除一个账号"""
    try:
        # 先查询一次，确保账号存在
        cursor.execute("SELECT name FROM accounts WHERE name = %s", (username,))
        result = cursor.fetchone()
        if not result:
            print(f"\n[失败] 错误：账号 '{username}' 不存在。")
            return False

        # 确认删除
        confirm = input(f"您确定要删除账号 '{username}' 吗？这是一个不可逆的操作！(输入 y 确认): ")
        if confirm.lower() != 'y':
            print("\n操作已取消。")
            return False

        sql = "DELETE FROM accounts WHERE name = %s"
        cursor.execute(sql, (username,))
        
        if cursor.rowcount > 0:
            print(f"\n[成功] 账号 '{username}' 已被删除。")
            return True
        else: # 理论上不会发生，因为前面已经检查过了
            print(f"\n[失败] 错误：账号 '{username}' 不存在或删除失败。")
            return False

    except mysql.connector.Error as err:
        print(f"\n[失败] 删除时发生数据库错误: {err}")
        return False

def main():
    """主程序菜单"""
    try:
        print("正在连接到数据库...")
        connection = mysql.connector.connect(**DB_CONFIG)
        print("数据库连接成功！")
    except mysql.connector.Error as err:
        print(f"\n[致命错误] 无法连接到数据库: {err}")
        print("请检查：")
        print("1. DB_CONFIG中的服务器IP、密码是否正确。")
        print("2. 您的云服务器防火墙（安全组）是否已对您当前的IP开放3306端口。")
        return

    cursor = connection.cursor()

    while True:
        print("\n===== 冒险岛账号管理程序 =====")
        print("1. 添加新账号")
        print("2. 删除账号")
        print("3. 退出")
        choice = input("请输入您的选择 (1-3): ")

        if choice == '1':
            username = input("请输入要添加的账号名: ")
            password = getpass.getpass("请输入新密码 (输入时不可见): ")
            if username and password:
                if add_account(cursor, username, password):
                    connection.commit() # 只有成功才提交更改
            else:
                print("\n[错误] 账号名和密码不能为空。")

        elif choice == '2':
            username = input("请输入要删除的账号名: ")
            if username:
                if delete_account(cursor, username):
                    connection.commit() # 只有成功才提交更改
            else:
                print("\n[错误] 账号名不能为空。")

        elif choice == '3':
            break
        
        else:
            print("\n无效输入，请输入 1, 2 或 3。")

    cursor.close()
    connection.close()
    print("\n数据库连接已关闭。再见！")

if __name__ == "__main__":
    main()

```

---

### **第四步：如何运行和使用脚本**

1. 将上面的代码保存到您本地电脑的 `account_manager.py` 文件中。
    
2. **务必修改**代码开头的 `DB_CONFIG` 部分，填入您服务器的**公网IP**和正确的数据库密码。
    
3. 打开您本地电脑的命令行/终端，进入到该文件所在的目录。
    
4. 运行脚本：
    
    Bash
    
    ```
    python account_manager.py
    ```
    
5. 程序会启动，并显示一个菜单，您可以根据提示输入 `1` 或 `2` 来选择添加或删除账号，然后按提示输入账号名和密码即可。
    

现在，您拥有了一个专属的、便捷的账号管理工具！