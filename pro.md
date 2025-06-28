graph TD
    subgraph 用户操作
        A[你在本地修改并提交] --> B(git push 到 Gitee 仓库);
    end

    subgraph Gitee (源仓库)
        B --> C{Gitee 检测到 Push 事件};
        C --> D[Gitee 触发 WebHook];
        D -- 发送 HTTP POST 请求 --> E(Vercel Serverless Function <br> api/webhook.js);
        D -- 携带 Gitee 事件数据 <br> (Payload) 和可选的 Secret --> E;
    end

    subgraph Vercel (中间层服务)
        E --> F{Vercel Function 接收 WebHook 请求};
        F -- 1. 验证 Gitee Secret (可选) --> G{提取所需信息};
        G -- 2. 准备 GitHub API 请求 --> H(调用 GitHub REST API <br> /repos/{owner}/{repo}/dispatches);
        H -- 携带 GitHub PAT (作为认证) <br> 和 event_type: 'gitee_push' --> I(GitHub API Gateway);
    end

    subgraph GitHub (目标仓库)
        I --> J{GitHub 接收 API 请求};
        J --> K{GitHub 触发 repository_dispatch 事件};
        K --> L[GitHub Actions 工作流 <br> (.github/workflows/sync.yml) 启动];
        L -- 1. 检出 GitHub 仓库 <br> (uses: actions/checkout) --> M(GitHub Actions Runner);
        L -- 2. 配置 SSH key (Gitee 私钥来自 GitHub Secrets) --> M;
        M -- 3. git pull Gitee master --rebase --> N(从 Gitee 拉取最新代码);
        N --> O(将 Gitee master 内容合并到 GitHub main);
        O -- 4. git push origin main --force --> P(推送到 GitHub main 分支);
    end

    subgraph 结果
        P --> Q[GitHub 仓库同步更新];
        Q -- 最终状态 --> R(Gitee 和 GitHub 内容一致);
    end
