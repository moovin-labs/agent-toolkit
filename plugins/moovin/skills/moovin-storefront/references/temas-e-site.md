# Temas, tecnologia e configurações do site

## Modelo de recursos

- **Site:** identidade, domínios, configurações compartilhadas e, no mecanismo
  clássico, associação de temas por dispositivo.
- **Template:** definição das opções que o editor pode apresentar. Suas instruções
  não são os valores preenchidos pelo lojista.
- **Tema:** personalização de um template, com conteúdo de edição e publicado.
  Copiar um tema produz outro tema; ativá-lo seleciona o que a loja usa.
- **Menu:** recurso da conta referenciado por configurações de temas/colunas.
  Alterá-lo pode afetar outros temas que usam o mesmo menu.

`eco-store` oferece formatos clássicos e atuais como alternativas. Identifique
o mecanismo pelos contratos e recursos retornados, sem deduzi-lo apenas pelo
nome do serviço.

Comece por `eco-store` → `listSites`, `getSite`, `listThemes` e `getTheme`. Confira
os vínculos do site e o formato efetivamente retornado. No mecanismo atual,
`eco-template` → `getActiveTheme`, `getTheme` e `getMigrationStatus` ajudam a
identificar versões e estado. O retorno atual de `getTheme` documenta
`editorVersion` e `publishedVersion`, enquanto o clássico traz a referência de
`template` e a associação de `site`; essas diferenças são evidência mais útil
do que o formato do ID. `COMPLETED` também pode ser retornado porque já
existe tema ativo, sem registro de migração. `PENDING` sozinho não autoriza migrar.
Se a evidência não distinguir os mecanismos e isso mudar o payload, esclareça
antes da gravação.

No clássico, identifique o tema ativo pelo vínculo ao dispositivo do site.
No atual, confira o campo `active`. Respeite vínculos DESKTOP/MOBILE existentes em vez
de supor que todas as lojas usam BOTH. `settings.version = 2` descreve o formato
das instruções do editor e também existe no caminho clássico.

## Operações de tema

Os nomes abaixo são pistas de descoberta, sempre acompanhadas de `get_operation`.

| Intenção | Operações e tratamento |
| --- | --- |
| Listar biblioteca e temas próprios | `listPublicThemes`, `listThemes`, `getTheme`; disponíveis nos dois serviços. Paginar conforme o contrato. |
| Adicionar tema da biblioteca ou duplicar | `copyTheme` com o ID de origem e `body.name`. Consulte o novo ID retornado. |
| Renomear | `updateTheme`, enviando apenas `name` e preservando os demais campos. |
| Criar a partir de um template clássico | `eco-store` → `createTheme`, somente quando essa for a intenção; não confundir com o botão “Adicionar” da biblioteca. |
| Ativar no clássico | `eco-store` → `setSiteDeviceTheme`, com ID do site, dispositivo autorizado e `body.theme.id`. |
| Ativar no atual | `eco-template` → `activateTheme`, com ID do tema. |
| Publicar edição | `publishTheme` no serviço aplicável, com ID do tema. Não ativa automaticamente outro tema. |
| Excluir | `deleteTheme`, com alvo resolvido. A UI não oferece excluir o tema ativo; não troque o tema ativo como efeito implícito de uma exclusão. |

**Cópia:** não ative `fullCopy` por parecer uma opção mais completa. No clássico,
esse campo também copia menus ao importar um tema público. No atual, é alias de
`isSetup`, que pode limpar vitrines; cópias públicas também removem links de
banners e produtos/links de stories. Para uma cópia comum, envie somente `{ name }`,
salvo pedido específico com as consequências esclarecidas.
O retorno pode ser apenas `{ id }`; não exija o mesmo envelope nos dois serviços.
A UI limita adicionar/duplicar a cinco temas; confira a quantidade e o contrato
atual antes de criar mais, sem apagar temas para liberar espaço automaticamente.

**Publicação:** verifique o escopo completo. No clássico, `publishTheme` publica
configurações, checkout, seções, itens e menus pendentes da conta. Não prometa
publicar apenas a cor ou seção recém-alterada. Se houver outros rascunhos fora do
pedido, apresente o alcance concreto antes de publicar, a menos que ele já esteja
autorizado. No atual, publicação de tema e `publishMenu` são operações distintas;
consulte o contrato e confira os menus alterados/referenciados. Só execute etapas
adicionais necessárias e incluídas na autorização de publicação.

## Configurações compartilhadas da loja

`eco-store` → `getSiteConfigs` lê o mapa. Use `patchSiteConfigs` para
alterações parciais; `replaceSiteConfigs` é substituição total e não é equivalente.
O PATCH combina as seções/subobjetos documentados, mas arrays substituem os
anteriores. Confira novamente os campos modificados após salvar.

| Configuração | Campo |
| --- | --- |
| Logo principal e favicon | `store.topLogo`, `store.favicon` |
| Identidade, contato, horários e endereço | `store.name`, `corporationName`, `email`, telefones, `openingHours`, `address` |
| Banner de login | `store.loginBanner` |
| Redes sociais | `social` |
| Manutenção e modo catálogo | `store.maintenance`, `catalogMode`, `storeUsageType` |
| Robots | `robots` |
| Códigos de medição e verificação | `scripts`, `ownershipVerification` |
| Comportamento do checkout | `checkout`; diferente da aparência em `theme/{id}/preview/checkout-configs` |

Esses campos não são um rascunho do tema. Trocar o logo pela configuração do site
pode afetar todos os temas/loja publicada. Se o template tiver um campo próprio de
imagem, escolha o local coerente com o pedido e com a leitura atual, sem gravar
nos dois indiscriminadamente. Não exponha configurações privadas de login ao
relatar outras mudanças.

Para nome, domínio e subdomínio, use `getSite`/`updateSite`; leia a verificação DNS
retornada. Alterar o cadastro não configura DNS nem prova disponibilidade do
domínio. Scripts personalizados usam `listScripts`, `getScript`, `createScript`,
`updateScript`, `deleteScript`; seus campos de página, posição, método de carga e
estado dependem do schema. Não use scripts como atalho para uma opção de layout
ausente sem que isso faça parte do pedido.

## Migração e versão de componentes

Migrar a tecnologia da conta usa `eco-template` → `activateNewTemplate` e
`getMigrationStatus`. A atualização é definitiva. Execute
apenas quando a migração tiver sido solicitada com esse alcance entendido; ela
não é pré-requisito automático para personalizar a loja. A aceitação HTTP 204
não significa migração concluída. Acompanhe `PENDING`, `IN_PROGRESS`, `COMPLETED`
ou `FAILED`, sem reenviar a ativação para acelerar o processo.

O rodapé clássico também possui controle de versão próprio:
`eco-store` → `getThemeVersionControl`/`saveThemeVersionControl`, identificador
`FOOTER`, corpo `{ "version": 2 }` para o fluxo de atualização mostrado na UI.
Isso não é histórico de desfazer nem migração completa da conta. Leia a versão
antes e só altere se o pedido abranger a conversão do rodapé. Não prometa rollback
automático sem uma operação disponível que permita restaurar o estado anterior.
