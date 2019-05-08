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
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Instalação do Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

Use a página [Notas](release-notes.md) de versão do Dispatcher para obter o arquivo de instalação do Dispatcher mais recente para seu sistema operacional e servidor Web. Os números de versão do Dispatcher são independentes dos números de lançamento do Adobe Experience Manager e são compatíveis com as versões do Adobe Experience Manager 6. x, 5. x e Adobe CQ 5. x.

A seguinte convenção de nomenclatura de arquivo é usada:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

For example, the `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` file contains Dispatcher version 4.3.1 for an Apache 2.4 web server that runs on Linux i686 and is packaged using the **tar** format.

A tabela a seguir lista o identificador do servidor da Web usado em nomes de arquivo para cada servidor Web:

| Servidor Web | Kit de instalação |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt; outros parâmetros &gt; |
| Apache 2.2 | dispatcher-apache **2.2**-&lt; outros parâmetros &gt; |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**iis**-&lt; outros parâmetros &gt; |
| Sun Java Web Server iplanet | dispatcher-**ns**-&lt; other parameters &gt; |

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

### Componentes necessários para IIS {#required-iis-components}

As versões 8.5 e 10 do IIS exigem que os seguintes componentes do IIS sejam instalados:

* Extensões de ISAPI

Além disso, você deve adicionar a função Servidor da Web (IIS). Use o Gerenciador de servidor para adicionar a função e os componentes.

## Microsoft IIS - Instalação do módulo Dispatcher {#microsoft-iis-installing-the-dispatcher-module}

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
      * Instância do autor: `author_dispatcher.any`
      * Instância de publicação: `dispatcher.any`

## Microsoft IIS - Configurar o arquivo Dispatcher INI {#microsoft-iis-configure-the-dispatcher-ini-file}

Edite o `disp_iis.ini` arquivo para configurar a instalação do Dispatcher. O formato básico do `.ini` arquivo é o seguinte:

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
| configurar caminho | O local do `dispatcher.any` sistema de arquivos local (caminho absoluto). |
| arquivo de log | O local do `dispatcher.log` arquivo. Se isso não for definido, registre as mensagens para o log do evento do Windows. |
| nível de logon | Define o Nível de log usado para gerar mensagens para o log do evento. Os seguintes valores podem ser especificados: Nível de log para o arquivo de log: <br/>0 - apenas mensagens de erro. <br/>1 - erros e avisos. <br/>2 - erros, avisos e mensagens informativas <br/>3 - erros, avisos, mensagens informativas e de depuração. <br/>**Observação**: Recomenda-se definir o nível de log como 3 durante a instalação e o teste, em seguida, para 0 quando executados em um ambiente de produção. |
| replaceauthorization | Especifica como os cabeçalhos de autorização na solicitação HTTP são manipulados. Os seguintes valores são válidos:<br/>0 - Os cabeçalhos de autorização não são modificados. <br/>1 - Substitui qualquer cabeçalho chamado &quot;Autorização&quot; diferente de &quot;Básico&quot; por seu `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
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

### Configuração do Microsoft IIS {#configuring-microsoft-iis}

Configure o IIS para integrar o módulo do Dispatcher ISAPI. No IIS, você usa o mapeamento do aplicativo curinga.

### Configuração de acesso anônimo - IIS 8.5 e 10 {#configuring-anonymous-access-iis-and}

O agente de replicação padrão do Flush na instância do Autor é configurado para que não envie credenciais de segurança com solicitações de flush. Portanto, o site que você está usando em um cache do Dispatcher deve permitir acesso anônimo.

Se o site usa um método de autenticação, o agente de replicação do Flush deve ser configurado adequadamente.

1. Abra o IIS Manager e selecione o site que você está usando como cache do Disptcher.
1. Usando o modo de Exibição de recursos, na seção IIS clique duas vezes em Autenticação.
1. Se a Autenticação anônima não estiver ativada, selecione Autenticação anônima e, na área Ações, clique em Ativar.

### Integração do módulo ISAPI do Dispatcher - IIS 8.5 e 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Use o procedimento a seguir para adicionar o Módulo ISAPI do Dispatcher ao IIS.

1. Abra o IIS Manager.
1. Selecione o site que você está usando como o Cache do Dispatcher.
1. Usando o modo de Exibição de recursos, na seção IIS, clique duas vezes em Mapeamentos do manipulador.
1. No painel Ações da página Mapeamentos do manipulador, clique em Adicionar mapa de scripts curinga, adicione os seguintes valores de propriedade e clique em OK:

   * Caminho de solicitação: *
   * Executável: O caminho absoluto do arquivo disp_ iis. dll, por exemplo `C:\inetpub\Scripts\disp_iis.dll`.
   * Nome: Um nome descritivo para o mapeamento do manipulador, por exemplo `Dispatcher`.

1. Na caixa de diálogo exibida, para adicionar a biblioteca disp_ iis. dll à lista de Restrições de ISAPI e CGI, clique em Sim.

   Para IIS 7.0 e 7.5, a configuração é concluída. Continue com as etapas restantes se estiver configurando o IIS 8.0.

1. (IIS 8.0) Na lista de mapeamentos do manipulador, selecione aquele que acabou de criar e, na área Ações, clique em Editar.
1. (IIS 8.0) Na caixa de diálogo Editar mapa de script, clique no botão Restrições de solicitação.
1. (IIS 8.0) Para garantir que o manipulador seja usado para arquivos e pastas que ainda não estão em cache, desmarque Invocar somente manipulador se a Solicitação estiver vinculada e clique em OK.
1. (IIS 8.0) Na caixa de diálogo Editar Mapa de script, clique em OK.

### Configuração de acesso ao cache - IIS 8.5 e 10 {#configuring-access-to-the-cache-iis-and}

Forneça o usuário padrão do Pool de aplicativos com acesso de gravação à pasta que está sendo usada como cache do Dispatcher.

1. Clique com o botão direito do mouse na pasta raiz do site que está usando como cache do Dispatcher e clique em Propriedades, como `C:\inetpub\wwwroot`.
1. Na guia Segurança, clique em Editar e, na caixa de diálogo Permissões, clique em Adicionar. Uma caixa de diálogo é aberta para selecionar contas de usuário. Clique no botão Locais, selecione o nome do computador e clique em OK.

   Mantenha essa caixa de diálogo aberta enquanto você concluir a próxima etapa.

1. no IIS Manager, selecione o site IIS que você está usando para o cache do Dispatcher e, no lado direito da janela, clique em Configurações avançadas.
1. Selecione o valor da propriedade Pool de aplicativos e copie-o para a área de transferência.
1. Retorne à caixa de diálogo aberta. Na caixa Inserir nomes de objetos para selecionar, digite `IIS AppPool\` e cole o conteúdo da área de transferência. O valor deve ser semelhante ao seguinte exemplo:

   `IIS AppPool\DefaultAppPool`

1. Clique no botão Verificar nomes. Quando o Windows resolver a conta de usuário, clique em OK.
1. Na caixa de diálogo Permissões da pasta do expedidor, selecione a conta que acabou de adicionar, ative todas as permissões da conta **, exceto para Controle** total, e clique em OK. Clique em OK para fechar a caixa de diálogo Propriedades da pasta.

### Registro do tipo Mime JSON - IIS 8.5 e 10 {#registering-the-json-mime-type-iis-and}

Use o procedimento a seguir para registrar o tipo JSON MIME, quando quiser que o Dispatcher permita chamadas JSON.

1. No IIS Manager, selecione o site e usando a Exibição de recursos, clique duas vezes em Tipos Mime.
1. Se a extensão JSON não estiver na lista, no painel Ações, clique em Adicionar, insira os seguintes valores de propriedade e clique em OK:

   * Extensão de nome de arquivo: `.json`
   * Tipo MIME: `application/json`

### Remoção do compartimento oculto - IIS 8.5 e 10 {#removing-the-bin-hidden-segment-iis-and}

Use o procedimento a seguir para remover o segmento `bin` oculto. Sites que não são novos podem conter esse segmento oculto.

1. No IIS Manager, selecione o site e usando a Exibição de recursos, clique duas vezes em Solicitar filtragem.
1. Selecione o `bin` segmento, clique em Remover e, na caixa de diálogo de confirmação, clique em Sim.

### Registro de mensagens IIS em um arquivo - IIS 8.5 e 10 {#logging-iis-messages-to-a-file-iis-and}

Use o procedimento a seguir para gravar mensagens de log do Dispatcher em um arquivo de log em vez do log do evento do Windows. É necessário configurar o Dispatcher para usar o arquivo de log e fornecer o IIS com acesso de gravação ao arquivo.

1. Use o Windows Explorer para criar uma pasta nomeada `dispatcher` abaixo da pasta logs da instalação do IIS. O caminho dessa pasta para uma instalação típica é `C:\inetpub\logs\dispatcher`.

1. Clique com o botão direito do mouse na pasta do expedidor e clique em Propriedades.
1. Na guia Segurança, clique em Editar e, na caixa de diálogo Permissões, clique em Adicionar. Uma caixa de diálogo é aberta para selecionar contas de usuário. Clique no botão Locais, selecione o nome do computador e clique em OK.

   Mantenha essa caixa de diálogo aberta enquanto você concluir a próxima etapa.

1. no IIS Manager, selecione o site IIS que você está usando para o cache do Dispatcher e, no lado direito da janela, clique em Configurações avançadas.
1. Selecione o valor da propriedade Pool de aplicativos e copie-o para a área de transferência.
1. Retorne à caixa de diálogo aberta. Na caixa Inserir nomes de objetos para selecionar, digite `IIS AppPool\` e cole o conteúdo da área de transferência. O valor deve ser semelhante ao seguinte exemplo:

   `IIS AppPool\DefaultAppPool`

1. Clique no botão Verificar nomes. Quando o Windows resolver a conta de usuário, clique em OK.
1. Na caixa de diálogo Permissões da pasta do expedidor, selecione a conta que acabou de adicionar, ative todas as permissões da conta** exceto para Controle total,** e clique em OK. Clique em OK para fechar a caixa de diálogo Propriedades da pasta.
1. Use um editor de texto para abrir o `disp_iis.ini` arquivo.
1. Adicione uma linha de texto semelhante ao seguinte exemplo para configurar o local do arquivo de log e salve o arquivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Próximas etapas {#next-steps}

Antes de começar a usar o Dispatcher, você deve saber:

* [Configure](dispatcher-configuration.md) Dispatcher
* [Configure o AEM](page-invalidate.md) para trabalhar com o Dispatcher.

## Apache Web Server {#apache-web-server}

>[!CAUTION]
>
>As instruções para instalação sob **o Windows** e **Unix** são abordadas aqui. Tenha cuidado ao executar as etapas.

### Instalação do Apache Web Server {#installing-apache-web-server}

Para obter informações sobre como instalar um servidor Web do Apache, leia o manual da instalação - [on-line](https://httpd.apache.org/) ou na distribuição.

>[!CAUTION]
>
>Se você estiver criando um binário do Apache compilando os arquivos de origem, certifique-se de ativar os módulos **dinâmicos**. This can be done by using any of the **--enable-shared** options. No mínimo, inclua `mod_so` o módulo.
>
>Mais informações podem ser encontradas no manual de instalação do Apache Web Server.

Consulte também as Dicas [de segurança do Apache HTTP Server](https://httpd.apache.org/docs/2.2/misc/security_tips.html) e [Relatórios de segurança](https://httpd.apache.org/security_report.html).

### Apache Web Server - Adicionar o módulo do Dispatcher {#apache-web-server-add-the-dispatcher-module}

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
      Copie `dispatcher-apache<options>.so` para este diretório.\
      Para simplificar a manutenção de longo prazo, você também pode criar um link simbólico chamado `mod_dispatcher.so` Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copie o dispatcher. qualquer arquivo para o `<APACHE_ROOT>/conf` diretório.

   **Observação:** É possível colocar esse arquivo em um local diferente, desde que a propriedade dispatcherlog do módulo Dispatcher esteja configurada adequadamente. (Consulte Entradas de configuração específicas do Dispatcher abaixo.)

### Apache Web Server - Configurar propriedades do selinux {#apache-web-server-configure-selinux-properties}

Se você estiver executando o Dispatcher no rehat Linux Kernel 2.6 com o selinux ativado, você pode colocar mensagens de erro como isso no arquivo de log do expedidor.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Isso pode ser devido a uma segurança de selinux ativada. Em seguida, você precisa executar as seguintes tarefas:

* Configure o contexto selinux do arquivo do módulo do expedidor.
* Ative scripts e módulos HTTPD para fazer conexões de rede.
* Configure o contexto selinux do docroot, onde os arquivos em cache são armazenados.

Insira os seguintes comandos em uma janela de terminal, substituindo `[path to the dispatcher.so file]` pelo caminho para o módulo Dispatcher instalado no Apache Web Server e *`path to the docroot`* pelo caminho onde o docroot está localizado (por exemplo, `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web Server - Configuração do Apache Web Server para Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

O Apache Web Server precisa ser configurado, usando `httpd.conf`. No kit de instalação do Dispatcher, você encontrará um arquivo de configuração de exemplo chamado `httpd.conf.disp<x>`.

Essas etapas são obrigatórias:

1. Vá até `<APACHE_ROOT>/conf`.
1. Abrir `httpd.conf`para edição.
1. As seguintes entradas de configuração devem ser adicionadas, na ordem listada:

   * **Loadmodule** para carregar o módulo ao iniciar.
   * Entradas de configuração específicas do Dispatcher, incluindo **dispatcherconfig, dispatcherlog** e **dispatcherloglevel**.
   * **Sethandler** para ativar o Dispatcher. **Loadmodule**.
   * **Modmimeusepathinfo** para configurar o comportamento **de mod_ mime**.

1. (Opcional) Recomenda-se alterar o proprietário do diretório htdocs:

   * O servidor do apache começa como raiz, embora os processos filho iniciem como daemon (para fins de segurança). Documentroot (`<APACHE_ROOT>/htdocs`) deve pertencer ao daemon do usuário:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**Loadmodule**

A tabela a seguir lista exemplos que podem ser usados; as entradas exatas são de acordo com seu Apache Web Server específico:

|  |
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
| Dispatcherdeclineroot | Define se deseja recusar solicitações para a raiz &quot;/&quot;: <br/>**0** - aceitar solicitações para/ <br/>**1** - solicitações para/não são manipuladas pelo expedidor; use mod_ alias para o mapeamento correto. |
| Dispatcheruseprocessedurl | Define se os urls pré-processados devem ser usados para todo processamento adicional pelo Dispatcher: <br/>**0** - use o URL original passado para o servidor da Web. <br/>**1** - o dispatcher usa o URL processado pelos manipuladores que antecedem o dispatcher (isto é `mod_rewrite`,) em vez do URL original passado para o servidor da Web. Por exemplo, o URL original ou processado é correspondido com os filtros do Dispatcher. O URL também é usado como a base para a estrutura do arquivo cache. Consulte a documentação do site do Apache para obter informações sobre mod_ rewrite; por exemplo, Apache 2.2. Ao usar mod_ rewrite, é aconselhável usar a senha do sinalizador | PT &#39; (passagem para o próximo manipulador) para forçar o mecanismo de regravação a definir o campo uri da estrutura interna request_ rec para o valor do campo de nome de arquivo. |
| Dispatcherpasserror | Define como suportar códigos de erro para manuseio errordocument: <br/>**0** - O Dispatcher spools todas as respostas de erro para o cliente. <br/>**1** - O Dispatcher não coloca uma resposta de erro no cliente (onde o código de status é maior ou igual a 400), mas passa o código de status para o Apache, que permite que uma diretiva errordocument processe tal código de status. <br/>**Intervalo** de código - Especifique uma gama de códigos de erro para os quais a resposta é transmitida ao Apache. Outros códigos de erro são transmitidos para o cliente. Por exemplo, a configuração a seguir envia respostas do erro 412 para o cliente, e todos os outros erros são passados para o Apache: Dispatcherpasserror 400-411,413-417 |
| Dispatcherkeepalivetimeout | Especifica o tempo limite de manutenção, em segundos. A partir do Dispatcher versão 4.2.0, o valor de manutenção padrão é 60. Um valor de 0 desativa manutenção. |
| Dispatpaintcanonurl | Definir esse parâmetro como Ativado transmite o URL bruto para o backend em vez do canonicalizado e substituirá as configurações de dispatcheruseprocessedurl. O valor padrão é Desligado. <br/>**Observação**: As regras de filtro na configuração do Dispatcher sempre serão avaliadas em relação à URL sanitizada não o URL bruto. |




>[!NOTE]
>
>Entradas de caminho são relativas ao diretório raiz do Apache Web Server.

>[!NOTE]
>
>As configurações padrão do Cabeçalho do servidor são: `  
ServerTokens Full``  
DispatcherNoServerHeader 0`\
O que mostra a versão do AEM (para fins estatísticos). Se você quiser desativar essas informações estão disponíveis no cabeçalho que pode ser definido: `  
ServerTokens Prod`\
Consulte a [Documentação do Apache sobre a diretiva servertokens (por exemplo, para Apache 2.2)](https://httpd.apache.org/docs/2.2/mod/core.html) para obter mais informações.

**Sethandler**

Após essas entradas, você deve adicionar uma **declaração sethandler** ao contexto da sua configuração ( `<Directory>`, `<Location>`) para que o Dispatcher trate as solicitações de entrada. O exemplo a seguir configura o Dispatcher para lidar com solicitações do site completo:

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
O parâmetro da declaração **sethandler** deve ser escrito *exatamente como nos exemplos acima*, já que esse é o nome do manipulador definido no módulo.
Consulte os arquivos de configuração de exemplo fornecidos e a documentação do Apache Web Server para obter detalhes completos sobre esse comando.

**Modmimeusepathinfo**

Após a **declaração sethandler** , você também deve adicionar a definição **modmimeusepathinfo** .

>[!NOTE]
O `ModMimeUsePathInfo` parâmetro só deve ser usado e configurado se você estiver usando o Dispatcher versão 4.0.9 ou superior.
(Observe que o Dispatcher versão 4.0.9 foi lançado em 2011. Se estiver usando uma versão mais antiga, a atualização para uma versão recente do Dispatcher seria apropriada).

O parâmetro **modmimeusepathinfo** deve ser definido `On` para todas as configurações do Apache:

`ModMimeUsePathInfo On`

O módulo mod_ mime (veja por exemplo, [Apache Module mod_ mime](https://httpd.apache.org/docs/2.2/mod/mod_mime.html)) é usado para atribuir metadados de conteúdo ao conteúdo selecionado para uma resposta HTTP. A configuração padrão significa que quando mod_ mime determina o tipo de conteúdo, apenas a parte do URL que mapeia para um arquivo ou diretório será considerada.

Quando `On`, o `ModMimeUsePathInfo` parâmetro especifica o `mod_mime` tipo de conteúdo com base no URL *completo* ; isso significa que os recursos virtuais terão as informações aplicadas com base na sua extensão.

O exemplo a seguir ativa **modmimeusepathinfo**:

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

### Ativar suporte para HTTPS (Unix e Linux) {#enable-support-for-https-unix-and-linux}

O Dispatcher usa o openssl para implementar comunicações seguras em HTTP. A partir do Dispatcher versão **4.2.0**, openssl 1.0.0 e openssl 1.0.1 são compatíveis. O Dispatcher usa o openssl 1.0.0 por padrão. Para usar o openssl 1.0.1, use o seguinte procedimento para criar links simbólicos para que o Dispatcher use as bibliotecas openssl instaladas.

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
Se você estiver usando uma versão personalizada do Apache, verifique se o Apache e o Dispatcher são compilados usando a mesma versão de [openssl](https://www.openssl.org/source/).

### Próximas etapas {#next-steps-1}

Antes de começar a usar o Dispatcher, você deve:

* [Configure](dispatcher-configuration.md) Dispatcher
* [Configure o AEM](page-invalidate.md) para trabalhar com o Dispatcher.

## Sun Java System Web Server/iplanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
As instruções para ambientes do Windows e Unix são abordadas aqui.
Tenha cuidado ao selecionar o que deve ser executado.

### Sun Java System Web Server/iplanet - Instalação do servidor Web {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obter informações completas sobre como instalar esses servidores da Web, consulte a respectiva documentação:

* Sun Java System Web Server
* Iplanet Web Server

### Sun Java System Web Server/iplanet - Adicionar o módulo do Dispatcher {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

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

1. Coloque o arquivo do Dispatcher no `plugin` diretório do servidor da Web:

### Sun Java System Web Server/iplanet - Configurar para o Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

O servidor Web precisa ser configurado, usando `obj.conf`. No kit de instalação do Dispatcher, você encontrará um arquivo de configuração de exemplo chamado `obj.conf.disp`.

1. Vá até `<WEBSERVER_ROOT>/config`.
1. Abrir `obj.conf`para edição.
1. Copie a linha que começa:\
   `Service fn="dispService"`\
   from `obj.conf.disp` to the initialization section of `obj.conf`.

1. Salve as alterações.
1. Abrir `magnus.conf` para edição.
1. Copie as duas linhas que iniciam:\
   `Init funcs="dispService, dispInit"`\
   e\
   `Init fn="dispInit"`\
   from `obj.conf.disp` to the initialization section of `magnus.conf`.

1. Salve as alterações.

>[!NOTE]
As configurações a seguir devem estar em uma linha e devem `$(SERVER_ROOT)``$(PRODUCT_SUBDIR)` ser substituídas pelos respectivos valores.

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
| config | Localização e nome do arquivo de configuração `dispatcher.any.` |
| arquivo de log | Local e nome do arquivo de log. |
| nível de logon | Nível de log para gravar mensagens no arquivo de log: <br/>**0** Erros <br/>**1** Avisos <br/>**2** do Infos <br/>**3** de depuração <br/>**:** Recomenda-se definir o nível de log como 3 durante a instalação e testes e a 0 quando executados em um ambiente de produção. |
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

### Próximas etapas {#next-steps-2}

Antes de começar a usar o Dispatcher, você deve:

* [Configure](dispatcher-configuration.md) Dispatcher
* [Configure o AEM](page-invalidate.md) para trabalhar com o Dispatcher.
