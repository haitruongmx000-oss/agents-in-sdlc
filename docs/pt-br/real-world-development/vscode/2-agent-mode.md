---
title: "Exercício 2 - Adicionar funcionalidade com o Agent Mode do Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Até uma atualização simples de um aplicativo pode exigir alterações em vários arquivos e operações como executar testes. Em geral, um desenvolvedor precisa localizar todos os arquivos necessários, fazer alterações, executar os testes, depurar, identificar o arquivo que faltou atualizar e repetir o processo. O modo de agente do Copilot ajuda a coordenar esse trabalho.

O Copilot Agent Mode foi criado para trabalhar com mais autonomia no ambiente de desenvolvimento integrado (IDE). Ele explora a estrutura do projeto, faz as alterações necessárias, executa tarefas como testes e corrige automaticamente os problemas encontrados. Neste exercício, você usará o modo de agente para adicionar uma funcionalidade ao site.

> [!NOTE]
> Embora os nomes sejam parecidos, o modo de agente e o agente na nuvem oferecem experiências diferentes. O modo de agente trabalha no IDE e permite ciclos de feedback rápidos e interação. O agente na nuvem trabalha de forma assíncrona, como uma pessoa da equipe, e interage por meio de issues e pull requests.

Neste exercício, você aprenderá como:

- o Copilot Agent Mode explora o projeto, identifica arquivos relevantes e coordena alterações.
- o modo de agente implementa recursos na interface e na camada de dados.
- revisar alterações e testes gerados pelo modo de agente antes do merge.

## Cenário

À medida que a lista de jogos cresce, você quer permitir que as pessoas filtrem por categoria e editora. No exercício anterior, você adicionou um auxiliar de editoras. Agora, concluirá o trabalho restante na camada de dados, na interface e nos testes com o Copilot Agent Mode.

## Executar o site Tailspin Toys

Antes de alterar o código, explore o site para conhecer o comportamento atual.

O site é uma plataforma de financiamento coletivo para jogos de tabuleiro com temática de desenvolvimento. As pessoas podem listar jogos e consultar os detalhes. O site é um único aplicativo Astro que renderiza páginas como HyperText Markup Language (HTML) estático durante a compilação. As páginas consultam diretamente um banco de dados SQLite local no frontmatter, usando o mapeador objeto-relacional (ORM) Drizzle; não há uma interface de programação de aplicações (API) de backend separada nem um framework de interface no cliente. Os auxiliares reutilizáveis de acesso a dados ficam em `src/lib/`, e a interatividade usa um pequeno elemento `<script>` do Astro com APIs padrão do Document Object Model (DOM).

### Iniciar o site

Para executar o site, use a tarefa `npm run dev`, que inicia o servidor de desenvolvimento do único aplicativo Astro.

1. Volte ao codespace. Você deverá estar em `main` depois do exercício anterior.
2. Abra um terminal no Codespace pressionando <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) ou <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Crie uma branch para o trabalho de filtragem:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Inicie o site:

   ```bash
   npm run dev
   ```

   Quando o servidor Astro estiver pronto, deverá aparecer uma mensagem semelhante a esta:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Se uma caixa de diálogo solicitar a abertura de `http://localhost:4321` em um navegador, feche-a selecionando **x**.

5. Para abrir o site, mantenha pressionada a tecla <kbd>Command</kbd> (Mac) ou <kbd>Ctrl</kbd> (Windows/Linux) e selecione o endereço do servidor Astro `http://localhost:4321` no terminal.

> [!NOTE]
> Em um codespace, selecionar o link `localhost` no terminal redireciona automaticamente para `https://<your-codespace-name>-4321.app.github.dev/`. Esse endereço é um túnel privado para o servidor do codespace.

### Explorar o site

Com o site em execução, explore suas principais funcionalidades:

- **Home Page**: exibe uma lista de jogos de tabuleiro com títulos, imagens e descrições.
- **Game Details Page**: ao selecionar um jogo, abre uma página de detalhes com informações como título, descrição, editora e categoria.

## Explorar o backlog com o Copilot

> [!TIP]
> **Abrir o Copilot Chat**
>
> Volte ao codespace, abra o painel Copilot Chat e selecione **New Chat** para iniciar uma conversa limpa. O modo e o modelo variam por exercício; cada etapa informa o que selecionar.

O site funciona, mas você quer ampliar seus recursos. O modelo do projeto criou automaticamente um backlog de issues. Peça ao GitHub Copilot que mostre os itens.

1. No seletor de agentes da exibição Chat, selecione **Agent**. Esse agente planeja e implementa alterações em vários arquivos, executa comandos de terminal e invoca ferramentas.

   ![Captura de tela do seletor de agentes na exibição Chat.](../../../_images/shared-chat-mode-selector.png)

2. Selecione **Claude Sonnet 4.5** na lista de modelos disponíveis.

> [!CAUTION]
> Os autores deste workshop não recomendam um modelo em particular. Durante a criação do workshop, usamos Claude Sonnet 4.5 e o incluímos para tentar manter uma experiência consistente. Como os modelos de linguagem grandes (LLMs) são probabilísticos, as sugestões podem ser diferentes das descritas aqui. Isso é normal.

> [!NOTE]
> Devido à natureza probabilística dos LLMs, o Copilot pode usar um comando do Model Context Protocol (MCP) diferente, mas ainda deverá concluir a tarefa.

3. Envie este prompt para perguntar sobre o backlog:

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. Selecione **Continue** para executar o comando que lista as issues.
5. Revise a lista gerada.

Observe como o Copilot priorizou os itens de acordo com o que considera mais útil para os usuários.

## Revisar os arquivos de instruções

Antes de pedir ao agente que gere código, examine o arquivo de instruções que fornecerá contexto. O arquivo [user interface (UI)][ui-instructions] contém orientações para adicionar funcionalidades ao site.

1. No codespace, abra `.github/instructions/ui.instructions.md`.
2. Examine as orientações gerais para adicionar funcionalidades:
   - uma visão geral da arquitetura.
   - princípios de design de componentes, testabilidade e acessibilidade.
   - links para arquivos de instruções específicos, incluindo Astro, Tailwind Cascading Style Sheets (CSS) e Drizzle.

> [!TIP]
> Os arquivos de instruções podem fazer referência a outros arquivos de instruções e a arquivos do projeto. Os caminhos são relativos ao arquivo de instruções. Isso permite reutilizar orientações, dividir conjuntos complexos em arquivos menores e incluir exemplos e modelos.

## Implementar a filtragem

Para concluir a filtragem, serão necessárias pelo menos três alterações:

- adicionar ou aperfeiçoar a lógica restante de filtragem na camada de dados (`src/lib/`).
- adicionar ou atualizar testes do comportamento de filtragem.
- atualizar a página de listagem de jogos para incluir a interface de filtragem.

Os testes também devem ser executados e aprovados antes do merge. O Copilot Agent Mode pode realizar essas tarefas.

1. Continue a conversa atual ou selecione **New Chat** para iniciar outra.
2. Selecione **Add Context**, **Instructions** e **ui** para adicionar o arquivo de instruções.

   ![Captura de tela da seleção do arquivo de instruções de interface](../../../_images/ex3-select-instructions-file.png)

3. No seletor de agentes da exibição Chat, confirme que **Agent** está selecionado.

   ![Captura de tela do seletor de agentes na exibição Chat.](../../../_images/shared-chat-mode-selector.png)

4. Confirme que **Claude Sonnet 4.5** continua selecionado.
5. Envie este prompt para implementar os requisitos da issue relacionada no backlog:

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Acompanhe o Copilot enquanto ele explora o projeto, localiza os arquivos relevantes, incluindo auxiliares da camada de dados, interface e testes, altera os arquivos e executa os testes.

   ![Captura de tela do Copilot explorando os arquivos do projeto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> O Copilot executará várias tarefas, como explorar o projeto, alterar arquivos e executar testes. Isso pode levar alguns minutos, dependendo da tarefa e da base de código. Durante o trabalho, os controles **Keep** e **Undo** podem aparecer no editor. Quando o Copilot terminar, você poderá aceitar ou desfazer todas as alterações; não precisa selecionar esses controles enquanto o trabalho estiver em andamento.

7. Quando o Copilot solicitar, selecione **Continue** para executar os testes.

   ![Captura de tela de uma solicitação no painel Copilot Chat para confirmar a execução de testes](../../../_images/ex3-agent-mode-run-tests.png)

8. Pode haver pausas e falhas temporárias nos testes. O Copilot itera entre a geração de código e os testes até concluir a tarefa e não detectar erros.

   ![Captura de tela de uma sessão concluída do Copilot Agent Mode](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Examine o código gerado para identificar possíveis problemas.

> [!CAUTION]
> Sempre revise o código gerado pelo Copilot ou por outras ferramentas de inteligência artificial (IA).

10. Volte ao navegador em que o site está em execução e explore o recurso.
11. Depois de confirmar que tudo funciona e revisar o código, selecione **Keep** na janela Copilot Chat.

## Resumo e próximos passos

Você usou o GitHub Copilot Agent Mode para adicionar funcionalidades ao site Tailspin Toys. Nesta lição, você:

- usou o modo de agente para implementar funcionalidades na interface e na camada de dados.
- explorou como o modo de agente identifica arquivos relevantes e coordena alterações.
- revisou as alterações e os testes gerados antes do merge.

Agora, [teste o recurso com o servidor MCP do Playwright][next-lesson] e abra um pull request.

### Exercício complementar — implementar paginação

À medida que a lista de jogos crescer, será necessário habilitar a paginação. Use o que aprendeu neste exercício para pedir ao Copilot que atualize o site com esse recurso. Considere:

- seguir as práticas existentes e usar os arquivos de instruções disponíveis.
- decidir se as pessoas poderão escolher o tamanho da página ou se ele será fixo.
- fornecer ao Copilot as orientações necessárias no prompt.
- iterar com o GitHub Copilot, pedindo alterações e fornecendo contexto. Esse é o fluxo normal de trabalho.

## Recursos

- [Modos ask, edit e agent do Copilot: o que fazem e quando usar cada um][choose-mode]
- [Modo de agente no VS Code][vs-code-agent-mode]

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
