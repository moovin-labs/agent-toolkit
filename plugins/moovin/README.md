# Moovin

Use a Moovin durante a conversa com seu assistente de IA. Consulte informações, encontre
os dados de que precisa e peça alterações nos serviços da plataforma.

## Instalação

No Claude Code, execute:

```text
/plugin install moovin --marketplace moovin-labs/agent-toolkit
```

Para ChatGPT, Claude e Codex CLI, escolha sua plataforma no
[guia de instalação](https://github.com/moovin-labs/agent-toolkit#comece-por-aqui).

## Como usar

1. Siga as instruções do assistente para entrar na Moovin e autorizar a conexão.
2. Informe em qual conta deseja trabalhar. Se precisar, peça para listar suas contas.
3. Faça seu pedido. O assistente encontra as operações disponíveis e consulta as
   informações necessárias para realizar a tarefa.

O plugin respeita suas permissões e as contas autorizadas na conexão. Para alterar dados,
o servidor também precisa permitir essa ação. Se faltar alguma informação, como a conta ou
o registro que deseja alterar, o assistente pede que você a informe.

Experimente pedir:

- “Liste minhas contas disponíveis na Moovin.”
- “O que posso consultar sobre meus atendimentos?”
- “Mostre as conversas abertas da Loja Exemplo.”
- “Ajuste o cabeçalho e o rodapé do tema da Loja Exemplo e mostre o preview.”

Substitua “Loja Exemplo” pelo nome de uma das suas contas. A skill
[`moovin-mcp`](skills/moovin-mcp/SKILL.md) orienta o assistente a escolher as ferramentas,
consultar os parâmetros e conferir o resultado de cada pedido.

Para temas, layouts, seções, banners, vitrines, menus, cores e fontes, a skill
[`moovin-storefront`](skills/moovin-storefront/SKILL.md) orienta a personalização da
loja e devolve o link de preview somente na resposta final. Publicação e ativação
do tema são etapas próprias; configurações compartilhadas do site podem afetar a loja diretamente.

## Configuração da conexão

| Campo        | Valor                        |
| ------------ | ---------------------------- |
| Endpoint     | `https://mcp.moovin.app/mcp` |
| Transporte   | Streamable HTTP              |
| Autenticação | OAuth                        |
| Scope        | `api:access`                 |

O assistente descobre as configurações de autenticação pelo servidor. O cliente OAuth
cuida do login, do consentimento e da renovação dos tokens; o IAM autoriza as contas
disponíveis para cada usuário. O uso de CIMD depende do suporte do cliente OAuth e do IAM.

Mantenha credenciais fora dos arquivos do plugin. Cada pessoa autoriza a conexão com seu
próprio usuário.

## Formatos do pacote

`plugin.json` e `mcp.json` atendem ao formato Agent Plugins 1.0.
`.claude-plugin/plugin.json` e `.mcp.json` permitem a instalação pelo Claude. Os dois
formatos usam a mesma pasta `skills/`.

Ao atualizar o plugin, mantenha a versão e os dados comuns iguais nos dois manifestos. Se
mudar o endpoint, atualize os dois arquivos de conexão.
