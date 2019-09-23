---
title: Notas de versão do AEM Dispatcher
seo-title: Notas de versão do AEM Dispatcher
description: Release notes specific to Adobe Experience Manager Dispatcher
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: notas de versão
content-type: referência
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: 2d72839d459973cba40f6a938ee157198c7cf50a

---


# Notas de versão do AEM Dispatcher{#aem-dispatcher-release-notes}

## Informações da versão {#release-information}

|  |  |
|--- |--- |
| Produtos | Adobe Experience Manager (AEM) Dispatcher |
| Versão | 4.3.2 |
| Tipo | Versão secundária |
| Data | 31 de janeiro de 2019 |
| URL de download | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilidade | AEM 6.1 ou superior |

## Requisitos e pré-requisitos do sistema {#system-requirements-and-prerequisites}

Consulte a página Plataformas [](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) suportadas para obter mais informações sobre requisitos e pré-requisitos.

A Adobe recomenda usar a versão mais recente do AEM Dispatcher para aproveitar a funcionalidade mais recente, as correções de erros mais recentes e o melhor desempenho possível.

## Instruções de instalação {#installation-instructions}

Para obter instruções detalhadas, consulte [Instalação do Dispatcher](dispatcher-install.md).

## Histórico da versão {#release-history}

### Versão 4.3.2 (31 de janeiro de 2019) {#jan}

**Correções** de erros:

* DISP-734 - O Dispatcher causa falha em insert_output_filter se não estiver definido como handler
* DISP-735 - Os REs não funcionam no Linux Alpino
* DISP-740 - O envio do dispatcher no macOS Mojave está desativado por padrão
* DISP-742 - Solicitações bloqueadas podem vazar informações para os recursos protegidos do verificador automático

**Melhorias**:

* DISP-746 - As strings não rotuladas no dispatcher.any devem gerar um aviso

**Novo recurso**:

* DISP-747 - Fornecer informações de solicitação no ambiente Apache

### Versão 4.3.1 (16 de outubro de 2018) {#oct}

**Correções** de erros:

* DISP-656 - O Dispatcher serve um cabeçalho ETag incorreto
* DISP-694 - Suprimir avisos quando manter as conexões ativas obsoletas
* DISP-714 - O gerenciamento de sessões baseado em cookies não funciona no IIS
* DISP-715 - Secure flag for renderid cookie
* DISP-720 - Temporary files not closed, can lead to exhaustion (too many open files)
* DISP-721 - Dispatcher interrupts poll() when Apache gracefully restarts child
* DISP-722 - Cache files are created with octal mode 0600
* DISP-723 - Implicit 10 minute timeout (and retry) when Render timeouts set to 0
* DISP-725 - Trailing characters after strings are silently converted to unnamed value
* DISP-726 - Log a warning when no farm actually matches the incoming host
* DISP-727 - Dispatcher checks request content length for empty cache files
* DISP-730 - 404 when trying to access header file over dispatcher
* DISP-731 - Dispatcher is vulnerable to Log Injection
* DISP-732 - Dispatcher should remove consecutive ‘/’ in the URL
* DISP-733 - Dispatcher should set (calculate) an Age Header

**Improvements:**

* DISP-656 - Dispatcher serves wrong ETag Header
* DISP-694 - Suppress warnings when keep alive connections go stale
* DISP-715 - Sinalizador seguro para cookie renderid
* DISP-722 - Arquivos de cache são criados no modo octal 0600
* DISP-726 - Registre um aviso quando nenhum farm realmente corresponder ao host recebido

### Versão 4.3.0 (2018-jun-13) {#jun}

**Correções** de erros:

* DISP-682 - Nível de log numérico aplicado incorretamente
* DISP-685 - Binários SPARC Solaris de 32 bits têm uma referência indefinida a __divdi3
* DISP-688 - O Dispatcher não retorna o cabeçalho "X-Cache-Info" na resposta 404
* DISP-690 - O cabeçalho da última modificação não pode ser armazenado em cache
* DISP-691 - Violações de acesso em w3wp.exe
* DISP-693 - Necessidade de atualizar os detalhes da arquitetura para servidores solaris na página de download do dispatcher
* DISP-695 - Problema com o nível DispatcherLog no módulo Dispatcher 4.2.3
* DISP-698 - O Dispatcher TTL precisa suportar diretivas s-maxage e privadas
* DISP-700 - O módulo não funciona corretamente no Linux Alpino
* DISP-704 - As solicitações do navegador contendo %2b são enviadas para o Editor sem codificação
* DISP-705 - Falha do Dispatcher devido a dois tipos de ausência ou corrupção (fixador)
* DISP-706 - Durante a invalidação, o dispatcher segue links simbólicos de referência traseiros que podem causar um loop infinito
* DISP-709 - Bloquear algumas extensões vanity URL
* DISP-710 - Compilações para Linux inutilizáveis no Cent OS 6

**Melhorias**:

* DISP-652 - O Dispatcher serve o cabeçalho Date incorreto

## Recursos úteis {#helpful-resources}

* [Visão geral do AEM Dispatcher](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Plataforma | Arquitetura | Suporte SSL | Download |
|---|---|---|---|
| AIX | PowerPC (32 bits) | Não | [dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.2.tar.gz) |
| AIX | PowerPC (32-bit) | Sim | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.2.tar.gz) |
| AIX | PowerPC (64-bit) | Não | [dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.2.tar.gz) |
| AIX | PowerPC (64-bit) | Sim | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.2.tar.gz) |
| Linux | i686 (32-bit) | Não | [dispatcher-apache2.4-linux-i686-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.2.tar.gz) |
| Linux | i686 (32 bits) | Sim | [dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.2.tar.gz) |
| Linux | x86_64 (64-bit) | Não | [dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.2.tar.gz) |
| Linux | x86_64 (64-bit) | Sim | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.2.tar.gz) |
| macOS | x86_64 (64-bit) | Não | [dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Não | [dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.2.tar.gz) |
| Solaris | AMD (32 bits) | Sim | [dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Não | [dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.2.tar.gz) |
| Solaris | AMD (64 bits) | Sim | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (32-bit) | Não | [dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.2.tar.gz) |
| Solaris | SPARC (32 bits) | Sim | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.2.tar.gz) |
| Solaris | SPARC (64-bit) | Não | [dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.2.tar.gz) |
| Solaris | SPARC (64 bits) | Sim | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.2.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitetura | Suporte SSL | Download |
|---|---|---|---|
| Windows | x86 (32 bits) | Não | [dispatcher-is-windows-x86-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.2.zip) |
| Windows | x86 (32 bits) | Sim | [dispatcher-iis-windows-x86-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.2.zip) |
| Windows | x64 (64 bits) | Não | [dispatcher-is-windows-x64-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.2.zip) |
| Windows | x64 (64 bits) | Sim | [dispatcher-is-windows-x64-ssl-4.3.2.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.2.zip) |
