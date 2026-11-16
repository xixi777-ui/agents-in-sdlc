---
title: "练习 1 - 自定义说明（Cloud agent）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot cloud agent →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或者 Copilot 应该了解背景信息，就需要确保它能够获取这些上下文。[说明文件][instruction-files]可为 Copilot 提供指导，让它不仅理解要做*什么*，也理解要*如何*完成。

本练习将：

- 了解项目专属上下文、编码准则和文档标准如何通过存储库自定义说明及按路径限定范围的说明文件传递给 Copilot。
- 向 `.github/copilot-instructions.md` 添加新的全库标准。

> [!NOTE]
> 与 VS Code 和 CLI 工作环境不同，这里不会运行更新前后对比提示。Copilot cloud agent 会异步处理 GitHub 议题，因此难以实时并排展示效果。稍后审查 cloud agent 创建的拉取请求时，可以看到说明文件的影响。

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
2. 浏览该文件，注意对项目的简要介绍，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。还要查看 **Code standards** 下的嵌套 **GitHub Actions Workflows** 指导。这些内容适用于与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览其中的文件。这里有针对 Astro 文件、Drizzle 数据层、测试等内容的说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它设置了一个相对于存储库根目录的 glob，用于决定说明适用的文件。在这里，任何 TypeScript 测试文件（例如匹配 `**/*.test.ts` 的文件）都会匹配。
5. 查看其中针对本项目单元测试的具体说明。
6. 最后，打开 `.github/instructions/drizzle.instructions.md` 并滚动到底部。注意其中指向其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的说明集拆分成更小、可复用的文件，并为 Copilot 指出生成代码时可参考的示例。（这些路径是相对于说明文件本身，而不是存储库根目录。）

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目的编码标准，但目前没有要求在代码中编写文档。接下来会添加 TSDoc 文档注释和文件注释头规则。

## 添加新的存储库标准

接下来要做的唯一编辑，是添加一条项目级规则，要求以 TSDoc 文档注释和文件注释头的形式在代码中提供文档。之后，本工作环境中分配的议题会由 cloud agent 处理，它将采用这条新规则。

编辑前，先创建工作分支（cloud agent 会读取议题目标分支中的说明，但我们仍会在独立分支上提交更改）：

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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

## 提交、推送并合并说明更新

Cloud agent 会从议题目标分支读取说明。下一练习中将把议题分配给 Copilot，它会从 `main` 分支创建新分支，因此必须先将说明更改合并到 `main`，cloud agent 才能使用。

1. 暂存并提交：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并将其合并。之后 cloud agent 处理下一练习中的议题时，就会读取这些说明。

> [!TIP]
> 如果想在本工作坊中直接使用 `main`，可以跳过分支步骤并直接提交到 `main`。此处创建分支是为了模拟真实项目中的工作方式。

## 总结与后续步骤

你了解了 Copilot 如何获取本项目说明文件中的上下文，并向 `.github/copilot-instructions.md` 添加了全库标准。稍后可以在 cloud agent 创建的拉取请求中看到该标准的效果。

接下来，[将第一个议题分配给 Copilot cloud agent][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更有效 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot — 说明文件及其他资源合集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
