---
title: Resolução de problemas do Dispatcher
seo-title: Resolução de problemas do AEM Dispatcher
description: Saiba como solucionar problemas do Dispatcher.
seo-description: Saiba como solucionar problemas do AEM Dispatcher.
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
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: ht
source-wordcount: '553'
ht-degree: 100%

---

# Resolução de Problemas do Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM, no entanto, a documentação do Dispatcher está incorporada na documentação do AEM. Sempre use a documentação do Dispatcher incorporada à documentação da versão mais recente do AEM.
>
>Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

>[!NOTE]
>
>Verifique também a [Knowledge base do Dispatcher](https://helpx.adobe.com/pt/experience-manager/kb/index/dispatcher.html), [Resolução de problemas de liberação do Dispatcher](https://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) e as [Perguntas frequentes sobre os problemas principais do Dispatcher](dispatcher-faq.md) para obter mais informações.

## Verifique a configuração básica {#check-the-basic-configuration}

Como sempre, as primeiras etapas são verificar as noções básicas:

* [Confirmar operação básica](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Verificar todos os arquivos de log do servidor Web e do Dispatcher. Se necessário, aumente o `loglevel` usado para o [logging](/help/using/dispatcher-configuration.md#logging) do Dispatcher.

* [Verifique sua configuração](/help/using/dispatcher-configuration.md):

   * Você tem vários Dispatchers?

      * Você determinou qual Dispatcher está lidando com o site/página que você está investigando?
   * Você implementou filtros?

      * Eles estão afetando o assunto que você está investigando?


## Ferramentas de diagnóstico do IIS {#iis-diagnostic-tools}

O IIS fornece várias ferramentas de rastreamento, dependendo da versão real:

* IIS 6 - ferramentas de diagnóstico do IIS podem ser baixadas e configuradas
* IIS 7 - o rastreamento é totalmente integrado

Isso pode ajudá-lo a monitorar a atividade.

## IIS e 404 Não encontrado {#iis-and-not-found}

Ao usar o IIS, você pode experimentar `404 Not Found` retornando em vários cenários. Em caso afirmativo, consulte os seguintes artigos da Knowledge base.

* [IIS 6/7: método POST retorna 404](https://helpx.adobe.com/pt/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Solicitações para URLs que contêm o caminho base `/bin` retornam `404 Not Found`](https://helpx.adobe.com/pt/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Você também deve verificar se a raiz do cache do dispatcher e a raiz do documento do IIS estão definidas no mesmo diretório.

## Problemas ao excluir modelos de fluxos de trabalho {#problems-deleting-workflow-models}

**Sintomas**

Problemas ao tentar excluir modelos de fluxos de trabalho ao acessar uma instância de autor do AEM por meio do Dispatcher.

**Etapas a serem reproduzidas:**

1. Faça logon na sua instância do autor (confirme se as solicitações estão sendo roteadas pelo dispatcher).
1. Crie um novo fluxo de trabalho. Por exemplo, com o Título definido como workflowToDelete.
1. Confirme se o fluxo de trabalho foi criado com êxito.
1. Selecione e clique com o botão direito no fluxo de trabalho e depois clique em **Excluir**.

1. Clique em **Sim** para confirmar.
1. Uma caixa de mensagem de erro será exibida mostrando:\
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

Descreve como o Dispatcher interage com o `mod_dir` no servidor Web Apache, pois isso pode levar a vários efeitos possivelmente inesperados:

### Apache 1.3 {#apache}

No Apache 1.3, `mod_dir` lida com cada solicitação em que o URL mapeia para um diretório no sistema de arquivos.

Ele irá:

* redirecionar a solicitação para um arquivo `index.html` existente
* gerar uma listagem de diretórios

Quando o Dispatcher está ativado, ele processa essas solicitações se registrando como um manipulador para o tipo de conteúdo `httpd/unix-directory`.

### Apache 2.x {#apache-x}

No Apache 2.x as coisas são diferentes. Um módulo pode lidar com diferentes estágios da solicitação, como correção de URL. O `mod_dir` lida com esse estágio redirecionando uma solicitação (quando o URL mapeia para um diretório) para o URL com uma `/` anexada.

O Dispatcher não intercepta a correção `mod_dir`, mas lida completamente com a solicitação para o URL redirecionado (ou seja, com `/` anexada). Isso pode causar um problema se o servidor remoto (por exemplo, o AEM) manipula solicitações para `/a_path` de forma diferente para solicitações para `/a_path/` (quando `/a_path` mapeia para um diretório existente).

Se isso acontecer, você deve:

* desativar `mod_dir` para a subárvore `Directory` ou `Location` manipulada pelo dispatcher

* usar `DirectorySlash Off` para configurar `mod_dir` para não anexar `/`
