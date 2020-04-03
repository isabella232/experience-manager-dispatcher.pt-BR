---
title: Configurando o Dispatcher
seo-title: Configurando o Dispatcher
description: Saiba como configurar o Dispatcher.
seo-description: Saiba como configurar o Dispatcher.
uuid: 253ef0f7-2491-4cec-ab22-97439df29fd6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: aeffee8e-bb34-42a7-9a5e-b7d0e848391a
translation-type: tm+mt
source-git-commit: 183131dec51b67e152a8660c325ed980ae9ef458

---


# Configurando o Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

As seções a seguir descrevem como configurar vários aspectos do Dispatcher.

## Suporte para IPv4 e IPv6 {#support-for-ipv-and-ipv}

Todos os elementos do AEM e do Dispatcher podem ser instalados em redes IPv4 e IPv6. Consulte [IPV4 e IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Arquivos de configuração do Dispatcher {#dispatcher-configuration-files}

Por padrão, a configuração do Dispatcher é armazenada no arquivo de `dispatcher.any` texto, embora você possa alterar o nome e o local desse arquivo durante a instalação.

O arquivo de configuração contém uma série de propriedades de valor único ou valores múltiplos que controlam o comportamento do Dispatcher:

* Os nomes de propriedades recebem o prefixo de uma barra `/`.
* As propriedades de vários valores incluem itens filhos usando chaves `{ }`.

Um exemplo de configuração é estruturado da seguinte maneira:

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website 
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement 
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

Você pode incluir outros arquivos que contribuem para a configuração:

* Se o arquivo de configuração for grande, você poderá dividi-lo em vários arquivos menores (que são mais fáceis de gerenciar) e incluí-los.
* Para incluir arquivos que são gerados automaticamente.

Por exemplo, para incluir o arquivo myFarm.any na configuração /farms, use o seguinte código:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Use o asterisco (&quot;*&quot;) como curinga para especificar uma variedade de arquivos a serem incluídos.

Por exemplo, se os arquivos `farm_1.any` até para `farm_5.any` conter a configuração de farm de um a cinco, você pode incluí-los da seguinte maneira:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Uso de variáveis de Ambiente {#using-environment-variables}

Você pode usar variáveis de ambiente em propriedades com valores de sequência de caracteres no arquivo dispatcher.any em vez de codificar os valores. Para incluir o valor de uma variável de ambiente, use o formato `${variable_name}`.

Por exemplo, se o arquivo dispatcher.any estiver localizado no mesmo diretório do diretório de cache, o seguinte valor para a propriedade [docroot](dispatcher-configuration.md#main-pars-title-30) poderá ser usado:

```xml
/docroot "${PWD}/cache"
```

Como outro exemplo, se você criar uma variável de ambiente chamada `PUBLISH_IP` que armazena o nome do host da instância de publicação do AEM, a seguinte configuração da propriedade [/renders](dispatcher-configuration.md#main-pars-127-25-0008) pode ser usada:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Como nomear a instância do Dispatcher {#naming-the-dispatcher-instance-name}

Use a `/name` propriedade para especificar um nome exclusivo para identificar a instância do Dispatcher. A `/name` propriedade é uma propriedade de nível superior na estrutura de configuração.

## Definição de Farms {#defining-farms-farms}

A `/farms` propriedade define um ou mais conjuntos de comportamentos do Dispatcher, nos quais cada conjunto é associado a sites ou URLs diferentes. A `/farms` propriedade pode incluir uma única fazenda ou várias fazendas:

* Use um único farm quando quiser que o Dispatcher manipule todas as suas páginas da Web ou sites da mesma maneira.
* Crie vários farm quando diferentes áreas do site ou sites diferentes exigirem um comportamento diferente do Dispatcher.

A `/farms` propriedade é uma propriedade de nível superior na estrutura de configuração. Para definir um farm, adicione uma propriedade filho à `/farms` propriedade. Use um nome de propriedade que identifique exclusivamente o farm na instância do Dispatcher.

A `/farmname` propriedade tem vários valores e contém outras propriedades que definem o comportamento do Dispatcher:

* Os URLs das páginas às quais o farm se aplica.
* Um ou mais URLs de serviço (normalmente de instâncias de publicação do AEM) a serem usados para renderizar documentos.
* As estatísticas a serem usadas para o balanceamento de carga de vários renderizadores de documento.
* Vários outros comportamentos, como quais arquivos serão armazenados em cache e onde.

O valor pode ter qualquer caractere alfanumérico (a-z, 0-9). O exemplo a seguir mostra a definição do esqueleto para duas fazendas nomeadas `/daycom` e `/docsdaycom`:

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>Se você usar mais de um farm de renderização, a lista será avaliada de baixo para cima. Isso é particularmente relevante ao definir hosts [](dispatcher-configuration.md#main-pars-117-15-0006) virtuais para seus sites.

Cada propriedade farm pode conter as seguintes propriedades secundárias:

| Nome da propriedade | Descrição |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Página inicial padrão (opcional) (somente IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Os cabeçalhos da solicitação HTTP do cliente para passar. |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | Os hosts virtuais para este farm. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Suporte para gerenciamento e autenticação de sessão. |
| [/renders](#defining-page-renderers-renders) | Os servidores que fornecem páginas renderizadas (normalmente, instâncias de publicação do AEM). |
| [/filter](#configuring-access-to-content-filter) | Define os URLs aos quais o Dispatcher ativa o acesso. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configura o acesso a URLs personalizados. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Suporte para o encaminhamento de solicitações de sindicalização. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configura o comportamento de cache. |
| [/statistics](#configuring-load-balancing-statistics) | Definindo categorias estatísticas para cálculos de balanceamento de carga. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | A pasta que contém documentos fixos. |
| [/health_check](#specifying-a-health-check-page) | O URL a ser usado para determinar a disponibilidade do servidor. |
| [/retryDelay](#specifying-the-page-retry-delay) | O atraso antes de tentar novamente uma conexão com falha. |
| [/unavailablePenalidade](#reflecting-server-unavailability-in-dispatcher-statistics) | Sanções que afetam as estatísticas relativas aos cálculos de balanceamento de carga. |
| [/failover](#using-the-failover-mechanism) | Reenviar solicitações para renderizações diferentes quando a solicitação original falhar. |
| [/auth_checker](permissions-cache.md) | Para obter acesso a cache sensível a permissões, consulte [Armazenamento em cache de conteúdo](permissions-cache.md)protegido. |

## Especificar uma página padrão (somente IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>O `/homepage`parâmetro (somente IIS) não funciona mais. Em vez disso, você deve usar o Módulo [de regravação de URL do](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module)IIS.
>
>Se estiver usando o Apache, você deve usar o `mod_rewrite` módulo. Consulte a documentação do site do Apache para obter informações sobre `mod_rewrite` (por exemplo, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Ao usar `mod_rewrite`, é aconselhável usar o sinalizador **[&#39;passthrough|PT&#39; (passar para o próximo manipulador)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)**para forçar o mecanismo de regravação a definir o`uri`campo da`request_rec`estrutura interna para o valor do`filename`campo.

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## Especificação dos cabeçalhos HTTP a serem passados {#specifying-the-http-headers-to-pass-through-clientheaders}

A `/clientheaders` propriedade define uma lista de cabeçalhos HTTP que o Dispatcher transmite da solicitação HTTP do cliente para o renderizador (instância do AEM).

Por padrão, o Dispatcher encaminha os cabeçalhos HTTP padrão para a instância do AEM. Em alguns casos, talvez você queira encaminhar cabeçalhos adicionais ou remover cabeçalhos específicos:

* Adicione cabeçalhos, como cabeçalhos personalizados, que sua instância do AEM espera na solicitação HTTP.
* Remova cabeçalhos, como cabeçalhos de autenticação, que sejam relevantes apenas para o servidor da Web.

Se você personalizar o conjunto de cabeçalhos para passar, deverá especificar uma lista exaustiva de cabeçalhos, incluindo aqueles normalmente incluídos por padrão.

Por exemplo, uma instância do Dispatcher que lida com solicitações de ativação de página para instâncias de publicação requer o `PATH` cabeçalho na `/clientheaders` seção. O `PATH` cabeçalho permite a comunicação entre o agente de replicação e o dispatcher.

O código a seguir é um exemplo de configuração para `/clientheaders`:

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }
```

## Como identificar hosts virtuais {#identifying-virtual-hosts-virtualhosts}

A `/virtualhosts` propriedade define uma lista de todas as combinações de nome do host/URI aceitas pelo Dispatcher para este farm. Você pode usar o caractere asterisco (&quot;*&quot;) como curinga. Os valores para a propriedade / `virtualhosts` usam o seguinte formato:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Opcional) Ou `https://` ou `https://.`
* `host`: O nome ou endereço IP do computador host e o número da porta, se necessário. (Consulte [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`: (Opcional) O caminho para os recursos.

O exemplo de configuração a seguir lida com solicitações para os domínios .com e .ch da myCompany e todos os domínios da mySubDivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

A configuração a seguir lida com *todas* as solicitações:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolução do host virtual {#resolving-the-virtual-host}

Quando o Dispatcher recebe uma solicitação HTTP ou HTTPS, encontra o valor do host virtual que melhor corresponde aos cabeçalhos `host,``uri`e `scheme` da solicitação. O Dispatcher avalia os valores nas `virtualhosts` propriedades na seguinte ordem:

* O Dispatcher começa no farm mais baixo e avança para cima no arquivo dispatcher.any.
* Para cada farm, o Dispatcher começa com o valor mais alto na `virtualhosts` propriedade e avança para baixo na lista de valores.

O Dispatcher encontra o melhor valor de host virtual correspondente da seguinte maneira:

* O primeiro host virtual encontrado que corresponde a todos os três da solicitação `host`, o `scheme`e o `uri` da solicitação é usado.
* Se nenhum `virtualhosts` valor tiver `scheme` e `uri` partes que correspondam ao `scheme` e `uri` à solicitação, o primeiro host virtual encontrado que corresponde ao `host` da solicitação será usado.
* Se nenhum `virtualhosts` valor tiver uma parte do host que corresponda ao host da solicitação, o host virtual mais alto do farm mais alto será usado.

Portanto, você deve colocar seu host virtual padrão na parte superior da `virtualhosts` propriedade no farm mais alto do arquivo dispatcher.any.

### Exemplo de resolução de host virtual {#example-virtual-host-resolution}

O exemplo a seguir representa um snippet de um dispatcher.qualquer arquivo que define dois farm do Dispatcher e cada farm define uma `virtualhosts` propriedade.

```xml
/farms
  {
  /myProducts 
    { 
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany 
    { 
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

Usando este exemplo, a tabela a seguir mostra os hosts virtuais que são resolvidos para as solicitações HTTP fornecidas:

| URL de solicitação | Host virtual resolvido |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Habilitar Sessões Seguras - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` **deve** ser definido como `"0"` na `/cache` seção para ativar esse recurso.

Crie uma sessão segura para acessar o farm de renderização para que os usuários precisem fazer logon para acessar qualquer página no farm. Depois de fazer logon, os usuários podem acessar páginas no farm. Consulte [Criação de um grupo](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) de usuários fechado para obter informações sobre como usar esse recurso com CUGs. Além disso, consulte a Lista de verificação [de](/help/using/security-checklist.md) segurança do Dispatcher antes de entrar em execução.

A `/sessionmanagement` propriedade é uma subpropriedade de `/farms`.

>[!CAUTION]
>
>Se as seções do seu site usarem requisitos de acesso diferentes, será necessário definir vários farm.

**/sessionmanagement** tem vários sub-parâmetros:

**/diretory** (obrigatório)

O diretório que armazena as informações da sessão. Se o diretório não existir, ele será criado.

>[!CAUTION]
>
> Ao configurar o subparâmetro de diretório, **não** aponte para a pasta raiz (`/directory "/"`), pois isso pode causar problemas graves. Você deve sempre especificar o caminho para a pasta que armazena as informações da sessão. Por exemplo:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (opcional)

Como as informações da sessão são codificadas. Use &quot;md5&quot; para criptografia usando o algoritmo md5 ou &quot;hex&quot; para codificação hexadecimal. Se você criptografar os dados da sessão, um usuário com acesso ao sistema de arquivos não poderá ler o conteúdo da sessão. O padrão é &quot;md5&quot;.

**/header** (opcional)

O nome do cabeçalho HTTP ou cookie que armazena as informações de autorização. Se você armazenar as informações no cabeçalho http, use `HTTP:<*header-name*>`. Para armazenar as informações em um cookie, use `Cookie:<header-name>`. Se você não especificar um valor `HTTP:authorization` será usado.

**/timeout** (opcional)

O número de segundos até que a sessão atinja o tempo limite após ser usada por último. Se não for especificado &quot;800&quot;, a sessão expirará pouco mais de 13 minutos após a última solicitação do usuário.

Um exemplo de configuração tem a seguinte aparência:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }
```

## Definindo renderizadores de página {#defining-page-renderers-renders}

A propriedade /renders define o URL para o qual o Dispatcher envia solicitações para renderizar um documento. A seguinte seção de exemplo `/renders` identifica uma única instância do AEM para renderização:

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

A seguinte seção de exemplo/renderização identifica uma instância do AEM que é executada no mesmo computador que o dispatcher:

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }
```

A seguinte seção de exemplo/renderização distribui as solicitações de renderização igualmente entre duas instâncias do AEM:

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }
```

### Opções de renderização {#renders-options}

**/timeout**

Especifica o tempo limite de conexão acessando a instância do AEM em milissegundos. O padrão é &quot;0&quot;, fazendo com que o Dispatcher aguarde indefinidamente.

**/receiveTimeout**

Especifica o tempo, em milissegundos, que uma resposta pode levar. O padrão é &quot;600000&quot;, fazendo com que o Dispatcher aguarde 10 minutos. Uma configuração de &quot;0&quot; elimina completamente o tempo limite.\
Se o tempo limite for atingido durante a análise dos cabeçalhos de resposta, um Status HTTP 504 (Gateway inválido) será retornado. Se o tempo limite for atingido enquanto o corpo da resposta for lido, o Dispatcher retornará a resposta incompleta ao cliente, mas excluirá qualquer arquivo de cache que possa ter sido gravado.

**/ipv4**

Especifica se o Dispatcher usa a `getaddrinfo` função (para IPv6) ou a `gethostbyname` função (para IPv4) para obter o endereço IP da renderização. Um valor de 0 faz com que `getaddrinfo` seja usado. Um valor de 1 faz com que `gethostbyname` seja usado. O valor padrão é 0.

A função getaddrinfo retorna uma lista de endereços IP. O Dispatcher repete a lista de endereços até estabelecer uma conexão TCP/IP. Portanto, a propriedade ipv4 é importante quando o nome do host de renderização é associado a vários endereços IP e o host, em resposta à função getaddrinfo, retorna uma lista de endereços IP que estão sempre na mesma ordem. Nessa situação, você deve usar a função gethostbyname para que o endereço IP ao qual o Dispatcher se conecte seja aleatório.

O Amazon Elastic Load Balancing (ELB) é um serviço que responde a getaddrinfo com uma lista potencialmente igual de endereços IP.

**/secure**

Se a `/secure` propriedade tiver um valor de &quot;1&quot;, o Dispatcher usará HTTPS para se comunicar com a instância do AEM. Para obter detalhes adicionais, consulte também [Configuração do Dispatcher para Usar SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Com a versão **4.1.6** do Dispatcher, é possível configurar a `/always-resolve` propriedade da seguinte maneira:

* Quando definido como &quot;1&quot;, ele resolverá o nome do host em cada solicitação (o Dispatcher nunca armazenará em cache nenhum endereço IP). Pode haver um pequeno impacto no desempenho devido à chamada adicional necessária para obter as informações do host para cada solicitação.
* Se a propriedade não estiver definida, o endereço IP será armazenado em cache por padrão.

Além disso, essa propriedade pode ser usada no caso de problemas de resolução dinâmica de IP, conforme mostrado na amostra a seguir:

```xml
/renders {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configuração do acesso ao conteúdo {#configuring-access-to-content-filter}

Use a `/filter` seção para especificar as solicitações HTTP aceitas pelo Dispatcher. Todas as outras solicitações são enviadas de volta ao servidor da Web com um código de erro 404 (página não encontrada). Se não houver nenhuma `/filter` seção, todas as solicitações serão aceitas.

**Observação:** As solicitações para o [statfile](dispatcher-configuration.md#main-pars-title-28) são sempre rejeitadas.

>[!CAUTION]
>
>Consulte a Lista de verificação [de segurança do](security-checklist.md) Dispatcher para obter mais considerações ao restringir o acesso usando o Dispatcher. Além disso, leia a Lista de verificação [de segurança do](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) AEM para obter detalhes adicionais sobre a instalação do AEM.

A seção /filter consiste em uma série de regras que negam ou permitem acesso ao conteúdo de acordo com os padrões na parte da linha de solicitação da solicitação HTTP. Você deve usar uma estratégia de lista de permissões para sua seção /filter:

* Primeiro, negar acesso a tudo.
* Permita o acesso ao conteúdo, conforme necessário.

### Definição de um filtro {#defining-a-filter}

Cada item na `/filter` seção inclui um tipo e um padrão que são correspondentes a um elemento específico da linha de solicitação ou a toda a linha de solicitação. Cada filtro pode conter os seguintes itens:

* **Tipo**: O `/type` indica se o acesso às solicitações que correspondem ao padrão deve ser permitido ou negado. O valor pode ser `allow` ou `deny`.

* **Elemento da Linha de Solicitação:** Inclua `/method`, `/url`ou `/query``/protocol` e um padrão para filtrar solicitações de acordo com essas partes específicas da parte da linha de solicitação da solicitação HTTP. A filtragem em elementos da linha de solicitação (em vez de na linha de solicitação inteira) é o método de filtro preferencial.

* **Elementos avançados da linha de solicitação:** A partir do Dispatcher 4.2.0, quatro novos elementos de filtro estarão disponíveis para uso. Esses novos elementos são `/path`, `/selectors`e `/extension``/suffix` , respectivamente. Inclua um ou mais desses itens para controlar ainda mais os padrões de URL.

>[!NOTE]
>
>Para obter mais informações sobre qual parte da linha de solicitação cada um desses elementos faz referência, consulte a página wiki de Decomposição [do URL](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) Sling.

* **propriedade** global: A `/glob` propriedade é usada para corresponder a toda a linha de solicitação da solicitação HTTP.

>[!CAUTION]
>
>A filtragem com globs está obsoleta no Dispatcher. Dessa forma, você deve evitar o uso de globs nas `/filter` seções, pois isso pode levar a problemas de segurança. Então, em vez de:

`/glob "* *.css *"`

você deve usar

`/url "*.css"`

#### A parte da linha de solicitação das solicitações HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 define a linha de [solicitação](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) da seguinte maneira:

*Método Request-URI HTTP-Version*&lt;CRLF>

Os caracteres &lt;CRLF> representam um retorno de carro seguido por um feed de linha. O exemplo a seguir é a linha de solicitação recebida quando um cliente solicita a página final do site Geometrixx-Outoors:

OBTENHA O /content/geometrixx-outdoors/en.html HTTP.1.1&lt;CRLF>

Seus padrões devem levar em conta os caracteres de espaço na linha de solicitação e os caracteres &lt;CRLF>.

#### aspas de Duplo vs aspas simples {#double-quotes-vs-single-quotes}

Ao criar suas regras de filtro, use aspas de duplo `"pattern"` para obter padrões simples. Se você estiver usando o Dispatcher 4.2.0 ou posterior e seu padrão incluir uma expressão regular, será necessário colocar o padrão regex entre aspas simples `'(pattern1|pattern2)'` .

#### Regular Expressions {#regular-expressions}

Depois do Dispatcher 4.2.0, é possível incluir Expressões regulares POSIX Extended nos padrões de filtro.

#### Solução de problemas de Filtros {#troubleshooting-filters}

Se os filtros não estiverem acionando da forma esperada, ative o [Rastreamento de registro](#trace-logging) no dispatcher para que você possa ver qual filtro está interceptando a solicitação.

#### Exemplo de filtro: Negar tudo {#example-filter-deny-all}

A seção de filtro de exemplo a seguir faz com que o Dispatcher negue solicitações para todos os arquivos. Você deve negar o acesso a todos os arquivos e permitir o acesso a áreas específicas.

```xml
  /0001  { /glob "*" /type "deny" }
```

As solicitações para uma área explicitamente negada resultam no retorno de um código de erro 404 (página não encontrada).

#### Exemplo de filtro: Negar acesso a áreas específicas {#example-filter-deny-acess-to-specific-areas}

Filtros também permitem negar o acesso a vários elementos, por exemplo, páginas ASP e áreas confidenciais em uma instância de publicação. O filtro a seguir nega acesso às páginas ASP:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Exemplo de filtro: Ativar solicitações POST {#example-filter-enable-post-requests}

O filtro de exemplo a seguir permite o envio de dados de formulário pelo método POST:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Exemplo de filtro: Permitir acesso ao console de fluxo de trabalho {#example-filter-allow-access-to-the-workflow-console}

O exemplo a seguir mostra um filtro usado para negar acesso externo ao console Fluxo de trabalho:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Se sua instância de publicação usar um contexto de aplicativo da Web (por exemplo, publicar), isso também poderá ser adicionado à definição do filtro.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Se ainda precisar acessar páginas únicas dentro da área restrita, você poderá permitir o acesso a elas. Por exemplo, para permitir o acesso à guia Arquivo no console Fluxo de trabalho, adicione a seguinte seção:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Quando vários padrões de filtros se aplicam a uma solicitação, o último padrão de filtro aplicado é efetivo.

#### Exemplo de filtro: Usando Expressões regulares {#example-filter-using-regular-expressions}

Esse filtro permite extensões em diretórios de conteúdo não público usando uma expressão regular, definida aqui entre aspas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Exemplo de filtro: Filtrar elementos adicionais de um URL de solicitação {#example-filter-filter-additional-elements-of-a-request-url}

Abaixo está um exemplo de regra que bloqueia a captura de conteúdo do `/content` caminho e de sua subárvore, usando filtros para caminho, seletores e extensões:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Exemplo /seção de filtro {#example-filter-section}

Ao configurar o Dispatcher, você deve restringir o acesso externo o máximo possível. O exemplo a seguir fornece acesso mínimo para visitantes externos:

* `/content`
* Conteúdos diversos, tais como desenhos e bibliotecas de clientes; por exemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Depois de criar filtros, [teste o acesso](dispatcher-configuration.md#main-pars-title-19) à página para garantir que sua instância do AEM esteja segura.

A seguinte seção /filter do dispatcher.any file pode ser usada como base no arquivo de configuração [do](dispatcher-configuration.md) Dispatcher.

Este exemplo é baseado no arquivo de configuração padrão fornecido com o Dispatcher e serve como exemplo para uso em um ambiente de produção. Os itens com o prefixo # são desativados (comentários), deve-se tomar cuidado se você decidir ativar qualquer um desses itens (removendo o # nessa linha), pois isso pode ter um impacto na segurança.

Você deve negar acesso a tudo e, em seguida, permitir acesso a elementos específicos (limitados):

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001 { /type "deny" /glob "*" }
      
      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console
        
      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only
      
#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features 
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>Quando usado com o Apache, crie seus padrões de URL de filtro de acordo com a propriedade DispatcherUseProcessedURL do módulo Dispatcher. (Consulte [Apache Web Server - Configure seu Apache Web Server para Dispatcher](dispatcher-install.md#main-pars-55-35-1022).)

>[!NOTE]
>
>Os Filtros 0030 e 0031 com relação ao Dynamic Media são aplicáveis ao AEM 6.0 e superior.

Considere as seguintes recomendações se você optar por estender o acesso:

* O acesso externo a `/admin` deve ser sempre *completamente* desativado se você estiver usando o CQ versão 5.4 ou uma versão anterior.

* É necessário ter cuidado ao permitir o acesso aos arquivos em `/libs`. O acesso deve ser permitido numa base individual.
* Negar acesso à configuração de replicação para que ela não possa ser vista:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Negar acesso ao proxy reverso do Google Gadgets:

   * `/libs/opensocial/proxy*`

Dependendo da sua instalação, pode haver recursos adicionais sob `/libs`, `/apps` ou em outro lugar, que devem ser disponibilizados. Você pode usar o `access.log` arquivo como um método para determinar os recursos que estão sendo acessados externamente.

>[!CAUTION]
>
>O acesso a consoles e diretórios pode apresentar um risco de segurança para ambientes de produção. A menos que você tenha justificações explícitas, elas devem permanecer desativadas (comentado).

>[!CAUTION]
>
>Se você estiver [usando relatórios em um ambiente](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) de publicação, configure o Dispatcher para negar acesso a visitantes externos `/etc/reports` .

### Restrição de sequências de caracteres de Query {#restricting-query-strings}

Como o Dispatcher versão 4.1.5, use a `/filter` seção para restringir strings de query. É altamente recomendável permitir explicitamente strings de query e excluir a permissão genérica por meio de elementos de `allow` filtro.

Uma única entrada pode ter um *valor global* ou alguma combinação de *método*,*url*,*query* e *versão* , mas não ambos. O exemplo a seguir permite a string do `a=*` query e nega todas as outras strings do query para URLs que são resolvidos para o `/etc` nó:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Se uma regra contiver uma regra, ela só corresponderá às solicitações que contêm uma sequência de caracteres de query e corresponderão ao padrão de query fornecido. `/query`
>
>No exemplo acima, se as solicitações para `/etc` que não tenham uma sequência de caracteres de query também forem permitidas, as seguintes regras serão obrigatórias:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Teste da segurança do Dispatcher {#testing-dispatcher-security}

Os filtros do Dispatcher devem bloquear o acesso às seguintes páginas e scripts nas instâncias de publicação do AEM. Use um navegador da Web para tentar abrir as páginas a seguir como um visitante do site faria e verificar se um código 404 é retornado. Se qualquer outro resultado for obtido, ajuste as filtros.

Observe que você deve ver a renderização de página normal para /content/add_valid_page.html?debug=layout.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{.}/libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /content/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json?Statement=//*
* /content/add_valid_page.qu%65ry.js%6Fn?Statement=//*
* /content/add_valid_page.query.json?Statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html?debug=layout
* /projetos
* /marcação
* /etc/replication.html
* /etc/cloudservices.html
* /bem-vindo

Execute o seguinte comando em um terminal ou prompt de comando para determinar se o acesso anônimo de gravação está ativado. Não é possível gravar dados no nó.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Emita o seguinte comando em um terminal ou prompt de comando para tentar invalidar o cache do Dispatcher e garantir que você receba uma resposta do código 404:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Ativando o acesso a URLs personalizados {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configure o Dispatcher para ativar o acesso a URLs personalizados configurados para suas páginas CQ ou AEM.

Quando o acesso a URLs personalizados está ativado, o Dispatcher chama periodicamente um serviço que é executado na instância de renderização para obter uma lista de URLs personalizados. O Dispatcher armazena essa lista em um arquivo local. Quando uma solicitação de página é negada devido a um filtro na `/filter` seção, o Dispatcher consulta a lista de URLs personalizados. Se o URL negado estiver na lista, o Dispatcher permitirá acesso ao URL personalizado.

Para ativar o acesso a URLs personalizados, adicione uma `/vanity_urls` seção à `/farms` seção, semelhante ao exemplo a seguir:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

A `/vanity_urls` seção contém as seguintes propriedades:

* `/url`: O caminho para o serviço de URL personalizado que é executado na instância de renderização. O valor dessa propriedade deve ser `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: O caminho para o arquivo local onde o Dispatcher armazena a lista de URLs personalizados. Verifique se o Dispatcher tem acesso de gravação a esse arquivo.
* `/delay`: (Segundos) O tempo entre as chamadas para o serviço vanity URL.

>[!NOTE]
>
>Se a renderização for uma instância do AEM, você deverá instalar o pacote [VanityURLS-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) para instalar o serviço vanity URL. (Consulte [Fazer logon no Compartilhamento](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare)de pacotes.)

Use o procedimento a seguir para habilitar o acesso a URLs personalizados.

1. Se o serviço de renderização for uma instância do AEM, instale o pacote com.adobe.granite.dispatcher.vanityurl.content na instância de publicação (consulte a nota acima).
1. Para cada URL personalizado que você configurou para uma página AEM ou CQ, verifique se a ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` configuração nega o URL. Se necessário, adicione um filtro que negue o URL.
1. Adicione a `/vanity_urls` seção abaixo `/farms`.
1. Reinicie o servidor Web Apache.

## Encaminhando solicitações de sindicalização - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Normalmente, as solicitações de sindicalização são destinadas apenas ao Dispatcher, portanto, por padrão, elas não são enviadas ao renderizador (por exemplo, uma instância do AEM).

Se necessário, defina a propriedade /propagateSyndPost como &quot;1&quot; para encaminhar solicitações de sindicalização ao Dispatcher. Se estiver definido, verifique se as solicitações POST não foram negadas na seção de filtro.

## Configuração do Cache do Dispatcher - /cache {#configuring-the-dispatcher-cache-cache}

A `/cache` seção controla como o Dispatcher armazena documentos em cache. Configure várias subpropriedades para implementar suas estratégias de cache:


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /regras
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowClients
* /ignoreUrlParams
* /cabeçalhos
* /mode
* /GracePeriod
* /enableTTL


Uma seção de cache de exemplo pode ter a seguinte aparência:

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"          
  /allowAuthorized "0"
      
  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>Para armazenar em cache com permissão, leia [Armazenando em Cache Conteúdo](permissions-cache.md)Protegido.

### Especificação do diretório de cache {#specifying-the-cache-directory}

A `/docroot` propriedade identifica o diretório onde os arquivos em cache são armazenados.

>[!NOTE]
>
>O valor deve ser exatamente o mesmo caminho da raiz do documento do servidor da Web para que o Dispatcher e o servidor da Web manipulem os mesmos arquivos.\
>O servidor da Web é responsável por fornecer o código de status correto quando o arquivo de cache do dispatcher é usado, por isso é importante que ele também possa encontrá-lo.

Se você usar vários farm, cada farm deverá usar uma raiz de documento diferente.

### Nomear o arquivo de status {#naming-the-statfile}

A `/statfile` propriedade identifica o arquivo a ser usado como o arquivo de status. O Dispatcher usa esse arquivo para registrar a hora da atualização de conteúdo mais recente. O arquivo de status pode ser qualquer arquivo no servidor da Web.

O arquivo de status não tem conteúdo. Quando o conteúdo é atualizado, o Dispatcher atualiza o carimbo de data e hora. O arquivo de status padrão é chamado de .stat e é armazenado no docroot. O Dispatcher bloqueia o acesso ao arquivo de status.

>[!NOTE]
>
>Se `/statfileslevel` estiver configurado, o Dispatcher ignorará a `/statfile` propriedade e usará .stat como o nome.

### Servindo Documentos obsoletos quando ocorrem erros {#serving-stale-documents-when-errors-occur}

A `/serveStaleOnError` propriedade controla se o Dispatcher retorna documentos invalidados quando o servidor de renderização retorna um erro. Por padrão, quando um arquivo de status é tocado e invalida o conteúdo em cache, o Dispatcher exclui o conteúdo em cache na próxima vez que for solicitado.

Se `/serveStaleOnError` estiver definido como &quot;1&quot;, o Dispatcher não excluirá o conteúdo invalidado do cache, a menos que o servidor de renderização retorne uma resposta bem-sucedida. Uma resposta 5xx do AEM ou um tempo limite de conexão faz com que o Dispatcher sirva o conteúdo desatualizado e responda com o Status HTTP 111 (Falha na Revalidação).

### Armazenamento em cache quando a autenticação é usada {#caching-when-authentication-is-used}

A `/allowAuthorized` propriedade controla se as solicitações que contêm qualquer uma das seguintes informações de autenticação são armazenadas em cache:

* The `authorization` header.
* Um cookie chamado `authorization`.
* Um cookie chamado `login-token`.

Por padrão, as solicitações que incluem essas informações de autenticação não são armazenadas em cache porque a autenticação não é executada quando um documento em cache é retornado ao cliente. Essa configuração impede que o Dispatcher disponibilize documentos em cache para usuários que não tenham os direitos necessários.

No entanto, se seus requisitos permitirem o armazenamento em cache de documentos autenticados, defina /allowAuthorized como um:

`/allowAuthorized "1"`

>[!NOTE]
>
>Para ativar o gerenciamento de sessão (usando a `/sessionmanagement` propriedade), a `/allowAuthorized` propriedade deve ser definida como `"0"`.

### Especificação dos Documentos para Cache {#specifying-the-documents-to-cache}

A `/rules` propriedade controla quais documentos são armazenados em cache de acordo com o caminho do documento. Independentemente da propriedade /rules, o Dispatcher nunca armazena um documento em cache nas seguintes circunstâncias:

* Se o URI da solicitação contiver um ponto de interrogação (&quot;?&quot;).\
   Isso geralmente indica uma página dinâmica, como um resultado de pesquisa que não precisa ser armazenado em cache.
* A extensão do arquivo está ausente.\
   O servidor Web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (isso pode ser configurado)
* Se a instância do AEM responder com os seguintes cabeçalhos:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Os métodos GET ou HEAD (para o cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. For additional information on response header caching, see the [Caching HTTP Response Headers](dispatcher-configuration.md#caching-http-response-headers) section.

Cada item na propriedade /rules inclui um padrão [global](#designing-patterns-for-glob-properties) e um tipo:

* O padrão global é usado para corresponder ao caminho do documento.
* O tipo indica se os documentos que correspondem ao padrão global devem ser armazenados em cache. O valor pode ser permitir (para armazenar o documento em cache) ou negar (para sempre renderizar o documento).

Se você não tiver páginas dinâmicas (além daquelas já excluídas pelas regras acima), poderá configurar o Dispatcher para armazenar tudo em cache. A seção de regras para isso é a seguinte:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Para obter informações sobre propriedades globais, consulte [Criar padrões para propriedades](#designing-patterns-for-glob-properties)globais.

Se houver algumas seções de sua página que sejam dinâmicas (por exemplo, um aplicativo de notícias) ou dentro de um grupo de usuários fechado, você pode definir exceções:

>[!NOTE]
>
>Os grupos de usuários fechados não devem ser armazenados em cache, pois os direitos de usuário não são verificados para páginas em cache.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Compactação**

Em servidores da Web Apache, é possível compactar os documentos em cache. A compactação permite que o Apache devolva o documento em um formulário compactado, se solicitado pelo cliente. A compactação é feita automaticamente ao ativar o módulo Apache `mod_deflate`, por exemplo:

```xml
AddOutputFilterByType DEFLATE text/plain
```

O módulo é instalado por padrão com o Apache 2.x.

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### Invalidando arquivos por nível de pasta {#invalidating-files-by-folder-level}

Use a `/statfileslevel` propriedade para invalidar arquivos em cache de acordo com seu caminho:

* O Dispatcher cria `.stat`arquivos em cada pasta a partir da pasta docroot até o nível especificado. A pasta docroot é do nível 0.
* Os arquivos são invalidados tocando-se no `.stat` arquivo. A última data de modificação do `.stat` arquivo é comparada à última data de modificação de um documento em cache. O documento será buscado novamente se o `.stat` arquivo for mais recente.

* Quando um arquivo localizado em um determinado nível é invalidado, **todos** os `.stat` arquivos do ponto **para** o nível do arquivo invalidado ou do configurado `statsfilevel` (o que for menor) serão tocados.

   * Por exemplo: se você definir a propriedade `statfileslevel` como 6 e um arquivo for invalidado no nível 5, todos os arquivos `.stat` do ponto para o 5 serão tocados. Continuando com este exemplo, se um arquivo for invalidado no nível 7, então a cada . `stat` o arquivo de ponto para 6 será tocado (desde `/statfileslevel = "6"`).

Somente os recursos **ao longo do caminho** para o arquivo invalidado são afetados. Considere o seguinte exemplo: um site usa a estrutura `/content/myWebsite/xx/.` Se você definir `statfileslevel` como 3, um `.stat`arquivo será criado da seguinte forma:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Quando um arquivo em `/content/myWebsite/xx` é invalidado, todos os `.stat` arquivos de ponto para baixo para baixo `/content/myWebsite/xx`são tocados. Isso aconteceria apenas para `/content/myWebsite/xx` o caso, e não por exemplo `/content/myWebsite/yy` ou `/content/anotherWebSite`.

>[!NOTE]
>
>A invalidação pode ser impedida enviando um cabeçalho adicional `CQ-Action-Scope:ResourceOnly`. Isso pode ser usado para liberar recursos específicos sem invalidar outras partes do cache. Consulte [esta página](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) e Invalidando [manualmente o Cache](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) do Dispatcher para obter mais detalhes.

>[!NOTE]
>
>Se você especificar um valor para a `/statfileslevel` propriedade, a `/statfile` propriedade será ignorada.

### Invalidando automaticamente arquivos em cache {#automatically-invalidating-cached-files}

A `/invalidate` propriedade define os documentos que são automaticamente invalidados quando o conteúdo é atualizado.

Com a invalidação automática, o Dispatcher não exclui arquivos em cache após uma atualização de conteúdo, mas verifica sua validade na próxima vez que forem solicitados. Os Documentos no cache que não forem invalidados automaticamente permanecerão no cache até que uma atualização de conteúdo os exclua explicitamente.

A invalidação automática geralmente é usada para páginas HTML. Geralmente, as páginas HTML contêm links para outras páginas, tornando difícil determinar se uma atualização de conteúdo afeta uma página. Para garantir que todas as páginas relevantes sejam invalidadas quando o conteúdo for atualizado, invalide automaticamente todas as páginas HTML. A configuração a seguir invalida todas as páginas HTML:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Para obter informações sobre propriedades globais, consulte [Criar padrões para propriedades](#designing-patterns-for-glob-properties)globais.

Essa configuração gera a seguinte atividade quando /content/geometrixx/en é ativado:

* Todos os arquivos com padrão en.* são removidas da pasta /content/geometrixx/.
* A pasta /content/geometrixx/en/_jcr_content foi removida.
* Todos os outros arquivos que correspondem à configuração /invalidate não são excluídos imediatamente. Esses arquivos são excluídos quando a próxima solicitação ocorrer. Em nosso exemplo, /content/geometrixx.html não é excluído, ele será excluído quando /content/geometrixx.html for solicitado.

Se você oferta arquivos PDF e ZIP gerados automaticamente para download, talvez seja necessário invalidá-los automaticamente também. Um exemplo de configuração é o seguinte:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

A integração do AEM com o Adobe Analytics fornece dados de configuração em um arquivo analytics.sitecatalyst.js em seu site. O exemplo dispatcher.any file fornecido com o Dispatcher inclui a seguinte regra de invalidação para este arquivo:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Uso de scripts de invalidação personalizados {#using-custom-invalidation-scripts}

A propriedade /invalidateHandler permite definir um script que é chamado para cada solicitação de invalidação recebida pelo Dispatcher.

É chamada com os seguintes argumentos:

* Alça\
   O caminho de conteúdo que é invalidado
* Ação\
   A ação de replicação (por exemplo, Ativar, Desativar)
* Âmbito de ação\
   O escopo da ação de replicação (vazio, a menos que um cabeçalho de `CQ-Action-Scope: ResourceOnly` seja enviado, consulte [Invalidar páginas em cache do AEM](page-invalidate.md) para obter detalhes)

Isso pode ser usado para abranger vários casos de uso diferentes, como a invalidação de outros caches específicos do aplicativo ou para lidar com casos em que o URL externo de uma página e seu local na docroot não correspondem ao caminho do conteúdo.

Abaixo do exemplo, o script registra cada solicitação de invalidação em um arquivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### exemplo de script do manipulador de invalidação {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitação dos clientes que podem liberar o cache {#limiting-the-clients-that-can-flush-the-cache}

A propriedade /allowClients define clientes específicos que têm permissão para liberar o cache. Os padrões de globalização são comparados com o IP.

O exemplo a seguir:

1. nega acesso a qualquer cliente
1. permite explicitamente o acesso ao host local

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Para obter informações sobre propriedades globais, consulte [Criar padrões para propriedades](#designing-patterns-for-glob-properties)globais.

>[!CAUTION]
>
>É recomendável definir os /allowClients.
>
>Se isso não for feito, qualquer cliente poderá emitir uma chamada para limpar o cache; se isso for feito repetidamente, isso pode afetar gravemente o desempenho do site.

### Ignorando parâmetros de URL {#ignoring-url-parameters}

A `ignoreUrlParams` seção define quais parâmetros de URL são ignorados ao determinar se uma página é armazenada em cache ou entregue do cache:

* Quando um URL de solicitação contém parâmetros que são todos ignorados, a página é armazenada em cache.
* Quando um URL de solicitação contém um ou mais parâmetros que não são ignorados, a página não é armazenada em cache.

Quando um parâmetro é ignorado para uma página, ela é armazenada em cache na primeira vez que a página é solicitada. As solicitações subsequentes da página são atendidas para a página em cache, independentemente do valor do parâmetro na solicitação.

Para especificar quais parâmetros são ignorados, adicione regras de valor global à `ignoreUrlParams` propriedade:

* Para ignorar um parâmetro, crie uma propriedade global que permita o parâmetro.
* Para impedir que a página seja armazenada em cache, crie uma propriedade global que negue o parâmetro.

O exemplo a seguir faz com que o Dispatcher ignore o parâmetro &quot;q&quot;, para que os URLs de solicitação que incluem o parâmetro q sejam armazenados em cache:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Usando o `ignoreUrlParams` valor de exemplo, a seguinte solicitação HTTP faz com que a página seja armazenada em cache porque o `q` parâmetro é ignorado:

```xml
GET /mypage.html?q=5
```

Usando o `ignoreUrlParams` valor de exemplo, a seguinte solicitação HTTP faz com que a página **não** seja armazenada em cache porque o `p` parâmetro não é ignorado:

```xml
GET /mypage.html?q=5&p=4
```

Para obter informações sobre propriedades globais, consulte [Criar padrões para propriedades](#designing-patterns-for-glob-properties)globais.

### Armazenamento em Cache de Cabeçalhos de Resposta HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Este recurso está disponível na versão **4.1.11** do Dispatcher.

A `/headers` propriedade permite definir os tipos de cabeçalho HTTP que serão armazenados em cache pelo Dispatcher. Na primeira solicitação a um recurso não armazenado em cache, todos os cabeçalhos que correspondem a um dos valores configurados (consulte a amostra de configuração abaixo) são armazenados em um arquivo separado, ao lado do arquivo de cache. Em solicitações subsequentes ao recurso em cache, os cabeçalhos armazenados são adicionados à resposta.

Abaixo é apresentada uma amostra da configuração padrão:

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}
```

>[!NOTE]
>
>Além disso, lembre-se de que os caracteres de bloqueio de arquivo não são permitidos. Para obter mais detalhes, consulte [Criar padrões para propriedades](#designing-patterns-for-glob-properties)globais.

>[!NOTE]
>
>Se você precisar que o Dispatcher armazene e entregue cabeçalhos de resposta ETag do AEM, faça o seguinte:
>
>* Adicione o nome do cabeçalho na `/cache/headers`seção.
>* Adicione a seguinte diretiva [do](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) Apache na seção relacionada ao Dispatcher:
>



```xml
FileETag none
```

### Permissões de arquivo de cache do Dispatcher {#dispatcher-cache-file-permissions}

A `mode` propriedade especifica quais permissões de arquivo são aplicadas a novos diretórios e arquivos no cache. Essa configuração é restringida pelo processo `umask` de chamada. É um número octal construído a partir da soma de um ou mais dos seguintes valores:

* 0400 Permitir leitura por proprietário.
* 0200 Permitir gravação por proprietário.
* 0100 Permite que o proprietário pesquise em diretórios.
* 0040 Permitir leitura por membros do grupo.
* 0020 Permitir gravação por membros do grupo.
* 0010 Permite que membros do grupo pesquisem no diretório.
* 0004 Permitir leitura por outras pessoas.
* 0002 Permitir gravação por outras pessoas.
* 0001 Permite que outras pessoas pesquisem no diretório.

O valor padrão é 0755, que permite que o proprietário leia, grave ou pesquise e que o grupo e outros leiam ou pesquisem.

### A limitar o toque do ficheiro .stat {#throttling-stat-file-touching}

Com a `/invalidate` propriedade padrão, cada ativação invalida efetivamente todos os `.html` arquivos (quando seu caminho corresponde à `/invalidate` seção). Em um site com tráfego considerável, várias ativações subsequentes aumentarão a carga da cpu no backend. Nesse cenário, seria desejável &quot;limitar&quot; o `.stat` toque do arquivo para manter o site responsivo. Você pode fazer isso usando a `/gracePeriod` propriedade.

A `/gracePeriod` propriedade define o número de segundos em que um recurso obsoleto e autovalidado ainda pode ser servido do do cache após a última ativação que ocorre. A propriedade pode ser usada em uma configuração em que um lote de ativações invalidaria repetidamente o cache inteiro. O valor recomendado é de 2 segundos.

Para obter mais detalhes, leia também as `/invalidate` seções `/statfileslevel`e acima.

### Configurando a Invalidação do Cache Baseado em Tempo - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Se definida, a `/enableTTL` propriedade avaliará os cabeçalhos de resposta do backend e, se contiverem uma idade `Cache-Control` máxima ou uma `Expires` data, um arquivo vazio e auxiliar ao lado do arquivo de cache será criado, com o tempo de modificação igual à data de expiração. Quando o arquivo em cache é solicitado após a hora de modificação, ele é automaticamente solicitado novamente do back-end.

>[!NOTE]
>
>Esse recurso está disponível na versão **4.1.11** ou posterior do Dispatcher.

## Configuração do balanceamento de carga - /statistics {#configuring-load-balancing-statistics}

A `/statistics` seção define categorias de arquivos para os quais o Dispatcher classifica a capacidade de resposta de cada renderização. O Dispatcher usa as pontuações para determinar qual renderização enviar uma solicitação.

Cada categoria criada define um padrão global. O Dispatcher compara o URI do conteúdo solicitado a esses padrões para determinar a categoria do conteúdo solicitado:

* A ordem das categorias determina a ordem em que são comparadas ao URI.
* O primeiro padrão de categoria que corresponde ao URI é a categoria do arquivo. Não são avaliados mais padrões de categoria.

O Dispatcher suporta no máximo 8 categorias de estatísticas. Se você definir mais de 8 categorias, somente as primeiras 8 serão usadas.

**Renderizar seleção**

Sempre que o Dispatcher exigir uma página renderizada, ele usará o seguinte algoritmo para selecionar a renderização:

1. Se a solicitação contiver o nome da renderização em um `renderid` cookie, o Dispatcher usará essa renderização.
1. Se a solicitação não incluir nenhum `renderid` cookie, o Dispatcher compara as estatísticas de renderização:

   1. O Dispatcher determina a categoria do URI da solicitação.
   1. O Dispatcher determina qual renderização tem a menor pontuação de resposta para essa categoria e seleciona essa renderização.

1. Se nenhuma renderização ainda estiver selecionada, use a primeira renderização na lista.

A pontuação para a categoria de uma renderização é baseada nos tempos de resposta anteriores, bem como nas conexões com falha e sucesso anteriores que o Dispatcher tenta. Para cada tentativa, a pontuação para a categoria do URI solicitado é atualizada.

>[!NOTE]
>
>Se você não usar o balanceamento de carga, poderá omitir esta seção.

### Definindo Categorias de Estatísticas {#defining-statistics-categories}

Defina uma categoria para cada tipo de documento para o qual você deseja manter estatísticas para a seleção de renderização. A seção /statistics contém uma seção /categoria. Para definir uma categoria, adicione uma linha abaixo da seção /categoria que tenha o seguinte formato:

`/name { /glob "pattern"}`

A categoria `name` deve ser exclusiva do farm. O `pattern` é descrito na seção [Design Patterns for globa Properties](#designing-patterns-for-glob-properties) .

Para determinar a categoria de um URI, o Dispatcher compara o URI a cada padrão de categoria até que uma correspondência seja encontrada. O Dispatcher começa com a primeira categoria na lista e a sequência em ordem. Portanto, coloque as categorias com padrões mais específicos primeiro.

Por exemplo, o Dispatcher o arquivo padrão dispatcher.any define uma categoria HTML e outra categoria. A categoria HTML é mais específica e, portanto, aparece primeiro:

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

O exemplo a seguir também inclui uma categoria para páginas de pesquisa:

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Refletindo a indisponibilidade do servidor nas estatísticas do Dispatcher {#reflecting-server-unavailability-in-dispatcher-statistics}

A `/unavailablePenalty` propriedade define o tempo (em décimos de segundo) que é aplicado às estatísticas de renderização quando uma conexão com a renderização falha. O Dispatcher adiciona o tempo à categoria de estatísticas que corresponde ao URI solicitado.

Por exemplo, a penalidade é aplicada quando a conexão TCP/IP com o nome do host/porta designada não pode ser estabelecida, porque o AEM não está em execução (e não está acompanhando) ou devido a um problema relacionado à rede.

A `/unavailablePenalty` propriedade é um filho direto da `/farm` seção (um irmão da `/statistics` seção).

Se não houver nenhuma `/unavailablePenalty` propriedade, um valor de &quot;1&quot; será usado.

```xml
/unavailablePenalty "1"
```

## Como identificar uma pasta de conexão adesiva - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

A `/stickyConnectionsFor` propriedade define uma pasta que contém documentos fixos; isso será acessado usando o URL. O Dispatcher envia todas as solicitações, de um único usuário, que estão nessa pasta para a mesma instância de renderização. As conexões adesivas garantem que os dados da sessão estejam presentes e consistentes para todos os documentos. Este mecanismo usa o `renderid` cookie.

O exemplo a seguir define uma conexão aderente com a pasta /products:

```xml
/stickyConnectionsFor "/products"
```

Quando uma página for composta de conteúdo de vários nós de conteúdo, inclua a `/paths` propriedade que lista os caminhos para o conteúdo. Por exemplo, uma página contém conteúdo de `/content/image`, `/content/video`e `/var/files/pdfs`. A configuração a seguir permite conexões aderentes para todo o conteúdo da página:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

Quando as conexões aderentes estão ativadas, o módulo do dispatcher define o `renderid` cookie. Este cookie não tem o `httponly` sinalizador, que deve ser adicionado para melhorar a segurança. Você pode fazer isso definindo a `httpOnly` propriedade no `/stickyConnections` nó de um arquivo de `dispatcher.any` configuração. O valor da propriedade (0 ou 1) define se o `renderid` cookie tem o `HttpOnly` atributo anexado. O valor padrão é 0, o que significa que o atributo não será adicionado.

Para obter informações adicionais sobre o `httponly` sinalizador, leia [esta página](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Quando as conexões aderentes estão ativadas, o módulo do dispatcher define o `renderid` cookie. Este cookie não tem o sinalizador **seguro** , que deve ser adicionado para melhorar a segurança. Você pode fazer isso definindo a `secure` propriedade no `/stickyConnections` nó de um arquivo de `dispatcher.any` configuração. O valor da propriedade (0 ou 1) define se o `renderid` cookie tem o `secure` atributo anexado. O valor padrão é 0, o que significa que o atributo será adicionado **se** a solicitação recebida for segura. Se o valor for definido como 1, o sinalizador seguro será adicionado independentemente de a solicitação recebida ser ou não segura.

## Tratamento de erros de conexão de renderização {#handling-render-connection-errors}

Configure o comportamento do Dispatcher quando o servidor de renderização retornar um erro 500 ou estiver indisponível.

### Especificação de uma Página de Verificação de Integridade {#specifying-a-health-check-page}

Use a `/health_check` propriedade para especificar um URL marcado quando um código de status 500 ocorrer. Se essa página também retornar um código de status 500, a instância será considerada indisponível e uma penalidade de tempo configurável ( `/unavailablePenalty`) será aplicada à renderização antes de tentar novamente.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Especificação do Atraso de Repetição de Página {#specifying-the-page-retry-delay}

A propriedade / `retryDelay` define o tempo (em segundos) que o Dispatcher aguarda entre as rodadas de tentativas de conexão com as renderizações do farm. Para cada rodada, o número máximo de vezes que o Dispatcher tenta uma conexão com uma renderização é o número de renderizações no farm.

O Dispatcher usa um valor de `"1"` se não `/retryDelay` estiver explicitamente definido. O valor padrão é apropriado na maioria dos casos.

```xml
/retryDelay "1"
```

### Configuração do número de Tentativas {#configuring-the-number-of-retries}

A `/numberOfRetries` propriedade define o número máximo de rodadas de tentativas de conexão que o Dispatcher executa com as renderizações. Se o Dispatcher não conseguir se conectar com êxito a uma renderização após esse número de tentativas, o Dispatcher retornará uma resposta com falha.

Para cada rodada, o número máximo de vezes que o Dispatcher tenta uma conexão com uma renderização é o número de renderizações no farm. Portanto, o número máximo de vezes que o Dispatcher tenta uma conexão é ( `/numberOfRetries`) x (o número de renderizações).

Se o valor não estiver explicitamente definido, o valor padrão será **5**.

```xml
/numberOfRetries "5"
```

### Usando o mecanismo de failover {#using-the-failover-mechanism}

Ative o mecanismo de failover em seu farm do Dispatcher para reenviar solicitações a renderizações diferentes quando a solicitação original falhar. Quando o failover é ativado, o Dispatcher tem o seguinte comportamento:

* Quando uma solicitação para uma renderização retorna o status HTTP 503 (INDISPONÍVEL), o Dispatcher envia a solicitação para uma renderização diferente.
* Quando uma solicitação para uma renderização retorna o status HTTP 50x (diferente de 503), o Dispatcher envia uma solicitação para a página configurada para a `health_check` propriedade.

   * Se a verificação de integridade retornar 500 (INTERNAL_SERVER_ERROR), o Dispatcher enviará a solicitação original para uma renderização diferente.
   * Se a verificação de recuperação retornar o status HTTP 200, o Dispatcher retornará o erro HTTP 500 inicial ao cliente.

Para ativar o failover, adicione a seguinte linha ao farm (ou site):

```xml
/failover "1" 
```

>[!NOTE]
>
>Para repetir solicitações HTTP que contêm um corpo, o Dispatcher envia um cabeçalho de `Expect: 100-continue` solicitação para a renderização antes de spool do conteúdo real. O CQ 5.5 com CQSE responde imediatamente com 100 (CONTINUE) ou um código de erro. Outros container de servlet também devem suportar isso.

## Ignorando Erros de Interrupção - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Essa opção geralmente não é necessária. Só é necessário usá-la quando você vir as seguintes mensagens de log:
>
>`Error while reading response: Interrupted system call`

Qualquer chamada de sistema orientada para o sistema de arquivos pode ser interrompida `EINTR` se o objeto da chamada do sistema estiver localizado em um sistema remoto acessado via NFS. Se essas chamadas do sistema podem expirar ou ser interrompidas, isso se baseia em como o sistema de arquivos subjacente foi montado no computador local.

Use o parâmetro /ignoreEINTR se sua instância tiver tal configuração e o log contiver a seguinte mensagem:

`Error while reading response: Interrupted system call`

Internamente, o Dispatcher lê a resposta do servidor remoto (ou seja, AEM) usando um loop que pode ser representado como:

`while (response not finished) {  
read more data  
}`

Essas mensagens podem ser geradas quando `EINTR` ocorrem na seção &quot; `read more data`&quot; e são causadas pela recepção de um sinal antes de qualquer dado ser recebido.

Para ignorar essas interrupções, adicione o seguinte parâmetro a `dispatcher.any` (antes `/farms`):

`/ignoreEINTR "1"`

A configuração `/ignoreEINTR` como `"1"` faz com que o Dispatcher continue tentando ler os dados até que a resposta completa seja lida. O valor padrão é 0 e desativa a opção.

## Criar padrões para propriedades globais {#designing-patterns-for-glob-properties}

Várias seções no arquivo de configuração do Dispatcher usam `glob` propriedades como critérios de seleção para solicitações do cliente. Os valores das propriedades globais são padrões que o Dispatcher compara a um aspecto da solicitação, como o caminho do recurso solicitado ou o endereço IP do cliente. Por exemplo, os itens na `/filter` seção usam padrões globalizados para identificar os caminhos das páginas nas quais o Dispatcher atua ou rejeita.

Os valores globais podem incluir caracteres curingas e caracteres alfanuméricos para definir o padrão.

| Caracteres válidos | Descrição | Exemplos |
|--- |--- |--- |
| `*` | Corresponde a zero ou mais instâncias contíguas de qualquer caractere na string. O caractere final da correspondência é determinado por uma das seguintes situações: <br/>Um caractere na string corresponde ao caractere seguinte no padrão, e esse caractere tem as seguintes características:<br/><ul><li>Não é um *</li><li>Não é um?</li><li>Um caractere literal (incluindo um espaço) ou uma classe de caractere.</li><li>O fim do padrão é atingido.</li></ul>Dentro de uma classe de caracteres, o caractere é interpretado literalmente. | `*/geo*` Corresponde a qualquer página abaixo do `/content/geometrixx` nó e do `/content/geometrixx-outdoors` nó. As seguintes solicitações HTTP correspondem ao padrão global: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` Corresponde <br/>a qualquer página abaixo do `/content/geometrixx-outdoors` nó. Por exemplo, a seguinte solicitação HTTP corresponde ao padrão global: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Corresponde a qualquer caractere único. Use classes de caracteres externas. Dentro de uma classe de caracteres, esse caractere é interpretado literalmente. | `*outdoors/??/*`<br/> Corresponde as páginas para qualquer idioma no site geometrixx-outdoors. Por exemplo, a seguinte solicitação HTTP corresponde ao padrão global: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>A solicitação a seguir não corresponde ao padrão global: <br/><ul><li>&quot;OBTENHA /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Demarca o início e o fim de uma classe de caracteres. As classes de caracteres podem incluir um ou mais intervalos de caracteres e caracteres únicos.<br/>Uma correspondência ocorre se o caractere de público alvo corresponde a qualquer um dos caracteres na classe de caracteres, ou dentro de um intervalo definido.<br/>Se o colchete de fechamento não estiver incluído, o padrão não produz correspondências. | `*[o]men.html*`<br/> Corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` Corresponde <br/>às seguintes solicitações HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Indica um intervalo de caracteres. Para uso em classes de caracteres.  Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | `*[m-p]men.html*` Corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Nega o caractere ou a classe de caractere a seguir. Use apenas para negar caracteres e intervalos de caracteres nas classes de caracteres. Equivalente ao `^ wildcard`. <br/>Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | `*[!o]men.html*`<br/> Corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Não corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` ou `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Nega o caractere ou o intervalo de caracteres a seguir. Use para negar somente caracteres e intervalos de caracteres nas classes de caracteres. Equivalente ao caractere `!` curinga. <br/>Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | Os exemplos para o caractere curinga se aplicam, substituindo os `!` caracteres nos padrões de exemplo por `!` `^` caracteres. |


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## Registro {#logging}

Na configuração do servidor da Web, é possível definir:

* A localização do arquivo de log do Dispatcher.
* O nível do log.

Consulte a documentação do servidor da Web e o arquivo readme de sua instância do Dispatcher para obter mais informações.

**Logs girados / Piped do Apache**

Se estiver usando um servidor da Web **Apache** , você poderá usar a funcionalidade padrão para registros rotacionados e/ou encanados. Por exemplo, usando registros de tubulação:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Isso girará automaticamente:

* O ficheiro de registro do expedidor; com um carimbo de data e hora na extensão (logs/dispatcher.log%Y%m%d).
* semanalmente (60 x 60 x 24 x 7 = 604800 segundos).

Consulte a documentação do servidor Web Apache em Rotação de log e Logs Piped; por exemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Durante a instalação, o nível de log padrão é alto (ou seja, nível 3 = Depuração), de modo que o Dispatcher registre todos os erros e avisos. Isso é muito útil nas etapas iniciais.
>
>No entanto, isso requer recursos adicionais, portanto, quando o Dispatcher estiver funcionando sem problemas, de *acordo com seus requisitos*, você poderá (deve) diminuir o nível de log.

### Registro de rastreamento {#trace-logging}

Entre outros aprimoramentos para o Dispatcher, a versão 4.2.0 também introduz o Trace Logging.

Este é um nível superior ao do registro de depuração, mostrando informações adicionais nos registros. Ele adiciona registro para:

* Os valores dos cabeçalhos encaminhados;
* A regra que está sendo aplicada para uma determinada ação.

Você pode ativar o Registro de rastreamento definindo o nível de log como `4` no servidor da Web.

Abaixo está um exemplo de registros com rastreamento ativado:

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

E um evento registrado quando um arquivo que corresponde a uma regra de bloqueio é solicitado:

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Confirmação da operação básica {#confirming-basic-operation}

Para confirmar a operação básica e a interação do servidor da Web, do Dispatcher e da instância do AEM, você pode usar as seguintes etapas:

1. Defina `loglevel` como `3`.

1. Start do servidor Web; isso também start o Dispatcher.
1. Start da instância do AEM.
1. Verifique os arquivos de log e de erro do servidor da Web e do Dispatcher.\
   Dependendo do servidor da Web, você deverá ver mensagens como:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   e:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Navegue pelo site através do servidor da Web. Confirme se o conteúdo está sendo exibido, conforme necessário.\
   Por exemplo, em uma instalação local onde o AEM é executado na porta `4502` e o servidor da Web no `80` acesso ao console Sites usando:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Os resultados devem ser idênticos. Confirme o acesso a outras páginas com o mesmo mecanismo.

1. Verifique se o diretório de cache está sendo preenchido.
1. Ative uma página para verificar se o cache está sendo descarregado corretamente.
1. Se tudo estiver funcionando corretamente, você pode reduzir o `loglevel` para `0`.

## Usando vários Dispatchers v{#using-multiple-dispatchers}

Em configurações complexas, você pode usar vários Dispatchers. Por exemplo, você pode usar:

* um Dispatcher para publicar um site na Intranet
* um segundo Dispatcher, em um endereço diferente e com configurações de segurança diferentes, para publicar o mesmo conteúdo na Internet.

Nesse caso, verifique se cada solicitação passa por apenas um Dispatcher. Um Dispatcher não lida com solicitações provenientes de outro Dispatcher. Portanto, verifique se ambos os Dispatchers acessam o site do AEM diretamente.

## Depuração {#debugging}

Ao adicionar o cabeçalho `X-Dispatcher-Info` a uma solicitação, o Dispatcher responde se o público alvo foi armazenado em cache, retornado do cache ou não é armazenado em cache. O cabeçalho de resposta `X-Cache-Info` contém essas informações em um formulário legível. Você pode usar esses cabeçalhos de resposta para depurar problemas que envolvam respostas armazenadas em cache pelo Dispatcher.

Essa funcionalidade não está ativada por padrão, portanto, para que o cabeçalho de resposta seja incluído, o farm deve conter a seguinte entrada: `X-Cache-Info`

```xml
/info "1"
```

Por exemplo,

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

Além disso, o cabeçalho `X-Dispatcher-Info` não precisa de um valor, mas se você usar `curl` para teste, deverá fornecer um valor para enviar o cabeçalho, como:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Abaixo está uma lista contendo os cabeçalhos de resposta que `X-Dispatcher-Info` retornarão:

* **em cache**\
   O arquivo de público alvo está contido no cache e o dispatcher determinou que é válido entregá-lo.
* **cache**\
   O arquivo de público alvo não está contido no cache e o dispatcher determinou que é válido armazenar a saída em cache e entregá-la.
* **armazenamento em cache: o arquivo de estatística é mais recente** O arquivo de público alvo está contido no cache, no entanto, ele é invalidado por um arquivo de estatística mais recente. O dispatcher excluirá o arquivo de público alvo, recriará-o da saída e o entregará.
* **não pode ser armazenado em cache: raiz** do documento A configuração do farm não contém uma raiz do documento (elemento de configuração `cache.docroot`).
* **não pode ser armazenado em cache: caminho do arquivo de cache muito longo**\
   O arquivo de público alvo - a concatenação da raiz e do arquivo URL do documento - excede o nome de arquivo mais longo possível no sistema.
* **não pode ser armazenado em cache: caminho de arquivo temporário muito longo**\
   O modelo de nome de arquivo temporário excede o nome de arquivo mais longo possível no sistema. O dispatcher cria um arquivo temporário primeiro, antes de realmente criar ou substituir o arquivo em cache. O nome do arquivo temporário é o nome do arquivo do público alvo com os caracteres `_YYYYXXXXXX` anexados a ele, onde o nome `Y` e `X` será substituído para criar um nome exclusivo.
* **não pode ser armazenado em cache: o URL de solicitação não tem extensão**\
   O URL da solicitação não tem extensão ou há um caminho após a extensão do arquivo, por exemplo: `/test.html/a/path`.
* **não pode ser armazenado em cache: não era GET ou HEAD** O método HTTP não é GET nem HEAD. O dispatcher supõe que a saída conterá dados dinâmicos que não devem ser armazenados em cache.
* **não pode ser armazenado em cache: solicitação continha uma string de query**\
   A solicitação continha uma string de query. O dispatcher supõe que a saída dependa da string de query fornecida e, portanto, não armazena em cache.
* **não pode ser armazenado em cache: gerenciador de sessão não autenticado**\
   O cache do farm é regido por um gerenciador de sessão (a configuração contém um `sessionmanagement` nó) e a solicitação não contém as informações de autenticação apropriadas.
* **não pode ser armazenado em cache: solicitação contém autorização**\
   O farm não tem permissão para armazenar em cache a saída ( `allowAuthorized 0`) e a solicitação contém informações de autenticação.
* **não pode ser armazenado em cache: público alvo é um diretório**\
   O arquivo de público alvo é um diretório. Isso pode apontar para algum erro conceitual, em que um URL e alguns sub-URL contêm saída em cache, por exemplo, se uma solicitação `/test.html/a/file.ext` chegar primeiro e contiver saída em cache, o dispatcher não poderá armazenar a saída de uma solicitação subsequente em cache para `/test.html`.
* **não pode ser armazenado em cache: o URL da solicitação tem uma barra**\
   O URL da solicitação tem uma barra.
* **não pode ser armazenado em cache: URL de solicitação não nas regras de cache**\
   As regras de cache do farm negam explicitamente o armazenamento em cache da saída de algum URL de solicitação.
* **não pode ser armazenado em cache: acesso negado ao verificador de autorização**\
   O verificador de autorização do farm negou acesso ao arquivo em cache.
* **não pode ser armazenado em cache: sessão inválida** O cache do farm é regido por um gerenciador de sessão (a configuração contém um `sessionmanagement` nó) e a sessão do usuário não é ou não é mais válida.
* **não pode ser armazenado em cache: a resposta contém`no_cache `**O servidor remoto retornou um`Dispatcher: no_cache`cabeçalho, proibindo o dispatcher de armazenar a saída em cache.
* **não pode ser armazenado em cache: comprimento do conteúdo da resposta é zero** O comprimento do conteúdo da resposta é zero; o dispatcher não criará um arquivo de comprimento zero.
