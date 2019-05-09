---
title: Notas de versão do AEM Dispatcher
seo-title: Notas de versão do AEM Dispatcher
description: Notas de versão específicas do Adobe Experience Manager Dispatcher
seo-description: Notas de versão específicas do Adobe Experience Manager Dispatcher
uuid: ae 3 ccf 62-0514-4 c 03-a 3 b 9-71799 a 482 cbd
topic-tags: notas-notas
content-type: referência
products: SG_ EXPERIENCEMANAGER/6.4
discoiquuid: ff 3 d 38 e 0-71 c 9-4 b 41-85 f 9-fa 896393 aac 5
translation-type: tm+mt
source-git-commit: 2d72839d459973cba40f6a938ee157198c7cf50a

---


# Notas de versão do AEM Dispatcher{#aem-dispatcher-release-notes}

## Informações da versão {#release-information}

|  |
|--- |--- |
| Produtos | Dispatcher do Adobe Experience Manager (AEM) |
| Versão | 4.3.2 |
| Tipo | Versão secundária |
| Data | January 1 de janeiro de 21 19 |
| URL de download | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilidade | AEM 6.1 ou superior |

## Requisitos de sistema e pré-requisitos {#system-requirements-and-prerequisites}

Consulte a página [Plataformas](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) compatíveis para obter mais informações sobre os requisitos e pré-requisitos.

A Adobe recomenda usar a versão mais recente do AEM Dispatcher para aproveitar a funcionalidade mais recente, as correções de erros mais recentes e o melhor desempenho possível.

## Instruções de instalação {#installation-instructions}

Para obter instruções detalhadas, consulte [Instalação do Dispatcher](dispatcher-install.md).

## Histórico de versões {#release-history}

### Versão 4.3.2 (2019-Jan -31) {#jan}

**Correções de erros**:

* DISP -734 - O Dispatcher causa falha em insert_ output_ filter se não estiver definido como manipulador
* DISP -735 - REs não funcionam no Linux Alpux
* DISP -740 - O carregamento do dispatcher no macos Mojave está desativado por padrão
* DISP -742 - As solicitações bloqueadas podem eliminar informações aos recursos protegidos pelo verificador de autenticação

**Melhorias**:

* DISP -746 - Strings não rotuladas no dispatcher. Qualquer um deve gerar um aviso

**Novo recurso**:

* DISP -747 - Fornecer informações de solicitação no ambiente Apache

### Versão 4.3.1 (2018-Out -16) {#oct}

**Correções de erros**:

* DISP -656 - Dispatcher serve um cabeçalho de etag incorreto
* DISP -694 - Suprimir avisos quando manter conexões vivas
* DISP -714 - O gerenciamento de sessão baseado em cookies não funciona no IIS
* DISP -715 - Sinalizador seguro para cookie renderid
* DISP -720 - Arquivos temporários não fechados podem levar à alteração (muitos arquivos abertos)
* DISP -721 - O Dispatcher interrompe a pesquisa () quando o Apache reinicia o filho
* DISP -722 - Arquivos cache são criados com modo octal 0600
* DISP -723 - Tempo limite de 10 minutos implícito (e tente novamente) quando o tempo limite da renderização for definido como 0
* DISP -725 - Caracteres de chave depois de strings são convertidos silenciosamente em valor não nomeado
* DISP -726 - Registra um aviso quando nenhum farm corresponde ao host recebido
* DISP -727 - O Dispatcher verifica a duração do conteúdo da solicitação para arquivos vazios do cache
* DISP -730 - 404 ao tentar acessar o arquivo de cabeçalho por meio do expedidor
* DISP -731 - O Dispatcher está vulnerável à injeção de log
* DISP -732 - O Dispatcher deve remover &quot;/&quot; no URL
* DISP -733 - O Dispatcher deve definir (calcular) um cabeçalho de idade

**Melhorias**:

* DISP -656 - Dispatcher serve um cabeçalho de etag incorreto
* DISP -694 - Suprimir avisos quando manter conexões vivas
* DISP -715 - Sinalizador seguro para cookie renderid
* DISP -722 - Arquivos cache são criados com modo octal 0600
* DISP -726 - Registra um aviso quando nenhum farm corresponde ao host recebido

### Versão 4.3.0 (2018-Jun -13) {#jun}

**Correções de erros**:

* DISP -682 - Nível de log numérico aplicado incorretamente
* DISP -685 - Os binários Solaris do Solaris de 32 bits têm uma referência indefinida para__ divdi 3
* DISP -688 - O Dispatcher não retorna o cabeçalho «X-Cache-Info» na resposta 404
* DISP -690 - O cabeçalho modificado pela última vez não pode ser armazenado em cache
* DISP -691 - Acesso de violações em w 3 wp. exe
* DISP -693 - Precisa atualizar os detalhes de arquitetura para servidores solaris na página de download do dispatcher
* DISP -695 - Problema com o nível de dispatcherlog no módulo Dispatcher 4.2.3
* DISP -698 - O Dispatcher TTL precisa suportar as diretivas de s-maxage e privadas
* DISP -700 - Módulo não funciona corretamente no Linux Alpux
* DISP -704 - Solicitações do navegador contendo % 2 b são enviadas para o Editor não codificado
* DISP -705 - Falha do Dispatcher devido a um duplo ou corrupção (fasttop)
* DISP -706 - Durante a invalidação, o dispatcher está seguindo novamente os sincronizações de referência que podem causar um loop infinito
* DISP -709 - Bloquear algumas extensões vanity URL
* DISP -710 - Builds para Linux não utilizável no Centavos OS 6

**Melhorias**:

* DISP -652 - O Dispatcher serve um cabeçalho de data incorreto

## Recursos úteis {#helpful-resources}

* [Visão geral do AEM Dispatcher](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Plataforma | Arquitetura | Suporte para SSL | Download |
|---|---|---|---|
| AIX | Powerpc (32 bits) | Não | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | Powerpc (32 bits) | Sim | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | Powerpc (64 bits) | Não | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | Powerpc (64 bits) | Sim | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i 686 (32 bits) | Não | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i 686 (32 bits) | Sim | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 bits) | Não | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x 86_ 64 (64 bits) | Sim | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| macOS | x 86_ 64 (64 bits) | Não | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Não | [dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Sim | [dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Não | [dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Sim | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | Não | [dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | Sim | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | Não | [dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | Sim | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitetura | Suporte para SSL | Download |
|---|---|---|---|
| Windows | x 86 (32 bits) | Não | [dispatcher-iis-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x 86 (32 bits) | Sim | [dispatcher-iis-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x 64 (64 bits) | Não | [dispatcher-iis-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x 64 (64 bits) | Sim | [dispatcher-iis-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
