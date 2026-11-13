---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

几乎所有组织都面临技术债务问题，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。完成工作后，它会创建草稿拉取请求，供开发者审查。这样可以将繁琐任务交给智能体，加快开发进度，让开发者专注于更重要的工作。

你将在本练习中探索以下内容：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些技术债务需要处理。最初受聘开发网站的承包商没有做好文档工作，整个项目几乎没有文档。团队希望首先为应用程序中所有导出的函数添加 TypeScript 文档注释 (TSDoc)。

此外，设计团队希望改进游戏发现功能。他们想在每款游戏的详细信息页面上显示相关游戏，也就是属于同一类别的其他游戏。目前不需要精致的实现，只需要可供团队进行用户体验验收测试的版本。此项工作目前受阻，不过还有优先级更高的议题。

这两项任务都可能很快被降低优先级，因此很适合分配给 Copilot 云智能体。云智能体可以异步处理任务，让开发者继续处理其他工作，之后再回来审查 Copilot 的成果并确认一切符合预期。

## 介绍 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发者一样在后台执行任务。就像与人类开发者协作一样，可以通过多种方式委派工作，包括[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配任务后，Copilot 会创建草稿拉取请求来跟踪进度、设置环境并开始处理任务。任务进行期间或完成后，你都可以查看 Copilot 会话。提出的解决方案准备好供你审查时，Copilot 会在拉取请求中提及你。

## 明确界定说明范围的重要性

GitHub Copilot 并非魔法工具，使用它并不意味着可以跳过思考。看似简单的操作深入分析后也会变得复杂，因此[请谨慎界定分配给 Copilot 云智能体的任务范围][cloud-agent-best-practices]。将它视为人工智能 (AI) 结对编程助手：分阶段工作、不断学习、试验和调整。引入生成式人工智能并不会改变软件开发的基本原则。

## 本存储库中的自定义说明

前面的练习介绍了自定义说明及其如何引导 Copilot。如果你已完成自定义说明的实践练习，就会看到 `.github/copilot-instructions.md` 的实际效果；否则，现在可以快速查看。将工作分配给 Copilot 云智能体前，先只读查看存储库中已有的说明文件，以便稍后观察它们的作用。

在 GitHub Web 界面中打开以下文件；如果已有正在运行的 codespace，也可以在那里打开：

1. 打开 `.github/copilot-instructions.md`，查看 **Code standards** 部分，重点关注 TypeScript 约定和 TypeScript 文档注释 (TSDoc) 要求。
2. 打开 `.github/instructions/unit-tests.instructions.md`。注意其 `applyTo` frontmatter，它将这些说明限定用于 `**/*.test.ts` 文件。

下一部分将 `Code lacks documentation` 议题分配给云智能体时，请留意生成的拉取请求 (PR) 是否包含 TypeScript 文档注释 (TSDoc)、TypeScript 约定和文件注释头。这些要求来自上述说明文件，之后的练习审查 PR 时还会再次提到。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要特定环境并运行一些设置脚本，以确保环境正常。将任务分配给 Copilot 时也是如此，因为它的工作方式类似软件工程师 (SWE)。

云智能体在 GitHub Actions 中运行。你可以通过创建[专用设置工作流][setup-workflow]，在智能体开始工作前自定义环境。该工作流配置在 `.github/workflows/copilot-setup-steps.yml` 文件中，确保智能体能够使用所需的开发工具和依赖项。为简化实验流程并提供学习机会，项目已预先配置此工作流。它会为单个 Astro 应用准备 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及已迁移和填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
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

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

此文件与其他 GitHub 工作流文件类似，但有几个关键点：

- 它包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，该作业会在 GitHub Actions 中执行。
- 注意 `workflow_dispatch` 触发器，它允许你从存储库的 **Actions** 选项卡手动运行工作流。这便于测试工作流是否成功，而不必等待 Copilot 触发它。

## 添加文档

虽然人人都知道文档很重要，但多数项目中的信息不是过时，就是完全缺失。这类技术债务往往长期得不到处理，降低工作效率，也增加维护代码库或帮助新开发者加入团队的难度。Copilot 擅长生成文档，因此很适合将此议题分配给 Copilot 云智能体。它会在后台生成所需文档，之后的练习会审查其成果。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue**，打开新议题对话框。
4. 选择 **Blank issue** 创建空白议题。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新生成的拉取请求 (PR)，其标题应类似于 `[WIP]: Code lacks documentation`。如果列表中没有出现新 PR，请稍等片刻并刷新浏览器窗口。
12. 几分钟后，Copilot 应会创建待办列表。

> [!NOTE]
> Copilot 可能需要几分钟才能在 PR 中显示待办列表。它正在创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的最佳方法。

13. 查看待办列表及其计划完成的任务。
14. 向下滚动拉取请求时间线，应能看到 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态指示器。

16. 浏览实时会话，观察 Copilot 如何解决问题，包括探索代码并了解当前状态、暂停思考并决定合适的计划，以及编写代码。

这可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让我们能专注于其他工作。接下来会利用这一点：先再分配一项任务给 Copilot 云智能体，然后着手编写代码，为应用添加功能。

## 在游戏详细信息页添加相关游戏部分

如前所述，GitHub Copilot 云智能体的一大优势是可以拆分工作：你专注于一组任务时，它可以处理另一组。为设计团队添加相关游戏部分可能不会花太长时间，但仍可以把这段时间用于其他工作。现在将这项工作分配给 Copilot 云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue**，打开新议题对话框。
4. 选择 **Blank issue**，使用空白模板。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，议题的第一条评论中会出现一组 👀，表示 Copilot 已开始处理任务。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 现在正在认真处理第二个请求。Copilot 云智能体的工作方式类似软件工程师，因此无需一直主动监控；等它完成后再审查即可。现在转而创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。可以将议题分配给云智能体，让它异步处理任务。Copilot 可用于处理技术债务、创建新功能，或协助将代码从一种框架迁移到另一种框架。

你探索了以下内容：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体正在后台处理任务，现在可以转而创建和使用自定义智能体。[Copilot 云智能体也可以使用模型上下文协议 (MCP) 服务器][cloud-agent-mcp]，并可使用前面模块介绍的自定义说明。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

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
