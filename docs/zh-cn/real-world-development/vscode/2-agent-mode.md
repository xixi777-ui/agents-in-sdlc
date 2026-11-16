---
title: "练习 2 - 使用 Copilot 智能体模式添加新功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也涉及多个文件以及运行测试等操作。作为开发人员，你通常需要找到所有相关文件、进行修改、运行测试、调试、找出遗漏的文件并再次更新……这样的工作还会继续下去。

这正是 Copilot 智能体模式的用武之地。

Copilot 智能体模式旨在让 Copilot 在 IDE 中自主工作。它的工作方式类似开发人员：先探索现有项目结构，再执行所需更新、运行测试等任务，并自动修复发现的问题。现在一起看看如何使用智能体模式为网站添加新功能。

> [!NOTE]
> 虽然名称相似，但智能体模式和 cloud agent 面向两种不同的体验。智能体模式在 IDE 中执行任务，支持快速反馈和交互。Cloud agent 则像团队成员一样异步工作，通过议题和拉取请求与你协作。

本练习将介绍：

- Copilot 智能体模式如何探索项目、识别相关文件并协同修改。
- GitHub Copilot 智能体模式如何跨 UI 和数据层实现新功能。
- 合并代码前如何审查 Copilot 智能体模式生成的更改和测试。

## 场景

随着游戏列表不断增加，你希望用户能够按类别和发行商筛选。上一练习已添加发行商 helper，现在将使用 Copilot 智能体模式完成剩余的数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

更改网站前，先浏览 Tailspin Toys，了解现有功能。

这是一个带有开发者主题的棋盘游戏众筹平台。用户可以浏览游戏列表和查看游戏详情。该网站是一个 Astro 应用，会在构建时将页面渲染为静态 HTML。页面通过 frontmatter 使用 Drizzle ORM 直接查询本地 SQLite 数据库，没有单独的后端 API 或客户端 UI 框架。可复用的数据访问 helper 位于 `src/lib/`，交互功能则通过小型、作用域明确的 Astro `<script>` 和标准 DOM API 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。按照以下步骤，可以在 GitHub Codespace 中运行该任务：

1. 返回 Codespace。上一练习结束后，你应该位于 `main` 分支。
2. 在 Codespace 中打开新终端，按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）。
3. 创建并切换到用于筛选功能开发的新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，应该会看到类似以下内容的横幅，其中包含 URL：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果系统提示是否在浏览器窗口中打开 `http://localhost:4321`，请选择 **x** 关闭提示框。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后在终端中选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 使用 Codespace 时，在终端中选择 localhost URL 会自动重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是指向 Codespace 的专用隧道，你的网站服务器现在就在其中运行！

### 浏览网站

网站运行后，可以浏览现有功能。主要功能包括：

- **主页：** 显示棋盘游戏列表，包括标题、图片和描述。
- **游戏详情页：** 选择游戏后会打开详情页，显示标题、描述、发行商和类别等详细信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，启动一个干净的对话。模式和模型的选择因练习而异；每个步骤都会在相关位置说明。

网站的初始实现已可正常运行，但我们希望添加新功能。先查看待办事项。通过模板设置项目时，系统会自动创建一组议题作为待办事项。让 GitHub Copilot 显示这些议题。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。**Agent** 智能体会自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中智能体选择器的截图](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本工作坊作者并非推荐某种模型。编写本工作坊时使用了 Claude Sonnet 4.5，因此步骤中包含该模型，希望获得相对一致的代码建议。但由于 LLM 具有概率性，你看到的建议可能与本工作坊中的示例不同。这是完全正常且符合预期的。

> [!NOTE]
> 由于 LLM 具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，询问待办事项中的议题：

   ```plaintext
   请显示我 GitHub 存储库中的待办事项，并根据对用户最有用的程度帮助我确定优先级。
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 查看生成的议题列表。

注意 Copilot 会根据它认为对用户最有用的程度为事项排序。

## 审查说明文件

让智能体生成代码前，先查看用于向 Copilot 提供上下文的说明文件。你将使用[用户界面（UI）](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)说明文件，其中介绍了为网站添加功能的方法。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 注意其中的总体指导，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍原则。
   - 指向各种文件类型专用说明的链接，包括：
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 说明文件既可以引用其他说明文件，也可以引用项目中的文件。路径是相对于说明文件所在位置的。这样可以重复使用内容、将复杂说明拆分成更容易管理的小块，并提供示例和模板。

## 实现筛选功能

完成筛选至少需要对应用进行三项独立更新：

- 在数据层（`src/lib/`）添加或完善剩余的筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页，添加筛选 UI。

此外，合并前必须运行测试并确保通过。Copilot 智能体模式可以完成这些任务！现在添加筛选功能。

1. 可以继续使用当前 Copilot 对话，也可以选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 说明文件。

   ![选择 UI 说明文件的示例截图](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉列表中仍然选择了 **Agent**。

   ![Chat 视图中智能体选择器的截图](../../../_images/shared-chat-mode-selector.png)

4. 确认仍选择 **Claude Sonnet 4.5** 模型。
5. 使用以下提示，让 Copilot 根据待办事项中的相关议题实现功能：

   ```plaintext
   请根据待办事项中相关 GitHub 议题的要求，更新网站以支持按发行商和类别筛选。上一练习已创建 publishers helper，请保留并根据需要完善该工作，同时完成剩余的数据层、UI 和测试。结束前确保所有测试通过。服务器已经在运行，无需重新启动。
   ```

6. 观察 Copilot 如何先探索项目并查找相关文件。你应该能看到它找到数据层 helper、UI 和测试，然后开始修改文件并运行测试。

   ![Copilot 探索项目文件的截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多个任务，例如探索项目、修改文件和运行测试。根据任务和代码库的复杂程度，这可能需要几分钟。在此过程中，代码编辑器中可能会出现 **Keep** 和 **Undo** 按钮。Copilot 完成后，你会看到所有更改的 **Keep** 或 **Undo** 按钮，因此无需在工作过程中提前选择。

7. 按 Copilot 的提示选择 **Continue** 以运行测试。

   ![Copilot Chat 窗格中询问用户是否允许运行测试的对话框截图](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会出现暂停，也可能有测试失败。这没有关系！Copilot 会在代码生成和测试之间反复迭代，直至任务完成且未发现错误。

   ![Copilot 智能体模式完整 Chat 会话的截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码是否存在潜在问题。

> [!CAUTION]
> 请记住，始终需要审查 Copilot 或其他 AI 工具生成的代码。

10. 返回运行网站的浏览器，探索新功能！
11. 确认功能正常且代码已审查后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结与后续步骤

恭喜！本练习中，我们使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加了新功能。你了解了：

- GitHub Copilot 智能体模式如何跨 UI 和数据层实现新功能。
- Copilot 智能体模式如何探索项目、识别相关文件并协同修改。
- 合并到代码库前，如何审查 Copilot 智能体模式生成的更改和测试。

接下来使用 [Playwright MCP 服务器测试功能][next-lesson]，并为其创建拉取请求。

### 扩展练习 — 实现分页

随着游戏列表越来越长，需要添加分页。运用本练习学到的技能，提示 Copilot 更新网站并实现分页。可以考虑：

- 遵循现有最佳实践，包括使用现有说明文件。
- 考虑分页方案，例如是否让用户选择每页大小，或将其设为固定值。
- 编写提示时，提供足够指导，让 Copilot 按照你的设想实现功能。
- 你可能需要与 GitHub Copilot 多次迭代，提出更改要求并提供上下文。这是使用 Copilot 的正常工作流程！

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
