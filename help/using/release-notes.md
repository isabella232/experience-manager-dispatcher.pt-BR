---
title: 'Notas de versão do AEM Dispatcher '
seo-title: Notas de versão do AEM Dispatcher
description: Notas de versão específicas do Adobe Experience Manager Dispatcher
seo-description: Notas de versão específicas do Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
source-git-commit: 3a0e237278079a3885e527d7f86989f8ac91e09d
workflow-type: ht
source-wordcount: '805'
ht-degree: 100%

---

# Notas de versão do AEM Dispatcher{#aem-dispatcher-release-notes}

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

Consulte a página [Plataformas compatíveis](https://helpx.adobe.com/br/experience-manager/6-4/sites/deploying/using/technical-requirements.html) para obter mais informações sobre os requisitos e pré-requisitos.

A Adobe recomenda o uso da versão mais recente do AEM Dispatcher para aproveitar os recursos e as correções de erros mais recentes e o melhor desempenho possível.

## Instruções de instalação {#installation-instructions}

Para obter instruções detalhadas, consulte [Instalação do Dispatcher](dispatcher-install.md).

## Histórico da versão {#release-history}

### Versão 4.3.3 (18 de outubro de 2019) {#october}

**Correções de erros**:

* DISP-739 - Dispatcher LogLevel: **nível** não funciona
* DISP-749 - O dispatcher Alpine Linux trava com o nível de log de rastreamento

**Melhorias**:

* DISP-813 - Suporte no Dispatcher para openssl 1.1.x
* DISP-814 - Erros 40x do Apache durante liberações de cache
* DISP-818 - O mod_expires adiciona cabeçalhos de controle de cache para conteúdo não armazenável em cache
* DISP-821 - Não armazena contexto de log no soquete
* DISP-822 - O Dispatcher deve usar ppoll em vez de pselect
* DISP-824 - DispatcherUseForwardedHost protegido
* DISP-825 - Registra mensagem especial quando não há mais espaço no disco
* DISP-826 - Suporte a URIs de rebusca com uma cadeia de caracteres de consulta

**Novos recursos**:

* DISP-703 - Taxa de acertos de cache específico do Farm
* DISP-827 - Servidor local para teste
* DISP-828 - Criação de imagem de docker de teste para dispatcher

### Versão 4.3.2 (31 de janeiro de 2019) {#jan}

**Correções de erros**:

* DISP-734 - O Dispatcher causa falha no insert_output_filter se não estiver definido como manipulador
* DISP-735 - Os REs não funcionam no Alpine Linux
* DISP-740 - O carregamento do dispatcher no macOS Mojave está desativado por padrão
* DISP-742 - As solicitações bloqueadas podem vazar informações para os recursos protegidos do auth checker.

**Melhorias**:

* DISP-746 - As cadeias de caracteres não rotuladas no dispatcher.any devem gerar um aviso

**Novo recurso**:

* DISP-747 - Fornecimento de informações de solicitação no ambiente do Apache

### Versão 4.3.1 (16 de outubro de 2018) {#oct}

**Correções de erros**:

* DISP-656 - O Dispatcher apresenta um cabeçalho ETag incorreto
* DISP-694 - Suprime avisos quando conexões keep-alive se tornam obsoletas
* DISP-714 - O gerenciamento de sessões baseado em cookies não funciona no IIS
* DISP-715 - Sinalizador seguro para cookie renderid
* DISP-720 - Arquivos temporários não fechados, que podem levar à exaustão (muitos arquivos abertos)
* DISP-721 - O Dispatcher interrompe o poll() quando o Apache reinicia normalmente o filho
* DISP-722 - Arquivos de cache são criados com o modo octal 0600
* DISP-723 - Tempo limite de 10 minutos implícito (e “Tente novamente”) quando o tempo limite de renderização está definido como 0
* DISP-725 - Caracteres finais de cadeias de caracteres são silenciosamente convertidos em valor não nomeado
* DISP-726 - Registra um aviso quando nenhum farm corresponde ao host de entrada
* DISP-727 - O Dispatcher verifica a duração do conteúdo da solicitação para arquivos de cache vazios
* DISP-730 - 404 ao tentar acessar o arquivo de cabeçalho pelo dispatcher
* DISP-731 - O Dispatcher é vulnerável à Injeção de Log
* DISP-732 - O Dispatcher deveria remover ‘/’ consecutivos do URL
* DISP-733 - O Dispatcher deveria definir (calcular) um Cabeçalho de idade

**Melhorias**:

* DISP-656 - O Dispatcher apresenta um cabeçalho ETag incorreto
* DISP-694 - Suprime avisos quando conexões keep-alive se tornam obsoletas
* DISP-715 - Sinalizador seguro para cookie renderid
* DISP-722 - Arquivos de cache são criados com o modo octal 0600
* DISP-726 - Registra um aviso quando nenhum farm corresponde ao host de entrada

### Versão 4.3.0 (13 de junho de 2018) {#jun}

**Correções de erros**:

* DISP-682 - Nível de log numérico aplicado incorretamente
* DISP-685 - Os binários SPARC Solaris de 32 bits têm uma referência indefinida a __divdi3
* DISP-688 - O Dispatcher não retorna o cabeçalho &quot;X-Cache-Info&quot; na resposta 404
* DISP-690 - O cabeçalho que foi modificado por último não pode ser armazenado em cache
* DISP-691 - Violações de acesso em w3wp.exe
* DISP-693 - Precisa atualizar detalhes de arquitetura para servidores Solaris na página de download do Dispatcher
* DISP-695 - Problema com o nível do DispatcherLog no módulo Dispatcher 4.2.3
* DISP-698 - O Dispatcher TTL precisa suportar diretivas s-maxage e privadas
* DISP-700 - O módulo não funciona corretamente no Alpine Linux
* DISP-704 - As solicitações do navegador contendo %2b são enviadas para o Editor não codificadas
* DISP-705 - Falha do Dispatcher devido a double free or corruption (fasttop)
* DISP-706 - Durante a invalidação, o Dispatcher volta a seguir links simbólicos de referência, o que pode causar um loop infinito
* DISP-709 - Bloqueia algumas extensões personalizadas de URL
* DISP-710 - Builds para Linux não utilizáveis no Cent OS 6

**Melhorias**:

* DISP-652 - O Dispatcher apresenta cabeçalho de data incorreto

## Recursos úteis {#helpful-resources}

* [Visão geral do AEM Dispatcher](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Plataforma | Arquitetura | Compatível com OpenSSL | Download |
|---|---|---|---|
| Linux | i686 (32-bit) | Nenhum | [dispatcher-apache2.4-linux-i686-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.3.tar.gz) |
| Linux | i686 (32-bit) | 1.0 | [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.3.tar.gz) |
| Linux | i686 (32-bit) | 1.1 | [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.3.tar.gz) |
| Linux | x86_64 (64-bit) | Nenhum | [dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.3.tar.gz) |
| Linux | x86_64 (64-bit) | 1.0 | [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.3.tar.gz) |
| Linux | x86_64 (64-bit) | 1.1 | [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.3.tar.gz) |
| macOS | x86_64 (64-bit) | Nenhum | [dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.3.tar.gz) |

### IIS {#iis}

| Plataforma | Arquitetura | Compatível com OpenSSL | Download |
|---|---|---|---|
| Windows | x86 (32-bit) | Nenhum | [dispatcher-iis-windows-x86-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.3.zip) |
| Windows | x86 (32-bit) | 1.0 | [dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.3.zip) |
| Windows | x86 (32-bit) | 1.1 | [dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.3.zip) |
| Windows | x64 (64-bit) | Nenhum | [dispatcher-iis-windows-x64-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.3.zip) |
| Windows | x64 (64-bit) | 1.0 | [dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.3.zip) |
| Windows | x64 (64-bit) | 1.1 | [dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.3.zip) |
