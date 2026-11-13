---
title: "Exercício 5: Iterar sobre o trabalho do GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Exercício anterior: Gerenciar agentes][previous-lesson] |
|:--|

## Revisar o trabalho

Durante este laboratório, você trabalhou com o GitHub Copilot em várias tarefas para melhorar a experiência e adicionar funcionalidades. Você pediu ao Copilot que documentasse o código, criasse um recurso de jogos relacionados para a equipe de design explorar e implementasse recursos de acessibilidade, incluindo opções de alto contraste e modo claro. Agora, examine as alterações e, se necessário, dê feedback ao Copilot para aprimorá-las.

### Cenário

Os fundamentos do design de software e do DevOps não mudam com a chegada da inteligência artificial (IA) generativa. Sempre é necessário revisar o código gerado e seguir o processo normal de DevOps. Antes de habilitar a revisão pelo restante da equipe, examine as sugestões do GitHub Copilot para a documentação, os jogos relacionados e os recursos de acessibilidade.

## Segurança e GitHub Copilot cloud agent

Como o Copilot cloud agent trabalha de forma assíncrona e sem supervisão, há restrições de segurança:

- O Copilot tem acesso de leitura ao repositório e acesso de gravação **somente** à branch que usará para o código.
- O agente na nuvem é executado no GitHub Actions, em um ambiente separado e efêmero.
- Um ser humano precisa aprovar a execução de qualquer fluxo de trabalho do GitHub Actions.
- [O acesso a recursos externos é limitado por padrão](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall), inclusive a servidores MCP.

## Revisar a documentação gerada

Comece pelo primeiro pull request (PR) criado pelo agente na nuvem: o PR que adiciona documentação ao código. Você usará a interface padrão de PRs do GitHub.com.

> [!NOTE]
> Talvez você veja um aviso de que o Copilot foi bloqueado por um firewall. Isso é **esperado**, pois o Copilot tem acesso limitado a recursos externos por padrão, incluindo servidores MCP. Se quiser, você pode [personalizar ou desabilitar o firewall do Copilot cloud agent][agent-firewall].

1. Volte ao repositório em github.com.
2. Selecione **Pull Requests** para abrir a lista de PRs.
3. Abra o PR com um título semelhante a **Add missing documentation**.

> [!NOTE]
> Se o Copilot ainda estiver trabalhando, o PR terá a indicação **[WIP]**. Aguarde a conclusão do trabalho, que pode levar alguns minutos.

4. Quando o PR estiver pronto, selecione a guia **Files changed** e revise as alterações.

    ![Guia Files changed](../../../_images/shared-pr-files-changed.png)

5. Examine o código atualizado, que inclui comentários TypeScript Documentation (TSDoc) e outra documentação. As alterações exatas variam. Procure comentários TSDoc, convenções de TypeScript e cabeçalhos de comentário, que seguem os arquivos de instruções personalizados vistos no início do Exercício 2.
6. Quando terminar a revisão, volte à guia **Conversation** e role a página para baixo.
7. Verifique se há uma indicação de que alguns fluxos de trabalho aguardam aprovação.
8. Se houver fluxos de trabalho aguardando aprovação, selecione **Approve and run workflows**.

   ![Aprovar e executar fluxos de trabalho](../../../_images/shared-approve-workflows.png)

9. Os fluxos de trabalho devem aparecer na seção de verificações do PR. Aguarde até que as verificações do projeto sejam aprovadas para o aplicativo Astro. Isso pode levar alguns minutos.

## Solicitar alterações ao GitHub Copilot

Você também pode mencionar o Copilot nos comentários do PR ou nos comentários em linha do código, como faria com outra pessoa da equipe. O Copilot verá esses comentários e iniciará outra sessão para atendê-los. Como os resultados não são determinísticos, não há um texto exato para solicitar. Por exemplo, você pode pedir para:

- adicionar cabeçalhos breves aos arquivos de código.
- adicionar comentários TypeScript Documentation (TSDoc) aos arquivos TypeScript e Astro.
- criar um README que descreva a estrutura do aplicativo Astro.

1. Adicione um comentário solicitando uma alteração na documentação gerada e mencione **@copilot**. Use uma das ideias acima ou outro pedido relacionado à documentação.
2. Selecione **View Session** para acompanhar o trabalho do Copilot. Observe que ele inicia uma nova sessão para fazer as atualizações.
3. Selecione **Back to pull request** para voltar ao PR.

    ![Voltar ao pull request](../../../_images/ex7-back-to-pr.png)

4. Quando o Copilot terminar, deverá aparecer um novo commit no PR.
5. Selecione a guia **Files changed** para revisar as alterações.

Continue iterando até ficar satisfeito. Depois, converta o PR de rascunho para pronto para revisão e faça o merge na branch principal.

![Converter o PR para pronto para revisão](../../../_images/ex7-ready-for-review.png)

## Revisar o recurso de jogos relacionados

Agora, volte ao PR criado pelo Copilot para resolver a issue de exibir jogos relacionados na página de detalhes de cada jogo.

1. Volte ao repositório no GitHub.com.
2. Selecione a guia **Pull Requests**.
3. Abra o PR com um título semelhante a **Show related games on the game details page**.
4. Selecione a guia **Files changed** e revise o código gerado.
5. Quando terminar, volte à guia **Conversation** e role a página para baixo.
6. Verifique se há fluxos de trabalho aguardando aprovação.
7. Se houver, selecione **Approve and run workflows**.

   ![Aprovar e executar fluxos de trabalho](../../../_images/shared-approve-workflows.png)

8. Aguarde as verificações do projeto na seção do PR. Elas devem ser aprovadas para o aplicativo Astro; isso pode levar alguns minutos.
9. **Opcional:** Para testar manualmente o recurso, mude para a branch no Codespace, abra o terminal e execute os comandos a seguir, substituindo `<branch-name>` pelo nome da branch criada pelo Copilot, como `copilot/fix-8`:

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

O Copilot criou o recurso de jogos relacionados. Você pode continuar iterando sobre o trabalho dele, por exemplo, para alterar quantos jogos relacionados são exibidos ou adicionar cabeçalhos e comentários TSDoc. Lembre-se de que essa issue foi atribuída **antes** da atualização das instruções personalizadas. Para solicitar alterações, adicione um comentário na guia **Conversation**; o Copilot iniciará uma nova sessão.

## Revisar os recursos de acessibilidade

Por fim, revise os recursos de acessibilidade implementados pelo agente personalizado. Esse PR deve incluir o modo de alto contraste atribuído no Exercício 3 e o modo claro solicitado no painel central no Exercício 4.

1. Volte ao repositório no GitHub.com.
2. Selecione a guia **Pull Requests**.
3. Abra o PR com um título semelhante a **Add high contrast mode to website**.

> [!NOTE]
> Se o Copilot ainda estiver trabalhando, o PR terá a indicação **[WIP]**. Aguarde alguns minutos até que ele conclua o trabalho.

4. Selecione a guia **Files changed** e revise o código gerado.
5. Examine a implementação, principalmente:
   - os componentes de alternância entre os modos.
   - o uso do armazenamento local para manter as preferências.
   - as alterações de CSS ou estilo dos modos de alto contraste e claro.
   - os atributos de acessibilidade, como Accessible Rich Internet Applications (ARIA) e navegação por teclado.
   - o código JavaScript/TypeScript que controla a alternância dos modos.
6. Quando terminar, volte à guia **Conversation** e role a página para baixo.
7. Verifique se há fluxos de trabalho aguardando aprovação.
8. Se houver, selecione **Approve and run workflows**.

   ![Aprovar e executar fluxos de trabalho](../../../_images/shared-approve-workflows.png)

9. Aguarde as verificações do projeto na seção do PR e confirme que foram aprovadas para o aplicativo Astro.
10. **Opcional:** Mude para a branch no Codespace e teste manualmente os recursos de acessibilidade. Abra o terminal e execute os comandos abaixo, substituindo `<branch-name>` pelo nome da branch criada pelo Copilot:

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    Depois, inicie o aplicativo e teste os controles de alto contraste e modo claro no navegador. Confirme que funcionam e que as preferências persistem após recarregar a página.

O agente personalizado de acessibilidade ajudou a orientar a implementação segundo as práticas recomendadas. Se encontrar problemas de acessibilidade ou oportunidades de melhoria, mencione **@copilot** em um comentário para solicitar alterações.

## Exercício opcional — continuar delegando

O agente na nuvem é útil quando você pode delegar itens reais do backlog e se dedicar a outras tarefas. Para praticar, crie algumas issues no repositório e atribua-as ao Copilot. Algumas ideias:

- criar um formulário de interesse de apoiadores na página de detalhes do jogo.
- implementar paginação na página de lista de jogos.
- adicionar validação de entrada e tratamento de erros aos auxiliares de acesso a dados.

## Resumo

Você concluiu o percurso do agente na nuvem. Ao longo dos exercícios, você:

- **Inspecionou os arquivos de instruções personalizados do repositório** para ver seu efeito no resultado do agente.
- **Atribuiu issues ao Copilot cloud agent** e observou a configuração do ambiente, o planejamento e a execução assíncrona.
- **Criou e usou um agente personalizado** de acessibilidade para adicionar modos de alto contraste e claro.
- **Usou a página de agentes do Copilot como painel central** para monitorar e orientar a sessão de acessibilidade.
- **Revisou e iterou sobre os pull requests do agente**, mencionando `@copilot` para solicitar alterações e aprovando fluxos de trabalho.

## Revisão e próximos passos

Você concluiu o percurso do agente na nuvem. Se quiser continuar explorando, os outros percursos complementam o que praticou:

- 🖥️ **[Percurso do VS Code](../../vscode/)** — explore o modo de agente do Copilot Chat e a integração com MCP diretamente no IDE.
- 💻 **[Percurso da CLI](../../cli/)** — entregue duas alterações revisadas pelo terminal com Plan e Autopilot, skills de agente, um agente personalizado de QA, MCP do Playwright, Agent Merge e uma ponte opcional `/delegate` de volta ao agente na nuvem.

No seu repositório, experimente estas ideias:

- Atribua ao agente na nuvem uma issue de refatoração ou de cobertura de testes.
- Crie um agente personalizado para outro domínio.
- Configure `copilot-setup-steps.yml` para outra pilha de tecnologia.

## Recursos

- [GitHub Copilot][github-copilot]
- [Sobre os agentes do Copilot][copilot-agents]
- [Atribuir issues do GitHub ao Copilot][assign-issue]
- [Práticas recomendadas para o fluxo de configuração do agente na nuvem][cloud-agent-best-practices]
- [Configurar o firewall do Copilot cloud agent][agent-firewall]

---

| [← Exercício anterior: Gerenciar agentes][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/
