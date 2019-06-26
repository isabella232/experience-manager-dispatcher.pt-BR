---
title: Visão geral do Dispatcher
seo-title: Visão geral do Adobe AEM Dispatcher
description: Este artigo fornece uma visão geral geral do Dispatcher.
seo-description: Este artigo fornece uma visão geral geral do Adobe Experience Manager Dispatcher.
uuid: 71766 f 86-5 e 91-446 b-a 078-061 b 179 d 090 d
pageversionid: '1193211344162'
contentOwner: Usuário
topic-tags: dispatcher
content-type: referência
discoiquuid: 1 d 449 ee 2-4 cdd -4 b 7 a -8 b 4 e -7 e 6 fc 0 a 1 d 7 ee
translation-type: tm+mt
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# Dispatcher Overview {#dispatcher-overview}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

O Dispatcher é a ferramenta de balanceamento de cache e/ou cache do Adobe Experience Manager. O uso do Dispatcher do AEM também ajuda a proteger seu servidor de AEM contra ataques. Portanto, você pode aumentar a segurança da sua instância do AEM usando o Dispatcher junto com um servidor Web de classe empresarial.

O processo de implantação de um Dispatcher independente do servidor Web e da plataforma do sistema operacional escolhido:

1. Saiba mais sobre o Dispatcher (esta página). Also, see [frequently asked questions about dispatcher](https://helpx.adobe.com/experience-manager/using/dispatcher-faq.html).
1. Install a [supported web server](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html) according to the web server documentation.

1. [Instale o módulo Dispatcher](dispatcher-install.md) no servidor Web e configure o servidor da Web de acordo.
1. [Configurar o Dispatcher](dispatcher-configuration.md) (o dispatcher. any file).

1. [Configure o AEM](page-invalidate.md) para que as atualizações de conteúdo invalidem o cache.

>[!NOTE]
>
>To gain a better undestanding of how Dispatcher works with AEM see [Ask the AEM Community Experts for July 2017](https://bit.ly/ATACE0717).

Use as seguintes informações, conforme necessário:

* [A lista de verificação de segurança do Dispatcher](security-checklist.md)
* [A Base de dados de conhecimento do Dispatcher](https://helpx.adobe.com/cq/kb/index/dispatcher.html)
* [Otimizar um site para o desempenho do cache](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html)
* [Uso do Dispatcher com vários domínios](dispatcher-domains.md)
* [Usar SSL com o Dispatcher](dispatcher-ssl.md)
* [Implementação de armazenamento em cache com diferenciação de permissão](permissions-cache.md)
* [Solução de problemas do Dispatcher](dispatcher-troubleshooting.md)
* [Perguntas frequentes sobre os problemas do Dispatcher](dispatcher-faq.md)

>[!NOTE]
>
>**O uso mais comum do Dispatcher** é armazenar as respostas em cache de uma instância **de publicação de AEM**, para aumentar a capacidade de resposta e segurança do site publicado externamente. A maior parte da discussão foca nesse caso.
>
>But, the Dispatcher can also be used to increase the responsiveness of your **author instance**, particularly if you have a large number users editing and updating your website. For details specific to this case see [Using a Dispatcher with an Author Server](#using-a-dispatcher-with-an-author-server), below.

## Why use Dispatcher to implement Caching? {#why-use-dispatcher-to-implement-caching}

Há duas abordagens básicas para a publicação na Web:

* **Servidores Web estáticos**: como Apache ou IIS, são muito simples, mas rápidos.
* **Servidores de gerenciamento de conteúdo**: que fornecem conteúdo dinâmico, em tempo real e inteligente, mas exigem muito mais tempo de computação e outros recursos.

O Dispatcher ajuda a compreender um ambiente que é rápido e dinâmico. Funciona como parte de um servidor HTML estático, como o Apache, com o objetivo de:

* armazenamento (ou «armazenamento em cache») da maior parte do conteúdo do site possível, na forma de um site estático
* acessando o mecanismo de layout o mínimo possível.

O que significa que:

* **o conteúdo** estático é tratado com exatamente a mesma velocidade e facilidade em um servidor Web estático;*além disso, você pode usar as ferramentas de administração e segurança disponíveis para seu (s) servidor (s) Web estático (s)*.

* **o conteúdo dinâmico** é gerado conforme necessário, sem desacelerar o sistema mais do que o absolutamente necessário.

O Dispatcher contém mecanismos para gerar e atualizar HTML estático com base no conteúdo do site dinâmico. Você pode especificar detalhadamente quais documentos são armazenados como arquivos estáticos e que são sempre gerados dinamicamente.

Esta seção ilustra os princípios por trás dessa seção.

### Static Web Server {#static-web-server}

![](assets/chlimage_1-3.png)

Um servidor Web estático, como Apache ou IIS, serve arquivos HTML estáticos para os visitantes do seu site. As páginas estáticas são criadas uma vez, para que o mesmo conteúdo seja entregue para cada solicitação.

Esse processo é muito simples e, portanto, extremamente eficiente. Se um visitante solicitar um arquivo (por exemplo, uma página HTML), o arquivo normalmente é retirado diretamente da memória, no pior dos casos é lido da unidade local. Os servidores Web estáticos estão disponíveis há algum tempo, portanto há uma grande variedade de ferramentas para administração e gerenciamento de segurança, e elas são muito bem integradas com infraestruturas de rede.

### Content Management Servers {#content-management-servers}

![](assets/chlimage_1-4.png)

Se você usar um servidor de gerenciamento de conteúdo, como o AEM, um mecanismo de layout avançado processará a solicitação de um visitante. O mecanismo lê o conteúdo de um repositório que, combinado com estilos, formatos e direitos de acesso, transforma o conteúdo em um documento que é ajustado às necessidades e direitos do visitante.

Isso permite criar conteúdo dinâmico e superior, o que aumenta a flexibilidade e a funcionalidade do site. No entanto, o mecanismo de layout requer mais poder de processamento do que um servidor estático, portanto essa configuração pode ser propensa a desacelerar se muitos visitantes usarem o sistema.

## How Dispatcher performs Caching {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**O diretório Cache** para armazenamento em cache, o módulo do Dispatcher usa a capacidade do servidor da Web de enviar conteúdo estático. O Dispatcher coloca os documentos em cache na raiz do documento do servidor Web.

>[!NOTE]
>
>Quando não há a configuração para armazenamento em cache HTTP, o Dispatcher armazena apenas o código HTML da página - ele não armazena os cabeçalhos HTTP. Isso pode ser um problema se você usar codificações diferentes em seu site, já que elas podem se perder. To enable HTTP Header Caching, see [Configuring the Dispatcher Cache.](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html)

>[!NOTE]
>
>Localizar a raiz do documento de seu servidor Web no armazenamento vinculado à rede (NAS) causa a degradação do desempenho. Além disso, quando uma raiz de documento localizada nas NAS é compartilhada entre vários servidores Web, os bloqueios intermitentes podem ocorrer quando as ações de replicação são executadas.

>[!NOTE]
>
>O Dispatcher armazena o documento em cache em uma estrutura igual à URL solicitada.
>
>Podem haver limitações no nível do sistema operacional para o comprimento do nome do arquivo; Ou seja, se você tiver um URL com muitos seletores.

### Métodos para armazenamento em cache

O Dispatcher tem dois métodos principais para atualizar o conteúdo do cache quando alterações são feitas no site.

* **As atualizações** de conteúdo removem as páginas que foram alteradas, bem como os arquivos que estão diretamente associados a eles.
* **A Invalidação automática** invalida automaticamente as partes do cache que podem estar desatualizadas após uma atualização. Ou seja, marca efetivamente as páginas relevantes como desatualizadas, sem excluir nada.

### Atualizações de conteúdo

Em uma atualização de conteúdo, um ou mais documentos AEM são alterados. O AEM envia uma solicitação de sincronização para o Dispatcher, que atualiza o cache de acordo:

1. Ele exclui os arquivos modificados do cache.
1. Exclui todos os arquivos que começam com a mesma alça do cache. Por exemplo, se o arquivo /en/index.html for atualizado, todos os arquivos que começam com /en/index. são excluídos. Esse mecanismo permite que você projete sites eficientes do cache, especialmente em relação às navegações de imagem.
1. *Toque* no arquivo chamado **de statfile**; isso atualiza o carimbo de data e hora do arquivo para indicar a data da última alteração.

Os pontos a seguir devem ser anotados:

* As atualizações de conteúdo são normalmente usadas em conjunto com um sistema de criação que &quot;saiba&quot; o que deve ser substituído.
* Os arquivos afetados por uma atualização de conteúdo são removidos, mas não são substituídos imediatamente. Na próxima vez que um arquivo for solicitado, o Dispatcher obterá o novo arquivo da instância do AEM e o colocará no cache, substituindo o conteúdo antigo.
* Normalmente, imagens geradas automaticamente que incorporam texto de uma página são armazenadas em arquivos de imagem que começam com a mesma alça, garantindo dessa forma que a associação exista para exclusão. Por exemplo, você pode armazenar o texto de título da página mypage.html como a imagem mypage. tif. gif na mesma pasta. Dessa forma, a imagem é excluída automaticamente do cache sempre que a página é atualizada, para que você possa garantir que a imagem sempre reflita a versão atual da página.
* Você pode ter vários arquivos, por exemplo, uma pasta por idioma. If a page is updated, AEM looks for the next parent folder containing a statfile, and *touches* that file.

### Invalidação automática

A invalidação automática invalida automaticamente partes do cache, sem excluir fisicamente quaisquer arquivos. Em cada atualização de conteúdo, o arquivo chamado de statfile é tocado, portanto seu carimbo de data e hora reflete a última atualização de conteúdo.

O Dispatcher tem uma lista de arquivos que estão sujeitos a invalidação automática. Quando um documento dessa lista é solicitado, o Dispatcher compara a data do documento em cache com o carimbo de data e hora do statfile:

* se o documento em cache for mais recente, o Dispatcher o retornará.
* se for mais antiga, o Dispatcher recupera a versão atual da instância do AEM.

Novamente, alguns pontos devem ser anotados:

* A invalidação automática é normalmente usada quando as inter-relações são complexas, por exemplo, para páginas HTML. Essas páginas contêm links e entradas de navegação, portanto, geralmente elas precisam ser atualizadas após uma atualização de conteúdo. Se tiver gerado arquivos PDF ou de imagem automaticamente, você pode optar por invalidar automaticamente esses arquivos.
* A invalidação automática não envolve nenhuma ação pelo dispatcher no momento da atualização, exceto para tocar no arquivo. Entretanto, tocar no arquivo renderiza automaticamente o conteúdo do cache obsoleto, sem removê-lo fisicamente do cache.

## How Dispatcher returns Documents {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Determinar se um documento está sujeito a cache

You can [define which documents the Dispatcher caches in the configuration file](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html). O Dispatcher verifica a solicitação na lista de documentos armazenados em cache. Se o documento não estiver nesta lista, o Dispatcher solicita o documento da instância do AEM.

The Dispatcher *always* requests the document directly from the AEM instance in the following cases:

* Se a URI de solicitação contiver um ponto de interrogação &quot;?&quot;. Isso geralmente indica uma página dinâmica, como um resultado de pesquisa, que não precisa ser armazenada em cache.
* A extensão do arquivo está ausente. O servidor Web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (isto pode ser configurado)

>[!NOTE]
>
>Os métodos GET ou HEAD (para os cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. For additional information on response header caching, see the [Caching HTTP Response Headers](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html) section.

### Determinar se um documento está em cache

O Dispatcher armazena os arquivos em cache no servidor da Web como se fizessem parte de um site estático. Se um usuário solicitar um documento que pode ser armazenado em cache, o Dispatcher verificará se este documento existe no sistema de arquivos do servidor da Web:

* se o documento for armazenado em cache, o Dispatcher retornará o arquivo.
* se não for armazenado em cache, o Dispatcher solicita o documento da instância do AEM.

### Determinar se um documento está atualizado

Para saber se um documento está atualizado, o Dispatcher executa duas etapas:

1. Verifica se o documento está sujeito a invalidação automática. Caso contrário, o documento será considerado atualizado.
1. Se o documento estiver configurado para invalidação automática, o Dispatcher verifica se ele é mais antigo ou mais recente do que a última alteração disponível. Se for mais antiga, o Dispatcher solicita a versão atual da instância do AEM e substitui a versão no cache.

>[!NOTE]
>
>Documents without **auto-invalidation** remain in the cache until they are physically deleted; e.g. by a content update on the web site.

## The Benefits of Load Balancing {#the-benefits-of-load-balancing}

O balanceamento de carga é a prática da distribuição do carregamento computacional do site em várias instâncias do AEM.

![](assets/chlimage_1-7.png)

Você obtém:

* **aumentar a potência
de processamento** na prática Isso significa que o Dispatcher compartilha solicitações de documentos entre várias instâncias do AEM. Como cada instância agora tem menos documentos para processar, você tem tempos de resposta mais rápidos. O Dispatcher mantém estatísticas internas para cada categoria de documento, para que possa estimar a carga e distribuir as consultas de forma eficiente.

* **cobertura
segura de falha de reprovação** Se o Dispatcher não receber respostas de uma instância, ele automaticamente transmitirá solicitações para uma das outras instâncias. Assim, se uma instância ficar indisponível, o único efeito será um desaceleração do site, proporcional à potência computacional perdida. No entanto, todos os serviços continuarão.

* também é possível gerenciar sites diferentes no mesmo servidor estático da Web.

>[!NOTE]
>
>Enquanto o balanceamento de carga espalha a carga eficiente, o armazenamento em cache ajuda a reduzir a carga. Portanto, tente otimizar o armazenamento em cache e reduzir a carga geral antes de configurar o balanceamento de carga. O cache bom pode aumentar o desempenho do balanceador de carga ou o balanceamento de carga de renderização desnecessário.

>[!CAUTION]
>
>Embora um único Dispatcher geralmente consiga saturar a capacidade das instâncias de Publicação disponíveis, para alguns aplicativos raros pode fazer sentido equilibrar adicionalmente a carga entre duas instâncias do Dispatcher. Configurações com vários Dispatchers precisam ser consideradas cuidadosamente, já que um Dispatcher adicional aumentará a carga nas instâncias de Publicação disponíveis e pode reduzir o desempenho na maioria dos aplicativos.

## How the Dispatcher performs Load Balancing {#how-the-dispatcher-performs-load-balancing}

### Estatísticas de desempenho

O Dispatcher mantém estatísticas internas sobre o quão rápido cada instância do AEM processa documentos. Com base nesses dados, o Dispatcher estima qual instância fornecerá o tempo de resposta mais rápido ao responder uma solicitação e, portanto, reserva o tempo de computação necessário nessa instância.

Tipos diferentes de solicitações podem ter um tempo de conclusão médio diferente, por isso o Dispatcher permite que você especifique categorias de documento. Em seguida, são considerados ao calcular as estimativas de tempo. Por exemplo, você pode fazer uma distinção entre páginas HTML e imagens, já que os tempos de resposta típicos podem ser muito diferentes.

Se você usar uma função de pesquisa elaborada, poderá criar uma nova categoria para consultas de pesquisa. Isso ajuda o Dispatcher a enviar consultas de pesquisa à instância que responde mais rápido. Isso impede que uma instância mais lenta pare quando recebe várias consultas de pesquisa &quot;caras&quot;, enquanto as outras recebem solicitações &quot;mais baratos&quot;.

### Conteúdo personalizado (conexões aderentes)

Conexões adesivas garantem que os documentos para um usuário sejam compostos na mesma instância do AEM. Isso é importante se você usar páginas personalizadas e dados de sessão. Os dados são armazenados na instância, portanto, as solicitações subsequentes do mesmo usuário devem retornar àquela instância ou os dados serão perdidos.

Como as conexões aderentes limitam a capacidade do Dispatcher de otimizar as solicitações, você deve usá-las apenas quando necessário. Você pode especificar a pasta que contém os documentos &quot;aderentes&quot;, assegurando que todos os documentos nessa pasta sejam compostos na mesma instância para cada usuário.

>[!NOTE]
>
>Para a maioria das páginas que usam conexões adesivas, é necessário desativar o armazenamento em cache; caso contrário, a página será semelhante a todos os usuários, independentemente do conteúdo da sessão.
>
>For a *few* applications, it can be possible to use both sticky connections and caching; for example, if you display a form that writes data to the session.

## Using Multiple Dispatchers {#using-multiple-dispatchers}

Em configurações complexas, você pode usar vários Dispatchers. Por exemplo, você pode usar:

* um Dispatcher para publicar um site na Intranet
* um segundo Dispatcher, em um endereço diferente e com configurações de segurança diferentes, para publicar o mesmo conteúdo na Internet.

Nesse caso, verifique se cada solicitação passa por apenas um Dispatcher. Um Dispatcher não trata solicitações que vêm de outro Dispatcher. Portanto, certifique-se de que os Dispatchers acessam diretamente o site do AEM.

## Using Dispatcher with a CDN {#using-dispatcher-with-a-cdn}

Uma rede de fornecimento de conteúdo (CDN), como Akamai Edge Delivery ou Amazon Cloud Front, disponibiliza conteúdo de um local próximo ao usuário final. Por que

* acelerar tempos de resposta para usuários finais
* carregar os servidores

Como um componente de infraestrutura HTTP, um CDN funciona como o Dispatcher: quando um nó CDN recebe uma solicitação, ele serve a solicitação de seu cache, se possível (o recurso está disponível no cache e é válido). Caso contrário, chega ao próximo servidor mais próximo para recuperar o recurso e armazená-lo em cache para solicitações adicionais, se apropriado.

O &quot;servidor próximo&quot; depende da configuração específica. Por exemplo, em uma configuração do Akamai, a solicitação pode seguir o seguinte caminho:

* O Nó do Akamai Edge
* A camada Akamai Midgres
* Seu firewall
* Seu balanceador de carga
* Dispatcher
* AEM

Na maioria dos casos, o Dispatcher é o próximo servidor que pode servir o documento de um cache e influenciar os cabeçalhos de resposta retornados ao servidor CDN.

## Controlling a CDN cache {#controlling-a-cdn-cache}

Há um numérico de maneiras de controlar por quanto tempo um CDN fará o cache de um recurso antes que recupere novamente é do Dispatcher.

1. Configuração explícita\
   Configure, por quanto tempo os recursos específicos são mantidos no cache do CDN, dependendo do tipo mime, extensão, tipo de solicitação, etc.

1. Cabeçalhos de controle de expiração e cache\
   Most CDNs will honor `Expires:` and `Cache-Control:` HTTP Headers if sent by the upstream server. This can be achieved e.g. by using the [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) Apache Module.

1. Invalidação manual\
   Os cdns permitem que os recursos sejam removidos do cache por meio de interfaces da Web.
1. Invalidação baseada em API\
   A maioria dos cdns também oferecem uma API REST e/ou SOAP que permite a remoção de recursos do cache.

Em uma configuração AEM típica, a configuração por extensão e/ou caminho, que pode ser alcançada através dos pontos 1 e 2 acima, oferece possibilidades para definir períodos de armazenamento em cache razoáveis para recursos com frequência usados que não mudam frequentemente, como imagens de design e bibliotecas cliente. Quando novas versões são implantadas, normalmente é necessária uma invalidação manual.

Se essa abordagem for usada para armazenar o conteúdo gerenciado em cache, isso significa que as alterações de conteúdo estarão visíveis somente para os usuários finais depois que o período de armazenamento em cache configurado expirar e o documento será obtido do Dispatcher novamente.

Para controle refinado, a autenticação baseada em API permite invalidar o cache de um CDN como o cache do Dispatcher é invalidado. Based on the CDNs API, you can implement your own [ContentBuilder](https://docs.adobe.com/docs/en/cq/current/javadoc/com/day/cq/replication/ContentBuilder.html) and [TransportHandler](https://docs.adobe.com/docs/en/cq/current/javadoc/com/day/cq/replication/TransportHandler.html) (if the API is not REST-based) and set up a Replication Agent that will use these to invalidate the CDN&#39;s cache.

>[!NOTE]
>
>See also [AEM (CQ) Dispatcher Security and CDN+Browser Caching](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) and recorded presentation on [Dispatcher Caching](https://docs.adobe.com/content/ddc/en/gems/dispatcher-caching---new-features-and-optimizations.html).

## Using a Dispatcher with an Author Server {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>if you are using [AEM with Touch UI](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html) you should **not** cache author instance content. Se o armazenamento em cache foi ativado para a instância do autor, é necessário desativá-lo e excluir o conteúdo do diretório do cache. To disable caching, you should edit the `author_dispatcher.any` file and modify the `/rule` property of the `/cache` section as follows:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Um Dispatcher pode ser usado na frente de uma instância do autor para melhorar o desempenho de criação. Para configurar um Dispatcher de criação, faça o seguinte:

1. Install a Dispatcher in a web server (this could be Apache or IIS web server, see [Installing Dispatcher](dispatcher-install.md)).
1. Você pode testar o Dispatcher instalado recentemente em relação a uma instância de publicação de AEM trabalhando, a fim de garantir que a instalação correta tenha sido concluída corretamente.
1. Agora certifique-se de que o Dispatcher possa conectar-se via TCP/IP à sua instância do autor.
1. Replace the sample dispatcher.any file with the author_dispatcher.any file provided with the [Dispatcher download](release-notes.md#downloads).
1. Open the `author_dispatcher.any` in a text editor and make the following changes:

   1. Change the `/hostname` and `/port` of the `/renders` section to point to your author instance.
   1. Change the `/docroot` of the `/cache` section to point to a cache directory. In case you are using [AEM with Touch UI](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html), see the warning above.
   1. Salve as alterações.

1. Delete all existing files in the `/cache` &gt; `/docroot` directory which you configured above.
1. Reinicie o servidor da Web.

>[!NOTE]
>
>Please note that with the provided `author_dispatcher.any` configuration, when you install a CQ5 feature pack, hotfix, or application code package that affects any content under `/libs` or `/apps` then you must delete the cached files under those directories in your dispatcher cache to ensure that the next time they are requested the newly upgraded files are fetched, and not the old cached ones.

>[!CAUTION]
>
>If you have used the previously configured author dispatcher and enabled a *dispatcher flushing agent* then please do the following:

1. Delete or disable the **author dispatcher&#39;s** flushing agent on your AEM author instance.
1. Faça a configuração do dispatcher novamente seguindo as novas instruções acima.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
