---
title: 'Uso do Dispatcher com vários domínios '
seo-title: 'Uso do Dispatcher com vários domínios '
description: Saiba como usar o Dispatcher para processar solicitações de página em vários domínios da Web.
seo-description: Saiba como usar o Dispatcher para processar solicitações de página em vários domínios da Web.
uuid: 7342 a 1 c 2-fe 61-49 be-a 240-b 487 d 53 c 7 ec 1
contentOwner: Usuário
cq-exporttemplate: /etc/contentsync/templates/geometrixx/page/rewrite
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: 40 d 91 d 66-c 99 b -422 d -8 e 61-c 0 ed 23272 ef
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Uso do Dispatcher com vários domínios {#using-dispatcher-with-multiple-domains}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação do AEM ou do CQ.

Use o Dispatcher para processar solicitações de página em vários domínios da Web ao oferecer suporte às seguintes condições:

* O conteúdo da Web para ambos os domínios é armazenado em um único repositório do AEM.
* Os arquivos no cache do Dispatcher podem ser invalidados separadamente para cada domínio.

Por exemplo, uma empresa publica sites para duas de suas marcas: Marca A e Marca B. O conteúdo das páginas do site é criado no AEM e armazenado na mesma área de trabalho de repositório:

```
/
| - content  
   | - sitea  
       | - content nodes  
   | - siteb  
       | - content nodes
```

As páginas para `BrandA.com` são armazenadas abaixo `/content/sitea`. As solicitações de cliente para o URL `https://BrandA.com/en.html` são retornadas a página renderizada do `/content/sitea/en` nó. Da mesma forma, as páginas para `BrandB.com` são armazenadas abaixo `/content/siteb`.

Ao usar o Dispatcher para armazenar o conteúdo em cache, as associações devem ser feitas entre o URL da página na solicitação HTTP do cliente, o caminho do arquivo correspondente no cache e o caminho do arquivo correspondente no repositório.

## Solicitações de cliente

Quando os clientes enviam solicitações HTTP para o servidor da Web, o URL da página solicitada deve ser resolvido para o conteúdo no cache do Dispatcher e, eventualmente, para o conteúdo no repositório.

![](assets/chlimage_1-8.png)

1. O sistema de nome de domínio descobre o endereço IP do servidor Web registrado para o nome do domínio na solicitação HTTP.
1. A solicitação HTTP é enviada para o servidor da Web.
1. A solicitação HTTP é transmitida para o Dispatcher.
1. O Dispatcher determina se os arquivos em cache são válidos. Se for válido, os arquivos em cache serão servidos para o cliente.
1. Se os arquivos em cache não forem válidos, o Dispatcher solicita páginas renderizadas recentemente da instância de publicação de AEM.

## Cache de cache

Quando os agentes de replicação do Dispatcher Flush solicitam que o Dispatcher invalide arquivos em cache, o caminho do conteúdo no repositório deve resolver para o conteúdo no cache.

![](assets/chlimage_1-9.png)

1. Uma página é ativada na instância do autor do AEM e o conteúdo é replicado para a instância de publicação.
1. O Agente Flush do Dispatcher chama o Dispatcher para invalidar o cache do conteúdo replicado.
1. O Dispatcher toque em um ou mais arquivos. stat para invalidar os arquivos em cache.

Para usar o Dispatcher com vários domínios, você precisa configurar o AEM, o Dispatcher e o servidor Web. As soluções descritas nesta página são gerais e aplicam-se à maioria dos ambientes. Devido à complexidade de algumas topologias de AEM, sua solução pode exigir outras configurações personalizadas para resolver problemas específicos. Provavelmente, você precisará adaptar os exemplos para atender às políticas de gerenciamento e infraestrutura de TI existentes.

## Mapeamento de URL {#url-mapping}

Para ativar urls de domínio e caminhos de conteúdo para resolver arquivos em cache, em algum momento no processo, um caminho de arquivo ou URL da página deve ser convertido. As descrições das seguintes estratégias comuns são fornecidas, onde as traduções de caminho ou URL ocorrem em pontos diferentes no processo:

* (Recomendado) A instância de publicação de AEM usa o mapeamento de Sling para resolução de recursos para implementar regras internas de regravação de URL. Os urls de domínio são convertidos para caminhos de repositório de conteúdo. (Consulte [Regravar urls de entrada do AEM](dispatcher-domains.md#main-pars-title-2).)
* O servidor Web usa regras de regravação internas de URL que convertem os urls de domínio para colocar os caminhos em cache. (Consulte [o servidor Web Regrava os urls de entrada](dispatcher-domains.md#main-pars-title-1).)

Geralmente é desejável usar urls curtos para páginas da Web. Normalmente, os urls de página espelham a estrutura das pastas de repositório que contêm o conteúdo da Web. No entanto, os urls não revelam os nós de repositório mais altos, como `/content`. O cliente não tem necessariamente conhecimento da estrutura do repositório do AEM.

## Requisitos gerais {#general-requirements}

Seu ambiente deve implementar as seguintes configurações para auxiliar o Dispatcher trabalhando com vários domínios:

* O conteúdo de cada domínio reside em ramos separados do repositório (consulte o ambiente de exemplo abaixo).
* O agente de replicação do Dispatcher Flush está configurado na instância de publicação de AEM. (Consulte [Invalidando cache do Dispatcher de uma Instância](page-invalidate.md)de publicação.)
* O sistema de nome de domínio resolve os nomes de domínio para o endereço IP do servidor da Web.
* O cache do Dispatcher espelha a estrutura de diretório do repositório de conteúdo do AEM. Os caminhos de arquivo abaixo da raiz do documento do servidor da Web são os mesmos dos caminhos dos arquivos no repositório.

## Ambiente para os exemplos fornecidos {#environment-for-the-provided-examples}

As soluções de exemplo fornecidas se aplicam a um ambiente com as seguintes características:

* As instâncias de autor e publicação do AEM são implantadas em sistemas Linux.
* O Apache HTTPD é o servidor Web implantado em um sistema Linux.
* O repositório de conteúdo do AEM e a raiz do documento do servidor da Web usam as seguintes estruturas de arquivo (a raiz do documento do servidor da Web Apache é/`usr/lib/apache/httpd-2.4.3/htdocs)`:

   **Repositório**

```
  | - /content  
    | - sitea  
  |    | - content nodes 
    | - siteb  
       | - conent nodes
```

**Raiz do documento do servidor da Web**

```
  | - /usr  
    | - lib  
      | - apache  
        | - httpd-2.4.3  
          | - htdocs  
            | - content  
              | - sitea  
                 | - content nodes 
              | - siteb  
                 | - content nodes
```

## Urls de entrada do AEM Rewrite {#aem-rewrites-incoming-urls}

O mapeamento de sling para resolução de recursos permite associar urls de entrada com caminhos de conteúdo AEM. Crie mapeamentos na instância de publicação de AEM para que as solicitações de renderização do Dispatcher resolva o conteúdo correto no repositório.

As solicitações do Dispatcher para renderização de página identificam a página usando o URL que ele é transmitido do servidor Web. Quando o URL inclui um nome de domínio, os mapeamentos de Sling resolvem o URL para o conteúdo. O gráfico a seguir ilustra um mapeamento do `branda.com/en.html` URL para o `/content/sitea/en` nó.

![](assets/chlimage_1-10.png)

O cache do Dispatcher espelha a estrutura do nó de repositório. Portanto, quando as ativações de página ocorrem, as solicitações resultantes de inválação da página em cache não exigem traduções de URL ou de caminho.

![](assets/chlimage_1-11.png)

## Definir hosts virtuais no servidor Web {#define-virtual-hosts-on-the-web-server}

Defina hosts virtuais no servidor da Web para que uma raiz de documento diferente possa ser atribuída a cada domínio da Web:

* O servidor Web deve definir um domínio virtual para cada domínio da Web.
* Para cada domínio, configure a raiz do documento para coincidir com a pasta no repositório que contém o conteúdo da Web do domínio.
* Cada domínio virtual também deve incluir configurações relacionadas ao Dispatcher, conforme descrito na página [Instalação do Dispatcher](dispatcher-install.md) .

O arquivo de exemplo `httpd.conf` a seguir configura dois domínios virtuais para um servidor Web Apache:

* Os nomes do servidor (que coincidem com os nomes de domínio) são branda.com (linha 16) e brandb.com (linha 30).
* A raiz do documento de cada domínio virtual é o diretório no cache do Dispatcher que contém as páginas do site. (linhas 17 e 31)

Com essa configuração, o servidor da Web executa as seguintes ações quando obtém uma solicitação para `https://branda.com/en/products.html`:

* Associa o URL com o host virtual que tem um `ServerName` de `branda.com.`

* Encaminhe o URL para o Dispatcher.

### httpd. conf {#httpd-conf}

```xml
# load the Dispatcher module
LoadModule dispatcher_module modules/mod_dispatcher.so
# configure the Dispatcher module
<IfModule disp_apache2.c>
 DispatcherConfig conf/dispatcher.any
 DispatcherLog    logs/dispatcher.log  
 DispatcherLogLevel 3
 DispatcherNoServerHeader 0 
 DispatcherDeclineRoot 0
 DispatcherUseProcessedURL 0
 DispatcherPassError 0
</IfModule>

# Define virtual host for brandA.com
<VirtualHost *:80>
  ServerName branda.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# define virtual host for brandB.com
<VirtualHost *:80>
  ServerName brandB.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# document root for web server
DocumentRoot "/usr/lib/apache/httpd-2.4.3/htdocs"
```

Observe que os hosts virtuais herdam o [valor](dispatcher-install.md#main-pars-67-table-7) da propriedade dispatcherconfig configurado na seção do servidor principal. Os hosts virtuais podem incluir sua própria propriedade dispatcherconfig para substituir a configuração do servidor principal.

### Configurar o Dispatcher para lidar com vários domínios {#configure-dispatcher-to-handle-multiple-domains}

Para suportar urls que incluem nomes de domínio e seus hosts correspondentes correspondentes, defina as seguintes empresas do Dispatcher:

* Configure um farm do Dispatcher para cada host virtual. Essas fazendas processam solicitações do servidor Web para cada domínio, verificam arquivos em cache e solicitam páginas das renderizações.
* Configure um farm do Dispatcher usado para invalidar o conteúdo do cache, independentemente do domínio ao qual o conteúdo pertence. Esse farm trata solicitações de invalidação do arquivo dos agentes de replicação do Flush Dispatcher.

### Criar fazendas do Dispatcher para hosts virtuais

Os fazimentos para hosts virtuais devem ter as seguintes configurações para que os urls nas solicitações HTTP do cliente sejam resolvidos para os arquivos corretos no cache do Dispatcher:

* `/virtualhosts` A propriedade é definida como o nome do domínio. Essa propriedade permite que o Dispatcher associe o conjunto ao domínio.
* `/filter` A propriedade permite acesso ao caminho do URL da solicitação truncado depois da parte do nome do domínio. Por exemplo, para `https://branda.com/en.html` o URL, o caminho é interpretado como `/en.html`, portanto, o filtro deve permitir o acesso a esse caminho.

* `/docroot` A propriedade é definida como o caminho do diretório raiz do conteúdo do site do domínio no cache do Dispatcher. Esse caminho é usado como o prefixo do URL concatenado da solicitação original. Por exemplo, o docroot de `/usr/lib/apache/httpd-2.4.3/htdocs/sitea` causas faz com que a solicitação `https://branda.com/en.html` seja resolvida para o `/usr/lib/apache/httpd-2.4.3/htdocs/sitea/en.html` arquivo.

Além disso, a instância de publicação de AEM deve ser designada como a renderização para o host virtual. Configure outras propriedades da empresa, conforme necessário. O código a seguir é uma configuração de publicação abreviada para o domínio branda.com:

```xml
/farm_sitea  {     
    ...
    /virtualhosts { "branda.com" }
    /renders {
      /rend01  { /hostname "127.0.0.1"  /port "4503" }
    }
    /filter {
      /0001 { /type "deny"  /glob "*" }
      /0023 { /type "allow" /glob "*/en*" }  
      ...
     }
    /cache {
      /docroot "/usr/lib/apache/httpd-2.4.3/htdocs/content/sitea"
      ...
   }
   ...
}
```

### Criar um farm do Dispatcher para invalidação do cache

Um fazidor do Dispatcher é necessário para lidar com solicitações de arquivos em cache invalidando. Esse farm deve ser capaz de acessar arquivos. stat nos diretórios de documentos de cada host virtual.

As configurações de propriedade a seguir permitem que o Dispatcher solucione arquivos no repositório de conteúdo do AEM a partir de arquivos no cache:

* `/docroot` A propriedade é definida como o docroot padrão do servidor Web. Normalmente, esse é o diretório onde a `/content` pasta é criada. Um valor de exemplo para Apache no Linux é `/usr/lib/apache/httpd-2.4.3/htdocs`.
* `/filter` A propriedade permite acesso a arquivos abaixo do `/content` diretório.

`/statfileslevel`A propriedade deve ser alta o suficiente para que os arquivos. stat sejam criados no diretório raiz de cada host virtual. Essa propriedade permite que o cache de cada domínio seja invalidado separadamente. Para obter a configuração de exemplo, um `/statfileslevel` valor `2` de criação de arquivos. stat no `*docroot*/content/sitea` diretório e `*docroot*/content/siteb` no diretório.

Além disso, a instância de publicação deve ser designada como a renderização para o host virtual. Configure outras propriedades da empresa, conforme necessário. O código a seguir é uma configuração abreviada do conjunto usado para invalidar o cache:

```xml
/farm_flush {  
    ...
    /virtualhosts   { "invalidation_only" }
    /renders  {
      /rend01  { /hostname "127.0.0.1" /port "4503" }
    }
    /filter   {
      /0001 { /type "deny"  /glob "*" }
      /0023 { /type "allow" /glob "*/content*" } 
      ...
      }
    /cache  {
       /docroot "/usr/lib/apache/httpd-2.4.3/htdocs"
       /statfileslevel "2"
       ...
   }
   ...
}
```

Quando você inicia o servidor da Web, o log do Dispatcher (no modo de depuração) indica a inicialização de todas as empresas:

```shell
Dispatcher initializing (build 4.1.2)
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_sitea].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_siteb].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_flush].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs
[Fri Nov 02 16:27:18 2012] [I] [24974(140006182991616)] Dispatcher initialized (build 4.1.2)
```

### Configurar o mapeamento de sling para resolução de recursos {#configure-sling-mapping-for-resource-resolution}

Use o mapeamento de Sling para resolução de recursos para que urls baseados em domínio resolverem o conteúdo na instância de publicação de AEM. O mapeamento de recursos traduz os urls recebidos do Dispatcher (originalmente de solicitações HTTP do cliente) para nós de conteúdo.

Para saber mais sobre o mapeamento de recursos de Sling, consulte [Mapeamentos para resolução](https://sling.apache.org/site/mappings-for-resource-resolution.html) de recursos na documentação de Sling.

Normalmente, os mapeamentos são necessários para os seguintes recursos, embora possam ser necessários mapeamentos adicionais:

* O nó raiz da página de conteúdo (abaixo `/content`)
* O nó de design usado pelas páginas (abaixo `/etc/designs`)
* A `/libs` pasta

Depois de criar o mapeamento para a página de conteúdo, para descobrir os mapeamentos necessários, use um navegador da Web para abrir uma página no servidor da Web. No arquivo error. log da instância de publicação, localize mensagens sobre recursos que não foram encontrados. A seguinte mensagem de exemplo indica que um mapeamento é `/etc/clientlibs` obrigatório:

```shell
01.11.2012 15:59:24.601 *INFO* [10.36.34.243 [1351799964599] GET /etc/clientlibs/foundation/jquery.js HTTP/1.1] org.apache.sling.engine.impl.SlingRequestProcessorImpl service: Resource /content/sitea/etc/clientlibs/foundation/jquery.js not found
```

>[!NOTE]
>
>O transformador do linkverificador do rewriter padrão do Apache Sling modifica automaticamente hiperlinks na página para evitar links quebrados. No entanto, a reformulação do link é realizada somente quando o destino do link é um arquivo HTML ou HTM. Para atualizar links para outros tipos de arquivo, crie um componente de transformação e adicione-o a um pipeline de regravação HTML.

### Exemplo de nós de mapeamento de recursos

A tabela a seguir lista os nós que implementam o mapeamento de recursos para o domínio branda.com. Nós similares são criados para o `brandb.com` domínio, como `/etc/map/http/brandb.com`. Em todos os casos, é necessário mapear os mapeamentos quando as referências no HTML da página não forem resolvidas corretamente no contexto de Sling.

| Caminho do nó | Tipo | Propriedade |
|--- |--- |--- |
| `/etc/map/http/branda.com` | sling: Mapeamento | Nome: sling: Tipo de internalredirect: Valor da string: /content/sitea |
| `/etc/map/http/branda.com/libs` | sling: Mapeamento | Nome: sling: Tipo de internalredirect <br/>: Valor da string <br/>: /libs |
| `/etc/map/http/branda.com/etc` | sling: Mapeamento |
| `/etc/map/http/branda.com/etc/designs` | sling: Mapeamento | Nome: sling: Internalredirect <br/>vtype: String <br/>vvalue: /etc/designs |
| `/etc/map/http/branda.com/etc/clientlibs` | sling: Mapeamento | Nome: sling: Internalredirect <br/>vtype: String <br/>vvalue: /etc/clientlibs |

## Configuração do agente de replicação do Dispatcher Flush {#configuring-the-dispatcher-flush-replication-agent}

O agente de replicação do Dispatcher Flush na instância de publicação de AEM deve enviar solicitações de invalidação para a fazenda correta do Dispatcher. Para direcionar uma publicação, use a propriedade URI do agente de replicação do Dispatcher Flush (na guia Transporte). Inclua o valor da `/virtualhost` propriedade para o conjunto do Dispatcher configurado para invalidar o cache:

`https://*webserver_name*:*port*/*virtual_host*/dispatcher/invalidate.cache`

Por exemplo, para usar `farm_flush` a fazenda do exemplo anterior, o URI é `https://localhost:80/invalidation_only/dispatcher/invalidate.cache`.

![](assets/chlimage_1-12.png)

## O servidor Web regrava urls de entrada {#the-web-server-rewrites-incoming-urls}

Use o recurso interno de regravação de URL do servidor Web para converter urls com base em domínio para caminhos de arquivo no cache do Dispatcher. Por exemplo, as solicitações de cliente para a `https://brandA.com/en.html` página são traduzidas para o `content/sitea/en.html`arquivo na raiz do documento do servidor Web.

![](assets/chlimage_1-13.png)

O cache do Dispatcher espelha a estrutura do nó de repositório. Portanto, quando as ativações de página ocorrem, as solicitações resultantes de invalidar a página em cache não exigem traduções de URL ou de caminho.

![](assets/chlimage_1-14.png)

## Definir hosts virtuais e regras de regravação no servidor Web {#define-virtual-hosts-and-rewrite-rules-on-the-web-server}

Configure os seguintes aspectos no servidor Web:

* Defina um host virtual para cada domínio da Web.
* Para cada domínio, configure a raiz do documento para coincidir com a pasta no repositório que contém o conteúdo da Web do domínio.
* Para cada domínio virtual, crie uma regra de renomeação de URL que converta o URL de entrada para o caminho do arquivo em cache.
* Cada domínio virtual também deve incluir configurações relacionadas ao Dispatcher, conforme descrito na página [Instalação do Dispatcher](dispatcher-install.md) .
* O módulo Dispatcher deve ser configurado para usar o URL que o servidor da Web escreveu novamente. (Consulte a `DispatcherUseProcessedURL` propriedade em [Instalação do Dispatcher](dispatcher-install.md).)

O exemplo httpd. conf a seguir configura dois hosts virtuais para um servidor Web Apache:

* Os nomes do servidor (que coincidem com os nomes de domínio) são `brandA.com` (linha 16) e `brandB.com` (linha 32).

* A raiz do documento de cada domínio virtual é o diretório no cache do Dispatcher que contém as páginas do site. (linhas 20 e 33)
* A regra de regravação de URL para cada domínio virtual é uma expressão regular que coloca o caminho da página solicitada no caminho para as páginas no cache. (linhas 19 e 35)
* A `DispatherUseProcessedURL` propriedade está definida `1`como. (linha 10)

Por exemplo, o servidor Web executa as seguintes ações quando recebe uma solicitação com `https://brandA.com/en/products.html` o URL:

* Associa o URL com o host virtual que tem um `ServerName` de `brandA.com.`
* Regravar o URL para ser `/content/sitea/en/products.html.`
* Encaminhe o URL para o Dispatcher.

### httpd. conf {#httpd-conf-1}

```xml
# load the Dispatcher module
LoadModule dispatcher_module modules/mod_dispatcher.so
# configure the Dispatcher module
<IfModule disp_apache2.c>
 DispatcherConfig conf/dispatcher.any
 DispatcherLog    logs/dispatcher.log  
 DispatcherLogLevel 3
 DispatcherNoServerHeader 0 
 DispatcherDeclineRoot 0
 DispatcherUseProcessedURL 1
 DispatcherPassError 0
</IfModule>

# Define virtual host for brandA.com
<VirtualHost *:80>
  ServerName branda.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
  RewriteEngine  on
  RewriteRule    ^/(.*)\.html$  /content/sitea/$1.html [PT]
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# define virtual host for brandB.com
<VirtualHost *:80>
  ServerName brandB.com
  DocumentRoot /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
  RewriteEngine  on
  RewriteRule    ^/(.*)\.html$  /content/siteb/$1.html [PT]
   <Directory /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb>
     <IfModule disp_apache2.c>
       SetHandler dispatcher-handler
       ModMimeUsePathInfo On
     </IfModule>
     Options FollowSymLinks
     AllowOverride None
   </Directory>
</VirtualHost>

# document root for web server
DocumentRoot "/usr/lib/apache/httpd-2.4.3/htdocs"
```

### Configurar um Dispatcher Farm {#configure-a-dispatcher-farm}

Quando o servidor Web reescreve urls, o Dispatcher requer uma única empresa definida de acordo com [Configuração do Dispatcher](dispatcher-configuration.md). As configurações a seguir são necessárias para suportar os hosts virtuais do servidor Web e as regras de renomeação de URL:

* `/virtualhosts` A propriedade deve incluir os valores servername para todas as definições virtualhost.
* `/statfileslevel` A propriedade deve ser alta o suficiente para criar arquivos. stat nos diretórios que contêm os arquivos de conteúdo de cada domínio.

O arquivo de configuração de exemplo a seguir se baseia no `dispatcher.any` arquivo de exemplo instalado com o Dispatcher. As seguintes alterações são necessárias para suportar as configurações do servidor Web do `httpd.conf` arquivo anterior:

* `/virtualhosts` A propriedade faz com que o Dispatcher manipule solicitações para os domínios `brandA.com` e `brandB.com` domínios. (linha 12)
* `/statfileslevel` A propriedade é definida como 2, para que os arquivos de armazenamento temporário sejam criados em cada diretório que contenha o conteúdo da Web do domínio (linha 41): `/statfileslevel "2"`

Como de costume, a raiz do documento do cache é a mesma do servidor da Web do servidor da Web (linha 40): `/usr/lib/apache/httpd-2.4.3/htdocs`

### `dispatcher.any` {#dispatcher-any}

```xml
/name "testDispatcher"
/farms
  {
  /dispfarm0
    {  
    /clientheaders
      {
      "*"
      }      
    /virtualhosts
      {
      "brandA.com" "brandB.com"
      }
    /renders
      {
      /rend01    {  /hostname "127.0.0.1"   /port "4503"  }
      }
    /filter
      {
      /0001 { /type "deny"  /glob "*" }
      /0023 { /type "allow" /glob "*/content*" }  # disable this rule to allow mapped content only
      /0041 { /type "allow" /glob "* *.css *"   }  # enable css
      /0042 { /type "allow" /glob "* *.gif *"   }  # enable gifs
      /0043 { /type "allow" /glob "* *.ico *"   }  # enable icos
      /0044 { /type "allow" /glob "* *.js *"    }  # enable javascript
      /0045 { /type "allow" /glob "* *.png *"   }  # enable png
      /0046 { /type "allow" /glob "* *.swf *"   }  # enable flash
      /0061 { /type "allow" /glob "POST /content/[.]*.form.html" }  # allow POSTs to form selectors under content
      /0062 { /type "allow" /glob "* /libs/cq/personalization/*"  }  # enable personalization
      /0081 { /type "deny"  /glob "GET *.infinity.json*" }
      /0082 { /type "deny"  /glob "GET *.tidy.json*"     }
      /0083 { /type "deny"  /glob "GET *.sysview.xml*"   }
      /0084 { /type "deny"  /glob "GET *.docview.json*"  }
      /0085 { /type "deny"  /glob "GET *.docview.xml*"  }      
      /0086 { /type "deny"  /glob "GET *.*[0-9].json*" }
      /0090 { /type "deny"  /glob "* *.query.json*" }
      }
    /cache
      {
      /docroot "/usr/lib/apache/httpd-2.4.3/htdocs"
      /statfileslevel "2"
      /allowAuthorized "0"
      /rules
        {
        /0000  { /glob "*"     /type "allow"  }
        }
      /invalidate
        {
        /0000  {   /glob "*" /type "deny"  }
        /0001 {  /glob "*.html" /type "allow"  }
        }
      /allowedClients
        {
        }     
      }
    /statistics
      {
      /categories
        {
        /html  { /glob "*.html" }
        /others  {  /glob "*"  }
        }
      }
    }
  }
```

>[!NOTE]
>
>Como um único farm do Dispatcher é definido, o agente de replicação do Dispatcher Flush na instância de publicação de AEM não requer configurações especiais.

## Regravar links para arquivos não HTML {#rewriting-links-to-non-html-files}

Para regravar referências a arquivos que têm extensões diferentes de.html ou.htm, crie um componente de transformação Sling rewriter e adicione-o ao pipeline de regravação padrão.

Regravar referências quando os caminhos de recurso não forem resolvidos corretamente no contexto do servidor da Web. Por exemplo, um transformador é necessário quando os componentes geram imagens criam links como /content/sitea/en/products.navimage.png. O componente de navegação de [Como criar um site de Internet totalmente destacado](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/the-basics.html) cria tais links.

O redator [Sling](https://sling.apache.org/documentation/bundles/output-rewriting-pipelines-org-apache-sling-rewriter.html) é um módulo que processa a saída de Sling. Implementações de pipeline SAX do redator consistem em um gerador, um ou mais transformadores e um serializador:

* **Generator:** Analisa o fluxo de saída Sling (documento HTML) e gera eventos SAX quando encontra tipos de elementos específicos.
* **Processador:** Escuta eventos do SAX e, consequentemente, modifica o destino do evento (um elemento HTML). Um pipeline de regravação contém zero ou mais transformações. Os transformadores são executados em sequência, passando os eventos SAX para o próximo transformador na sequência.
* **Serializador:** Serializa a saída, incluindo as modificações de cada transformador.

![](assets/chlimage_1-15.png)

### O Pipeline de regravação padrão do AEM {#the-aem-default-rewriter-pipeline}

O AEM usa um rewriter de pipeline padrão que processa documentos do tipo text/html:

* O gerador analisa documentos HTML e gera eventos SAX quando encontra uma área, img, área, formulário, base, script, script e elementos de corpo. O alias do gerador é `htmlparser`.
* O pipeline inclui os seguintes transformadores: `linkchecker``mobile``mobiledebug``contentsync`. O `linkchecker` processador externaliza caminhos em arquivos HTML ou HTM referenciados para evitar links quebrados.
* O serializador grava a saída HTML. O alias serializador é htmlwriter.

O `/libs/cq/config/rewriter/default` nó define o pipeline.

### Criar um Transformer {#creating-a-transformer}

Execute as seguintes tarefas para criar um componente de transformação e usá-lo em um pipeline:

1. Implemente `org.apache.sling.rewriter.TransformerFactory` a interface. Essa classe cria instâncias da sua classe de transformações. Especifique os valores para a `transformer.type` propriedade (o alias do processador) e configure a classe como um componente de serviço osgi.
1. Implemente `org.apache.sling.rewriter.Transformer` a interface. Para minimizar o trabalho, você pode estender a `org.apache.cocoon.xml.sax.AbstractSAXPipe` classe. Substitua o método startelement para personalizar o comportamento de regravação. Esse método é chamado para cada evento SAX que é passado para o transformador.
1. Agrupe e implante as classes.
1. Adicione um nó de configuração ao aplicativo AEM para adicionar o processador ao pipeline.

>[!TIP]
>Em vez disso, você pode configurar transformerfactory para que o transformador seja inserido em cada redator definido. Consequentemente, não é necessário configurar um pipeline:
>
>* Defina `pipeline.mode` a propriedade como `global`.
>* Defina a `service.ranking` propriedade como um número inteiro positivo.
>* Não inclua uma `pipeline.type` propriedade.


>[!NOTE]
>
>Use [o arquule de lynule](https://helpx.adobe.com/experience-manager/aem-previous-versions.html) do Plug-in Package Package Maven para criar seu projeto Maven. Os poms criam e instalam automaticamente um pacote de conteúdo.

Os exemplos a seguir implementam um transformador que regrava referências a arquivos de imagem.

* A classe myrewritertransformerfactory instancia objetos myrewritertransformer. A propriedade pipeline. type define o alias do transformador como mytransformer. Para incluir o alias em um pipeline, o nó de configuração do pipeline inclui esse alias na lista de transformadores.
* A classe myrewritertransformer substitui o método startelement da classe abstractsaxtransformer. O método startelement reescreve o valor de atributos src para elementos img.

Os exemplos não são robustos e não devem ser usados em um ambiente de produção.

### Exemplo de implementação de transformerfactory {#example-transformerfactory-implementation}

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.rewriter.Transformer;
import org.apache.sling.rewriter.TransformerFactory;

@Component
@Service
public class MyRewriterTransformerFactory implements TransformerFactory {
    /* Define the alias */
    @Property(value="mytransformer")
    static final String PIPELINE_TYPE ="pipeline.type";
 
    public Transformer createTransformer() {
        
        return new MyRewriterTransformer ();
    }
}
```

### Exemplo de implementação de transformações {#example-transformer-implementation}

```java
package com.adobe.example;

import java.io.IOException;

import org.apache.cocoon.xml.sax.AbstractSAXPipe;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.rewriter.ProcessingComponentConfiguration;
import org.apache.sling.rewriter.ProcessingContext;
import org.apache.sling.rewriter.Transformer;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.AttributesImpl;

import javax.servlet.http.HttpServletRequest;

public class MyRewriterTransformer extends AbstractSAXPipe implements Transformer {

 private static final Logger log = LoggerFactory.getLogger(MyRewriterTransformer.class);
 private SlingHttpServletRequest httpRequest; 
 /* The element and attribute to act on  */
 private static final String ATT_NAME = new String("src");
 private static final String EL_NAME = new String("img");

 public MyRewriterTransformer () {
 }
 public void dispose() {
 }
 public void init(ProcessingContext context, ProcessingComponentConfiguration config) throws IOException {
  this.httpRequest = context.getRequest();
  log.debug("Transforming request {}.", httpRequest.getRequestURI());
 }
 @Override
 public void startElement (String nsUri, String localname, String qname, Attributes atts) throws SAXException {
  /* copy the element attributes */
  AttributesImpl linkAtts = new AttributesImpl(atts); 
  /* Only interested in EL_NAME elements */
  if(EL_NAME.equalsIgnoreCase(localname)){

   /* iterate through the attributes of the element and act only on ATT_NAME attributes */
   for (int i=0; i < linkAtts.getLength(); i++) {
    if (ATT_NAME.equalsIgnoreCase(linkAtts.getLocalName(i))) {
     String path_in_link = linkAtts.getValue(i);

     /* use the resource resolver of the http request to reverse-resolve the path  */
     String mappedPath = httpRequest.getResourceResolver().map(httpRequest, path_in_link);

     log.info("Tranformed {} to {}.", path_in_link,mappedPath);

     /* update the attribute value */
     linkAtts.setValue(i,mappedPath);
    }
   }

  }
        /* return updated attributes to super and continue with the transformer chain */
 super.startElement(nsUri, localname, qname, linkAtts);
 }
}
```

### Adicionar o Transformer a um pipeline de redator {#adding-the-transformer-to-a-rewriter-pipeline}

Crie um nó JCR que defina um pipeline que usa seu transformador. A definição de nó a seguir cria um pipeline que processa arquivos text/html. O gerador e o analisador padrão de AEM para HTML são usados.

>[!NOTE]
>
>Se você definir a propriedade Transformer `pipeline.mode` como `global`, não precisará configurar um pipeline. O `global` modo insere o transformador em todos os gasodutos.

### Nó de configuração Rewriter - representação XML {#rewriter-configuration-node-xml-representation}

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jcr:root xmlns:jcr="https://www.jcp.org/jcr/1.0" xmlns:nt="https://www.jcp.org/jcr/nt/1.0"
    jcr:primaryType="nt:unstructured"
    contentTypes="[text/html]"
    enabled="{Boolean}true"
    generatorType="htmlparser"
    order="5"
    serializerType="htmlwriter"
    transformerTypes="[mytransformer]">
</jcr:root>
```

O gráfico a seguir mostra a representação CRXDE Lite do nó:

![](assets/chlimage_1-16.png)
