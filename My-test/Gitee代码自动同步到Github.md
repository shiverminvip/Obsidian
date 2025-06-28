好的，我们来使用 **GitHub Actions** 实现 Gitee 仓库到 GitHub 仓库的自动化同步。

这种方法的核心是：当你的 Gitee 仓库有新的提交时，你可以通过某种方式（例如，手动触发 GitHub Actions，或者更高级的通过 Gitee WebHook 触发）来运行一个 GitHub Actions 工作流。这个工作流会从 Gitee 拉取最新代码，然后将其推送到 GitHub。

为了简化并确保成功，我们将主要设置一个在 **GitHub 仓库中运行的工作流**，它会从 Gitee 拉取代码并推送到 GitHub。

---

### **实现 Gitee 到 GitHub 自动同步的步骤 (使用 GitHub Actions)**

这个过程涉及几个关键部分：

1. **生成一个新的 SSH 密钥对**：专门用于 GitHub Actions 访问 Gitee。
    
2. **将公钥添加到 Gitee 仓库的部署密钥中**：允许 GitHub Actions 读取 Gitee 仓库。
    
3. **将私钥添加到 GitHub 仓库的 Secrets 中**：让 GitHub Actions 安全地使用私钥。
    
4. **创建 GitHub Actions 工作流文件**：定义同步的逻辑。
    

---

#### **步骤 1：生成一个新的 SSH 密钥对**

为了让 GitHub Actions 能够访问你的 Gitee 仓库，我们需要一个专门的 SSH 密钥对。**这个密钥对是为自动化过程准备的，不要与你本地电脑使用的密钥混淆。**

1. **打开 Git Bash 或你的 Linux/macOS 终端。**
    
2. 执行以下命令生成密钥对（请选择一个你容易记住的文件名，例如 `gitee_github_sync_key`）：
    
    Bash
    
    ```
    ssh-keygen -t rsa -b 4096 -C "gitee_to_github_sync_key" -f ~/.ssh/gitee_github_sync_key
    ```
    
    - `-f ~/.ssh/gitee_github_sync_key`：指定密钥文件的名称和路径。
        
    - 当提示 `Enter passphrase (empty for no passphrase):` 时，直接按两次 **回车键** 留空。这个密钥是用于自动化的，不应该设置密码。
        
3. 生成后，你会在 `~/.ssh/` 目录下找到两个文件：
    
    - `gitee_github_sync_key` (私钥)
        
    - `gitee_github_sync_key.pub` (公钥)
        
4. **复制公钥内容：**
    
    Bash
    
    ```
    cat ~/.ssh/gitee_github_sync_key.pub
    ```
    
    复制从 `ssh-rsa` 开始到最后的注释（`gitee_to_github_sync_key`）的所有内容。
    
5. **复制私钥内容：**
    
    Bash
    
    ```
    cat ~/.ssh/gitee_github_sync_key
    ```
    
    复制从 `-----BEGIN RSA PRIVATE KEY-----` 开始到 `-----END RSA PRIVATE KEY-----` 结束的所有内容。**请务必妥善保管私钥，不要泄露。**
    

---

#### **步骤 2：将公钥添加到 Gitee 仓库的部署密钥中**

这一步是为了让 GitHub Actions 能够从你的 Gitee 仓库拉取代码。

1. **登录 Gitee。**
    
2. 进入你要同步的**Gitee 仓库**页面。
    
3. 点击仓库页面右侧的 **“管理”** 按钮。
    
4. 在左侧导航栏中找到 **“部署公钥管理”**。
    
5. 点击 **“添加公钥”** 按钮。
    
6. **标题：** 填写一个易于识别的名称，例如 `GitHub Actions Sync Key`。
    
7. **公钥：** 粘贴你在 **步骤 1.4** 中复制的 `gitee_github_sync_key.pub` 的内容。
    
8. **权限：** 勾选 **“只读”**。GitHub Actions 只需要从 Gitee 拉取代码，不需要写入。
    
9. 点击 **“确定”**。Gitee 可能会要求你输入密码进行确认。
    

---

#### **步骤 3：将私钥添加到 GitHub 仓库的 Secrets 中**

这一步是为了让 GitHub Actions 工作流能够安全地使用私钥来访问 Gitee。

1. **登录 GitHub。**
    
2. 进入你要同步的**GitHub 仓库**页面（这个仓库应该是空的，或者已经有 Gitee 的内容）。
    
3. 点击仓库上方的 **“Settings”** (设置) 选项卡。
    
4. 在左侧导航栏中，找到 **“Security”** 下的 **“Secrets and variables”**，然后点击 **“Actions”**。
    
5. 点击右上角的 **“New repository secret”** (新建仓库密钥)。
    
6. **Name：** 输入 `GITEE_PRIVATE_KEY` (这个名称在后续的工作流文件中会用到，请精确匹配)。
    
7. **Secret：** 粘贴你在 **步骤 1.5** 中复制的 `gitee_github_sync_key` 的**私钥内容**。
    
8. 点击 **“Add secret”**。
    

---

#### **步骤 4：创建 GitHub Actions 工作流文件**

现在，我们来定义实际的同步逻辑。

1. 在你的 **GitHub 仓库**页面，点击 **“Actions”** 选项卡。
    
2. 点击 **“set up a workflow yourself”** 或 **“New workflow”** (如果你还没有任何工作流)。
    
3. 系统会打开一个编辑器。将默认内容删除，然后粘贴以下 YAML 代码：
    
    YAML
    
    ```
    name: Sync Gitee to GitHub
    
    on:
      push:
        branches:
          - master # 监听 Gitee 推送到 GitHub 仓库的 master 分支
          - main   # 如果你的主分支是 main，也监听 main 分支
      workflow_dispatch: # 允许手动触发工作流
    
    jobs:
      sync:
        runs-on: ubuntu-latest # 在最新的 Ubuntu 虚拟机上运行
        steps:
          - name: Checkout GitHub Repository
            uses: actions/checkout@v4
            with:
              fetch-depth: 0 # 获取所有历史，包括标签和所有分支
    
          - name: Add Gitee Remote
            run: |
              # 配置 Git，使用 SSH 密钥
              mkdir -p ~/.ssh
              echo "${{ secrets.GITEE_PRIVATE_KEY }}" > ~/.ssh/gitee_key
              chmod 600 ~/.ssh/gitee_key
              echo "Host gitee.com" >> ~/.ssh/config
              echo "  IdentityFile ~/.ssh/gitee_key" >> ~/.ssh/config
              echo "  StrictHostKeyChecking no" >> ~/.ssh/config # 首次连接时跳过主机密钥检查
    
              # 添加 Gitee 作为新的远程仓库
              git remote add gitee git@gitee.com:<你的Gitee用户名>/<你的Gitee仓库名>.git
              # 替换 <你的Gitee用户名> 和 <你的Gitee仓库名>
    
          - name: Fetch from Gitee
            run: |
              git fetch gitee --tags # 拉取 Gitee 的所有分支和标签
    
          - name: Sync Master/Main Branch
            run: |
              # 切换到主分支 (假设你主要同步 master 或 main)
              # 如果你的主分支是 master:
              git checkout master
              git pull gitee master --rebase # 从 Gitee 的 master 分支拉取并变基
              git push origin master --force # 强制推送到 GitHub 的 master (如果 Gitee 是权威源)
    
              # 如果你的主分支是 main:
              git checkout main
              git pull gitee main --rebase # 从 Gitee 的 main 分支拉取并变基
              git push origin main --force # 强制推送到 GitHub 的 main (如果 Gitee 是权威源)
    
              # 注意：这里假设你的 Gitee 和 GitHub 仓库的主分支名称一致。
              # 如果不一致，你需要调整 git checkout 和 git pull/push 的分支名。
              # 比如 Gitee 是 master，GitHub 是 main，则：
              # git checkout main
              # git pull gitee master --rebase
              # git push origin main --force
    
          - name: Sync Other Branches (Optional)
            run: |
              # 遍历 Gitee 的所有分支并推送到 GitHub
              for branch in $(git branch -r | grep 'gitee/' | sed 's/gitee\///g' | grep -v 'HEAD'); do
                git checkout -B $branch gitee/$branch
                git push origin $branch --force
              done
    
          - name: Sync Tags (Optional)
            run: |
              git push origin --tags # 推送所有标签
    ```
    
4. **修改占位符：**
    
    - 将 `name: Sync Gitee to GitHub` (工作流名称) 修改为你喜欢的。
        
    - 在 `Add Gitee Remote` 步骤中，将 `git@gitee.com:<你的Gitee用户名>/<你的Gitee仓库名>.git` 替换为你的**实际 Gitee 仓库 SSH 地址**。
        
    - 根据你的实际情况，调整 `on:` 部分的 `branches` (是 `master` 还是 `main`，或者两者都有)。
        
    - 在 `Sync Master/Main Branch` 步骤中，根据你的主分支名称，保留或修改 `master` 或 `main` 相关的命令。**请仔细检查你的 Gitee 和 GitHub 的主分支名称。**
        
    - `--force` 推送是危险的，因为它会覆盖远程仓库的历史。这里使用它是为了确保 Gitee 的内容是权威的，直接覆盖 GitHub。**如果你不确定或有复杂的分支管理需求，请谨慎使用 `--force`，并考虑更精细的合并策略。**
        
5. **提交工作流：**
    
    - 点击右上角的 **“Commit changes”** 按钮。
        
    - 输入提交信息，例如 `feat: Add Gitee to GitHub sync workflow`。
        
    - 选择 **“Commit directly to the main branch”** (直接提交到主分支)。
        
    - 点击 **“Commit changes”**。
        

---

### **如何触发同步？**

1. **手动触发 (推荐初期调试和简单场景)：**
    
    - 在 GitHub 仓库页面，点击 **“Actions”** 选项卡。
        
    - 在左侧导航栏中，点击你的工作流名称 (`Sync Gitee to GitHub`)。
        
    - 在右侧，你会看到一个 **“Run workflow”** 按钮。点击它，然后选择要运行的分支，再点击 **“Run workflow”**。
        
    - 工作流就会开始运行，尝试从 Gitee 拉取并推送到 GitHub。
        
2. **通过 Gitee push 自动触发 (更高级，需要额外配置)：** 你工作流的 `on: push:` 配置意味着，当**这个 GitHub 仓库**接收到 `push` 事件时，工作流会运行。 要实现 Gitee push 触发 GitHub Actions，通常需要：
    
    - **Gitee WebHook -> 中间服务 -> GitHub `repository_dispatch` 事件：** 这是最可靠但最复杂的方案，需要你搭建一个中间服务器来接收 Gitee 的 WebHook，然后调用 GitHub API 触发 GitHub Actions 的 `repository_dispatch` 事件。这超出了一个简单的 YAML 文件配置的范畴。
        
    - **定期运行 (Scheduled Workflow)：** 你可以在 GitHub Actions 工作流中添加 `on: schedule:` 触发器，让它每隔一段时间（例如每天或每小时）自动运行一次，从而拉取 Gitee 的最新内容。 在 `on:` 部分添加：
        
        YAML
        
        ```
        on:
          push:
            branches:
              - master
              - main
          workflow_dispatch:
          schedule:
            - cron: '0 * * * *' # 每小时运行一次，根据需要调整 cron 表达式
        ```
        

**建议：** 初期先使用**手动触发**来测试工作流是否正常。如果一切顺利，并且你对自动化有更高要求，可以考虑添加 `schedule` 触发器，或者研究如何通过 Gitee WebHook 触发 GitHub `repository_dispatch` 事件。

希望这个详细的步骤能帮助你成功设置 Gitee 到 GitHub 的自动同步！