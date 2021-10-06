---
title: 'Configuração do Dispatcher para evitar ataques CSRF '
seo-title: Configuring Adobe AEM Dispatcher to Prevent CSRF Attacks
description: Saiba como configurar o AEM Dispatcher para impedir ataques de falsificações de solicitações entre sites.
seo-description: Learn how to configure Adobe AEM Dispatcher to prevent Cross-Site Request Forgery attacks.
uuid: f290bdeb-54e2-4649-b0fc-6257b422af2d
topic-tags: dispatcher
content-type: reference
discoiquuid: d61d021e-b338-4a1d-91ee-55427557e931
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: ht
source-wordcount: '225'
ht-degree: 100%

---

# Configuração do Dispatcher para evitar ataques CSRF {#configuring-dispatcher-to-prevent-csrf-attacks}

O AEM fornece uma estrutura destinada a impedir ataques de falsificação de solicitação entre sites. Para usar adequadamente essa estrutura, é necessário fazer as seguintes alterações na configuração do Dispatcher:

>[!NOTE]
>
>Atualize os números de regras nos exemplos abaixo com base em sua configuração existente. Lembre-se de que os dispatchers usarão a última regra correspondente para conceder ou negar uma permissão. Portanto, coloque as regras perto da parte inferior da lista existente.

1. Na seção `/clientheaders` de author-farm.any e publish-farm.any, adicione a seguinte entrada à parte inferior da lista:\
   `CSRF-Token`
1. Na seção /filters, dos seus arquivos `author-farm.any` e `publish-farm.any` ou `publish-filters.any`, adicione a seguinte linha para permitir solicitações de `/libs/granite/csrf/token.json` por meio do dispatcher.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Na seção `/cache /rules` do `publish-farm.any`, adicione uma regra para impedir que o dispatcher armazene em cache o arquivo `token.json`. Geralmente, os autores ignoram o armazenamento em cache, de modo que não é necessário adicionar a regra ao `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Para confirmar que a configuração esteja funcionando, observe o dispatcher.log no modo DEBUG para validar que o arquivo token.json não esteja sendo armazenado em cache e não esteja sendo bloqueado por filtros. Você deve ver mensagens semelhantes a:\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Também é possível confirmar se as solicitações têm êxito no Apache `access_log`. As solicitações para ``/libs/granite/csrf/token.json devem retornar um código de status HTTP 200.
