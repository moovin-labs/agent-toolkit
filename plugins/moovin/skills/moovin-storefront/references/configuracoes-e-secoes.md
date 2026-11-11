# Configurações dinâmicas e estrutura do layout

## Instruções do template e valores preenchidos

1. Leia o tema e suas referências. No clássico, obtenha `template.id` e use
   `eco-store` → `getTemplateSettings`. No atual, use `eco-template` →
   `getTemplateSettings`, sem ID, conforme o contrato retornado.
2. Leia os valores do preview: `eco-store` → `getEditorThemeConfigs` ou
   `eco-template` → `getEditorStoreSettings`.
3. Encontre os parâmetros pelo ID, tipo e rótulo nas instruções. Percorra
   `PAGE`, `SECTION`, `GROUP` por `items` e `TAB` por `tabs[].items`.
   Use a área apropriada: `default`, `top`, `footer`, `copyright`, `product_list`,
   `product_detail`, `text_page`, `home_sections`, `product_detail_sections` ou
   `footer_sections`, conforme a resposta real. Esses grupos organizam a UI;
   não se tornam automaticamente objetos aninhados no payload.
4. Para cores/fontes globais, altere os IDs encontrados no mapa do tema. Para uma
   seção, altere seu `configs`. Cabeçalho e configurações gerais usam o mapa do
   tema; não crie seções `TOP` ou `DEFAULT` por analogia com nomes da interface.

Se o retorno tiver `version: 1` ou não trouxer os metadados esperados, não aplique
a estrutura V2 por suposição. Use somente opções que o contrato e os dados
permitam identificar.

As instruções de checkout são lidas com `eco-store` → `getCheckoutSettings`.
Seus valores são separados: `getEditorCheckoutConfigs`/`replaceEditorCheckoutConfigs`
no `eco-store`, ou `getEditorCheckoutSettings`/`saveEditorCheckoutSettings` no
`eco-template`. Salvar configurações da loja não substitui as de checkout.

## Tipos dos valores

O editor mantém valores escalares principalmente como strings e preserva objetos
e arrays. Não transforme todo valor em booleano/número só pela aparência do campo.

| Controle | Formato do valor |
| --- | --- |
| `COLOR` | String da cor, no formato aceito pelo parâmetro/valor existente. |
| `TEXT`, `TEXTAREA`, `LINK` | String; respeitar tamanho, formato e conteúdo HTML quando declarado. |
| `SELECT` customizado | ID da opção como string, não seu rótulo. |
| `SELECT` de menu/newsletter/atributo | ID real do recurso; multisseleção mantém array quando aplicável. |
| `SWITCH` | Strings `"true"` e `"false"`. |
| `CHECKBOX` | A UI salva `"true"` quando marcado e remove a chave ao desmarcar. |
| `RANGE` | Valor como string, respeitando `min`, `max`, `step` e unidade. |
| `FONTSTYLE` | Objeto com propriedades como `fontSize`, `letterSpacing`, `fontWeight`, `textDecoration`, `textTransform`; preservar as não alteradas. |
| `UPLOAD` | URL devolvida pelo armazenamento, seguindo dimensões/restrições do parâmetro. |

`showWhen` compara um campo controlador com `equals` e possível `defaultValue`.
`disabledWhen` restringe alguns ranges. Antes de mudar um campo invisível ou
inativo, entenda a dependência; não habilite outra funcionalidade sem necessidade
para o pedido. Distingua ausência, string vazia, `"false"` e `false`.

Para novos recursos, use os defaults declarados no grupo/tipo correspondente,
preservando defaults explícitos falsos/zero quando o contrato os aceitar. Não
aplique todos os defaults do template sobre a personalização existente. Na UI,
limpar um campo remove sua chave; para reproduzir isso via PUT, remova apenas
essa chave do mapa lido. O PATCH clássico combina o primeiro nível e não deve
ser usado como se apagasse chaves omitidas.

`replaceEditorThemeConfigs` e `saveEditorStoreSettings` substituem o mapa. Leia,
altere apenas os campos pedidos e envie o mapa preservado. O PATCH
`patchEditorThemeConfigs` existe para o clássico; não presume compatibilidade com
o atual nem combinação profunda de objetos. Use a mesma cautela no checkout.

## Seções por página e grupo

| Destino | `pageType` | `group` | Complemento |
| --- | --- | --- | --- |
| Conteúdo da home | `HOME` | `MAIN` | — |
| Detalhes do produto | `PRODUCT_DETAIL` | `MAIN` | Preservar a estrutura obrigatória do template. |
| Rodapé compartilhado | `ALL` | `FOOTER` | Seções `BLOCK` no rodapé por blocos. |
| Landing page | `LANDING_PAGE` | `MAIN` | `query.landingPageId` obrigatório na gravação. |

Use os enums exatos do `inputSchema`, inclusive sua capitalização.

No `eco-store`, prefira `getEditorSectionsGroup`/`saveEditorSectionsGroup` para
deixar o escopo explícito. No `eco-template`, use
`getEditorSections`/`saveEditorSections`. Os caminhos abreviados do clássico
assumem home/main, portanto não os use para editar outro destino.

### Criar, configurar, reordenar, ocultar ou excluir

1. Leia a lista completa do grupo e encontre a seção por ID ou características
   inequívocas. A posição no array determina a ordem.
2. Construa a nova lista preservando as outras seções. Uma criação adiciona
   `{ type, active: true, configs }` sem ID inventado; o tipo deve existir no
   template e ser aceito pelo contrato para essa página.
3. Para mover, altere somente a posição. Para ocultar, altere `active`, preservando
   configurações e conteúdo. Para remover, omita apenas o alvo autorizado.
4. Projete o corpo nos campos graváveis `id`, `type`, `active`, `configs`, conforme
   o schema. O GET pode trazer itens expandidos; não os reenviar na escrita das
   seções. No clássico, se editar `active`, `type` ou `configs` de um item,
   envie o trio completo. No atual, `type` é obrigatório.
5. Salve a lista e obtenha o ID criado. Só então grave banners, produtos, vídeos,
   colunas ou outros itens na operação específica da seção. Releia a estrutura e
   os conteúdos alterados. O PUT atual de seções pode retornar apenas dados
   estruturais, sem arrays de conteúdo; isso não comprova remoção desses itens.
6. Prepare o preview na rota correspondente, com a âncora da seção quando útil,
   para incluí-lo somente na resposta final.

Não substitua a lista inteira apenas pelo item alterado. Uma lista vazia remove
todo o grupo. Para uma operação com criação de seção e conteúdo, verifique de
antemão que dispõe dos dados e operações necessários; se a segunda etapa falhar,
informe a seção já criada, sem repetir a criação. Inclua seu preview na resposta final.

No clássico, `PRODUCT_DETAIL/MAIN` exige `INFOS`, `DESCRIPTION`, `REVIEWS`,
`SPECIFICATIONS`, `SHOWCASE_SIMILAR` e `MEASUREMENT_GUIDE`, sem repetição de tipo.
Não aplique a regra de adicionar/remover livremente da home a esse grupo. Para
landing pages de produto/listagem, preserve as seções estruturais retornadas
(`PRODUCT`/`PRODUCT_LIST`) e siga as instruções do template.

### Seção HTML personalizada

Use a seção `HTML` para implementar personalizações que não tenham uma opção
pronta nas configurações do template, como faixas promocionais, grades de
benefícios, blocos editoriais, tabelas e perguntas frequentes. Ela permite
complementar os componentes nativos com código personalizado, salvo pelo MCP
no `configs` da própria seção. Não depende de uma operação de itens separada.

1. Consulte `getTemplateSettings` e confirme que `HTML` está disponível para a
   página e o grupo desejados. Localize o parâmetro de código nas instruções do
   tipo; ele pode ser um `TEXTAREA` com `typeConfigs.type = "html"`. Use seu `id`
   como chave em `configs`, sem presumir que se chama `html` em todo template.
2. Leia a lista completa de seções. Edite a seção HTML escolhida ou acrescente
   `{ type: "HTML", active: true, configs }`, sem inventar um ID. Guarde o código
   como string no parâmetro identificado, preservando as outras configurações
   e os defaults aplicáveis. Insira o bloco na posição correspondente ao pedido.
3. Escreva um fragmento HTML para a seção, sem substituir o documento da página.
   Para estilos personalizados, limite os seletores a uma classe ou ID próprio
   do bloco e confira o suporte a CSS no preview. Se forem necessárias interações
   com JavaScript, confirme que o template permite sua execução; um campo HTML
   e uma gravação aceita, por si só, não comprovam esse suporte. Mantenha o bloco
   acessível e adaptável a desktop e mobile.
4. Salve a lista preservada com `saveEditorSectionsGroup` no mecanismo clássico
   ou `saveEditorSections` no atual, conforme o contrato. Releia o recurso e
   confira o bloco no preview, incluindo estilos, links, interações solicitadas
   e preservação das demais seções. Inclua o link somente na resposta final;
   publicação continua sendo uma ação separada.

HTML personaliza a apresentação e as interações suportadas no navegador. Quando
o pedido depender de dados ou operações de negócio, use as operações disponíveis
no MCP; o bloco não concede acesso a APIs indisponíveis nem substitui dados reais.
Se o destino não oferecer o tipo HTML, explique essa limitação específica.

### Rodapé em blocos

Confira a tecnologia/versão do rodapé antes de escolher o fluxo. No rodapé por
blocos, leia `ALL/FOOTER`, crie ou selecione `BLOCK` e use
`getEditorBlockColumns`/`saveEditorBlockColumns` no serviço aplicável.
Os tipos de coluna e seus parâmetros vêm de
`footer_sections[].typeConfigs.columns`; uma coluna tem `type` e `configs`.
Se o template oferecer uma coluna `HTML`, use-a para conteúdo personalizado no
rodapé e grave o código no parâmetro indicado para essa coluna, mantendo a seção
`BLOCK` e as demais colunas.
Leia e preserve todas as colunas ao gravar. No contrato atual, ambos os campos
são obrigatórios. Copyright usa seu grupo de parâmetros, não uma coluna
inventada. O editor oferece até quatro blocos e seis colunas por bloco;
confira os limites atuais antes de expandir além desse comportamento.

Tipos de conteúdo e suas operações estão em [menus e conteúdo](menus-e-conteudo.md).
