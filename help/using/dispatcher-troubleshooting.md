---
title: Resolução de Problemas do Dispatcher
seo-title: Solução de problemas AEM Dispatcher
description: Saiba como solucionar problemas do Dispatcher.
seo-description: Saiba como solucionar problemas AEM Dispatcher.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
translation-type: tm+mt
source-git-commit: 9af0dc22d32f1176b84c28a70b1a4701414d434e
workflow-type: tm+mt
source-wordcount: '553'
ht-degree: 6%

---


# Resolução de Problemas do Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM, no entanto, a documentação do Dispatcher é incorporada na documentação do AEM. Use sempre a documentação do Dispatcher incorporada na documentação para obter a versão mais recente do AEM.
>
>Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

>[!NOTE]
>
>Consulte também a Base [de conhecimento do](https://helpx.adobe.com/cq/kb/index/dispatcher.html)Dispatcher, a [Solução de problemas de descarga do Dispatcher e as Perguntas frequentes sobre](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) os problemas principais do [Dispatcher](dispatcher-faq.md) para obter mais informações.

## Verifique a configuração básica {#check-the-basic-configuration}

Como sempre, as primeiras etapas são verificar as noções básicas:

* [Confirmar operação básica](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Verifique todos os arquivos de log para seu servidor da Web e despachante. Se necessário, aumente o `loglevel` usado para o [registro](/help/using/dispatcher-configuration.md#logging)do dispatcher.

* [Verifique sua configuração](/help/using/dispatcher-configuration.md):

   * Você tem vários Dispatchers?

      * Você determinou qual Dispatcher está manipulando o site / página que está investigando?
   * Você implementou filtros?

      * Eles estão afetando o assunto que você está investigando?


## Ferramentas de Diagnóstico do IIS {#iis-diagnostic-tools}

O IIS fornece várias ferramentas de rastreamento, dependendo da versão real:

* IIS 6 - Ferramentas de diagnóstico IIS podem ser baixadas e configuradas
* IIS 7 - O rastreamento está totalmente integrado

Isso pode ajudá-lo a monitorar a atividade.

## IIS e 404 não encontrados {#iis-and-not-found}

Ao usar o IIS, você pode experimentar `404 Not Found` ser retornado em vários cenários. Em caso afirmativo, consulte os seguintes artigos da Base de conhecimento.

* [IIS 6/7: método POST retorna 404](https://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Solicitações para URLs que contêm o caminho base `/bin` retornar `404 Not Found`](https://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

Você também deve verificar se a raiz do cache do dispatcher e a raiz do documento do IIS estão definidas para o mesmo diretório.

## Problemas ao excluir modelos de fluxo de trabalho {#problems-deleting-workflow-models}

**Sintomas**

Problemas ao tentar excluir modelos de fluxo de trabalho ao acessar uma instância do autor AEM por meio do Dispatcher.

**Etapas para reproduzir:**

1. Faça logon na instância do autor (confirme se as solicitações estão sendo encaminhadas pelo dispatcher).
1. Criar um novo fluxo de trabalho; por exemplo, com o Título definido como workflowToDelete.
1. Confirme se o fluxo de trabalho foi criado com êxito.
1. Selecione e clique com o botão direito do mouse no fluxo de trabalho e clique em **Excluir**.

1. Click **Yes** to confirm.
1. Uma caixa de mensagem de erro será exibida:\
   &quot; `ERROR 'Could not delete workflow model!!`&quot;.

**Resolução**

Adicione os seguintes cabeçalhos à `/clientheaders` seção do seu `dispatcher.any` arquivo:

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Interferência com mod_dir (Apache) {#interference-with-mod-dir-apache}

Isso descreve como o dispatcher interage com `mod_dir` o servidor Web Apache, pois isso pode causar vários efeitos potencialmente inesperados:

### Apache 1.3 {#apache}

No Apache 1.3, `mod_dir` lida com cada solicitação em que o URL é mapeado para um diretório no sistema de arquivos.

Isso irá:

* redirecionar a solicitação para um `index.html` arquivo existente
* gerar uma listagem de diretório

Quando o dispatcher está ativado, ele processa essas solicitações ao se registrar como um manipulador para o tipo de conteúdo `httpd/unix-directory`.

### Apache 2.x {#apache-x}

No Apache 2.x as coisas são diferentes. Um módulo pode lidar com diferentes estágios da solicitação, como correção de URL. `mod_dir` trata desse estágio redirecionando uma solicitação (quando o URL mapeia para um diretório) para o URL com um `/` anexo.

O Dispatcher não intercepta a `mod_dir` correção, mas lida completamente com a solicitação para o URL redirecionado (isto é, com `/` anexo). Isso pode causar um problema se o servidor remoto (por exemplo, AEM) manipular solicitações de forma `/a_path` diferente das solicitações feitas `/a_path/` (quando `/a_path` mapeia para um diretório existente).

Se isso acontecer, você deverá:

* desativar `mod_dir` para a `Directory` subárvore ou `Location` subárvore manuseada pelo dispatcher

* use `DirectorySlash Off` para configurar `mod_dir` para não anexar `/`
