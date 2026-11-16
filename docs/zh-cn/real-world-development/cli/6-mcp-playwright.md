---
title: "第 6 课 - 使用 Playwright MCP 验证功能"
description: "检查或配置 Copilot CLI 中的 Playwright MCP，并在浏览器中探索筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

前面已经提到，编写代码不只是写代码。我们还要处理数据、外部服务，并让 Copilot 使用更多自动化工具。这正是 MCP 服务器的用武之地。MCP 服务器可以扩展 Copilot CLI 的内置功能，为它提供更多工具和服务。

本课将：

- 了解 Model Context Protocol（MCP）是什么，以及 Copilot CLI 如何使用它。
- 如果尚未添加 Playwright MCP 服务器，则进行配置。
- 让智能体操作浏览器并探索筛选功能。

## 什么是 Model Context Protocol（MCP）？

[Model Context Protocol (MCP)][mcp-blog-post] 为 AI 智能体与外部工具和服务通信提供了一种方式。借助 MCP，AI 智能体可以实时访问外部工具和服务，获取最新信息并代表你执行操作。

智能体通过 MCP 服务器访问这些工具和资源。MCP 服务器充当 AI 智能体与外部工具和服务之间的桥梁。每个 MCP 服务器都会提供一组 AI 智能体可以访问的工具和资源。

一些常用的 MCP 服务器包括：

- **[GitHub MCP Server][github-mcp]：** 提供用于管理 GitHub 存储库、议题和拉取请求的 API。
- **[Playwright MCP Server][playwright-mcp-server]：** 使用 Playwright 提供浏览器自动化功能。

还有许多其他 MCP 服务器。GitHub 提供了一个 [MCP 注册表][mcp-registry]，帮助大家发现资源并参与生态建设。

> [!CAUTION]
> 应像对待项目中的其他依赖项一样对待 MCP 服务器。使用前请审查其源代码、验证发布者，并考虑安全影响。

## 添加 Playwright MCP 服务器

如果尚未配置 Playwright MCP 服务器，请添加它，让 Copilot 能像用户一样与网站交互。

> [!NOTE]
> 如果 Copilot CLI 中已配置 Playwright MCP，请跳过这些设置步骤，继续下一节。

1. 返回 Codespace。
2. 在 Copilot CLI 中输入以下命令，打开添加 MCP 服务器对话框：

   ```plaintext
   /mcp add
   ```

3. 输入名称 `playwright`，然后按 <kbd>Tab</kbd>。
4. 按 <kbd>Enter</kbd> 确认服务器类型为 **STDIO**，然后按 <kbd>Tab</kbd>。
5. 将以下内容粘贴到 **Command** 对话框中：

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. 按 <kbd>Control</kbd>+<kbd>S</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）保存新的 MCP 服务器。
7. 按 <kbd>Esc</kbd> 退出 MCP 对话框。

## 让 Copilot 使用 Playwright 探索功能

之前你亲自确认了功能是否正常。现在使用 Copilot 中的 Playwright MCP 服务器进行同样的检查！

1. 使用以下提示，让 Copilot 通过 Playwright MCP 服务器验证功能：

   ```plaintext
   启动应用，并使用 Playwright MCP 根据议题和计划检查筛选功能。告诉我哪些功能正常、哪些不正常，但不要进行任何更改。完成后停止你启动的服务器。
   ```

> [!NOTE]
> 通常不必明确告诉 Copilot 使用 MCP 服务器，因为它往往会自行判断。不过，既然你知道要使用哪个服务器，指出来总没有坏处！这有助于获得更一致的结果，也能节省一些令牌。

2. 观察 Copilot 在浏览器中列出的验证步骤。
3. 阅读报告，确认所有功能符合预期。

Copilot 会启动服务器，使用 Playwright 与网站交互，停止服务器，并向你提供报告。

## 总结与后续步骤

恭喜！你已在 Copilot CLI 中使用 Playwright MCP 服务器，通过真实浏览器探索功能。回顾一下，你：

- 了解了 Model Context Protocol（MCP）是什么，以及 Copilot CLI 如何使用它。
- 如果此前尚未配置 Playwright MCP 服务器，则完成了添加。
- 让智能体操作浏览器并探索筛选功能。

接下来，[创建 QA 自定义智能体][next-lesson]，将技能与浏览器工具整合到一个专门角色中。

## 资源

- [MCP 到底是什么，为什么大家都在讨论？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [向 Copilot CLI 添加 MCP 服务器][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
