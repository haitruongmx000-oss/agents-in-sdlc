---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前两项练习中，你让 Copilot 云智能体处理了三项任务，重点是改善用户体验和添加功能。云智能体虽然能够异步自主运行，但监控这些任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个 mission control 中，你可以查看所有具有未关闭拉取请求 (PR) 的智能体任务、探索执行的操作，甚至引导进行中的会话。

在本课中，你将：

- 查看智能体页面，监控云智能体任务。
- 引导正在进行的会话，要求添加额外功能。

## 场景

将任务分配给智能体以创建高对比度模式后，团队意识到也应该添加浅色模式。由于已经在更新网站样式并添加切换功能，因此同时实现这项功能很合理。现在引导智能体，让它在添加高对比度模式的同时也实现浅色模式。

## 查看 Copilot 云智能体任务

现在查看分配给 Copilot 云智能体的所有任务的状态。

1. 打开 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主面板和左侧面板中的任务列表。应能看到你分配给 Copilot 的任务，包括：
   - 更新代码库文档。
   - 生成用于修改游戏的应用程序编程接口 (API)。
   - 为网站添加高对比度模式。
3. 选择一项正在运行的任务，查看 Copilot 已执行的工作，例如：
   - 从存储库中检出代码。
   - 为 Copilot 创建工作环境。
   - 设置模型上下文协议 (MCP) 服务器。
   - 执行完成分配任务所需的各种步骤。

> [!NOTE]
> 显示的具体步骤会根据 Copilot 的工作状态和采用的方法而变化。

4. 同时注意右侧显示的拉取请求面板。该面板可让你查看 PR 和已更改文件，进一步监控任务。

## 引导云智能体

了解当前正在运行的任务后，要求 Copilot 在实现高对比度模式时也添加浅色模式切换开关。

1. 选择与添加高对比度模式相关的会话。确切标题会根据 Copilot 使用的名称和当前工作状态而有所不同。

    ![mission control 中的无障碍会话](../../../_images/ex6-accessibility-session.png)

2. 观察会话几分钟，直到它显示设置已完成且工作已开始。你会看到类似以下内容的消息。
3. 在 **Steer active session while Copilot is working** 对话框中，输入以下提示：

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![智能体页面中突出显示“Steer active session while Copilot is working”对话框的云智能体任务截图](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 注意 Copilot 如何确认收到提示并将其纳入工作流程。

## 让 Copilot 继续工作

和之前一样，Copilot 会继续处理更新后的任务。它会在完成收到消息时正在执行的具体步骤后，将新请求纳入工作流程。

与之前一样，这可能需要几分钟。此时可以暂停一下，回顾目前学到和探索的内容。

## 总结和后续步骤

本课介绍了 Copilot 智能体页面，这是监控和引导 GitHub Copilot 云智能体任务的中心位置。借助此 mission control，可以跟踪所有活动和已完成的任务、审查正在执行的工作，甚至重定向进行中的任务以调整范围或提供额外指导。

你探索了以下内容：

- 查看 mission control 和智能体页面以监控云智能体任务。
- 重定向正在进行的会话，要求添加额外功能。

Copilot 正在完成无障碍功能，接下来将[迭代改进它创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
