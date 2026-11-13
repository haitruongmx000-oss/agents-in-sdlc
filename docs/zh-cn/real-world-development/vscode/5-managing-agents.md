---
title: "练习 5 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义智能体][previous-lesson] |
|:--|

使用 GitHub Copilot 智能体模式时，它会自主探索代码库、提出更改、编辑文件并运行命令。由于这些工作实时发生在你的计算机上，VS Code 中的 Copilot Chat 会持续显示每次工具调用和文件编辑。你可以直接在聊天中审查每个差异、接受或拒绝单项更改，并通过后续提示引导对话，以改进或扩展工作，而无需离开编辑器。

在本练习中，你将：

- 实时监控无障碍智能体的工作。
- 审查并接受建议的文件编辑。
- 在会话进行期间引导智能体，添加浅色模式切换开关，同时保留高对比度模式。

## 场景

你在[练习 4][previous-lesson]中使用的无障碍自定义智能体正在实现高对比度模式。工作进行期间，设计团队提出了额外需求：为觉得默认主题太暗的用户添加浅色模式切换开关。与其从头开始新对话，不如引导现有会话扩展工作范围。

## 监控 Copilot 的进度

Copilot Chat 视图会显示智能体执行的每个步骤，包括探索的文件、运行的命令和提出的编辑。每项文件编辑都会以内嵌差异的形式显示，并提供 **Keep** 和 **Undo** 控件。

1. 返回练习 4 中打开的 Copilot Chat 视图。
2. 智能体工作时，滚动聊天记录，查看它执行的每次工具调用。你会看到文件读取、文件编辑以及它请求运行的终端命令。
3. 智能体提出文件编辑时，差异会以内嵌形式显示：
   - 选择 **Keep** 接受更改。
   - 选择 **Undo** 撤销更改，让智能体重新尝试。
4. 也可以打开聊天顶部的 **Files changed** 指示器，查看智能体在本次会话中修改的完整工作集。

> [!NOTE]
> 如果工具调用请求权限（例如运行终端命令），智能体会暂停并显示 **Allow** 提示。仅批准你认可的命令。

5. 等待智能体完成高对比度模式的实现。完成后应会显示总结所做更改的最终消息。

## 引导智能体添加浅色模式

高对比度模式实现后，你将继续在同一对话中添加浅色模式切换开关。由于继续使用同一聊天会话，智能体会保留先前工作的上下文，包括修改的组件、使用的样式方案和设置的本地存储模式。

1. 在 Copilot Chat 输入框中发送以下后续提示：

    ```plaintext
    Nice work! Now add a light-mode toggle alongside the high-contrast toggle. Light mode should follow the same pattern — persist via local storage and be accessible from the same area of the UI. The user should be able to enable high-contrast and light mode independently.
    ```

2. 智能体会从先前工作继续，探索已有更改并提出浅色模式的新增内容。
3. 像审查高对比度模式更改那样，逐项审查新编辑。使用每个差异上的 **Keep** 或 **Undo**。
4. 如果智能体的方向不符合你的预期，例如尝试以不喜欢的方式重构切换用户界面 (UI)，可以选择聊天输入框旁的 **Stop** 按钮停止任务，然后发送澄清后的后续消息。

> [!TIP]
> 引导进行中的会话比从头开始更快。可以使用后续提示要求调整（“简化 CSS，改用 CSS 变量而不是重复选择器”）、纠正方向（“不要添加第三个开关，把浅色模式集成到现有设置面板”），或扩展范围（“还要为两个开关添加键盘快捷键”）。

## 审查工作集

提交更改前，快速检查智能体修改过的所有内容。

1. 在 VS Code 中打开 **Source Control** 视图。
2. 查看全部已更改文件。你应会看到 Astro 组件、样式和相关测试的更新。
3. 打开几个已更改文件并查看差异。确认其中采用了自定义智能体要求的无障碍模式，包括无障碍富互联网应用 (ARIA) 属性、键盘导航、语义化超文本标记语言 (HTML) 和本地存储持久化。
4. 确认满意后，在 Source Control 面板中暂存并提交更改。下一课会发布这些更改。

## 总结和后续步骤

你使用 Copilot Chat 智能体模式监控并引导进行中的会话：逐项审查文件编辑、接受或拒绝更改，并通过后续提示扩展工作，而不丢失上下文。

在本课中，你探索了以下内容：

- 实时监控智能体的工作。
- 接受或拒绝单项文件编辑。
- 在会话进行期间使用后续提示引导智能体。

在[下一课][next-lesson]中，你将把无障碍工作发布为拉取请求，并审查本地自定义智能体在本实验中生成的更改。

## 资源

- [VS Code 中的 Copilot Chat][copilot-chat-vscode]
- [使用智能体模式][agent-mode]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
