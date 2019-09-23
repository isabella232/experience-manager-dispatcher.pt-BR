---
title: Armazenamento em cache de conteúdo protegido
seo-title: Armazenamento em cache de conteúdo protegido no AEM Dispatcher
description: Saiba como o cache sensível a permissões funciona no Dispatcher.
seo-description: Saiba como o cache sensível a permissões funciona no AEM Dispatcher.
uuid: abfeed68a-2efe-45f6-bdf7-2284931629d6
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: expedidor
content-type: referência
discoiquuid: 4f9b2bc8-a309-47bc-b70d-a1c0da78d464
translation-type: tm+mt
source-git-commit: 8dd56f8b90331f0da43852e25893bc6f3e606a97

---


# Armazenamento em cache de conteúdo protegido {#caching-secured-content}

O cache sensível a permissões permite que você armazene páginas seguras em cache. O Dispatcher verifica as permissões de acesso do usuário para uma página antes de entregar a página em cache.

O Dispatcher inclui o módulo AuthChecker que implementa o cache sensível a permissões. Quando o módulo é ativado, a renderização chama um servlet AEM para executar autenticação de usuário e autorização para o conteúdo solicitado. A resposta do servlet determina se o conteúdo é entregue ao navegador da Web.

Como os métodos de autenticação e autorização são específicos da implantação do AEM, você deve criar o servlet.

>[!NOTE]
>
>Use `deny` filtros para aplicar restrições de segurança em aberto. Use o cache sensível a permissões para páginas configuradas para permitir acesso a um subconjunto de usuários ou grupos.

Os diagramas a seguir ilustram a ordem dos eventos que ocorrem quando um navegador da Web solicita uma página para a qual o cache sensível a permissões é usado.

## A página é armazenada em cache e o usuário é autorizado {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. O Dispatcher determina que o conteúdo solicitado é armazenado em cache e válido.
1. O Dispatcher envia uma mensagem de solicitação para a renderização. A seção HEAD inclui todas as linhas de cabeçalho da solicitação do navegador.
1. A renderização chama o autorizador para executar a verificação de segurança e responde ao Dispatcher. A mensagem de resposta inclui um código de status HTTP 200 para indicar que o usuário está autorizado.
1. O Dispatcher envia uma mensagem de resposta ao navegador que consiste nas linhas de cabeçalho da resposta de renderização e do conteúdo em cache no corpo.

## A página não está em cache e o usuário está autorizado {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. O Dispatcher determina que o conteúdo não está em cache ou requer atualização.
1. O Dispatcher encaminha a solicitação original para a renderização.
1. A renderização chama o servlet do autorizador para executar uma verificação de segurança. Quando o usuário é autorizado, a renderização inclui a página renderizada no corpo da mensagem de resposta.
1. O Dispatcher encaminha a resposta ao navegador. O Dispatcher adiciona o corpo da mensagem de resposta da renderização ao cache.

## Usuário não autorizado {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. O Dispatcher verifica o cache.
1. O Dispatcher envia uma mensagem de solicitação para a renderização que inclui todas as linhas de cabeçalho da solicitação do navegador.
1. A renderização chama o servlet do autorizador para executar uma verificação de segurança que falha e a renderização encaminha a solicitação original para o Dispatcher.

## Implementação de cache sensível a permissões {#implementing-permission-sensitive-caching}

Para implementar o cache sensível a permissões, execute as seguintes tarefas:

* Desenvolver um servlet que execute autenticação e autorização
* Configurar o Dispatcher

>[!NOTE]
>
>Normalmente, os recursos protegidos são armazenados em uma pasta separada do que em arquivos não protegidos. Por exemplo, /content/secure/


## Criar o servlet de autorização {#create-the-authorization-servlet}

Crie e implante um servlet que execute a autenticação e a autorização do usuário que solicita o conteúdo da Web. O servlet pode usar qualquer método de autenticação e autorização, como a conta de usuário do AEM e as ACLs do repositório, ou um serviço de pesquisa LDAP. Você implanta o servlet na instância do AEM que o Dispatcher usa como renderização.

O servlet deve estar acessível a todos os usuários. Portanto, seu servlet deve estender a `org.apache.sling.api.servlets.SlingSafeMethodsServlet` classe, que fornece acesso somente leitura ao sistema.

O servlet recebe somente solicitações HEAD da renderização, portanto, você só precisa implementar o `doHead` método.

A renderização inclui o URI do recurso solicitado como um parâmetro da solicitação HTTP. Por exemplo, um servlet de autorização é acessado via `/bin/permissioncheck`. Para executar uma verificação de segurança na página /content/geometrixx-outdoors/en.html, a renderização inclui o seguinte URL na solicitação HTTP:

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

A mensagem de resposta do servlet deve conter os seguintes códigos de status HTTP:

* 200: Autenticação e autorização aprovados.

O servlet de exemplo a seguir obtém o URL do recurso solicitado da solicitação HTTP. O código usa a anotação Felix SCR `Property` para definir o valor da `sling.servlet.paths` propriedade como /bin/permissioncheck. No `doHead` método, o servlet obtém o objeto session e usa o `checkPermission` método para determinar o código de resposta apropriado.

>[!NOTE]
>
>O valor da propriedade sling.servlet.path deve ser ativado no serviço Sling Servlet Resolver (org.apache.sling.servlets.resolvedor.SlingServletResolver).

### Exemplo de servlet {#example-servlet}

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.jcr.Session;

@Component(metatype=false)
@Service
public class AuthcheckerServlet extends SlingSafeMethodsServlet {
 
    @Property(value="/bin/permissioncheck")
    static final String SERVLET_PATH="sling.servlet.paths";
    
    private Logger logger = LoggerFactory.getLogger(this.getClass());
    
    public void doHead(SlingHttpServletRequest request, SlingHttpServletResponse response) {
     try{ 
      //retrieve the requested URL
      String uri = request.getParameter("uri");
      //obtain the session from the request
      Session session = request.getResourceResolver().adaptTo(javax.jcr.Session.class);     
      //perform the permissions check
      try {
       session.checkPermission(uri, Session.ACTION_READ);
       logger.info("authchecker says OK");
       response.setStatus(SlingHttpServletResponse.SC_OK);
      } catch(Exception e) {
       logger.info("authchecker says READ access DENIED!");
       response.setStatus(SlingHttpServletResponse.SC_FORBIDDEN);
      }
     }catch(Exception e){
      logger.error("authchecker servlet exception: " + e.getMessage());
     }
    }
}
```

## Configurar o Dispatcher para armazenamento em cache sensível a permissões {#configure-dispatcher-for-permission-sensitive-caching}

A seção auth_checker do dispatcher.any file controla o comportamento do cache sensível a permissões. A seção auth_checker inclui as seguintes subseções:

* `url`: O valor da `sling.servlet.paths` propriedade do servlet que executa a verificação de segurança.

* `filter`: Filtros que especificam as pastas às quais o cache sensível a permissões é aplicado. Normalmente, um `deny` filtro é aplicado a todas as pastas e `allow` os filtros são aplicados a pastas seguras.

* `headers`: Especifica os cabeçalhos HTTP que o servlet de autorização inclui na resposta.

Quando o Dispatcher é iniciado, o arquivo de log do Dispatcher inclui a seguinte mensagem de nível de depuração:

`AuthChecker: initialized with URL 'configured_url'.`

A seguinte seção auth_checker configura o Dispatcher para usar o servlet do tópico anterior. A seção de filtro faz com que as verificações de permissão sejam executadas somente em recursos HTML seguros.

### Exemplo de configuração {#example-configuration}

```xml
/auth_checker
  {
  # request is sent to this URL with '?uri=<page>' appended
  /url "/bin/permissioncheck"
      
  # only the requested pages matching the filter section below are checked,
  # all other pages get delivered unchecked
  /filter
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "/content/secure/*.html"
      /type "allow"
      }
    }
  # any header line returned from the auth_checker's HEAD request matching
  # the section below will be returned as well
  /headers
    {
    /0000
      {
      /glob "*"
      /type "deny"
      }
    /0001
      {
      /glob "Set-Cookie:*"
      /type "allow"
      }
    }
  }
```

