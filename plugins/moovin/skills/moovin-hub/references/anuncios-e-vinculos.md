# Criar anúncios, vincular ofertas e mapear categorias

## Criar ou editar um anúncio

1. Use `createAnnouncementWithBuilder` com `command: init` e `channel.id`.
   Para um anúncio novo, envie os produtos ou kits em `sources`. Para editar,
   use `edit.id` com o ID do anúncio no Hub. Se a fonte já tiver um anúncio no
   canal, consulte o existente antes de continuar.
2. Monte `announcements` a partir do resultado de `init`, usando os campos
   aceitos pelo schema. Preserve imagens, variações e configurações ao editar e
   envie o ID em `announcement.id` para distinguir anúncios da mesma fonte.
   Use `run` para inspecionar as pendências e `build` para validar. Uma
   solicitação de prévia termina nessa etapa. O builder exige `call_write`
   inclusive para essas etapas de preparação.
3. Para efetivar a criação ou edição solicitada, envie `commit` com os dados
   validados. Isso grava no Hub e aciona a sincronização com o canal. Verifique
   o resultado no anúncio e nas atividades antes de solicitar um reenvio. Se um
   lote falhar, consulte cada anúncio antes de retomar os itens pendentes.

## Vincular um anúncio que já existe no marketplace

- Para revisar sugestões existentes, confira o canal e o ID da execução em
  `getMatchProgress`. Essa operação retorna a descoberta mais recente da conta.
  Se ela corresponder ao canal desejado, consulte `getMatchItemPaginatedList`
  com `withSuggestion: true` e filtre pela situação do vínculo.
- Para uma nova descoberta, use `startMatch` com o canal, o critério de sugestão
  (SKU, GTIN ou ambos) e os filtros solicitados. Mantenha
  `autoLinkSuggestions` desativado quando o objetivo for revisar sugestões.
  Ative-o apenas se o pedido incluir aplicar os vínculos automaticamente.
- Para aplicar um vínculo com `linkMatch`, confira o código do anúncio no canal,
  o produto de origem e as correspondências entre as variações. Envie `product`
  e `variations` juntos para um vínculo explícito. Omita ambos somente quando
  houver uma única sugestão compatível com o pedido. Confira o anúncio retornado.

## Mapear categorias e atributos

- Localize a categoria da loja em `getCatalogCategories` e a categoria e os
  atributos de destino em `getChannelCategorization` para o canal escolhido.
  Se um atributo depender de escolhas anteriores, envie `dependentValues` em
  `getChannelCategorizationAttributeOptions`. Essa consulta exige `call_write`.
- Para valores vindos do catálogo, consulte `getCatalogAttributes`,
  `getCatalogSpecifications` ou `getCatalogBrands` conforme a origem do valor.
  Confira os identificadores dos dois lados ao montar o de/para.
- Filtre `getMappingPaginatedList` por `channelId` e localize a categoria da
  loja nos resultados. Use `createMapping` quando faltar o mapeamento para essa
  categoria no canal. Para mudar o destino, use `updateMapping` com o ID do
  mapeamento existente. Ao editar configurações ou atributos, leia
  `getMappingById` e preserve os demais itens nas listas enviadas, pois cada
  lista substitui a anterior.
