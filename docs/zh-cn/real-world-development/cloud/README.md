---
slug: zh-cn/real-world-development/cloud
title: "GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

**[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent)** 可让 GitHub Copilot 在云端异步工作。你可以在 GitHub 上分配任务，cloud agent 会在后台接手——探索存储库、进行更改并创建拉取请求——而你可以继续处理其他工作。

通过这些练习，你将添加 cloud agent 会遵循的自定义说明，然后分配 GitHub 议题，让它实现任务。你还将审查并使用自定义智能体来指导它的工作方式，通过智能体仪表板监控并引导会话，最后审查它创建的拉取请求并迭代优化结果。

## 练习

| 练习 | 主题 | 说明 |
|----------|-------|-------------|
| [0. 先决条件][ex0] | 设置 | 创建存储库和 Codespace |
| [1. 自定义说明][ex1] | 上下文 | 添加 cloud agent 会遵循的自定义说明 |
| [2. Cloud Agent][ex2] | 异步智能体 | 将议题分配给 Copilot cloud agent |
| [3. 自定义智能体][ex3] | 专业智能体 | 审查并使用自定义智能体 |
| [4. 管理智能体][ex4] | 监控 | 监控并引导智能体会话 |
| [5. 迭代][ex5] | 审查 | 审查 PR、迭代改进 Copilot 的工作并选择后续步骤 |

## 先决条件

参加本工作坊前，请确保具备以下条件：

- [ ] 拥有 GitHub 帐户，并订阅有效的 **Copilot Student、Pro、Pro+、Business 或 Enterprise** 计划
- [ ] 已为帐户或组织启用 Copilot cloud agent

> [!TIP]
> 没有付费计划？通过 [GitHub Education][callout-student-plan-education] 验证身份的学生可免费使用 GitHub Copilot。**Copilot Student** 计划包含本工作坊所用的智能体、MCP、代码审查和 Copilot CLI 功能，可用于完成所有工作环境练习。

[callout-student-plan-education]: https://github.com/education/students

> [!NOTE]
> Copilot cloud agent 支持 **Copilot Student、Pro、Pro+、Business 和 Enterprise**，不支持 Copilot Free。在 Copilot Business 或 Enterprise 计划中，管理员必须为组织启用该功能。

> [!NOTE]
> 本工作坊不涉及 MCP。若想了解如何将 MCP 服务器与 Copilot 配合使用，请参阅 [CLI 工作坊](../cli/)或 [VS Code 工作坊](../vscode/)。

## 开始

**[从练习 0：先决条件开始 →][ex0]**

[ex0]: 0-prerequisites/
[ex1]: 1-custom-instructions/
[ex2]: 2-cloud-agent/
[ex3]: 3-custom-agents/
[ex4]: 4-managing-agents/
[ex5]: 5-iterating/
