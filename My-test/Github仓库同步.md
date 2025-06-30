明白了！你想实现：

- **仓库A** 是 Obsidian，目录是 `My-test`
    
- **仓库B** 是 `mynote`，目录是 `笔记/Obsidian`
    
- 只要单向同步（仓库A → 仓库B）
    
- 监听仓库A的 `My-test` 目录变动，自动同步到仓库B的 `笔记/Obsidian` 目录
    

---

我帮你写详细的 GitHub Actions 工作流代码，还有 SSH Key 生成和配置步骤。

---

# 一、生成 SSH Key 并配置权限

1. 在你本地机器（Windows/Mac/Linux）打开终端，运行：
    

bash

复制编辑

`ssh-keygen -t ed25519 -C "github-actions" -f gh-sync-key -N ""`

- 生成两个文件：`gh-sync-key`（私钥）和 `gh-sync-key.pub`（公钥）
    
- `-N ""` 表示不设置密码
    

2. **添加公钥到仓库 B**
    

- 打开 GitHub，进入仓库 B (`mynote`) 页面
    
- 点击 **Settings** > **Deploy keys** > **Add deploy key**
    
- 标题随意写，比如：`GitHub Actions Sync Key`
    
- 把 `gh-sync-key.pub` 文件内容复制粘贴进去
    
- 勾选 **Allow write access**
    
- 点击保存
    

3. **把私钥添加到仓库 A 的 Secrets**
    

- 进入仓库 A (`Obsidian`) 页面
    
- 点击 **Settings** > **Secrets and variables** > **Actions** > **New repository secret**
    
- 名称填 `SSH_PRIVATE_KEY`
    
- 内容填 `gh-sync-key` 文件的全部私钥内容（打开这个文件，复制粘贴全部文本）
    
- 保存
    

---

# 二、GitHub Actions Workflow 代码（放仓库A）

文件路径：`.github/workflows/sync-to-mynote.yml`

yaml

复制编辑

`name: Sync My-test to mynote repo  on:   push:     branches:       - main     paths:       - 'My-test/**'  # 只监听这个目录的变动  jobs:   sync:     runs-on: ubuntu-latest      steps:       - name: Checkout repo A         uses: actions/checkout@v3         with:           fetch-depth: 0        - name: Setup SSH         uses: webfactory/ssh-agent@v0.8.1         with:           ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}        - name: Clone mynote repo         run: |           git clone git@github.com:yourusername/mynote.git           cd mynote           git checkout main        - name: Sync My-test to mynote/笔记/Obsidian         run: |           rsync -av --delete ./My-test/ ./mynote/笔记/Obsidian/        - name: Commit and push changes         run: |           cd mynote           git config user.name "github-actions[bot]"           git config user.email "github-actions[bot]@users.noreply.github.com"            if [ -n "$(git status --porcelain)" ]; then             git add 笔记/Obsidian/             git commit -m "Sync from Obsidian My-test at ${{ github.sha }}"             git push origin main           else             echo "No changes to commit"           fi`

---

# 三、使用说明

- 把 `yourusername` 换成你的 GitHub 用户名
    
- 确保两个仓库的默认分支都是 `main`，否则修改为你的分支名
    
- 这个 Workflow 只在 `My-test` 目录有修改时触发
    
- 它会将 `My-test` 目录内容完全同步覆盖到仓库 B 的 `笔记/Obsidian` 目录
    
- 如果没有变更不会提交，避免无效提交
    

---

# 四、总结

1. 本地生成 SSH Key
    
2. 添加公钥到仓库 B 的 Deploy Key，允许写入权限
    
3. 添加私钥到仓库 A 的 Actions Secret
    
4. 仓库 A 新建上述 Workflow 文件
    
5. 提交后自动生效，未来每次 `My-test` 目录变更都会自动同步更新到仓库 B
    

---

如果你需要，我还能帮你写个双向同步版本或者带分支选择的版本，告诉我哈！