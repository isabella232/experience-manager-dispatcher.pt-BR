---
title: Configuração do Dispatcher para Evitar Ataques CSRF
seo-title: Configuração do Adobe AEM Dispatcher para Evitar Ataques CSRF
description: Saiba como configurar AEM Dispatcher para impedir ataques de Permissão de Solicitação entre Sites.
seo-description: Saiba como configurar o Adobe AEM Dispatcher para impedir ataques de Permissão de Solicitação em Sites Cruzados.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4
workflow-type: tm+mt
source-wordcount: '246'
ht-degree: 4%

---


# Configuração do Dispatcher para Evitar Ataques CSRF{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM fornece uma estrutura destinada a impedir ataques de falsificação de solicitações entre sites. Para utilizar corretamente esta estrutura, é necessário fazer as seguintes alterações na configuração do seu dispatcher:

>[!NOTE]
>
>Certifique-se de atualizar os números de regras nos exemplos abaixo com base na sua configuração existente. Lembre-se de que os despachantes usarão a última regra de correspondência para conceder uma permissão ou negar, portanto, coloque as regras perto da parte inferior da sua lista existente.

1. Na seção `/clientheaders` de author-farm.any e publish-farm.any, adicione a seguinte entrada à parte inferior da lista:\
   `CSRF-Token`
1. Na seção /filtros do arquivo `author-farm.any` e `publish-farm.any` ou `publish-filters.any`, adicione a seguinte linha para permitir solicitações de `/libs/granite/csrf/token.json` pelo dispatcher.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Na seção `/cache /rules` de seu `publish-farm.any`, adicione uma regra para impedir que o dispatcher armazene o arquivo `token.json` em cache. Geralmente, os autores ignoram o cache; portanto, não é necessário adicionar a regra ao `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Para validar se a configuração está funcionando, observe o dispatcher.log no modo DEBUG para validar se o arquivo token.json não está sendo armazenado em cache e se não está sendo bloqueado por filtros. Você deve ver mensagens semelhantes a:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Você também pode validar se as solicitações foram bem-sucedidas em seu apache `access_log`. As solicitações para &quot;/libs/granite/csrf/token.json devem retornar um código de status HTTP 200.
