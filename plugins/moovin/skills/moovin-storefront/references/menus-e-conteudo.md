# Menus, mídias e conteúdo das seções

## Conteúdo de seções

Primeiro resolva o tema, a página, o grupo e o `sectionId`. A criação da seção e
seu `configs` pertencem à [estrutura do layout](configuracoes-e-secoes.md);
os itens abaixo são gravados separadamente. Envie arrays diretamente no corpo,
preservando itens e ordem fora do pedido.

| Conteúdo | `eco-store`: leitura / escrita | `eco-template`: leitura / escrita |
| --- | --- | --- |
| Banners | `getEditorBannerItems` / `saveEditorBannerItems` | `getEditorBanner` / `saveEditorBanner` |
| Vitrine de produtos ou filtro | `getEditorShowcaseItems` / `saveEditorShowcaseItems` | `getEditorShowcase` / `saveEditorShowcase` |
| Vídeo de banner | `getEditorBannerVideoItems` / `saveEditorBannerVideoItems` | `getEditorBannerVideo` / `saveEditorBannerVideo` |
| Vitrine em vídeo | `getEditorShowcaseVideo` / `saveEditorShowcaseVideo` | `getEditorShowcaseVideo` / `saveEditorShowcaseVideo` |
| Barra de informações | `getEditorStoreInformationItems` / `saveEditorStoreInformationItems` | `getEditorStoreInformation` / `saveEditorStoreInformation` |
| Avaliações da loja | `getEditorStoreReviewsItems` / `saveEditorStoreReviewsItems` | `getEditorStoreReviews` / `saveEditorStoreReviews` |
| Colunas do rodapé | `getEditorBlockColumns` / `saveEditorBlockColumns` | `getEditorBlockColumns` / `saveEditorBlockColumns` |
| Stories | Usar o serviço atual quando a loja o suportar. | `getEditorStories` / `saveEditorStories` |

Tipos disponíveis dependem do template; a existência da operação não habilita o
recurso no clássico. `SHOWCASE_BANNER` pode precisar das listas de banner e de
vitrine da mesma seção. Para conteúdo personalizado além das opções nativas,
use o fluxo de [seção HTML](configuracoes-e-secoes.md#seção-html-personalizada),
gravado no `configs` da própria seção. Texto + Imagem também pode usar o
`configs` dinâmico do tipo, sem uma API de itens própria.

- **Banners:** preserve `resource.desktop` e `resource.mobile`, link, textos e
  agendamento. No clássico, `resource.desktop` é obrigatório, mesmo que nulo/vazio.
  Não envie IDs numéricos temporários usados pela UI. Em links, o clássico pode
  usar `type/typeValue`; o atual usa o contrato de URL. Não converta um formato
  no outro sem consultar o schema. Resolva datas e fuso do agendamento e use o
  formato documentado; banner salvo pode ainda não estar no período de exibição.
- **Vitrine:** escolha produtos explícitos (`PRODUCT`) ou um filtro (`FILTER`).
  A UI e o clássico não misturam os dois modos nem usam vários filtros. Adicionar
  produtos preserva os produtos existentes; mudar para filtro substitui a lista
  por um item de filtro. Confirme que essa troca está no pedido. Produtos precisam
  de título/URN e, conforme o contrato, ID/imagem reais. Resolva esses dados por
  consultas disponíveis ou use dados já verificados; não invente produtos ou categorias.
- **Vídeos:** respeite os campos do tipo. Vitrine em vídeo aceita até seis itens
  conforme o schema e não aceita `videoFile` e `videoLink` simultâneos.
- **Stories:** até 12 stories e 10 produtos por story, conforme o schema;
  `mediaType` IMAGE/VIDEO, ação PRODUCTS/LINK, vídeo por FILE/LINK. Preserve capas,
  mídia e ação não alteradas. A UI aceita YouTube Shorts e Vimeo para esse campo;
  não generalize as regras de links da vitrine em vídeo. Metadados opcionais
  ausentes não devem ser fabricados.
- **Avaliações:** use depoimentos fornecidos ou existentes, sem criar avaliações
  fictícias como se fossem de clientes reais.

`null` nem sempre limpa campos de itens no mecanismo atual: alguns são atualizados
somente com string/número. Após limpar um campo, confira a leitura. Não remova e
recrie o item silenciosamente para contornar essa semântica.

## Arquivos

Use `dam-storage` → `saveFile` para upload: JSON com `content`,
visibilidade `public` e caminho relativo à conta. O conteúdo pode ser data URI,
texto ou URL acessível ao serviço, segundo o schema; base64 sem prefixo é texto.

Use caminho novo para um novo recurso, como faz o editor com nomes temporais;
`saveFile` também sobrescreve destinos existentes. `createFile` é uma alternativa
quando o contrato e a intenção exigirem criação sem sobrescrita. Não use
`system-file` para arquivos comuns da loja. Use a URL devolvida pelo serviço no
campo de imagem/vídeo, sem construí-la manualmente. Respeite MIME, dimensões e
limites do parâmetro, do servidor e do transporte MCP; uma data URI aumenta o
tamanho do payload. Não improvise multipart ou divisão em partes se o contrato
não oferecer esse fluxo. Um arquivo aceito não comprova renderização correta.
Remover uma imagem do layout não implica apagar o arquivo compartilhado no storage.

## Menus e navegação

1. Liste e leia os menus (`listMenus`, `getMenu`) para resolver o ID. Confira
   paginação; `name`/`query` não filtram a listagem no contrato direto atual de
   `eco-template`, portanto filtre os resultados lidos quando necessário.
2. Para criar, use `createMenu` com nome, tipo e menus especiais quando pedidos.
   Tipos: `CUSTOM`, `CATEGORY`, `CATEGORY_IMPORT`. `CATEGORY` é automático;
   edição manual de itens se aplica a CUSTOM/CATEGORY_IMPORT no estado READY.
   Não tente gravar durante PENDING. Não converta o tipo para contornar a restrição.
3. `updateMenu` atualiza nome/menus especiais, não substitui o endpoint de itens.
   Especiais são `NEWS`, `BRAND`, `SALE`; preserve a lista não alterada. O editor
   preenche rótulos faltantes com Novidades, Marcas e Promoções, respectivamente.
4. Leia `getEditorMenuItems`. Para adicionar, editar, mover ou remover um item,
   transforme a árvore completa de `subItems`, preservando os outros ramos e sua
   ordem; grave com `saveEditorMenuItems`.
5. Siga o formato do mecanismo. No clássico, `link.type` pode exigir
   `typeValue` com categorias, marcas ou atributo/valores. No atual, `destination`
   contém `{ type: CATEGORY|BRAND, id }` e o link usa `url`. `destination.status`,
   `parentId` e posições retornadas não são parâmetros de escrita por inferência.
   O serviço atual regenera IDs/posições; releia a árvore antes de outra edição.
6. Vincule o ID do menu ao parâmetro `SELECT` com origem MENU no cabeçalho, rodapé
   ou coluna correspondente. Criar o menu isoladamente não faz aparecer navegação
   em uma área que ainda referencia outro menu.
7. Confira `getEditorRenderedMenu` quando precisar verificar URLs resolvidas.
   Rascunho e renderização publicada usam operações diferentes. Publicar menus
   compartilhados pode afetar outros temas; aplique as regras de
   [publicação](temas-e-site.md).

Não confunda remoção de item com exclusão do recurso menu. Para excluir um menu,
descubra a operação correspondente no catálogo. Se ela não estiver disponível,
explique que a exclusão não pôde ser executada; não esvazie a árvore como substituto.

## Landing pages e páginas institucionais

Landing pages pertencem a `eco-template`: `listLandingPages`, `getLandingPage`,
`createLandingPage`, `updateLandingPage`, `deleteLandingPage`.

- `PRODUCT`, `CATEGORY` e `BRAND` exigem o vínculo correspondente; `BLANK` usa
  `urn`. Criação exige `title`, `status` e `type`, além do vínculo/URN.
- Existe somente uma página ativa por vínculo; URNs BLANK são únicas na conta.
  Alterar o tipo não é suportado. Não apague e recrie para contornar um conflito.
- Depois de criar/selecionar, edite `LANDING_PAGE/MAIN` com `landingPageId`.
  Seletores por produto/marca/categoria/URN podem resolver leituras, mas não
  substituem o ID na gravação de seções.
- Metadados/estado da landing page são diferentes do conteúdo em preview do tema.
  Exclusão remove também suas seções das versões; não a trate como edição de
  rascunho reversível.
- Para acompanhar o resultado, use a rota e `1eg-landing-page` descritos em
  [preview](preview.md).

Landing pages, páginas institucionais e configurações de SEO são recursos
distintos. Descubra no catálogo as operações correspondentes ao pedido e preserve
links e conteúdo existentes. Não substitua uma página institucional por uma
landing page sem que essa mudança faça parte do pedido.
