---
title: Configuração do Dispatcher
description: Saiba como configurar o Dispatcher. Saiba mais sobre o suporte para IPv4 e IPv6, arquivos de configuração, variáveis de ambiente, nomeação da instância, definição de farms, identificação de hosts virtuais e muito mais.
exl-id: 91159de3-4ccb-43d3-899f-9806265ff132
source-git-commit: 434a17077cea8958a55a637eddd1f4851fc7f2ee
workflow-type: ht
source-wordcount: '8941'
ht-degree: 100%

---

# Configuração do Dispatcher {#configuring-dispatcher}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

As seções a seguir descrevem como configurar vários aspectos do Dispatcher.

## Suporte para IPv4 e IPv6 {#support-for-ipv-and-ipv}

Todos os elementos do AEM e do Dispatcher podem ser instalados em redes IPv4 e IPv6. Consulte [IPV4 e IPV6](https://experienceleague.adobe.com/docs/experience-manager-65/deploying/introduction/technical-requirements.html?lang=pt-BR#ipv-and-ipv).

## Arquivos de configuração do Dispatcher {#dispatcher-configuration-files}

Por padrão, a configuração do Dispatcher é armazenada no arquivo de texto `dispatcher.any`, embora você possa alterar o nome e o local desse arquivo durante a instalação.

O arquivo de configuração contém uma série de propriedades com valor único ou com vários valores que controlam o comportamento do Dispatcher:

* Os nomes de propriedades recebem o prefixo de uma barra `/`.
* As propriedades com vários valores incluem itens secundários usando chaves `{ }`.

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

* Se o arquivo de configuração for grande, é possível dividi-lo em vários arquivos menores (que são mais fáceis de gerenciar) e incluir cada um deles.
* Para incluir arquivos gerados automaticamente.

Por exemplo, para incluir o arquivo myFarm.any na configuração de /farms, use o seguinte código:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Para especificar um grupo de arquivos a serem incluídos, use o asterisco (`*`) como um curinga.

Por exemplo, se os arquivos `farm_1.any` até `farm_5.any` contiverem a configuração de farms de um a cinco, você poderá incluí-los da seguinte maneira:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Uso de variáveis de ambiente {#using-environment-variables}

Você pode usar variáveis de ambiente em propriedades com valores de cadeias de caracteres no arquivo dispatcher.any, em vez de codificar os valores. Para incluir o valor de uma variável de ambiente, use o formato `${variable_name}`.

Por exemplo, se o arquivo dispatcher.any estiver localizado no mesmo diretório do cache, o seguinte valor poderá ser usado para a propriedade [docroot](#specifying-the-cache-directory):

```xml
/docroot "${PWD}/cache"
```

Como outro exemplo, se você criar uma variável de ambiente chamada `PUBLISH_IP`, que armazena o nome do host da instância de publicação do AEM, a seguinte configuração da propriedade [/renders](#defining-page-renderers-renders) poderá ser usada:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Nomenclatura da instância do Dispatcher {#naming-the-dispatcher-instance-name}

Use a propriedade `/name` para especificar um nome exclusivo que identifique a instância do Dispatcher. A propriedade `/name` é uma propriedade de nível superior na estrutura de configuração.

## Definição de farms {#defining-farms-farms}

A propriedade `/farms` define um ou mais conjuntos de comportamentos do Dispatcher, em que cada conjunto é associado a sites ou URLs diferentes. A propriedade `/farms` pode incluir um único farm ou vários farms:

* Use um único farm quando quiser que o Dispatcher lide com todas as páginas da Web ou sites da mesma maneira.
* Crie vários farms quando áreas diferentes do seu site ou sites diferentes exigirem um comportamento diferente do Dispatcher.

A propriedade `/farms` é uma propriedade de nível superior na estrutura de configuração. Para definir um farm, adicione uma propriedade secundária à propriedade `/farms`. Use um nome de propriedade que identifique exclusivamente o farm na instância do Dispatcher.

A propriedade `/farmname` tem vários valores e contém outras propriedades que definem o comportamento do Dispatcher:

* Os URLs das páginas às quais o farm se aplica.
* Um ou mais URLs de serviço (normalmente de instâncias de publicação do AEM) para usar na renderização de documentos.
* As estatísticas que serão usadas para balanceamento de carga em vários renderizadores de documento.
* Vários outros comportamentos, como quais arquivos armazenar em cache e onde.

O valor pode incluir qualquer caractere alfanumérico (a-z, 0-9). O exemplo a seguir mostra a definição do esqueleto para dois farms chamados `/daycom` e `/docsdaycom`:

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
>Se você usar mais de um farm de renderização, a lista será avaliada de baixo para cima. Esse fluxo é particularmente relevante ao definir [hosts virtuais](#identifying-virtual-hosts-virtualhosts) para seus sites.

Cada propriedade farm pode conter as seguintes propriedades secundárias:

| Nome da propriedade | Descrição |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Página inicial padrão (opcional) (somente IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Os cabeçalhos da solicitação HTTP do cliente que serão transmitidos. |
| [/virtualhosts](#identifying-virtual-hosts-virtualhosts) | Os hosts virtuais para este farm. |
| [/sessionmanagement](#enabling-secure-sessions-sessionmanagement) | Suporte para gerenciamento e autenticação de sessão. |
| [/renders](#defining-page-renderers-renders) | Os servidores que fornecem páginas renderizadas (normalmente instâncias de publicação do AEM). |
| [/filter](#configuring-access-to-content-filter) | Define os URLs aos quais o Dispatcher permite acesso. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configura o acesso a URLs personalizados. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagatesyndpost) | Suporte para o encaminhamento de solicitações de sindicalização. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configura o comportamento de armazenamento em cache. |
| [/statistics](#configuring-load-balancing-statistics) | Definição de categorias estatísticas para cálculos de balanceamento de carga. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-stickyconnectionsfor) | A pasta que contém documentos fixos. |
| [/health_check](#specifying-a-health-check-page) | O URL a ser usado para determinar a disponibilidade do servidor. |
| [/retryDelay](#specifying-the-page-retry-delay) | O atraso antes de tentar novamente uma conexão com falha. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Sanções que afetam as estatísticas para cálculos de balanceamento de carga. |
| [/failover](#using-the-failover-mechanism) | Reenvia solicitações para renderizações diferentes quando a solicitação original falha. |
| [/auth_checker](permissions-cache.md) | Para armazenamento em cache sensível a permissões, consulte [Armazenamento em cache de conteúdo protegido](permissions-cache.md). |

## Especificação de uma página padrão (somente IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>O parâmetro `/homepage` (somente IIS) não funciona mais. Em vez disso, você deve usar o [Módulo de regravação de URL do IIS](https://learn.microsoft.com/pt-br/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Se você estiver usando o Apache, deve usar o módulo `mod_rewrite`. Consulte a documentação do site do Apache para obter informações sobre `mod_rewrite` (por exemplo, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Ao usar `mod_rewrite`, é aconselhável usar o sinalizador “passthrough|PT” (que transmite para o próximo manipulador) para forçar o mecanismo de reescrita a definir o campo `uri` da estrutura interna `request_rec` com o valor do campo `filename`.

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

## Especificação dos cabeçalhos HTTP que serão transmitidos {#specifying-the-http-headers-to-pass-through-clientheaders}

A propriedade `/clientheaders` define uma lista de cabeçalhos HTTP que o Dispatcher transmite da solicitação HTTP do cliente para o renderizador (instância do AEM).

Por padrão, o Dispatcher encaminha os cabeçalhos HTTP padrão para a instância do AEM. Em algumas instâncias, você pode encaminhar cabeçalhos adicionais ou remover cabeçalhos específicos:

* Adicione cabeçalhos, como cabeçalhos personalizados, que sua instância do AEM espera na solicitação HTTP.
* Remova cabeçalhos, como cabeçalhos de autenticação, que são relevantes apenas para o servidor web.

Se você personalizar o conjunto de cabeçalhos que serão transmitidos, será necessário especificar uma lista completa de cabeçalhos, incluindo aqueles que normalmente são incluídos por padrão.

Por exemplo, uma instância do Dispatcher que lida com solicitações de ativação de página para instâncias de publicação requer o cabeçalho `PATH` na seção `/clientheaders`. O cabeçalho `PATH` permite a comunicação entre o agente de replicação e o Dispatcher.

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

## Identificação de hosts virtuais {#identifying-virtual-hosts-virtualhosts}

A propriedade `/virtualhosts` define uma lista de todas as combinações de nome de host/URI aceitas pelo Dispatcher para este farm. Você pode usar o caractere asterisco (`*`) como curinga. Os valores para a propriedade / `virtualhosts` usam o seguinte formato:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Opcional) `https://` ou `https://.`
* `host`: O nome ou endereço IP do computador host e o número da porta, se necessário. (Consulte [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`: (Opcional) O caminho para os recursos.

O exemplo de configuração a seguir lida com solicitações para os domínios `.com` e `.ch` de myCompany, bem como todos os domínios de mySubDivision:

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

Quando o Dispatcher recebe uma solicitação HTTP ou HTTPS, ele encontra o valor do host virtual que melhor corresponde aos cabeçalhos `host,` `uri` e `scheme` da solicitação. O Dispatcher avalia os valores nas propriedades `virtualhosts` na seguinte ordem:

* O Dispatcher começa no farm mais baixo e avança para cima no arquivo dispatcher.any.
* Para cada farm, o Dispatcher começa com o valor mais alto na propriedade `virtualhosts` e avança para baixo na lista de valores.

O Dispatcher encontra o melhor valor de host virtual correspondente da seguinte maneira:

* O primeiro host virtual encontrado que corresponde a todos os três do `host`, o `scheme` e o `uri` da solicitação é usado.
* Se nenhum valor de `virtualhosts` tiver partes de `scheme` e `uri` que correspondam ao `scheme` e `uri` da solicitação, o primeiro host virtual encontrado que corresponda ao `host` da solicitação será usado.
* Se nenhum valor `virtualhosts` tiver uma parte do host que corresponda ao host da solicitação, o host virtual mais acima do farm mais acima será usado.

Portanto, você deve colocar seu host virtual padrão na parte superior da propriedade `virtualhosts` no farm mais acima do arquivo `dispatcher.any`.

### Exemplo de resolução de host virtual {#example-virtual-host-resolution}

O exemplo a seguir representa um trecho de um arquivo `dispatcher.any` que define dois farms do Dispatcher, e cada farm define uma propriedade `virtualhosts`.

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

## Ativação de sessões seguras - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>Defina `/allowAuthorized` como `"0"` na seção `/cache` para ativar esse recurso. Conforme detalhado na seção [Armazenamento em cache quando a autenticação é usada](#caching-when-authentication-is-used), ao definir solicitações `/allowAuthorized 0 ` que incluem informações de autenticação que **não** estão em cache. Se o armazenamento em cache com permissão confidencial for necessário, consulte a página [Armazenamento em cache de conteúdo protegido](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/permissions-cache.html?lang=pt-BR).

Crie uma sessão segura para acessar o farm de renderização, de modo que os usuários precisem fazer logon para acessar qualquer página no farm. Depois de fazer logon, os usuários podem acessar as páginas no farm. Consulte [Criação de um grupo fechado de usuários](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/cug.html?lang=pt-BR#creating-the-user-group-to-be-used) para informações sobre como usar este recurso com CUGs. Além disso, consulte a [Lista de verificação de segurança](/help/using/security-checklist.md) do Dispatcher antes de entrar em atividade.

A propriedade `/sessionmanagement` é uma subpropriedade de `/farms`.

>[!CAUTION]
>
>Se as seções do seu site tiverem requisitos de acesso diferentes, você precisará definir vários farms.

**/sessionmanagement** tem vários subparâmetros:

**/directory** (obrigatório)

O diretório que armazena as informações da sessão. Se o diretório não existir, ele será criado.

>[!CAUTION]
>
> Ao configurar o subparâmetro de diretório, **não** aponte para a pasta raiz (`/directory "/"`), pois isso pode causar problemas graves. Sempre especifique o caminho para a pasta que armazena as informações da sessão. Por exemplo:

```xml
/sessionmanagement
  {
  /directory "/usr/local/apache/.sessions"
  }
```

**/encode** (opcional)

Como as informações da sessão são codificadas. Use `md5` para criptografia usando o algoritmo md5 ou `hex` para codificação hexadecimal. Se você criptografar os dados da sessão, um usuário com acesso ao sistema de arquivos não poderá ler o conteúdo da sessão. O padrão é `md5`.

**/header** (opcional)

O nome do cabeçalho ou cookie HTTP que armazena as informações de autorização. Se você armazenar as informações no cabeçalho http, use `HTTP:<header-name>`. Para armazenar as informações em um cookie, use `Cookie:<header-name>`. Se você não especificar um valor, `HTTP:authorization` será usado.

**/timeout** (opcional)

O número de segundos até que a sessão atinja o tempo limite após ter sido usada por último. Se `"800"` não especificado for usado, a sessão expira pouco mais de 13 minutos após a última solicitação do usuário.

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

## Definição de renderizadores de página {#defining-page-renderers-renders}

A propriedade /renders define o URL para o qual o Dispatcher envia solicitações para renderizar um documento. O exemplo de seção `/renders` a seguir identifica uma única instância do AEM para renderização:

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

O exemplo de seção /renders a seguir identifica uma instância do AEM que é executada no mesmo computador que o Dispatcher:

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

O exemplo de seção /renders a seguir distribui as solicitações de renderização igualmente entre duas instâncias do AEM:

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

Especifica o tempo limite da conexão acessando a instância do AEM em milissegundos. O padrão é `"0"`, fazendo com que o Dispatcher aguarde indefinidamente.

**/receiveTimeout**

Especifica o tempo em milissegundos que uma resposta pode demorar. O padrão é `"600000"`, fazendo com que o Dispatcher aguarde 10 minutos. Uma configuração de `"0"` elimina o tempo limite.

Se o tempo limite for atingido durante a análise dos cabeçalhos de resposta, um Status 504 de HTTP (Gateway incorreto) será retornado. Se o tempo limite for atingido enquanto o corpo da resposta estiver sendo lido, o Dispatcher retornará a resposta incompleta ao cliente. Isso também exclui qualquer arquivo de cache que possa ter sido gravado.

**/ipv4**

Especifica se o Dispatcher usa a função `getaddrinfo` (para IPv6) ou a função `gethostbyname` (para IPv4) para obter o endereço IP da renderização. Um valor de 0 faz com que `getaddrinfo` seja usado. Um valor `1` faz com que `gethostbyname` seja usado. O valor padrão é `0`.

A função `getaddrinfo` retorna uma lista de endereços IP. O Dispatcher repete a lista de endereços até estabelecer uma conexão TCP/IP. Portanto, a propriedade `ipv4` é importante quando o nome do host de renderização está associado a vários endereços IP e o host, em resposta à função `getaddrinfo`, retorna uma lista de endereços IP que estão sempre na mesma ordem. Nessa situação, você deve usar a função `gethostbyname` para que o endereço IP com o qual o Dispatcher se conecte seja aleatório.

O Elastic Load Balancing (ELB) da Amazon é um serviço que responde a getaddrinfo com uma lista de endereços IP potencialmente iguais.

**/secure**

Se a propriedade `/secure` tiver um valor de `"1"`, o Dispatcher usará HTTPS para se comunicar com a instância do AEM. Para obter detalhes adicionais, consulte também [Configuração do Dispatcher para usar SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Com a versão do Dispatcher **4.1.6**, você pode configurar a propriedade `/always-resolve` da seguinte maneira:

* Se definido como `"1"`, ele resolverá o nome do host em cada solicitação (o Dispatcher nunca armazenará em cache nenhum endereço IP). Pode haver um pequeno impacto no desempenho devido à chamada adicional necessária para obter as informações do host para cada solicitação.
* Se a propriedade não for definida, o endereço IP será armazenado em cache por padrão.

Além disso, essa propriedade pode ser usada caso você tenha problemas de resolução de IP dinâmico, conforme mostrado no exemplo a seguir:

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

Use a seção `/filter` para especificar as solicitações HTTP aceitas pelo Dispatcher. Todas as outras solicitações são enviadas de volta ao servidor Web com um código de erro 404 (página não encontrada). Se não existir nenhuma seção `/filter`, todas as solicitações serão aceitas.

**Observação:** as solicitações para o [arquivo de status](#naming-the-statfile) são sempre rejeitadas.

>[!CAUTION]
>
>Consulte a [Lista de verificação de segurança do Dispatcher](security-checklist.md) para considerações adicionais ao restringir o acesso usando o Dispatcher. Além disso, leia a [Lista de verificação de segurança do AEM](https://experienceleague.adobe.com/docs/experience-manager-65/administering/security/security-checklist.html?lang=pt-BR#security) para obter detalhes de segurança adicionais relacionados à instalação do AEM.

A seção `/filter` consiste em uma série de regras que negam ou permitem acesso ao conteúdo de acordo com os padrões na parte da linha da solicitação HTTP. Use uma estratégia de lista de permissões para a seção `/filter`:

* Primeiro, negar acesso a tudo.
* Permita acesso ao conteúdo conforme necessário.

>[!NOTE]
>
>Remova o cache sempre que houver qualquer alteração nas regras de filtro.

### Definição de um filtro {#defining-a-filter}

Cada item na seção `/filter` inclui um tipo e um padrão que são combinados com um elemento específico da linha de solicitação ou de toda a linha de solicitação. Cada filtro pode conter os seguintes itens:

* **Tipo**: o `/type` indica se o acesso às solicitações que correspondem ao padrão deve ser permitido ou negado. O valor pode ser `allow` ou `deny`.

* **Elemento da linha de solicitação:** inclua `/method`, `/url`, `/query` ou `/protocol` e um padrão para filtrar solicitações de acordo com essas partes específicas da parte da linha de solicitação da solicitação HTTP. A filtragem de elementos da linha de solicitação (em vez da linha de solicitação inteira) é o método de filtro preferido.

* **Elementos avançados da linha de solicitação:** a partir do Dispatcher 4.2.0, quatro novos elementos de filtro estarão disponíveis para uso. Esses novos elementos são `/path`, `/selectors`, `/extension` e `/suffix`, respectivamente. Inclua um ou mais desses itens para controlar ainda mais os padrões de URL.

>[!NOTE]
>
>Para obter mais informações sobre a que parte da linha de solicitação cada um desses elementos faz referência, consulte a página wiki [Sling URL Decomposition](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html).

* **Propriedade glob**: a propriedade `/glob` é usada para corresponder a toda a linha de solicitação da solicitação HTTP.

>[!CAUTION]
>
>A filtragem com globs está obsoleta no Dispatcher. Dessa forma, você deve evitar o uso de globs nas seções `/filter`, pois pode causar problemas de segurança. Então, em vez de:
>
>`/glob "* *.css *"`
>
>use
>
>`/url "*.css"`

#### A parte da linha de solicitação de solicitações HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 define o [request-line](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) da seguinte maneira:

`Method Request-URI HTTP-Version<CRLF>`

Os caracteres `<CRLF>` representam um retorno de carro seguido por um feed de linha. O exemplo a seguir é a linha de solicitação recebida quando um cliente solicita a página em inglês dos EUA do site WKND:

`GET /content/wknd/us/en.html HTTP.1.1<CRLF>`

Seus padrões devem levar em conta os caracteres de espaço na linha de solicitação e os caracteres `<CRLF>`.

#### Aspas duplas vs. aspas simples {#double-quotes-vs-single-quotes}

Ao criar suas regras de filtro, use aspas duplas `"pattern"` para padrões simples. Se você estiver usando o Dispatcher 4.2.0 ou posterior e seu padrão incluir uma expressão regular, será necessário colocar o padrão regex `'(pattern1|pattern2)'` entre aspas simples.

#### Expressões regulares {#regular-expressions}

Nas versões posteriores do Dispatcher 4.2.0, é possível incluir expressões regulares POSIX estendidas nos padrões de filtro.

#### Resolução de problemas de filtros {#troubleshooting-filters}

Se os filtros não estiverem sendo acionados da maneira esperada, ative a opção [Registro de rastreamento](#trace-logging) no Dispatcher para ver qual filtro está interceptando a solicitação.

#### Exemplo de filtro: Negar tudo {#example-filter-deny-all}

O exemplo de seção de filtro a seguir faz com que o Dispatcher negue solicitações para todos os arquivos. Negue o acesso a todos os arquivos e permita o acesso a áreas específicas.

```xml
/0001  { /type "deny" /url "*"  }
```

As solicitações para uma área explicitamente negada resultam no retorno de um código de erro 404 (página não encontrada).

#### Exemplo de filtro: Negar acesso a áreas específicas {#example-filter-deny-access-to-specific-areas}

Os filtros também permitem negar o acesso a vários elementos, por exemplo, páginas ASP e áreas confidenciais em uma instância de publicação. O filtro a seguir nega acesso a páginas ASP:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Exemplo de filtro: Ativar solicitações POST {#example-filter-enable-post-requests}

O exemplo de filtro a seguir permite o envio de dados de formulário pelo método POST:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Exemplo de filtro: Permitir acesso ao console Fluxo de trabalho {#example-filter-allow-access-to-the-workflow-console}

O exemplo a seguir mostra um filtro usado para negar acesso externo ao console Fluxo de trabalho:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Se a instância de publicação usar um contexto de aplicativo web (por exemplo, publicação), ele também poderá ser adicionado à definição do filtro.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Se ainda precisar acessar páginas únicas na área restrita, você poderá permitir o acesso a elas. Por exemplo, para permitir o acesso à guia Arquivo, no console Fluxo de trabalho, adicione a seguinte seção:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Quando vários padrões de filtros se aplicarem a uma solicitação, o último padrão de filtro utilizado será aplicado.

#### Exemplo de filtro: Usar expressões regulares {#example-filter-using-regular-expressions}

Esse filtro permite extensões em diretórios de conteúdo não acessível ao público usando uma expressão regular, definida aqui entre aspas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Exemplo de filtro: Filtrar elementos adicionais de um URL de solicitação {#example-filter-filter-additional-elements-of-a-request-url}

Veja abaixo um exemplo de regra que bloqueia a captura de conteúdo do caminho `/content` e de sua árvore secundária, usando filtros para caminho, seletores e extensões:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy|sysview|docview|query|jcr:content|_jcr_content|search|childrenlist|ext|assets|assetsearch|[0-9-]+)'
        /extension '(json|xml|html|feed))'
        }
```

### Exemplo de seção /filtro {#example-filter-section}

Ao configurar o Dispatcher, você deve restringir o acesso externo o máximo possível. O exemplo a seguir fornece acesso mínimo para visitantes externos:

* `/content`
* conteúdos diversos, como designs e bibliotecas de clientes. Por exemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Depois de criar filtros, [teste o acesso à página](#testing-dispatcher-security) para garantir que sua instância do AEM esteja segura.

A seguinte seção `/filter`, do arquivo `dispatcher.any`, pode ser usada como base em seu [Arquivo de configuração do Dispatcher.](#dispatcher-configuration-files)

Este exemplo é baseado no arquivo de configuração padrão fornecido com o Dispatcher e destina-se a ser usado como exemplo em um ambiente de produção. Itens com o prefixo `#` são desativados (comentados). Deve-se tomar cuidado caso decida ativar qualquer um desses itens (removendo o `#` dessa linha). Fazer isso pode impactar a segurança.

Negue acesso a tudo e, em seguida, permita o acesso a elementos específicos (limitados):

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
      /0001  { /type "deny" /url "*"  }

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
>Quando usado com o Apache, crie seus padrões de filtros de URL de acordo com a propriedade DispatcherUseProcessedURL do módulo Dispatcher. (Consulte [Apache Web Server - Configuração do Apache Web Server para o Dispatcher](dispatcher-install.md##apache-web-server-configure-apache-web-server-for-dispatcher).)

<!----
>[!NOTE]
>
>Filters `0030` and `0031` regarding Dynamic Media are applicable to AEM 6.0 and higher. -->

Considere as seguintes recomendações se optar por estender o acesso:

* Desative o acesso externo a `/admin` se você estiver usando o CQ versão 5.4 ou anterior.

* Tenha cuidado ao permitir o acesso a arquivos em `/libs`. O acesso deve ser permitido de maneira individual.
* Negar acesso à configuração de replicação para que ela não possa ser vista:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Negar acesso ao proxy reverso de Google Gadgets:

   * `/libs/opensocial/proxy*`

Dependendo da sua instalação, pode haver recursos adicionais em `/libs`, `/apps` ou em outro lugar, que devem ser disponibilizados. Você pode usar o arquivo `access.log` como um método para determinar os recursos que estão sendo acessados externamente.

>[!CAUTION]
>
>O acesso a consoles e diretórios pode apresentar um risco de segurança para ambientes de produção. A menos que você tenha justificativas explícitas, elas devem permanecer desativadas (comentadas).

>[!CAUTION]
>
>Se estiver [usando relatórios em um ambiente de publicação](https://experienceleague.adobe.com/docs/experience-manager-65/administering/operations/reporting.html?lang=pt-BR#using-reports-in-a-publish-environment), configure o Dispatcher para negar o acesso a `/etc/reports` para visitantes externos.

### Restrição de cadeias de caracteres de consulta {#restricting-query-strings}

Desde a versão 4.1.5 do Dispatcher, use a seção `/filter` para restringir cadeias de caracteres de consulta. É altamente recomendável permitir explicitamente cadeias de caracteres de consulta e excluir a permissão genérica por meio de elementos de filtro `allow`.

Uma única entrada pode ter `glob` ou alguma combinação de `method`, `url`, `query` e `version`, mas não ambos. O exemplo a seguir permite a cadeia de caracteres de consulta `a=*` e nega todas as outras para URLs resolvidos para o nó `/etc`:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Se uma regra tiver um `/query`, ela só corresponderá às solicitações que tenham uma sequência de consulta e correspondam ao padrão de consulta fornecido.
>
>No exemplo acima, se as solicitações para `/etc` que não têm cadeia de caracteres de consulta também forem permitidas, as seguintes regras serão necessárias:

```xml
/filter {  
>/0001 { /type "deny" /method "*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type "deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Teste da segurança do Dispatcher {#testing-dispatcher-security}

Os filtros do Dispatcher devem bloquear o acesso às seguintes páginas e scripts nas instâncias de publicação do AEM. Use um navegador da Web para tentar abrir as seguintes páginas, como um visitante do site faria, e verificar se um código 404 é retornado. Se qualquer outro resultado for obtido, ajuste os filtros.

Você deve ver a renderização normal da página para `/content/add_valid_page.html?debug=layout`.

* `/admin`
* `/system/console`
* `/dav/crx.default`
* `/crx`
* `/bin/crxde/logs`
* `/jcr:system/jcr:versionStorage.json`
* `/_jcr_system/_jcr_versionStorage.json`
* `/libs/wcm/core/content/siteadmin.html`
* `/libs/collab/core/content/admin.html`
* `/libs/cq/ui/content/dumplibs.html`
* `/var/linkchecker.html`
* `/etc/linkchecker.html`
* `/home/users/a/admin/profile.json`
* `/home/users/a/admin/profile.xml`
* `/libs/cq/core/content/login.json`
* `/content/../libs/foundation/components/text/text.jsp`
* `/content/.{.}/libs/foundation/components/text/text.jsp`
* `/apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata`
* `/libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet`
* `/content.pages.json`
* `/content.languages.json`
* `/content.blueprint.json`
* `/content.-1.json`
* `/content.10.json`
* `/content.infinity.json`
* `/content.tidy.json`
* `/content.tidy.-1.blubber.json`
* `/content/dam.tidy.-100.json`
* `/content/content/geometrixx.sitemap.txt `
* `/content/add_valid_page.query.json?statement=//*`
* `/content/add_valid_page.qu%65ry.js%6Fn?statement=//*`
* `/content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)`
* `/content/add_valid_path_to_a_page/_jcr_content.json`
* `/content/add_valid_path_to_a_page/jcr:content.json`
* `/content/add_valid_path_to_a_page/_jcr_content.feed`
* `/content/add_valid_path_to_a_page/jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename._jcr_content.feed`
* `/content/add_valid_path_to_a_page/pagename.jcr:content.feed`
* `/content/add_valid_path_to_a_page/pagename.docview.xml`
* `/content/add_valid_path_to_a_page/pagename.docview.json`
* `/content/add_valid_path_to_a_page/pagename.sysview.xml`
* `/etc.xml`
* `/content.feed.xml`
* `/content.rss.xml`
* `/content.feed.html`
* `/content/add_valid_page.html?debug=layout`
* `/projects`
* `/tagging`
* `/etc/replication.html`
* `/etc/cloudservices.html`
* `/welcome`

Para determinar se o acesso de gravação anônimo está ativado, execute o seguinte comando em um terminal ou prompt de comando. Não deverá ser possível gravar dados no nó.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Para tentar invalidar o cache do Dispatcher e garantir que você receba uma resposta de código 403, execute o seguinte comando em um terminal ou prompt de comando:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Ativação do acesso a URLs personalizados {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The "com.adobe.granite.dispatcher.vanityurl.content" package needs to be made public before publishing this contnet.</p>
 -->

Configure o Dispatcher para ativar o acesso a URLs personalizados que estão configurados para suas páginas do AEM.

Quando o acesso a URLs personalizados está ativado, o Dispatcher chama periodicamente um serviço que é executado na instância de renderização para obter uma lista de URLs personalizados. O Dispatcher armazena essa lista em um arquivo local. Quando uma solicitação de página é negada devido a um filtro na seção `/filter`, o Dispatcher consulta a lista de URLs personalizados. Se o URL negado estiver na lista, o Dispatcher permitirá acesso ao URL personalizado.

Para habilitar o acesso a URLs personalizados, adicione uma seção `/vanity_urls` à seção `/farms`, semelhante ao exemplo a seguir:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

A seção `/vanity_urls` contém as seguintes propriedades:

* `/url`: o caminho para o serviço de URL personalizado que é executado na instância de renderização. O valor dessa propriedade deve ser `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: o caminho para o arquivo local onde o Dispatcher armazena a lista de URLs personalizados. Verifique se o Dispatcher tem acesso de gravação a esse arquivo.
* `/delay`: (segundos) o tempo entre chamadas para o serviço de URL personalizado.

>[!NOTE]
>
>Se a renderização for uma instância do AEM, você deverá instalar o [pacote VanityURLS-Components da Distribuição de softwares](https://experience.adobe.com/#/downloads/content/software-distribution/br/aem.html?package=/content/software-distribution/en/details.html/content/dam/aem/public/adobe/packages/granite/vanityurls-components) para ativar o serviço de URLs personalizados. (Consulte [Distribuição de softwares](https://experienceleague.adobe.com/docs/experience-manager-65/administering/contentmanagement/package-manager.html?lang=pt-BR#software-distribution) para obter mais detalhes.)

Use o procedimento a seguir para habilitar o acesso a URLs personalizados.

1. Se o serviço de renderização for uma instância do AEM, instale o pacote `com.adobe.granite.dispatcher.vanityurl.content` na instância de publicação (consulte a nota acima).
1. Para cada URL personalizado que você configurou para uma página do AEM ou do CQ, verifique se a configuração [`/filter`](#configuring-access-to-content-filter) nega o URL. Se necessário, adicione um filtro que negue o URL.
1. Adicione a seção `/vanity_urls` abaixo de `/farms`.
1. Reinicie o Apache Web Server.

## Encaminhamento de solicitações de sindicalização - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

As solicitações de distribuição destinam-se apenas ao Dispatcher, de modo que, por padrão, elas não são enviadas ao renderizador (por exemplo, uma instância do AEM).

Se necessário, defina a propriedade `/propagateSyndPost` como `"1"` para encaminhar solicitações de sindicalização ao Dispatcher. Se definido, você deve garantir que as solicitações POST não sejam negadas na seção de filtro.

## Configuração do cache do Dispatcher - /cache {#configuring-the-dispatcher-cache-cache}

A seção `/cache` controla como o Dispatcher armazena documentos em cache. Configure várias subpropriedades para implementar suas estratégias de armazenamento em cache:

* `/docroot`
* `/statfile`
* `/serveStaleOnError`
* `/allowAuthorized`
* `/rules`
* `/statfileslevel`
* `/invalidate`
* `/invalidateHandler`
* `/allowedClients`
* `/ignoreUrlParams`
* `/headers`
* `/mode`
* `/gracePeriod`
* `/enableTTL`

Um exemplo de seção de cache pode se parecer como o seguinte:

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
>Para armazenamento em cache sensível a permissões, leia [Armazenamento em cache de conteúdo protegido](permissions-cache.md).

### Especificação do diretório de cache {#specifying-the-cache-directory}

A propriedade `/docroot` identifica o diretório em que os arquivos em cache são armazenados.

>[!NOTE]
>
>O valor deve ser exatamente o mesmo caminho da raiz do documento do servidor Web, para que o Dispatcher e o servidor Web lidem com os mesmos arquivos.\
>O servidor da Web é responsável por fornecer o código de status correto quando o arquivo de cache do Dispatcher é usado, por isso é importante que ele também possa encontrá-lo.

Se você usar vários farms, cada farm deverá usar uma raiz de documento diferente.

### Nomenclatura do arquivo de status {#naming-the-statfile}

A propriedade `/statfile` identifica o arquivo a ser usado como o arquivo de status. O Dispatcher usa esse arquivo para registrar a hora da atualização de conteúdo mais recente. O arquivo de status pode ser qualquer arquivo no servidor Web.

O arquivo de status não tem conteúdo. Quando o conteúdo é atualizado, o Dispatcher atualiza o carimbo de data e hora. O arquivo de status padrão é nomeado como `.stat` e armazenado no docroot. O Dispatcher bloqueia o acesso ao arquivo.

>[!NOTE]
>
>Se `/statfileslevel` estiver configurado, o Dispatcher ignorará a propriedade `/statfile` e usará `.stat` como o nome.

### Entrega de documentos obsoletos em caso de erros {#serving-stale-documents-when-errors-occur}

A propriedade `/serveStaleOnError` controla se o Dispatcher retorna documentos invalidados quando o servidor de renderização retorna um erro. Por padrão, quando um arquivo de status é tocado e invalida o conteúdo em cache, o Dispatcher exclui o conteúdo em cache na próxima vez que for solicitado.

Se `/serveStaleOnError` estiver definido como `"1"`, o Dispatcher não excluirá o conteúdo invalidado do cache, a menos que o servidor de renderização retorne uma resposta bem-sucedida. Uma resposta 5xx do AEM ou um tempo limite de conexão faz com que o Dispatcher forneça o conteúdo desatualizado e responda com e o Status HTTP de 111 (Falha na revalidação).

### Armazenamento em cache quando a autenticação é usada {#caching-when-authentication-is-used}

A propriedade `/allowAuthorized` controla se as solicitações que contêm qualquer uma das seguintes informações de autenticação são armazenadas em cache:

* O cabeçalho `authorization`
* Um cookie chamado `authorization`
* Um cookie chamado `login-token`

Por padrão, as solicitações que incluem essas informações de autenticação não são armazenadas em cache porque a autenticação não é executada quando um documento em cache é retornado ao cliente. Essa configuração impede que o Dispatcher forneça documentos em cache para usuários que não têm os direitos necessários.

No entanto, se seus requisitos permitirem o armazenamento em cache de documentos autenticados, defina `/allowAuthorized` como um:

`/allowAuthorized "1"`

>[!NOTE]
>
>Para habilitar o gerenciamento de sessão (usando a propriedade `/sessionmanagement` ), a propriedade `/allowAuthorized` deve ser definida como `"0"`.

### Especificação de documentos a serem armazenados em cache {#specifying-the-documents-to-cache}

A propriedade `/rules` controla quais documentos são armazenados em cache de acordo com o caminho do documento. Independentemente da propriedade `/rules`, o Dispatcher nunca armazena em cache um documento nas seguintes circunstâncias:

* Se o URI da solicitação tiver um ponto de interrogação (`?`).
   * Indica uma página dinâmica, como um resultado de pesquisa, que não precisa ser armazenado em cache.
* A extensão do arquivo está ausente.
   * O servidor web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (configurável).
* Se a instância do AEM responder com os seguintes cabeçalhos:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Os métodos GET ou HEAD (para o cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. Para obter informações adicionais sobre o armazenamento em cache do cabeçalho de resposta, consulte a seção [Armazenamento em cache de cabeçalhos de resposta HTTP](#caching-http-response-headers).

Cada item na propriedade `/rules` inclui um padrão [`glob`](#designing-patterns-for-glob-properties) e um tipo:

* O padrão `glob` é usado para corresponder ao caminho do documento.
* O tipo indica se os documentos que correspondem ao padrão `glob` devem ser armazenados em cache. O valor pode ser `allow` (para armazenar o documento em cache) ou `deny` (para sempre renderizar o documento).

Se você não tiver páginas dinâmicas (além das já excluídas pelas regras acima), poderá configurar o Dispatcher para armazenar tudo em cache. A seção de regras se parece com o seguinte:

```xml
/rules
  {
    /0000  {  /glob "*"   /type "allow" }
  }
```

Para obter informações sobre propriedades glob, consulte [Criação de padrões para propriedades glob](#designing-patterns-for-glob-properties).

Se houver seções na sua página que sejam dinâmicas (por exemplo, um aplicativo de notícias) ou em um grupo fechado de usuários, você poderá definir exceções:

>[!NOTE]
>
>Não armazene em cache grupos de usuários fechados, pois os direitos do usuário não são verificados para páginas em cache.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }
  }
```

**Compactação**

Nos servidores da Web Apache, é possível compactar os documentos em cache. A compactação permite que o Apache retorne o documento em formato compactado, se solicitado pelo cliente. A compactação é feita automaticamente ao habilitar o módulo Apache `mod_deflate`, por exemplo:

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

### Invalidação de arquivos por nível de pasta {#invalidating-files-by-folder-level}

Use a propriedade `/statfileslevel` para invalidar arquivos em cache de acordo com o caminho de cada um deles:

* O Dispatcher cria arquivos `.stat` em cada pasta da pasta docroot para o nível especificado. A pasta docroot é de nível 0.
* Os arquivos são invalidados ao tocar no arquivo `.stat`. A data da última modificação do arquivo `.stat` é comparada com a data da última modificação de um documento em cache. O documento será recuperado se o arquivo `.stat` for mais recente.

* Quando um arquivo em um determinado nível é invalidado, **todos** os arquivos `.stat`, desde o docroot **até** o nível do arquivo invalidado ou do `statsfilevel` configurado (o que for menor), serão tocados.

   * Por exemplo: se você definir a propriedade `statfileslevel` como 6 e um arquivo for invalidado no nível 5, cada arquivo `.stat`, desde o docroot até o 5, será tocado. Continuando com este exemplo, se um arquivo for invalidado no nível 7, então cada arquivo `stat`, do docroot até seis, será tocado (desde `/statfileslevel = "6"`).

Somente os recursos **no caminho** para o arquivo invalidado são afetados. Considere o exemplo a seguir: um site usa a estrutura `/content/myWebsite/xx/.` Se você definir `statfileslevel` como 3, um arquivo `.stat` será criado da seguinte maneira:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Quando um arquivo em `/content/myWebsite/xx` for invalidado, cada arquivo `.stat`, do docroot até `/content/myWebsite/xx`, será tocado. Esse seria o caso somente para `/content/myWebsite/xx` e não, por exemplo, para `/content/myWebsite/yy` ou `/content/anotherWebSite`.

>[!NOTE]
>
>A invalidação pode ser evitada enviando um cabeçalho adicional `CQ-Action-Scope:ResourceOnly`. Esse método pode ser usado para liberar recursos específicos sem invalidar outras partes do cache. Consulte [esta página](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) e a página [Invalidação manual do cache do Dispatcher](https://experienceleague.adobe.com/docs/experience-manager-dispatcher/using/configuring/page-invalidate.html?lang=pt-BR#configuring) para obter detalhes adicionais.

>[!NOTE]
>
>Se você especificar um valor para a propriedade `/statfileslevel`, a propriedade `/statfile` será ignorada.

### Invalidação automática de arquivos em cache {#automatically-invalidating-cached-files}

A propriedade `/invalidate` define os documentos que são invalidados automaticamente quando o conteúdo é atualizado.

Com a invalidação automática, o Dispatcher não exclui arquivos em cache após uma atualização de conteúdo, mas verifica a validade deles na próxima vez que forem solicitados. Os documentos no cache que não forem invalidados automaticamente permanecerão no cache até que uma atualização de conteúdo os exclua explicitamente.

A invalidação automática geralmente é usada para páginas HTML. As páginas HTML geralmente contêm links para outras páginas, o que torna difícil determinar se uma atualização de conteúdo afeta uma página. Para garantir que todas as páginas relevantes sejam invalidadas quando o conteúdo for atualizado, invalide automaticamente todas as páginas HTML. A configuração a seguir invalida todas as páginas HTML:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Para obter informações sobre propriedades glob, consulte [Criação de padrões para propriedades glob](#designing-patterns-for-glob-properties).

Essa configuração causa a seguinte atividade quando `/content/wknd/us/en` é ativado:

* Todos os arquivos com padrão en.* são removidos da pasta `/content/wknd/us`.
* A pasta `/content/wknd/us/en./_jcr_content` é removida.
* Todos os outros arquivos que correspondem à configuração `/invalidate` não são excluídos imediatamente. Esses arquivos são excluídos quando ocorre a próxima solicitação. No exemplo, `/content/wknd.html` não é excluído. Ele será excluído quando `/content/wknd.html` for solicitado.

Se você oferecer arquivos PDF e ZIP gerados automaticamente para download, também pode ser necessário invalidar automaticamente esses arquivos. Um exemplo de configuração se parece com o seguinte:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

A integração do AEM com o Adobe Analytics fornece dados de configuração em um arquivo `analytics.sitecatalyst.js` em seu site. O arquivo de exemplo `dispatcher.any` fornecido com o Dispatcher inclui a seguinte regra de invalidação para esse arquivo:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Uso de scripts de invalidação personalizados {#using-custom-invalidation-scripts}

A propriedade `/invalidateHandler` permite definir um script que seja chamado para cada solicitação de invalidação recebida pelo Dispatcher.

Ele é chamado com os seguintes argumentos:

* Handle - O caminho do conteúdo que é invalidado
* Ação - A ação de replicação (por exemplo, ativar, desativar)
* Action Scope - O escopo da ação de replicação (vazio, a menos que um cabeçalho `CQ-Action-Scope: ResourceOnly` seja enviado. Consulte [Invalidação de páginas em cache do AEM](page-invalidate.md) para obter detalhes)

Este método pode ser usado para abranger vários casos de uso diferentes. Por exemplo, invalidar outros caches específicos do aplicativo ou lidar com casos em que o URL externo de uma página e seu lugar no docroot não correspondem ao caminho do conteúdo.

O exemplo de script abaixo registra cada solicitação de invalidação em um arquivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### Exemplo de script de manipulador de invalidação {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitação de clientes que podem liberar o cache {#limiting-the-clients-that-can-flush-the-cache}

A propriedade `/allowedClients` define clientes específicos que têm permissão para liberar o cache. Os padrões do recurso de curinga são comparados com o IP.

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

Para obter informações sobre propriedades glob, consulte [Criação de padrões para propriedades glob](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>É recomendável definir o `/allowedClients`.
>
>Se isso não for feito, qualquer cliente poderá emitir uma chamada para limpar o cache. Caso seja feito repetidamente, isso poderá afetar severamente o desempenho do site.

### Ignorar parâmetros de URL {#ignoring-url-parameters}

A seção `ignoreUrlParams` define quais parâmetros de URL são ignorados ao determinar se uma página é armazenada em cache ou entregue do cache:

* Quando um URL de solicitação contém parâmetros que são ignorados, a página é armazenada em cache.
* Quando um URL de solicitação contém um ou mais parâmetros que não são ignorados, a página não é armazenada em cache.

Quando um parâmetro é ignorado para uma página, ela é armazenada em cache na primeira vez que a página é solicitada. As solicitações subsequentes da página são enviadas para a página em cache, independentemente do valor do parâmetro na solicitação.

>[!NOTE]
>
>É recomendável definir a configuração `ignoreUrlParams` de maneira semelhante a uma lista de permissões. Dessa forma, todos os parâmetros de consulta são ignorados e somente os parâmetros conhecidos ou esperados são isentos (ou “negados”) de serem ignorados. Para obter mais detalhes e exemplos, consulte [esta página](https://github.com/adobe/aem-dispatcher-optimizer-tool/blob/main/docs/Rules.md#dot---the-dispatcher-publish-farm-cache-should-have-its-ignoreurlparams-rules-configured-in-an-allow-list-manner).

Para especificar quais parâmetros são ignorados, adicione regras glob à propriedade `ignoreUrlParams`:

* Para armazenar em cache uma página, apesar da solicitação que contém um parâmetro de URL, crie uma propriedade glob que permita (ignorar) o parâmetro.
* Para evitar que a página seja armazenada em cache, crie uma propriedade glob que negue o parâmetro (a ser ignorado).

>[!NOTE]
>
>Ao configurar a propriedade glob, ela deve corresponder ao nome do parâmetro de consulta. Por exemplo, se desejar ignorar o parâmetro “p1” do URL `http://example.com/path/test.html?p1=test&p2=v2`, então a propriedade global deverá ser:
> `/0002 { /glob "p1" /type "allow" }`

O exemplo a seguir faz com que o Dispatcher ignore todos os parâmetros, exceto o parâmetro `nocache`. Sendo assim, os URLs de solicitação que incluam o parâmetro `nocache` nunca serão armazenadas em cache pelo Dispatcher:

```xml
/ignoreUrlParams
{
    # allow-the-url-parameter-nocache-to-bypass-dispatcher-on-every-request
    /0001 { /glob "nocache" /type "deny" }
    # all-other-url-parameters-are-ignored-by-dispatcher-and-requests-are-cached
    /0002 { /glob "*" /type "allow" }
}
```

No contexto do exemplo da configuração `ignoreUrlParams` acima, a seguinte solicitação HTTP faz com que a página seja armazenada em cache porque o parâmetro `willbecached` é ignorado:

```xml
GET /mypage.html?willbecached=true
```

Usando a configuração `ignoreUrlParams` como exemplo, a seguinte solicitação HTTP faz com que a página **não** seja armazenada em cache porque o parâmetro `nocache` não é ignorado:

```xml
GET /mypage.html?nocache=true
GET /mypage.html?nocache=true&willbecached=true
```

Para obter informações sobre propriedades glob, consulte [Criação de padrões para propriedades glob](#designing-patterns-for-glob-properties).

### Armazenamento em cache de cabeçalhos de resposta HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Esse recurso está disponível na versão **4.1.11** do Dispatcher.

A propriedade `/headers` permite definir os tipos de cabeçalho HTTP que serão armazenados em cache pelo Dispatcher. Na primeira solicitação para um recurso não armazenado em cache, todos os cabeçalhos que correspondem a um dos valores configurados (consulte a amostra de configuração abaixo) são armazenados em um arquivo separado, ao lado do arquivo de cache. Em solicitações subsequentes do recurso em cache, os cabeçalhos armazenados são adicionados à resposta.

Veja abaixo um exemplo da configuração padrão:

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
>Os caracteres de recurso de curinga em arquivos não são permitidos. Para obter mais detalhes, consulte [Criação de padrões para propriedades glob](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Se você precisar que o Dispatcher armazene e entregue cabeçalhos de resposta ETag do AEM, faça o seguinte:
>
>* Adicione o nome do cabeçalho na seção `/cache/headers`.
>* Adicione a seguinte [diretiva do Apache](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) na seção relacionada ao Dispatcher:
>
>```xml
>FileETag none
>```

### Permissões de arquivos de cache do Dispatcher {#dispatcher-cache-file-permissions}

A propriedade `mode` especifica que permissões de arquivos são aplicadas a novos diretórios e arquivos no cache. Essa configuração é restrita pelo `umask` do processo de chamada. É um número octal construído a partir da soma de um ou mais dos seguintes valores:

* `0400` Permitir leitura pelo proprietário.
* `0200` Permitir gravação pelo proprietário.
* `0100` Permitir que o proprietário pesquise em diretórios.
* `0040` Permitir leitura pelos membros do grupo.
* `0020` Permitir gravação pelos membros do grupo.
* `0010` Permitir que membros do grupo pesquisem no diretório.
* `0004` Permitir leitura por outros.
* `0002` Permitir gravação por outros.
* `0001` Permitir que outras pessoas pesquisem no diretório.

O valor padrão é `0755`, que permite que o proprietário leia, grave ou pesquise, e que o grupo e outros leiam ou pesquisem.

### Limitação de toque do arquivo .stat {#throttling-stat-file-touching}

Com a propriedade `/invalidate` padrão, cada ativação invalida efetivamente todos os arquivos `.html` (quando o caminho de cada um deles corresponde à seção `/invalidate`). Em um site com tráfego considerável, várias ativações subsequentes aumentarão a carga da CPU no back-end. Nesse cenário, seria desejável “limitar” o toque ao arquivo `.stat` para manter o site responsivo. Você pode fazer isso usando a propriedade `/gracePeriod`.

A propriedade `/gracePeriod` define o número de segundos em que um recurso obsoleto e invalidado automaticamente ainda pode ser enviado do cache após a última ativação que ocorreu. A propriedade pode ser usada em uma configuração em que um lote de ativações invalidaria repetidamente todo o cache. O valor recomendado é de 2 segundos.

Para obter detalhes adicionais, leia também as seções `/invalidate` e `/statfileslevel` acima.

### Configuração da invalidação de cache baseada em tempo - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

A invalidação de cache com base no tempo depende da propriedade `/enableTTL` e da presença de cabeçalhos de expiração regulares do padrão HTTP. Caso defina a propriedade para 1 (`/enableTTL "1"`), ela avaliará os cabeçalhos de resposta desde o back-end. Se os cabeçalhos contiverem uma data de `Cache-Control`, `max-age` ou `Expires`, um arquivo auxiliar e vazio ao lado do arquivo de cache será criado, com o tempo de modificação igual à data de expiração. Quando o arquivo em cache for solicitado depois do tempo de modificação, ele será automaticamente solicitado outra vez no back-end.

Antes da versão 4.3.5 do Dispatcher, a lógica de invalidação de TTL era baseada somente no valor TTL configurado. Com a versão 4.3.5 do Dispatcher, tanto o TTL definido **quanto** as regras de invalidação do cache do Dispatcher são considerados. Dessa forma, para um arquivo em cache:

1. Se `/enableTTL` for definido como 1, a expiração do arquivo será verificada. Se o arquivo tiver expirado de acordo com o TTL definido, nenhuma outra verificação será executada e o arquivo em cache será solicitado novamente no back-end.
2. Se o arquivo não tiver expirado ou o `/enableTTL` não estiver configurado, as regras padrão de invalidação de cache serão aplicadas, como aquelas definidas pelo [/statfileslevel](#invalidating-files-by-folder-level) e [/invalidate](#automatically-invalidating-cached-files). Esse fluxo significa que o Dispatcher pode invalidar arquivos para os quais o TTL não expirou.

Essa nova implementação aceita casos de uso em que os arquivos têm um TTL mais longo (por exemplo, no CDN), mas ainda podem ser invalidados mesmo que o TTL não tenha expirado. Isso favorece a atualização do conteúdo em relação à taxa de ocorrências do cache no Dispatcher.

Por outro lado, caso você precise **somente** da lógica de expiração aplicada a um arquivo, defina `/enableTTL` para 1 e exclua aquele arquivo do mecanismo de invalidação de cache padrão. Por exemplo, você pode:

* Para ignorar o arquivo, configure as [regras de invalidação](#automatically-invalidating-cached-files) na seção do cache. No trecho abaixo, todos os arquivos terminando em `.example.html` são ignorados e expirarão somente quando o TTL definido tiver passado.

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
   /0002  { /glob "*.example.html" /type "deny" }
  }
```

* Projetar a estrutura de conteúdo de forma que você possa definir uma alta [/statfilelevel](#invalidating-files-by-folder-level) para que o arquivo não seja invalidado automaticamente.

Isso garante que a invalidação do arquivo `.stat` não seja usada e que somente a expiração de TTL esteja ativa para os arquivos especificados.

>[!NOTE]
>
>Lembre-se que configurar `/enableTTL` para 1 habilita o armazenamento em cache TTL somente no lado do dispatcher. Dessa forma, as informações TTL contidas no arquivo adicional (veja acima) não são fornecidas a nenhum outro agente do usuário que solicite esse tipo de arquivo do dispatcher. Se desejar fornecer cabeçalhos de armazenamento em cache a sistemas downstream como um CDN ou um navegador, você precisa configurar a seção `/cache/headers` conforme necessário.

>[!NOTE]
>
>Esse recurso está disponível na versão **4.1.11** ou posterior do Dispatcher.

## Configuração do balanceamento de carga - /statistics {#configuring-load-balancing-statistics}

A seção `/statistics` define categorias de arquivos para as quais o Dispatcher classifica a capacidade de resposta de cada renderização. O Dispatcher usa as pontuações para determinar qual renderizador enviará uma solicitação.

Cada categoria criada define um padrão glob. O Dispatcher compara o URI do conteúdo solicitado a esses padrões para determinar a categoria do conteúdo solicitado:

* A ordem das categorias determina a ordem em que são comparadas ao URI.
* O primeiro padrão de categoria que corresponde ao URI é a categoria do arquivo. Não são avaliados mais padrões de categoria.

O Dispatcher é compatível com no máximo oito categorias de estatísticas. Se você definir mais de oito categorias, somente as oito primeiras serão usadas.

**Seleção de renderizador**

Sempre que o Dispatcher exigir uma página renderizada, ele usa o seguinte algoritmo para selecionar o renderizador:

1. Se a solicitação tiver o nome do renderizador em um cookie `renderid`, o Dispatcher usará esse renderizador.
1. Se a solicitação não incluir o cookie `renderid`, o Dispatcher compara as estatísticas de renderização:

   1. O Dispatcher determina a categoria do URI da solicitação.
   1. O Dispatcher determina qual renderizador tem a pontuação de resposta mais baixa para essa categoria e seleciona esse renderizador.

1. Se nenhum renderizador ainda não estiver selecionado, use o primeiro renderizador da lista.

A pontuação para uma categoria de renderizador é baseada em tempos de resposta anteriores e em conexões com falha e sucesso anteriores de tentativas do Dispatcher. Para cada tentativa, a pontuação para a categoria do URI solicitado é atualizada.

>[!NOTE]
>
>Se você não usar balanceamento de carga, poderá omitir esta seção.

### Definição de categorias de estatísticas {#defining-statistics-categories}

Defina uma categoria para cada tipo de documento para o qual deseja manter estatísticas para a seleção de renderizador. A seção `/statistics` contém uma seção `/categories`. Para definir uma categoria, adicione uma linha abaixo da seção `/categories` que tenha o seguinte formato:

`/name { /glob "pattern"}`

A categoria `name` deve ser exclusiva do farm. O `pattern` é descrito na seção [Criação de padrões para propriedades glob](#designing-patterns-for-glob-properties).

Para determinar a categoria de um URI, o Dispatcher compara o URI com cada padrão de categoria até que uma correspondência seja encontrada. O Dispatcher começa com a primeira categoria na lista e continua na ordem. Portanto, coloque primeiro as categorias com padrões mais específicos.

Por exemplo, no arquivo padrão `dispatcher.any`, o Dispatcher define uma categoria HTML, e depois outras categorias. A categoria HTML é mais específica, portanto, aparece primeiro:

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

### Indisponibilidade do servidor refletida nas estatísticas do Dispatcher {#reflecting-server-unavailability-in-dispatcher-statistics}

A propriedade `/unavailablePenalty` define o tempo (em décimos de segundos) que é aplicado às estatísticas de renderização quando uma conexão com a renderização falha. O Dispatcher adiciona o tempo à categoria de estatísticas que corresponde ao URI solicitado.

Por exemplo, a penalidade é aplicada quando a conexão TCP/IP com o nome de host/porta designado não pode ser estabelecida, seja porque o AEM não está sendo executado (nem acompanhando) ou devido a um problema relacionado à rede.

A propriedade `/unavailablePenalty` é um filho direto da seção `/farm` (um irmão da seção `/statistics`).

Se nenhuma propriedade `/unavailablePenalty` existir, um valor `"1"` será usado.

```xml
/unavailablePenalty "1"
```

## Identificação de uma pasta de conexão adesiva - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

A propriedade `/stickyConnectionsFor` define uma pasta que contém documentos adesivos. Essa propriedade é acessada usando o URL. O Dispatcher envia todas as solicitações de um único usuário que estão nessa pasta para a mesma instância de renderização. As conexões adesivas garantem que os dados da sessão estejam presentes e sejam consistentes para todos os documentos. Esse mecanismo usa o cookie `renderid`.

O exemplo a seguir define uma conexão adesiva à pasta /products:

```xml
/stickyConnectionsFor "/products"
```

Quando uma página é composta de conteúdo de vários nós de conteúdo, inclua a propriedade `/paths`, que lista os caminhos para o conteúdo. Por exemplo, uma página trm conteúdo de `/content/image`, `/content/video` e `/var/files/pdfs`. A seguinte configuração ativa conexões adesivas para todo o conteúdo da página:

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

Quando as conexões adesivas são ativadas, o módulo Dispatcher define o cookie `renderid`. Este cookie não tem o sinalizador `httponly`, que deve ser adicionado para melhorar a segurança. Você pode adicionar o sinalizador `httponly` definindo a propriedade `httpOnly` no nó `/stickyConnections` de um arquivo de configuração `dispatcher.any`. O valor da propriedade (`0` ou `1`) define se o cookie `renderid` tem o atributo `HttpOnly` anexado. O valor padrão é `0`, o que significa que o atributo não será adicionado.

Para obter informações adicionais sobre o sinalizador `httponly`, leia [esta página](https://www.owasp.org/index.php/HttpOnly).

### segurança {#secure}

Quando as conexões adesivas são ativadas, o módulo Dispatcher define o cookie `renderid`. Este cookie não tem o sinalizador `secure`, que deve ser adicionado para melhorar a segurança. Você adiciona o sinalizador `secure` definindo a propriedade `secure` no nó `/stickyConnections` de um arquivo de configuração `dispatcher.any`. O valor da propriedade (`0` ou `1`) define se o cookie `renderid` tem o atributo `secure` anexado. O valor padrão é `0`, o que significa que o atributo será adicionado **se** a solicitação recebida estiver segura. Se o valor for definido como `1`, o sinalizador de segurança será adicionado independentemente da solicitação de entrada ser segura ou não.

## Lidar com erros de conexão de renderização {#handling-render-connection-errors}

Configure o comportamento do Dispatcher quando o servidor de renderização retornar um erro 500 ou estiver indisponível.

### Especificação de uma página de verificação de integridade {#specifying-a-health-check-page}

Use a propriedade `/health_check` para especificar um URL que é verificado quando ocorre um código de status 500. Se essa página também retornar um código de status 500, a instância será considerada indisponível e uma sanção de tempo configurável (`/unavailablePenalty`) será aplicada à renderização antes de uma nova tentativa.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Especificação do atraso de repetição de página {#specifying-the-page-retry-delay}

A propriedade `/retryDelay` define o tempo (em segundos) que o Dispatcher aguarda entre rodadas de tentativas de conexão com os renderizadores do farm. Para cada rodada, o número máximo de vezes que o Dispatcher tenta uma conexão com um renderizador é o número de renderizações no farm.

O Dispatcher usa um valor `"1"` se `/retryDelay` não estiver explicitamente definido. O valor padrão geralmente é apropriado.

```xml
/retryDelay "1"
```

### Configuração do número de tentativas {#configuring-the-number-of-retries}

A propriedade `/numberOfRetries` define o número máximo de rodadas de tentativas de conexão executadas pelo Dispatcher com os renderizadores. Se o Dispatcher não conseguir se conectar com êxito a um renderizador após esse número de tentativas, ele retornará uma resposta com falha.

Para cada rodada, o número máximo de vezes que o Dispatcher tenta uma conexão com um renderizador é o número de renderizações no farm. Portanto, o número máximo de vezes que o Dispatcher tenta uma conexão é ( `/numberOfRetries`) x (o número de renderizadores).

Se o valor não estiver definido explicitamente, o valor padrão será `5`.

```xml
/numberOfRetries "5"
```

### Utilização do mecanismo de failover {#using-the-failover-mechanism}

Para reenviar solicitações para renderizações diferentes quando a solicitação original falhar, ative o mecanismo de failover no farm do Dispatcher. Quando o failover é ativado, o Dispatcher apresenta o seguinte comportamento:

* Quando uma solicitação para um renderizador retorna o status HTTP 503 (INDISPONÍVEL), o Dispatcher envia a solicitação para um renderizador diferente.
* Quando uma solicitação para um renderizador retorna o status HTTP 50x (diferente de 503), o Dispatcher envia uma solicitação para a página configurada para a propriedade `health_check`.
   * Se a verificação de integridade retornar 500 (INTERNAL_SERVER_ERROR), o Dispatcher enviará a solicitação original para um renderizador diferente.
   * Se a verificação de integridade retornar o status HTTP 200, o Dispatcher retornará o erro inicial HTTP 500 ao cliente.

Para habilitar o failover, adicione a seguinte linha no farm (ou site):

```xml
/failover "1"
```

>[!NOTE]
>
>Para repetir solicitações HTTP que contêm um corpo, o Dispatcher envia um cabeçalho de solicitação `Expect: 100-continue` para o renderizador antes de fazer spool do conteúdo real. O CQ 5.5 com CQSE responde imediatamente com 100 (CONTINUE) ou com um código de erro. Outros contêineres de servlet também são compatíveis.

## Erros de Interrupção ignorados - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Essa opção não é necessária. Use-a somente quando vir as seguintes mensagens de log:
>
>`Error while reading response: Interrupted system call`

Qualquer chamada de sistema orientada pelo sistema de arquivos pode ser interrompida `EINTR` se o objeto da chamada do sistema estiver localizado em um sistema remoto acessado via NFS. O fato de essas chamadas do sistema expirarem ou serem interrompidas se baseia em como o sistema de arquivos subjacente foi montado no computador local.

Use o parâmetro `/ignoreEINTR` se a instância tiver essa configuração e o log tiver a seguinte mensagem:

`Error while reading response: Interrupted system call`

Internamente, o Dispatcher lê a resposta do servidor remoto (ou seja, do AEM) usando um loop que pode ser representado como:

```text
while (response not finished) {  
read more data  
}
```

Essas mensagens podem ser geradas quando o `EINTR` ocorrer na seção “`read more data`” e são causadas pela recepção de um sinal antes de quaisquer dados serem recebidos.

Para ignorar essas interrupções, você pode adicionar o seguinte parâmetro a `dispatcher.any` (antes de `/farms`):

`/ignoreEINTR "1"`

Configurar `/ignoreEINTR` como `"1"` faz com que o Dispatcher continue tentando ler os dados até que a resposta completa seja lida. O valor padrão é `0` e desativa a opção.

## Criação de padrões para propriedades glob {#designing-patterns-for-glob-properties}

Várias seções no arquivo de configuração do Dispatcher usam propriedades `glob` como critérios de seleção para solicitações de clientes. Os valores das propriedades `glob` são padrões que o Dispatcher compara a um aspecto da solicitação, como o caminho do recurso solicitado ou o endereço IP do cliente. Por exemplo, os itens na seção `/filter` usam os padrões `glob` para identificar os caminhos das páginas em que o Dispatcher atua ou rejeita.

Os valores `glob` podem incluir caracteres curingas e caracteres alfanuméricos para definir o padrão.

| Caracteres curingas | Descrição | Exemplos |
|--- |--- |--- |
| `*` | Corresponde a zero ou mais instâncias contíguas de qualquer caractere na cadeia de caracteres. O caractere final da correspondência é determinado por uma das seguintes situações: <br/>um caractere na cadeia de caracteres corresponde ao próximo caractere no padrão, e o caractere padrão tem as seguintes características:<br/><ul><li>Não é um *</li><li>Não é um ?</li><li>Um caractere literal (incluindo um espaço) ou uma classe de caractere.</li><li>O final do padrão é alcançado.</li></ul>Dentro de uma classe de caracteres, o caractere é interpretado literalmente. | `*/geo*` Corresponde a qualquer página abaixo do nó `/content/geometrixx` e do nó `/content/geometrixx-outdoors`. As seguintes solicitações HTTP correspondem ao padrão glob: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` <br/>Corresponde a qualquer página abaixo do nó `/content/geometrixx-outdoors`. Por exemplo, a seguinte solicitação HTTP corresponde ao padrão glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Corresponde a qualquer caractere único. Use classes de caracteres externos. Dentro de uma classe de caracteres, esse caractere é interpretado literalmente. | `*outdoors/??/*`<br/> Corresponde às páginas de qualquer idioma no site geometrixx-outdoors. Por exemplo, a seguinte solicitação HTTP corresponde ao padrão glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>A seguinte solicitação não corresponde ao padrão glob: <br/><ul><li>&quot;GET /content/geometrixx-outdoors/en.html&quot;</li></ul> |
| `[ and ]` | Demarca o início e o fim de uma classe de caracteres. As classes de caracteres podem incluir um ou mais intervalos de caracteres e caracteres únicos.<br/>Uma correspondência ocorre se o caractere de destino corresponde a qualquer um dos caracteres na classe de caracteres ou dentro de um intervalo definido.<br/>Se o colchete não estiver incluído, o padrão não produzirá correspondências. | `*[o]men.html*`<br/> Corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` <br/>Corresponde às seguintes solicitações HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Indica um intervalo de caracteres. Para uso em classes de caracteres. Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | `*[m-p]men.html*` Corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Nega o caractere ou a classe de caracteres que se segue. Use apenas para negar caracteres e intervalos de caracteres dentro de classes de caracteres. Equivalente ao `^ wildcard`. <br/>Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | `*[!o]men.html*`<br/> Corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` ou `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Nega o caractere ou o intervalo de caracteres a seguir. Use para negar somente caracteres e intervalos de caracteres dentro de classes de caracteres. Equivalente ao caractere curinga `!`. <br/>Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | Os exemplos para o caractere curinga `!` se aplicam substituindo os caracteres `!` nos padrões de exemplo por caracteres `^`. |


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

## Logs {#logging}

Na configuração do servidor Web, é possível definir:

* O local do arquivo de log do Dispatcher.
* O nível de log.

Consulte a documentação do servidor Web e o arquivo readme da instância do Dispatcher para obter mais informações.

**Logs rotacionados/canalizados do Apache**

Se estiver usando um servidor web do **Apache**, você poderá usar a funcionalidade padrão para logs rotacionados e/ou canalizados. Por exemplo, usando logs canalizados:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Essa funcionalidade rotaciona automaticamente:

* o arquivo de log do Dispatcher; com um carimbo de data e hora na extensão (`logs/dispatcher.log%Y%m%d`).
* semanalmente (60 x 60 x 24 x 7 = 604.800 segundos).

Consulte a documentação do servidor web do Apache sobre rotação de logs e logs canalizados. Por exemplo, [Apache 2.4](https://httpd.apache.org/docs/2.4/logs.html).

>[!NOTE]
>
>Após a instalação, o nível de log padrão é alto (ou seja, nível 3 = depuração), para que o Dispatcher registre todos os erros e avisos. Esse nível é útil nas etapas iniciais.
>
>No entanto, ele requer recursos adicionais. Quando o Dispatcher estiver funcionando sem problemas *de acordo com seus requisitos*, será possível diminuir o nível de log.

### Registro de rastreamento {#trace-logging}

Além de outros aprimoramentos para o Dispatcher, a versão 4.2.0 também introduz a opção Registro de rastreamento.

Esse é um nível superior ao Registro de depuração que mostra informações adicionais nos logs. Ele adiciona registro para:

* Os valores dos cabeçalhos encaminhados;
* A regra que está sendo aplicada para uma determinada ação.

Você pode ativar o Registro de rastreamento definindo o nível de log como `4` em seu servidor Web.

Veja abaixo um exemplo de logs com rastreamento ativado:

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

Para confirmar a operação básica e a interação do servidor Web, do Dispatcher e da instância do AEM, você pode usar as seguintes etapas:

1. Defina o `loglevel` como `3`.

1. Inicie o servidor web. Isso também inicia o Dispatcher.
1. Inicie a instância do AEM.
1. Verifique os arquivos de log e de erro do servidor Web e do Dispatcher.
   * Dependendo do servidor web, você deverá ver mensagens como:
      * `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured` e
      * `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Navegue pelo site por meio do servidor Web. Confirme se o conteúdo está sendo exibido conforme exigido.\
   Por exemplo, em uma instalação local em que o AEM é executado na porta `4502` e o servidor Web em `80` acesse o console Webites usando:
   * `https://localhost:4502/libs/wcm/core/content/siteadmin.html`
   * `https://localhost:80/libs/wcm/core/content/siteadmin.html`
   * Os resultados devem ser idênticos. Confirme o acesso a outras páginas com o mesmo mecanismo.

1. Verifique se o diretório de cache está sendo preenchido.
1. Para verificar se o cache está sendo liberado corretamente, ative uma página.
1. Se tudo estiver funcionando corretamente, você poderá reduzir o `loglevel` para `0`.

## Uso de vários Dispatchers {#using-multiple-dispatchers}

Em configurações complexas, você pode usar vários Dispatchers. Por exemplo, você pode usar:

* um Dispatcher para publicar um site na Intranet
* um segundo Dispatcher, em um endereço diferente e com configurações de segurança diferentes, para publicar o mesmo conteúdo na Internet.

Nesse caso, verifique se cada solicitação passa por apenas um Dispatcher. Um Dispatcher não lida com solicitações provenientes de outro Dispatcher. Portanto, verifique se ambos os Dispatchers acessam o site do AEM diretamente.

## Depuração {#debugging}

Ao adicionar o cabeçalho `X-Dispatcher-Info` a uma solicitação, o Dispatcher responde se o destino foi armazenado em cache, recuperado do armazenamento em cache ou se não é armazenável em cache. O cabeçalho de resposta `X-Cache-Info` contém essas informações em um formulário legível. Você pode usar esses cabeçalhos de resposta para depurar problemas envolvendo respostas armazenadas em cache pelo Dispatcher.

Essa funcionalidade não está habilitada por padrão, portanto, para que o cabeçalho de resposta `X-Cache-Info` seja incluído, o farm deve conter a seguinte entrada:

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

Além disso, o cabeçalho `X-Dispatcher-Info` não precisa de um valor, mas, se você usar `curl` para testes, deverá fornecer um valor para enviar ao cabeçalho, como:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/wknd/us/en.html
```

Veja abaixo uma lista contendo os cabeçalhos de resposta que o `X-Dispatcher-Info` retorna:

* **em cache**\
   O arquivo de destino está contido no cache e o Dispatcher determinou que é válido entregá-lo.
* **armazenamento em cache**\
   O arquivo de destino não está contido no cache e o Dispatcher determinou que é válido armazenar a saída em cache e entregá-la.
* **armazenamento em cache: o arquivo de status é mais recente**
O arquivo de destino está contido no cache, no entanto, ele é invalidado por um arquivo de status mais recente. O Dispatcher excluirá o arquivo de destino, recriará o arquivo a partir da saída e o entregará.
* **não armazenável em cache: nenhuma raiz do documento**
A configuração do farm não contém uma raiz do documento (elemento de configuração 
`cache.docroot`).
* **não armazenável em cache: caminho do arquivo de cache muito longo**\
   O arquivo de destino - a concatenação da raiz do documento e do arquivo de URL - excede o maior nome de arquivo possível no sistema.
* **não armazenável em cache: caminho de arquivo temporário muito longo**\
   O modelo de nome de arquivo temporário excede o nome de arquivo mais longo possível no sistema. O Dispatcher cria um arquivo temporário primeiro, antes de realmente criar ou substituir o arquivo em cache. O nome de arquivo temporário é o nome de arquivo de destino com os caracteres `_YYYYXXXXXX` anexados a ele, onde `Y` e `X` serão substituídos para criar um nome exclusivo.
* **não armazenável em cache: o URL da solicitação não tem extensão**\
   O URL da solicitação não tem extensão ou há um caminho após a extensão de arquivo, por exemplo: `/test.html/a/path`.
* **não armazenável em cache: a solicitação não era GET ou HEAD**
O método HTTP não é GET ou HEAD. O Dispatcher presume que a saída contenha dados dinâmicos que não devem ser armazenados em cache.
* **não armazenável em cache: a solicitação continha uma cadeia de caracteres de consulta**\
   A solicitação continha uma cadeia de caracteres de consulta. O Dispatcher presume que a saída depende da sequência de consulta fornecida e, portanto, não a armazena em cache.
* **não armazenável em cache: o gerenciador de sessão não autenticou**\
   O cache do farm é regido por um gerenciador de sessão (a configuração contém um nó `sessionmanagement`) e a solicitação não continha as informações de autenticação apropriadas.
* **não armazenável em cache: a solicitação contém autorização**\
   O farm não tem permissão para armazenar em cache a saída ( `allowAuthorized 0`) e a solicitação contém informações de autenticação.
* **não armazenável em cache: o destino é um diretório**\
   O arquivo de destino é um diretório. Esse local pode apontar para algum erro conceitual, onde um URL e alguns URLs secundários contêm saídas armazenáveis em cache. Por exemplo, se uma solicitação para `/test.html/a/file.ext` vier primeiro e contiver uma saída armazenável em cache, o Dispatcher não será capaz de armazenar a saída de uma solicitação subsequente em cache para `/test.html`.
* **não armazenável em cache: o URL de solicitação tem uma barra à direita**\
   O URL da solicitação tem uma barra à direita.
* **não armazenável em cache: o URL de solicitação não está nas regras de cache**\
   As regras de cache do farm negam explicitamente o armazenamento em cache da saída de algum URL de solicitação.
* **não armazenável em cache: acesso negado pelo verificador de autorização**\
   O verificador de autorização do farm negou acesso ao arquivo armazenado em cache.
* **não armazenável em cache: sessão não válida**
O cache do farm é controlado por um gerenciador de sessão (a configuração contém um nó `sessionmanagement`) e a sessão do usuário não é válida ou não é mais válida.
* **não armazenável em cache: a resposta contém`no_cache`**
O servidor remoto retornou um cabeçalho 
Cabeçalho `Dispatcher: no_cache` que proíbe o Dispatcher de armazenar a saída em cache.
* **não armazenável em cache: o comprimento do conteúdo da resposta é zero**
O comprimento do conteúdo da resposta é zero; o Dispatcher não pode criar um arquivo de comprimento zero.
