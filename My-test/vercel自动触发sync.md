你可以直接在 GitHub 上新建一个项目（或者在你现有的项目里创建子文件夹），然后创建 `api/webhook.js` 文件并粘贴代码。接着，通过 Vercel 的 Web 界面连接这个 GitHub 项目，Vercel 就能自动检测并部署你的函数。

---

### **Vercel Web 端部署的详细步骤**

下面是完整流程，一步步教你如何操作：

1. **在 GitHub 上创建你的函数仓库**
    
    - 登录 **GitHub**。
        
    - 点击右上角的 **“+”** 号，选择 **“New repository”** (新建仓库)。
        
    - **Repository name (仓库名称):** 输入一个名字，比如 `gitee-webhook-dispatcher`。
        
    - 选择 **“Public”** 或 **“Private”** (通常选 Public 即可，因为里面不包含敏感信息，敏感信息存在 Vercel 环境变量里)。
        
    - **不要勾选** “Add a README file” 或其他初始化选项。选择一个**空仓库**。
        
    - 点击 **“Create repository”** (创建仓库)。
        
    - 在新创建的仓库页面，点击 **“creating a new file”** 链接，或者点击 **“Add file”** -> **“Create new file”**。
        
    - **在文件路径/名称框中，输入 `api/webhook.js`**。GitHub 会自动为你创建 `api` 文件夹。
        
    - 将之前提供的 **`webhook.js` 代码**粘贴到文件内容区域。
    - ```
```
```// api/webhook.js
const https = require('https');
const crypto = require('crypto'); // 用于 Webhook 签名验证（可选）

// 从 Vercel 环境变量中获取（这些变量将在后面设置）
const GITHUB_PAT = process.env.GITHUB_PAT;
const GITHUB_REPO_OWNER = process.env.GITHUB_REPO_OWNER;
const GITHUB_REPO_NAME = process.env.GITHUB_REPO_NAME;
const GITEE_WEBHOOK_SECRET = process.env.GITEE_WEBHOOK_SECRET; // 可选，但强烈推荐用于安全

// 用于验证 Gitee Webhook 签名的函数（可选，但推荐）
function verifyGiteeSignature(payload, secret, signatureHeader) {
  if (!secret || !signatureHeader) {
    console.warn('Gitee WebHook 密钥或签名头未提供。跳过验证。');
    return true; // 如果未设置密钥，则无法验证，带警告继续。
  }
  // Gitee 对于 Push 事件的 Webhook 配置，如果设置了“密码”，通常会在 X-Gitee-Token 头中发送该密码。
  // 对于 HMAC-SHA256 签名，Gitee 通常使用 'X-Gitee-Signature'。
  // 这里的逻辑是针对 Gitee Webhook 配置中直接使用“密码”的情况进行比较。
  if (signatureHeader === secret) {
    return true;
  }
  console.error('Gitee WebHook 签名不匹配！');
  return false;
}

module.exports = (req, res) => {
  // 确保请求是 POST 方法
  if (req.method !== 'POST') {
    res.status(405).send('Method Not Allowed');
    return;
  }

  // 如果配置了密钥，检查 Gitee 的 X-Gitee-Token 头
  const giteeTokenHeader = req.headers['x-gitee-token'];
  if (GITEE_WEBHOOK_SECRET && !verifyGiteeSignature(req.body, GITEE_WEBHOOK_SECRET, giteeTokenHeader)) {
      console.error('无效的 Gitee WebHook 令牌！');
      res.status(403).send('Forbidden: Invalid Gitee Token');
      return;
  }

  // 检查是否是来自 Gitee 的 'Push Hook' 事件
  const giteeEvent = req.headers['x-gitee-event'];
  if (giteeEvent !== 'Push Hook') {
    res.status(200).send(`忽略 Gitee 事件：${giteeEvent}`);
    return;
  }

  console.log('接收到 Gitee push 事件。正在触发 GitHub Actions...');

  // 为 GitHub 的 repository_dispatch 事件准备 payload
  const postData = JSON.stringify({
    event_type: 'gitee_push', // 这必须与你的 GitHub Actions 工作流中定义的 'types' 匹配
    client_payload: {
      gitee_event: giteeEvent,
      timestamp: new Date().toISOString(),
    }
  });

  // 发送给 GitHub API 的 HTTPS 请求选项
  const options = {
    hostname: 'api.github.com',
    port: 443,
    path: `/repos/${GITHUB_REPO_OWNER}/${GITHUB_REPO_NAME}/dispatches`,
    method: 'POST',
    headers: {
      'User-Agent': 'Gitee-GitHub-Sync-App', // GitHub API 要求
      'Accept': 'application/vnd.github.v3+json',
      'Authorization': `token ${GITHUB_PAT}`, // 使用你的 GitHub PAT 进行身份验证
      'Content-Type': 'application/json',
      'Content-Length': Buffer.byteLength(postData)
    }
  };

  // 向 GitHub API 发送请求
  const githubReq = https.request(options, (githubRes) => {
    let data = '';
    githubRes.on('data', (chunk) => {
      data += chunk;
    });
    githubRes.on('end', () => {
      if (githubRes.statusCode >= 200 && githubRes.statusCode < 300) {
        console.log('GitHub Actions 触发成功！');
        res.status(200).send('GitHub Actions 触发。');
      } else {
        console.error(`触发 GitHub Actions 失败：${githubRes.statusCode} - ${data}`);
        res.status(githubRes.statusCode).send(`触发 GitHub Actions 失败：${data}`);
      }
    });
  });

  githubReq.on('error', (e) => {
    console.error(`GitHub API 请求出现问题：${e.message}`);
    res.status(500).send(`GitHub API 请求错误：${e.message}`);
  });

  githubReq.write(postData);
  githubReq.end();
};
```

        
 拉到页面底部，点击 **“Commit new file”** (提交新文件)。
        
2. **在 Vercel 连接 GitHub 仓库并部署**
    
    - 登录 **Vercel Dashboard** ([vercel.com/dashboard](https://vercel.com/dashboard))。
        
    - 点击页面左上角的 **“Add New...”** (添加新项目)，然后选择 **“Project”** (项目)。
        
    - 在 **“Import Git Repository”** (导入 Git 仓库) 部分，选择 **“GitHub”**。Vercel 可能会要求你授权访问你的 GitHub 账号。
        
    - 在弹出的仓库列表中，找到并选择你刚刚在 GitHub 上创建的仓库 (例如 `gitee-webhook-dispatcher`)，点击 **“Import”** (导入)。
        
    - **配置项目：** Vercel 会自动识别这是一个 Node.js Serverless Function 项目。
        
        - **Project Name (项目名称):** 默认就是你的仓库名，你可以保留。
            
        - **Root Directory (根目录):** 默认是 `./`，保持不变。
            
        - 其他设置保持默认即可。
            
    - **设置环境变量：** 这是关键步骤！在部署页面的底部，找到 **“Environment Variables”** (环境变量) 部分。点击展开并添加以下四个变量，确保名称和值都准确无误：
        
        - `GITHUB_PAT`: **你的 GitHub 个人访问令牌 (PAT)**。
            
        - `GITHUB_REPO_OWNER`: **你的 GitHub 用户名** (例如：`shiverminvip`)。
            
        - `GITHUB_REPO_NAME`: **你的 GitHub 仓库名** (例如：`Obsidian`)。
            
        - `GITEE_WEBHOOK_SECRET`: **你为 Gitee WebHook 设置的密码字符串** (例如，一个随机生成的强密码，这个要和后面 Gitee 设置的密码一致)。
            
    - 点击 **“Deploy”** (部署) 按钮。
        
3. **获取 Vercel 函数 URL**
    
    - 部署完成后，Vercel 会显示一个恭喜页面。你会看到一个 **“Visit”** 或 **“Continue to Dashboard”** 按钮。
        
    - 点击进入项目 Dashboard，在项目概览页面，你会找到你的**部署 URL**。它通常是 `https://你的项目名.vercel.app/api/webhook`。
        
    - **复制这个 URL**，你需要在 Gitee WebHook 配置中使用它。

太好了！你已经成功部署了 Vercel 函数，并且获得了域名。这是一个非常棒的进展！

你看到的这三个域名分别是：

1. **`gitee-webhook-dispatcher.vercel.app`**：这是你的**生产环境域名**。它是你部署的函数的主要、稳定的 URL。在 Gitee 配置 Webhook 时，你通常会使用这个域名。
    
2. **`gitee-webhook-dispatcher-git-main-shivermins-projects.vercel.app`**：这是你的 `main` 分支的**预览部署域名**。Vercel 会自动为每次推送到 Git 分支（除了生产分支）的代码部署一个独特的预览 URL。这对于在合并到 `main` 之前测试新功能非常方便。
    
3. **`gitee-webhook-dispatcher-8ysfb9olj-shivermins-projects.vercel.app`**：这是一个**独特的部署 ID 域名**。每次 Vercel 部署，它都会获得一个独特的 ID。这个 URL 指向你特定部署版本。
    

---

### **Gitee Webhook 应该使用哪个域名？**

对于你的 Gitee Webhook，你应该使用**生产环境域名**：

**`https://gitee-webhook-dispatcher.vercel.app/api/webhook`**

下面我们来详细说明原因，并明确接下来的步骤。

---

### **下一步：配置 Gitee Webhook**

现在 Vercel 函数已经部署成功并通过 URL 访问，最后一步就是告诉 Gitee 将 Webhook 通知发送到哪里。

1. **登录 Gitee。**
    
2. 进入你要同步的 **Gitee 仓库**页面。
    
3. 点击仓库页面右侧的 **“管理”** 按钮。
    
4. 在左侧导航栏中，找到 **“WebHooks”**。
    
5. 点击 **“添加WebHook”** 按钮。
    
6. **URL:** 粘贴你的 Vercel 函数的生产环境域名 URL，并在后面加上 `/api/webhook`： **`https://gitee-webhook-dispatcher.vercel.app/api/webhook`**
    
7. **密码 (Password/Secret):**
    
    - 如果你之前在 Vercel 中设置了 `GITEE_WEBHOOK_SECRET` 环境变量，请在这里输入**完全相同**的密钥字符串。
        
    - **强烈建议设置此密钥以提高安全性。**如果你在 Vercel 中没有设置，这里可以留空（但为了安全考虑，建议稍后添加！）。
        
8. **触发事件 (触发事件):**
    
    - **只勾选 “Push” (推送) 事件。**这确保只有当代码推送到 Gitee 时，Webhook 才会触发。
        
9. 点击 **“添加”**。
    

---

### **最终测试和验证**

配置好 Gitee Webhook 后，你整个自动化同步管道就准备就绪了！

1. **在你的本地 Obsidian 库中做一些小的改动。** (例如，给某个笔记添加一行文字，或新建一个笔记)。
    
2. **提交更改：** `git commit -m "测试自动同步"`
    
3. **推送到 Gitee：** `git push`
    
4. **检查 Gitee WebHook 状态：** 返回你的 Gitee 仓库的 **WebHooks** 设置页面。你应该能看到你的 Webhook 最近的触发尝试，希望显示绿色的成功指示。
    
5. **检查 GitHub Actions：** 立即前往你的 **GitHub 仓库**，点击顶部的 **“Actions”** (操作) 选项卡。你应该会看到一个由 `repository_dispatch` 事件触发的新工作流运行（名称为 `gitee_push`）。
    
6. **验证 GitHub 仓库内容：** 一旦 GitHub Actions 工作流成功完成，刷新你的 GitHub 仓库页面。你在 Obsidian 中所做的更改并推送到 Gitee 的内容，现在应该也同步到你的 GitHub 仓库的 `main` 分支了。
    

就是这样！你的 Gitee 仓库现在会在你每次推送到 Gitee 时，自动同步到 GitHub。