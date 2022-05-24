---
title: Lista de verificação de segurança do Dispatcher
seo-title: The Dispatcher Security Checklist
description: Uma lista de verificação de segurança que deve ser concluída antes de entrar em produção.
seo-description: A security checklist that should be completed before going on production.
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
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: a21b527700b3a5c3a11eadaefc729e754ba2b37b
workflow-type: tm+mt
source-wordcount: '638'
ht-degree: 98%

---

# Lista de verificação de segurança do Dispatcher {#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

A Adobe recomenda concluir a seguinte lista de verificação antes de continuar a produção.

>[!CAUTION]
>
>Você também deve concluir a Lista de verificação de segurança da sua versão do AEM antes de entrar em atividade. Consulte a [documentação do Adobe Experience Manager](https://helpx.adobe.com/experience-manager/6-5/sites/administering/using/security-checklist.html) correspondente.

## Usar a versão mais recente do Dispatcher {#use-the-latest-version-of-dispatcher}

Você deve instalar a versão mais recente disponível para a sua plataforma. Atualize a instância do Dispatcher para usar a versão mais recente e assim aproveitar os aprimoramentos de produto e segurança. Consulte [Instalação do Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Você pode verificar a versão atual de instalação do Dispatcher observando o arquivo de log do dispatcher.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Para localizar o arquivo de log, inspecione a configuração do dispatcher no `httpd.conf`.

## Restringir clientes que podem liberar seu cache {#restrict-clients-that-can-flush-your-cache}

A Adobe recomenda que você [limite os clientes que possam liberar seu cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Ativar HTTPS para segurança da camada de transporte {#enable-https-for-transport-layer-security}

A Adobe recomenda ativar a camada de transporte HTTPS nas instâncias de autor e publicação.

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

Ao configurar o Dispatcher, você deve restringir o acesso externo o máximo possível. Consulte [Seção de Exemplo /filtro](dispatcher-configuration.md#main-pars_184_1_title) na documentação do Dispatcher.

## Verificar se o acesso a URLs administrativos é negado {#make-sure-access-to-administrative-urls-is-denied}

Use filtros para bloquear o acesso externo a URLs administrativos, como o Console da Web.

Consulte [Teste de segurança do Dispatcher](dispatcher-configuration.md#testing-dispatcher-security) para obter uma lista de URLs que precisam ser bloqueados.

## Uso de listas de permissões em vez de lista de bloqueios {#use-allowlists-instead-of-blocklists}

Listas de permissões são uma maneira melhor de fornecer controle de acesso, pois, por natureza, elas pressupõem que todas as solicitações de acesso devem ser negadas, a menos que façam parte explicitamente da lista de permissões. Esse modelo oferece controle mais restritivo sobre novas solicitações que podem ainda não ter sido revisadas ou consideradas durante uma determinada etapa de configuração.

## Execução do Dispatcher com um usuário do sistema dedicado {#run-dispatcher-with-a-dedicated-system-user}

Ao configurar o Dispatcher, você deve garantir que o servidor Web seja executado por um usuário dedicado com menos privilégios. É recomendável conceder acesso de gravação somente à pasta de cache do Dispatcher.

Além disso, os usuários do IIS precisam configurar seu site da seguinte maneira:

1. Na configuração do caminho físico para o seu site, selecione **Conectar como usuário específico**.
1. Defina o usuário.

## Evitar ataques de negação de serviço (DoS) {#prevent-denial-of-service-dos-attacks}

Um ataque de negação de serviço (DoS) é uma tentativa de tornar um recurso de computador indisponível para os usuários desejados.

No nível do dispatcher, há dois métodos de configuração para evitar ataques de DoS: [](https://experienceleague.adobe.com/docs/?lang=pt-BR#/filter (Filtros))

* Use o módulo mod_rewrite (por exemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) para executar validações de URL (se as regras do padrão de URL não forem muito complexas).

* Evite que o dispatcher armazene URLs em cache com extensões falsas usando [filtros](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por exemplo, altere as regras de armazenamento em cache para limitá-lo aos tipos MIME esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

   Um exemplo de arquivo de configuração pode ser visto para [restringir o acesso externo](#restrict-access), isso inclui restrições para tipos MIME.

Para ativar com segurança a funcionalidade completa nas instâncias de publicação, configure filtros para impedir o acesso aos seguintes nós:

* `/etc/`
* `/libs/`

Em seguida, configure os filtros para permitir o acesso aos seguintes caminhos de nó:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS e JSON)
* `/libs/cq/security/userinfo.json` (Informações ao usuário do CQ)
* `/libs/granite/security/currentuser.json` (**os dados não devem ser armazenados em cache**)

* `/libs/cq/i18n/*` (Internalização)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Configuração do Dispatcher para evitar ataques CSRF {#configure-dispatcher-to-prevent-csrf-attacks}

O AEM fornece uma [estrutura](https://experienceleague.adobe.com/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions.html?lang=pt-BR) destinada a impedir ataques de falsificação de solicitação entre sites. Para fazer uso correto dessa estrutura, é necessário incluir na lista de permissões o suporte ao token CSRF no dispatcher. Você pode fazer isso ao:

1. criar um filtro para permitir o caminho `/libs/granite/csrf/token.json`;
1. adicionar o cabeçalho `CSRF-Token` à seção `clientheaders` da configuração do Dispatcher.

## Prevenção contra clickjacking {#prevent-clickjacking}

Para evitar clickjacking, recomendamos que você configure seu servidor Web para fornecer o cabeçalho HTTP `X-FRAME-OPTIONS` definido como `SAMEORIGIN`.

Para obter mais [informações sobre clickjacking, consulte o site OWASP](https://www.owasp.org/index.php/Clickjacking).

## Realização de teste de penetração {#perform-a-penetration-test}

A Adobe recomenda realizar um teste de penetração na infraestrutura do seu AEM antes de continuar a produção.
