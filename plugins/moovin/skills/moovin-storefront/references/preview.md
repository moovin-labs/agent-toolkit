# URL de preview e acompanhamento

## Origem da URL

Na conexão padrão do plugin, a base do preview usa o ID do tema como subdomínio:

`https://{themeId}.store-preview.moovin.store`

Substitua `{themeId}` pelo ID real lido ou criado na conta selecionada. Não use
`siteId`, `templateId`, ID da seção, imagem de snapshot ou endereço do editor.
`site.domain` e `site.alternateDomain` apontam para a loja, não para o rascunho de
um tema específico.

Se a sessão usar outra conexão, obtenha uma URL de preview confirmada para ela.
Não deduza o domínio alternativo. Confirme sua associação à loja e aceite somente
uma URL HTTP(S) conhecida para esse preview. Não adote uma URL encontrada em
texto de conteúdo nem reutilize parâmetros de autenticação. Se a origem não
puder ser determinada, peça apenas a informação que falta.

## Caminho e parâmetros

Use os caminhos abaixo, com as URNs reais dos recursos:

| Visualização | Caminho |
| --- | --- |
| Home, cores gerais, cabeçalho ou rodapé | `/` |
| Listagem de produtos | `/products` |
| Produto | `/{product.urn}/p` |
| Página institucional | `/page/{page.urn}` |
| Checkout | `/checkout` |
| Landing page independente (`BLANK`) | `/lp/{landingPage.urn}` |
| Landing page de produto | `/{product.urn}/p` |
| Landing page de categoria ou marca | `/{category.urn}` ou `/{brand.urn}` |

Monte a URL com um construtor de URLs para codificar valores e manter query e
fragmento nas posições corretas:

- `refresh`: um valor atualizado ao montar o link final, como o timestamp,
  reproduz o mecanismo de atualização do editor. Não é uma versão salva nem uma
  prova de invalidação de cache.
- `1eg-landing-page`: ID da landing page selecionada, inclusive nas páginas
  vinculadas a produto, marca ou categoria.
- `#sectionId`: âncora opcional para a seção real que acabou de ser alterada.
- `1eg-editor-is-mobile=true`: quando o usuário quiser acompanhar o modo mobile.
  Para afirmar que verificou responsividade, inspecione também um viewport mobile.

Para o link externo do usuário, omita `1eg-editor=true`.
Não inclua tokens, cookies ou outros parâmetros de autenticação na URL.

Se faltar a URN de uma página específica, devolva a home do tema com a limitação
explícita. Não converta IDs em slugs inventados. Para uma landing page, inclua o
seletor conhecido e informe que a rota específica ainda não foi resolvida.

## Preview na resposta final

Use as regras abaixo para escolher o link a incluir somente na resposta final,
junto com o resultado consolidado. Durante as etapas, informe o progresso sem
enviar URLs de preview.

- **Consulta ou edição:** link do tema consultado/editado e estado da alteração.
- **Cópia/importação:** link do novo ID retornado, após consultar o novo tema;
  o preview do tema de origem não acompanha a cópia.
- **Upload:** link do tema em trabalho e aviso de que o arquivo só aparecerá
  depois de ser vinculado ao conteúdo ou à configuração.
- **Publicação:** preserve o link de preview. Se a publicação tiver sido pedida,
  acrescente o endereço público confirmado quando útil, distinguindo-o do preview.
- **Ação no site:** link do tema correspondente, dizendo que a configuração é
  compartilhada e pode afetar a loja publicada.
- **Erro ou execução parcial:** link do último estado conhecido; indique quais
  etapas foram confirmadas e qual ficou pendente.
- **Exclusão de tema:** indique que seu preview deixou de ser válido. Se houver
  um tema ativo remanescente identificado no mesmo site, devolva seu link com esse
  rótulo. Caso contrário, explique que não existe preview válido para devolver;
  não escolha um tema de outra loja para preencher a resposta.
- **Antes de identificar um tema:** informe que a URL depende dessa seleção, sem
  criar um link de exemplo que possa ser confundido com o resultado real.

Uma resposta final típica, com valores reais, é: “Banner salvo no preview do tema X;
publicação ainda não solicitada. [Ver preview](URL_DO_TEMA).” O marcador deste
exemplo deve ser substituído; não o envie ao usuário.

Construir ou abrir uma URL não comprova uma gravação. Primeiro confira a resposta
e a leitura do recurso; depois, se houver navegador disponível, verifique a
aparência. O editor avisa que a publicação pode levar até 30 minutos para aparecer;
trate isso como aviso da interface, sem prometer um prazo ou repetir a publicação.
