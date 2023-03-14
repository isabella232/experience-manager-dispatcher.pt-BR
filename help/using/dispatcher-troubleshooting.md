---
title: Resolução de problemas do Dispatcher
seo-title: Troubleshooting AEM Dispatcher Problems
description: Saiba como solucionar problemas do Dispatcher.
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: 26c8edbb142297830c7c8bd068502263c9f0e7eb
workflow-type: tm+mt
source-wordcount: '560'
ht-degree: 43%

---

# Resolução de Problemas do Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM, no entanto, a documentação do Dispatcher está incorporada na documentação do AEM. Sempre use a documentação do Dispatcher incorporada à documentação da versão mais recente do AEM.
>
>Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

>[!NOTE]
>
>Verifique a [Base de conhecimento do Dispatcher](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), [Solução de problemas de liberação do Dispatcher](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) e [Perguntas frequentes sobre problemas principais do Dispatcher](dispatcher-faq.md) para obter mais informações.

## Verifique a configuração básica {#check-the-basic-configuration}

Como sempre, as primeiras etapas são verificar as noções básicas:

* [Confirmar operação básica](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Verifique todos os arquivos de log do seu servidor da Web e do Dispatcher. Se necessário, aumente o `loglevel` usado para o Dispatcher [fazendo logon](/help/using/dispatcher-configuration.md#logging).

* [Verifique sua configuração](/help/using/dispatcher-configuration.md):

   * Você tem vários Dispatchers?

      * Você determinou qual Dispatcher está lidando com o site/página que você está investigando?
   * Você implementou filtros?

      * Esses filtros estão afetando a questão que você está investigando?


## Ferramentas de diagnóstico do IIS {#iis-diagnostic-tools}

O IIS fornece várias ferramentas de rastreamento, dependendo da versão real:

* IIS 6 - ferramentas de diagnóstico do IIS podem ser baixadas e configuradas
* IIS 7 - o rastreamento é totalmente integrado

Essas ferramentas podem ajudá-lo a monitorar a atividade.

## IIS e 404 Não encontrado {#iis-and-not-found}

Ao usar o IIS, você pode experimentar `404 Not Found` sendo retornado em vários cenários. Em caso afirmativo, consulte os seguintes artigos da Knowledge base.

* [IIS 6/7: método POST retorna 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Solicitações para URLs que contêm o caminho base `/bin` retorne um `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Verifique também se a raiz do cache do Dispatcher e a raiz do documento IIS estão definidas no mesmo diretório.

## Problemas ao excluir modelos de fluxos de trabalho {#problems-deleting-workflow-models}

**Sintomas**

Problemas ao tentar excluir modelos de fluxos de trabalho ao acessar uma instância de autor do AEM por meio do Dispatcher.

**Etapas a serem reproduzidas:**

1. Faça logon na instância do autor (confirme se as solicitações estão sendo roteadas pelo Dispatcher).
1. Criar um workflow; por exemplo, com o Título definido como workflowToDelete.
1. Confirme se o fluxo de trabalho foi criado com êxito.
1. Selecione , clique com o botão direito do mouse no workflow e clique em **Excluir**.

1. Clique em **Sim** para confirmar.
1. Uma caixa de mensagem de erro é exibida mostrando o seguinte:\
   &quot;`ERROR 'Could not delete workflow model!!`&quot;.

**Resolução**

Adicione os seguintes cabeçalhos à seção `/clientheaders` do arquivo `dispatcher.any`:

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

Este processo descreve como o Dispatcher interage com `mod_dir` no servidor web Apache, pois pode causar vários efeitos potencialmente inesperados:

### Apache 1.3 {#apache}

No Apache 1.3, `mod_dir` lida com todas as solicitações em que o URL mapeia para um diretório no sistema de arquivos.

Ele irá:

* redirecionar a solicitação para um arquivo `index.html` existente
* gerar uma listagem de diretórios

Quando o Dispatcher está ativado, ele processa essas solicitações ao se registrar como um manipulador para o tipo de conteúdo `httpd/unix-directory`.

### Apache 2.x {#apache-x}

No Apache 2.x, as coisas são diferentes. Um módulo pode lidar com diferentes estágios da solicitação, como correção de URL. O `mod_dir` trata desse estágio redirecionando uma solicitação (quando o URL mapeia para um diretório) para o URL com uma `/` anexado.

O Dispatcher não intercepta o `mod_dir` correção, mas lida totalmente com a solicitação para o URL redirecionado (ou seja, com `/` anexado). Esse processo pode causar um problema se o servidor remoto (por exemplo, AEM) manipular solicitações para `/a_path` de maneira diferente em solicitações de `/a_path/` (quando `/a_path` mapeia para um diretório existente).

Se essa situação ocorrer, é necessário:

* disable `mod_dir` para `Directory` ou `Location` subárvore tratada pelo Dispatcher

* usar `DirectorySlash Off` para configurar `mod_dir` para não anexar `/`
