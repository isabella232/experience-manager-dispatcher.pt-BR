---
title: Invalidar páginas em cache do AEM
seo-title: Invalidar páginas em cache do Adobe AEM
description: Saiba como configurar a interação entre o Dispatcher e o AEM para garantir um gerenciamento eficaz do cache.
seo-description: Saiba como configurar a interação entre o Adobe AEM Dispatcher e o AEM para garantir um gerenciamento eficaz do cache.
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/models/contentpage
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: expedidor
content-type: referência
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Invalidar páginas em cache do AEM {#invalidating-cached-pages-from-aem}

Ao usar o Dispatcher com AEM, a interação deve ser configurada para garantir um gerenciamento eficaz do cache. Dependendo do seu ambiente, a configuração também pode aumentar o desempenho.

## Configuração de contas de usuário do AEM {#setting-up-aem-user-accounts}

A conta de `admin` usuário padrão é usada para autenticar os agentes de replicação instalados por padrão. Você deve criar uma conta de usuário dedicada para uso com agentes de replicação. [](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps)

Para obter mais informações, consulte a seção [Configurar usuários](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps) de replicação e transporte da lista de verificação de segurança do AEM.

## Invalidando o Cache do Dispatcher do Ambiente de Criação {#invalidating-dispatcher-cache-from-the-authoring-environment}

Um agente de replicação na instância do autor de AEM envia uma solicitação de invalidação de cache ao Dispatcher quando uma página é publicada. A solicitação faz com que o Dispatcher eventualmente atualize o arquivo no cache à medida que novo conteúdo é publicado.

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

Use o seguinte procedimento para configurar um agente de replicação na instância do autor de AEM para invalidar o cache do Dispatcher na ativação da página:

1. Abra o console Ferramentas do AEM. (`https://localhost:4502/miscadmin#/etc`)
1. Abra o agente de replicação necessário abaixo de Ferramentas/replicação/Agentes no autor. Você pode usar o agente do Dispatcher Flush instalado por padrão.
1. Clique em Editar e, na guia Configurações, verifique se **Ativado** está selecionado.

1. (opcional) Para ativar solicitações de invalidação de alias ou caminho personalizado, selecione a opção de atualização **** Alias.
1. Na guia Transporte, digite o URI necessário para acessar o Dispatcher.\
   Se você estiver usando o agente padrão do Dispatcher Flush, provavelmente será necessário atualizar o nome do host e a porta; por exemplo, https://&lt;*dispatcherHost*&gt;:&lt;*portApache*&gt;/dispatcher/invalidate.cache

   **** Observação: Para os agentes do Dispatcher Flush, a propriedade URI é usada somente se você usar entradas de host virtual baseadas em caminho para diferenciar entre fazendas. Use esse campo para direcionar o farm a ser invalidado. Por exemplo, o farm nº 1 tem um host virtual de `www.mysite.com/path1/*` e o farm nº 2 tem um host virtual de `www.mysite.com/path2/*`. Você pode usar um URL de `/path1/invalidate.cache` para direcionar o primeiro farm e `/path2/invalidate.cache` para o segundo farm. Para obter mais informações, consulte [Uso do Dispatcher com Vários Domínios](dispatcher-domains.md).

1. Configure outros parâmetros, conforme necessário.
1. Clique em OK para ativar o agente.

Como alternativa, você também pode acessar e configurar o agente do Dispatcher Flush na interface do usuário [do](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/replication.html#ConfiguringaDispatcherFlushagent)AEM Touch.

Para obter mais detalhes sobre como ativar o acesso a URLs personalizados, consulte [Ativando o acesso a URLs](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls)personalizados.

>[!NOTE]
>
>O agente para liberar o cache do dispatcher não precisa ter um nome de usuário e senha, mas se configurado, eles serão enviados com autenticação básica.

Há dois problemas potenciais com essa abordagem:

* O Dispatcher deve estar acessível na instância de criação. Se sua rede (por exemplo, o firewall) estiver configurada de modo que o acesso entre os dois seja restrito, isso pode não ser o caso.

* A publicação e a invalidação do cache ocorrem ao mesmo tempo. Dependendo do tempo, um usuário pode solicitar uma página logo depois de ela ter sido removida do cache e antes da nova página ser publicada. Agora, o AEM retorna a página antiga e o Dispatcher armazena em cache novamente. Isso é mais um problema para sites grandes.

## Invalidando o Cache do Dispatcher de uma Instância de Publicação {#invalidating-dispatcher-cache-from-a-publishing-instance}

Em determinadas circunstâncias, os ganhos de desempenho podem ser feitos com a transferência do gerenciamento de cache do ambiente de criação para uma instância de publicação. Em seguida, será o ambiente de publicação (não o ambiente de criação do AEM) que envia uma solicitação de invalidação de cache para o Dispatcher quando uma página publicada for recebida.

Essas circunstâncias incluem:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Impedindo possíveis conflitos de tempo entre o Dispatcher e a instância de publicação (consulte [Invalidando o cache do Dispatcher do Ambiente](#invalidating-dispatcher-cache-from-the-authoring-environment)de Criação).
* O sistema inclui várias instâncias de publicação que residem em servidores de alto desempenho e apenas uma instância de criação.

>[!NOTE]
>
>A decisão de usar esse método deve ser tomada por um administrador do AEM experiente.

O despacho do dispatcher é controlado por um agente de replicação que opera na instância de publicação. No entanto, a configuração é feita no ambiente de criação e, em seguida, transferida por meio da ativação do agente:

1. Abra o console Ferramentas do AEM.
1. Abra o agente de replicação necessário abaixo de Ferramentas/replicação/Agentes ao publicar. Você pode usar o agente do Dispatcher Flush instalado por padrão.
1. Clique em Editar e, na guia Configurações, verifique se **Ativado** está selecionado.
1. (opcional) Para ativar solicitações de invalidação de alias ou caminho personalizado, selecione a opção de atualização **** Alias.
1. Na guia Transporte, digite o URI necessário para acessar o Dispatcher.\
   Se você estiver usando o agente padrão do Dispatcher Flush, provavelmente será necessário atualizar o nome do host e a porta; por exemplo, `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **** Observação: Para os agentes do Dispatcher Flush, a propriedade URI é usada somente se você usar entradas de host virtual baseadas em caminho para diferenciar entre fazendas. Use esse campo para direcionar o farm a ser invalidado. Por exemplo, o farm nº 1 tem um host virtual de `www.mysite.com/path1/*` e o farm nº 2 tem um host virtual de `www.mysite.com/path2/*`. Você pode usar um URL de `/path1/invalidate.cache` para direcionar o primeiro farm e `/path2/invalidate.cache` para o segundo farm. Para obter mais informações, consulte [Uso do Dispatcher com Vários Domínios](dispatcher-domains.md).

1. Configure outros parâmetros, conforme necessário.
1. Repita o procedimento para cada instância de publicação afetada.

Após a configuração, ao ativar uma página do autor para publicar, esse agente inicia uma replicação padrão. O registro inclui mensagens indicando solicitações provenientes do servidor de publicação, semelhantes ao exemplo a seguir:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Invalidando manualmente o cache do Dispatcher {#manually-invalidating-the-dispatcher-cache}

Para invalidar (ou liberar) o cache do Dispatcher sem ativar uma página, é possível emitir uma solicitação HTTP para o dispatcher. Por exemplo, você pode criar um aplicativo AEM que permite que os administradores ou outros aplicativos liberem o cache.

A solicitação HTTP faz com que o Dispatcher exclua arquivos específicos do cache. Como opção, o Dispatcher atualiza o cache com uma nova cópia.

### Excluir arquivos em cache {#delete-cached-files}

Emita uma solicitação HTTP que faz com que o Dispatcher exclua arquivos do cache. O Dispatcher armazena os arquivos em cache novamente somente quando recebe uma solicitação do cliente para a página. A exclusão de arquivos em cache dessa maneira é apropriada para sites que provavelmente não recebem solicitações simultâneas para a mesma página.

A solicitação HTTP tem o seguinte formulário:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

O Dispatcher limpa (exclui) os arquivos e pastas em cache que têm nomes que correspondem ao valor do `CQ-Handler` cabeçalho. Por exemplo, uma `CQ-Handle` das correspondências `/content/geomtrixx-outdoors/en` corresponde aos seguintes itens:

* Todos os arquivos (de qualquer extensão de arquivo) nomeados `en` no `geometrixx-outdoors` diretório

* Qualquer diretório chamado " `_jcr_content`" abaixo do diretório en (que, se existir, contém renderizações em cache de subnós da página)

Todos os outros arquivos no cache do dispatcher (ou até um nível específico, dependendo da `/statfileslevel` configuração) são invalidados tocando no `.stat` arquivo. A última data de modificação do arquivo é comparada à última data de modificação de um documento em cache e o documento é buscado novamente se o `.stat` arquivo for mais recente. Consulte [Invalidando arquivos por nível](dispatcher-configuration.md#main-pars_title_26) de pasta para obter detalhes.

A invalidação (ou seja, o toque de arquivos .stat) pode ser impedida pelo envio de um cabeçalho adicional `CQ-Action-Scope: ResourceOnly`. Isso pode ser usado para liberar recursos específicos sem invalidar outras partes do cache, como dados JSON que são criados dinamicamente e que exigem o descarte regular independente do cache (por exemplo, a representação de dados obtidos de um sistema de terceiros para exibir notícias, indicadores de ações etc.).

### Excluir e registrar arquivos {#delete-and-recache-files}

Emita uma solicitação HTTP que faz com que o Dispatcher exclua arquivos em cache e recupere e armazene imediatamente o arquivo em cache. Exclua e recoloque imediatamente os arquivos quando os sites provavelmente receberão solicitações simultâneas do cliente para a mesma página. O cache imediato garante que o Dispatcher recupere e armazene a página em cache apenas uma vez, em vez de uma vez para cada solicitação de cliente simultânea.

**** Observação: A exclusão e o cache de arquivos devem ser executados somente na instância de publicação. Quando executadas a partir da instância do autor, as condições de raça ocorrem quando as tentativas de recuperar recursos ocorrem antes de serem publicados.

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

Os caminhos de página para serem imediatamente registrados são listados em linhas separadas no corpo da mensagem. O valor de `CQ-Handle` é o caminho de uma página que invalida as páginas a serem cache. (Consulte o `/statfileslevel` parâmetro do item de configuração [Cache](dispatcher-configuration.md#main-pars_146_44_0010) .) A seguinte mensagem de solicitação HTTP de exemplo exclui e armazena em cache o `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Exemplo de servlet flush {#example-flush-servlet}

O código a seguir implementa um servlet que envia uma solicitação de invalidação para o Dispatcher. O servlet recebe uma mensagem de solicitação que contém `handle` e `page` parâmetros. Esses parâmetros fornecem o valor do `CQ-Handle` cabeçalho e o caminho da página para o cache, respectivamente. O servlet usa os valores para construir a solicitação HTTP para o Dispatcher.

Quando o servlet é implantado na instância de publicação, o seguinte URL faz com que o Dispatcher exclua a página /content/geometrixx-outdoors/en.html e, em seguida, armazene uma nova cópia em cache.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Este exemplo de servlet não é seguro e demonstra apenas o uso da mensagem de solicitação HTTP Post. Sua solução deve proteger o acesso ao servlet.


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

