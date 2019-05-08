---
title: Armazenamento de conteúdo protegido em cache
seo-title: Cache de conteúdo protegido no AEM Dispatcher
description: Saiba como o armazenamento em cache com diferenciação de permissão funciona no Dispatcher.
seo-description: Saiba como o armazenamento em cache com diferenciação de permissão funciona no AEM Dispatcher.
uuid: abaliment68 a -2 efe -45 f 6-bdf 7-2284931629 d 6
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: 4 f 9 b 2 bc 8-a 309-47 bc-b 70 d-a 1 c 0 da 78 d 464
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Armazenamento de conteúdo protegido em cache {#caching-secured-content}

O armazenamento em cache com permissão de permissão permite que você armazene páginas seguras. O Dispatcher verifica as permissões de acesso do usuário para uma página antes de entregar a página em cache.

O Dispatcher inclui o módulo authchecker que implementa cache que leva em consideração a permissão. Quando o módulo é ativado, a renderização chama um servlet do AEM para executar autenticação e autorização do usuário para o conteúdo solicitado. A resposta do servlet determina se o conteúdo é disponibilizado para o navegador da Web.

Como os métodos de autenticação e autorização são específicos da implantação do AEM, você deve criar o servlet.

>[!NOTE]
>
>Use `deny` filtros para impor restrições de segurança em branco. Use cache que diferencia permissões para páginas configuradas para permitir acesso a um subconjunto de usuários ou grupos.

Os diagramas a seguir ilustram a ordem dos eventos que ocorrem quando um navegador solicita uma página para a qual o cache que diferencia a permissão é usado.

## A página é armazenada em cache e o usuário está autorizado {#page-is-cached-and-user-is-authorized}

![](assets/chlimage_1.png)

1. O Dispatcher determina que o conteúdo solicitado é armazenado em cache e válido.
1. O Dispatcher envia uma mensagem de solicitação para a renderização. A seção HEAD inclui todas as linhas de cabeçalho da solicitação do navegador.
1. A renderização chama o autorizador para executar a verificação de segurança e responder ao Dispatcher. A mensagem de resposta inclui um código de status HTTP de 200 para indicar que o usuário está autorizado.
1. O Dispatcher envia uma mensagem de resposta para o navegador que consiste nas linhas do cabeçalho da resposta de renderização e do conteúdo em cache no corpo.

## A página não é armazenada em cache e o usuário está autorizado {#page-is-not-cached-and-user-is-authorized}

![](assets/chlimage_1-1.png)

1. O Dispatcher determina que o conteúdo não é armazenado em cache ou requer atualização.
1. O Dispatcher encaminha a solicitação original para a renderização.
1. A renderização chama o servlet do autorizador para realizar uma verificação de segurança. Quando o usuário é autorizado, a renderização inclui a página renderizada no corpo da mensagem de resposta.
1. O Dispatcher encaminhará a resposta para o navegador. O Dispatcher adiciona o corpo da mensagem de resposta da renderização ao cache.

## O usuário não está autorizado {#user-is-not-authorized}

![](assets/chlimage_1-2.png)

1. O Dispatcher verifica o cache.
1. O Dispatcher envia uma mensagem de solicitação para a renderização que inclui todas as linhas de cabeçalho da solicitação do navegador.
1. A renderização chama o servlet autorizador para executar uma verificação de segurança que falhar, e a renderização encaminhará a solicitação original para o Dispatcher.

## Implementação de armazenamento em cache com diferenciação de permissão {#implementing-permission-sensitive-caching}

Para implementar o armazenamento em cache com diferenciação de permissão, execute as seguintes tarefas:

* Desenvolver um servlet que executa autenticação e autorização
* Configurar o Dispatcher

>[!NOTE]
>
>Normalmente, os recursos protegidos são armazenados em uma pasta separada do que arquivos inseguros. Por exemplo, /content/secure/


## Criar o servlet de autorização {#create-the-authorization-servlet}

Crie e implante um servlet que executa a autenticação e autorização do usuário que solicita o conteúdo da Web. O servlet pode usar qualquer método de autenticação e autorização, como a conta de usuário e os acls de repositório do AEM, ou um serviço de pesquisa LDAP. Você implantará o servlet para a instância do AEM que o Dispatcher usa como renderização.

O servlet deve estar acessível a todos os usuários. Portanto, o servlet deve estender a `org.apache.sling.api.servlets.SlingSafeMethodsServlet` classe, que fornece acesso somente leitura ao sistema.

O servlet recebe somente solicitações HEAD da renderização, portanto você só precisa implementar o `doHead` método.

A renderização inclui o URI do recurso solicitado como um parâmetro da solicitação HTTP. Por exemplo, um servlet de autorização é acessado por `/bin/permissioncheck`meio de. Para realizar uma verificação de segurança na /content/geometrixx-outdoors/en.html ágina, a renderização inclui o seguinte URL na solicitação HTTP:

`/bin/permissioncheck?uri=/content/geometrixx-outdoors/en.html`

A mensagem de resposta do servlet deve conter os seguintes códigos de status HTTP:

* : 00: Autenticação e autorização transmitida.

O exemplo de servlet a seguir obtém o URL do recurso solicitado a partir da solicitação HTTP. O código usa a anotação de `Property` Felix SCR para definir o valor da `sling.servlet.paths` propriedade como /bin/permissioncheck. No `doHead` método, o servlet obtém o objeto session e usa o `checkPermission` método para determinar o código de resposta apropriado.

>[!NOTE]
>
>O valor da propriedade sling. servlet. paths deve ser ativado no serviço Sling Servlet Param (org. apache. sling. servlets. resolver. slingservletparam).

### Exemplo servlet {#example-servlet}

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

## Configurar o Dispatcher para armazenamento em cache com permissão de permissão {#configure-dispatcher-for-permission-sensitive-caching}

A seção auth_ checker do dispatcher. Qualquer arquivo controla o comportamento de armazenamento em cache que leva em consideração a permissão. A seção auth_ checker inclui as seguintes subseções:

* `url`: O valor da `sling.servlet.paths` propriedade do servlet que executa a verificação de segurança.

* `filter`: Filtros que especificam as pastas em que o cache que leva em consideração a permissão é aplicado. Normalmente, um `deny` filtro é aplicado a todas as pastas e `allow` os filtros são aplicados a pastas seguras.

* `headers`: Especifica os cabeçalhos HTTP que o servlet de autorização inclui na resposta.

Quando o Dispatcher é iniciado, o arquivo de log do Dispatcher inclui a seguinte mensagem de nível de depuração:

`AuthChecker: initialized with URL 'configured_url'.`

A seção de exemplo auth_ checker a seguir configura o Dispatcher para usar o servlet do tópico prevoius. A seção de filtro faz com que verificações de permissão sejam feitas somente em recursos HTML seguros.

### Configuração de exemplo {#example-configuration}

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

