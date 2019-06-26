---
title: Instalação do Dispatcher
seo-title: Instalação do AEM Dispatcher
description: Saiba como instalar o módulo Dispatcher no Microsoft Internet Information Server, Apache Web Server e Sun Java Web Server-iplanet.
seo-description: Saiba como instalar o módulo AEM Dispatcher no Microsoft Internet Information Server, Apache Web Server e Sun Java Web Server-iplanet.
uuid: 2384 b 907-1042-4707-b 02 f-fba 2125618 cf
contentOwner: Usuário
converted: verdadeiro
topic-tags: dispatcher
content-type: referência
discoiquuid: f 00 ad 751-6 b 95-4365-8500-e 1 e 0108 d 9536
translation-type: tm+mt
source-git-commit: 6d3ff696780ce55c077a1d14d01efeaebcb8db28

---


# Installing Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

Use the [Dispatcher Release Notes](release-notes.md) page to obtain the latest Dispatcher installation file for your operating system and web server. Os números de versão do Dispatcher são independentes dos números de lançamento do Adobe Experience Manager e são compatíveis com as versões do Adobe Experience Manager 6. x, 5. x e Adobe CQ 5. x.

A seguinte convenção de nomenclatura de arquivo é usada:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

For example, the `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` file contains Dispatcher version 4.3.1 for an Apache 2.4 web server that runs on Linux i686 and is packaged using the **tar** format.

A tabela a seguir lista o identificador do servidor da Web usado em nomes de arquivo para cada servidor Web:

| Servidor Web | Kit de instalação |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;other parameters&gt; |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**iis**-&lt;other parameters&gt; |
| Sun Java Web Server iplanet | dispatcher-**ns**-&lt;other parameters&gt; |

>[!NOTE]
>
>Você deve instalar a versão mais recente do Dispatcher disponível para a sua plataforma. Anualmente, você deve atualizar a instância do Dispatcher para usar a versão mais recente para aproveitar as melhorias do produto.

Cada arquivo contém os seguintes arquivos:

* os módulos do Dispatcher
* um arquivo de configuração exemplo
* o arquivo README que contém instruções de instalação e informações de último minuto
* o arquivo de alterações que lista problemas corrigidos nas versões atuais e anteriores

>[!NOTE]
>
>Verifique o arquivo README para quaisquer notas de última hora/plataforma específicas antes de iniciar a instalação.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific web server installation procedures.</p>

 -->

## Microsoft Internet Information Server {#microsoft-internet-information-server}

Para obter informações sobre como instalar esse servidor da Web, consulte os seguintes recursos:

* Documentação própria da Microsoft sobre o Servidor de informações da Internet
* [&quot; O site oficial do Microsoft IIS &quot;](https://www.iis.net/)

### Required IIS Components {#required-iis-components}

As versões 8.5 e 10 do IIS exigem que os seguintes componentes do IIS sejam instalados:

* Extensões de ISAPI

Além disso, você deve adicionar a função Servidor da Web (IIS). Use o Gerenciador de servidor para adicionar a função e os componentes.

## Microsoft IIS - Installing the Dispatcher module {#microsoft-iis-installing-the-dispatcher-module}

O arquivo necessário para o Microsoft Internet Information System é:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

O arquivo ZIP contém os seguintes arquivos:

| Arquivo | Descrição |
|--- |--- |
| `disp_iis.dll` | O arquivo da biblioteca do link dinâmico do Dispatcher. |
| `disp_iis.ini` | Arquivo de configuração para IIS. Este exemplo pode ser atualizado com seus requisitos. **Observação**: O arquivo ini deve ter o mesmo nome do nome da dll. |
| `dispatcher.any` | Um arquivo de configuração de exemplo para o Dispatcher. |
| `author_dispatcher.any` | Um arquivo de configuração de exemplo para o Dispatcher que trabalha com a instância do autor. |
| README | Arquivo leiame que contém instruções de instalação e informações de último minuto. **Observação**: Verifique esse arquivo antes de iniciar a instalação. |
| CHANGES | Altera o arquivo que lista problemas corrigidos nas versões atuais e anteriores. |

Use o procedimento a seguir para copiar os arquivos do Dispatcher para o local correto.

1. Use Windows Explorer to create the `<IIS_INSTALLDIR>/Scripts` directory, for example, `C:\inetpub\Scripts`.

1. Extraia os seguintes arquivos do pacote do Dispatcher para este diretório Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Um dos seguintes arquivos, dependendo se o Dispatcher está trabalhando com uma instância de autor ou instância de autor de AEM:
      * Author instance: `author_dispatcher.any`
      * Publish instance: `dispatcher.any`

## Microsoft IIS - Configure the Dispatcher INI File {#microsoft-iis-configure-the-dispatcher-ini-file}

Edit the `disp_iis.ini` file to configure the Dispatcher installation. The basic format of the `.ini` file is as follows:

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

A tabela a seguir descreve cada propriedade.

| Parâmetro | Descrição |
|--- |--- |
| configurar caminho | The location of `dispatcher.any` within the local file system (absolute path). |
| arquivo de log | The location of the `dispatcher.log` file. Se isso não for definido, registre as mensagens para o log do evento do Windows. |
| nível de logon | Define o Nível de log usado para gerar mensagens para o log do evento. The following values may be specified:Log level for the log file: <br/>0 - error messages only. <br/>1 - erros e avisos. <br/>2 - erros, avisos e mensagens informativas <br/>3 - erros, avisos, mensagens informativas e de depuração. <br/>**Observação**: Recomenda-se definir o nível de log como 3 durante a instalação e o teste, em seguida, para 0 quando executados em um ambiente de produção. |
| replaceauthorization | Especifica como os cabeçalhos de autorização na solicitação HTTP são manipulados. The following values are valid:<br/>0 - Authorization headers are not modified. <br/>1 - Substitui qualquer cabeçalho chamado &quot;Autorização&quot; diferente de &quot;Básico&quot; por seu `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
| servervariables | Define como as variáveis do servidor são processadas.<br/>0 - As variáveis do IIS são enviadas para o Dispatcher e AEM. <br/>1 - todas as variáveis do IIS Server (como `LOGON\_USER, QUERY\_STRING, ...`) são enviadas para o Dispatcher, juntamente com os cabeçalhos de solicitação (e também para a instância do AEM, se não estiverem em cache). <br/>As variáveis do servidor incluem `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` e muitas outras. Consulte a documentação do IIS para obter a lista completa de variáveis, com detalhes. |
| enable_ chunked_ transfer | Define se deseja ativar (1) ou desativar (0) a transferência vinculada para a resposta do cliente. O valor padrão é 0. |

Uma configuração de exemplo:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configuring Microsoft IIS {#configuring-microsoft-iis}

Configure o IIS para integrar o módulo do Dispatcher ISAPI. No IIS, você usa o mapeamento do aplicativo curinga.

### Configuring Anonymous Access - IIS 8.5 and 10 {#configuring-anonymous-access-iis-and}

O agente de replicação padrão do Flush na instância do Autor é configurado para que não envie credenciais de segurança com solicitações de flush. Portanto, o site que você está usando em um cache do Dispatcher deve permitir acesso anônimo.

Se o site usa um método de autenticação, o agente de replicação do Flush deve ser configurado adequadamente.

1. Abra o IIS Manager e selecione o site que você está usando como cache do Disptcher.
1. Usando o modo de Exibição de recursos, na seção IIS clique duas vezes em Autenticação.
1. Se a Autenticação anônima não estiver ativada, selecione Autenticação anônima e, na área Ações, clique em Ativar.

### Integrating the Dispatcher ISAPI Module - IIS 8.5 and 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Use o procedimento a seguir para adicionar o Módulo ISAPI do Dispatcher ao IIS.

1. Abra o IIS Manager.
1. Selecione o site que você está usando como o Cache do Dispatcher.
1. Usando o modo de Exibição de recursos, na seção IIS, clique duas vezes em Mapeamentos do manipulador.
1. No painel Ações da página Mapeamentos do manipulador, clique em Adicionar mapa de scripts curinga, adicione os seguintes valores de propriedade e clique em OK:

   * Caminho de solicitação: *
   * Executable: The absolute path of the disp_iis.dll file, for example `C:\inetpub\Scripts\disp_iis.dll`.
   * Name: A descriptive name for the handler mapping, for example `Dispatcher`.

1. Na caixa de diálogo exibida, para adicionar a biblioteca disp_ iis. dll à lista de Restrições de ISAPI e CGI, clique em Sim.

   Para IIS 7.0 e 7.5, a configuração é concluída. Continue com as etapas restantes se estiver configurando o IIS 8.0.

1. (IIS 8.0) Na lista de mapeamentos do manipulador, selecione aquele que acabou de criar e, na área Ações, clique em Editar.
1. (IIS 8.0) Na caixa de diálogo Editar mapa de script, clique no botão Restrições de solicitação.
1. (IIS 8.0) Para garantir que o manipulador seja usado para arquivos e pastas que ainda não estão em cache, desmarque Invocar somente manipulador se a Solicitação estiver vinculada e clique em OK.
1. (IIS 8.0) Na caixa de diálogo Editar Mapa de script, clique em OK.

### Configuring Access to the Cache - IIS 8.5 and 10 {#configuring-access-to-the-cache-iis-and}

Forneça o usuário padrão do Pool de aplicativos com acesso de gravação à pasta que está sendo usada como cache do Dispatcher.

1. Right-click the root folder of the website that you are using as the Dispatcher cache and click Properties, such as `C:\inetpub\wwwroot`.
1. Na guia Segurança, clique em Editar e, na caixa de diálogo Permissões, clique em Adicionar. Uma caixa de diálogo é aberta para selecionar contas de usuário. Clique no botão Locais, selecione o nome do computador e clique em OK.

   Mantenha essa caixa de diálogo aberta enquanto você concluir a próxima etapa.

1. no IIS Manager, selecione o site IIS que você está usando para o cache do Dispatcher e, no lado direito da janela, clique em Configurações avançadas.
1. Selecione o valor da propriedade Pool de aplicativos e copie-o para a área de transferência.
1. Retorne à caixa de diálogo aberta. In the Enter The Object Names To Select box, type `IIS AppPool\` and then paste the contents of your clipboard. O valor deve ser semelhante ao seguinte exemplo:

   `IIS AppPool\DefaultAppPool`

1. Clique no botão Verificar nomes. Quando o Windows resolver a conta de usuário, clique em OK.
1. In the Permissions dialog box for the dispatcher folder, select the account that you just added, enable all of the permissions for the account  **except for Full Control** and click OK. Clique em OK para fechar a caixa de diálogo Propriedades da pasta.

### Registering the JSON Mime Type - IIS 8.5 and 10 {#registering-the-json-mime-type-iis-and}

Use o procedimento a seguir para registrar o tipo JSON MIME, quando quiser que o Dispatcher permita chamadas JSON.

1. No IIS Manager, selecione o site e usando a Exibição de recursos, clique duas vezes em Tipos Mime.
1. Se a extensão JSON não estiver na lista, no painel Ações, clique em Adicionar, insira os seguintes valores de propriedade e clique em OK:

   * File Name Extension: `.json`
   * MIME Type: `application/json`

### Removing the bin Hidden Segment - IIS 8.5 and 10 {#removing-the-bin-hidden-segment-iis-and}

Use the following procedure to remove the `bin` hidden segment. Sites que não são novos podem conter esse segmento oculto.

1. No IIS Manager, selecione o site e usando a Exibição de recursos, clique duas vezes em Solicitar filtragem.
1. Select the `bin` segment, click Remove, and in the confirmation dialog box click Yes.

### Logging IIS Messages to a File - IIS 8.5 and 10 {#logging-iis-messages-to-a-file-iis-and}

Use o procedimento a seguir para gravar mensagens de log do Dispatcher em um arquivo de log em vez do log do evento do Windows. É necessário configurar o Dispatcher para usar o arquivo de log e fornecer o IIS com acesso de gravação ao arquivo.

1. Use Windows Explorer to create a folder named `dispatcher` below the logs folder of the IIS installation. The path of this folder for a typical installation is `C:\inetpub\logs\dispatcher`.

1. Clique com o botão direito do mouse na pasta do expedidor e clique em Propriedades.
1. Na guia Segurança, clique em Editar e, na caixa de diálogo Permissões, clique em Adicionar. Uma caixa de diálogo é aberta para selecionar contas de usuário. Clique no botão Locais, selecione o nome do computador e clique em OK.

   Mantenha essa caixa de diálogo aberta enquanto você concluir a próxima etapa.

1. no IIS Manager, selecione o site IIS que você está usando para o cache do Dispatcher e, no lado direito da janela, clique em Configurações avançadas.
1. Selecione o valor da propriedade Pool de aplicativos e copie-o para a área de transferência.
1. Retorne à caixa de diálogo aberta. In the Enter The Object Names To Select box, type `IIS AppPool\` and then paste the contents of your clipboard. O valor deve ser semelhante ao seguinte exemplo:

   `IIS AppPool\DefaultAppPool`

1. Clique no botão Verificar nomes. Quando o Windows resolver a conta de usuário, clique em OK.
1. Na caixa de diálogo Permissões da pasta do expedidor, selecione a conta que acabou de adicionar, ative todas as permissões da conta** exceto para Controle total,** e clique em OK. Clique em OK para fechar a caixa de diálogo Propriedades da pasta.
1. Use a text editor to open the `disp_iis.ini` file.
1. Adicione uma linha de texto semelhante ao seguinte exemplo para configurar o local do arquivo de log e salve o arquivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Next Steps {#next-steps}

Antes de começar a usar o Dispatcher, você deve saber:

* [Configurar o Dispatcher](dispatcher-configuration.md)
* [Configure o AEM](page-invalidate.md) para trabalhar com o Dispatcher.

## Apache Web Server {#apache-web-server}

>[!CAUTION]
>
>Instructions for installation under both **Windows** and **Unix** are covered here. Tenha cuidado ao executar as etapas.

### Installing Apache Web Server {#installing-apache-web-server}

For Information about how to install an Apache Web Server read the installation manual - either [online](https://httpd.apache.org/) or in the distribution.

>[!CAUTION]
>
>If you are creating an Apache binary by compiling the source files, make sure that you turn on **dynamic modules support**. This can be done by using any of the **--enable-shared** options. At a minimum include the `mod_so` module.
>
>Mais informações podem ser encontradas no manual de instalação do Apache Web Server.

Also see the Apache HTTP Server [Security Tips](https://httpd.apache.org/docs/2.4/misc/security_tips.html) and [Security Reports](https://httpd.apache.org/security_report.html).

### Apache Web Server - Add the Dispatcher Module {#apache-web-server-add-the-dispatcher-module}

O Dispatcher vem como:

* **Windows**: uma Biblioteca de links dinâmicos (DLL)
* **Unix**: um DSO (Objeto compartilhado dinâmico)

Os arquivos de arquivo de instalação contêm os seguintes arquivos - dependendo se você selecionou Windows ou Unix:

| Arquivo | Descrição |
|--- |--- |
| disp_ apache &lt; x. y &gt;. dll | Windows: O arquivo da biblioteca do link dinâmico do Dispatcher. |
| dispatcher-apache &lt; x. y &gt;-&lt; rel-nr &gt;. so | Unix: O arquivo da biblioteca de objetos compartilhados do Dispatcher. |
| mod_ dispatcher. so | Unix: Um link de exemplo. |
| http. conf. disp &lt; x &gt; | Um arquivo de configuração de exemplo para o servidor Apache. |
| dispatcher. any | Um arquivo de configuração de exemplo para o Dispatcher. |
| README | Arquivo leiame que contém instruções de instalação e informações de último minuto. **Observação**: Verifique esse arquivo antes de iniciar a instalação. |
| CHANGES | Altera o arquivo que lista problemas corrigidos nas versões atuais e anteriores. |

Use as seguintes etapas para adicionar o Dispatcher ao seu Apache Web Server:

1. Coloque o arquivo do Dispatcher no diretório apropriado do módulo Apache:

   * **Windows**: Local `disp_apache<x.y>.dll``<APACHE_ROOT>/modules`
   * **Unix**: Localize o diretório `<APACHE_ROOT>/libexec` ou `<APACHE_ROOT>/modules`diretório de acordo com sua instalação.\
      Copy `dispatcher-apache<options>.so` into this directory.\
      To simplify long-term maintenance you can also create a symbolic link named `mod_dispatcher.so` to the Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copy the dispatcher.any file to the `<APACHE_ROOT>/conf` directory.

   **Observação:** É possível colocar esse arquivo em um local diferente, desde que a propriedade dispatcherlog do módulo Dispatcher esteja configurada adequadamente. (Consulte Entradas de configuração específicas do Dispatcher abaixo.)

### Apache Web Server - Configure SELinux Properties {#apache-web-server-configure-selinux-properties}

Se você estiver executando o Dispatcher no rehat Linux Kernel 2.6 com o selinux ativado, você pode colocar mensagens de erro como isso no arquivo de log do expedidor.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Isso pode ser devido a uma segurança de selinux ativada. Em seguida, você precisa executar as seguintes tarefas:

* Configure o contexto selinux do arquivo do módulo do expedidor.
* Ative scripts e módulos HTTPD para fazer conexões de rede.
* Configure o contexto selinux do docroot, onde os arquivos em cache são armazenados.

Enter the following commands in a terminal window, replacing `[path to the dispatcher.so file]` with the path to the Dispatcher module that you installed to Apache Web Server, and *`path to the docroot`* with the path where the docroot is located (e.g. `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web Server - Configure Apache Web Server for Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

The Apache Web Server needs to be configured, using `httpd.conf`. In the Dispatcher installation kit you will find an example configuration file named `httpd.conf.disp<x>`.

Essas etapas são obrigatórias:

1. Vá até `<APACHE_ROOT>/conf`.
1. Open `httpd.conf`for editing.
1. As seguintes entradas de configuração devem ser adicionadas, na ordem listada:

   * **Loadmodule** para carregar o módulo ao iniciar.
   * Dispatcher-specific configuration entries, including **DispatcherConfig, DispatcherLog** and **DispatcherLogLevel**.
   * **Sethandler** para ativar o Dispatcher. **Loadmodule**.
   * **Modmimeusepathinfo** para configurar o comportamento **de mod_ mime**.

1. (Opcional) Recomenda-se alterar o proprietário do diretório htdocs:

   * O servidor do apache começa como raiz, embora os processos filho iniciem como daemon (para fins de segurança). The DocumentRoot (`<APACHE_ROOT>/htdocs`) must belong to the user daemon:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**Loadmodule**

A tabela a seguir lista exemplos que podem ser usados; as entradas exatas são de acordo com seu Apache Web Server específico:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (pressupondo link simbólico) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>O primeiro parâmetro de cada declaração deve ser escrito exatamente como nos exemplos acima.
>
>Consulte os arquivos de configuração de exemplo fornecidos e a documentação do Apache Web Server para obter detalhes completos sobre esse comando.

**Entradas de configuração específicas do Dispatcher**

As entradas de configuração específicas do Dispatcher são colocadas após a entrada loadmodule. A tabela a seguir lista uma configuração de exemplo aplicável para Unix e Windows:

**Windows &amp; Unix**

```
...
<fModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

Os parâmetros de configuração individuais:

| Parâmetro | Descrição |
|--- |--- |
| Dispatcherconfig | Localização e nome do arquivo de configuração do Dispatcher. <br/>Quando essa propriedade está localizada na configuração do servidor principal, todos os hosts virtuais herdam o valor da propriedade. No entanto, os hosts virtuais podem incluir uma propriedade dispatcherconfig para substituir a configuração do servidor principal. |
| Dispatcherlog | Local e nome do arquivo de log. |
| Dispatcherloglevel | Log level for the log file: <br/>0 - Errors <br/>1 - Warnings <br/>2 - Infos <br/>3 - Debug <br/>**Note**: It is recommended to set the log level to 3 during installation and testing, then to 0 when running in a production environment. |
| Dispatpaintserverheader | *Esse parâmetro está obsoleto e não tem mais efeito.*<br/><br/> Define o cabeçalho do servidor a ser usado: <br/><ul><li>undefined ou 0 - o cabeçalho do servidor HTTP contém a versão do AEM. </li><li>1 - o cabeçalho do servidor Apache é usado.</li></ul> |
| Dispatcherdeclineroot | Defines whether to decline requests to the root &quot;/&quot;: <br/>**0** - accept requests to / <br/>**1** - requests to / are not handled by the dispatcher; use mod_alias for the correct mapping. |
| Dispatcheruseprocessedurl | Defines whether to use pre-processed URLs for all further processing by Dispatcher: <br/>**0** - use the original URL passed to the web server. <br/>**1** - o dispatcher usa o URL processado pelos manipuladores que antecedem o dispatcher (isto é `mod_rewrite`,) em vez do URL original passado para o servidor da Web. Por exemplo, o URL original ou processado é correspondido com os filtros do Dispatcher. O URL também é usado como a base para a estrutura do arquivo cache. Consulte a documentação do site do Apache para obter informações sobre mod_ rewrite; por exemplo, Apache 2.4. Ao usar mod_ rewrite, é aconselhável usar a senha do sinalizador | PT &#39; (passagem para o próximo manipulador) para forçar o mecanismo de regravação a definir o campo uri da estrutura interna request_ rec para o valor do campo de nome de arquivo. |
| Dispatcherpasserror | Defines how to support error codes for ErrorDocument handling: <br/>**0** - Dispatcher spools all error responses to the client. <br/>**1** - O Dispatcher não coloca uma resposta de erro no cliente (onde o código de status é maior ou igual a 400), mas passa o código de status para o Apache, que permite que uma diretiva errordocument processe tal código de status. <br/>**Intervalo** de código - Especifique uma gama de códigos de erro para os quais a resposta é transmitida ao Apache. Outros códigos de erro são transmitidos para o cliente. Por exemplo, a configuração a seguir envia respostas do erro 412 para o cliente, e todos os outros erros são passados para o Apache: Dispatcherpasserror 400-411,413-417 |
| Dispatcherkeepalivetimeout | Especifica o tempo limite de manutenção, em segundos. A partir do Dispatcher versão 4.2.0, o valor de manutenção padrão é 60. Um valor de 0 desativa manutenção. |
| Dispatpaintcanonurl | Definir esse parâmetro como Ativado transmite o URL bruto para o backend em vez do canonicalizado e substituirá as configurações de dispatcheruseprocessedurl. O valor padrão é Desligado. <br/>**Observação**: As regras de filtro na configuração do Dispatcher sempre serão avaliadas em relação à URL sanitizada não o URL bruto. |




>[!NOTE]
>
>Entradas de caminho são relativas ao diretório raiz do Apache Web Server.

>[!NOTE]
>
>The default settings for the Server Header are: `  
ServerTokens Full` `  
DispatcherNoServerHeader 0`\
O que mostra a versão do AEM (para fins estatísticos). If you want to disable such information being available in the header you can set: `  
ServerTokens Prod`\
See the [Apache Documentation about ServerTokens Directive (for example, for Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) for more information.

**Sethandler**

After these entries you must add a **SetHandler** statement to the context of your configuration ( `<Directory>`, `<Location>`) for the Dispatcher to handle the incoming requests. O exemplo a seguir configura o Dispatcher para lidar com solicitações do site completo:

**Windows e Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

O exemplo a seguir configura o Dispatcher para lidar com solicitações de um domínio virtual:

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**Unix**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
The parameter of the **SetHandler** statement must be written *exactly as in the above examples*, as this is the name of the handler defined in the module.
Consulte os arquivos de configuração de exemplo fornecidos e a documentação do Apache Web Server para obter detalhes completos sobre esse comando.

**Modmimeusepathinfo**

After the **SetHandler** statement you should also add the **ModMimeUsePathInfo** definition.

>[!NOTE]
The `ModMimeUsePathInfo` parameter should only be used and configured if you are using Dispatcher version 4.0.9, or higher.
(Observe que o Dispatcher versão 4.0.9 foi lançado em 2011. Se estiver usando uma versão mais antiga, a atualização para uma versão recente do Dispatcher seria apropriada).

The **ModMimeUsePathInfo** parameter should be set `On` for all Apache configurations:

`ModMimeUsePathInfo On`

The mod_mime module (see for example, [Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) is used to assign content metadata to the content selected for an HTTP response. A configuração padrão significa que quando mod_ mime determina o tipo de conteúdo, apenas a parte do URL que mapeia para um arquivo ou diretório será considerada.

When `On`, the `ModMimeUsePathInfo` parameter specifies that `mod_mime` is to determine the content type based on the *complete* URL; this means that virtual resources will have metainformation applied based on their extension.

The following example activates **ModMimeUsePathInfo**:

**Windows e Unix**

```
...  
<Directory />  
<IfModule disp\_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Enable Support for HTTPS (Unix and Linux) {#enable-support-for-https-unix-and-linux}

O Dispatcher usa o openssl para implementar comunicações seguras em HTTP. Starting from Dispatcher version **4.2.0**, OpenSSL 1.0.0 and OpenSSL 1.0.1 are supported. O Dispatcher usa o openssl 1.0.0 por padrão. Para usar o openssl 1.0.1, use o seguinte procedimento para criar links simbólicos para que o Dispatcher use as bibliotecas openssl instaladas.

1. Abra um terminal e altere o diretório atual para o diretório onde as bibliotecas openssl são instaladas, por exemplo:

   ```shell
   cd /usr/lib64
   ```

1. Para criar os links simbólicos, insira os seguintes comandos:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
If you are using a customized version of Apache, make sure Apache and Dispatcher are compiled using the same version of [OpenSSL](https://www.openssl.org/source/).

### Next Steps {#next-steps-1}

Antes de começar a usar o Dispatcher, você deve:

* [Configurar o Dispatcher](dispatcher-configuration.md)
* [Configure o AEM](page-invalidate.md) para trabalhar com o Dispatcher.

## Sun Java System Web Server / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
As instruções para ambientes do Windows e Unix são abordadas aqui.
Tenha cuidado ao selecionar o que deve ser executado.

### Sun Java System Web Server / iPlanet - Installing your Web Server {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obter informações completas sobre como instalar esses servidores da Web, consulte a respectiva documentação:

* Sun Java System Web Server
* Iplanet Web Server

### Sun Java System Web Server / iPlanet - Add the Dispatcher Module {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

O Dispatcher vem como:

* **Windows**: uma Biblioteca de links dinâmicos (DLL)
* **Unix**: um DSO (Objeto compartilhado dinâmico)

Os arquivos de arquivo de instalação contêm os seguintes arquivos - dependendo se você selecionou Windows ou Unix:

| Arquivo | Descrição |
|---|---|
| `disp_ns.dll` | Windows: O arquivo da biblioteca do link dinâmico do Dispatcher. |
| `dispatcher.so` | Unix: O arquivo da biblioteca de objetos compartilhados do Dispatcher. |
| `dispatcher.so` | Unix: Um link de exemplo. |
| `obj.conf.disp` | Um arquivo de configuração de exemplo para o servidor Web iplanet/Sun Java System. |
| `dispatcher.any` | Um arquivo de configuração de exemplo para o Dispatcher. |
| README | Arquivo leiame que contém instruções de instalação e informações de último minuto. Observação: Verifique esse arquivo antes de iniciar a instalação. |
| CHANGES | Altera o arquivo que lista problemas corrigidos nas versões atuais e anteriores. |

Use as seguintes etapas para adicionar o Dispatcher ao servidor Web:

1. Place the Dispatcher file in the web server&#39;s `plugin` directory:

### Sun Java System Web Server / iPlanet - Configure for the Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

The web server needs to be configured, using `obj.conf`. In the Dispatcher installation kit you will find an example configuration file named `obj.conf.disp`.

1. Vá até `<WEBSERVER_ROOT>/config`.
1. Open `obj.conf`for editing.
1. Copie a linha que começa:\
   `Service fn="dispService"`\
   from `obj.conf.disp` to the initialization section of `obj.conf`.

1. Salve as alterações.
1. Open `magnus.conf` for editing.
1. Copie as duas linhas que iniciam:\
   `Init funcs="dispService, dispInit"`\
   e\
   `Init fn="dispInit"`\
   from `obj.conf.disp` to the initialization section of `magnus.conf`.

1. Salve as alterações.

>[!NOTE]
The following configurations should all be on one line and the `$(SERVER_ROOT)` and `$(PRODUCT_SUBDIR)` must be replaced by the respective values.

**Init**

A tabela a seguir lista exemplos que podem ser usados; as entradas exatas são de acordo com seu servidor Web específico:

**Windows e Unix**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

em que:

| Parâmetro | Descrição |
|--- |--- |
| config | Location and name of the configuration file `dispatcher.any.` |
| arquivo de log | Local e nome do arquivo de log. |
| nível de logon | Log level for when writing messages to the log file: <br/>**0** Errors <br/>**1** Warnings <br/>**2** Infos <br/>**3** Debug <br/>**Note:** It is recommended to set the log level to 3 during installation and testing and to 0 when running in a production environment. |
| keepalivetimeout | Especifica o tempo limite de manutenção, em segundos. A partir do Dispatcher versão 4.2.0, o valor de manutenção padrão é 60. Um valor de 0 desativa manutenção. |

Dependendo dos seus requisitos, você pode definir o Dispatcher como um serviço para seus objetos. Para configurar o Dispatcher para todo o seu site, modifique o objeto padrão:


**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**Unix**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Next Steps {#next-steps-2}

Antes de começar a usar o Dispatcher, você deve:

* [Configurar o Dispatcher](dispatcher-configuration.md)
* [Configure o AEM](page-invalidate.md) para trabalhar com o Dispatcher.
