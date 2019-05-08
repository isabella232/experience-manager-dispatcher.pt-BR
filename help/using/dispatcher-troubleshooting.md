---
title: Solução de problemas do Dispatcher
seo-title: Solução de problemas do AEM Dispatcher
description: Saiba como solucionar problemas do Dispatcher.
seo-description: Saiba como solucionar problemas do AEM Dispatcher.
uuid: 9 c 109 a 48-d 921-4 b 6 e -9626-1158 cebc 41 e 7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
template: /apps/docs/templates/contentpage
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: a 612 e 745-f 1 e 6-43 de-b 25 a -9 adcaadab 5 cf
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Solução de problemas do Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM, mas a documentação do Dispatcher é incorporada à documentação do AEM. Sempre use a documentação do Dispatcher incorporada à documentação da versão mais recente do AEM.
>
>Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

>[!NOTE]
>
>Consulte também a [Base de conhecimento do Dispatcher](https://helpx.adobe.com/cq/kb/index/dispatcher.html), [Solução de problemas do Dispatcher e](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) perguntas frequentes sobre problemas principais [do Dispatcher](dispatcher-faq.md) para obter mais informações.

## Verificar a configuração básica {#check-the-basic-configuration}

Como sempre as primeiras etapas são verificar as noções básicas:

* [Confirmar operação básica](#ConfirmBasicOperation)
* Verifique todos os arquivos de log do servidor Web e do expedidor. Se necessário, aumente o `loglevel` uso do [registro do expedidor](#Logging).

* [Verifique sua configuração](#ConfiguringtheDispatcher):

   * Você tem vários Dispatchers?

      * Você determinou qual Dispatcher está manipulando o site/página que está investigando?
   * Você implementou filtros?

      * Isso afeta o problema que está investigando?


## Ferramentas de diagnóstico IIS {#iis-diagnostic-tools}

O IIS fornece várias ferramentas de rastreamento, dependendo da versão real:

* IIS 6 - As ferramentas de diagnóstico IIS podem ser baixadas e configuradas
* IIS 7 - o rastreamento é totalmente integrado

Eles podem ajudar a monitorar a atividade.

## IIS e 404 não encontrado {#iis-and-not-found}

Ao usar o IIS, você pode ter uma experiência `404 Not Found` retornada em vários cenários. Em caso afirmativo, consulte os seguintes artigos da Base de conhecimento.

* [IIS 6/7: Método POST retorna 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Solicitações para urls que contêm o caminho base `/bin` retornado `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Você também deve verificar se a raiz do cache do dispatcher e a raiz do documento IIS estão definidas para o mesmo diretório.

## Problemas ao excluir modelos de fluxo de trabalho {#problems-deleting-workflow-models}

**Sintomas**

Problemas ao tentar excluir modelos de fluxo de trabalho ao acessar uma instância de autor de AEM por meio do Dispatcher.

**Etapas para reproduzir:**

1. Faça logon na instância do autor (confirme se as solicitações estão sendo roteadas pelo expedidor).
1. Criar um novo fluxo de trabalho; por exemplo, com o Título definido como workflowtodelete.
1. Confirme se o fluxo de trabalho foi criado com êxito.
1. Selecione e clique com o botão direito do mouse no fluxo de trabalho e clique **em Excluir**.

1. Clique **em Sim** para confirmar.
1. Uma caixa de mensagem de erro será exibida mostrando:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Resolução**

Adicione os seguintes cabeçalhos à `/clientheaders` seção do `dispatcher.any` arquivo:

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## Interferência com mod_ dir (Apache) {#interference-with-mod-dir-apache}

Isso descreve como o dispatcher interage `mod_dir` com dentro do servidor Web Apache, pois isso pode levar a vários efeitos potencialmente inesperados:

### Apache 1.3 {#apache}

No Apache 1.3 `mod_dir` , é possível ler cada solicitação em que o URL mapeia para um diretório no sistema de arquivos.

Isso será:

* redirecionar a solicitação para um `index.html` arquivo existente
* gerar uma lista de diretório

Quando o expedidor está ativado, processa essas solicitações registrando-se como um manipulador para o tipo `httpd/unix-directory`de conteúdo.

### Apache 2. x {#apache-x}

No Apache 2. x, as coisas são diferentes. Um módulo pode lidar com diferentes estágios da solicitação, como correção de URL. `mod_dir` trata desse estágio redirecionando uma solicitação (quando o URL mapeia para um diretório) para o URL com um `/` anexo anexado.

O Dispatcher não intercepta a `mod_dir` correção, mas lida totalmente com a solicitação para o URL redirecionado (ou seja, com `/` anexado). Isso pode causar um problema se o servidor remoto (por exemplo, AEM) lida com solicitações `/a_path` de forma diferente para solicitações ( `/a_path/` quando `/a_path` mapear para um diretório existente).

Se isso acontecer, você deve:

* desativar `mod_dir` para a `Directory``Location` subárvore manipulada pelo expedidor

* use `DirectorySlash Off` para não `mod_dir` anexar `/`
