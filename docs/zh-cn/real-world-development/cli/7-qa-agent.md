---
title: "第 7 课 - 创建并使用 QA 智能体"
description: "创建一个 QA 自定义智能体，将议题需求、quality-checks 技能和 Playwright MCP 结合起来。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

你已经使用 `quality-checks` 技能运行自动化检查，并使用 Playwright MCP 在浏览器中观察筛选体验。现在将这些能力整合到一个自定义智能体中，并为其定义清晰的 QA 流程。

本课将：

- 了解自定义智能体如何与说明、技能和 MCP 工具协作。
- 创建并检查可复用的质量保证配置。
- 选择 QA 智能体，根据筛选议题审查其发现。

## 场景

在创建拉取请求（PR）前，Tailspin Toys 希望统一检查需求、代码质量、自动化检查、测试覆盖范围和浏览器行为。自定义智能体可以协调 QA 流程，并生成可复用的报告。

## 什么是自定义智能体？

自定义智能体是在 Markdown 配置文件中定义的 Copilot 专业角色。配置文件说明智能体的用途、指令和可用工具。在本工作坊中，你将在 `.github/agents/qa.agent.md` 中定义 QA 角色，并在 Copilot CLI 中选择它。

你用过的自定义项各有不同用途：存储库说明描述团队标准；`quality-checks` 技能封装可重复执行的检查；Playwright MCP 提供浏览器工具；QA 配置则告诉 Copilot 如何使用这些能力评估需求并报告发现。QA 配置不会取代这些工具，也不需要启动另一个对话。

## 创建 QA 配置

创建功能 PR 前，让 Copilot 创建一个可复用的 QA 配置。配置中会定义 QA 执行的检查和必须遵守的边界。

1. 返回 Codespace，并确认筛选对话处于 Interactive 模式。
2. 让默认智能体创建新的自定义智能体：

   ```plaintext
   创建一个名为 QA 的自定义智能体，并将配置保存到 .github/agents/qa.agent.md。它应根据议题和已达成一致的需求检查功能，遵循存储库说明，运行 quality-checks 技能，使用 Playwright MCP 验证行为，并在测试覆盖不足时添加测试。

   要求它通过通过、失败或受阻来报告每项需求，并提供证据。修改实现代码前必须征求同意，不得提交更改或创建拉取请求。

   现在只创建配置，供我审查。
   ```

## 检查配置

使用新智能体前，先审查其配置，确认 Copilot 捕获了预期的 QA 工作流和边界。这样可避免配置不完整，或在你只想验证功能时意外修改功能。

1. 输入 `/diff` 并打开 `.github/agents/qa.agent.md`。
2. 阅读 frontmatter。`description` 为必填项；`name` 是可选项，但添加后能显示更清晰的名称。
3. 阅读配置说明，确认 QA 会从需求出发、遵循存储库说明、运行 `quality-checks` 技能并使用 Playwright MCP。
4. 确认 QA 会提供证据、修改实现代码前会征求同意，并且不会提交更改或创建拉取请求。
5. 如果生成的配置遗漏了这些职责或边界，请让默认智能体先修改，再继续。

## 根据议题运行 QA

Copilot CLI 会在对话开始时加载项目智能体。创建配置后，请恢复同一个筛选对话，再选择 QA，这样它就能使用已有的议题和规划决策。

1. 使用以下提示启用智能体：

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 刚创建的智能体可能不会立即出现在可用智能体列表中。它已经存在，可以使用上述命令启用。

2. 使用以下提示，让 QA 智能体审查功能：

   ```plaintext
   根据议题和计划中的决策审查筛选功能。它是否已准备好创建 PR？
   ```

3. QA 智能体将开始工作。
4. 完成后，阅读它提供的报告。

## 总结与后续步骤

你已为工作流添加可复用的专家角色，并审查了它的工作。在本课中，你：

- 了解了自定义智能体如何与说明、技能和 MCP 工具协作。
- 创建并检查了可复用的质量保证配置。
- 选择了 QA 智能体，并根据筛选议题审查了它的发现。

现在，实现、技能更新、QA 配置、测试和验证报告都已准备好接受审查。接下来，[将这些内容整合到功能 PR 中，并使用 Agent Merge][next-lesson]。

## 资源

- [为 Copilot CLI 创建自定义智能体][create-agents]
- [自定义智能体配置][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration
