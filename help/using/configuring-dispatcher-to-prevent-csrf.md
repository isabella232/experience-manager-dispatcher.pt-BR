---
title: Configuração do Dispatcher para impedir ataques CSRF
seo-title: Configuração do Adobe AEM Dispatcher para impedir ataques CSRF
description: Saiba como configurar o AEM Dispatcher para impedir ataques de Falsificação de solicitação entre sites.
seo-description: Saiba como configurar o Adobe AEM Dispatcher para impedir ataques de Falsificação de solicitação entre sites.
uuid: f 290 bdeb -54 e 2-4649-b 0 fc -6257 b 422 af 2 d
topic-tags: dispatcher
content-type: referência
discoiquuid: d 61 d 021 e-b 338-4 a 1 d -91 ee -55427557 e 931
translation-type: tm+mt
source-git-commit: 69edbe7608b46c93d238515e4223606eadad0ac4

---


# Configuração do Dispatcher para impedir ataques CSRF{#configuring-dispatcher-to-prevent-csrf-attacks}

O AEM fornece uma estrutura visando impedir ataques de Falsificação de solicitação entre sites. Para usar corretamente essa estrutura, você precisa fazer as seguintes alterações na configuração do seu dispatcher:

>[!NOTE]
>
>Certifique-se de atualizar os números de regras nos exemplos abaixo com base na configuração existente. Lembre-se de que os dispatchers usarão a última regra correspondente para conceder um permissão ou negar, portanto, coloque as regras perto da parte inferior da lista existente.

1. Na `/clientheaders` seção de seu autor-farm. any e publish-farm. any, adicione a seguinte entrada à parte inferior da lista:\
   `CSRF-Token`
1. Na seção /filters de seu `author-farm.any` e `publish-farm.any` ou `publish-filters.any` arquivo, adicione a seguinte linha para permitir solicitações `/libs/granite/csrf/token.json` por meio do expedidor.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Na `/cache /rules` seção de seu `publish-farm.any`, adicione uma regra para bloquear o dispatcher a bloquear o `token.json` arquivo. Normalmente, os autores ignoram o armazenamento em cache, portanto, não é necessário adicionar a regra em seu `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Para validar se a configuração está funcionando, assista ao dispatcher. log no modo DEBUG para validar se o arquivo token. json não está sendo armazenado em cache e não está sendo bloqueado por filtros. Você deve ver mensagens semelhantes a:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Você também pode validar que as solicitações estão com êxito no apache `access_log`. Solicitações de «/libs/granite/csrf/token. json devem retornar um código de status HTTP 200.
