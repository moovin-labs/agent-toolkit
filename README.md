# Moovin Agent Toolkit

Use seu assistente de IA para consultar e atualizar informações na Moovin.

O plugin **Moovin** conecta o ChatGPT, Claude ou Codex CLI aos serviços da plataforma. Você
pode consultar atendimentos, encontrar contatos e pedir ajuda com as tarefas do dia a dia,
usando suas contas e permissões da Moovin.

## Comece por aqui

Escolha onde quer usar a Moovin:

[ChatGPT](#chatgpt) · [Claude](#claude) · [Claude Code](#claude-code) · [Codex CLI](#codex-cli)

Você precisa de um usuário Moovin com acesso às contas em que deseja trabalhar. O
assistente orienta o login e a autorização da conexão durante a instalação ou no primeiro
uso.

### ChatGPT

No aplicativo desktop do ChatGPT, use o plugin em **Work** ou **Codex**.

1. Abra **Plugins**, clique em **Add** e escolha a opção de adicionar um marketplace.
2. Em **Source**, informe `moovin-labs/agent-toolkit`.
3. Deixe **Git ref** e **Sparse paths** vazios.
4. Clique em **Add marketplace**.
5. Abra **Browse directory**, selecione o marketplace **Moovin** e instale o plugin
   **Moovin**.
6. Siga as instruções para conectar a Moovin e abra uma nova conversa em **Work** ou
   **Codex**.

Veja também a
[documentação de marketplaces no ChatGPT desktop](https://developers.openai.com/plugins/build/plugins#build-your-own-curated-plugin-list).

O plugin **Moovin** ainda não está disponível na versão web do ChatGPT. Para usá-lo,
instale-o no aplicativo desktop seguindo os passos acima.

### Claude

No Claude na web ou no aplicativo desktop:

1. Abra **Customize → Plugins**.
2. Selecione **Add → Add marketplace → Add from a repository**.
3. Informe `moovin-labs/agent-toolkit`.
4. Localize **Moovin** no marketplace `moovin-labs` e selecione **Add**.
5. Conecte a Moovin quando solicitado e abra uma nova conversa.

É necessário um plano pago do Claude e permissão para instalar plugins.
[Veja as instruções do Claude](https://support.claude.com/en/articles/13837440-use-plugins-in-claude).

### Claude Code

No Claude Code 2.1.275 ou mais recente, execute:

```text
/plugin install moovin --marketplace moovin-labs/agent-toolkit
```

Confirme a adição do marketplace, instale o plugin e siga as instruções para conectar a
Moovin.

### Codex CLI

No terminal, adicione o marketplace:

```sh
codex plugin marketplace add moovin-labs/agent-toolkit
```

Abra o Codex CLI e use `/plugins` para selecionar o marketplace **Moovin** e instalar o
plugin **Moovin**. Conecte a Moovin quando solicitado e inicie uma nova conversa para
usar o plugin.

Veja também as
[instruções de plugins do Codex CLI](https://learn.chatgpt.com/docs/plugins#plugin-browser-in-codex-cli).

## Seu primeiro pedido

Comece pedindo:

> Liste minhas contas disponíveis na Moovin.

Escolha uma das contas retornadas e faça seu pedido:

> Use a conta Loja Exemplo e mostre as conversas abertas.

Substitua **Loja Exemplo** pelo nome da sua conta. Na mesma conversa, o assistente
continua usando essa conta até você pedir para trocá-la.

Você também pode experimentar:

- “O que posso consultar sobre meus atendimentos?”
- “Encontre o contato pelo e-mail que vou informar.”
- “Quais serviços da Moovin estão disponíveis?”

## O que está incluído

O [plugin Moovin](plugins/moovin/README.md) reúne a conexão com os serviços da plataforma
e a skill [moovin-mcp](plugins/moovin/skills/moovin-mcp/SKILL.md), que orienta o
assistente a identificar suas contas, encontrar as ações disponíveis e conferir os
resultados dos seus pedidos.

As ações disponíveis dependem dos serviços conectados e das suas permissões. Algumas
conexões permitem apenas consultas; para alterar dados, a integração também precisa estar
habilitada para isso.

## Precisa de ajuda?

- **O plugin não aparece no marketplace:** confira se você adicionou
  `moovin-labs/agent-toolkit` e procure por **Moovin** na lista de plugins.
- **Sua conta Moovin não aparece:** peça para listar as contas e confira seu acesso na
  plataforma. Se a conta não foi autorizada na conexão, reconecte a Moovin para revisar
  essa autorização.
- **O assistente pede para entrar novamente:** refaça a conexão pelo próprio aplicativo e
  peça para listar suas contas antes de continuar.

## Desenvolvimento

Consulte o [guia de desenvolvimento](DEVELOPMENT.md) para conhecer a estrutura do
repositório, testar uma instalação local e adicionar novas skills.
