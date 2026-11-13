---
title: "第 9 课 - GitHub Copilot CLI 斜杠命令"
description: "探索用于管理上下文、选择模型、共享会话和可选委派给云智能体的斜杠命令。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

与其他优秀的 CLI 工具一样，GitHub Copilot CLI 提供许多可用于交互的斜杠命令。这些命令提供高级功能、幕后信息和额外配置选项。你已使用过 `/diff`、`/mcp`、`/skills`、`/agent` 和 `/pr` 等命令。现在探索其他一些实用命令。

在本课中，你将：

- 使用 `/context` 和 `/compact` 查看 Copilot 如何管理对话上下文。
- 使用 `/model` 探索可用模型。
- 了解 `/share` 如何导出或共享会话。
- 探索用于并行工作、工作树和委派给云智能体的可选命令。

## 场景

核心 CLI 工作流已完成。现在看看其他功能，例如管理上下文、切换模型、共享会话，以及选择是否将任务委派给 [Copilot 云智能体][about-cloud-agent]。

## 探索 Copilot CLI 上下文

处理较大或较复杂的任务时，可能会遇到模型上下文窗口的容量上限。Copilot CLI 会在需要时自动压缩对话，你也可以使用斜杠命令检查或压缩上下文。

1. 返回 Codespace；如果 Copilot CLI 尚未打开，请从存储库根目录启动。
2. 输入：

   ```plaintext
   /context
   ```

3. 注意模型、当前 token 用量，以及上下文在系统说明、工具、消息和可用空间之间的分布。
4. 压缩对话：

   ```plaintext
   /compact
   ```

5. 再次输入 `/context` 并比较结果。如果对话原本就很短，变化可能并不明显。

> [!NOTE]
> 当上下文窗口接近容量上限时，Copilot CLI 会自动压缩上下文。想自行决定压缩时机时，可以使用 `/compact`。切换到无关任务并希望开始全新对话时，可以使用 `/clear` 或 `/new`。

## 选择模型

不同模型各有优势，开发者的偏好也各不相同。Copilot CLI 可以列出并选择要使用的模型。

1. 输入：

   ```plaintext
   /model
   ```

2. 浏览可用模型及其用量信息。
3. 保留当前模型、选择其他模型，或按 <kbd>Esc</kbd> 关闭列表。

## 共享会话

团队合作并分享经验有助于所有人更好地使用人工智能 (AI) 工具。`/share` 命令可以将会话导出为 Markdown 或超文本标记语言 (HTML) 文件、创建可共享链接，或发布 GitHub gist。

1. 输入 `/help`，查看你所用版本中的 `/share` 选项。
2. 如果要共享此会话，请选择适合的目标，例如 `/share file`，将会话导出为本地 Markdown 文件。
3. 在发送给其他人或发布之前，审查导出内容。会话导出可能包含提示、回复和项目详细信息。

发布链接或 gist 是可选的。不要发布团队无意共享的存储库或对话内容。

## 可选：扩展并行工作或委派任务

核心研讨会已完成。Copilot CLI 还提供适用于较大任务的命令：

- `/fleet` 可将相互独立的子任务分配给多个子智能体并行执行。
- `/worktree` 可为单独任务创建隔离的 Git 工作树。
- `/delegate` 可将任务发送给 Copilot 云智能体，由其异步处理并可能创建拉取请求。

这些命令是可选的，因为它们可能创建额外工作树或触发远程工作。尝试前，请开始一项范围明确的新任务，并通过常规工作流审查结果。想深入了解异步智能体工作，可继续学习[云智能体研讨会][cloud-workshop]。

## 总结和后续步骤

使用 Copilot CLI 斜杠命令可以对其进行配置、共享会话并查看幕后运行情况。本课中，你：

- 使用 `/context` 和 `/compact` 查看 Copilot 如何管理对话上下文。
- 使用 `/model` 探索可用模型。
- 了解了 `/share` 如何导出或共享会话。
- 探索了用于并行工作、工作树和委派给云智能体的可选命令。

Copilot CLI 还有更多斜杠命令和功能可供探索。现在，[回顾已学内容][next-lesson]并了解后续学习方式。

## 资源

- [Copilot CLI 命令参考][cli-reference]
- [Copilot CLI 中的上下文管理][context-management]
- [关于 Copilot 云智能体][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
