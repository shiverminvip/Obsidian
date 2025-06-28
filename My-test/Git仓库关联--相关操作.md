---

好的，你想在本地新建一个 Obsidian 仓库，然后同步地在 Gitee 上也创建一个同名的新仓库。这个过程分为两个主要部分：**在本地初始化 Obsidian 库为 Git 仓库**，以及**在 Gitee 上创建新仓库并进行首次同步**。

下面是详细的步骤：

---

## 1. 在本地初始化 Obsidian 仓库

首先，在你的电脑上创建一个新的 Obsidian 笔记库。

1. **新建 Obsidian 笔记库：**
    
    - 打开 Obsidian。
        
    - 点击左下角的 **“打开另一个库”** (Open another vault) 图标。
        
    - 选择 **“创建新库”** (Create new vault)。
        
    - 为你的库命名（例如：`MyNewObsidianVault`），并选择一个本地存储路径。记住这个名称和路径，我们稍后会用到。
        
    - 创建完成后，Obsidian 会打开这个新的空库。
        
2. **打开命令行工具并进入库目录：**
    
    - 打开 **Git Bash** (推荐) 或 **Windows PowerShell**。
        
    - 使用 `cd` 命令进入你刚刚创建的 Obsidian 库的根目录。
        
        - 例如，如果你的库路径是 `D:\Obsidian\MyNewObsidianVault`，则输入：
            
            Bash
            
            ```
            cd D:/Obsidian/MyNewObsidianVault
            ```
            
            或 (PowerShell):
            
            PowerShell
            
            ```
            Set-Location D:\Obsidian\MyNewObsidianVault
            ```
            
3. **初始化 Git 仓库：**
    
    - 在该目录下执行 Git 初始化命令。这会在你的 Obsidian 库文件夹中创建一个隐藏的 `.git` 文件夹，将其变成一个本地 Git 仓库。
        
    
    Bash
    
    ```
    git init
    ```
    
4. **添加并提交初始文件：**
    
    - 尽管现在库是空的，或者只有 Obsidian 的 `.obsidian` 配置文件夹，我们仍然需要进行一次初始提交。
        
    - 将所有文件添加到暂存区：
        
    
    Bash
    
    ```
    git add .
    ```
    
    - 提交到本地仓库：
        
    
    Bash
    
    ```
    git commit -m "Initial commit for new Obsidian vault"
    ```
    

---

## 2. 在 Gitee 上创建同名新仓库并同步

现在，我们去 Gitee 上创建这个同名的远程仓库，并与本地库进行关联和同步。

1. **在 Gitee 上创建新仓库：**
    
    - 登录 Gitee ([gitee.com](https://gitee.com/))。
        
    - 在右上角点击绿色的 **“+”** 号图标，选择 **“新建仓库”**。
        
    - **关键步骤：**
        
        - **仓库名称：** 这里**输入与你本地 Obsidian 库完全相同的名称**（例如：`MyNewObsidianVault`）。
            
        - **是否开源：** 根据你的需求选择“公开”或“私有”。
            
        - **选择模板：** 保持 **“不使用模板”**。
            
        - **初始化仓库：** **务必不要勾选“使用 Readme 文件初始化这个仓库”**。我们需要一个完全空的远程仓库来与本地对齐。
            
    - 点击底部的 **“创建”** 按钮。
        
2. **复制远程仓库的 SSH 地址：**
    
    - 仓库创建成功后，Gitee 会显示仓库的页面。
        
    - 在页面上方找到并复制你的新仓库的 **SSH 地址**（通常以 `git@gitee.com:` 开头）。例如：`git@gitee.com:your_username/MyNewObsidianVault.git`。
        
3. **关联本地仓库与远程仓库：**
    
    - 回到你的命令行工具（Git Bash 或 PowerShell），确保你还在 Obsidian 库的目录下。
        
    - 执行以下命令，将本地仓库与 Gitee 上的远程仓库关联起来：
        
    
    Bash
    
    ```
    git remote add origin 你的Gitee仓库SSH地址
    ```
    
    - 例如：
        
    
    Bash
    
    ```
    git remote add origin git@gitee.com:your_username/MyNewObsidianVault.git
    ```
    
4. **推送本地内容到 Gitee：**
    
    - 最后一步，将你本地仓库的内容推送到远程的 Gitee 仓库。
        
    
    Bash
    
    ```
    git push -u origin master
    # 或者如果你的默认分支是 main
    # git push -u origin main
    ```
    
    - `-u` 参数（或 `--set-upstream`）会将本地的 `master`/`main` 分支和远程的 `origin/master`/`main` 分支关联起来，这样以后你只需要简单地 `git push` 和 `git pull` 即可。
        

完成以上步骤后，刷新你的 Gitee 仓库页面，你就会看到你的 Obsidian 库的初始文件（包括 `.obsidian` 配置文件夹）已经成功同步到 Gitee 上的同名仓库了。

现在，你就可以在 Obsidian 中使用 Git 插件的**自动同步功能**，让它帮你管理后续的更改了。