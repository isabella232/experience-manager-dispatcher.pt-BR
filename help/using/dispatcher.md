---
title: Visão geral do Dispatcher
seo-title: Adobe AEM Dispatcher Overview
description: Saiba como usar o Dispatcher para melhorar a segurança, o armazenamento em cache e muito mais no AEM Cloud Services.
seo-description: This article provides a general overview of Adobe Experience Manager Dispatcher.
uuid: 71766f86-5e91-446b-a078-061b179d090d
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: 1d449ee2-4cdd-4b7a-8b4e-7e6fc0a1d7ee
exl-id: c9266683-6890-4359-96db-054b7e856dd0
source-git-commit: 76f7a3fc8d98657c5f46912f452c05fce1b5be61
workflow-type: tm+mt
source-wordcount: '3190'
ht-degree: 99%

---

# Visão geral do Dispatcher {#dispatcher-overview}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

O Dispatcher é uma ferramenta de armazenamento em cache e/ou balanceamento de carga do Adobe Experience Manager, que pode ser usado em conjunto com um servidor Web de classe empresarial.

O processo de implantação do Dispatcher é independente do servidor Web e da plataforma de SO escolhida:

1. Saiba mais sobre o Dispatcher (nesta página). Além disso, consulte as [perguntas frequentes sobre o Dispatcher](https://helpx.adobe.com/br/experience-manager/using/dispatcher-faq.html).
1. Instale um [servidor Web compatível](https://helpx.adobe.com/br/experience-manager/6-3/sites/deploying/using/technical-requirements.html) de acordo com a documentação do servidor Web.
1. [Instale o módulo Dispatcher](dispatcher-install.md) em seu servidor Web e configure o servidor Web apropriadamente.
1. [Configurar o Dispatcher](dispatcher-configuration.md) (qualquer arquivo no dispatcher).
1. [Configure o AEM](page-invalidate.md) para que atualizações de conteúdo invalidem o cache.

>[!NOTE]
>
>Para entender melhor como o Dispatcher funciona com o AEM:
>
>* Consulte [Pergunte aos especialistas da comunidade AEM, de julho de 2017](https://bit.ly/ATACE0717).
>* Acesse [este repositório](https://github.com/adobe/aem-dispatcher-experiments). Ele contém uma coleção de experiências num formato de laboratório &quot;móvel&quot;.



Use as seguintes informações conforme necessário:

* [Lista de verificação de segurança do Dispatcher](security-checklist.md)
* [Base de conhecimento do Dispatcher](https://helpx.adobe.com/pt/experience-manager/kb/index/dispatcher.html)
* [Otimização de um site para desempenho de cache](https://helpx.adobe.com/br/experience-manager/6-4/sites/deploying/using/configuring-performance.html)
* [Utilização do Dispatcher com vários domínios](dispatcher-domains.md)
* [Utilização do SSL com o Dispatcher](dispatcher-ssl.md)
* [Implementação de armazenamento em cache sensível a permissões](permissions-cache.md)
* [Resolução de problemas do Dispatcher](dispatcher-troubleshooting.md)
* [Perguntas frequentes sobre os principais problemas do Dispatcher](dispatcher-faq.md)

>[!NOTE]
>
>**O uso mais comum do Dispatcher** é armazenar em cache as respostas de uma **instância de publicação** do AEM, para aumentar a capacidade de resposta e a segurança de seu site publicado voltado para o exterior. A maior parte da discussão se concentra nesse caso.
>
>Mas o Dispatcher também pode ser usado para aumentar a capacidade de resposta da instância do seu **autor**, principalmente se você tiver um grande número de usuários editando e atualizando seu site. Para obter detalhes específicos deste caso, consulte [Uso de um Dispatcher com um servidor de autor](#using-a-dispatcher-with-an-author-server), abaixo.

## Por que usar o Dispatcher para implementar o armazenamento em cache? {#why-use-dispatcher-to-implement-caching}

Há duas abordagens básicas para publicação na Web:

* **Servidores Web estáticos**: como Apache ou IIS, são muito simples, mas rápidos.
* **Servidores**: que fornecem conteúdo dinâmico, em tempo real e inteligente, mas exigem muito mais tempo de computação e outros recursos.

O Dispatcher ajuda a realizar um ambiente rápido e dinâmico. Funciona como parte de um servidor HTML estático, como o Apache, com o objetivo de:

* armazenar (ou &quot;armazenar em cache&quot;) o máximo possível do conteúdo do site, na forma de um site estático
* acesso ao mecanismo de layout o mínimo possível.

O que significa que:

* **o conteúdo** estático é manipulado com a mesma velocidade e facilidade de um servidor Web estático;*além disso, você pode usar as ferramentas de administração e segurança disponíveis para seus servidores Web estáticos*.

* **o conteúdo** dinâmico é gerado conforme necessário, sem retardar o sistema além do absolutamente necessário.

O Dispatcher contém mecanismos para gerar e atualizar HTML estático com base no conteúdo do site dinâmico. Você pode especificar em detalhes quais documentos são armazenados como arquivos estáticos e quais são sempre gerados dinamicamente.

A presente seção ilustra os princípios subjacentes a este fato.

### Servidor Web estático {#static-web-server}

![](assets/chlimage_1-3.png)

Um servidor Web estático, como o Apache ou o IIS, serve arquivos HTML estáticos para os visitantes do seu site. As páginas estáticas são criadas uma vez, portanto, o mesmo conteúdo será entregue para cada solicitação.

Este processo é muito simples e, portanto, extremamente eficiente. Se um visitante solicitar um arquivo (por exemplo, uma página HTML), o arquivo normalmente é retirado diretamente da memória, na pior das hipóteses, é lido da unidade local. Os servidores Web estáticos estão disponíveis há bastante tempo, portanto há uma grande variedade de ferramentas para administração e gerenciamento de segurança, e eles estão muito bem integrados às infraestruturas de rede.

### Servidores de gerenciamento de conteúdo {#content-management-servers}

![](assets/chlimage_1-4.png)

Se você usar um Servidor de gerenciamento de conteúdo, como o AEM, um mecanismo de layout avançado processa a solicitação de um visitante. O mecanismo lê o conteúdo de um repositório que, combinado com estilos, formatos e direitos de acesso, transforma o conteúdo em um documento adaptado às necessidades e direitos do visitante.

Isso permite que você crie conteúdo dinâmico e mais rico, o que aumenta a flexibilidade e funcionalidade do seu site. No entanto, o mecanismo de layout requer mais potência do que um servidor estático, portanto, essa configuração pode ser sujeita a lentidão se muitos visitantes usarem o sistema.

## Como o Dispatcher executa o armazenamento em cache {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png)

**O diretório** de cache para armazenamento em cache, o módulo Dispatcher, usa a capacidade do servidor Web de fornecer conteúdo estático. O Dispatcher coloca os documentos em cache na raiz do documento do servidor web.

>[!NOTE]
>
>Quando falta a configuração para o HTTP Header Caching, o Dispatcher armazena apenas o código HTML da página - não armazena os cabeçalhos HTTP. Isso pode ser um problema se você usar diferentes codificações dentro do seu website, pois elas podem se perder. Para permitir o HTTP Header Caching, veja [Configuração do Cache do Dispatcher.](https://helpx.adobe.com/pt/experience-manager/dispatcher/using/dispatcher-configuration.html)

>[!NOTE]
>
>Localizar o documento-raiz do seu servidor Web no armazenamento anexo à rede (NAS) causa degradação de desempenho. Da mesma forma, quando um documento-raiz que está localizado no NAS é compartilhado entre vários servidores Web, bloqueios intermitentes podem ocorrer quando ações de replicação são realizadas.

>[!NOTE]
>
>O Dispatcher armazena o documento em cache em uma estrutura igual ao URL solicitado.
>
>Pode haver limitações a nível de SO para o comprimento do nome do arquivo; por exemplo, se você tem um URL com muitos seletores.

### Métodos de cache

O Dispatcher tem dois métodos primários para atualizar o conteúdo de cache quando mudanças forem feitas ao website.

* **As Atualizações de conteúdo** removem as páginas que foram alteradas, bem como os arquivos que estão diretamente associados a elas.
* **A invalidação automática** invalida automaticamente as partes do cache que podem estar desatualizadas após uma atualização. Por exemplo, eficientemente marcar páginas relevantes como sendo desatualizadas, sem excluir nada.

### Atualizações de conteúdo

Em uma atualização de conteúdo, um ou mais documentos do AEM são alterados. O AEM envia uma solicitação de agregação para o Dispatcher, que atualiza o cache de acordo:

1. Ele exclui os arquivos modificados do cache.
1. Ele exclui do cache todos os arquivos que iniciam com o mesmo identificador. Por exemplo, se o arquivo /pt_br/index.html for atualizado, todos os arquivos que começam com /pt_br/index. são excluídos. Esse mecanismo permite que você crie sites eficientes em cache, especialmente em relação à navegação de imagens.
1. Ela *toca* o chamado **Arquivo de estado**, Isso atualiza o carimbo de data e hora do arquivo de status para indicar a data da última alteração.

É de salientar os seguintes pontos:

* As Atualizações de conteúdo geralmente são usadas em conjunto com um sistema de criação que &quot;sabe&quot; o que deve ser substituído.
* Os arquivos afetados por uma atualização de conteúdo são removidos, mas não são substituídos imediatamente. Na próxima vez que um arquivo for solicitado, o Dispatcher buscará o novo arquivo da instância do AEM e o colocará no cache, substituindo assim o conteúdo antigo.
* Normalmente, as imagens geradas automaticamente que incorporam texto de uma página são armazenadas em arquivos de imagem que começam com o mesmo identificador - garantindo assim que a associação exista para exclusão. Por exemplo, você pode armazenar o texto do título da página mypage.html como a imagem mypage.titlePicture.gif na mesma pasta. Desta forma, a imagem é automaticamente eliminada do cache sempre que a página é atualizada, para que possa ter a certeza de que a imagem reflete sempre a versão atual da página.
* Você pode ter vários arquivos de status, por exemplo, um por pasta de idioma. Se uma página for atualizada, o AEM procurará a próxima pasta pai que contém um arquivo de status e *tocará* esse arquivo.

### Invalidação automática

A invalidação automática invalida automaticamente partes do cache - sem excluir fisicamente quaisquer arquivos. Em cada atualização de conteúdo, o chamado arquivo de status é tocado, portanto, seu carimbo de data e hora reflete a última atualização de conteúdo.

O Dispatcher tem uma lista de arquivos que estão sujeitos à invalidação automática. Quando um documento dessa lista é solicitado, o Dispatcher compara a data do documento em cache com o carimbo de data e hora do arquivo de status:

* se o documento em cache for mais recente, o Dispatcher o retornará.
* se for mais antigo, o Dispatcher recuperará a versão atual da instância do AEM.

Mais uma vez, é de salientar alguns pontos:

* Normalmente, a invalidação automática é usada quando as inter-relações são complexas, por exemplo, para páginas HTML. Essas páginas contêm links e entradas de navegação, portanto, elas geralmente precisam ser atualizadas após uma atualização de conteúdo. Se você tem arquivos de PDF ou figuras automaticamente gerados, pode escolher a invalidação automática também para eles.
* A invalidação automática não envolve nenhuma ação do despachante no momento da atualização, exceto para tocar no arquivo de status. Entretanto, tocar no arquivo de status torna automaticamente o conteúdo do cache obsoleto, sem removê-lo fisicamente do cache.

## Como o Dispatcher retorna documentos {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Determinar se um documento está sujeito ao armazenamento em cache

Você pode [definir quais documentos o Dispatcher armazena em cache no arquivo de configuração](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html). O Dispatcher verifica a solicitação em relação à lista de documentos que podem ser armazenados em cache. Se o documento não estiver nessa lista, o Dispatcher solicitará o documento da instância do AEM.

O Dispatcher *sempre* solicita o documento diretamente da instância do AEM nos seguintes casos:

* Se o URI da solicitação contiver um ponto de interrogação &quot;?&quot;. Isso geralmente indica uma página dinâmica, como um resultado de pesquisa, que não precisa ser armazenada em cache.
* A extensão do arquivo está ausente. O servidor Web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (isso pode ser configurado)

>[!NOTE]
>
>Os métodos GET ou HEAD (para o cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. Para obter informações adicionais sobre o armazenamento em cache do cabeçalho de resposta, consulte a seção [Armazenamento em cache de cabeçalhos de resposta HTTP](https://helpx.adobe.com/experience-manager/dispatcher/using/dispatcher-configuration.html).

### Determinar se um documento está em cache

O Dispatcher armazena os arquivos em cache no servidor Web como se fossem parte de um site estático. Se um usuário solicitar um documento armazenável em cache, o Dispatcher verificará se esse documento existe no sistema de arquivos do servidor Web:

* se o documento estiver em cache, o Dispatcher retornará o arquivo.
* se não estiver em cache, o Dispatcher solicitará o documento da instância do AEM.

### Como determinar se um documento está atualizado

Para descobrir se um documento está atualizado, o Dispatcher executa duas etapas:

1. Verifica se o documento está sujeito a invalidação automática. Caso contrário, o documento será considerado atualizado.
1. Se o documento estiver configurado para invalidação automática, o Dispatcher verificará se ele é mais antigo ou mais recente do que a última alteração disponível. Se for mais antigo, o Dispatcher solicitará a versão atual da instância do AEM e substituirá a versão no cache.

>[!NOTE]
>
>Os documentos sem invalidação **automática** permanecem no cache até serem fisicamente excluídos; por exemplo, por uma atualização de conteúdo no site.

## Os benefícios do balanceamento de carga {#the-benefits-of-load-balancing}

Balanceamento de carga é a prática de distribuir a carga computacional do site em várias instâncias do AEM.

![](assets/chlimage_1-7.png)

Você ganha:

* **maior poder de processamento** Na prática, isso significa que o Dispatcher compartilha solicitações de documento entre várias instâncias do AEM. Como cada instância agora tem menos documentos para processar, você tem tempos de resposta mais rápidos. O Dispatcher mantém estatísticas internas para cada categoria de documento, de modo que ele possa estimar o carregamento e distribuir as consultas com eficiência.

* **maior cobertura à prova de falhas** Se o Dispatcher não receber respostas de uma instância, ele retornará automaticamente solicitações para uma das outras instâncias. Assim, se uma instância se tornar indisponível, o único efeito é uma desaceleração do site, proporcional à potência computacional perdida. No entanto, todos os serviços continuarão.

* você também pode gerenciar sites diferentes no mesmo servidor Web estático.

>[!NOTE]
>
>Enquanto o balanceamento de carga espalha a carga com eficiência, o cache ajuda a reduzir a carga. Portanto, tente otimizar o cache e reduzir a carga geral antes de configurar o balanceamento de carga. Um bom cache pode aumentar o desempenho do balanceador de carga ou tornar desnecessário o balanceamento de carga.

>[!CAUTION]
>
>Embora um único Dispatcher possa normalmente saturar a capacidade das instâncias de Publicação disponíveis, para alguns aplicativos raros, pode fazer sentido balancear adicionalmente a carga entre duas instâncias do Dispatcher. As configurações com vários Dispatchers precisam ser consideradas com cuidado, já que um Dispatcher adicional aumentará a carga nas instâncias de Publicação disponíveis e poderá facilmente diminuir o desempenho na maioria dos aplicativos.

## Como o Dispatcher executa o balanceamento de carga {#how-the-dispatcher-performs-load-balancing}

### Estatísticas de desempenho

O Dispatcher mantém estatísticas internas sobre a velocidade com que cada instância do AEM processa documentos. Com base nesses dados, o Dispatcher estima qual instância fornecerá o tempo de resposta mais rápido ao responder uma solicitação e, portanto, reserva o tempo de cálculo necessário para essa instância.

Tipos diferentes de solicitações podem ter tempos médios diferentes de conclusão, de modo que o Dispatcher permite que você especifique categorias de documento. Eles são considerados ao calcular as estimativas de tempo. Por exemplo, você pode fazer uma distinção entre páginas HTML e imagens, já que os tempos de resposta típicos podem ser diferentes.

Se você usar uma função de pesquisa elaborada, poderá criar uma nova categoria para consultas de pesquisa. Isso ajuda o Dispatcher a enviar consultas de pesquisa para a instância que responde mais rapidamente. Isso impede que uma instância mais lenta seja paralisada quando recebe várias consultas de pesquisa &quot;caras&quot;, enquanto as outras recebem as solicitações &quot;mais baratas&quot;.

### Conteúdo personalizado (conexões adesivas)

As conexões adesivas garantem que os documentos de um usuário sejam todos compostos na mesma instância do AEM. Isso é importante se você usar páginas personalizadas e dados de sessão. Os dados são armazenados na instância, portanto as solicitações subsequentes do mesmo usuário devem retornar a essa instância ou os dados são perdidos.

Como as conexões aderentes restringem a capacidade do Dispatcher de otimizar as solicitações, você deve usá-las somente quando necessário. Você pode especificar a pasta que contém os documentos &quot;fixos&quot;, garantindo que todos os documentos dessa pasta sejam compostos na mesma instância para cada usuário.

>[!NOTE]
>
>Para a maioria das páginas que usam conexões adesivas, é necessário desligar o cache; caso contrário, a página será a mesma para todos os usuários, independentemente do conteúdo da sessão.
>
>Para *algumas* aplicações, pode ser possível utilizar ligações aderentes e armazenamento em cache; por exemplo, se você exibir um formulário que grava dados na sessão.

## Uso de vários Dispatchers {#using-multiple-dispatchers}

Em configurações complexas, você pode usar vários Dispatchers. Por exemplo, você pode usar:

* um Dispatcher para publicar um site na Intranet
* um segundo Dispatcher, em um endereço diferente e com configurações de segurança diferentes, para publicar o mesmo conteúdo na Internet.

Nesse caso, verifique se cada solicitação passa por apenas um Dispatcher. Um Dispatcher não lida com solicitações provenientes de outro Dispatcher. Portanto, verifique se ambos os Dispatchers acessam o site do AEM diretamente.

## Uso do Dispatcher com um CDN {#using-dispatcher-with-a-cdn}

Uma rede de entrega de conteúdo (CDN), como Akamai Edge Delivery ou Amazon Cloud Front, fornece conteúdo de um local próximo ao usuário final. Assim,

* acelera os tempos de resposta para usuários finais
* e carrega seus servidores

Como um componente de infraestrutura HTTP, um CDN funciona como o Dispatcher: quando um nó CDN recebe uma solicitação, ele serve a solicitação de seu cache, se possível (o recurso está disponível no cache e é válido). Caso contrário, ele chegará ao próximo servidor mais próximo para recuperar o recurso e armazená-lo em cache para outras solicitações, se apropriado.

O &quot;próximo servidor mais próximo&quot; depende de sua configuração específica. Por exemplo, em uma configuração do Akamai, a solicitação pode seguir o seguinte caminho:

* O nó Akamai Edge
* A Camada Akamai Midgres
* Seu firewall
* Seu balanceador de carga
* Dispatcher
* AEM

Na maioria dos casos, o Dispatcher é o próximo servidor que pode servir o documento de um cache e influenciar os cabeçalhos de resposta retornados ao servidor CDN.

## Controle de um cache CDN {#controlling-a-cdn-cache}

Há várias maneiras de controlar por quanto tempo um CDN armazenará em cache um recurso antes que ele seja obtido novamente no Dispatcher.

1. Configuração explícita\
   Configure por quanto tempo os recursos específicos são mantidos no cache do CDN, dependendo do tipo mime, extensão, tipo de solicitação etc.

1. Cabeçalhos de expiração e controle de cache\
   A maioria dos CDNs respeitará `Expires:` e os `Cache-Control:` HTTP Headers serão enviados pelo servidor upstream. Isso pode ser feito, por exemplo, usando o Módulo Apache [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html).

1. Invalidação manual\
   As CDNs permitem que os recursos sejam removidos do cache por meio de interfaces da Web.
1. Invalidação baseada em API\
   A maioria das CDNs também oferece uma REST e/ou SOAP API que permite a remoção de recursos do cache.

Em uma configuração típica do AEM, a configuração por extensão e/ou caminho, que pode ser alcançada através dos pontos 1 e 2 acima, oferece possibilidades de definir períodos razoáveis de cache para recursos frequentemente usados que não são alterados com frequência, como imagens de design e bibliotecas de clientes. Quando novas versões são implantadas, geralmente é necessária uma invalidação manual.

Se essa abordagem for usada para armazenar em cache conteúdo gerenciado, isso implica que as alterações de conteúdo só estarão visíveis para os usuários finais depois que o período de cache configurado expirar e o documento for buscado do Dispatcher novamente.

Para obter um controle mais refinado, a invalidação com base em API permite invalidar o cache de um CDN quando o cache do Dispatcher for invalidado. Com base na API de CDNs, você pode implementar seu próprio [ContentBuilder](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/day/cq/replication/ContentBuilder.html) e [TransportHandler](https://helpx.adobe.com/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/day/cq/replication/TransportHandler.html), se a API não for baseada em REST, e configurar um Agente de Replicação que os usará para invalidar o cache do CDN.

>[!NOTE]
>
>Consulte também [Segurança do AEM (CQ) Dispatcher e Armazenamento em cache de CDN+Browser](https://www.slideshare.net/andrewmkhoury/dispatcher-caching-aemgemspart2jan2015) e uma apresentação gravada sobre [Armazenamento em cache do Dispatcher](https://docs.adobe.com/content/ddc/en/gems/dispatcher-caching---new-features-and-optimizations.html).

## Uso de um Dispatcher com um Servidor de Autores {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>se você estiver usando o [AEM com Touch UI](https://helpx.adobe.com/br/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html), **não** armazene em cache o conteúdo da instância do autor. Se o cache tiver sido ativado para a instância do autor, será necessário desativá-lo e excluir o conteúdo do diretório do cache. Para desativar o armazenamento em cache, edite o `author_dispatcher.any` arquivo e modifique a propriedade `/rule` da `/cache` seção da seguinte maneira:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

Um Dispatcher pode ser usado na frente de uma instância do autor para melhorar o desempenho da criação. Para configurar um Dispatcher de criação, faça o seguinte:

1. Instale um Dispatcher em um servidor Web (pode ser o Apache ou o IIS, consulte [Instalação do Dispatcher](dispatcher-install.md)).
1. Você pode testar o Dispatcher recém-instalado em relação a uma instância de publicação do AEM em funcionamento, para garantir que uma instalação correta da linha de base tenha sido atingida.
1. Agora, verifique se o Dispatcher consegue se conectar via TCP/IP à sua instância do autor.
1. Substitua o arquivo dispatcher.any de amostra pelo arquivo author_dispatcher.any fornecido com o download do [Dispatcher](release-notes.md#downloads).
1. Abra o `author_dispatcher.any` em um editor de texto e faça as seguintes alterações:

   1. Altere o `/hostname` e `/port` da `/renders` seção para apontar para a instância do autor.
   1. Altere o `/docroot` da `/cache` seção para apontar para um diretório de cache. Caso esteja usando o [AEM com Touch UI](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/touch-ui-concepts.html), consulte o aviso acima.
   1. Salve as alterações.

1. Exclua todos os arquivos existentes no diretório `/cache` > `/docroot` que você configurou acima.
1. Reinicie o servidor Web.

>[!NOTE]
>
>Observe que, com a `author_dispatcher.any` configuração fornecida, ao instalar um pacote de recursos, uma correção ou um pacote de código de aplicativo do CQ5 que afeta qualquer conteúdo sob `/libs` ou `/apps` você deve excluir os arquivos em cache nesses diretórios no cache do dispatcher para garantir que, na próxima vez que forem solicitados, os arquivos recém-atualizados sejam buscados e não os arquivos em cache antigos.

>[!CAUTION]
>
>Se você tiver usado o Dispatcher do autor previamente configurado e ativado um *agente de limpeza do dispatcher*, faça o seguinte:

1. Exclua ou desative o agente de limpeza do **Dispatcher do autor** na instância do autor do AEM.
1. Refaça a configuração do Dispatcher do autor seguindo as novas instruções acima.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
