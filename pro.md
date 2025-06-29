graph TD
    subgraph 用户操作
        A[你在本地修改并提交] --> B[git push 到 Gitee 仓库]
    end

    subgraph 吞吞吐吐
        B --> C{Gitee 检测到 Push 事件}
        C --> D[Gitee 触发 WebHook]
        D -->|发送 HTTP POST 请求| E[Vercel Serverless Function api/webhook.js]
        D -->|携带 Gitee 事件数据 payload 和 secret| E
    end

    subgraph 中转操作
        E --> F{Vercel Function 接收 WebHook 请求}
        F -->|验证 Gitee Secret| G[提取所需信息]
        F -->|准备 GitHub API 请求| H[调用 GitHub REST API dispatches 接口]
        H -->|使用 GitHub PAT 和 event_type| I[GitHub API Gateway]
    end

    subgraph GitHub 目标仓库
        I --> J{GitHub 接收 API 请求}
        J --> K{触发 repository_dispatch 事件}
        K --> L[GitHub Actions sync.yml 启动]
        L -->|检出 GitHub 仓库| M[Actions Runner]
        L -->|配置 SSH key| M
        M -->|git pull Gitee master --rebase| N[拉取 Gitee 最新代码]
        N --> O[合并到 GitHub main]
        O -->|git push origin main --force| P[推送到 GitHub main]
    end

    subgraph 结果
        P --> Q[GitHub 仓库同步完成]
        Q --> R[Gitee 和 GitHub 保持一致]
    end
