---
title: Configuração do Dispatcher
seo-title: Configuração do Dispatcher
description: Saiba como configurar o Dispatcher.
seo-description: Saiba como configurar o Dispatcher.
uuid: 253 ef 0 f 7-2491-4 cec-ab 22-97439 df 29 fd 6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: '1193211344162'
topic-tags: dispatcher
content-type: referência
discoiquuid: aeffee 8 e-bb 34-42 a 7-9 a 5 e-b 7 d 0 e 848391 a
translation-type: tm+mt
source-git-commit: 2f0ca874c23cb7aecbcedc22802c46a295bb4d75

---


# Configuração do Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

As seções a seguir descrevem como configurar vários aspectos do Dispatcher.

## Suporte para ipv 4 e ipv 6 {#support-for-ipv-and-ipv}

Todos os elementos do AEM e do Dispatcher podem ser instalados em redes ipv 4 e ipv 6. Consulte [IPV 4 e IPV 6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Arquivos de configuração do Dispatcher {#dispatcher-configuration-files}

Por padrão, a configuração do Dispatcher é armazenada no arquivo `dispatcher.any` de texto, embora você possa alterar o nome e o local desse arquivo durante a instalação.

O arquivo de configuração contém uma série de propriedades de valor único ou de vários valores que controlam o comportamento do Dispatcher:

* Os nomes das propriedades recebem um prefixo em diante `/`.
* Propriedades de diversos valores incluem itens filhos usando chaves `{ }`.

Uma configuração de exemplo está estruturada da seguinte maneira:

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

É possível incluir outros arquivos que contribuem para a configuração:

* Se o seu arquivo de configuração for grande, você poderá dividi-lo em vários arquivos menores (mais fáceis de gerenciar) e, em seguida, incluí-los.
* Para incluir arquivos gerados automaticamente.

Por exemplo, para incluir o arquivo myfarm. qualquer um na /farms ção usa o seguinte código:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Use o asterisco (&quot; *&quot;) como um caractere curinga para especificar um intervalo de arquivos a ser incluído.

Por exemplo, se os arquivos `farm_1.any` através de uma `farm_5.any` configuração contiverem a configuração de farms a cinco, você pode incluí-los como segue:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Uso de variáveis de ambiente {#using-environment-variables}

É possível usar variáveis de ambiente em propriedades de valores sequenciais no dispatcher. qualquer arquivo em vez de codificar os valores permanentemente. Para incluir o valor de uma variável de ambiente, use o formato `${variable_name}`.

Por exemplo, se o expedidor. qualquer arquivo estiver localizado no mesmo diretório que o diretório do cache, pode ser usado o seguinte valor para a propriedade [docroot](dispatcher-configuration.md#main-pars-title-30) :

```xml
/docroot "${PWD}/cache"
```

Como outro exemplo, se você criar uma variável de ambiente nomeada `PUBLISH_IP` que armazena o nome do host da instância de publicação de AEM, a seguinte configuração da [/renders](dispatcher-configuration.md#main-pars-127-25-0008) é possível usar:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Nomeação da instância do Dispatcher {#naming-the-dispatcher-instance-name}

Use `/name` a propriedade para especificar um nome exclusivo para identificar sua instância do Dispatcher. `/name` A propriedade é uma propriedade de nível superior na estrutura de configuração.

## Definição de explorações {#defining-farms-farms}

`/farms` A propriedade define um ou mais conjuntos de comportamentos do Dispatcher, onde cada conjunto está associado a sites diferentes ou urls. `/farms` A propriedade pode incluir uma única empresa ou várias explorações:

* Use uma única empresa quando quiser que o Dispatcher manipule todas as páginas da Web ou sites da mesma maneira.
* Crie várias explorações quando diferentes áreas de site ou sites diferentes exigem comportamento diferente do Dispatcher.

`/farms` A propriedade é uma propriedade de nível superior na estrutura de configuração. Para definir uma fazenda, adicione uma propriedade filho à `/farms` propriedade. Use um nome de propriedade que identifique exclusivamente o farm na instância do Dispatcher.

A `/*farmname*` propriedade tem vários valores e contém outras propriedades que definem o comportamento do Dispatcher:

* Os urls das páginas às quais o conjunto se aplica.
* Um ou mais urls de serviço (normalmente de instâncias de publicação de AEM) a serem usados para renderizar documentos.
* As estatísticas a serem usadas para balancear a carga de vários renderizadores de documentos.
* Vários outros comportamentos, como quais arquivos o cache e onde.

O valor pode ter incluído qualquer caractere alfanumérico (a-z, 0-9). O exemplo a seguir mostra a definição de esqueleto para duas explorações com nome `/daycom` e `/docsdaycom`:

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
>Se você usar mais de um farm de renderização, a lista será avaliada como parte inferior. Isso é particularmente relevante ao definir [hosts virtuais](dispatcher-configuration.md#main-pars-117-15-0006) para seus sites.

Cada propriedade da empresa pode conter as seguintes propriedades secundárias:

| Nome da propriedade | Descrição |
|--- |--- |
| [/homepage](#specify-a-default-page-iis-only-homepage) | Página inicial padrão (opcional) (somente IIS) |
| [/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders) | Os cabeçalhos da solicitação HTTP do cliente para passar. |
| [/virtualhosts](#identifying-virtual-hosts-virtual-hosts) | Os hosts virtuais desta fazenda. |
| [/sessionmanagement](#enabling-secure-sessions-session-management) | Suporte para gerenciamento e autenticação de sessão. |
| [/renders](#defining-page-renderers-renders) | Os servidores que fornecem páginas renderizadas (normalmente as instâncias de publicação de AEM). |
| [/filter](#configuring-access-to-content-filter) | Define os urls aos quais o Dispatcher permite acesso. |
| [/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls) | Configura o acesso a urls vanity. |
| [/propagateSyndPost](#forwarding-syndication-requests-propagate-syndpost) | Suporte para o encaminhamento de solicitações de sindicalização. |
| [/cache](#configuring-the-dispatcher-cache-cache) | Configura o comportamento de armazenamento em cache. |
| [/statistics](#configuring-load-balancing-statistics) | Definição de categorias estatísticas para cálculos de balanceamento de carga. |
| [/stickyConnectionsFor](#identifying-a-sticky-connection-folder-sticky-connections-for) | A pasta que contém documentos fixos. |
| [/health_check](#specifying-a-health-check-page) | O URL a ser usado para determinar a disponibilidade do servidor. |
| [/retryDelay](#specifying-the-page-retry-delay) | O atraso antes de tentar novamente uma conexão com falha. |
| [/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics) | Sanções que afetam as estatísticas de cálculos de balanceamento de carga. |
| [/failover](#using-the-fail-over-mechanism) | Reenvie solicitações para renderizações diferentes quando a solicitação original falhar. |

## Especificar uma página padrão (somente IIS) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>O `/homepage`parâmetro (somente IIS) não funciona mais. Em vez disso, você deve usar [o IIS URL Rewrite Module](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>Se estiver usando o Apache, você deve usar `mod_rewrite` o módulo. Consulte a documentação do site do Apache para obter informações sobre `mod_rewrite` (por exemplo [, Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). Ao usar `mod_rewrite`, é aconselhável usar o sinalizador** [passthrough| PT&#39; (passe para o próximo manipulador)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)** para forçar o mecanismo de reescrever a definir o `uri` campo da `request_rec` estrutura interna para o valor do `filename` campo.

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

## Especificação dos cabeçalhos HTTP para aprovação {#specifying-the-http-headers-to-pass-through-clientheaders}

`/clientheaders` A propriedade define uma lista de cabeçalhos HTTP que o Dispatcher transmite da solicitação HTTP do cliente para o renderizador (instância do AEM).

Por padrão, o Dispatcher encaminha os cabeçalhos HTTP padrão para a instância do AEM. Em alguns casos, você pode desejar encaminhar cabeçalhos adicionais ou remover cabeçalhos específicos:

* Adicione cabeçalhos, como cabeçalhos personalizados, que sua instância do AEM espera na solicitação HTTP.
* Remova cabeçalhos, como cabeçalhos de autenticação, que são relevantes somente para o servidor Web.

Se você personalizar o conjunto de cabeçalhos para passar, deverá especificar uma lista exaustiva de cabeçalhos, incluindo aqueles que normalmente são incluídos por padrão.

Por exemplo, uma instância do Dispatcher que manipula solicitações de ativação de página para instâncias de publicação exige o `PATH` cabeçalho na `/clientheaders` seção. O `PATH` cabeçalho habilita a comunicação entre o agente de replicação e o expedidor.

O código a seguir é uma configuração de exemplo para `/clientheaders`:

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

## Identificação de hosts virtuais {#identifying-virtual-hosts-virtualhosts}

`/virtualhosts` A propriedade define uma lista de todas as combinações de nome de host/URI que o Dispatcher aceita para esta produção. Você pode usar o caractere asterisco (&quot; *&quot;) como um caractere curinga. Os valores para a `virtualhosts` propriedade/use o seguinte formato:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Opcional) `https://` Ou `https://.`
* `host`: O nome ou endereço IP do computador host e o número da porta, se necessário. (Consulte [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23)
* `uri`: (Opcional) O caminho para os recursos.

A configuração de exemplo a seguir trata solicitações para os domínios.com e. ch de mycompany, e todos os domínios de mysubdivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

A configuração a seguir trata *todas* as solicitações:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolver o host virtual {#resolving-the-virtual-host}

Quando o Dispatcher recebe uma solicitação HTTP ou HTTPS, encontra o valor do host virtual que corresponde melhor ao `host,``uri`e `scheme` aos cabeçalhos da solicitação. O Dispatcher avalia os valores nas `virtualhosts` propriedades na seguinte ordem:

* O Dispatcher começa no menor conjunto e avança para cima no dispatcher. qualquer arquivo.
* Para cada conjunto, o Dispatcher começa com o valor mais alto na `virtualhosts` propriedade e avança para baixo na lista de valores.

O Dispatcher encontra o valor de host virtual melhor correspondente da seguinte maneira:

* O host virtual primeiro encontrado que corresponde a todos `host`os três, o `scheme`e `uri` o da solicitação é usado.
* Se nenhum `virtualhosts` valor tiver `scheme` e `uri` partes que correspondam ao `scheme` e `uri` da solicitação, o host virtual primeiro encontrado que corresponder à `host` solicitação será usado.
* Se nenhum `virtualhosts` valor tiver uma parte de host que corresponda ao host da solicitação, o host virtual na extremidade superior da extremidade superior será usado.

Portanto, você deve colocar o host virtual padrão na parte superior da `virtualhosts` propriedade na extremidade superior do seu dispatcher. qualquer arquivo.

### Exemplo de resolução de host virtual {#example-virtual-host-resolution}

O exemplo a seguir representa um trecho de um dispatcher. Qualquer arquivo que define duas explorações do Dispatcher e cada uma define uma `virtualhosts` propriedade.

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

Usando este exemplo, a tabela a seguir mostra os hosts virtuais que são resolvidos para as solicitações HTTP em questão:

| URL de solicitação | Host virtual resolvido |
|---|---|
| `https://www.mycompany.com/products/gloves.html` | `www.mycompany.com/products/*;` |
| `https://www.mycompany.com/about.html` | `www.mycompany.com` |

## Ativar sessões seguras - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized`**deve** ser definido `"0"` na `/cache` seção para ativar esse recurso.

Crie uma sessão segura para acesso à publicação de renderização para que os usuários precisem fazer logon para acessar qualquer página no farm. Depois de fazer logon, os usuários podem acessar todas as páginas na agricultura. Consulte [Criar um grupo de usuários fechado](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) para obter informações sobre como usar esse recurso com cugs.

`/sessionmanagement` A propriedade é uma subpropriedade de `/farms`.

>[!CAUTION]
>
>Se as seções do seu site usam requisitos de acesso diferentes, você precisa definir várias explorações.

**/sessionmanagement** tem vários subparâmetros:

**/directory** (obrigatório)

O diretório que armazena as informações da sessão. Se o diretório não existir, ele será criado.

**/encode** (opcional)

Como as informações da sessão são codificadas. Use &quot;md 5&quot; para criptografia usando o algoritmo md 5 ou &quot;hex&quot; para codificação hexadecimal. Se você criptografar os dados da sessão, um usuário com acesso ao sistema de arquivos não poderá ler o conteúdo da sessão. O padrão é &quot;md 5&quot;.

**/header** (opcional)

O nome do cabeçalho ou cookie HTTP que armazena as informações de autorização. Se você armazenar as informações no cabeçalho http, use `HTTP:<*header-name*>`. Para armazenar as informações em um cookie, use `Cookie:<header-name>`. Se você não especificar um valor `HTTP:authorization` , será usado.

**/timeout** (opcional)

O número de segundos até a sessão expirar após a última utilização. Se &quot;800&quot; não for especificado, a sessão atingirá um pouco mais de 13 minutos após a última solicitação do usuário.

Uma configuração de exemplo é a seguinte:

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

A propriedade /renders define o URL para o qual o Dispatcher envia solicitações para renderizar um documento. A seção de exemplo `/renders` a seguir identifica uma única instância do AEM para renderização:

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

O exemplo a seguir /renders seção identifica uma instância do AEM que é executada no mesmo computador que o Dispatcher:

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

O exemplo a seguir /renders seção distribui solicitações de renderização igualmente entre duas instâncias do AEM:

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

Especifica o tempo limite da conexão acessando a instância do AEM em milissegundos. O padrão é &quot;0&quot;, fazendo com que o Dispatcher aguarde indefinidamente.

**/receiveTimeout**

Especifica o tempo em milissegundos que uma resposta tem permissão para ser tomada. O padrão é &quot;600000&quot;, fazendo com que o Dispatcher aguarde por 10 minutos. Uma configuração de &quot;0&quot; elimina completamente o tempo limite.\
Se o tempo limite for atingido durante a análise dos cabeçalhos de resposta, um status HTTP de 504 (Gateway incorreto) será retornado. Se o tempo limite for atingido enquanto o corpo da resposta for lido, o Dispatcher retornará a resposta incompleta ao cliente, mas excluirá qualquer arquivo de cache que possa ter sido escrito.

**/ipv4**

Especifica se o Dispatcher usa `getaddrinfo` a função (para ipv 6) ou `gethostbyname` a função (para ipv 4) para obter o endereço IP da renderização. Um valor de 0 causas `getaddrinfo` a ser usado. Um valor de 1 causas `gethostbyname` a ser usado. O valor padrão é 0.

A função getaddrinfo retorna uma lista de endereços IP. O Dispatcher iterará a lista de endereços até estabelecer uma conexão TCP/IP. Portanto, a propriedade ipv 4 é importante quando o nome do host de renderização está associado a endereços IP mutliple e o host, em resposta à função getaddrinfo, retorna uma lista de endereços IP que estão sempre na mesma ordem. Nessa situação, você deve usar a função gethostbyname para que o endereço IP com o qual o Dispatcher conecta esteja aleatorizado.

O Amazon Elastic Load Balance (INSTITUTE) é um serviço que responde a getaddrinfo com uma lista potencialmente ordenada de endereços IP.

**/secure**

Se `/secure` a propriedade tiver um valor de &quot;1&quot; o Dispatcher usa HTTPS para se comunicar com a instância do AEM. Para obter detalhes adicionais, consulte [Configuração do Dispatcher para usar o SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

Com a versão **4.1.6 do Dispatcher**, é possível configurar `/always-resolve` a propriedade da seguinte maneira:

* Quando definido como &quot;1&quot;, resolverá o nome do host em cada solicitação (o Dispatcher nunca armazenará em cache nenhum endereço IP). Pode haver um pequeno impacto de desempenho devido à chamada adicional necessária para obter as informações do host para cada solicitação.
* Se a propriedade não estiver definida, o endereço IP será armazenado em cache por padrão.

Além disso, essa propriedade pode ser usada caso você seja executada em problemas de resolução IP dinâmicos, como mostrado na amostra a seguir:

```xml
/rend {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configuração do acesso ao conteúdo {#configuring-access-to-content-filter}

Use `/filter` a seção para especificar as solicitações HTTP que o Dispatcher aceita. Todas as outras solicitações são enviadas de volta ao servidor Web com um código de erro 404 (página não encontrada). Se nenhuma `/filter` seção existir, todas as solicitações serão aceitas.

**Observação:** As solicitações para [o arquivo](dispatcher-configuration.md#main-pars-title-28) são sempre rejeitadas.

>[!CAUTION]
>
>Consulte a Lista de verificação de segurança [do Dispatcher](security-checklist.md) para obter mais considerações ao restringir o acesso usando o Dispatcher. Além disso, leia a [Lista de](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) segurança do AEM para obter mais detalhes sobre a instalação do AEM.

A seção /filter consiste em uma série de regras que negam ou permitem o acesso ao conteúdo de acordo com padrões na parte de linha de solicitação da solicitação HTTP. Você deve usar uma estratégia de teltelist para sua seção /filter:

* Primeiro, negue o acesso a tudo.
* Permita o acesso ao conteúdo conforme necessário.

### Definição de um filtro {#defining-a-filter}

Cada item na `/filter` seção inclui um tipo e um padrão que corresponde a um elemento específico da linha de solicitação ou a toda a linha de solicitação. Cada filtro pode conter os seguintes itens:

* **Tipo**: `/type` O indica se deseja permitir ou negar o acesso das solicitações que correspondem ao padrão. O valor pode ser `allow` ou `deny`.

* **Elemento da linha de solicitação:** Incluir `/method``/url`, `/query`ou `/protocol` um padrão para filtrar solicitações de acordo com essas partes específicas da parte da linha de solicitação da solicitação HTTP. A filtragem de elementos da linha de solicitação (em vez de toda a linha de solicitação) é o método de filtro preferencial.

* **Elementos avançados da linha de solicitação:** A partir do Dispatcher 4.2.0, quatro novos elementos de filtro estão disponíveis para uso. Esses novos elementos são `/path`, `/selectors`e `/extension``/suffix` respectivamente. Inclua um ou mais desses itens para controlar ainda mais os padrões de URL.

>[!NOTE]
>
>Para obter mais informações sobre a parte da solicitação de linha de cada um desses elementos, consulte a [página WIKI Decomposição](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) de URL de Sling.

* **property Property**: `/glob` A propriedade é usada para corresponder à linha de solicitação inteira da solicitação HTTP.

>[!CAUTION]
>
>A filtragem com globs está obsoleta no Dispatcher. Dessa forma, você deve evitar o uso de globs nas `/filter` seções, pois pode causar problemas de segurança. Assim, em vez de:

`/glob "* *.css *"`

você deve usar

`/url "*.css"`

#### A linha de solicitação Parte de solicitações HTTP {#the-request-line-part-of-http-requests}

HTTP/1.1 define a [linha de solicitação](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) da seguinte maneira:

*Solicitação de método-URI HTTP-Version*&lt; CRLF &gt;

Os caracteres &lt; CRLF &gt; redirecionam um retorno de carro seguido por um feed de linha. O exemplo a seguir é a linha de solicitação que é recievada quando um cliente solicita a página en do site Geometrixx-Outoors:

GET /content/geometrixx-outdoors/en.html HTTP .1 .1 &lt; CRLF &gt;

Seus padrões devem considerar os caracteres de espaço na linha de solicitação e os caracteres &lt; CRLF &gt;.

#### Aspas duplas x aspas simples {#double-quotes-vs-single-quotes}

Ao criar suas regras de filtragem, use aspas duplas `"pattern"` para padrões simples. Se você estiver usando o Dispatcher 4.2.0 ou posterior e seu padrão incluir uma expressão regular, deverá colocar o padrão `'(pattern1|pattern2)'` regex em aspas simples.

#### Expressões regulares {#regular-expressions}

Depois do Dispatcher 4.2.0, é possível incluir Expressões regulares estendidas do POSIX em seus padrões de filtragem.

#### Resolução de Problemas de Filtros {#troubleshooting-filters}

Se os seus filtros não estiverem sendo acionados da maneira esperada, ative [o Log de rastreio](#trace-logging) no dispatcher para que você possa ver qual filtro está interceptando a solicitação.

#### Filtro de exemplo: Negar tudo {#example-filter-deny-all}

A seção de filtro de exemplo a seguir faz com que o Dispatcher negue solicitações de todos os arquivos. Você deve negar acesso a todos os arquivos e, em seguida, permitir acesso a áreas específicas.

```xml
  /0001  { /glob "*" /type "deny" }
```

As solicitações para uma área explicitamente negada resultam no retorno de um código de erro 404 (página não encontrada).

#### Filtro de exemplo: Negar a Acess a áreas específicas {#example-filter-deny-acess-to-specific-areas}

Os filtros também permitem negar acesso a vários elementos, por exemplo, páginas ASP e áreas sigilosas em uma instância de publicação. O filtro a seguir nega acesso a páginas ASP:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Filtro de exemplo: Ativar solicitações POST {#example-filter-enable-post-requests}

O seguinte filtro de exemplo permite enviar dados de formulário pelo método POST:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Filtro de exemplo: Permitir acesso ao console Fluxo de trabalho {#example-filter-allow-access-to-the-workflow-console}

O exemplo a seguir mostra um filtro usado para negar acesso externo ao console Fluxo de trabalho:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

Se a instância de publicação usar um contexto de aplicativo da Web (por exemplo, publicar), isso também pode ser adicionado à definição do filtro.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

Se ainda precisar acessar páginas únicas na área restrita, você poderá permitir o acesso a elas. Por exemplo, para permitir acesso à guia Arquivar no console Fluxo de trabalho, adicione a seguinte seção:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>Quando vários filtros se aplicam a uma solicitação, o último padrão de filtro aplicável é eficaz.

#### Exemplo de filtro: Usando expressões regulares {#example-filter-using-regular-expressions}

Esse filtro permite extensões em diretórios de conteúdo não público usando uma expressão regular, definida aqui entre aspas simples:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Exemplo de filtro: Filtrar elementos adicionais de um URL de solicitação {#example-filter-filter-additional-elements-of-a-request-url}

Abaixo está um exemplo de regra que bloqueia o conteúdo a partir do `/content` caminho e da subárvore, usando filtros para caminhos, seletores e extensões:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Exemplo de /filter seção {#example-filter-section}

Ao configurar o Dispatcher, você deve restringir o acesso externo sempre que possível. O exemplo a seguir fornece acesso mínimo para visitantes externos:

* `/content`
* conteúdo diversos, como designs e bibliotecas do cliente; por exemplo:

   * `/etc/designs/default*`
   * `/etc/designs/mydesign*`

Depois de criar filtros, [teste o acesso à página](dispatcher-configuration.md#main-pars-title-19) para garantir que sua instância do AEM esteja segura.

A seguinte seção /filter do dispatcher. Qualquer arquivo pode ser usado como base no seu arquivo [de configuração](dispatcher-configuration.md) do Dispatcher.

Este exemplo se baseia no arquivo de configuração padrão fornecido com o Dispatcher e serve como exemplo para uso em um ambiente de produção. Os itens com # são desativados (comentados), devem ser tomados se você decidir ativar qualquer um desses itens (ao remover o # nessa linha), pois isso pode afetar a segurança.

Você deve negar o acesso a tudo e permitir o acesso a elementos específicos (limitados):

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
>Quando usada com o Apache, projete seus padrões de URL de filtro de acordo com a propriedade dispatcheruseprocessedurl do módulo Dispatcher. (Consulte [Apache Web Server - Configurar o Apache Web Server para Dispatcher](dispatcher-install.md#main-pars-55-35-1022).)

>[!NOTE]
>
>Os filtros 0030 e 0031 relativos ao Dynamic Media são aplicáveis ao AEM 6.0 e superior.

Considere as seguintes recomendações se você optar por estender o acesso:

* O acesso externo a `/admin`*ser sempre* desativado se estiver usando o CQ versão 5.4 ou uma versão anterior.

* Care must be taken when allowing access to files in `/libs`. O acesso deve ser permitido individualmente.
* Negar acesso à configuração de replicação para que ele não possa ser visto:

   * `/etc/replication.xml*`
   * `/etc/replication.infinity.json*`

* Negar acesso ao proxy reverso do Google Gadgets:

   * `/libs/opensocial/proxy*`

Dependendo da sua instalação, pode haver recursos adicionais em `/libs`, `/apps` ou em outro lugar, que precisam ser disponibilizados. Você pode usar o `access.log` arquivo como um método para determinar os recursos que estão sendo acessados externamente.

>[!CAUTION]
>
>O acesso a consoles e diretórios pode apresentar um risco de segurança para ambientes de produção. A menos que você tenha as justificações explícitas, elas devem permanecer desativadas (comentadas).

>[!CAUTION]
>
>Se você estiver [usando relatórios em um ambiente](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) de publicação, configure o Dispatcher para negar o acesso aos `/etc/reports` visitantes externos.

### Restringir strings de consulta {#restricting-query-strings}

Desde o Dispatcher versão 4.1.5, use `/filter` a seção para restringir as sequências de consulta. É altamente recomendado permitir explicitamente sequências de consulta e excluir a alocação genérica por meio `allow` de elementos de filtro.

Uma única entrada pode ter *glob* ou alguma combinação *de método*,*url*,*consulta* e *versão* , mas não ambos. O exemplo a seguir permite a string `a=*` de consulta e nega todas as outras strings de consulta para urls que resolvem o `/etc` nó:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>Se uma regra contiver a `/query`, ela corresponderá somente às solicitações que contêm uma sequência de consulta e corresponderão ao padrão de consulta fornecido.
>
>Em exemplo acima, se as solicitações com `/etc` nenhuma sequência de consulta também deveriam ser permitidas, as seguintes regras seriam necessárias:


```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Teste da segurança do Dispatcher {#testing-dispatcher-security}

Os filtros do Dispatcher devem bloquear o acesso às seguintes páginas e scripts em instâncias de publicação de AEM. Use um navegador da Web para tentar abrir as seguintes páginas como um visitante do site e verificar se um código 404 é retornado. Se algum outro resultado for obtido, ajuste seus filtros.

Observe que você deve ver a renderização normal de página para /content/add_valid_page.html? debug = layout.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr: system/jcr: Versionstorage. json
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
* /content/.{.}/libs/foundation/components/text/text. jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr % 3 trunent/jcr % 3 adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1. json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1. blubber. json
* /content/dam.tidy.-100. json
* /content/content/geometrixx.sitemap.txt
* /content/add_valid_page.query.json? statement =//*
* /content/add_valid_page.qu % 65 ry. js % 6 Fn? statement =//*
* /content/add_valid_page.query.json? statement =//*[@ transportpassword]/(@ transportpassword % 20|% 20@transportUri % 20|% 20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr: content. json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr: content. feed
* /content/add_valid_path_to_a_page/pagename._ jcr_ content. feed
* /content/add_valid_path_to_a_page/pagename.jcr: content. feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html? debug = layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /bem-vindo

Emite o seguinte comando em um terminal ou prompt de comando para determinar se o acesso a gravação anônima está ativado. Você não deve ser capaz de gravar dados no nó.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Emita o seguinte comando em um terminal ou prompt de comando para tentar invalidar o cache do Dispatcher e garantir que você recija uma resposta do código 404:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Ativando acesso a urls Vanity {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The com.adobe.granite.dispatcher.vanityurl.content package needs to be made public before publishing this contnet.</p>

 -->

Configure o Dispatcher para permitir o acesso a urls vanity configurados para suas páginas do CQ ou AEM.

Quando o acesso a urls vanity está ativado, o Dispatcher chama periodicamente um serviço executado na instância de renderização para obter uma lista de urls vanity. O Dispatcher armazena essa lista em um arquivo local. Quando uma solicitação de uma página é negada devido a um filtro na `/filter` seção, o Dispatcher consulta a lista de urls vanity. Se o URL negado estiver na lista, o Dispatcher permitirá acesso à URL personalizada.

Para habilitar o acesso a urls vanity, adicione uma `/vanity_urls` seção à `/farms` seção, semelhante ao exemplo a seguir:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

`/vanity_urls` A seção contém as seguintes propriedades:

* `/url`: O caminho para o serviço de URL personalizado que é executado na instância de renderização. O valor dessa propriedade deve ser `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: O caminho para o arquivo local onde o Dispatcher armazena a lista de urls vanity. Certifique-se de que o Dispatcher tenha acesso a gravação nesse arquivo.
* `/delay`: (Segundos) O tempo entre chamadas para o serviço URL personalizado.

>[!NOTE]
>
>Se a renderização for uma instância do AEM, instale o pacote [vanityurls-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) para instalar o serviço de URL personalizado. (Consulte [Fazer logon no compartilhamento de pacotes](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare).)

Use o procedimento a seguir para permitir o acesso a urls vanity.

1. Se o serviço de renderização for uma instância do AEM, instale o pacote com. adobe. granite. dispatcher. vanityurl. content na instância de publicação (consulte a observação acima).
1. Para cada URL personalizada que você configurou para uma página AEM ou CQ, certifique-se de que a ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` configuração nega o URL. Se necessário, adicione um filtro que negue o URL.
1. Adicione `/vanity_urls` a seção abaixo `/farms`.
1. Reinicie o servidor Web do Apache.

## Encaminhar solicitações de sindicalização - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

As solicitações de sincronização geralmente são destinadas apenas ao Dispatcher; por isso, por padrão, não são enviadas ao renderizador (por exemplo, uma instância do AEM).

Se necessário, defina a propriedade /propagateSyndPost como &quot;1&quot; para encaminhar solicitações de sindicalização para o Dispatcher. Se definido, certifique-se de que as solicitações POST não sejam negadas na seção de filtro.

## Configuração do Cache do Dispatcher - /cache {#configuring-the-dispatcher-cache-cache}

`/cache` A seção controla como o Dispatcher armazena documentos em cache. Configure várias subpropriedades para implementar suas estratégias de cache:


* /docroot
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /regras
* /statfileslevel
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /gracePeriod


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
>Para armazenamento em cache com diferenciação de permissão, leia [Armazenamento de conteúdo protegido em cache](permissions-cache.md).

### Especificação do diretório de cache {#specifying-the-cache-directory}

`/docroot` A propriedade identifica o diretório onde os arquivos em cache são armazenados.

>[!NOTE]
>
>O valor deve ser exatamente igual à raiz do documento do servidor da Web para que o Dispatcher e o servidor da Web manipulem os mesmos arquivos.\
>O servidor Web é responsável por entregar o código de status correto quando o arquivo de cache do dispatcher é usado, por isso é importante que ele também possa encontrá-lo.

Se você usar várias explorações, cada conjunto deverá usar uma raiz de documento diferente.

### Como nomear o arquivo {#naming-the-statfile}

`/statfile` A propriedade identifica o arquivo a ser usado como arquivo. O Dispatcher usa esse arquivo para registrar o horário da atualização de conteúdo mais recente. O arquivo pode ser qualquer arquivo no servidor da Web.

O status não tem conteúdo. Quando o conteúdo é atualizado, o Dispatcher atualiza o carimbo de data e hora. O arquivo padrão é denominado. stat e armazenado no docroot. O Dispatcher bloqueia o acesso ao statfile.

>[!NOTE]
>
>Se `/statfileslevel` estiver configurado, o Dispatcher ignora a `/statfile` propriedade e usa. stat como o nome.

### Fornecimento de documentos Stale quando erros ocorrem {#serving-stale-documents-when-errors-occur}

A `/serveStaleOnError` propriedade controla se o Dispatcher retorna documentos invalidados quando o servidor de renderização retorna um erro. Por padrão, quando um arquivo é tocado e invalida conteúdo em cache, o Dispatcher exclui o conteúdo em cache na próxima vez que for solicitado.

Se `/serveStaleOnError` for definido como &quot;1&quot;, o Dispatcher não excluirá conteúdo invalidado do cache, a menos que o servidor de renderização retorne uma resposta bem-sucedida. A response xx resposta do AEM ou um tempo limite de conexão faz com que o Dispatcher sirva o conteúdo desatualizado e responda com o status e o status HTTP de 111 (Falha de revalidação).

### Armazenamento em cache quando a autenticação é usada {#caching-when-authentication-is-used}

`/allowAuthorized` A propriedade controla se as solicitações que contêm qualquer uma das seguintes informações de autenticação são armazenadas em cache:

* O `authorization` cabeçalho.
* Um cookie chamado `authorization`.
* Um cookie chamado `login-token`.

Por padrão, as solicitações que incluem essas informações de autenticação não são armazenadas em cache porque a autenticação não é executada quando um documento em cache é retornado para o cliente. Essa configuração impede que o Dispatcher forneça documentos em cache para usuários que não têm os direitos necessários.

No entanto, se os seus requisitos permitirem o armazenamento em cache de documentos autenticados, defina /allowAuthorized como um:

`/allowAuthorized "1"`

>[!NOTE]
>
>Para ativar o gerenciamento de sessão (usando a `/sessionmanagement` propriedade), a `/allowAuthorized` propriedade deve ser definida `"0"`como.

### Especificar os documentos como cache {#specifying-the-documents-to-cache}

A `/rules` propriedade controla quais documentos são armazenados em cache de acordo com o caminho do documento. Independentemente da propriedade /rules, o Dispatcher nunca armazena em cache um documento nas seguintes circunstâncias:

* Se a URI de solicitação contiver um ponto de interrogação (&quot;? &quot;).\
   Isso geralmente indica uma página dinâmica, como um resultado de pesquisa que não precisa ser armazenado em cache.
* A extensão do arquivo está ausente.\
   O servidor Web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (isto pode ser configurado)
* Se a instância do AEM responder com os seguintes cabeçalhos:

   * `no-cache`
   * `no-store`
   * `must-revalidate`

>[!NOTE]
>
>Os métodos GET ou HEAD (para os cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. Para obter informações adicionais sobre armazenamento em cache de cabeçalho de resposta, consulte a [seção Cache de cabeçalhos](dispatcher-configuration.md#caching-http-response-headers) HTTP de cache.

Cada item na propriedade /rules inclui um padrão [glob](#designing-patterns-for-glob-properties) e um tipo:

* O padrão glob é usado para corresponder ao caminho do documento.
* O tipo indica se deseja armazenar em cache os documentos que correspondem ao padrão glob. O valor pode ser permitir (para armazenar o documento em cache) ou negar (para sempre renderizar o documento).

Se você não tiver páginas dinâmicas (além daquelas que já foram excluídas pelas regras acima), você pode configurar o Dispatcher para armazenar tudo em cache. A seção de regras é a seguinte:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

Para obter informações sobre propriedades de clusters, consulte [Criar padrões para glob Propriedades](#designing-patterns-for-glob-properties).

Se há seções de sua página que são dinâmicas (por exemplo, um aplicativo de notícias) ou dentro de um grupo de usuários fechado, você pode definir exceções:

>[!NOTE]
>
>Grupos de usuários fechados não devem ser armazenados em cache, pois os direitos de usuário não são verificados para páginas em cache.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Compactação**

Nos servidores Web do Apache, você pode compactar os documentos em cache. A compactação permite que o Apache retorne o documento em um formulário compactado se for solicitado pelo cliente. A compactação é feita automaticamente ao ativar o módulo `mod_deflate`Apache, por exemplo:

```xml
AddOutputFilterByType DEFLATE text/plain
```

O módulo é instalado por padrão com o Apache 2. x.

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

### Invalidar arquivos por nível de pasta {#invalidating-files-by-folder-level}

Use `/statfileslevel` a propriedade para invalidar arquivos em cache de acordo com seu caminho:

* O Dispatcher cria `.stat`arquivos em cada pasta da pasta docroot para o nível especificado. A pasta docroot é nível 0.
* Os arquivos são invalidados tocando o `.stat` arquivo. A última data de modificação `.stat` do arquivo é comparada à última data de modificação de um documento em cache. O documento é obtido novamente se o `.stat` arquivo for mais recente.

* Quando um arquivo localizado em determinado nível for invalidado, **todos** `.stat` os arquivos do docroot **para** o nível do arquivo invalidado ou a configuração configurada `statsfilevel` (o que for menor) serão toques.

   * Por exemplo: se você definir a `statfileslevel` propriedade como 6 e um arquivo for invalidado no nível 5, cada `.stat` arquivo do docroot para 5 será tocado. Continuando com este exemplo, se um arquivo for invalidado em nível 7, então cada. `stat` do docroot para 6 será tocada (desde `/statfileslevel = "6"`a).

Somente os recursos** ao longo do caminho** para o arquivo invalidado são afetados. Considere o seguinte exemplo: um site usa a estrutura `/content/myWebsite/xx/.` Se você definir `statfileslevel` como 3, um `.stat`arquivo será criado da seguinte maneira:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

Quando um arquivo é `/content/myWebsite/xx` invalidado, cada `.stat` arquivo do docroot para baixo `/content/myWebsite/xx`é tocado. Esse seria o caso apenas para `/content/myWebsite/xx` , por exemplo `/content/myWebsite/yy` , ou `/content/anotherWebSite`.

>[!NOTE]
>
>A invalidação pode ser evitada enviando um cabeçalho `CQ-Action-Scope:ResourceOnly`adicional. Isso pode ser usado para separar recursos específicos sem invalidar outras partes do cache. Consulte [esta página](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) e [Invalidando manualmente o Cache do Dispatcher](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) para obter mais detalhes.

>[!NOTE]
>
>Se um valor for especificado para a `/statfileslevel` propriedade, a `/statfile` propriedade será ignorada.

### Invalidar automaticamente arquivos em cache {#automatically-invalidating-cached-files}

`/invalidate` A propriedade define os documentos que são invalidados automaticamente quando o conteúdo é atualizado.

Com invalidação automática, o Dispatcher não exclui arquivos em cache depois de uma atualização de conteúdo, mas verifica a validade quando são solicitados a seguir. Os documentos no cache que não são invalidados automaticamente permanecerão no cache até que uma atualização de conteúdo as exclua explicitamente.

A invalidação automática é geralmente usada para páginas HTML. As páginas HTML frequentemente contêm links para outras páginas, tornando difícil determinar se uma atualização de conteúdo afeta uma página. Para garantir que todas as páginas relevantes sejam invalidadas quando o conteúdo for atualizado, invalide automaticamente todas as páginas HTML. A configuração a seguir invalida todas as páginas HTML:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }
```

Para obter informações sobre propriedades de clusters, consulte [Criar padrões para glob Propriedades](#designing-patterns-for-glob-properties).

Essa configuração causa a seguinte atividade quando /content/geometrixx/en é ativada:

* Todos os arquivos com padrão en.* são removidos da pasta /content/geometrixx/.
* A pasta /content/geometrixx/en/_jcr_content é removida.
* Todos os outros arquivos que correspondem à /invalidate ção de vídeo não são excluídos imediatamente. Esses arquivos são excluídos quando a próxima solicitação ocorre. Em nosso exemplo /content/geometrixx.html, ela será excluída quando /content/geometrixx.html for solicitada.

Se você oferecer arquivos PDF e ZIP gerados automaticamente para download, talvez seja necessário invalidá-los automaticamente. Um exemplo de configuração é a seguinte:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }
```

A integração do AEM com o Adobe Analytics fornece dados de configuração em um arquivo analytics. sitecatalyst. js no seu site. O exemplo expedidor. qualquer arquivo fornecido com o Dispatcher inclui a seguinte regra de invalidação para este arquivo:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Como usar scripts de invalidação personalizados {#using-custom-invalidation-scripts}

A propriedade /invalidateHandler permite definir um script chamado para cada solicitação de invalidação recebida pelo Dispatcher.

É chamada com os seguintes argumentos:

* Alça\
   O caminho do conteúdo que é invalidado
* Ação\
   A ação de replicação (por exemplo, Ativar, Desativar)
* Escopo da ação\
   O Escopo da ação de replicação (vazio, a não ser que um cabeçalho de `CQ-Action-Scope: ResourceOnly` é enviado, consulte [Invalidando páginas em cache do AEM](page-invalidate.md) para obter detalhes)

Isso pode ser usado para abranger vários casos de uso diferentes, como invalidar outros caches específicos do aplicativo, ou para lidar com casos em que o URL externo de uma página e seu local no Docroot não correspondem ao caminho do conteúdo.

O script de exemplo abaixo registra cada solicitação de um arquivo.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### amostra do script do manipulador de invalidação {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limitação dos clientes que podem separar o cache {#limiting-the-clients-that-can-flush-the-cache}

A propriedade /allowedClients define clientes específicos que podem esvaziar o cache. Os padrões globais são combinados com o IP.

O exemplo a seguir:

1. nega acesso a qualquer cliente
1. permite explicitamente acesso ao host localhost

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

Para obter informações sobre propriedades de clusters, consulte [Criar padrões para glob Propriedades](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>Recomenda-se definir o /allowedClients.
>
>Se isso não for feito, qualquer cliente poderá emitir uma chamada para limpar o cache; se isso for feito repetidamente, poderá afetar seriamente o desempenho do site.

### Ignorar parâmetros de URL {#ignoring-url-parameters}

`ignoreUrlParams` A seção define quais parâmetros de URL são ignorados ao determinar se uma página é armazenada em cache ou entregue a partir do cache:

* Quando um URL de solicitação contém parâmetros que são todos ignorados, a página é armazenada em cache.
* Quando um URL de solicitação contém um ou mais parâmetros que não são ignorados, a página não é armazenada em cache.

Quando um parâmetro é ignorado para uma página, a página é armazenada em cache na primeira vez que a página é solicitada. As solicitações subsequentes da página são servidas a página em cache, independentemente do valor do parâmetro na solicitação.

Para especificar quais parâmetros são ignorados, adicione regras de glob à `ignoreUrlParams` propriedade:

* Para ignorar um parâmetro, crie uma propriedade glob que permita o parâmetro.
* Para evitar que a página seja armazenada em cache, crie uma propriedade glob que negue o parâmetro.

O exemplo a seguir faz com que o Dispatcher ignore o parâmetro &quot;q&quot;, para que os urls de solicitação que incluam o parâmetro q sejam armazenados em cache:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Usando `ignoreUrlParams` o valor de exemplo, a seguinte solicitação HTTP faz com que a página seja armazenada em cache porque o `q` parâmetro é ignorado:

```xml
GET /mypage.html?q=5
```

Usando `ignoreUrlParams` o valor de exemplo, a seguinte solicitação HTTP faz com que a página **não** seja armazenada em cache porque o `p` parâmetro não é ignorado:

```xml
GET /mypage.html?q=5&p=4
```

Para obter informações sobre propriedades de clusters, consulte [Criar padrões para glob Propriedades](#designing-patterns-for-glob-properties).

### Cache de cabeçalhos de resposta HTTP {#caching-http-response-headers}

>[!NOTE]
>
>Esse recurso é avaiável com a versão **4.1.11** do Dispatcher.

A `/headers` propriedade permite definir os tipos de cabeçalho HTTP que serão armazenados em cache pelo Dispatcher. Na primeira solicitação para um recurso não armazenado em cache, todos os cabeçalhos que corresponderem um dos valores configurados (consulte a amostra de configuração abaixo) são armazenados em um arquivo separado, ao lado do arquivo do cache. Em solicitações subsequentes do recurso em cache, os cabeçalhos armazenados são adicionados à resposta.

Apresentado abaixo é uma amostra da configuração padrão:

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
>Além disso, esteja ciente de que os caracteres globais do arquivo não são permitidos. Para obter mais detalhes, consulte [Criar padrões para glob Propriedades](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>Se você precisar que o Dispatcher armazene e entregue cabeçalhos de resposta etag do AEM, faça o seguinte:
>
>* Adicione o nome do cabeçalho na `/cache/headers`seção.
>* Adicione a seguinte diretiva [Apache](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) na seção relacionada ao Dispatcher:
>



```xml
FileETag none
```

### Permissões de arquivo do Dispatcher Cache {#dispatcher-cache-file-permissions}

A `mode` propriedade especifica quais permissões de arquivo são aplicadas a novos diretórios e arquivos no cache. Essa configuração é restrita `umask` pelo processo de chamada. É um número ocô construído a partir da soma de um ou mais dos seguintes valores:

* 0400 Permitir leitura por proprietário.
* 0200 Permitir gravação por proprietário.
* 0100 Permitir que o proprietário pesquise em diretórios.
* 0040 Permitir leitura pelos membros do grupo.
* 0020 Permitir gravação por membros do grupo.
* 0010 Permitir que membros do grupo pesquisem no diretório.
* 0004 Permitir leitura por terceiros.
* 0002 Permitir gravação por terceiros.
* 0001 Permitir que outras pessoas pesquisem no diretório.

O valor padrão é 0755, o que permite ao proprietário ler, gravar ou pesquisar, bem como o grupo e outras pessoas a serem lidas ou pesquisadas.

### Toque de arquivo throttling. stat {#throttling-stat-file-touching}

Com `/invalidate` a propriedade padrão, cada ativação invalida efetivamente todos `.html` os arquivos (quando seu caminho corresponde à `/invalidate` seção). Em um site com tráfego considerável, várias ativações subsequentes aumentarão a carga da cpu no backend. Nesse cenário, seria desejável chamar o toque `.stat` de arquivo para manter o site responsivo. Você pode fazer isso usando `/gracePeriod` a propriedade.

A `/gracePeriod` propriedade define o número de segundos que um obsoleto, um recurso invalidado automaticamente ainda pode ser veiculado pelo cache após a última ativação de ocorrência. A propriedade pode ser usada em uma configuração em que um lote de ativações de outra forma invalidaria repetidamente todo o cache. O valor recomendado é de 2 segundos.

Para obter detalhes adicionais, leia também as `/invalidate``/statfileslevel`seções acima.

## Configuração de cache com base em tempo - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

Se definido, a `enableTTL` propriedade avaliará os cabeçalhos de resposta do backend e, se contiverem uma data `Cache-Control` máxima ou `Expires` uma data, um arquivo auxiliar e vazio ao lado do arquivo de cache será criado, com tempo de modificação igual à data de expiração. Quando o arquivo em cache é solicitado além do tempo de modificação, ele é solicitado automaticamente do backend.

É possível ativar o recurso adicionando esta linha ao `dispatcher.any` arquivo:

```xml
/enableTTL "1"
```

>[!NOTE]
>
>Esse recurso é avaiável com a versão **4.1.11** do Dispatcher.

## Configuração do balanceamento de carga - /statistics {#configuring-load-balancing-statistics}

A `/statistics` seção define categorias de arquivos para os quais o Dispatcher pontua a responsividade de cada renderização. O Dispatcher usa as pontuações para determinar qual renderização enviar uma solicitação.

Cada categoria que você cria define um padrão glob. O Dispatcher compara o URI do conteúdo solicitado a esses padrões para determinar a categoria do conteúdo solicitado:

* A ordem das categorias determina a ordem em que são comparadas à URI.
* O primeiro padrão de categoria que corresponde à URI é a categoria do arquivo. Não são avaliados mais padrões de categoria.

O Dispatcher suporta um máximo de 8 categorias de estatísticas. Se você definir mais de 8 categorias, apenas os primeiros 8 serão usados.

**Renderizar seleção**

Cada vez que o Dispatcher requer uma página renderizada, ele usa o seguinte algoritmo para selecionar a renderização:

1. Se a solicitação contiver o nome de renderização em um `renderid` cookie, o Dispatcher usará essa renderização.
1. Se a solicitação não incluir `renderid` cookie, o Dispatcher compara as estatísticas de renderização:

   1. O Dispatcher determina o catálogo do URI da solicitação.
   1. O Dispatcher determina qual renderização tem a pontuação de resposta mais baixa para essa categoria e seleciona essa renderização.

1. Se nenhuma renderização estiver selecionada, use a primeira renderização na lista.

A pontuação da categoria de renderização é baseada nos tempos de resposta anteriores, bem como nas conexões falhas e bem sucedidas que o Dispatcher tenta. Para cada tentativa, a pontuação da categoria do URI solicitado é atualizada.

>[!NOTE]
>
>Se você não usar o balanceamento de carga, poderá omitir esta seção.

### Definição de categorias de estatísticas {#defining-statistics-categories}

Defina uma categoria para cada tipo de documento para o qual você deseja manter as estatísticas da seleção de renderização. A seção /statistics contém uma seção /categories. Para definir uma categoria, adicione uma linha abaixo da /categories seção que possui o seguinte formato:

`/name { /glob "pattern"}`

A categoria `name` deve ser exclusiva para a produção. O `pattern` descrito na seção [Criar padrões para glob Propriedades](#designing-patterns-for-glob-properties) .

Para determinar a categoria de um URI, o Dispatcher compara a URI com cada padrão de categoria até que uma correspondência seja encontrada. O Dispatcher começa com a primeira categoria na lista e nos cointinps em ordem. Portanto, coloque categorias com padrões mais específicos primeiro.

Por exemplo, o Dispatcher o dispatcher padrão. Qualquer arquivo define uma categoria HTML e outra categoria. A categoria HTML é mais específica e, portanto, aparece primeiro:

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

`/unavailablePenalty` A propriedade define o tempo (em décimos de segundos) que é aplicado às estatísticas de renderização quando uma conexão com a renderização falha. O Dispatcher adiciona o tempo à categoria de estatística que corresponde ao URI solicitado.

Por exemplo, a penalidade é aplicada quando a conexão TCP/IP com o nome/porta designada não pode ser estabelecida, porque o AEM não está em execução (e não está acompanhando) ou devido a um problema relacionado à rede.

`/unavailablePenalty` A propriedade é um filho direto da `/farm` seção (um irmão da `/statistics` seção).

Se nenhuma `/unavailablePenalty` propriedade existir, um valor de &quot;1&quot; será usado.

```xml
/unavailablePenalty "1"
```

## Identificação de uma pasta de conexão aderente - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

`/stickyConnectionsFor` A propriedade define uma pasta que contém documentos fixos; isso será acessado usando o URL. O Dispatcher envia todas as solicitações, de um único usuário, que estão nesta pasta para a mesma instância de renderização. Conexões adesivas garantem que os dados da sessão estejam presentes e consistentes em todos os documentos. Esse mecanismo usa o `renderid` cookie.

O exemplo a seguir define uma conexão aderente com a pasta /products:

```xml
/stickyConnectionsFor "/products"
```

Quando uma página é composta por conteúdo de vários nós de conteúdo, inclua a `/paths` propriedade que lista os caminhos para o conteúdo. Por exemplo, uma página contém conteúdo de `/content/image`, `/content/video`e `/var/files/pdfs`. A configuração a seguir permite conexões adesivas para todo o conteúdo na página:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### Httponly {#httponly}

Quando as conexões adesivas são ativadas, o módulo do dispatcher define o `renderid` cookie. Esse cookie não possui `httponly` o sinalizador, que deve ser adicionado para melhorar a segurança. Você pode fazer isso definindo a `httpOnly` propriedade no `/stickyConnections` nó de um arquivo `dispatcher.any` de configuração. O valor da propriedade (0 ou 1) define se o `renderid` cookie tem o `HttpOnly` atributo anexado. O valor padrão é 0, o que significa que o atributo não será adicionado.

Para obter mais informações sobre o `httponly` sinalizador, leia [esta página](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

Quando as conexões adesivas são ativadas, o módulo do dispatcher define o `renderid` cookie. Esse cookie não possui o sinalizador **seguro** , que deve ser adicionado para melhorar a segurança. Você pode fazer isso definindo a `secure` propriedade no `/stickyConnections` nó de um arquivo `dispatcher.any` de configuração. O valor da propriedade (0 ou 1) define se o `renderid` cookie tem o `secure` atributo anexado. O valor padrão é 0, o que significa que o atributo será adicionado se * * a solicitação de entrada estiver segura. Se o valor for definido como 1, o sinalizador seguro será adicionado independentemente da solicitação de entrada estar segura ou não.

## Manipulação de erros de conexão de renderização {#handling-render-connection-errors}

Configure o comportamento do Dispatcher quando o servidor de renderização retorna um erro 500 ou está indisponível.

### Especificação de uma página de verificação de integridade {#specifying-a-health-check-page}

Use `/health_check` a propriedade para especificar um URL verificado quando um código de status 500 ocorre. Se essa página também retornar um código de status 500, a instância será considerada como estando indisponível e um tempo configurável ( `/unavailablePenalty`) será aplicado à renderização antes de tentar novamente.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }
```

### Especificação do atraso de tentativas de página {#specifying-the-page-retry-delay}

A `retryDelay` /propriedade define o tempo (em segundos) que o Dispatcher aguarda entre arredonda de conexão e tenta renderizar com o farm. Para cada round, o número máximo de vezes que o Dispatcher tenta uma conexão com uma renderização é o número de renderizações na produção.

O Dispatcher usa um valor `"1"` de se `/retryDelay` não estiver definido explicitamente. Na maioria dos casos, o valor padrão é adequado.

```xml
/retryDelay "1"
```

### Configuração do número de tentativas {#configuring-the-number-of-retries}

A `/numberOfRetries` propriedade define o número máximo de tentativas de arbusto de conexão que o Dispatcher executa com as renderizações. Se o Dispatcher não conseguir se conectar com êxito a uma renderização após esse número de tentativas, o Dispatcher retornará uma resposta falha.

Para cada round, o número máximo de vezes que o Dispatcher tenta uma conexão com uma renderização é o número de renderizações na produção. Portanto, o número máximo de vezes que o Dispatcher tenta uma conexão é ( `/numberOfRetries`) x (o número de renderizações).

Se o valor não for explicitamente definido, o valor padrão será **5**.

```xml
/numberOfRetries "5"
```

### Uso do Mecanismo de Failover {#using-the-failover-mechanism}

Ative o mecanismo de failover em seu farm do Dispatcher para reenviar solicitações para diferentes renderizações quando a solicitação original falhar. Quando o failover está ativado, o Dispatcher tem o seguinte comportamento:

* Quando uma solicitação para uma renderização retorna o status HTTP 503 (UNAVAILABLE), o Dispatcher envia a solicitação para uma renderização diferente.
* Quando uma solicitação de renderização retorna o status HTTP 50 x (exceto 503), o Dispatcher envia uma solicitação para a página configurada para a `health_check` propriedade.

   * Se a verificação de integridade retornar 500 (INTERNAL_ SERVER_ ERROR), o Dispatcher envia a solicitação original para uma renderização diferente.
   * Se a verificação de recuperação retorna o status HTTP 200, o Dispatcher retorna o erro HTTP 500 inicial para o cliente.

Para ativar o failover, adicione a seguinte linha à publicação (ou site):

```xml
/failover "1" 
```

>[!NOTE]
>
>Para tentar novamente solicitações HTTP que contêm um corpo, o Dispatcher envia um `Expect: 100-continue` cabeçalho de solicitação para a renderização antes de reduzir o conteúdo real. O CQ 5.5 com CQSE responde imediatamente com 100 (CONTINUE) ou um código de erro. Outros contêineres de servlet também devem suportar isso.

## Ignorar erros de interrupção - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>Essa opção geralmente não é necessária. Você só precisa usar isso quando visualizar as seguintes mensagens de registro:
>
>`Error while reading response: Interrupted system call`

Qualquer chamada do sistema orientada por sistema de arquivos pode ser interrompida `EINTR` se o objeto da chamada do sistema estiver localizado em um sistema remoto acessado por NFS. Se essas chamadas do sistema podem ligar ou ser interrompidas se baseia em como o sistema de arquivos subjacente foi montado na máquina local.

Use o parâmetro /ignoreEINTR se sua instância tiver tal configuração e o log contiver a seguinte mensagem:

`Error while reading response: Interrupted system call`

Internamente, o Dispatcher lê a resposta do servidor remoto (ou seja, AEM) usando um loop que pode ser representado como:

`while (response not finished) {  
read more data  
}`

Essas mensagens podem ser geradas quando o `EINTR` evento ocorre na seção &quot; `read more data`&quot; e são causadas pela recepção de um sinal antes de qualquer dado ser recebido.

Para ignorar tais interrupções, você pode adicionar o seguinte parâmetro para `dispatcher.any` (antes `/farms`):

`/ignoreEINTR "1"`

Configuração `/ignoreEINTR` que `"1"` faz com que o Dispatcher continue tentando ler dados até que a resposta completa seja lida. O valor padrão é 0 e desativa a opção.

## Projetar padrões para glob Propriedades {#designing-patterns-for-glob-properties}

Várias seções no arquivo de configuração do Dispatcher usam `glob` propriedades como critérios de seleção para solicitações de cliente. Os valores das propriedades dos clusters são padrões que o Dispatcher compara com um aspecto da solicitação, como o caminho do recurso solicitado ou o endereço IP do cliente. Por exemplo, os itens na `/filter` seção usam padrões de glob para identificar os caminhos das páginas que o Dispatcher age ou rejeita.

Os valores glob podem incluir caracteres curinga e caracteres alfanuméricos para definir o padrão.

| Caracteres válidos | Descrição | Exemplos |
|--- |--- |--- |
| `*` | Corresponde a zero ou a instâncias mais contínuas de qualquer caractere na string. O caractere final da correspondência é determinado por uma das seguintes situações: <br/>Um caractere na string corresponde ao caractere seguinte no padrão e o caractere padrão tem as seguintes características:<br/><ul><li>Não é um *</li><li>Não é um?</li><li>Um caractere literal (incluindo um espaço) ou uma classe de caracteres.</li><li>O fim do padrão é atingido.</li></ul>Dentro de uma classe de caracteres, o caractere é interpretado literalmente. | `*/geo*` Corresponde a qualquer página abaixo do `/content/geometrixx` nó e `/content/geometrixx-outdoors` do nó. As solicitações HTTP a seguir correspondem ao padrão glob: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*`<br/>Corresponde a qualquer página abaixo do `/content/geometrixx-outdoors` nó. Por exemplo, a solicitação HTTP a seguir corresponde ao padrão glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul> |
| `?` | Corresponde a qualquer caractere único. Use classes de caracteres externas. Em uma classe de caracteres, esse caractere é interpretado literalmente. | `*outdoors/??/*`<br/> Corresponde às páginas de qualquer idioma no site geometrixx-outdoors. Por exemplo, a solicitação HTTP a seguir corresponde ao padrão glob: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>A solicitação a seguir não corresponde ao padrão glob: <br/><ul><li>&quot; GET /content/geometrixx-outdoors/en.html &quot;</li></ul> |
| `[ and ]` | Redefine o início e o fim de uma classe de caracteres. As classes de caracteres podem incluir um ou mais intervalos de caracteres e caracteres únicos.<br/>Uma correspondência ocorre se o caractere de destino corresponder a qualquer um dos caracteres na classe de caracteres ou dentro de um intervalo definido.<br/>Se o colchete de fechamento não estiver incluído, o padrão não produz correspondências. | `*[o]men.html*`<br/> Corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*`<br/>Corresponde às seguintes solicitações HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `-` | Denota um intervalo de caracteres. Para uso em classes de caracteres. Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | `*[m-p]men.html*` Corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul> |
| `!` | Nega a classe de caracteres ou caracteres a seguir. Use apenas para negação de caracteres e intervalos de caracteres dentro classes de caracteres. Equivalente ao `^ wildcard`. <br/>Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | `*[!o]men.html*`<br/> Corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Não corresponde à seguinte solicitação HTTP: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Não corresponde à seguinte solicitação HTTP:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` ou `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul> |
| `^` | Nega o intervalo de caracteres ou caracteres a seguir. Use para negar somente caracteres e intervalos de caracteres dentro classes de caracteres. Equivalente ao caractere `!` curinga. <br/>Fora de uma classe de caracteres, esse caractere é interpretado literalmente. | Os exemplos do caractere `!` curinga se aplicam, substituindo `!` os caracteres no padrões de exemplo por `^` caracteres. |


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

Na configuração do servidor da Web, você pode definir:

* O local do arquivo de log do Dispatcher.
* O nível de log.

Consulte a documentação do servidor da Web e o arquivo readme da sua instância do Dispatcher para obter mais informações.

**Logs de Apache Girated/Piped**

Se estiver usando um **servidor** Web Apache, você pode usar a funcionalidade padrão para logs girados e/ou agrupados. Por exemplo, usando logs com canetas:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

Isso girará automaticamente:

* o arquivo de log do expedidor; com um carimbo de data e hora na extensão (logs/dispatcher. log % Y % m % d).
* semanalmente (60 x 60 x 24 x 7 = 604800 segundos).

Consulte a documentação do servidor da Web Apache em Logs de rotação e Piped; por exemplo [Apache 2.2](https://httpd.apache.org/docs/2.2/logs.html).

>[!NOTE]
>
>Na instalação, o nível de log padrão é alto (ou seja, nível 3 = Depuração), para que o Dispatcher registre todos os erros e avisos. Isso é muito útil nos estágios iniciais.
>
>No entanto, isso requer recursos adicionais, portanto, quando o Dispatcher estiver funcionando sem problemas *de acordo com seus requisitos*, você pode (deve) diminuir o nível de log.

### Registro de rastreio {#trace-logging}

Entre outros aprimoramentos do Dispatcher, a versão 4.2.0 também introduz o Log de rastreio.

Este é um nível mais alto do que o registro em depuração, mostrando informações adicionais nos logs. Ele adiciona registro para:

* Os valores dos cabeçalhos encaminhados;
* A regra que está sendo aplicada para determinada ação.

Você pode ativar o Registro de rastreio definindo o nível de log para `4` o servidor Web.

Abaixo está um exemplo de registros com o rastreamento ativado:

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

Para confirmar a operação e a interação básica do servidor da Web, do Dispatcher e da instância do AEM, você pode usar as seguintes etapas:

1. Defina o `loglevel` para `3`.

1. Inicie o servidor da Web; isso também inicia o Dispatcher.
1. Inicie a instância do AEM.
1. Verifique os arquivos de log e erro do servidor Web e do Dispatcher.\
   Dependendo do servidor da Web, você deverá ver mensagens como:\
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`\
   e:\
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Faça o Surf o site através do servidor da Web. Confirme se o conteúdo está sendo exibido conforme necessário.\
   Por exemplo, em uma instalação local em que o AEM é executado em porta `4502` e o servidor Web em `80` acesso ao console Sites usando ambos:\
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
https://localhost:80/libs/wcm/core/content/siteadmin.html  
`Os resultados devem ser idênticos. Confirme o acesso a outras páginas com o mesmo mecanismo.

1. Verifique se o diretório do cache está sendo preenchido.
1. Ative uma página para verificar se o cache está sendo limpo corretamente.
1. Se tudo estiver operando corretamente, você pode reduzir o `loglevel` para `0`.

## Uso de vários Dispatchers {#using-multiple-dispatchers}

Em configurações complexas, você pode usar vários Dispatchers. Por exemplo, você pode usar:

* um Dispatcher para publicar um site na Intranet
* um segundo Dispatcher, em um endereço diferente e com configurações de segurança diferentes, para publicar o mesmo conteúdo na Internet.

Nesse caso, verifique se cada solicitação passa por apenas um Dispatcher. Um Dispatcher não trata solicitações que vêm de outro Dispatcher. Portanto, certifique-se de que os Dispatchers acessam diretamente o site do AEM.

## Depuração {#debugging}

Ao adicionar o cabeçalho `X-Dispatcher-Info` a uma solicitação, o Dispatcher responde se o destino foi armazenado em cache, retornado de cache ou não armazenado em cache. O cabeçalho de resposta `X-Cache-Info` contém essas informações em um formulário legível. Você pode usar esses cabeçalhos de resposta para depurar problemas envolvendo respostas armazenadas em cache pelo Dispatcher.

Essa funcionalidade não está ativada por padrão; portanto, para que o cabeçalho `X-Cache-Info` de resposta seja incluído, o conjunto de respostas deverá conter a seguinte entrada:

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

Além disso, o `X-Dispatcher-Info` cabeçalho não precisa de um valor, mas se você usar `curl` para teste, deverá fornecer um valor para enviar o cabeçalho, como:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html
```

Abaixo está uma lista contendo os cabeçalhos de resposta `X-Dispatcher-Info` que retornarão:

* **cache**\
   O arquivo de destino está contido no cache e o expedidor determinou que é válido entregá-lo.
* **cache**\
   O arquivo de destino não está contido no cache e o expedidor determinou que é válido armazenar o resultado em cache e entregá-lo.
* **cache: o arquivo de stat é mais recente que**
o arquivo de destino está contido no cache, no entanto, é invalidado por um arquivo de estatística mais recente. O expedidor excluirá o arquivo de destino, recrie-o da saída e entregará-o.
* **não armazenável em cache: nenhuma raiz
do documento** A configuração do conjunto não contém uma raiz de documento (elemento `cache.docroot`de configuração).
* **não armazenável em cache: caminho do arquivo cache muito longo**\
   O arquivo de destino - a concatenação de raiz do documento e o arquivo de URL - excede o maior nome de arquivo possível no sistema.
* **não armazenável em cache: caminho de arquivo temporário muito longo**\
   O modelo de nome de arquivo temporário excede o maior nome possível de arquivo no sistema. O dispatcher cria um arquivo temporário primeiro, antes de realmente criar ou substituir o arquivo em cache. O nome do arquivo temporário é o nome do arquivo de destino com os caracteres `_YYYYXXXXXX` anexados a ele, onde o `Y` e `X` será substituído para criar um nome exclusivo.
* **não armazenável em cache: URL de solicitação não tem extensão**\
   O URL da solicitação não tem extensão, ou há um caminho após a extensão do arquivo, por exemplo: `/test.html/a/path`.
* **não armazenável em cache: solicitação não era GET ou HEAD**
O método HTTP não é GET ou HEAD. O dispatcher assume que a saída conterá dados dinâmicos que não devem ser armazenados em cache.
* **não armazenável em cache: solicitação continha uma string de consulta**\
   A solicitação continha uma sequência de consulta. O expedidor parte do princípio de que a saída depende da sequência de caracteres de consulta fornecida e, portanto, do cache.
* **não armazenável em cache: o gerente de sessão não autenticou**\
   O cache do farm é regido por um gerente de sessão (a configuração contém um `sessionmanagement` nó) e a solicitação não contém as informações de autenticação apropriadas.
* **não armazenável em cache: solicitação contém autorização**\
   A publicação não tem permissão para armazenar em cache ( `allowAuthorized 0`) e a solicitação contém informações de autenticação.
* **não armazenável em cache: o target é um diretório**\
   O arquivo de destino é um diretório. Isso pode apontar para algum erro conceitual, onde um URL e um suburl contêm saída armazenável, por exemplo, se uma solicitação `/test.html/a/file.ext` vier primeiro e contiver saída armazenável, o expedidor não poderá armazenar o resultado de uma solicitação subsequente em cache `/test.html`.
* **não armazenável em cache: o URL da solicitação tem uma barra**\
   O URL da solicitação tem uma barra.
* **não armazenável em cache: URL de solicitação não em cache**\
   As regras de cache do farm negam explicitamente o armazenamento em cache da saída de algum URL de solicitação.
* **não armazenável em cache: verificador de autorização negado acesso**\
   O verificador de autorização do farm negou acesso ao arquivo em cache.
* **não armazenável em cache: sessão não válida**
O cache do farm é regido por um gerente de sessão (a configuração contém um `sessionmanagement` nó) e a sessão do usuário não é mais válida.
* **não armazenável em cache: response contém`no_cache `** o servidor remoto retornou um `Dispatcher: no_cache` cabeçalho, proibindo o dispatcher de armazenar o resultado em cache.
* **não armazenável em cache: a duração do conteúdo da resposta é zero**
O comprimento do conteúdo da resposta é zero; o expedidor não criará um arquivo de comprimento zero.
