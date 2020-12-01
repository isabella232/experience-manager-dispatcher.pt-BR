---
title: Lista de verificação de segurança do Dispatcher
seo-title: Lista de verificação de segurança do Dispatcher
description: Uma lista de verificação de segurança que deve ser concluída antes de iniciar a produção.
seo-description: Uma lista de verificação de segurança que deve ser concluída antes de iniciar a produção.
uuid: 7bfa3202-03f6-48e9-8d2e-2a40e137ecbe
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: fbfafa55-c029-4ed7-ab3e-1bebfde18248
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 7889c025fb8fb29e6f11ea01c5248470556d3160
workflow-type: tm+mt
source-wordcount: '653'
ht-degree: 1%

---


# Lista de verificação de segurança do Dispatcher{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

A Adobe recomenda que você preencha a seguinte lista de verificação antes de continuar a produção.

>[!CAUTION]
>
>Você também deve preencher a Lista de verificação de segurança da sua versão do AEM antes de entrar em funcionamento. Consulte a documentação correspondente [Adobe Experience Manager](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Usar a versão mais recente do Dispatcher {#use-the-latest-version-of-dispatcher}

Instale a versão mais recente disponível para a sua plataforma. Você deve atualizar sua instância do Dispatcher para usar a versão mais recente para aproveitar as vantagens dos aprimoramentos de produtos e de segurança. Consulte [Instalando o Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Você pode verificar a versão atual da instalação do dispatcher observando o arquivo de log do dispatcher.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Para localizar o arquivo de log, inspecione a configuração do dispatcher em seu `httpd.conf`.

## Restringir clientes que podem liberar seu cache {#restrict-clients-that-can-flush-your-cache}

O Adobe recomenda que você [limite os clientes que podem liberar seu cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Habilitar HTTPS para segurança de camada de transporte {#enable-https-for-transport-layer-security}

O Adobe recomenda ativar a camada de transporte HTTPS nas instâncias de autor e publicação.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Restringir acesso {#restrict-access}

Ao configurar o Dispatcher, você deve restringir o acesso externo o máximo possível. Consulte [Exemplo /filter Section](dispatcher-configuration.md#main-pars_184_1_title) na documentação do Dispatcher.

## Verifique se o acesso aos URLs administrativos foi negado {#make-sure-access-to-administrative-urls-is-denied}

Certifique-se de usar filtros para bloquear o acesso externo a quaisquer URLs administrativos, como o Console da Web.

Consulte [Testando o Dispatcher Security](dispatcher-configuration.md#testing-dispatcher-security) para obter uma lista de URLs que precisam ser bloqueados.

## Usar Lista de permissões em vez de Listas de bloqueios {#use-allowlists-instead-of-blocklists}

As Lista de permissões são uma maneira melhor de fornecer controle de acesso, pois, por natureza, assumem que todas as solicitações de acesso devem ser negadas, a menos que sejam explicitamente parte da lista de permissões. Este modelo proporciona um controle mais restritivo sobre novas solicitações que podem não ter sido ainda revisadas ou consideradas durante uma determinada fase de configuração.

## Execute o Dispatcher com um Usuário Dedicado do Sistema {#run-dispatcher-with-a-dedicated-system-user}

Ao configurar o Dispatcher, você deve garantir que o servidor da Web seja executado por um usuário dedicado com menos privilégios. É recomendável conceder acesso de gravação somente à pasta de cache do dispatcher.

Além disso, os usuários do IIS precisam configurar seu site da seguinte maneira:

1. Na configuração do caminho físico do site, selecione **Conectar como usuário específico**.
1. Defina o usuário.

## Impedir ataques de negação de serviço (DoS) {#prevent-denial-of-service-dos-attacks}

Um ataque de negação de serviço (DoS) é uma tentativa de tornar um recurso de computador indisponível para os usuários pretendidos.

No nível do dispatcher, há dois métodos de configuração para evitar ataques de DoS: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filtros))

* Use o módulo mod_rewrite (por exemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) para executar validações de URL (se as regras de padrão de URL não forem muito complexas).

* Evite que o dispatcher armazene URLs em cache com extensões espúrias usando [filtros](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por exemplo, altere as regras de cache para limitar o armazenamento em cache aos tipos MIME esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

   Um arquivo de configuração de exemplo pode ser visto para [restringir o acesso externo](#restrict-access), isso inclui restrições para tipos MIME.

Para habilitar com segurança a funcionalidade completa nas instâncias de publicação, configure os filtros para impedir o acesso aos seguintes nós:

* `/etc/`
* `/libs/`

Em seguida, configure os filtros para permitir o acesso aos seguintes caminhos de nó:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS e JSON)
* `/libs/cq/security/userinfo.json` (Informações do usuário do CQ)
* `/libs/granite/security/currentuser.json` (**os dados não devem ser armazenados em cache**)

* `/libs/cq/i18n/*` (Internalização)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Configure o Dispatcher para impedir ataques CSRF {#configure-dispatcher-to-prevent-csrf-attacks}

AEM fornece uma [estrutura](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) destinada a impedir ataques de Permissão de Solicitação entre Sites. Para utilizar adequadamente essa estrutura, é necessário lista de permissões o suporte a token CSRF no dispatcher. Você pode fazer isso:

1. Criando um filtro para permitir o caminho `/libs/granite/csrf/token.json`;
1. Adicione o cabeçalho `CSRF-Token` à seção `clientheaders` da configuração do Dispatcher.

## Impedir que os cliques sejam ativados {#prevent-clickjacking}

Para evitar o clickjacking, recomendamos que você configure seu servidor Web para fornecer o cabeçalho HTTP `X-FRAME-OPTIONS` definido como `SAMEORIGIN`.

Para obter mais [informações sobre clickjacking, consulte o site da OWASP](https://www.owasp.org/index.php/Clickjacking).

## Execute um teste de penetração {#perform-a-penetration-test}

A Adobe recomenda que você realize um teste de penetração de sua infraestrutura AEM antes de entrar na produção.

