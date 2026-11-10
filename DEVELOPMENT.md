# Desenvolver e manter o toolkit

O repositório reúne os pacotes dos plugins e os catálogos usados para distribuí-los. Cada
plugin deve conter as próprias skills, referências e configurações de conexão.

## Estrutura

```text
agent-toolkit/
├── .agents/plugins/marketplace.json
├── .claude-plugin/marketplace.json
├── DEVELOPMENT.md
└── plugins/
    └── moovin/
        ├── plugin.json
        ├── mcp.json
        ├── .claude-plugin/plugin.json
        ├── .mcp.json
        ├── README.md
        └── skills/
            ├── moovin-mcp/
            │   └── SKILL.md
            └── moovin-storefront/
                ├── SKILL.md
                └── references/
```

Os dois marketplaces têm o identificador `moovin-labs` e apontam para `./plugins/moovin`,
relativo à raiz do repositório. O plugin `moovin` usa `plugin.json` e `mcp.json` no
formato Agent Plugins 1.0, além dos arquivos de compatibilidade com o Claude.

Mantenha todos os arquivos necessários ao plugin dentro da sua pasta. Referências das
skills e symlinks não devem apontar para fora do pacote.

## Instalação local

Na raiz do repositório, adicione a cópia local ao cliente em que deseja testar.

### Codex CLI

```sh
codex plugin marketplace add .
```

Abra o Codex CLI e use `/plugins` para instalar **Moovin** pelo marketplace **Moovin**.

### Claude Code

```sh
claude plugin marketplace add .
claude plugin install moovin@moovin-labs
```

Se o marketplace `moovin-labs` já estiver cadastrado a partir do GitHub, confira a fonte
configurada antes de testar. O cliente precisa carregar a cópia local para que você possa
verificar as alterações feitas no diretório de trabalho.

## Skills

Adicione cada skill em `plugins/moovin/skills/<nome>/SKILL.md`, com instruções em
português e uma descrição clara de quando usá-la. Guarde referências e outros recursos
dentro da pasta da skill quando forem necessários ao seu funcionamento.

As skills orientam o uso dos serviços. Operações, parâmetros e schemas são descobertos
pelo catálogo do MCP. Ao atualizar `moovin-storefront`, confira os contratos disponíveis
e as regras de substituição de listas, publicação e construção do preview.

Este repositório e os pacotes distribuídos são públicos. Inclua somente instruções
necessárias ao uso do plugin. Relatórios de análise interna, hashes e caminhos de
repositórios privados, endpoints não expostos pelo MCP, detalhes de infraestrutura e
planos de integração de serviços devem permanecer fora do conteúdo versionado.

## Versões e configuração

- Ao lançar uma versão, atualize `plugin.json` e `.claude-plugin/plugin.json` do plugin.
  Use a mesma versão SemVer e mantenha os dados comuns iguais nos dois.
- Mantenha a mesma URL e o mesmo nome de servidor em `mcp.json` e `.mcp.json`. O
  transporte usa `streamable-http` no formato portátil e `http` no formato Claude.
- Guarde anotações de trabalho e arquivos temporários em `.tmp/`, ignorada pelo Git.
- Mantenha credenciais fora dos arquivos do plugin. A autenticação é feita pela conexão
  OAuth do cliente.

## Validação

Valide os manifestos do Claude com:

```sh
claude plugin validate --strict .
claude plugin validate --strict ./plugins/moovin
```

Confira também os arquivos JSON pelos schemas indicados em `$schema`, os caminhos dos
marketplaces e os links da documentação.

Para testar a integração, entre pelo assistente, escolha uma conta de teste e execute uma
consulta. Verifique o fluxo OAuth, a descoberta das ferramentas e as permissões no cliente
que receberá o plugin. Testes que alteram dados devem ter um objetivo definido e
autorização para a alteração.

## Referências

- [Agent Skills](https://agentskills.io/specification)
- [Agent Plugins](https://agent-plugins.org/specification)
- [Empacotamento de plugins para ChatGPT e Codex](https://developers.openai.com/plugins/build/plugins)
- [Manifesto de plugins Claude](https://code.claude.com/docs/en/plugins-reference)
- [Marketplaces Claude](https://code.claude.com/docs/en/plugin-marketplaces)
