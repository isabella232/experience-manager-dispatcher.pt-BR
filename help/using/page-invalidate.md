---
title: Invalidando páginas em cache do AEM
seo-title: Invalidar páginas em cache do Adobe AEM
description: Saiba como configurar a interação entre o Dispatcher e o AEM para garantir o gerenciamento efetivo de cache.
seo-description: Saiba como configurar a interação entre o Adobe AEM Dispatcher e o AEM para garantir gerenciamento efetivo de cache.
uuid: 66533299-55 c 0-4864-9 beb -77 e 281 af 9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: 79 cd 94 be-a 6 bc -4 d 34-bfe 9-393 b 4107925 c
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Invalidando páginas em cache do AEM {#invalidating-cached-pages-from-aem}

Ao usar o Dispatcher com o AEM, a interação deve ser configurada para garantir o gerenciamento efetivo do cache. Dependendo do seu ambiente, a configuração também pode aumentar o desempenho.

## Configuração de contas de usuário do AEM {#setting-up-aem-user-accounts}

A conta `admin` de usuário padrão é usada para autenticar os agentes de replicação instalados por padrão. Você deve criar uma conta de usuário dedicada para usar com agentes de replicação. [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps)

Para obter mais informações, consulte a [seção Configurar replicação e usuários](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) de transporte da Lista de verificação de segurança do AEM.

## Invalidar o cache do Dispatcher do ambiente de criação {#invalidating-dispatcher-cache-from-the-authoring-environment}

Um agente de replicação na instância de autor do AEM envia uma solicitação de invalidação de cache para o Dispatcher quando uma página é publicada. A solicitação faz com que o Dispatcher atualize o arquivo no cache à medida que novo conteúdo é publicado.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Use o procedimento a seguir para configurar um agente de replicação na instância de autor do AEM para invalidar o cache do Dispatcher mediante a ativação da página:

1. Abra o console Ferramentas do AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Abra o agente de replicação necessário abaixo de Ferramentas/replicação/agentes no autor. Você pode usar o agente do Dispatcher Flush instalado por padrão.
1. Clique em Editar e, na guia Configurações, certifique-se de que **Ativado** esteja selecionado.

1. (opcional) Para ativar o alias ou o caminho vanity solicita a opção **de atualização** de alias.
1. Na guia Transporte, digite a URI necessária para acessar o Dispatcher.\
   Se você estiver usando o agente padrão Dispatcher Flush, provavelmente precisará atualizar o nome do host e a porta; por exemplo, https:// &lt;*dispatcherhost*&gt;: &lt;*Portapache*&gt;/dispatcher/invalidate. cache

   **Observação:** Para agentes Flush do Dispatcher, a propriedade URI é usada apenas se você usar entradas virtualhospedadas baseadas em caminho para diferenciar entre as explorações. Use esse campo para direcionar a fazenda para invalidar. Por exemplo, farm # 1 tem um host virtual de `www.mysite.com/path1/*` e farm # 2 tem um host virtual de `www.mysite.com/path2/*`. Você pode usar um URL de `/path1/invalidate.cache` destino para o primeiro conjunto e `/path2/invalidate.cache` para definir como meta o segundo. Para obter mais informações, consulte [Uso do Dispatcher com vários domínios](dispatcher-domains.md).

1. Configure outros parâmetros, conforme necessário.
1. Clique em OK para ativar o agente.

Como alternativa, você também pode acessar e configurar o agente do Dispatcher Flush na interface do usuário do [AEM Touch](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent).

Para obter detalhes adicionais sobre como ativar o acesso a urls vanity, consulte [Ativar acesso a urls Vanity](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>O agente para limpar o cache do expedidor não precisa ter um nome de usuário e senha, mas se configurado, eles serão enviados com autenticação básica.

Há dois problemas potenciais com essa abordagem:

* O Dispatcher deve ser visível na instância de criação. Se a rede (por exemplo, o firewall) estiver configurada de forma que o acesso entre as duas seja restrito, pode não ser o caso.

* A anulação de publicação e cache ocorre ao mesmo tempo. Dependendo do tempo, um usuário pode solicitar uma página logo depois de ser removido do cache e antes da publicação da nova página. Agora o AEM retorna a página antiga e o Dispatcher o armazena novamente em cache. Isso é mais um problema para sites grandes.

## Invalidando o cache do Dispatcher de uma instância de publicação {#invalidating-dispatcher-cache-from-a-publishing-instance}

Em determinadas circunstâncias, o desempenho de desempenho pode ser obtido transferindo o gerenciamento de cache do ambiente de criação para uma instância de publicação. Será o ambiente de publicação (não o ambiente de criação do AEM) que envia uma solicitação de invalidação de cache para o Dispatcher quando uma página publicada for recebida.

Essas circunstâncias incluem:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Impedir possíveis conflitos de tempo entre o Dispatcher e a instância de publicação (consulte [Invalidar cache do Dispatcher no Ambiente de criação](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* O sistema inclui várias instâncias de publicação que residem em servidores de alto desempenho e apenas uma instância de criação.

>[!NOTE]
>
>A decisão de usar esse método deve ser feita por um administrador do AEM experiente.

O flush do dispatcher é controlado por um agente de replicação operando na instância de publicação. No entanto, a configuração é feita no ambiente de criação e transferida pela ativação do agente:

1. Abra o console Ferramentas do AEM.
1. Abra o agente de replicação necessário abaixo das Ferramentas/replicação/Agentes na publicação. Você pode usar o agente do Dispatcher Flush instalado por padrão.
1. Clique em Editar e, na guia Configurações, certifique-se de que **Ativado** esteja selecionado.
1. (opcional) Para ativar o alias ou o caminho vanity solicita a opção **de atualização** de alias.
1. Na guia Transporte, digite a URI necessária para acessar o Dispatcher.\
   Se você estiver usando o agente padrão Dispatcher Flush, provavelmente precisará atualizar o nome do host e a porta; por exemplo, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Observação:** Para agentes Flush do Dispatcher, a propriedade URI é usada apenas se você usar entradas virtualhospedadas baseadas em caminho para diferenciar entre as explorações. Use esse campo para direcionar a fazenda para invalidar. Por exemplo, farm # 1 tem um host virtual de `www.mysite.com/path1/*` e farm # 2 tem um host virtual de `www.mysite.com/path2/*`. Você pode usar um URL de `/path1/invalidate.cache` destino para o primeiro conjunto e `/path2/invalidate.cache` para definir como meta o segundo. Para obter mais informações, consulte [Uso do Dispatcher com vários domínios](dispatcher-domains.md).

1. Configure outros parâmetros, conforme necessário.
1. Repita o procedimento para cada instância de publicação afetada.

Depois de configurar, quando você ativa uma página do autor para publicar, esse agente inicia uma replicação padrão. O log inclui mensagens que indicam solicitações que vêm do servidor de publicação, semelhante ao exemplo a seguir:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidar manualmente o cache do Dispatcher {#manually-invalidating-the-dispatcher-cache}

Para invalidar (ou esvaziar) o cache do Dispatcher sem ativar uma página, você pode emitir uma solicitação HTTP para o expedidor. Por exemplo, você pode criar um aplicativo AEM que permita que administradores ou outros aplicativos esvaziem o cache.

A solicitação HTTP faz com que o Dispatcher exclua arquivos específicos do cache. Opcionalmente, o Dispatcher atualiza o cache com uma nova cópia.

### Excluir arquivos em cache {#delete-cached-files}

Emite uma solicitação HTTP que faz com que o Dispatcher exclua arquivos do cache. O Dispatcher armazena os arquivos novamente apenas quando recebe uma solicitação de cliente para a página. A exclusão de arquivos em cache nesta forma é adequada para sites que não têm probabilidade de receber solicitações simultâneas para a mesma página.

A solicitação HTTP tem o seguinte formulário:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

O Dispatcher limpa (exclui) os arquivos em cache e as pastas que têm nomes que correspondem ao valor do `CQ-Handler` cabeçalho. Por exemplo, uma `CQ-Handle` de `/content/geomtrixx-outdoors/en` corresponde aos seguintes itens:

* Todos os arquivos (de qualquer extensão de arquivo) nomeados `en` no `geometrixx-outdoors` diretório

* Qualquer diretório chamado &quot; `_jcr_content`&quot; abaixo do diretório en (que, se existir, contém representações em cache de subnós da página)

Todos os outros arquivos no cache do expedidor (ou até um determinado nível, dependendo da `/statfileslevel` configuração) são invalidados ao tocar no `.stat` arquivo. A última data de modificação do arquivo é comparada à última data de modificação de um documento em cache e o documento é obtido novamente se `.stat` o arquivo for mais recente. Consulte [Invalidar arquivos por nível de pasta](dispatcher-configuration.md#main-pars_title_26) para obter detalhes.

A invalidação (ou seja, o toque de arquivos. stat) pode ser evitada enviando um cabeçalho `CQ-Action-Scope: ResourceOnly`adicional. Isso pode ser usado para separar recursos específicos sem invalidar outras partes do cache, como dados JSON criados dinamicamente e requer limpeza regular do cache (por exemplo, representar dados obtidos de um sistema de terceiros para exibir notícias, controles de estoque etc.).

### Excluir e recache arquivos {#delete-and-recache-files}

Emite uma solicitação HTTP que faz com que o Dispatcher exclua arquivos em cache e recupere e recupere imediatamente o arquivo. Exclua e rearquiva imediatamente arquivos quando os sites provavelmente receberão solicitações de cliente simultâneas para a mesma página. O download imediato garante que o Dispatcher recupere e armazena a página somente uma vez em vez de uma para cada uma das solicitações de cliente simultâneas.

**Observação:** A exclusão e o saneamento de arquivos devem ser executados somente na instância de publicação. Quando realizada a partir da instância do autor, as condições de raça ocorrem quando as tentativas de recache recursos ocorrem antes da publicação.

A solicitação HTTP tem o seguinte formulário:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

Os caminhos de página para recache imediatamente são listados em linhas separadas no corpo da mensagem. O valor de `CQ-Handle` é o caminho de uma página que invalida as páginas para recache. (Consulte o `/statfileslevel` parâmetro do [item](dispatcher-configuration.md#main-pars_146_44_0010) de configuração Cache.) A mensagem de solicitação HTTP de exemplo a seguir exclui e arquina o `/content/geometrixx-outdoors/en.html page`seguinte:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Exemplo de servlet flush {#example-flush-servlet}

O código a seguir implementa um servlet que envia uma solicitação invalidate para o Dispatcher. O servlet recebe uma mensagem de solicitação contendo `handle``page` e parâmetros. Esses parâmetros fornecem o valor do `CQ-Handle` cabeçalho e o caminho da página para recache, respectivamente. O servlet usa os valores para construir a solicitação HTTP para o Dispatcher.

Quando o servlet é implantado na instância de publicação, o URL a seguir faz com que o Dispatcher exclua a página /content/geometrixx-outdoors/en.html e em seguida armazene em cache uma nova cópia.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Esse servlet de exemplo não é seguro e demonstra apenas o uso da mensagem de solicitação HTTP Post. Sua solução deve proteger o acesso ao servlet.


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

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```

