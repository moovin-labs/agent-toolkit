---
name: moovin-storefront
description: >-
  Gerencia a aparência e a configuração da loja virtual Moovin: temas, templates,
  layouts, seções, banners, vitrines, menus, cores, fontes, rodapé, landing pages e
  aparência do checkout. Use para personalizar, pré-visualizar, publicar ou ativar
  temas pelo MCP, devolvendo o link de preview somente na resposta final. Não se
  destina à gestão de pedidos, estoque ou preços de produtos.
---

# Gerenciar a vitrine da Moovin

Use o MCP da Moovin e as orientações de [moovin-mcp](../moovin-mcp/SKILL.md) para
conta, autenticação, descoberta de operações e tratamento de erros. Esta skill
acrescenta orientações para personalizar temas, layouts e conteúdo da loja.

## Identificar o contexto

1. Consulte as contas acessíveis e use a conta explicitamente escolhida pelo
   usuário nesta conversa. Não deduza a conta do domínio ou do tema nem escolha
   `initialAccountId` automaticamente.
2. Resolva o site e o tema por consultas. Diferencie `accountId`, `siteId`,
   `themeId`, `templateId`, `sectionId`, `menuId` e `landingPageId`. “Tema ativo”
   pode ser resolvido pelo estado consultado; nomes ou alvos ambíguos exigem
   esclarecimento.
3. Descubra as operações disponíveis e consulte `get_operation` antes de cada
   operação ainda não inspecionada. Os contratos atuais prevalecem sobre os IDs e
   exemplos das referências. Use somente `input.path`, `input.query` e `input.body`
   aceitos, com a conta escolhida em cada chamada.
4. Identifique o mecanismo clássico ou atual pelas respostas e pelo contexto da
   loja, conforme [temas e site](references/temas-e-site.md). `eco-store` também
   oferece compatibilidade com o mecanismo atual. A mera presença de
   `eco-template` no catálogo, um UUID ou `settings.version = 2` não identifica a
   tecnologia da conta. Não experimente gravações para descobri-la.
5. Resolva a URL de preview antes de alterar o layout. Leia
   [preview](references/preview.md) para ambiente, página, seção e limitações.

## Escolher o fluxo

Leia somente as referências necessárias ao pedido:

| Pedido | Referência |
| --- | --- |
| Biblioteca de temas, copiar, renomear, ativar, publicar, migrar; logo, domínio e dados da loja | [Temas e site](references/temas-e-site.md) |
| Cores, fontes, configurações dinâmicas, checkout, criar/reordenar/ocultar seções e montar rodapé | [Configurações e seções](references/configuracoes-e-secoes.md) |
| Banners, produtos de vitrine, vídeos, stories, menus, arquivos e landing pages | [Menus e conteúdo](references/menus-e-conteudo.md) |
| Link de preview na resposta final | [Preview](references/preview.md) |

## Executar e conferir

- Para personalização visual, trabalhe na versão de edição (`preview`). Salvar o
  rascunho, publicar seu conteúdo e selecionar o tema ativo são ações diferentes.
  Publique ou ative quando isso fizer parte do pedido; aproveite autorização já
  dada, sem pedir confirmação novamente a cada campo.
- Configurações do site, metadados de recursos, scripts e migração não têm a mesma
  separação de preview. Identifique esse alcance antes de gravar. Não transforme
  um pedido de mudar cores em migração, troca de domínio ou publicação.
- Leia os dados atuais e as instruções de configuração do template antes de
  montar o payload. Cores, menus e fontes usam identificadores definidos pelo
  template; não invente chaves a partir de seus rótulos na interface.
- Listas de seções, conteúdos e árvores de menu são substituições completas.
  Preserve o conteúdo fora do pedido e sua ordem. Para mapas gravados com PUT,
  preserve as chaves não alteradas. Envie apenas campos de escrita aceitos, sem
  copiar indiscriminadamente objetos expandidos da leitura.
- Faça gravações dependentes em sequência. Use os IDs retornados, releia o
  recurso e confira a mudança, inclusive a preservação dos demais itens. Se
  houver edição concorrente identificável, reconcilie-a antes de substituir dados.
- Em erro ou timeout, verifique o estado antes de repetir. Quando não for possível
  confirmar, informe a incerteza; não duplique temas, menus, seções ou uploads.
- Se faltar uma operação ou consulta auxiliar, explique qual parte do pedido não
  pôde ser executada, prossiga com partes independentes e preserve o estado existente.
  Não invente IDs, não use conteúdo fictício como dado real e não contorne o
  catálogo com chamadas diretas à API ou credenciais do navegador.

## Devolver o preview somente no final

Inclua **`[Ver preview](URL)`** somente na resposta final, junto com o resumo do
que mudou e o estado confirmado. Em tarefas com várias ações, consolide os
resultados e apresente o link do estado mais recente do tema. As atualizações
intermediárias podem informar o progresso, sem enviar URLs de preview. Se o pedido
envolver vários temas, reúna seus links na resposta final, identificando cada um.

Na resposta final, use o mesmo padrão para consultas, falhas ou ações parcialmente
concluídas quando o tema já estiver identificado. Diga “salvo no preview”,
“publicação aceita” ou
“alteração direta na loja” conforme a operação. Só diga que a aparência foi
verificada quando tiver inspecionado a página. Upload sem vínculo ainda não muda
o layout; publicação aceita não comprova atualização de cache.

Nunca invente uma URL quando faltar o tema ou o ambiente. Explique o dado
necessário. Após exclusão, não apresente o link do tema excluído como funcional;
siga a orientação de [preview](references/preview.md).
