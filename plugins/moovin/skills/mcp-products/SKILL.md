---
name: mcp-products
description: >-
  Gerencia produtos, variações, preços e estoque na Moovin pelo MCP. Use para
  localizar ou cadastrar um produto, editar uma variação, consultar ou ajustar
  preços por SKU ou tabela, aplicar promoções e conferir ou atualizar saldos por
  centro de distribuição.
---

# Gerenciar produtos, preços e estoque

Use a conta escolhida pelo usuário nesta conversa. Confira seu ID em `list_accounts`
e envie o `accountId` em cada chamada de negócio. Se faltar a escolha ou houver
ambiguidade, peça ao usuário para identificar a conta. Siga [moovin-mcp](../moovin-mcp/SKILL.md)
para autenticação e tratamento de erros.

Descubra a ação com `search_operations` no serviço correspondente. Confira o
`inputSchema` e a ferramenta indicada por `get_operation`. A prévia de promoção
usa `call_write` mesmo quando apenas calcula um resultado. Em uma conexão somente
de leitura, use as consultas disponíveis e informe quais ações ficaram pendentes.

## Produtos e variações

- Em `oms-product`, use `getProduct` para o ID do produto e `getProductByVariationSku`
  para localizar o produto a partir do SKU de uma variação. Para uma busca por
  nome ou GTIN, use `listProducts` e confira qual variação corresponde ao pedido.
- Em `createProduct`, confira os campos obrigatórios, incluindo o ID do produto
  e pelo menos uma variação com SKU. Resolva marca e categoria pelos cadastros da
  conta. Para editar o produto, use `updateProduct` e envie os campos solicitados.
  Para editar só uma variação existente, use `getVariationBySku` e `updateVariation`.
  Para adicionar uma variação, confira se o SKU já existe, pois `setProductVariation`
  tanto cria quanto atualiza a variação no produto indicado.
- Preço e quantidade são definidos por SKU. Se o pedido abranger todas as
  variações de uma seleção, percorra as páginas de `listProductVariationSkus`.
  O total retornado conta produtos. Para contar variações, conte os SKUs distintos.
  Se o usuário se referir a uma única variação e houver várias possíveis, esclareça qual.

## Preços

- Em `oms-pricing`, consulte `getPriceBySku`. Em `createOrUpdatePrice`, o objeto
  `price` exige custo (`cost`), preço de lista (`list`) e preço promocional
  (`sale`). Para alterar um só valor, preserve os demais com os valores cadastrados.
  No primeiro cadastro, peça os valores obrigatórios que faltarem.
- Para alterar apenas o preço base, omita `tables`, inclusive em
  `createOrUpdateManyPrices`. Para editar o preço de uma tabela, identifique a
  tabela ativa do tipo `PER_PRODUCT` em `getTablePaginatedList`. Ao enviar
  `tables`, inclua também os vínculos por produto que devem ser mantidos, com
  seus IDs e valores atuais de `list` e `sale`. Uma lista parcial pode remover
  outros vínculos do SKU.
- Para promoções em lote, use `previewBulkPromotionalPrice` com a seleção e a
  regra solicitadas. Confira os totais da prévia, pois a lista detalhada pode
  estar truncada. Se o pedido incluir a aplicação, use `applyBulkPromotionalPrice`
  com a mesma seleção, exclusões, regra e arredondamento. Quando houver processamento
  em segundo plano, acompanhe o ID retornado com `getBulkPromotionalPriceOperation`.

## Estoque

- Em `oms-inventory`, use `getStockBySku` para o saldo total e a distribuição
  por centro. Para explicar uma movimentação, consulte `listStockHistory`.
  Em `getStockAvailability`, um grupo fica disponível quando pelo menos um dos
  SKUs tem saldo positivo. Use um grupo por SKU se precisar da resposta individual.
- Antes de usar `updateStockBySku` ou `updateStockBatch`, confira os centros da
  conta em `listWarehouses`. Sem centro cadastrado, envie `total`. Com centros,
  envie apenas `warehouses` com o ID e o saldo final de cada centro afetado.
  Se o pedido trouxer só o total e houver mais de uma distribuição possível,
  pergunte como distribuí-lo.
- “Defina o saldo em 5” grava 5. Para “adicione 5”, leia o saldo atual do SKU
  no centro indicado ou no estoque geral e calcule o novo saldo. Após timeout,
  confira o saldo e o histórico antes de repetir um ajuste.

Em pedidos que combinam cadastro, preço e estoque, confirme o cadastro e os SKUs
antes das gravações dependentes. Releia os recursos alterados e informe a conta,
os SKUs e os resultados confirmados. Se uma etapa ou lote falhar, confira o que
foi aplicado antes de retomar os itens pendentes.
