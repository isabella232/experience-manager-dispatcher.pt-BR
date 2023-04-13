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
workflow-type: ht
source-wordcount: '560'
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
>Verifique a [Knowledge base do Dispatcher](https://helpx.adobe.com/br/experience-manager/kb/index/dispatcher.html), a [Solução de problemas de limpeza do Dispatcher](https://experienceleague.adobe.com/search.html?lang=pt-BR#q=troubleshooting%20dispatcher%20flushing%20issues&amp;sort=relevancy&amp;f:el_product=[Experience%20Manager]) e as [Perguntas frequentes sobre os problemas principais do Dispatcher](dispatcher-faq.md) para obter mais informações.

## Verifique a configuração básica {#check-the-basic-configuration}

Como sempre, as primeiras etapas são verificar as noções básicas:

* [Confirmar operação básica](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Verificar todos os arquivos de log do servidor da Web e do Dispatcher. Se necessário, aumente o `loglevel` usado para o [log](/help/using/dispatcher-configuration.md#logging) do Dispatcher.

* [Verifique sua configuração](/help/using/dispatcher-configuration.md):

   * Você tem vários Dispatchers?

      * Você determinou qual Dispatcher está lidando com o site/página que você está investigando?
   * Você implementou filtros?

      * Esses filtros estão afetando o assunto que você está investigando?


## Ferramentas de diagnóstico do IIS {#iis-diagnostic-tools}

O IIS fornece várias ferramentas de rastreamento, dependendo da versão real:

* IIS 6 - ferramentas de diagnóstico do IIS podem ser baixadas e configuradas
* IIS 7 - o rastreamento é totalmente integrado

Essas ferramentas podem ajudar você a monitorar a atividade.

## IIS e 404 Não encontrado {#iis-and-not-found}

Ao usar o IIS, o erro `404 Not Found` pode estar retornando em vários cenários. Em caso afirmativo, consulte os seguintes artigos da Knowledge base.

* [IIS 6/7: método POST retorna 404](https://helpx.adobe.com/br/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: solicitações para URLs que contêm o caminho base `/bin` retornam `404 Not Found`](https://helpx.adobe.com/br/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Verifique também se a raiz do cache do Dispatcher e a raiz do documento do IIS estão definidas para o mesmo diretório.

## Problemas ao excluir modelos de fluxos de trabalho {#problems-deleting-workflow-models}

**Sintomas**

Problemas ao tentar excluir modelos de fluxos de trabalho ao acessar uma instância de autor do AEM por meio do Dispatcher.

**Etapas a serem reproduzidas:**

1. Faça logon na sua instância do autor (confirme se as solicitações estão sendo roteadas pelo Dispatcher).
1. Crie um fluxo de trabalho; por exemplo, com o título definido como workflowToDelete.
1. Confirme se o fluxo de trabalho foi criado com êxito.
1. Selecione e clique com o botão direito do mouse no fluxo de trabalho e clique em **Excluir**.

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

Esse processo descreve como o Dispatcher interage com o `mod_dir` no servidor da Web Apache, pois isso pode levar a vários efeitos possivelmente inesperados:

### Apache 1.3 {#apache}

No Apache 1.3, `mod_dir` lida com cada solicitação em que o URL mapeia para um diretório no sistema de arquivos.

Ele irá:

* redirecionar a solicitação para um arquivo `index.html` existente
* gerar uma listagem de diretórios

Quando o Dispatcher estiver ativado, ele processará essas solicitações se registrando como um manipulador para o tipo de conteúdo `httpd/unix-directory`.

### Apache 2.x {#apache-x}

No Apache 2.x, isso é diferente. Um módulo pode lidar com diferentes estágios da solicitação, como correção de URL. O `mod_dir` lida com esse estágio redirecionando uma solicitação (quando o URL mapeia para um diretório) para o URL com uma `/` anexada.

O Dispatcher não intercepta a correção `mod_dir`, mas lida completamente com a solicitação para o URL redirecionado (ou seja, com `/` anexada). Esse processo pode causar um problema se o servidor remoto (por exemplo, o AEM) manipula as solicitações para `/a_path` de forma diferente das solicitações para `/a_path/` (quando `/a_path` mapeia para um diretório existente).

Se essa situação ocorrer, você deve:

* desativar o `mod_dir` para a árvore secundária `Directory` ou `Location` manipulada pelo Dispatcher

* usar `DirectorySlash Off` para configurar `mod_dir` para não anexar `/`
