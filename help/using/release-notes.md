---
title: 'Notas de lançamento do AEM Dispatcher Release '
seo-title: 'Notas de lançamento do AEM Dispatcher Release '
description: Notas de versão específicas do Adobe Experience Manager Dispatcher
seo-description: Notas de versão específicas do Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: notas de versão
content-type: referência
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
translation-type: tm+mt
source-git-commit: d0b646c718e2057b5f18b1b91391fa50ea4a9248

---


# Notas de lançamento do AEM Dispatcher Release {#aem-dispatcher-release-notes}

## Informações da versão {#release-information}

|  |  |
|--- |--- |
| Produtos | Adobe Experience Manager (AEM) Dispatcher |
| Versão | 4.3.3 |
| Tipo | Versão secundária |
| Data | 18 de outubro de 2019 |
| URL de download | <ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul> |
| Compatibilidade | AEM 6.1 ou superior |

## Requisitos e pré-requisitos do sistema {#system-requirements-and-prerequisites}

Consulte a página Plataformas [](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html) suportadas para obter mais informações sobre requisitos e pré-requisitos.

A Adobe recomenda usar a versão mais recente do AEM Dispatcher para aproveitar a funcionalidade mais recente, as correções de erros mais recentes e o melhor desempenho possível.

## Instruções de instalação {#installation-instructions}

Para obter instruções detalhadas, consulte [Instalação do Dispatcher](dispatcher-install.md).

## Histórico da versão {#release-history}

### Versão 4.3.3 (18 de outubro de 2019) {#october}

**Correções** de erros:

* DISP-739 - Despachante LogLevel: **nível** não funciona
* DISP-749 - O dispatcher Linux alpino trava com o nível de log de rastreamento

**Melhorias**:

* DISP-813 - Suporte no Dispatcher para o openssl 1.1.x
* DISP-814 - Erros do Apache 40x durante o descarregamento do cache
* DISP-818 - mod_expire adiciona cabeçalhos Cache-Control para conteúdo inacessível
* DISP-821 - Não armazenar contexto de registro no soquete
* DISP-822 - O Dispatcher deve usar a pesquisa em vez de selecionar
* DISP-824 - DispatcherSecureUseForwardedHost
* DISP-825 - Registrar mensagem especial quando não houver mais espaço no disco
* DISP-826 - Suporte para rebusca de URIs com uma string de consulta

**Novos recursos**:

* DISP-703 - Taxa de acertos de cache específico do farm
* DISP-827 - Servidor local para teste
* DISP-828 - Criar imagem do docker de teste para o dispatcher

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
* DISP-715 - Sinalizador seguro para cookie renderid
* DISP-720 - Arquivos temporários não fechados, podem levar à exaustão (muitos arquivos abertos)
* DISP-721 - O Dispatcher interrompe o poll() quando o Apache reinicia normalmente o filho
* DISP-722 - Arquivos de cache são criados no modo octal 0600
* DISP-723 - Tempo limite implícito de 10 minutos (e tentar novamente) quando o tempo limite de renderização estiver definido como 0
* DISP-725 - Caracteres posteriores são silenciosamente convertidos em valor sem nome
* DISP-726 - Registre um aviso quando nenhum farm realmente corresponder ao host recebido
* DISP-727 - O Dispatcher verifica a duração do conteúdo da solicitação para arquivos de cache vazios
* DISP-730 - 404 ao tentar acessar o arquivo de cabeçalho pelo dispatcher
* DISP-731 - O Dispatcher está vulnerável a Log Injection
* DISP-732 - O Dispatcher deve remover "/" consecutivos no URL
* DISP-733 - O Dispatcher deve definir (calcular) um Cabeçalho de Idade

**Melhorias**:

* DISP-656 - O Dispatcher serve um cabeçalho ETag incorreto
* DISP-694 - Suprimir avisos quando manter as conexões ativas obsoletas
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

| Plataforma | Arquitetura | SSL | Download |
|---|---|---|---|
| Linux | i686 (32 bits) | Nenhum | [dispatcher-apache2.4-linux-i686-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.3.tar.gz) |
| Linux | i686 (32 bits) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz) |
| Linux | i686 (32 bits) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | Nenhum | [dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz) |
| Linux | x86_64 (64 bits) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz) |
| macOS | x86_64 (64 bits) | Nenhum | [dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitetura | SSL | Download |
|---|---|---|---|
| Windows | x86 (32 bits) | Nenhum | [dispatcher-is-windows-x86-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.0 | [dispatcher-is-windows-x86-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip) |
| Windows | x86 (32 bits) | 1.1 | [dispatcher-is-windows-x86-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip) |
| Windows | x64 (64 bits) | Nenhum | [dispatcher-is-windows-x64-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.3.zip) |
| Windows | x64 (64 bits) | 1.0 | [dispatcher-is-windows-x64-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip) |
| Windows | x64 (64 bits) | 1.1 | [dispatcher-is-windows-x64-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip) |
