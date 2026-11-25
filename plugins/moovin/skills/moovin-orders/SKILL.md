---
name: moovin-orders
description: >-
  Consulta pedidos de venda da Moovin em modo somente leitura. Use para localizar
  pedidos, conferir itens, status, histórico, pagamento e entrega, contar pedidos
  por filtro ou consultar um resumo de vendas.
---

# Consultar pedidos da Moovin

Use `oms-order` (Pedidos) como fonte principal. Execute consultas de negócio
exclusivamente com `call_read`, mesmo quando a conexão permitir escrita. Se o
usuário pedir uma alteração, explique o limite da skill. Em um pedido misto,
atenda à parte de consulta sem executar nem delegar a alteração.

1. Confira em `list_accounts` a conta escolhida pelo usuário nesta conversa.
   Se faltar a escolha ou houver ambiguidade, apresente as opções e pergunte qual
   usar. Envie o `accountId` correspondente em cada `call_read`.
2. Use `search_operations` com `service: "oms-order"` e `readOnly: true`.
   Confira em `get_operation` se a operação é de leitura e indica `call_read`.
   Use os campos e tipos do `inputSchema` atual. Se faltar uma consulta adequada,
   informe o que ficou sem resposta. Nunca use `call_write` ou uma chamada direta
   à API como alternativa.
3. Escolha a consulta conforme a pergunta e o identificador disponível.

| O que o usuário quer saber | Consulta |
| --- | --- |
| Detalhes ou itens de um pedido | Com o ID interno, prefira `getRedactedOrder` quando os dados mascarados bastarem. Use `getOrder` quando precisar de um detalhe omitido. |
| Pedido pelo número da loja ou pelo código de origem | Use `getOrderBySequentialId` para o número sequencial da loja e `getOrderByExternalId` para o identificador atribuído pelo sistema de origem. |
| Quando o pedido mudou de situação | Use `getOrderHistory` com o ID do pedido. Para interpretar um status personalizado, consulte `getOrderStatus` pelo ID do status. |
| Pagamento ou entrega | Identifique o pagamento ou envio vinculado ao pedido. Use o ID do pagamento em `getOrderPayment` e o ID do envio em `getOrderShipping`. |
| Pedidos por cliente, período, situação ou canal | Use `listOrders` com os filtros solicitados. Diferencie a data de criação da data de atualização. Para uma contagem com esses filtros, use `countOrders`. |
| Faturamento ou resumo de vendas | Use `getOrderReport` quando o período solicitado estiver entre os aceitos. Para unidades vendidas de um produto, `getProductSales` considera pedidos aprovados e exclui o canal `IMPORT`. |

Se a pergunta for sobre **sincronização com um marketplace**, pesquise as leituras
em `moovin-hub`. Use `getOrderPaginatedList` para localizar o pedido pelo código do
canal e `getOrderById` com o ID retornado pelo Hub.

Na resposta, identifique a conta e o pedido ou os filtros usados. Ao listar todos
os pedidos, percorra a paginação. Se o resultado for parcial, indique a cobertura.
Mostre apenas os dados de cliente e pagamento necessários para responder.
Para reconexão OAuth e erros do MCP, siga as orientações de
[moovin-mcp](../moovin-mcp/SKILL.md).
