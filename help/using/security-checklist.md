---
title: A lista de verificação de segurança do Dispatcher
seo-title: A lista de verificação de segurança do Dispatcher
description: Uma lista de verificação de segurança que deve ser concluída antes de começar a produção.
seo-description: Uma lista de verificação de segurança que deve ser concluída antes de começar a produção.
uuid: 7 bfa 3202-03 f 6-48 e 9-8 d 2 e -2 a 40 e 137 ecbe
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: fbfafa 55-c 029-4 ed 7-ab 3 e -1 bebfde 18248
jcr-lastmodifiedby: remove-legacypath -6-1
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# The Dispatcher Security Checklist{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

O dispatcher como um sistema de front-end oferece uma camada extra de segurança à sua infraestrutura do Adobe Experience Manager. A Adobe recomenda que você preencha a lista de verificação a seguir antes de começar a produção.

>[!CAUTION]
>
>Você também deve concluir a Lista de verificação de segurança da sua versão do AEM antes de entrar em funcionamento. Please refer to the corresponding [Adobe Experience Manager documentation](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Use the Latest Version of Dispatcher {#use-the-latest-version-of-dispatcher}

Você deve instalar a versão mais recente disponível disponível para a sua plataforma. Você deve atualizar a instância do Dispatcher para usar a versão mais recente para aproveitar os aprimoramentos de produto e segurança. See [Installing Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Você pode verificar a versão atual da instalação do dispatcher observando o arquivo de log do expedidor.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>To find the log file, inspect the dispatcher configuration in your `httpd.conf`.

## Restrict Clients that Can Flush Your Cache {#restrict-clients-that-can-flush-your-cache}

Adobe recommends that you [limit the clients that can flush your cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Enable HTTPS for transport layer security {#enable-https-for-transport-layer-security}

A Adobe recomenda ativar a camada de transporte HTTPS em instâncias de autor e publicação.

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

## Restrict Access {#restrict-access}

Ao configurar o Dispatcher, você deve restringir o acesso externo sempre que possível. See [Example /filter Section](dispatcher-configuration.md#main-pars_184_1_title) in the Dispatcher documentation.

## Make Sure Access to Administrative URLs is Denied {#make-sure-access-to-administrative-urls-is-denied}

Certifique-se de usar filtros para bloquear acesso externo a quaisquer urls administrativos, como o Console da Web.

See [Testing Dispatcher Security](dispatcher-configuration.md#testing-dispatcher-security) for a list of URLs that need to be blocked.

## Use Whitelists Instead Of Blacklists {#use-whitelists-instead-of-blacklists}

As permissões são uma maneira melhor de fornecer controle de acesso, uma vez inerentemente, elas assumem que todas as solicitações de acesso devem ser negadas, a menos que façam parte explícita da lista de permissões. Este modelo fornece controle mais restrito sobre novas solicitações que podem ainda não ter sido analisadas ou levadas em consideração durante um determinado estágio de configuração.

## Run Dispatcher with a Dedicated System User {#run-dispatcher-with-a-dedicated-system-user}

Ao configurar o Dispatcher, você deve garantir que o servidor da Web seja executado por um usuário dedicado com menos privilégios. Recomenda-se conceder somente a gravação à pasta do cache do dispatcher.

Additionnaly, os usuários do IIS precisam configurar seu site da seguinte maneira:

1. In the physical path setting for your web site, select **Connect as specific user**.
1. Defina o usuário.

## Prevent Denial of Service (DoS) Attacks {#prevent-denial-of-service-dos-attacks}

Um ataque de negação de serviço (DoS) é uma tentativa de tornar um recurso de computador indisponível para os usuários pretendidos.

At the dispatcher level, there are two methods of configuring to prevent DoS attacks: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filters))

* Use the mod_rewrite module (for example, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) to perform URL validations (if the URL pattern rules are not too complex).

* Prevent the dispatcher from caching URLs with spurious extensions by using [filters](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por exemplo, altere as regras de cache para limitar o armazenamento em cache para os tipos mime esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   An example configuration file can be seen for [restricting external access](#restrict-access), this includes restrictions for mime types.

Para ativar com segurança a funcionalidade completa nas instâncias de publicação, configure os filtros para impedir o acesso aos seguintes nós:

* `/etc/`
* `/libs/`

Em seguida, configure os filtros para permitir acesso aos seguintes caminhos de nó:

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

## Configure Dispatcher to prevent CSRF Attacks {#configure-dispatcher-to-prevent-csrf-attacks}

AEM provides a [framework](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) aimed at preventing Cross-Site Request Forgery attacks. Para usar corretamente essa estrutura, você precisa de suporte de token CSRF de lista de permissões no dispatcher. Você pode fazer isso:

1. Creating a filter to allow the `/libs/granite/csrf/token.json` path;
1. Add the `CSRF-Token` header to the `clientheaders` section of the Dispatcher configuration.

## Prevent Clickjacking {#prevent-clickjacking}

To prevent clickjacking we recommend that you configure your webserver to provide the `X-FRAME-OPTIONS` HTTP header set to `SAMEORIGIN`.

For more [information on clickjacking please see the OWASP site](https://www.owasp.org/index.php/Clickjacking).

## Perform a Penetration Test {#perform-a-penetration-test}

A Adobe recomenda que você realize um teste de penetração da sua infraestrutura do AEM antes de começar a produção.

