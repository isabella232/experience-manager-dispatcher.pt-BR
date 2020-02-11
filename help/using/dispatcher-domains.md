---
title: 'Utilização do Dispatcher com vários domínios '
seo-title: 'Utilização do Dispatcher com vários domínios '
description: Saiba como usar o Dispatcher para processar solicitações de página em vários domínios da Web.
seo-description: Saiba como usar o Dispatcher para processar solicitações de página em vários domínios da Web.
uuid: 7342a1c2-fe61-49be-a240-b487d53c7ec1
contentOwner: User
cq-exporttemplate: /etc/contentsync/templates/geometrixx/page/rewrite
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 40d91d66-c99b-422d-8e61-c0ced23272ef
translation-type: tm+mt
source-git-commit: 64d26d802dbc9bb0b6815011a16e24c63a7672aa

---


# Utilização do Dispatcher com vários domínios{#using-dispatcher-with-multiple-domains}

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada na documentação do AEM ou CQ.

Use o Dispatcher para processar solicitações de página em vários domínios da Web, além de oferecer suporte às seguintes condições:

* O conteúdo da Web para ambos os domínios é armazenado em um único repositório do AEM.
* Os arquivos no cache do Dispatcher podem ser invalidados separadamente para cada domínio.

Por exemplo, uma empresa publica sites para duas de suas marcas: Marca A e Marca B. O conteúdo das páginas do site é criado no AEM e armazenado na mesma área de trabalho do repositório:

```
/
| - content  
   | - sitea  
       | - content nodes  
   | - siteb  
       | - content nodes
```

As páginas para `BrandA.com` são armazenadas abaixo `/content/sitea`. As solicitações do cliente para o URL `https://BrandA.com/en.html` são retornadas a página renderizada para o `/content/sitea/en` nó. Da mesma forma, as páginas para `BrandB.com` são armazenadas abaixo `/content/siteb`.

Ao usar o Dispatcher para armazenar em cache o conteúdo, as associações devem ser feitas entre o URL da página na solicitação HTTP do cliente, o caminho do arquivo correspondente no cache e o caminho do arquivo correspondente no repositório.

## Solicitações do cliente

Quando os clientes enviam solicitações HTTP para o servidor da Web, o URL da página solicitada deve ser resolvido para o conteúdo no cache do Dispatcher e, eventualmente, para o conteúdo no repositório.

![](assets/chlimage_1-8.png)

1. O sistema de nomes de domínio descobre o endereço IP do servidor Web que está registrado para o nome de domínio na solicitação HTTP.
1. A solicitação HTTP é enviada para o servidor da Web.
1. A solicitação HTTP é transmitida ao Dispatcher.
1. O Dispatcher determina se os arquivos em cache são válidos. Se for válido, os arquivos em cache serão enviados ao cliente.
1. Se os arquivos em cache não forem válidos, o Dispatcher solicitará páginas renderizadas recentemente da instância de publicação do AEM.

## Invalidação de cache

Quando os agentes de replicação do Dispatcher Flush solicitam que o Dispatcher invalie arquivos em cache, o caminho do conteúdo no repositório deve ser resolvido para o conteúdo no cache.

![](assets/chlimage_1-9.png)

1. Uma página é ativada na instância do autor de AEM e o conteúdo é replicado na instância de publicação.
1. O Dispatcher Flush Agent chama o Dispatcher para invalidar o cache do conteúdo replicado.
1. O Dispatcher toca em um ou mais arquivos .stat para invalidar os arquivos em cache.

Para usar o Dispatcher com vários domínios, é necessário configurar o AEM, o Dispatcher e o servidor da Web. As soluções descritas nesta página são gerais e se aplicam à maioria dos ambientes. Devido à complexidade de algumas topologias do AEM, sua solução pode exigir configurações personalizadas adicionais para resolver problemas específicos. Provavelmente, você precisará adaptar os exemplos para atender às políticas existentes de infraestrutura e gerenciamento de TI.

## Mapeamento de URL {#url-mapping}

Para permitir que URLs de domínio e caminhos de conteúdo sejam resolvidos para arquivos em cache, em algum momento do processo um caminho de arquivo ou URL de página deve ser convertido. São fornecidas descrições das seguintes estratégias comuns, onde traduções de caminho ou URL ocorrem em diferentes pontos do processo:

* (Recomendado) A instância de publicação do AEM usa o mapeamento Sling para resolução de recursos para implementar regras internas de regravação de URL. Os URLs de domínio são convertidos em caminhos de repositório de conteúdo. Consulte [AEM regrava URLs](#aem-rewrites-incoming-urls)de entrada.
* O servidor da Web usa regras internas de regravação de URL que traduzem URLs de domínio em caminhos de cache. Consulte [O Servidor Web regrava URLs](#the-web-server-rewrites-incoming-urls)de Entrada.

Geralmente, é desejável usar URLs curtos para páginas da Web. Normalmente, os URLs de página espelham a estrutura das pastas do repositório que contêm o conteúdo da Web. No entanto, os URLs não revelam os principais nós do repositório, como `/content`. O cliente não está necessariamente ciente da estrutura do repositório do AEM.

## Requisitos gerais {#general-requirements}

Seu ambiente deve implementar as seguintes configurações para suportar o Dispatcher trabalhando com vários domínios:

* O conteúdo de cada domínio reside em ramificações separadas do repositório (consulte o ambiente de exemplo abaixo).
* O agente de replicação do Dispatcher Flush está configurado na instância de publicação do AEM. (Consulte [Invalidando o Cache do Dispatcher de uma Instância](page-invalidate.md)de Publicação.)
* O sistema de nomes de domínio resolve os nomes de domínio para o endereço IP do servidor Web.
* O cache do Dispatcher reflete a estrutura de diretório do repositório de conteúdo do AEM. Os caminhos de arquivo abaixo da raiz do documento do servidor da Web são os mesmos que os caminhos dos arquivos no repositório.

## Ambiente para os exemplos fornecidos {#environment-for-the-provided-examples}

As soluções de exemplo fornecidas aplicam-se a um ambiente com as seguintes características:

* As instâncias de autor e publicação do AEM são implantadas em sistemas Linux.
* O Apache HTTPD é o servidor da Web, implantado em um sistema Linux.
* O repositório de conteúdo do AEM e a raiz do documento do servidor Web usam as seguintes estruturas de arquivo (a raiz do documento do servidor Web do Apache é /`usr/lib/apache/httpd-2.4.3/htdocs)`:

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

## O AEM regrava URLs de entrada {#aem-rewrites-incoming-urls}

O mapeamento Sling para resolução de recursos permite associar URLs de entrada a caminhos de conteúdo do AEM. Crie mapeamentos na instância de publicação do AEM para que as solicitações de renderização do Dispatcher sejam resolvidas para o conteúdo correto no repositório.

As solicitações do Dispatcher para renderização de página identificam a página usando a URL transmitida pelo servidor da Web. Quando o URL inclui um nome de domínio, os mapeamentos Sling resolvem o URL para o conteúdo. O gráfico a seguir ilustra um mapeamento do `branda.com/en.html` URL para o `/content/sitea/en` nó.

![](assets/chlimage_1-10.png)

O cache do Dispatcher espelha a estrutura do nó do repositório. Portanto, quando as ativações de página ocorrem, as solicitações resultantes para a inválida da página em cache não exigem traduções de URL ou caminho.

![](assets/chlimage_1-11.png)

## Definir hosts virtuais no servidor da Web {#define-virtual-hosts-on-the-web-server}

Defina hosts virtuais no servidor da Web para que uma raiz de documento diferente possa ser atribuída a cada domínio da Web:

* O servidor da Web deve definir um domínio virtual para cada um dos domínios da Web.
* Para cada domínio, configure a raiz do documento para coincidir com a pasta no repositório que contém o conteúdo da Web do domínio.
* Cada domínio virtual também deve incluir configurações relacionadas ao Dispatcher, conforme descrito na página [Instalação do Dispatcher](dispatcher-install.md) .

O arquivo de exemplo `httpd.conf` a seguir configura dois domínios virtuais para um servidor Web Apache:

* Os nomes de servidor (que coincidem com os nomes de domínio) são branda.com (linha 16) e brandb.com (linha 30).
* A raiz do documento de cada domínio virtual é o diretório no cache do Dispatcher que contém as páginas do site. (linhas 17 e 31)

Com essa configuração, o servidor da Web executa as seguintes ações ao receber uma solicitação de `https://branda.com/en/products.html`:

* Associa o URL ao host virtual que tem um `ServerName` de `branda.com.`

* Encaminha o URL para o Dispatcher.

### httpd.conf {#httpd-conf}

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

Observe que os hosts virtuais herdam o valor da propriedade [DispatcherConfig](dispatcher-install.md#main-pars-67-table-7) configurada na seção do servidor principal. Os hosts virtuais podem incluir sua própria propriedade DispatcherConfig para substituir a configuração do servidor principal.

### Configurar o Dispatcher para lidar com vários domínios {#configure-dispatcher-to-handle-multiple-domains}

Para suportar URLs que incluem nomes de domínio e seus hosts virtuais correspondentes, defina os seguintes farm do Dispatcher:

* Configure um farm do Dispatcher para cada host virtual. Esses farm processam solicitações do servidor da Web para cada domínio, verificam arquivos em cache e solicitam páginas das renderizações.
* Configure um farm do Dispatcher usado para invalidar o conteúdo do cache, independentemente do domínio ao qual o conteúdo pertence. Este farm lida com solicitações de invalidação de arquivo dos agentes de replicação do Dispatcher de Liberação.

### Criar farm do Dispatcher para hosts virtuais

Os Farms para hosts virtuais devem ter as seguintes configurações para que os URLs nas solicitações HTTP do cliente sejam resolvidos para os arquivos corretos no cache do Dispatcher:

* A `/virtualhosts` propriedade é definida como o nome do domínio. Essa propriedade permite que o Dispatcher associe o farm ao domínio.
* A `/filter` propriedade permite o acesso ao caminho do URL da solicitação truncado após a parte do nome do domínio. Por exemplo, para o `https://branda.com/en.html` URL, o caminho é interpretado como `/en.html`, portanto, o filtro deve permitir o acesso a esse caminho.

* A `/docroot` propriedade é definida como o caminho do diretório raiz do conteúdo do site do domínio no cache do Dispatcher. Esse caminho é usado como o prefixo do URL concatenado da solicitação original. Por exemplo, o ponto de `/usr/lib/apache/httpd-2.4.3/htdocs/sitea` faz com que a solicitação `https://branda.com/en.html` seja resolvida para o `/usr/lib/apache/httpd-2.4.3/htdocs/sitea/en.html` arquivo.

Além disso, a instância de publicação do AEM deve ser designada como renderização para o host virtual. Configure outras propriedades do farm, conforme necessário. O código a seguir é uma configuração abreviada de farm para o domínio de branda.com:

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

### Criar um farm do Dispatcher para invalidação de cache

Um farm do Dispatcher é necessário para lidar com solicitações de invalidação de arquivos em cache. Este farm deve poder acessar arquivos .stat nos diretórios docroot de cada host virtual.

As seguintes configurações de propriedade permitem que o Dispatcher resolva arquivos no repositório de conteúdo do AEM a partir de arquivos no cache:

* A `/docroot` propriedade é definida como o ponto padrão do servidor da Web. Normalmente, esse é o diretório onde a `/content` pasta é criada. Um exemplo de valor para Apache no Linux é `/usr/lib/apache/httpd-2.4.3/htdocs`.
* A `/filter` propriedade permite acesso aos arquivos abaixo do `/content` diretório.

A `/statfileslevel`propriedade deve estar alta o suficiente para que os arquivos .stat sejam criados no diretório raiz de cada host virtual. Essa propriedade permite que o cache de cada domínio seja invalidado separadamente. Para a configuração de exemplo, um `/statfileslevel` valor de `2` cria arquivos .stat no `*docroot*/content/sitea` diretório e no `*docroot*/content/siteb` diretório.

Além disso, a instância de publicação deve ser designada como renderização para o host virtual. Configure outras propriedades do farm, conforme necessário. O código a seguir é uma configuração abreviada para o farm que é usado para invalidar o cache:

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

Quando você inicia o servidor Web, o log do Dispatcher (no modo de depuração) indica a inicialização de todos os farm:

```shell
Dispatcher initializing (build 4.1.2)
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_sitea].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/sitea
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_siteb].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs/content/siteb
[Fri Nov 02 16:27:18 2012] [D] [24974(140006182991616)] farms[farm_flush].cache.docroot = /usr/lib/apache/httpd-2.4.3/htdocs
[Fri Nov 02 16:27:18 2012] [I] [24974(140006182991616)] Dispatcher initialized (build 4.1.2)
```

### Configurar mapeamento Sling para resolução de recursos {#configure-sling-mapping-for-resource-resolution}

Use o mapeamento Sling para a resolução de recursos para que URLs baseados em domínio sejam resolvidos para conteúdo na instância de publicação do AEM. O mapeamento de recursos traduz os URLs recebidos do Dispatcher (originalmente de solicitações HTTP do cliente) para nós de conteúdo.

Para saber mais sobre o mapeamento de recursos Sling, consulte [Mapeamentos para Resolução](https://sling.apache.org/site/mappings-for-resource-resolution.html) de Recursos na documentação Sling.

Normalmente, os mapeamentos são necessários para os seguintes recursos, embora seja necessário fazer mapeamentos adicionais:

* O nó raiz da página de conteúdo (abaixo `/content`)
* O nó de design que as páginas usam (abaixo `/etc/designs`)
* A `/libs` pasta

Depois de criar o mapeamento para a página de conteúdo, para descobrir mapeamentos adicionais necessários, use um navegador da Web para abrir uma página no servidor da Web. No arquivo error.log da instância de publicação, localize mensagens sobre recursos que não foram encontrados. A seguinte mensagem de exemplo indica que um mapeamento para `/etc/clientlibs` é obrigatório:

```shell
01.11.2012 15:59:24.601 *INFO* [10.36.34.243 [1351799964599] GET /etc/clientlibs/foundation/jquery.js HTTP/1.1] org.apache.sling.engine.impl.SlingRequestProcessorImpl service: Resource /content/sitea/etc/clientlibs/foundation/jquery.js not found
```

>[!NOTE]
>
>O transformador do vinculador da regravação padrão do Apache Sling modifica automaticamente os hiperlinks na página para evitar links quebrados. No entanto, a regravação do link é executada somente quando o destino do link é um arquivo HTML ou HTM. Para atualizar links para outros tipos de arquivos, crie um componente transformador e adicione-o a um pipeline de regravação HTML.

### Exemplo de nós de mapeamento de recursos

A tabela a seguir lista os nós que implementam o mapeamento de recursos para o domínio de branda.com. Nós semelhantes são criados para o `brandb.com` domínio, como `/etc/map/http/brandb.com`. Em todos os casos, os mapeamentos são necessários quando as referências no HTML da página não são resolvidas corretamente no contexto do Sling.

| Caminho do nó | Tipo | Propriedade |
|--- |--- |--- |
| `/etc/map/http/branda.com` | sling:Mapeamento | Nome: sling:internalRedirect Type: Valor da cadeia de caracteres: /content/site |
| `/etc/map/http/branda.com/libs` | sling:Mapeamento | Nome: sling:internalRedirect <br/>Type: String <br/>Value: /libs |
| `/etc/map/http/branda.com/etc` | sling:Mapeamento |  |
| `/etc/map/http/branda.com/etc/designs` | sling:Mapeamento | Nome: sling:internalRedirect <br/>VType: Valor <br/>da string: /etc/designs |
| `/etc/map/http/branda.com/etc/clientlibs` | sling:Mapeamento | Nome: sling:internalRedirect <br/>VType: Valor <br/>da string: /etc/clientlibs |

## Configuração do agente de replicação Dispatcher Flush {#configuring-the-dispatcher-flush-replication-agent}

O agente de replicação do Dispatcher Flush na instância de publicação do AEM deve enviar solicitações de invalidação para o farm do Dispatcher correto. Para direcionar um farm, use a propriedade URI do agente de replicação Dispatcher Flush (na guia Transporte). Inclua o valor da `/virtualhost` propriedade para o farm do Dispatcher configurado para invalidar o cache:

`https://*webserver_name*:*port*/*virtual_host*/dispatcher/invalidate.cache`

Por exemplo, para usar o `farm_flush` farm do exemplo anterior, o URI é `https://localhost:80/invalidation_only/dispatcher/invalidate.cache`.

![](assets/chlimage_1-12.png)

## O Servidor Web regrava URLs de entrada {#the-web-server-rewrites-incoming-urls}

Use o recurso interno de regravação de URL do servidor Web para traduzir URLs baseados em domínio para caminhos de arquivo no cache do Dispatcher. Por exemplo, as solicitações do cliente para a `https://brandA.com/en.html` página são traduzidas para o `content/sitea/en.html`arquivo na raiz do documento do servidor da Web.

![](assets/chlimage_1-13.png)

O cache do Dispatcher espelha a estrutura do nó do repositório. Portanto, quando as ativações de página ocorrem, as solicitações resultantes para invalidar a página em cache não exigem nenhuma tradução de URL ou caminho.

![](assets/chlimage_1-14.png)

## Definir hosts virtuais e regravar regras no servidor Web {#define-virtual-hosts-and-rewrite-rules-on-the-web-server}

Configure os seguintes aspectos no servidor Web:

* Defina um host virtual para cada domínio da Web.
* Para cada domínio, configure a raiz do documento para coincidir com a pasta no repositório que contém o conteúdo da Web do domínio.
* Para cada domínio virtual, crie uma regra de renomeação de URL que traduza o URL de entrada para o caminho do arquivo em cache.
* Cada domínio virtual também deve incluir configurações relacionadas ao Dispatcher, conforme descrito na página [Instalação do Dispatcher](dispatcher-install.md) .
* O módulo Dispatcher deve ser configurado para usar o URL que o servidor Web regravou. (Consulte a `DispatcherUseProcessedURL` propriedade em [Instalação do Dispatcher](dispatcher-install.md).)

O arquivo httpd.conf de exemplo a seguir configura dois hosts virtuais para um servidor da Web Apache:

* Os nomes de servidor (que coincidem com os nomes de domínio) são `brandA.com` (linha 16) e `brandB.com` (linha 32).

* A raiz do documento de cada domínio virtual é o diretório no cache do Dispatcher que contém as páginas do site. (linhas 20 e 33)
* A regra de regravação de URL para cada domínio virtual é uma expressão regular que prefixa o caminho da página solicitada com o caminho para as páginas no cache. (linhas 19 e 35)
* A `DispatherUseProcessedURL` propriedade está definida como `1`. (linha 10)

Por exemplo, o servidor da Web executa as seguintes ações quando recebe uma solicitação com o `https://brandA.com/en/products.html` URL:

* Associa o URL ao host virtual que tem um `ServerName` de `brandA.com.`
* Substitui o URL a ser `/content/sitea/en/products.html.`
* Encaminha o URL para o Dispatcher.

### httpd.conf {#httpd-conf-1}

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

### Configurar um farm do Dispatcher {#configure-a-dispatcher-farm}

Quando o servidor da Web regrava URLs, o Dispatcher requer um único farm definido de acordo com a [Configuração do Dispatcher](dispatcher-configuration.md). As configurações a seguir são necessárias para suportar os hosts virtuais do servidor da Web e as regras de renomeação de URL:

* A `/virtualhosts` propriedade deve incluir os valores de ServerName para todas as definições de VirtualHost.
* A `/statfileslevel` propriedade deve ser alta o suficiente para criar arquivos .stat nos diretórios que contêm os arquivos de conteúdo para cada domínio.

O arquivo de configuração de exemplo a seguir baseia-se no arquivo de exemplo `dispatcher.any` instalado com o Dispatcher. As seguintes alterações são necessárias para suportar as configurações do servidor da Web do arquivo anterior: `httpd.conf`

* A `/virtualhosts` propriedade faz com que o Dispatcher manipule solicitações para os domínios `brandA.com` e `brandB.com` . (linha 12)
* A `/statfileslevel` propriedade é definida como 2, de modo que os arquivos de estatística sejam criados em cada diretório que contenha o conteúdo da Web do domínio (linha 41): `/statfileslevel "2"`

Como de costume, a raiz do documento do cache é a mesma da raiz do documento do servidor da Web (linha 40): `/usr/lib/apache/httpd-2.4.3/htdocs`

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
>Como um único farm do Dispatcher está definido, o agente de replicação do Dispatcher Flush na instância de publicação do AEM não requer configurações especiais.

## Regravação de links para arquivos não HTML {#rewriting-links-to-non-html-files}

Para regravar referências a arquivos que tenham extensões diferentes de .html ou .htm, crie um componente transformador Sling rewriter e adicione-o ao pipeline padrão de regravação.

Substitua as referências quando os caminhos de recursos não forem resolvidos corretamente no contexto do servidor da Web. Por exemplo, um transformador é necessário quando componentes geradores de imagem criam links como /content/sitea/en/products.navimage.png. O componente de navegação superior do site [Como criar um site](https://helpx.adobe.com/experience-manager/6-3/sites/developing/using/the-basics.html) da Internet totalmente em destaque cria esses links.

O [Sling Rewriter](https://sling.apache.org/documentation/bundles/output-rewriting-pipelines-org-apache-sling-rewriter.html) é um módulo que pós-processa a saída Sling. As implementações de pipeline SAX da regravadora consistem em um gerador, um ou mais transformadores e um serializador:

* **** Gerador: Analisa o fluxo de saída Sling (documento HTML) e gera eventos SAX quando encontra tipos de elementos específicos.
* **** Transformador: Escuta eventos SAX e consequentemente modifica o destino do evento (um elemento HTML). Um pipeline de regravação contém zero ou mais transformadores. Transformadores são executados em sequência, passando os eventos SAX para o transformador seguinte na sequência.
* **** Serializador: Serializa a saída, incluindo as modificações de cada transformador.

![](assets/chlimage_1-15.png)

### O pipeline de regravação padrão do AEM {#the-aem-default-rewriter-pipeline}

O AEM usa uma regravação de pipeline padrão que processa documentos do tipo text/html:

* O gerador analisa documentos HTML e gera eventos SAX quando encontra elementos a, img, area, form, base, link, script e body. O alias do gerador é `htmlparser`.
* O pipeline inclui os seguintes transformadores: `linkchecker`, `mobile`, `mobiledebug`, `contentsync`. O `linkchecker` transformador externaliza caminhos para arquivos HTML ou HTM referenciados para evitar links quebrados.
* O serializador grava a saída HTML. O alias do serializador é htmlwriter.

O `/libs/cq/config/rewriter/default` nó define o pipeline.

### Criando um Transformador {#creating-a-transformer}

Execute as seguintes tarefas para criar um componente transformador e usá-lo em um pipeline:

1. Implemente a `org.apache.sling.rewriter.TransformerFactory` interface. Essa classe cria instâncias da sua classe de transformadores. Especifique valores para a `transformer.type` propriedade (o alias do transformador) e configure a classe como um componente de serviço OSGi.
1. Implemente a `org.apache.sling.rewriter.Transformer` interface. Para minimizar o trabalho, você pode estender a `org.apache.cocoon.xml.sax.AbstractSAXPipe` classe. Substitua o método startElement para personalizar o comportamento de regravação. Esse método é chamado para cada evento SAX transmitido ao transformador.
1. Agrupe e implante as classes.
1. Adicione um nó de configuração ao aplicativo AEM para adicionar o transformador ao pipeline.

>[!TIP]
>Você pode configurar o TransformerFactory para que o transformador seja inserido em cada regravador definido. Consequentemente, não é necessário configurar um pipeline:
>
>* Defina a `pipeline.mode` propriedade como `global`.
>* Defina a `service.ranking` propriedade como um número inteiro positivo.
>* Não inclua uma `pipeline.type` propriedade.


>[!NOTE]
>
>Use o arquétipo de [vários módulos](https://helpx.adobe.com/experience-manager/aem-previous-versions.html) do Plug-in Content Package Maven para criar seu projeto Maven. Os POMs criam e instalam automaticamente um pacote de conteúdo.

Os exemplos a seguir implementam um transformador que regrava referências a arquivos de imagem.

* A classe MyRewriterTransformerFactory instancia objetos MyRewriterTransformer. A propriedade pipeline.type define o alias do transformador como mytransformador. Para incluir o alias em um pipeline, o nó de configuração do pipeline inclui esse alias na lista de transformadores.
* A classe MyRewriterTransformer substitui o método startElement da classe AbstractSAXTransformer. O método startElement regrava o valor dos atributos src para elementos img.

Os exemplos não são robustos e não devem ser utilizados num ambiente de produção.

### Exemplo de implementação do TransformerFactory {#example-transformerfactory-implementation}

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

### Exemplo de implementação de Transformador {#example-transformer-implementation}

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

### Adicionando o Transformador a um Pipeline de Rewriter {#adding-the-transformer-to-a-rewriter-pipeline}

Crie um nó JCR que defina um pipeline que use seu transformador. A definição de nó a seguir cria um pipeline que processa arquivos text/html. O gerador e o analisador de AEM padrão para HTML são usados.

>[!NOTE]
>
>Se você definir a propriedade Transformer `pipeline.mode` como `global`, não será necessário configurar um pipeline. O `global` modo insere o transformador em todos os pipelines.

### Nó de configuração do redator - representação XML {#rewriter-configuration-node-xml-representation}

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
