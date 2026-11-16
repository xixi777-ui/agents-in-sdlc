---
title: "第 2 课 - 添加星级评分：快速改进"
description: "使用 Copilot CLI 修改游戏卡片，在转发的浏览器中审查，并通过第一个拉取请求合并更改。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

安装 Copilot CLI 并尝试过一次对话后，现在可以首次修改项目了。先从小处着手：游戏数据中已经包含星级评分，但主页的游戏卡片尚未显示它。你将让智能体展示评分、审查更改，并通过第一个拉取请求将其合并。

本课将：

- 在功能分支上启动一个聚焦的 Copilot 对话。
- 让智能体对项目进行小幅修改。
- 使用 `/diff` 审查更改。
- 运行应用，并在转发的浏览器中确认更改。
- 创建并合并第一个拉取请求。

## 场景

Tailspin Toys 中的每款游戏都有星级评分，该评分已显示在游戏详情页中。但主页的游戏卡片只显示标题、类别、发行商和描述。作为热身，你将让智能体在每张卡片上显示现有评分。这是一个小而独立的修改，非常适合首次使用。

## 对话的组成

**对话**是你使用 Copilot CLI 执行任务的地方。与 Copilot app 不同，普通的 CLI 对话使用终端当前检出的存储库和 Git 分支，而不是创建专用工作树。保存的对话可以让你稍后回到同一讨论；文件和分支仍是磁盘上的普通 Git 状态。

对话中有三类内容：你发出的提示和智能体的回复、智能体探索和编辑文件时执行的工具操作，以及可通过 `/diff` 检查的更改。

## 开始对话并提出修改要求

启动一个新对话，开始实现功能。

1. 返回 Codespace。
2. 如果终端尚未打开，按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）。
3. 如果 Copilot 尚未启动，运行以下命令：

   ```bash
   copilot --yolo
   ```

4. 使用斜杠命令 `/new` 并按 <kbd>Enter</kbd>，确保启动新会话。
5. 使用以下提示提出修改要求：

   ```plaintext
   在列表页的游戏卡片上显示每款游戏的 starRating（满分 5 分）。如果评分为 null，则显示“No rating yet”。保持卡片布局不变，添加测试并运行相关检查。
   ```

Copilot 会探索项目，找到显示游戏详情所用的文件，并创建所需代码。现在，你已通过 Copilot CLI 添加了一项新功能！

## 审查差异

所有 AI 生成的更改都应在合并前审查，即使是小改动也不例外。现在就在 Copilot CLI 中检查更改。

1. 输入 `/diff` 并检查所有更改的文件。
2. 确认游戏卡片会在评分存在时显示数值，在 `starRating` 为 `null` 时显示 `No rating yet`。
3. 确认测试覆盖了这两种状态。
4. 查看 Copilot 运行的检查结果，并要求它修复任何失败项。
5. 审查完成后，按 <kbd>Esc</kbd> 退出差异界面。

> [!NOTE]
> Copilot 和其他生成式 AI 工具一样具有概率性，而不是确定性的，因此实际代码可能会有所不同。应审查行为，而不是期待某种特定实现。

## 检查更改

当然，不能只阅读代码就假定一切正常。让 Copilot 启动网站，以便在 Codespaces 转发的浏览器中检查更新后的用户界面（UI）。

1. 让 Copilot 启动应用：

   ```plaintext
   启动应用，以便我在浏览器中检查星级评分更改。告诉我 URL，并让服务器继续运行。
   ```

2. Codespaces 报告端口 `4321` 可用时，选择 **Open in Browser**。
3. 确认游戏卡片显示了满分为 5 分的评分。
4. 返回 Copilot，并让它停止启动的服务器：

   ```plaintext
   停止你启动的开发服务器。
   ```

## 创建并合并第一个拉取请求

功能已完成！现在创建拉取请求（PR），将新代码合并到项目中。

1. 让默认智能体提交更改：

   ```plaintext
   使用合适的提交消息提交已审查的星级评分更改。
   ```

2. 输入 `/pr create`。Copilot CLI 创建 PR 时会推送现有提交，并显示 PR URL。
3. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后选择 Copilot CLI 显示的 URL，以打开 PR。
4. 审查更改的文件和检查结果。
5. 准备就绪后，选择 **Merge pull request**，然后确认合并。
6. 返回 Codespace，并使用 `/exit` 退出 Copilot CLI。
7. 更新本地 `main`：

   ```bash
   git checkout main
   git pull
   ```

## 总结与后续步骤

恭喜！你已使用 GitHub Copilot CLI 交付了第一个更改！具体来说，你：

- 在功能分支上启动了一个聚焦的 Copilot 对话。
- 指示智能体修改游戏卡片。
- 使用 `/diff` 审查了更改。
- 运行应用，并在转发的浏览器中确认星级评分。
- 创建并合并了第一个拉取请求。

接下来，[从筛选议题开始，使用 Plan 和 Autopilot 模式][next-lesson]构建更大的功能。

## 资源

- [关于 GitHub Copilot CLI][about-copilot-cli]
- [Copilot CLI 命令参考][cli-reference]

[previous-lesson]: ../1-install-copilot-cli/
[next-lesson]: ../3-agent-modes/
[about-copilot-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
