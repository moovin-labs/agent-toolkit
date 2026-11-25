---
name: moovin-hub
description: >-
  Opera canais de marketplace no Moovin Hub. Use para criar ou vincular anúncios,
  mapear categorias, ajustar preço ou estoque de um anúncio e investigar ou
  reprocessar falhas de sincronização de anúncios e pedidos com o canal.
---

# Operar marketplaces no Moovin Hub

Confira em `list_accounts` a conta escolhida pelo usuário nesta conversa. Se a
escolha faltar ou for ambígua, pergunte qual usar. Envie o `accountId` em cada
chamada de negócio. Descubra a ação com `search_operations` no serviço
`moovin-hub` e confira o `inputSchema` e a ferramenta em `get_operation`.
Em conexões somente leitura, atenda à parte de consulta. Para paginação,
reconexão e falhas após uma escrita, siga [moovin-mcp](../moovin-mcp/SKILL.md).

## Canais e anúncios

- Localize o canal em `getChannelPaginatedList`. Consulte `getChannelById` para
  conferir a conexão e as configurações. Use `getChannelStatus` se a dúvida for
  sobre a autenticação do canal. Para `createChannel`, resolva o provedor em
  `getProviderPaginatedList` e a conexão da conta em `getProviderConnectionsList`.
  Ao alterar um campo de `settings` com `updateChannel`, preserve os demais
  valores exigidos pelo schema.
- Para encontrar um anúncio, filtre `getAnnouncementPaginatedList` por
  `channelId`. Use `query` para SKU ou código do marketplace e
  `announcementCodeOnSource` para o código do produto na loja. Confira o anúncio
  em `getAnnouncementById` antes de atuar sobre uma variação.
- Para criar ou editar anúncios, vincular anúncios existentes por SKU ou GTIN,
  ou configurar o de/para de categorias, siga os
  [fluxos de anúncios, vínculos e categorias](references/anuncios-e-vinculos.md).
- Em `deleteAnnouncement`, a remoção afeta o anúncio, suas variações e o vínculo
  no Hub. O anúncio continua no marketplace. Se o pedido for retirar a oferta
  do ar, esclareça esse alcance antes de qualquer remoção.

## Preço e estoque no canal

- Para explicar uma falha, localize o SKU em `variations` no detalhe do anúncio.
  Leia `price.sync` para preço, `stock.sync` para estoque e `sync` para a própria
  variação. Use o status e a mensagem da parte afetada.
- Para ajustar a regra de preço de variações de **um anúncio**, use
  `updateAnnouncementPrice` com o ID do anúncio e os SKUs solicitados, cada um
  uma única vez. Para alterar preços por SKU no Hub com `updatePrice`, envie
  `channel.id` quando o pedido for para um canal. Omitir o canal pode atingir
  anúncios do mesmo SKU em vários marketplaces.
- `updateAnnouncementStock` altera o redutor de estoque de cada SKU do anúncio.
  Para reenviar o saldo da fonte ao canal, use `syncStock` com o `stock.id` da
  variação retornada por `getAnnouncementById`. Para alterar o saldo físico da
  loja ou o preço de origem, siga [mcp-products](../mcp-products/SKILL.md).

## Pedidos e diagnóstico de sincronização

- Localize o pedido com `getOrderPaginatedList` usando `codeOnChannel` para o
  código exato do marketplace. Confira o canal no resultado e use o ID do Hub
  em `getOrderById`. Para explicar uma falha, leia `sync.status` e
  `sync.message`. Para itens, pagamentos ou histórico da venda na loja, use
  [moovin-orders](../moovin-orders/SKILL.md).
- Em `getActivityPaginatedList`, filtre por canal, tipo e período. O filtro
  `announcementId` reúne atividades do anúncio, das variações, dos preços e dos
  estoques. Consulte `getActivityById` para a mensagem e o resultado de uma
  tentativa. Para indicadores agregados, `getActivityAnalytics` recebe o período
  em minutos.
- Um pedido de diagnóstico pode ser atendido com consultas. Quando o usuário
  solicitar nova sincronização, use `syncAnnouncementWithChannel` para o anúncio
  e confira a atividade retornada. Para um pedido, `processOrder` recebe o ID do
  Hub e retorna sem corpo. Releia o pedido e informe o estado de sincronização
  confirmado, o canal e eventual pendência.
