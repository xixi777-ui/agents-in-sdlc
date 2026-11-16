---
title: "第 4 课 - 使用自定义说明指导 Copilot"
description: "了解存储库说明和按路径限定范围的说明文件，添加文档标准，并将其应用于筛选代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，就应确保 Copilot 能获取相应指导。[说明文件][instruction-files]不仅描述想要*什么*代码，也会说明代码应当*如何*组织。现在你已经实现了筛选功能，接下来将了解 Copilot 使用的说明、添加文档标准，并将其应用于代码。

本课将：

- 了解存储库说明和按路径限定范围的说明文件如何传递给智能体。
- 更新说明文件，确保遵循编码标准。
- 观察说明文件对代码的影响。

## 场景

和优秀的开发团队一样，Tailspin Toys 也有一套开发实践准则和要求，包括：

- 注释应解释意图和不明显的决策，而不是复述代码。
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 说明其用途、参数和返回值（如有可注入的 `db` 参数，也要说明）。
- 可复用的 Astro 组件应说明其 `Props` 接口；相关代码更改时，注释也应保持最新。
- 保留现有格式和 lint 指导。

借助说明文件，可以确保 Copilot 获得正确信息，按照上述实践完成任务。

## 说明文件

自定义说明可为 Copilot 提供上下文和偏好，帮助它更好地理解编码风格和需求。这是一项强大的功能，可以引导 Copilot 提供更相关的建议和代码片段。你可以指定偏好的编码约定、库，甚至希望添加的注释类型。既可以为整个存储库创建说明，也可以针对特定文件类型创建说明，提供任务级上下文。

说明文件分为两类：

- `.github/copilot-instructions.md`：每次请求都会发送给 Copilot 的全库说明文件。它应包含与大多数请求相关的项目级信息。
- `.github/instructions/*.instructions.md`：针对特定语言、文件类型或任务提供指导的说明文件。

> [!NOTE]
> 不同环境支持的其他说明格式可能有所不同。依赖特定格式前，请查阅[自定义说明支持参考][custom-instructions-support]。

## 探索本项目中的自定义说明文件

入门项目已经包含了一些说明文件。先了解现有内容，再进行更改，观察其影响。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而非终端）中打开 `.github/copilot-instructions.md`。
3. 浏览文件，注意其中简要的项目说明和编码指导。这些说明适用于 Copilot 在此存储库中的每次交互。
4. 打开 `.github/instructions` 文件夹并浏览其中的文件。你会看到针对 Astro 文件、Drizzle 数据层、测试等内容的说明。
5. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它设置了一个 glob，用于决定说明适用的文件。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看它指向的其他说明文件和现有项目文件链接。这样可以将较大的说明集拆分成较小、可复用的文件，并提供可供 Copilot 参考的示例。

## 更新说明文件以符合团队准则

现有文件是不错的起点，但仍缺少一项标准。我们要修改核心 `copilot-instructions.md` 文件，确保新生成的 TypeScript 代码包含 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分，大约位于第 35 行。
2. 将以下内容添加为该部分的最后一项：

   ```markdown
   - 所有新编写的 TypeScript 代码都应包含 TSDoc 注释。
   ```

文件会自动保存！

## 使用更新后的指导

Copilot CLI 会在对话开始时加载存储库说明。保存更新后的说明后，请退出并恢复筛选对话，这样 Copilot 就能在保留功能上下文的同时加载新指导。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 关闭。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支上，恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果系统提示，请选择筛选对话，并确认当前检出的是 `game-filters-cli` 分支。
3. 让 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并查看更改过的 TypeScript 文件。注意新生成的 TSDoc 注释，并确认其准确说明了代码。

## 总结与后续步骤

你了解了 Copilot CLI 如何从说明文件获取上下文，并将新标准应用于功能。具体来说，你：

- 了解了存储库说明和按路径限定范围的说明文件如何传递给智能体。
- 更新了说明文件，确保遵循编码标准。
- 观察了说明文件对代码的影响。

接下来，[自定义并运行可复用的质量检查技能][next-lesson]，以确保始终运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义说明][instruction-files]
- [自定义说明支持][custom-instructions-support]
- [创建有效自定义说明的最佳实践][instructions-best-practices]
- [Awesome Copilot — 说明文件及其他资源合集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
