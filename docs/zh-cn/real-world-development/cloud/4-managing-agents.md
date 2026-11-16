---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

在前几节练习中，你让 Copilot cloud agent 执行了三个独立任务，用于改进用户体验和添加功能。cloud agent 虽然可以自主异步工作，但监控这些任务仍然很重要。

有多种工具可用于管理分配给 cloud agent 的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个控制中心，你可以查看所有具有未关闭拉取请求（PR）的智能体任务，了解已执行的操作，甚至可以在会话进行中引导它。

本课将：

- 浏览智能体页面，监控 cloud agent 任务。
- 在会话进行中引导智能体，要求添加其他功能。

## 场景

将任务分配给智能体以创建高对比度模式后，团队意识到也可以同时添加浅色模式。由于网站样式和切换功能已经在更新，把这项功能也纳入其中很合理。现在要引导智能体的工作，确保它在实现高对比度模式的同时也添加浅色模式。

## 查看 Copilot cloud agent 任务

查看当前分配给 Copilot cloud agent 的所有任务状态。

1. 转到 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 注意主面板和左侧面板中的任务列表。你应该能看到已分配给 Copilot 的任务，包括：
    - 更新代码文档。
    - 生成用于修改游戏的 API。
    - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 已执行的步骤，包括：
    - 从存储库检出代码。
    - 为 Copilot 创建工作环境。
    - 设置 MCP 服务器。
    - 执行完成分配任务所需的各种步骤。

> [!NOTE]
> 具体显示的步骤因 Copilot 当前的工作状态和执行方案而异。

4. 还要注意右侧显示的 PR 面板。你可以在这里查看 PR 和更改的文件，以便进一步监控。

## 引导 cloud agent

你已查看当前正在进行的任务。现在要求 Copilot 在实现高对比度模式时，也添加浅色模式切换开关。

1. 选择与添加高对比度模式相关的会话。确切标题会根据 Copilot 使用的名称和当前工作状态而有所不同。

    ![控制中心中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 监控会话几分钟，直到会话显示已完成设置并开始工作。当你看到类似下方的消息时，就说明已开始工作。
3. 在 **Steer active session while Copilot is working** 对话框中，输入以下提示：

    ```plaintext
    实现高对比度模式时，也添加浅色模式。浅色模式也应该有一个开关，让用户选择所需的显示模式。
    ```

    ![智能体页面中的 cloud agent 任务截图，突出显示 Steer active session while Copilot is working 对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 注意 Copilot 如何确认收到提示并将其纳入工作流程。

## 让 Copilot 继续工作

和之前一样，Copilot 会继续处理更新后的任务！它会先完成发送消息时正在执行的具体步骤，再将新请求纳入工作流程。

同样，这需要几分钟。可以利用这段时间暂停一下，回顾目前学到和探索的内容。

## 总结与后续步骤

本课介绍了 Copilot 智能体页面，它是监控和引导 GitHub Copilot cloud agent 任务的中心枢纽。通过这个控制中心，你可以跟踪所有活动和已完成的任务、审查正在进行的工作，甚至可以重定向执行中的任务来调整范围或提供额外指导。

你了解了：

- 使用控制中心和智能体页面监控 cloud agent 任务。
- 重定向正在进行的会话，要求添加其他功能。

Copilot 正在完成无障碍功能，接下来可以[迭代改进 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
