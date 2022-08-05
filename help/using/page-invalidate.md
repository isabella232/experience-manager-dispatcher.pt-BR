---
title: Invalidar páginas em cache do AEM
seo-title: Invalidating Cached Pages From Adobe AEM
description: Saiba como configurar a interação entre o Dispatcher e o AEM para garantir um gerenciamento eficiente do cache.
seo-description: Learn how to configure the interaction between Adobe AEM Dispatcher and AEM to ensure effective cache management.
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: f447ff9b3785248a4906c1c9abdcbd18576aa36d
workflow-type: ht
source-wordcount: '0'
ht-degree: 100%

---

# Invalidar páginas em cache do AEM {#invalidating-cached-pages-from-aem}

Ao usar o Dispatcher com o AEM, a interação deve ser configurada para garantir um gerenciamento eficiente do cache. Dependendo do seu ambiente, a configuração também pode aumentar o desempenho.

## Configuração das contas de usuário do AEM {#setting-up-aem-user-accounts}

A conta de usuário `admin` padrão é usada para autenticar os agentes de replicação instalados por padrão. Você deve criar uma conta de usuário dedicada para usar com agentes de replicação.

Para obter mais informações, consulte a seção [Configuração de usuários de replicação e transporte](https://helpx.adobe.com/br/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) da Lista de verificação de segurança do AEM.

## Invalidação do cache do Dispatcher no ambiente de criação {#invalidating-dispatcher-cache-from-the-authoring-environment}

Um agente de replicação na instância do autor do AEM envia uma solicitação de invalidação de cache para o Dispatcher quando uma página é publicada. A solicitação faz com que o Dispatcher posteriormente atualize o arquivo no cache conforme o novo conteúdo é publicado.

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

Use o procedimento a seguir para configurar um agente de replicação na instância do autor do AEM para invalidar o cache do Dispatcher na ativação da página:

1. Abra o console Ferramentas do AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Abra o agente de replicação necessário abaixo de Ferramentas/Replicação/Agentes no autor. Você pode usar o agente de limpeza do Dispatcher instalado por padrão.
1. Clique em Editar e, na guia Configurações, certifique-se de que **Ativado** esteja selecionado.

1. (Opcional) Para ativar solicitações de invalidação de alias ou caminho personalizado, selecione a opção **Atualização de alias**.
1. Na guia Transporte, insira o URI necessário para acessar o Dispatcher.\
   Se você estiver usando o agente de limpeza padrão do Dispatcher, provavelmente precisará atualizar o nome do host e a porta. Por exemplo, https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **Observação:** para agentes de limpeza do Dispatcher, a propriedade do URI será usada somente se você usar entradas de hosts virtuais baseadas em caminho para fazer distinção entre farms. Use esse campo para direcionar o farm a ser invalidado. Por exemplo, o farm nº 1 tem um host virtual de `www.mysite.com/path1/*`, e o farm nº 2 tem um host virtual de `www.mysite.com/path2/*`. Você pode usar um URL de `/path1/invalidate.cache` para direcionar o primeiro farm, e `/path2/invalidate.cache` para direcionar o segundo farm. Para obter mais informações, consulte [Uso do Dispatcher com vários domínios](dispatcher-domains.md).

1. Configure outros parâmetros conforme necessário.
1. Clique em OK para ativar o agente.

Como alternativa, você também pode acessar e configurar o agente de limpeza do Dispatcher direto do [AEM Touch UI](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/configuring/replication.html?lang=pt-BR#configuring-a-dispatcher-flush-agent).

Para obter detalhes adicionais sobre como habilitar o acesso a URLs personalizados, consulte [Habilitação de acesso a URLs personalizados](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>O agente para liberar o cache do dispatcher não precisa ter um nome de usuário e senha, mas, se configurado, eles serão enviados com autenticação básica.

Há dois problemas potenciais com essa abordagem:

* O Dispatcher deve estar acessível na instância de criação. Se sua rede (por exemplo, o firewall) estiver configurada de modo que o acesso entre os dois seja restrito, esse pode não ser o caso.

* A publicação e a invalidação do cache ocorrem simultaneamente. Dependendo do tempo, um usuário pode solicitar uma página logo após sua remoção do cache e antes da publicação da nova página. O AEM retorna a página antiga, e o Dispatcher a armazena em cache novamente. Isso é um problema que ocorre mais com sites grandes.

## Invalidação do cache do Dispatcher de uma Instância de publicação {#invalidating-dispatcher-cache-from-a-publishing-instance}

Em determinadas circunstâncias, os ganhos de desempenho podem ser feitos pela transferência do gerenciamento de cache do ambiente de criação para uma instância de publicação. Em seguida, será o ambiente de publicação (não o ambiente de criação do AEM) que enviará uma solicitação de invalidação de cache para o Dispatcher quando uma página publicada for recebida.

Essas circunstâncias incluem:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Prevenção de possíveis conflitos de tempo entre o Dispatcher e a instância de publicação (consulte [Invalidação do cache do Dispatcher do ambiente de criação](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* O sistema inclui várias instâncias de publicação que residem em servidores de alto desempenho e somente uma instância de criação.

>[!NOTE]
>
>A decisão de utilizar esse método deve ser tomada por um administrador experiente do AEM.

A liberação do dispatcher é controlada por um agente de replicação que opera na instância de publicação. No entanto, a configuração é feita no ambiente de criação e depois transferida pela ativação do agente:

1. Abra o console Ferramentas do AEM.
1. Abra o agente de replicação necessário abaixo de Ferramentas/Replicação/Agentes na publicação. Você pode usar o agente de limpeza do Dispatcher instalado por padrão.
1. Clique em Editar e, na guia Configurações, certifique-se de que **Ativado** esteja selecionado.
1. (Opcional) Para ativar solicitações de invalidação de alias ou caminho personalizado, selecione a opção **Atualização de alias**.
1. Na guia Transporte, insira o URI necessário para acessar o Dispatcher.\
   Se você estiver usando o agente de limpeza padrão do Dispatcher, provavelmente precisará atualizar o nome do host e a porta. Por exemplo, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Observação:** para agentes de limpeza do Dispatcher, a propriedade do URI será usada somente se você usar entradas de hosts virtuais baseadas em caminho para fazer distinção entre farms. Use esse campo para direcionar o farm a ser invalidado. Por exemplo, o farm nº 1 tem um host virtual de `www.mysite.com/path1/*`, e o farm nº 2 tem um host virtual de `www.mysite.com/path2/*`. Você pode usar um URL de `/path1/invalidate.cache` para direcionar o primeiro farm, e `/path2/invalidate.cache` para direcionar o segundo farm. Para obter mais informações, consulte [Uso do Dispatcher com vários domínios](dispatcher-domains.md).

1. Configure outros parâmetros conforme necessário.
1. Repita o procedimento para cada instância de publicação afetada.

Após a configuração, ao ativar uma página do autor para publicar, esse agente inicia uma replicação padrão. O log inclui mensagens indicando solicitações provenientes do seu servidor de publicação, semelhantes ao seguinte exemplo:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidação manual do cache do Dispatcher {#manually-invalidating-the-dispatcher-cache}

Para invalidar (ou liberar) o cache do Dispatcher sem ativar uma página, você pode emitir uma solicitação HTTP para o Dispatcher. Por exemplo, você pode criar um aplicativo do AEM que permita aos administradores ou outros aplicativos liberar o cache.

A solicitação HTTP faz com que o Dispatcher exclua arquivos específicos do cache. Como opção, o Dispatcher atualiza o cache com uma nova cópia.

### Exclusão de arquivos em cache {#delete-cached-files}

Emita uma solicitação HTTP que faz com que o Dispatcher exclua arquivos do cache. O Dispatcher armazena os arquivos em cache novamente somente quando recebe uma solicitação do cliente para a página. A exclusão de arquivos em cache dessa maneira é adequada para sites que provavelmente não receberão solicitações simultâneas para a mesma página.

A solicitação HTTP tem o seguinte formato:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

O Dispatcher libera (exclui) os arquivos e pastas em cache com nomes que correspondam ao valor do cabeçalho `CQ-Handler`. Por exemplo, um `CQ-Handle` de `/content/geomtrixx-outdoors/en` corresponde aos seguintes itens:

* Todos os arquivos (de qualquer extensão de arquivo) com nome `en` no diretório `geometrixx-outdoors`

* Qualquer diretório chamado &quot;`_jcr_content`&quot; abaixo do diretório en (que, se existir, contém renderizações em cache de subnós da página)

Todos os outros arquivos no cache do dispatcher (ou até um nível específico, dependendo da configuração `/statfileslevel`) são invalidados ao tocar no arquivo `.stat`. A última data de modificação desse arquivo é comparada com a última data de modificação de um documento em cache, e o documento será buscado novamente se o arquivo `.stat` for mais recente. Consulte [Invalidação de arquivos por nível de pasta](dispatcher-configuration.md#main-pars_title_26) para obter mais detalhes.

A invalidação (ou seja, tocar em arquivos .stat) pode ser evitada ao enviar um Cabeçalho adicional `CQ-Action-Scope: ResourceOnly`. Isso pode ser usado para liberar recursos específicos sem invalidar outras partes do cache, como dados JSON criados dinamicamente e que exigem limpeza regular independente do cache (por exemplo, representando dados obtidos de um sistema de terceiros para exibir notícias, marcadores de ações etc.).

### Exclusão e rearmazenamento de arquivos em cache {#delete-and-recache-files}

Emita uma solicitação HTTP que faz com que o Dispatcher exclua arquivos em cache e, imediatamente, recupere e armazene o arquivo em cache novamente. Exclua e imediatamente armazene arquivos em cache novamente quando houver probabilidade de os sites receberem solicitações simultâneas do cliente para a mesma página. O rearmazenamento em cache imediato garante que o Dispatcher recupere e armazene em cache a página apenas uma vez, e não um vez para cada uma das solicitações simultâneas do cliente.

**Observação:** a exclusão e o rearmazenamento em cache de arquivos devem ser executados somente na instância de publicação. Quando executados da instância do autor, as condições de corrida ocorrem mediante tentativas de recuperar recursos antes de serem publicadas.

A solicitação HTTP tem o seguinte formato:

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

Os caminhos da página para rearmazenamento imediato são listados em linhas separadas no corpo da mensagem. O valor de `CQ-Handle` é o caminho de uma página que invalida as páginas para o rearmazenamento em cache. (Consulte o parâmetro `/statfileslevel` do item de configuração do [Cache](dispatcher-configuration.md#main-pars_146_44_0010).) O exemplo de mensagem de solicitação HTTP a seguir exclui e rearmazena em cache o `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Exemplo de servlet de liberação {#example-flush-servlet}

O código a seguir implementa um servlet que envia uma solicitação de invalidação para o Dispatcher. O servlet recebe uma mensagem de solicitação que contém os parâmetros `handle` e `page`. Esses parâmetros fornecem o valor do cabeçalho `CQ-Handle` e o caminho da página para refazer o armazenamento em cache, respectivamente. O servlet usa os valores para criar a solicitação HTTP para o Dispatcher.

Quando o servlet é implantado na instância de publicação, o seguinte URL faz com que o Dispatcher exclua a página /content/geometrixx-outdoors/en.html e, em seguida, armazene em cache uma nova cópia.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Este servlet de exemplo não é seguro e demonstra apenas o uso da mensagem de Solicitação HTTP POST. Sua solução deve proteger o acesso ao servlet.

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
