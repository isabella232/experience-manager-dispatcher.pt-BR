---
title: Utilização de SSL com Dispatcher
seo-title: Utilização de SSL com Dispatcher
description: Saiba como configurar o Dispatcher para se comunicar com o AEM usando conexões SSL.
seo-description: Saiba como configurar o Dispatcher para se comunicar com o AEM usando conexões SSL.
uuid: 1a8f448c-d3d8-4798-a5cb-9579171171ed
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: 771cfd85-6c26-4ff2-a3fe-dff8d8f7920b
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: eed7c3f77ec64f2e7c5cfff070ef96108886a059

---


# Utilização de SSL com Dispatcher {#using-ssl-with-dispatcher}

Use conexões SSL entre o Dispatcher e o computador de renderização:

* [SSL unidirecional](dispatcher-ssl.md#main-pars-title-1)
* [SSL mútuo](dispatcher-ssl.md#main-pars-title-2)

>[!NOTE]
>
>As operações relacionadas aos certificados SSL estão vinculadas a produtos de terceiros. Eles não são cobertos pelo contrato de manutenção e suporte do Adobe Platinum.

## Usar SSL quando o Dispatcher se conecta ao AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configure o Dispatcher para se comunicar com a instância de renderização do AEM ou CQ usando conexões SSL.

Antes de configurar o Dispatcher, configure o AEM ou o CQ para usar o SSL:

* AEM 6.2: [Ativando HTTP por SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Ativando HTTP por SSL](https://docs.adobe.com/content/docs/en/aem/6-1/deploy/configuring/config-ssl.html)
* Versões anteriores do AEM: consulte [esta página](https://helpx.adobe.com/experience-manager/aem-previous-versions.html).

### Cabeçalhos de solicitação relacionados a SSL {#ssl-related-request-headers}

Quando o Dispatcher recebe uma solicitação HTTPS, o Dispatcher inclui os seguintes cabeçalhos na solicitação subsequente que ele envia para o AEM ou CQ:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Uma solicitação por meio do Apache-2.4 com cabeçalhos `mod_ssl` semelhantes ao seguinte exemplo:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configurando o Dispatcher para Usar SSL {#configuring-dispatcher-to-use-ssl}

Para configurar o Dispatcher para se conectar ao AEM ou CQ sobre SSL, seu arquivo [dispatcher.any](dispatcher-configuration.md) requer as seguintes propriedades:

* Um host virtual que lida com solicitações HTTPS.
* A `renders` seção do host virtual inclui um item que identifica o nome do host e a porta da instância CQ ou AEM que usa HTTPS.
* O `renders` item inclui uma propriedade chamada `secure` de valor `1`.

Observação: Crie outro host virtual para manipular solicitações HTTP, se necessário.

O exemplo dispatcher.any file mostra os valores de propriedade para conexão usando HTTPS a uma instância CQ que está sendo executada no host `localhost` e na porta `8443`:

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requestss
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "https://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Configuração do SSL mútuo entre o Dispatcher e o AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Configure as conexões entre o Dispatcher e o computador de renderização (normalmente uma instância de publicação de AEM ou CQ) para usar o SSL mútuo:

* O Dispatcher se conecta à instância de renderização por SSL.
* A instância de renderização verifica a validade do certificado do Dispatcher.
* O Dispatcher verifica se a CA do certificado da instância de renderização é confiável.
* (Opcional) O Dispatcher verifica se o certificado da instância de renderização corresponde ao endereço do servidor da instância de renderização.

Para configurar o SSL mútuo, você precisa de certificados assinados por uma autoridade de certificação (CA) confiável. Certificados autoassinados não são adequados. Você pode agir como CA ou usar os serviços de uma CA de terceiros para assinar seus certificados. Para configurar o SSL mútuo, você precisa dos seguintes itens:

* Certificados assinados para a instância de renderização e Dispatcher
* O certificado CA (se você estiver atuando como a CA)
* Bibliotecas OpenSSL para gerar a CA, certificados e solicitações de certificado.

Execute as seguintes etapas para configurar o SSL mútuo:

1. [Instale](dispatcher-install.md) a versão mais recente do Dispatcher para sua plataforma. Use um binário Dispatcher compatível com SSL (SSL está no nome do arquivo, como dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar).
1. [Crie ou obtenha um certificado](dispatcher-ssl.md#main-pars-title-3) assinado pela CA para o Dispatcher e a instância de renderização.
1. [Crie um armazenamento de chaves que contenha o certificado](dispatcher-ssl.md#main-pars-title-6) de renderização e configure o serviço HTTP da renderização para usá-lo.
1. [Configure o módulo](dispatcher-ssl.md#main-pars-title-4) do servidor Web Dispatcher para SSL mútuo.

### Criando ou Obtendo Certificados Assinados pela CA {#creating-or-obtaining-ca-signed-certificates}

Crie ou obtenha os certificados assinados pela CA que autenticam a instância de publicação e o Dispatcher.

#### Criando sua CA {#creating-your-ca}

Se você estiver atuando como a CA, use o [OpenSSL](https://www.openssl.org/) para criar a Autoridade de certificação que assina os certificados do servidor e do cliente. (Você deve ter as bibliotecas OpenSSL instaladas.) Se você estiver usando uma CA de terceiros, não execute este procedimento.

1. Abra um terminal e altere o diretório atual para o diretório que contém o arquivo CA.sh, como `/usr/local/ssl/misc`.
1. Para criar a CA, digite o seguinte comando e forneça valores quando solicitado:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Várias propriedades no arquivo openssl.cnf controlam o comportamento do script CA.sh. Você deve modificar esse arquivo conforme necessário antes de criar sua CA.

#### Criação de certificados {#creating-the-certificates}

Use o OpenSSL para criar as solicitações de certificado para enviar à CA de terceiros ou para fazer logon com a CA.

Quando você cria um certificado, o OpenSSL usa a propriedade Common Name para identificar o titular do certificado. Para o certificado da instância de renderização, use o nome de host do computador da instância como o Nome Comum se você estiver configurando o Dispatcher para aceitar o certificado somente se ele corresponder ao nome do host da instância Publicar. (See the [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) property.)

1. Abra um terminal e altere o diretório atual para o diretório que contém o arquivo CH.sh das bibliotecas OpenSSL.
1. Digite o seguinte comando e forneça valores quando solicitado. Se necessário, use o nome de host da instância de publicação como Nome comum. O nome do host é um nome que pode ser resolvido por DNS para o endereço IP da renderização:

   ```shell
   ./CA.sh -newreq
   ```

   Se você estiver usando uma CA de terceiros, envie o arquivo newreq.pem para a CA para assinar. Se você estiver atuando como CA, continue com a etapa 3.

1. Digite o seguinte comando para assinar o certificado usando o certificado da sua CA:

   ```shell
   ./CA.sh -sign
   ```

   Dois arquivos chamados newcert.pem e newkey.pem são criados no diretório que contém os arquivos de gerenciamento da CA. Estes são o certificado público e a chave privada para o computador de renderização, respectivamente.

1. Renomeie newcert.pem como rendercert.pem e renomeie newkey.pem como renderkey.pem.
1. Repita as etapas 2 e 3 para criar um novo certificado e uma nova chave pública para o módulo Dispatcher. Certifique-se de usar um Nome comum que seja específico para a instância do Dispatcher.
1. Renomeie newcert.pem como discert.pem e renomeie newkey.pem como dispkey.pem.

### Configuração do SSL no Computador de renderização {#configuring-ssl-on-the-render-computer}

Configure o SSL na instância de renderização usando os arquivos rendercert.pem e renderkey.pem.

#### Convertendo o certificado de renderização em formato JKS {#converting-the-render-certificate-to-jks-format}

Use o comando a seguir para converter o certificado de renderização, que é um arquivo PEM, em um arquivo PKCS#12. Inclua também o certificado da CA que assinou o certificado de renderização:

1. Em uma janela de terminal, altere o diretório atual para o local do certificado de renderização e da chave privada.
1. Digite o seguinte comando para converter o certificado de renderização, que é um arquivo PEM, em um arquivo PKCS#12. Inclua também o certificado da CA que assinou o certificado de renderização:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Digite o seguinte comando para converter o arquivo PKCS#12 no formato Java KeyStore (JKS):

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. O Java Keystore é criado usando um alias padrão. Altere o alias, se desejar:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Adicionando o certificado CA à Truststore da Renderização {#adding-the-ca-cert-to-the-render-s-truststore}

Se você estiver atuando como CA, importe seu certificado CA para um armazenamento de chaves. Em seguida, configure a JVM que executa a instância de renderização para confiar no armazenamento de chaves.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Use um editor de texto para abrir o arquivo cacert.pem e remover todo o texto que precede a seguinte linha:

   `-----BEGIN CERTIFICATE-----`

1. Use o seguinte comando para importar o certificado para um armazenamento de chaves:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Para configurar a JVM que executa sua instância de renderização para confiar no armazenamento de chaves, use a seguinte propriedade do sistema:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Por exemplo, se você usar o script crx-quickstart/bin/quickstart para iniciar a instância de publicação, poderá modificar a propriedade CQ_JVM_OPTS:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configuração da instância de renderização {#configuring-the-render-instance}

Use o certificado de renderização com as instruções na seção *Ativar SSL na seção Publicar instância* para configurar o serviço HTTP da instância de renderização para usar SSL:

* AEM 6.2: [Ativando HTTP por SSL](https://helpx.adobe.com/experience-manager/6-2/sites/deploying/using/config-ssl.html)
* AEM 6.1: [Ativando HTTP por SSL](https://docs.adobe.com/content/docs/en/aem/6-1/deploy/configuring/config-ssl.html)
* Versões anteriores do AEM: consulte [esta página.](https://helpx.adobe.com/experience-manager/aem-previous-versions.html)

### Configuração do SSL para o módulo Dispatcher {#configuring-ssl-for-the-dispatcher-module}

Para configurar o Dispatcher para usar SSL mútuo, prepare o certificado do Dispatcher e configure o módulo do servidor Web.

### Criando um Certificado de Dispatcher Unificado {#creating-a-unified-dispatcher-certificate}

Combine o certificado do dispatcher e a chave privada não criptografada em um único arquivo PEM. Use um editor de texto ou o `cat` comando para criar um arquivo semelhante ao seguinte exemplo:

1. Abra um terminal e altere o diretório atual para o local do arquivo dispkey.pem.
1. Para descriptografar a chave privada, digite o seguinte comando:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Use um editor de texto ou o `cat` comando para combinar a chave privada não criptografada e o certificado em um único arquivo semelhante ao seguinte exemplo:

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Especificação do certificado a ser usado pelo Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Adicione as seguintes propriedades à configuração [do módulo](dispatcher-install.md#main-pars-55-35-1022) Dispatcher (em `httpd.conf`):

* `DispatcherCertificateFile`: O caminho para o arquivo de certificado unificado do Dispatcher, que contém o certificado público e a chave privada não criptografada. Esse arquivo é usado quando o servidor SSL solicita o certificado do cliente Dispatcher.
* `DispatcherCACertificateFile`: O caminho para o arquivo de certificado da CA, usado se o servidor SSL apresentar uma CA que não seja confiável por uma autoridade raiz.
* `DispatcherCheckPeerCN`: Se você deseja ativar ( `On`) ou desativar ( `Off`) a verificação de nome de host para certificados de servidor remoto.

O código a seguir é um exemplo de configuração:

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```

