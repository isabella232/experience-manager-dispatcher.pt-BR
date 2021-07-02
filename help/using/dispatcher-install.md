---
title: Instalação do Dispatcher
seo-title: Instalação AEM Dispatcher
description: Saiba como instalar o módulo Dispatcher no Microsoft Internet Information Server, Apache Web Server e Sun Java Web Server-iPlanet.
seo-description: Saiba como instalar o módulo Dispatcher AEM no Microsoft Internet Information Server, Apache Web Server e Sun Java Web Server-iPlanet.
uuid: 2384b907-1042-4707-b02f-fba2125618cf
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
discoiquuid: f00ad751-6b95-4365-8500-e1e0108d9536
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
source-git-commit: 35739785aa835a0b995fab4710a0e37bd0ff62b4
workflow-type: tm+mt
source-wordcount: '3684'
ht-degree: 1%

---

# Instalação do Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Use a página [Notas de versão do Dispatcher](release-notes.md) para obter o arquivo de instalação do Dispatcher mais recente para seu sistema operacional e servidor da Web. Os números de versão do Dispatcher são independentes dos números de versão do Adobe Experience Manager e são compatíveis com as versões do Adobe Experience Manager 6.x, 5.x e Adobe CQ 5.x.

>[!NOTE]
>
>Observe que o Adobe Experience Manager 6.5 requer o Dispatcher versão 4.3.2 ou superior. Dito isso, as versões do Dispatcher são independentes de AEM, por exemplo, a versão 4.3.2 do Dispatcher também é compatível com o Adobe Experience Manager 6.4.

A seguinte convenção de nomenclatura de arquivo é usada:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Por exemplo, o arquivo `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` contém o Dispatcher versão 4.3.1 para um servidor Web Apache 2.4 que é executado no Linux i686 e é empacotado usando o formato **tar**.

A tabela a seguir lista o identificador do servidor da Web usado em nomes de arquivo para cada servidor da Web:

| Servidor Web | Kit de instalação |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;outros parâmetros> |
| Microsoft Internet Information Server 7.5, 8, 8.5 | dispatcher-**iis**-&lt;outros parâmetros> |
| Sun Java Web Server iPlanet | dispatcher-**ns**-&lt;outros parâmetros> |

>[!CAUTION]
>
>Você deve instalar a versão mais recente do Dispatcher disponível para sua plataforma. Anualmente, você deve atualizar sua instância do Dispatcher para usar a versão mais recente para aproveitar os aprimoramentos de produtos.

Cada arquivo contém os seguintes arquivos:

* os módulos do Dispatcher
* um exemplo de arquivo de configuração
* o arquivo README que contém instruções de instalação e informações de última hora
* o arquivo CHANGES que lista problemas corrigidos em versões atuais e anteriores

>[!NOTE]
>
>Verifique o arquivo README para ver se há alterações de última hora / notas específicas da plataforma antes de iniciar a instalação.

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

Para obter informações sobre como instalar este servidor Web, consulte os seguintes recursos:

* Documentação da Microsoft no Internet Information Server
* [&quot;O site oficial do Microsoft IIS&quot;](https://www.iis.net/)

### Componentes do IIS necessários {#required-iis-components}

As versões 8.5 e 10 do IIS exigem que os seguintes componentes do IIS sejam instalados:

* Extensões ISAPI

Além disso, você deve adicionar a função Servidor Web (IIS). Use o Gerenciador de Servidores para adicionar a função e os componentes.

## Microsoft IIS - Instalação do módulo Dispatcher {#microsoft-iis-installing-the-dispatcher-module}

O arquivo necessário para o Microsoft Internet Information System é:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

O arquivo ZIP contém os seguintes arquivos:

| Arquivo | Descrição |
|--- |--- |
| `disp_iis.dll` | O arquivo da biblioteca de links dinâmicos do Dispatcher. |
| `disp_iis.ini` | Arquivo de configuração para o IIS. Este exemplo pode ser atualizado com suas necessidades. **Observação**: O arquivo ini deve ter o mesmo nome-raiz que a dll. |
| `dispatcher.any` | Um exemplo de arquivo de configuração para o Dispatcher. |
| `author_dispatcher.any` | Um exemplo de arquivo de configuração para o Dispatcher que trabalha com a instância do autor. |
| LEITURA | Arquivo Leia-me que contém instruções de instalação e informações de última hora. **Observação**: Verifique este arquivo antes de iniciar a instalação. |
| ALTERAÇÕES | Altera o arquivo que lista os problemas corrigidos em versões atuais e anteriores. |

Use o procedimento a seguir para copiar os arquivos do Dispatcher para o local correto.

1. Use o Windows Explorer para criar o diretório `<IIS_INSTALLDIR>/Scripts`, por exemplo, `C:\inetpub\Scripts`.

1. Extraia os seguintes arquivos do pacote Dispatcher para o diretório Scripts:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * Um dos arquivos a seguir, dependendo se o Dispatcher está trabalhando com uma instância de autor AEM ou instância de publicação:
      * Instância do autor: `author_dispatcher.any`
      * Instância de publicação: `dispatcher.any`

## Microsoft IIS - Configurar o arquivo INI do Dispatcher {#microsoft-iis-configure-the-dispatcher-ini-file}

Edite o arquivo `disp_iis.ini` para configurar a instalação do Dispatcher. O formato básico do arquivo `.ini` é o seguinte:

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
| configpath | A localização de `dispatcher.any` no sistema de arquivos local (caminho absoluto). |
| arquivo de log | O local do arquivo `dispatcher.log`. Se isso não for definido, as mensagens de log serão enviadas para o log de eventos do Windows. |
| loglevel | Define o Nível de Log usado para enviar mensagens para o log de eventos. Os seguintes valores podem ser especificados:Nível de log para o arquivo de log: <br/>0 - apenas mensagens de erro. <br/>1 - erros e avisos. <br/>2 - erros, avisos e mensagens informativas  <br/>3 - erros, avisos, mensagens informativas e de depuração. <br/>**Observação**: É recomendável definir o nível de log como 3 durante a instalação e o teste e depois como 0 durante a execução em um ambiente de produção. |
| substituição de autorização | Especifica como os cabeçalhos de autorização na solicitação HTTP são tratados. Os seguintes valores são válidos:<br/>0 - Os cabeçalhos de autorização não são modificados. <br/>1 - Substitui qualquer cabeçalho chamado &quot;Autorização&quot;, diferente de &quot;Básica&quot;, por seu  `Basic <IIS:LOGON\_USER>` equivalente.<br/> |
| servervariables | Define como as variáveis do servidor são processadas.<br/>0 - As variáveis do servidor IIS não são enviadas para o Dispatcher nem para o AEM. <br/>1 - todas as variáveis do servidor IIS (como  `LOGON\_USER, QUERY\_STRING, ...`) são enviadas ao Dispatcher, juntamente com os cabeçalhos de solicitação (e também à instância de AEM, se não estiver armazenada em cache).  <br/>As variáveis de servidor incluem  `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` e muitas outras. Consulte a documentação do IIS para obter a lista completa de variáveis, com detalhes. |
| enable_chunked_transfer | Define se deseja habilitar (1) ou desabilitar (0) a transferência fragmentada para a resposta do cliente. O valor padrão é 0. |

Um exemplo de configuração:

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configuração do Microsoft IIS {#configuring-microsoft-iis}

Configure o IIS para integrar o módulo ISAPI do Dispatcher. No IIS, você usa mapeamento de aplicativo curinga.

### Configuração do acesso anônimo - IIS 8.5 e 10 {#configuring-anonymous-access-iis-and}

O agente de replicação de limpeza padrão na instância do autor é configurado para que ele não envie credenciais de segurança com solicitações de liberação. Portanto, o site que você está usando no cache do Dispatcher deve permitir acesso anônimo.

Se o site usar um método de autenticação, o agente de replicação de limpeza deverá ser configurado adequadamente.

1. Abra o Gerenciador do IIS e selecione o site que você está usando como cache do Dispatcher.
1. Usando o modo de Exibição de recursos, na seção IIS, clique duas vezes em Autenticação.
1. Se a Autenticação Anônima não estiver ativada, selecione Autenticação Anônima e, na área Ações, clique em Ativar.

### Integração do Dispatcher ISAPI Module - IIS 8.5 e 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Use o procedimento a seguir para adicionar o Módulo ISAPI do Dispatcher ao IIS.

1. Abra o Gerenciador do IIS.
1. Selecione o site que você está usando como Cache do Dispatcher.
1. Usando o modo de Exibição de recursos, na seção IIS, clique duas vezes em Mapeamentos do manipulador.
1. No painel Ações da página Mapeamentos do manipulador , clique em Adicionar mapa de script curinga , adicione os seguintes valores de propriedade e clique em OK:

   * Caminho da solicitação: *
   * Executável: O caminho absoluto do arquivo disp_is.dll, por exemplo `C:\inetpub\Scripts\disp_iis.dll`.
   * Nome: Um nome descritivo para o mapeamento do manipulador, por exemplo `Dispatcher`.

1. Na caixa de diálogo exibida, para adicionar a biblioteca disp_is.dll à lista ISAPI e Restrições de CGI, clique em Sim.

   Para o IIS 7.0 e 7.5, a configuração é concluída. Continue com as etapas restantes se estiver configurando o IIS 8.0.

1. (IIS 8.0) Na lista de mapeamentos do manipulador, selecione aquele que acabou de criar e, na área Ações, clique em Editar.
1. (IIS 8.0) Na caixa de diálogo Editar mapa de script, clique no botão Restrições de solicitação .
1. (IIS 8.0) Para garantir que o manipulador seja usado para arquivos e pastas que ainda não foram armazenados em cache, desmarque a opção Invocar manipulador somente se a solicitação estiver mapeada para e clique em OK.
1. (IIS 8.0) Na caixa de diálogo Editar Mapa de Script, clique em OK.

### Configuração do acesso ao cache - IIS 8.5 e 10 {#configuring-access-to-the-cache-iis-and}

Forneça ao usuário padrão do Pool de Aplicativos acesso de gravação à pasta que está sendo usada como cache do Dispatcher.

1. Clique com o botão direito do mouse na pasta raiz do site que você está usando como cache do Dispatcher e clique em Propriedades, como `C:\inetpub\wwwroot`.
1. Na guia Segurança, clique em Editar e, na caixa de diálogo Permissões, clique em Adicionar. Uma caixa de diálogo é aberta para selecionar contas de usuário. Clique no botão Localizações, selecione o nome do computador e clique em OK.

   Mantenha essa caixa de diálogo aberta enquanto conclui a próxima etapa.

1. no Gerenciador do IIS, selecione o site do IIS que você está usando para o cache do Dispatcher e, no lado direito da janela, clique em Configurações avançadas.
1. Selecione o valor da propriedade Pool de Aplicativos e copie-o para a área de transferência.
1. Retorne à caixa de diálogo aberta. Na caixa Inserir os nomes de objetos a serem selecionados, digite `IIS AppPool\` e cole o conteúdo da área de transferência. O valor deve ser semelhante ao seguinte exemplo:

   `IIS AppPool\DefaultAppPool`

1. Clique no botão Verificar nomes. Quando o Windows resolver a conta de usuário, clique em OK.
1. Na caixa de diálogo Permissões da pasta do dispatcher, selecione a conta que acabou de adicionar, ative todas as permissões para a conta **exceto para Controle completo** e clique em OK. Clique em OK para fechar a caixa de diálogo Propriedades da pasta.

### Registro do tipo MIME JSON - IIS 8.5 e 10 {#registering-the-json-mime-type-iis-and}

Use o procedimento a seguir para registrar o tipo MIME JSON, quando desejar que o Dispatcher permita chamadas JSON.

1. No Gerenciador do IIS, selecione seu site e usando a Exibição de recursos, clique duas vezes em Tipos MIME.
1. Se a extensão JSON não estiver na lista, no painel Ações, clique em Adicionar, insira os seguintes valores de propriedade e clique em OK:

   * Extensão do nome do arquivo: `.json`
   * Tipo MIME: `application/json`

### Remoção do segmento do compartimento oculto - IIS 8.5 e 10 {#removing-the-bin-hidden-segment-iis-and}

Use o procedimento a seguir para remover o segmento oculto `bin`. Os sites que não são novos podem conter esse segmento oculto.

1. No Gerenciador do IIS, selecione seu site e usando a Exibição de recursos, clique duas vezes em Solicitar filtragem.
1. Selecione o segmento `bin`, clique em Remover e, na caixa de diálogo de confirmação, clique em Sim.

### Registro de mensagens do IIS em um arquivo - IIS 8.5 e 10 {#logging-iis-messages-to-a-file-iis-and}

Use o procedimento a seguir para gravar mensagens de log do Dispatcher em um arquivo de log, em vez de no log de Eventos do Windows. Você precisa configurar o Dispatcher para usar o arquivo de log e fornecer ao IIS acesso de gravação ao arquivo.

1. Use o Windows Explorer para criar uma pasta chamada `dispatcher` abaixo da pasta de logs da instalação do IIS. O caminho desta pasta para uma instalação típica é `C:\inetpub\logs\dispatcher`.

1. Clique com o botão direito do mouse na pasta dispatcher e clique em Properties.
1. Na guia Segurança, clique em Editar e, na caixa de diálogo Permissões, clique em Adicionar. Uma caixa de diálogo é aberta para selecionar contas de usuário. Clique no botão Localizações, selecione o nome do computador e clique em OK.

   Mantenha essa caixa de diálogo aberta enquanto conclui a próxima etapa.

1. no Gerenciador do IIS, selecione o site do IIS que você está usando para o cache do Dispatcher e, no lado direito da janela, clique em Configurações avançadas.
1. Selecione o valor da propriedade Pool de Aplicativos e copie-o para a área de transferência.
1. Retorne à caixa de diálogo aberta. Na caixa Inserir os nomes de objetos a serem selecionados, digite `IIS AppPool\` e cole o conteúdo da área de transferência. O valor deve ser semelhante ao seguinte exemplo:

   `IIS AppPool\DefaultAppPool`

1. Clique no botão Verificar nomes. Quando o Windows resolver a conta de usuário, clique em OK.
1. Na caixa de diálogo Permissões da pasta do dispatcher, selecione a conta que acabou de adicionar, ative todas as permissões para a conta **exceto para Controle completo,** e clique em OK. Clique em OK para fechar a caixa de diálogo Propriedades da pasta.
1. Use um editor de texto para abrir o arquivo `disp_iis.ini`.
1. Adicione uma linha de texto semelhante ao seguinte exemplo para configurar o local do arquivo de log e, em seguida, salve o arquivo:

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Próximas etapas {#next-steps}

Antes de começar a usar o Dispatcher, você deve saber:

* [](dispatcher-configuration.md) ConfigureDispatcher
* [Configure o ](page-invalidate.md) AEM para funcionar com o Dispatcher.

## Servidor Web Apache {#apache-web-server}

>[!CAUTION]
>
>As instruções para instalação em **Windows** e **Unix** são abordadas aqui. Tenha cuidado ao executar as etapas.

### Instalar o servidor da Web Apache {#installing-apache-web-server}

Para obter informações sobre como instalar um Apache Web Server, leia o manual de instalação - [online](https://httpd.apache.org/) ou na distribuição.

>[!CAUTION]
>
>Se você estiver criando um binário do Apache compilando os arquivos de origem, certifique-se de ativar **o suporte aos módulos dinâmicos**. Isso pode ser feito usando qualquer uma das opções **—enable-shared**. No mínimo, inclua o módulo `mod_so`.
>
>Mais informações podem ser encontradas no manual de instalação do Apache Web Server.

Consulte também o Apache HTTP Server [Dicas de segurança](https://httpd.apache.org/docs/2.4/misc/security_tips.html) e [Relatórios de segurança](https://httpd.apache.org/security_report.html).

### Apache Web Server - Adicionar o módulo Dispatcher {#apache-web-server-add-the-dispatcher-module}

O Dispatcher vem como:

* **Windows**: uma DLL (Dynamic Link Library)
* **Unix**: um DSO (Dynamic Shared Object, objeto compartilhado dinâmico)

Os arquivos de arquivamento de instalação contêm os seguintes arquivos - dependendo se você selecionou Windows ou Unix:

| Arquivo | Descrição |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows: O arquivo da biblioteca de links dinâmicos do Dispatcher. |
| dispatcher-apache&lt;x.y>-&lt;rel-nr>.so | Unix: O arquivo da biblioteca de objetos compartilhados do Dispatcher. |
| mod_dispatcher.so | Unix: Um link de exemplo. |
| http.conf.disp&lt;x> | Um exemplo de arquivo de configuração para o servidor Apache. |
| dispatcher.any | Um exemplo de arquivo de configuração para o Dispatcher. |
| LEITURA | Arquivo Leia-me que contém instruções de instalação e informações de última hora. **Observação**: Verifique este arquivo antes de iniciar a instalação. |
| ALTERAÇÕES | Altera o arquivo que lista os problemas corrigidos nas versões atual e anterior. |

Use as seguintes etapas para adicionar o Dispatcher ao seu Apache Web Server:

1. Coloque o arquivo Dispatcher no diretório adequado do módulo Apache:

   * **Windows**: Local  `disp_apache<x.y>.dll` `<APACHE_ROOT>/modules`
   * **Unix**: Localize o  `<APACHE_ROOT>/libexec` diretório ou  `<APACHE_ROOT>/modules`de acordo com sua instalação.\
      Copie `dispatcher-apache<options>.so` neste diretório.\
      Para simplificar a manutenção de longo prazo, você também pode criar um link simbólico chamado `mod_dispatcher.so` para o Dispatcher:\
      `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copie o arquivo dispatcher.any para o diretório `<APACHE_ROOT>/conf`.

   **Observação:** você pode colocar esse arquivo em um local diferente, desde que a propriedade DispatcherLog do módulo Dispatcher esteja configurada adequadamente. (Consulte Entradas de configuração específicas do Dispatcher abaixo.)

### Apache Web Server - Configurar propriedades do SELinux {#apache-web-server-configure-selinux-properties}

Se você estiver executando o Dispatcher no RedHat Linux Kernel 2.6 com SELinux ativado, poderá encontrar mensagens de erro como essa no arquivo de log do dispatcher.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Isso provavelmente se deve a uma segurança ativada do SELinux. Em seguida, é necessário executar as seguintes tarefas:

* Configure o contexto do SELinux do arquivo de módulo do dispatcher.
* Habilite scripts e módulos HTTPD para fazer conexões de rede.
* Configure o contexto SELinux do docroot, onde os arquivos em cache são armazenados.

Insira os seguintes comandos em uma janela de terminal, substituindo `[path to the dispatcher.so file]` pelo caminho para o módulo Dispatcher que você instalou no Apache Web Server e *`path to the docroot`* pelo caminho onde o docroot está localizado (por exemplo, `/opt/cq/cache`):

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_content_t "[path to the docroot](/.*)?"
```

### Apache Web Server - Configurar o Apache Web Server para Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

O Apache Web Server precisa ser configurado, usando `httpd.conf`. No kit de instalação do Dispatcher, você encontrará um arquivo de configuração de exemplo chamado `httpd.conf.disp<x>`.

Estas etapas são obrigatórias:

1. Vá até `<APACHE_ROOT>/conf`.
1. Abra `httpd.conf`para edição.
1. As seguintes entradas de configuração devem ser adicionadas, na ordem listada:

   * **** LoadModule para carregar o módulo na inicialização.
   * Entradas de configuração específicas do Dispatcher, incluindo **DispatcherConfig, DispatcherLog** e **DispatcherLogLevel**.
   * **** SetHandler para ativar o Dispatcher. **LoadModule**.
   * **** ModMimeUsePathInfoto configura o comportamento de  **mod_mime**.

1. (Opcional) É recomendável alterar o proprietário do diretório htdocs:

   * O servidor apache é iniciado como raiz, embora os processos filho sejam iniciados como daemon (para fins de segurança). O DocumentRoot (`<APACHE_ROOT>/htdocs`) deve pertencer ao daemon do usuário:

      ```xml
      cd <APACHE_ROOT>  
      chown -R daemon:daemon htdocs
      ```

**LoadModule**

A tabela a seguir lista exemplos que podem ser usados; as entradas exatas são de acordo com seu Apache Web Server específico:

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| Unix (presumindo link simbólico) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>O primeiro parâmetro de cada instrução deve ser escrito exatamente como nos exemplos acima.
>
>Consulte os arquivos de configuração de exemplo fornecidos e a documentação do Apache Web Server para obter detalhes completos sobre esse comando.

**Entradas de configuração específicas do Dispatcher**

As entradas de configuração específicas do Dispatcher são colocadas após a entrada LoadModule . A tabela a seguir lista uma configuração de exemplo que é aplicável para Unix e Windows:

**Windows e Unix**

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
| DispatcherConfig | Localização e nome do arquivo de configuração do Dispatcher. <br/>Quando essa propriedade está localizada na configuração do servidor principal, todos os hosts virtuais herdam o valor da propriedade. No entanto, os hosts virtuais podem incluir uma propriedade DispatcherConfig para substituir a configuração do servidor principal. |
| DispatcherLog | Localização e nome do arquivo de log. |
| DispatcherLogLevel | Nível de log do arquivo de log: <br/>0 - Erros <br/>1 - Avisos <br/>2 - Informações <br/>3 - Depurar <br/>**Nota**: É recomendável definir o nível de log como 3 durante a instalação e o teste e depois como 0 durante a execução em um ambiente de produção. |
| DispatcherNoServerHeader | *Esse parâmetro está obsoleto e não tem mais efeito.*<br/><br/> Define o Cabeçalho do Servidor a ser usado:  <br/><ul><li>undefined or 0 - o cabeçalho do servidor HTTP contém a versão AEM. </li><li>1 - o cabeçalho do servidor Apache é usado.</li></ul> |
| DispatcherDeclineRoot | Define se as solicitações devem ser rejeitadas para a raiz &quot;/&quot;: <br/>**0** - aceitar solicitações para / <br/>**1** - solicitações para / não são tratadas pelo dispatcher; use mod_alias para o mapeamento correto. |
| DispatcherUseProcessedURL | Define se os URLs pré-processados devem ser usados para todo o processamento adicional pelo Dispatcher: <br/>**0** - use o URL original transmitido ao servidor da Web. <br/>**1**  - o dispatcher usa o URL já processado pelos manipuladores que precedem o dispatcher (ou seja,  `mod_rewrite`) em vez do URL original enviado ao servidor da Web.  Por exemplo, o URL original ou processado corresponde aos filtros do Dispatcher. O URL também é usado como a base para a estrutura do arquivo de cache.   Consulte a documentação do site do Apache para obter informações sobre mod_rewrite; por exemplo, Apache 2.4. Ao usar mod_rewrite, é aconselhável usar o sinalizador &#39;passthrough&#39; | PT&#39; (passe para o próximo manipulador) para forçar o mecanismo de regravação a definir o campo uri da estrutura interna request_rec para o valor do campo filename . |
| DispatcherPassError | Define como suportar códigos de erro para tratamento de ErrorDocument: <br/>**0** - O Dispatcher detecta todas as respostas de erro para o cliente. <br/>**1**  - O Dispatcher não spool uma resposta de erro para o cliente (onde o código de status é maior ou igual a 400), mas transmite o código de status para o Apache, que por exemplo permite que uma diretiva ErrorDocument processe tal código de status. <br/>**Intervalo de código**  - especifique um intervalo de códigos de erro para o qual a resposta é passada para o Apache. Outros códigos de erro são passados para o cliente. Por exemplo, a configuração a seguir transmite respostas para o erro 412 para o cliente e todos os outros erros são passados para o Apache: DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Especifica o tempo limite de manutenção em funcionamento, em segundos. A partir da versão 4.2.0 do Dispatcher, o valor padrão keep-alive é 60. Um valor de 0 desativa o keep-alive. |
| DispatcherNoCanonURL | Configurar esse parâmetro como Ativado passará o URL bruto para o backend em vez do canonizado e substituirá as configurações de DispatcherUseProcessedURL. O valor padrão é Desativado. <br/>**Observação**: As regras de filtro na configuração do Dispatcher sempre serão avaliadas em relação ao URL limpo, não ao URL bruto. |




>[!NOTE]
>
>As entradas de caminho são relativas ao diretório raiz do Apache Web Server.

>[!NOTE]
>
>As configurações padrão para o Cabeçalho do Servidor são:
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`
>
>Que apresenta a versão AEM (para fins estatísticos). Se desejar desativar a disponibilização dessas informações no cabeçalho, é possível definir:
>
>`ServerTokens Prod`
>
>Consulte a [Documentação do Apache sobre a Diretiva ServerTokens (por exemplo, para o Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) para obter mais informações.

**SetHandler**

Após essas entradas, você deve adicionar uma instrução **SetHandler** ao contexto de sua configuração ( `<Directory>`, `<Location>`) para que o Dispatcher manipule as solicitações recebidas. O exemplo a seguir configura o Dispatcher para lidar com solicitações do site completo:

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
>
>O parâmetro da instrução **SetHandler** deve ser escrito *exatamente como nos exemplos acima*, pois este é o nome do manipulador definido no módulo.
>
>Consulte os arquivos de configuração de exemplo fornecidos e a documentação do Apache Web Server para obter detalhes completos sobre esse comando.

**ModMimeUsePathInfo**

Depois da instrução **SetHandler**, você também deve adicionar a definição **ModMimeUsePathInfo**.

>[!NOTE]
>
>O parâmetro `ModMimeUsePathInfo` só deve ser usado e configurado se você estiver usando o Dispatcher versão 4.0.9 ou superior.
>
>(Observe que a versão 4.0.9 do Dispatcher foi lançada em 2011. Se você estiver usando uma versão mais antiga, seria apropriado atualizar para uma versão recente do Dispatcher).

O parâmetro **ModMimeUsePathInfo** deve ser definido `On` para todas as configurações do Apache:

`ModMimeUsePathInfo On`

O módulo mod_mime (consulte, por exemplo, [Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) é usado para atribuir metadados de conteúdo ao conteúdo selecionado para uma resposta HTTP. A configuração padrão significa que quando mod_mime determina o tipo de conteúdo, somente a parte do URL que mapeia para um arquivo ou diretório será considerada.

Quando `On`, o parâmetro `ModMimeUsePathInfo` especifica que `mod_mime` é para determinar o tipo de conteúdo com base na URL *complete*; isso significa que os recursos virtuais terão metainformações aplicadas com base em sua extensão.

O exemplo a seguir ativa **ModMimeUsePathInfo**:

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

O Dispatcher usa o OpenSSL para implementar comunicação segura via HTTP. A partir da versão do Dispatcher **4.2.0**, há suporte para OpenSSL 1.0.0 e OpenSSL 1.0.1. O Dispatcher usa o OpenSSL 1.0.0 por padrão. Para usar o OpenSSL 1.0.1, use o seguinte procedimento para criar links simbólicos para que o Dispatcher use as bibliotecas OpenSSL instaladas.

1. Abra um terminal e altere o diretório atual para o diretório onde as bibliotecas OpenSSL estão instaladas, por exemplo:

   ```shell
   cd /usr/lib64
   ```

1. Para criar links simbólicos, insira os seguintes comandos:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>Se estiver usando uma versão personalizada do Apache, verifique se o Apache e o Dispatcher estão compilados usando a mesma versão de [OpenSSL](https://www.openssl.org/source/).

### Próximas etapas {#next-steps-1}

Antes de começar a usar o Dispatcher, agora é necessário:

* [](dispatcher-configuration.md) ConfigureDispatcher
* [Configure o ](page-invalidate.md) AEM para funcionar com o Dispatcher.

## Servidor Web Sun Java System / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>As instruções para ambientes Windows e Unix são abordadas aqui.
>
>Tenha cuidado ao selecionar qual executar.

### Sun Java System Web Server / iPlanet - Instalando seu servidor da Web {#sun-java-system-web-server-iplanet-installing-your-web-server}

Para obter informações completas sobre como instalar esses servidores da Web, consulte a respectiva documentação:

* Servidor Web Sun Java System
* Servidor Web iPlanet

### Sun Java System Web Server / iPlanet - Adicionar o módulo Dispatcher {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

O Dispatcher vem como:

* **Windows**: uma DLL (Dynamic Link Library)
* **Unix**: um DSO (Dynamic Shared Object, objeto compartilhado dinâmico)

Os arquivos de arquivamento de instalação contêm os seguintes arquivos - dependendo se você selecionou Windows ou Unix:

| Arquivo | Descrição |
|---|---|
| `disp_ns.dll` | Windows: O arquivo da biblioteca de links dinâmicos do Dispatcher. |
| `dispatcher.so` | Unix: O arquivo da biblioteca de objetos compartilhados do Dispatcher. |
| `dispatcher.so` | Unix: Um link de exemplo. |
| `obj.conf.disp` | Um exemplo de arquivo de configuração para o servidor Web iPlanet / Sun Java System. |
| `dispatcher.any` | Um exemplo de arquivo de configuração para o Dispatcher. |
| LEITURA | Arquivo Leia-me que contém instruções de instalação e informações de última hora. Observação: Verifique este arquivo antes de iniciar a instalação. |
| ALTERAÇÕES | Altera o arquivo que lista os problemas corrigidos nas versões atual e anterior. |

Use as seguintes etapas para adicionar o Dispatcher ao seu servidor da Web:

1. Coloque o arquivo Dispatcher no diretório `plugin` do servidor da Web:

### Sun Java System Web Server / iPlanet - Configurar para o Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

O servidor da Web precisa ser configurado, usando `obj.conf`. No kit de instalação do Dispatcher, você encontrará um arquivo de configuração de exemplo chamado `obj.conf.disp`.

1. Vá até `<WEBSERVER_ROOT>/config`.
1. Abra `obj.conf`para edição.
1. Copie a linha que começa:\
   `Service fn="dispService"`\
   de `obj.conf.disp` à seção de inicialização de `obj.conf`.

1. Salve as alterações.
1. Abra `magnus.conf` para edição.
1. Copie as duas linhas que iniciam:\
   `Init funcs="dispService, dispInit"`\
   e\
   `Init fn="dispInit"`\
   de `obj.conf.disp` à seção de inicialização de `magnus.conf`.

1. Salve as alterações.

>[!NOTE]
>
>As configurações a seguir devem estar em uma linha e as `$(SERVER_ROOT)` e `$(PRODUCT_SUBDIR)` devem ser substituídas pelos respectivos valores.

**Inicializar**

A tabela a seguir lista exemplos que podem ser usados; as entradas exatas são de acordo com seu servidor da Web específico:

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
| configuração | Localização e nome do arquivo de configuração `dispatcher.any.` |
| arquivo de log | Localização e nome do arquivo de log. |
| loglevel | Nível de log para ao gravar mensagens no arquivo de log: <br/>**0** Erros <br/>**1** Avisos <br/>**2** Informações <br/>**3** Depuração <br/>**Observação:** É recomendável definir o nível de log como 3 durante a instalação e o teste e como 0 quando executado em um ambiente de produção. |
| keepalivetimeout | Especifica o tempo limite de manutenção em funcionamento, em segundos. A partir da versão 4.2.0 do Dispatcher, o valor padrão keep-alive é 60. Um valor de 0 desativa o keep-alive. |

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

Antes de começar a usar o Dispatcher, agora é necessário:

* [](dispatcher-configuration.md) ConfigureDispatcher
* [Configure o ](page-invalidate.md) AEM para funcionar com o Dispatcher.
