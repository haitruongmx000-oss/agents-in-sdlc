---
title: "Exercício 4 - Monitorar e gerenciar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Nos últimos exercícios, você atribuiu ao Copilot cloud agent três tarefas para melhorar a experiência e adicionar funcionalidades. Embora o agente na nuvem trabalhe de forma assíncrona e autônoma, é importante acompanhar essas tarefas.

Você tem várias ferramentas para gerenciar tarefas atribuídas ao agente na nuvem, incluindo a [página de agentes][agents-page] no GitHub.com. Nesse painel central, você pode ver as tarefas de agentes que têm pull requests (PRs) abertos, explorar as operações realizadas e orientar uma sessão em andamento.

Nesta lição, você vai:

- explorar a página de agentes para acompanhar as tarefas do agente na nuvem.
- orientar uma sessão em andamento para solicitar funcionalidade adicional.

## Cenário

Depois de atribuir ao agente a criação de um modo de alto contraste, a equipe decidiu que seria oportuno adicionar também um modo claro. Como o trabalho já envolve atualizar o estilo do site e adicionar uma opção de alternância, você orientará o agente para incluir os dois modos.

## Revisar as tarefas do Copilot cloud agent

Veja o estado atual das tarefas atribuídas ao agente na nuvem.

1. Acesse a [página de agentes do Copilot](https://github.com/copilot/agents).
2. Examine as tarefas nos painéis principal e esquerdo. Você deverá ver as tarefas atribuídas ao Copilot, incluindo:
   - atualizar a documentação da base de código.
   - gerar APIs para modificar jogos.
   - adicionar um modo de alto contraste ao site.
3. Selecione uma tarefa em andamento e revise as operações feitas pelo Copilot, que podem incluir:
   - obter o código do repositório.
   - configurar o ambiente de trabalho do Copilot.
   - configurar servidores MCP.
   - executar etapas para concluir a tarefa.

> [!NOTE]
> As etapas exibidas variam de acordo com o estado do trabalho e a abordagem do Copilot.

4. Observe também o painel do pull request à direita, que mostra o PR e os arquivos alterados.

## Orientar o agente na nuvem

Agora que você conhece as tarefas ativas, peça ao Copilot que inclua uma opção de modo claro enquanto trabalha no modo de alto contraste.

1. Selecione a sessão do modo de alto contraste. O título exato depende do nome escolhido pelo Copilot e do estado do trabalho.

    ![Sessão de acessibilidade no painel central](../../../_images/ex6-accessibility-session.png)

2. Aguarde alguns minutos, até que a configuração seja concluída e o agente comece a trabalhar. Você verá mensagens semelhantes às indicadas na interface.
3. Na caixa de diálogo **Steer active session while Copilot is working**, envie este prompt:

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Captura de tela da tarefa do agente na nuvem na página de agentes, com a caixa de diálogo para orientar a sessão ativa destacada](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pressione <kbd>Enter</kbd> para enviar o prompt.
5. Observe como o Copilot confirma o pedido e o inclui no fluxo de trabalho.

## Deixar o Copilot trabalhar

Como antes, o Copilot executará a tarefa atualizada. Ele incorporará o novo pedido depois de concluir a etapa em andamento no momento em que você enviou a mensagem.

O processo pode levar vários minutos. Aproveite esse tempo para refletir sobre o que aprendeu e explorou.

## Resumo e próximos passos

Você explorou a página de agentes do Copilot, o painel central para monitorar e orientar tarefas do GitHub Copilot cloud agent. Nele, você pode acompanhar tarefas ativas e concluídas, revisar o trabalho e redirecionar uma tarefa em andamento.

Você:

- explorou o painel central e a página de agentes para monitorar tarefas.
- redirecionou uma sessão em andamento para solicitar funcionalidade adicional.

Enquanto o Copilot conclui o trabalho de acessibilidade, você pode [iterar sobre os pull requests criados pelo Copilot][next-lesson].

## Recursos

- [Página de agentes do Copilot][agents-page]
- [Sobre agentes personalizados][custom-agents]

---

| [← Exercício anterior: Agentes personalizados][previous-lesson] | [Próximo exercício: Iterar sobre o trabalho do Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
