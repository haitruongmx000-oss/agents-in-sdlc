---
title: "Exercício 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Exercício anterior: Instruções personalizadas][previous-lesson] |
|:--|

É provável que quase todas as organizações enfrentem algum tipo de dívida técnica: problemas de segurança não resolvidos, código legado que precisa de atualização ou solicitações de recursos esquecidas no backlog por falta de tempo. O GitHub Copilot cloud agent foi criado para tarefas como atualizar código e adicionar funcionalidades de forma autônoma. Ao concluir o trabalho, ele cria um pull request de rascunho para que um desenvolvedor revise a proposta. Assim, é possível delegar tarefas repetitivas, acelerar o desenvolvimento e se concentrar em questões mais amplas.

Neste exercício, você explorará com o Copilot cloud agent:

- como personalizar o ambiente de geração de código.
- como garantir que as operações sejam executadas com segurança.
- a importância de delimitar as issues com clareza.
- como atribuir issues ao Copilot.

## Cenários

A Tailspin Toys quer resolver algumas dívidas técnicas. Os prestadores de serviço que criaram a primeira versão do site deixaram a documentação em uma situação inadequada: ela está completamente ausente. Como primeiro passo, a equipe quer adicionar comentários TypeScript Documentation (TSDoc) a todas as funções exportadas do aplicativo.

Além disso, a equipe de design quer melhorar a descoberta de jogos. Ela gostaria que a página de detalhes de cada jogo mostrasse outros títulos da mesma categoria, para que as pessoas continuem navegando. Por enquanto, a equipe precisa apenas de uma implementação que permita testar a experiência. Embora essa tarefa seja um bloqueio, há issues mais prioritárias.

Esses são exemplos de tarefas que podem ser adiadas. São bons candidatos para o Copilot cloud agent, que trabalha de forma assíncrona enquanto o desenvolvedor se dedica a outras atividades e, depois, revisa o trabalho concluído.

## Apresentação do GitHub Copilot cloud agent

O [GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) executa tarefas em segundo plano, de forma semelhante a um desenvolvedor. Há várias maneiras de trabalhar com ele, inclusive [atribuindo uma issue do GitHub ao Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Depois da atribuição, o Copilot cria um pull request de rascunho para acompanhar o progresso, configura um ambiente e começa a trabalhar. Você pode examinar a sessão enquanto o trabalho está em andamento ou depois que ele termina. Quando a solução estiver pronta para revisão, o Copilot mencionará você no pull request.

## Importância de instruções bem delimitadas

O GitHub Copilot não é mágico: ainda é necessário pensar. Mesmo tarefas aparentemente simples podem ter complexidades ocultas. Por isso, [delimite cuidadosamente as tarefas atribuídas ao agente na nuvem][cloud-agent-best-practices]. Trate-o como um parceiro de programação de inteligência artificial (IA): trabalhe por etapas, aprenda, experimente e adapte o processo. Os fundamentos do desenvolvimento de software não mudam com a chegada da IA generativa.

## Instruções personalizadas neste repositório

Os exercícios anteriores apresentaram instruções personalizadas e como elas orientam o Copilot. Se você já concluiu o exercício sobre instruções personalizadas, viu o arquivo `.github/copilot-instructions.md` em ação. Caso contrário, este é um bom momento para examiná-lo. Antes de atribuir trabalho ao agente na nuvem, leia os arquivos de instruções incluídos no repositório para observar seus efeitos mais tarde.

Abra os arquivos a seguir na interface Web do GitHub ou em um codespace:

- `.github/copilot-instructions.md`: examine a seção **Code standards**, principalmente as expectativas de convenções TypeScript e comentários TSDoc.
- `.github/instructions/unit-tests.instructions.md`: observe o frontmatter `applyTo`, que aplica as instruções a arquivos `**/*.test.ts`.

Ao atribuir a issue `Code lacks documentation` ao agente na nuvem, observe os comentários TSDoc, as convenções de TypeScript e os cabeçalhos de comentário no PR resultante. Essas orientações vêm dos arquivos de instruções. Você revisará esses elementos novamente em um exercício futuro.

## Configurar o ambiente de desenvolvimento do agente na nuvem

Criar código normalmente exige um ambiente específico e scripts de configuração que garantam que tudo esteja pronto. O mesmo vale ao atribuir tarefas ao Copilot, que trabalha de maneira semelhante a um engenheiro de software.

O agente na nuvem usa o [GitHub Actions][github-actions] como ambiente de trabalho. Você pode personalizá-lo com um [fluxo de configuração especial][setup-workflow] no arquivo `.github/workflows/copilot-setup-steps.yml`, executado antes do início do trabalho. Isso disponibiliza ferramentas e dependências necessárias. O fluxo já foi configurado para este laboratório: ele instala o Node.js e as dependências do projeto, os navegadores do Playwright para testes de ponta a ponta e prepara o banco de dados SQLite migrado e preenchido com dados do aplicativo Astro:

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

O arquivo segue o formato dos demais fluxos do GitHub, mas tem alguns pontos importantes:

- Ele contém um único job chamado `copilot-setup-steps`, executado no GitHub Actions antes de o Copilot começar a trabalhar no pull request.
- O gatilho `workflow_dispatch` permite executar o fluxo manualmente na guia **Actions** do repositório. Isso é útil para testar o fluxo sem esperar que o Copilot o execute.

## Adicionar documentação

Embora todos reconheçam a importância da documentação, muitos projetos têm informações desatualizadas ou não têm documentação. Esse tipo de dívida técnica pode reduzir a produtividade e dificultar a manutenção do código ou a integração de novos desenvolvedores. O Copilot é útil para criar documentação, por isso esta é uma boa issue para atribuir ao agente na nuvem. Em um exercício futuro, você voltará para revisar o trabalho.

1. Em uma nova guia do navegador, acesse o repositório em github.com.
2. Selecione a guia **Issues**.
3. Selecione **New issue** para abrir a caixa de diálogo de nova issue.
4. Selecione **Blank issue**.
5. No campo **Title**, digite `Code lacks documentation`.
6. No campo **Description**, digite:

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. Selecione **Create** para criar a issue.
8. À direita, selecione **Assign to Copilot** para abrir a caixa de diálogo de atribuição.

    ![Atribuição do Copilot a uma issue](../../../_images/shared-assign-copilot.png)

9. Selecione **Assign**.
10. Selecione a guia **Pull Requests**.
11. Abra o novo pull request (PR), com um título semelhante a `[WIP]: Code lacks documentation`. Se ele ainda não aparecer, aguarde um ou dois minutos e atualize a página.
12. Depois de alguns minutos, o Copilot deverá criar uma lista de tarefas.

> [!NOTE]
> A lista de tarefas do Copilot pode levar vários minutos para aparecer no PR. O agente está configurando o ambiente com o fluxo de trabalho descrito acima, analisando o projeto e definindo uma abordagem.

13. Revise a lista e as tarefas que serão executadas.
14. Role a linha do tempo do PR para baixo e localize a atualização de que o Copilot começou a trabalhar na issue.
15. Selecione **View session**.

    ![Visualização da sessão do Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Talvez seja necessário atualizar a janela para ver o indicador atualizado.

16. Explore a sessão em andamento e observe como o Copilot examina o código, entende o estado do projeto, pausa para planejar e, depois, cria o código.

O trabalho pode levar vários minutos. Um dos objetivos do agente na nuvem é executar tarefas de forma assíncrona para que você possa se dedicar a outras atividades. Aproveite isso atribuindo outra tarefa ao agente e, em paralelo, trabalhando em um recurso do aplicativo.

## Adicionar uma seção de jogos relacionados à página de detalhes

Uma das vantagens do agente na nuvem é dividir o trabalho: você pode se concentrar em uma tarefa enquanto o agente cuida de outra. Mesmo que a seção de jogos relacionados não leve muito tempo para ser implementada, esse tempo pode ser usado em outras atividades. Atribua a tarefa ao Copilot cloud agent.

1. Volte ao repositório em github.com.
2. Selecione a guia **Issues**.
3. Selecione **New issue** para abrir a caixa de diálogo.
4. Selecione **Blank issue**.
5. No campo **Title**, digite `Show related games on the game details page`.
6. No campo **Description**, digite:

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. Selecione **Create** para criar a issue.
8. À direita, selecione **Assign to Copilot** para abrir a caixa de diálogo de atribuição.

    ![Atribuição do Copilot a uma issue](../../../_images/shared-assign-copilot.png)

9. Selecione **Assign**.

Em seguida, você verá o emoji 👀 no primeiro comentário da issue, indicando que o Copilot começou a trabalhar.

![O Copilot usa o emoji de olhos para indicar que está trabalhando na issue](../../../_images/ex4-issue-eyes-emoji.png)

O Copilot está trabalhando no segundo pedido. Como o agente na nuvem funciona de maneira semelhante a um engenheiro de software, não é necessário acompanhá-lo continuamente; revise o resultado quando estiver pronto. Agora, você pode criar e usar agentes personalizados.

## Resumo e próximos passos

Este exercício apresentou o [GitHub Copilot cloud agent][copilot-agents]. Com o agente na nuvem, você pode atribuir issues ao Copilot para execução assíncrona. Ele pode resolver dívidas técnicas, criar recursos ou ajudar a migrar código entre frameworks.

Você explorou:

- como personalizar o ambiente de geração de código.
- como garantir que as operações sejam executadas com segurança.
- a importância de delimitar as issues com clareza.
- como atribuir issues ao Copilot.

Enquanto o agente na nuvem trabalha, você pode criar e usar agentes personalizados. O [Copilot cloud agent também pode usar servidores MCP][cloud-agent-mcp] e as instruções personalizadas que você explorou em módulos anteriores.

## Recursos

- [Sobre o Copilot cloud agent][copilot-agents]
- [Atribuir issues do GitHub ao Copilot][assign-issue]
- [Práticas recomendadas para o fluxo de configuração do agente na nuvem][cloud-agent-best-practices]

| [Próximo exercício: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
