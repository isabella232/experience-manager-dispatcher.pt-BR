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
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# A lista de verificação de segurança do Dispatcher{#the-dispatcher-security-checklist}

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
>Você também deve concluir a Lista de verificação de segurança da sua versão do AEM antes de entrar em funcionamento. Consulte a documentação correspondente [do Adobe Experience Manager](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html).

## Usar a versão mais recente do Dispatcher {#use-the-latest-version-of-dispatcher}

Você deve instalar a versão mais recente disponível disponível para a sua plataforma. Você deve atualizar a instância do Dispatcher para usar a versão mais recente para aproveitar os aprimoramentos de produto e segurança. Consulte [Instalação do Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Você pode verificar a versão atual da instalação do dispatcher observando o arquivo de log do expedidor.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Para encontrar o arquivo de log, inspecione a configuração do dispatcher em seu `httpd.conf`.

## Restringir clientes que podem separar o cache {#restrict-clients-that-can-flush-your-cache}

A Adobe recomenda [limitar os clientes que podem piscar o cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Ativar HTTPS para segurança de camada de transporte {#enable-https-for-transport-layer-security}

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

## Restringir acesso {#restrict-access}

Ao configurar o Dispatcher, você deve restringir o acesso externo sempre que possível. Consulte [Exemplo /filter Seção](dispatcher-configuration.md#main-pars_184_1_title) na documentação do Dispatcher.

## Verifique se o acesso a urls administrativos é negado {#make-sure-access-to-administrative-urls-is-denied}

Certifique-se de usar filtros para bloquear acesso externo a quaisquer urls administrativos, como o Console da Web.

Consulte [Testar o Dispatcher Security](dispatcher-configuration.md#testing-dispatcher-security) para obter uma lista de urls que precisam ser bloqueados.

## Usar permissões em vez de listas negras {#use-whitelists-instead-of-blacklists}

As permissões são uma maneira melhor de fornecer controle de acesso, uma vez inerentemente, elas assumem que todas as solicitações de acesso devem ser negadas, a menos que façam parte explícita da lista de permissões. Este modelo fornece controle mais restrito sobre novas solicitações que podem ainda não ter sido analisadas ou levadas em consideração durante um determinado estágio de configuração.

## Executar o Dispatcher com um usuário dedicado do sistema {#run-dispatcher-with-a-dedicated-system-user}

Ao configurar o Dispatcher, você deve garantir que o servidor da Web seja executado por um usuário dedicado com menos privilégios. Recomenda-se conceder somente a gravação à pasta do cache do dispatcher.

Additionnaly, os usuários do IIS precisam configurar seu site da seguinte maneira:

1. Na configuração de caminho físico para seu site, selecione **Connect como usuário específico**.
1. Defina o usuário.

## Ataques Impedir ataques de serviço (DoS) {#prevent-denial-of-service-dos-attacks}

Um ataque de negação de serviço (DoS) é uma tentativa de tornar um recurso de computador indisponível para os usuários pretendidos.

No nível do dispatcher, há dois métodos de configuração para impedir ataques do DOS: [](https://docs.adobe.com/content/docs/en/dispatcher.html#/filter (Filtros))

* Use o módulo mod_ rewrite (por exemplo, [Apache 2.2](https://httpd.apache.org/docs/2.2/mod/mod_rewrite.html)) para executar validações de URL (se as regras de padrão de URL não forem muito complexas).

* Impeça que o dispatcher armazene urls em cache com extensões desgastantes usando [filtros](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Por exemplo, altere as regras de cache para limitar o armazenamento em cache para os tipos mime esperados, como:

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`
   Um arquivo de configuração de exemplo pode ser visualizado para [restringir o acesso externo](#restrict-access), isso inclui restrições para tipos mime.

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

## Configurar o Dispatcher para impedir ataques CSRF {#configure-dispatcher-to-prevent-csrf-attacks}

O AEM fornece [uma estrutura](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps) visando impedir ataques de Falsificação de solicitação entre sites. Para usar corretamente essa estrutura, você precisa de suporte de token CSRF de lista de permissões no dispatcher. Você pode fazer isso:

1. Criação de um filtro para permitir `/libs/granite/csrf/token.json` o caminho;
1. Adicione o `CSRF-Token` cabeçalho à `clientheaders` seção da configuração do Dispatcher.

## Impedir clickjacking {#prevent-clickjacking}

Para evitar clickjacking, recomendamos que você configure seu servidor Web para fornecer o `X-FRAME-OPTIONS` cabeçalho HTTP definido `SAMEORIGIN`.

Para obter mais [informações sobre clickjacking, consulte o site OWASP](https://www.owasp.org/index.php/Clickjacking).

## Executar um teste de penetração {#perform-a-penetration-test}

A Adobe recomenda que você realize um teste de penetração da sua infraestrutura do AEM antes de começar a produção.

