---
title: "练习 1 - 自定义说明（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或者 Copilot 应该了解背景信息，就需要确保它能够获取这些上下文。可以通过多种方式向 Copilot 提供特定上下文，其中重要的一种方式是使用[说明文件][instruction-files]指导代码生成。

本练习将：

- 了解项目专属上下文、编码准则和文档标准如何通过存储库自定义说明及按路径限定范围的说明文件传递给 Copilot。
- 使用当前说明生成筛选功能的第一个数据切片（publisher helper）。
- 向 `.github/copilot-instructions.md` 添加新的全库标准。
- 再次运行相同提示，观察生成的代码如何采用新标准。
- 将说明更新和筛选功能切片提交到 `main`，供 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能与部分设定标准不完全一致。Copilot 具有概率性。本练习的目标是观察更新说明后行为变化的*趋势*，而不是逐字符匹配输出。

## 说明文件

### 场景

和优秀的开发团队一样，Tailspin Toys 也有一套开发实践准则和要求，包括：

- 数据层始终需要单元测试。
- UI 应使用深色模式，并具有现代感。
- 应以 TSDoc 文档注释的形式为代码添加文档。
- 每个文件顶部都应添加一段注释，说明该文件的作用。

通过使用说明文件，可以确保 Copilot 获得正确信息，按照上述实践完成任务。

### 自定义说明

自定义说明可为 Copilot 提供上下文和偏好，帮助它更好地理解编码风格和需求。这是一项强大的功能，可以引导 Copilot 提供更相关的建议和代码片段。你可以指定偏好的编码约定、库，甚至希望添加的注释类型。既可以为整个存储库创建说明，也可以针对特定文件类型创建说明，提供任务级上下文。

说明文件分为两类：

- `.github/copilot-instructions.md`：每次请求都会发送给 Copilot 的单一说明文件。它应包含项目级信息，也就是大多数聊天或 CLI 请求都适用的上下文，例如技术栈、项目概述、最佳实践和其他全局指导。
- `.github/instructions/*.instructions.md`：可针对特定任务或文件类型创建的说明文件。可为特定语言（如 TypeScript 或 Astro），或创建 UI 组件、新增单元测试等任务提供指导。

> [!NOTE]
> 在 IDE 中工作时，说明文件仅用于 Copilot Chat 的代码生成，不用于代码补全或下一次编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot cloud agent 在生成代码时都会使用存储库级说明文件以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> 此外，Copilot [还支持采用其他标准的说明文件][custom-instructions-support]，包括 `AGENTS.md` 和 `CLAUDE.md` 文件。

### 管理说明文件的最佳实践

关于如何创建说明文件的完整讨论超出了本工作坊范围。不过，示例项目中的内容展示了一种具有代表性的做法。大体而言：

- 将 `copilot-instructions.md` 中的说明聚焦于项目级指导，例如项目介绍、项目结构和全局编码标准。
- 使用 `*.instructions.md` 文件为特定文件类型（单元测试、Astro 组件、数据层）或特定任务提供具体说明。
- 使用自然语言，保持指导清晰，并提供代码应该和不应该采用的写法示例。

创建说明文件没有唯一正确的方法，就像使用 AI 也没有唯一正确的方法一样。通过不断尝试，可以找到最适合项目的做法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应该有一套完善的说明文件。查看本项目中的文件时，可能会发现有针对许多任务的说明，包括 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮助生成说明文件。不同界面对这一功能的展示方式不同（例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`）。当前工作环境的课程会在相关位置介绍。
>
> 想找模板或起点？可以看看 [awesome-copilot][awesome-copilot]，这里汇集了说明文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索本项目中的自定义说明文件

花点时间阅读存储库随附的说明文件，其中包含一个核心 `copilot-instructions.md` 和一组面向不同任务的 `*.instructions.md` 文件。可以在编辑器或 GitHub Web UI 中打开它们。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览该文件，注意对项目的简要介绍，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。在 **Code standards** 下，还要查看嵌套的 **GitHub Actions Workflows** 指导。这些内容适用于与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览其中的文件。这里有针对 Astro 文件、Drizzle 数据层、测试等内容的说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它设置了一个相对于存储库根目录的 glob，用于决定说明适用的文件。在这里，任何 TypeScript 测试文件（例如匹配 `**/*.test.ts` 的文件）都会匹配。
5. 查看其中针对本项目单元测试的具体说明。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到底部。注意其中指向其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的说明集拆分成更小、可复用的文件，并为 Copilot 指出生成代码时可参考的示例。（这些路径是相对于说明文件本身，而不是存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目的编码标准，但目前没有要求在代码中编写文档。接下来会添加 TSDoc 文档注释和文件注释头规则。

## 创建更改分支

按照最佳实践，先为更改创建一个分支。

1. 返回上一练习中使用的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新说明前，先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，启动一个干净的对话。模式和模型的选择因练习而异；每个步骤都会在相关位置说明。

为了观察自定义说明的影响，先在当前说明文件生效时发送提示。稍后更新说明文件后，再发送相同提示进行比较。

1. 关闭之前练习中打开的编辑器选项卡，这样 Copilot 只会获取所需上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道 helper 应放在哪个文件中。
3. 在 Chat 视图的智能体下拉列表中选择 **Agent**，让 Copilot 应用文件更改。

   ![Chat 视图中智能体选择器的截图](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   创建或更新 src/lib/publishers.ts，添加一个数据访问 helper，返回所有发行商及其名称和 ID。应用文件更改。
   ```

5. Copilot 会探索项目并应用代码更新，通常会同时修改 helper 文件及其测试。
6. 注意建议的 helper 是一个类型化函数，它将 `db` 客户端作为第一个参数，并返回类型化的发行商数组。这来自 `.github/instructions/drizzle.instructions.md`（适用于 `src/lib/*.ts`）中的数据层约定。
7. 注意建议的代码**缺少** TSDoc 文档注释和文件注释头。

> [!CAUTION]
> Copilot 具有概率性，因此它也可能在没有明确要求的情况下添加文档注释。如果发生这种情况也没关系；本练习的重点仍然是观察更新说明后行为的一致性变化。

## 添加新的存储库标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在添加编码标准，提高代码建议的质量。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分，大约位于第 27 行。注意其中记录了项目编码标准，但尚无代码文档规则，这正是生成的 helper 没有文档注释的原因。
3. 在现有标准下方添加以下 Markdown 内容，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - 每个导出的函数都应添加 TSDoc 注释，说明其用途、参数和返回值。
   - 在导入语句或任何代码之前，添加一个说明文件用途的注释块。
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所示，可以在存储库级别（`.github/copilot-instructions.md`）创建全局说明，也可以使用 `*.instructions.md` 文件为特定语言、文件类型或任务提供指导。存储库级文件适合放置项目级标准，例如刚刚添加的文档注释规则。

## 重新运行提示并观察变化

1. 返回 Copilot Chat 并选择 **New Chat**，清空上下文缓冲区。
2. 再次选择 `src/lib/publishers.ts`，让 Copilot 聚焦于正确的文件。
3. 发送与之前**相同的提示**：

   ```plaintext
   创建或更新 src/lib/publishers.ts，添加一个数据访问 helper，返回所有发行商及其名称和 ID。应用文件更改。
   ```

4. 注意新生成的文件现在会以类似以下内容的注释块开头：

   ```typescript
   /**
    * Tailspin Toys 众筹平台的发行商数据访问 helper。
    * 提供从数据库检索发行商信息的函数。
    */
   ```

5. 注意新生成的函数现在包含类似以下内容的 TSDoc 注释：

   ```typescript
   /**
    * 返回所有发行商及其 ID 和名称。
    *
    * @param db - Drizzle 数据库客户端。
    * @returns 一个 promise，解析为发行商对象数组。
    */
   ```

你刚刚引导 Copilot 遵循新的项目标准，并将其应用于真实代码。下一练习会在此基础上继续构建功能。

## 提交说明并推送分支

说明文件和存储库中的其他资源一样，需要通过相同的源代码管理流程进行管理。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
2. 在终端中运行以下命令，确认说明更新和 helper 更改已经就绪：

   ```bash
   git status
   ```

3. 在终端中暂存并提交说明更新，以及 Copilot 为 helper 基础实现修改的所有文件：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示还有其他相关更新（例如测试），也将这些文件添加到暂存区。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 在终端中将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送后，创建拉取请求，并将它与待办事项中的文档标准议题关联起来。你可以手动操作，也可以让 Copilot 使用已连接到项目的 GitHub 工具。让 Copilot 查找议题、创建可关闭该议题的 PR，然后合并 PR。

> [!NOTE]
> 这是第一次让 Copilot 代表你操作 GitHub。项目模板已经将 GitHub 工具连接到工作区，无需额外设置。Copilot 首次使用这些工具时，VS Code 可能会提示你登录 GitHub。按照提示完成身份验证。后续练习会介绍这种连接的工作方式（Model Context Protocol）。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确认在智能体选择器中选择了 **Agent**。
3. 让 Copilot 查找与更新说明文件相关的议题，并根据当前分支创建 PR，说明其中既有说明更新，也有新的 publishers helper 基础实现：

   ```
   查找与更新说明文件相关的议题。根据当前分支创建拉取请求，并注明该 PR 会关闭此议题，同时说明我们还为后续筛选工作添加了 publishers helper 基础实现。
   ```

4. Copilot 将开始查找议题并创建 PR。
5. 如果系统提示是否**允许** Copilot 代表你执行 GitHub 操作，请审查命令，并酌情选择 **Allow**。
6. PR 创建后，使用以下提示让 Copilot 合并 PR，并将本地分支切回 main：

   ```
   将 PR 合并到 main。然后在本地切回 main，并拉取最新代码以确保代码已更新。
   ```

7. 如果系统提示是否**允许** Copilot 执行 GitHub 操作和运行 shell 命令，请审查命令，并酌情选择 **Allow**。

现在，你已借助 GitHub Copilot 创建并合并了拉取请求！

## 总结与后续步骤

你了解了 Copilot 如何获取本项目说明文件中的上下文，并使用 VS Code 中的 Copilot Chat：

- 发送代码生成提示，并观察 Copilot 如何根据*现有*说明生成代码。
- 向 `.github/copilot-instructions.md` 添加新的全库标准。
- 再次发送相同提示，观察建议的代码如何采用新标准。
- 将说明更新和 helper 基础实现提交到 `main`，让下一练习能够继续使用。

接下来，在[智能体模式][next-lesson]中运用这些说明，让 Copilot 跨代码库添加新功能。

## 资源

- [用于自定义 GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更有效 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义说明][personal-instructions]
- [Awesome Copilot — 说明文件及其他资源合集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
