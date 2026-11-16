---
title: "练习 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

几乎所有组织都会面临技术债问题，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 的 cloud agent 可以自主完成更新代码和添加功能等任务。任务完成后，它会创建一个草稿 PR，供开发人员审查。这样可以将繁琐任务交给智能体，加快开发流程，让开发人员专注于更重要的工作。

你将探索 Copilot cloud agent 的以下用法：

- 自定义代码生成环境。
- 确保操作安全。
- 了解明确界定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债需要处理。最初受雇开发网站的承包商没有留下完善的文档——事实上，你会发现项目完全缺少文档。团队希望首先为应用中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队准备改进游戏发现体验。他们希望每款游戏的详情页显示相关游戏，也就是属于同一类别的其他游戏，方便用户继续浏览。他们暂时不需要完善的实现，只需要一个可用于用户体验验收测试的版本。目前这项工作受阻，但其他议题的优先级更高。

这两项任务都可能被不断降低优先级，很适合分配给 Copilot cloud agent。它可以异步处理任务，让开发人员继续处理其他工作，之后再回来审查 Copilot 的工作并确认结果。

## 了解 GitHub Copilot cloud agent

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) 可以像人类开发人员一样在后台执行任务。与人类开发人员协作类似，你可以通过多种方式启动任务，包括[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求来跟踪进度、设置工作环境并开始处理任务。你可以在 Copilot 会话进行中或完成后查看详情。解决方案准备好供你审查时，它会在拉取请求中提及你！

## 明确界定任务范围的重要性

GitHub Copilot 没有魔法，不会让你跳过思考。即使看似简单的操作，深入分析后也会发现其复杂性，因此请[谨慎界定分配给 Copilot cloud agent 的任务范围][cloud-agent-best-practices]。把它当作 AI 结对程序员：分阶段工作、学习、试验并不断调整。引入生成式 AI 并不会改变软件开发的基本原则。

## 本存储库中的自定义说明

前面的练习介绍了自定义说明及其如何指导 Copilot。如果你已完成自定义说明的实践练习，就已经见过 `.github/copilot-instructions.md` 的效果；如果还没看过，现在正好简单浏览一下。在分配任务给 Copilot cloud agent 前，先只读查看本存储库中的说明文件，之后就能观察它们对工作结果的影响。

在 GitHub Web UI 或已启动的 Codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：注意 `applyTo` frontmatter，它会将这些说明限定在 `**/*.test.ts` 文件中。

下一节将 `Code lacks documentation` 议题分配给 cloud agent 时，注意查看生成的拉取请求中是否包含 TSDoc 文档注释、TypeScript 约定和文件注释头。这些内容来自刚才查看的说明文件。后续练习审查 PR 时还会再次提到这一点。

## 为 Copilot cloud agent 设置开发环境

无论由谁编写代码，通常都需要特定环境并运行一些设置脚本，以确保环境正常。将任务分配给 Copilot 时也是如此，它会像软件工程师一样完成工作。

Cloud agent 使用 [GitHub Actions][github-actions] 作为工作环境。你可以通过在 `.github/workflows/copilot-setup-steps.yml` 文件中创建[专用设置工作流][setup-workflow]来自定义环境，让它在开始工作前运行，以便访问所需的开发工具和依赖项。为简化实验流程并提供学习机会，我们已预先配置好该工作流。它会确保 Copilot 可以使用 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及已迁移和填充数据的单个 Astro 应用 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 可在存储库的“Actions”选项卡中测试这些设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 将权限限制为步骤所需的最低级别。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 例如，如果设置步骤需要克隆存储库来安装依赖项，则需要 `contents: read` 权限。如果设置步骤不克隆存储库，Copilot 会在步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（应用现为 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，确保构建和测试有可用数据。
      - name: Set up the database
        run: npm run db:setup
```

这看起来和其他 GitHub 工作流文件类似，但有几个关键点：

- 其中只包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会先执行此作业。
- 注意 `workflow_dispatch` 触发器，它允许你在存储库的 **Actions** 选项卡中手动运行工作流。这有助于验证工作流是否成功，而不必等待 Copilot 启动它。

## 添加文档

大家都知道文档的重要性，但大多数项目的信息已经过时或根本没有文档。这类技术债常常得不到处理，会降低效率，也让维护代码库或引入新团队成员变得更加困难。Copilot 擅长生成文档，因此很适合将这类议题分配给 Copilot cloud agent。它会在后台生成所需文档。后续练习中会回来审查结果。

1. 在新浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 创建新议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

    ```plaintext
    我们组织要求在有助于理解代码时，为函数和方法添加 TSDoc 文档注释。但近期更新没有遵循此标准。我们需要更新现有代码，在能帮助解释行为的地方添加文档注释。
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

  ![Copilot 分配详情](../../../_images/ex4-assign-copilot-details.png)

10. 选择 **Pull Requests** 选项卡。
11. 打开刚创建的拉取请求（PR），其标题类似 `[WIP]: Code lacks documentation`。如果列表中没有新 PR，请稍等片刻并刷新浏览器窗口。
12. 几分钟后，Copilot 应该会创建一个待办事项列表。

> [!NOTE]
> Copilot 的待办事项列表可能需要几分钟才能显示在 PR 中。Copilot 正在创建工作环境（运行前面介绍的工作流）、分析项目并确定最佳方案。

13. 查看待办事项列表以及 Copilot 准备完成的任务。
14. 向下滚动 PR 时间线，应该可以看到 Copilot 已开始处理该议题。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到状态指示器更新。

16. 浏览实时会话，了解 Copilot 如何解决问题，包括探索代码并了解当前状态、暂停思考并确定合适的计划，以及编写代码。

这可能需要几分钟。Copilot cloud agent 的主要目标之一是异步执行任务，让我们可以专注于其他工作。现在就利用这一点：先给 Copilot cloud agent 分配另一项任务，然后我们转而编写应用代码、添加功能。

## 在游戏详情页添加相关游戏部分

如前所述，GitHub Copilot cloud agent 的一大优势是可以拆分工作，让你处理一组任务，而它同时处理另一组。即使为设计团队添加相关游戏部分不一定需要很长时间，也可以把时间留给其他工作。现在将这项任务分配给 Copilot cloud agent！

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

    ```markdown
    我们希望在每款游戏的详情页显示相关游戏，帮助用户发现更多游戏。设计团队希望探索用户体验并进行验收测试。具体需求如下：

   - 在 `src/lib/` 中添加一个数据访问 helper，根据给定游戏返回同类别的其他游戏，但不包括该游戏本身
   - 在游戏详情页添加一个使用此 helper 的“相关游戏”部分
   - 处理游戏没有相关游戏的情况
   - 为新 helper 编写单元测试
   - 创建 PR 前，确保所有测试通过
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

很快就会看到第一个议题的评论旁出现 👀，表示 Copilot 已开始处理任务！

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在努力完成第二项任务！Copilot cloud agent 的工作方式与软件工程师类似，不需要一直监控它；完成后再回来审查即可。现在可以开始创建和使用自定义智能体。

## 总结与后续步骤

本课介绍了 [GitHub Copilot cloud agent][copilot-agents]。你可以将议题分配给 cloud agent，让它异步处理。可以让 Copilot 处理技术债、创建新功能，或协助将代码从一个框架迁移到另一个框架。

你了解了：

- 自定义代码生成环境。
- 确保操作安全。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

Copilot cloud agent 正在后台工作。接下来开始创建和使用自定义智能体。[Copilot cloud agent 还可以使用 MCP 服务器][cloud-agent-mcp]，并可使用自定义说明；前面的模块已经介绍过相关内容。

## 资源

- [关于 Copilot cloud agent][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot cloud agent 设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
