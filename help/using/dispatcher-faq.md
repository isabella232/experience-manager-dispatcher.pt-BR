---
title: Problemas principais do Dispatcher
seo-title: Principais problemas do AEM Dispatcher
description: Principais problemas do AEM Dispatcher
seo-description: Principais problemas do Adobe AEM Dispatcher
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Perguntas frequentes sobre os principais problemas do AEM Dispatcher

![Configurando o Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introdução

### O que é o Dispatcher?

O Dispatcher é a ferramenta de cache e/ou balanceamento de carga do Adobe Experience Manager que ajuda a realizar um ambiente de autoria da Web rápido e dinâmico. Para armazenamento em cache, o Dispatcher funciona como parte de um servidor HTTP, como o Apache, com o objetivo de armazenar (ou "armazenar") o máximo possível de conteúdo estático do site e acessar o mecanismo de layout do site com a menor frequência possível. Em uma função de balanceamento de carga, o Dispatcher distribui solicitações de usuário (carga) em diferentes instâncias do AEM (renderizações).

Para armazenamento em cache, o módulo Dispatcher usa a capacidade do servidor Web de fornecer conteúdo estático. O Dispatcher coloca os documentos em cache na raiz do documento do servidor Web.

### Como o Dispatcher executa o cache?

O Dispatcher usa a capacidade do servidor da Web de fornecer conteúdo estático. O Dispatcher armazena documentos em cache na raiz do documento do servidor da Web. O Dispatcher tem dois métodos principais para atualizar o conteúdo do cache quando alterações são feitas no site.

* **As Atualizações** de conteúdo removem as páginas que foram alteradas, bem como os arquivos que estão diretamente associados a elas.
* **A Invalidação** automática invalida automaticamente as partes do cache que podem estar desatualizadas após uma atualização. Por exemplo, ele efetivamente sinaliza páginas relevantes como desatualizadas, sem excluir nada.

### Quais são os benefícios do balanceamento de carga?

O Balanceamento de Carga distribui solicitações de usuário (carga) em várias instâncias do AEM. A lista a seguir descreve as vantagens do balanceamento de carga:

* **Maior poder** de processamento: Na prática, isso significa que o Dispatcher compartilha solicitações de documento entre várias instâncias do AEM. Como cada instância tem menos documentos para processar, você tem tempos de resposta mais rápidos. O Dispatcher mantém estatísticas internas para cada categoria de documento, de modo que ele possa estimar o carregamento e distribuir as consultas com eficiência.
* **Maior cobertura**&#x200B;à prova de falhas: Se o Dispatcher não receber respostas de uma instância, ele retornará automaticamente solicitações para uma das outras instâncias. Assim, se uma instância se tornar indisponível, o único efeito é um abrandamento do site, proporcional à potência computacional perdida.

>[!NOTE]
>
>Para obter mais detalhes, consulte a página Visão geral do [Dispatcher](dispatcher.md)

## Instalar e configurar

### De onde faço download do módulo Dispatcher?

Você pode baixar o módulo Dispatcher mais recente da página Notas [de versão do](release-notes.md) Dispatcher.

### Como instalo o módulo Dispatcher?

Consulte a página [Instalação do Dispatcher](dispatcher-install.md)

### Como configurar o módulo Dispatcher?

Consulte a página [Configurando o Dispatcher](dispatcher-configuration.md) .

### Como configurar o Dispatcher para a instância do autor?

Consulte [Usando o Dispatcher com uma Instância](dispatcher.md#using-a-dispatcher-with-an-author-server) de Autor para obter as etapas detalhadas.

### Como configurar o Dispatcher com vários domínios?

Você pode configurar o CQ Dispatcher com vários domínios, desde que os domínios atendam às seguintes condições:

* O conteúdo da Web para ambos os domínios é armazenado em um único repositório do AEM
* Os arquivos no cache do Dispatcher podem ser invalidados separadamente para cada domínio

Leia [Usando o Dispatcher com Vários Domínios](dispatcher-domains.md) para obter mais detalhes.

### Como faço para configurar o Dispatcher, de modo que todas as solicitações de um usuário sejam encaminhadas para a mesma instância de publicação?

Você pode usar o recurso de conexões [](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) aderentes, que garante que todos os documentos de um usuário sejam processados na mesma instância do AEM. Esse recurso é importante se você usar páginas personalizadas e dados de sessão. Os dados são armazenados na instância. Portanto, as solicitações subsequentes do mesmo usuário devem retornar a essa instância ou os dados são perdidos.

Como as conexões aderentes restringem a capacidade do Dispatcher de otimizar solicitações, você deve usar essa abordagem somente quando necessário. Você pode especificar a pasta que contém os documentos "fixos", garantindo assim que todos os documentos nessa pasta sejam processados na mesma instância para um usuário.

### Posso usar conexões pegajosas e cache em tandem?

Para a maioria das páginas que usam conexões aderentes, você deve desativar o cache. Caso contrário, a mesma instância da página será exibida para todos os usuários, independentemente do conteúdo da sessão.

Para alguns aplicativos, pode ser possível usar conexões adesivas e cache. Por exemplo, se você exibir um formulário que grava dados em uma sessão, poderá usar conexões aderentes e armazenamento em cache em conjunto.

### Um Dispatcher e uma instância do AEM Publish podem residir na mesma máquina física?

Sim, se a máquina for suficientemente poderosa. No entanto, é recomendável configurar o Dispatcher e a instância de publicação de AEM em máquinas diferentes.

Normalmente, a instância Publicar fica dentro do firewall e o Dispatcher reside no DMZ. Se você decidir ter a instância Publicar e o Dispatcher na mesma máquina física, verifique se as configurações do firewall proíbem o acesso direto à instância Publicar de redes externas.

### Posso armazenar em cache somente arquivos com extensões específicas?

Sim. Por exemplo, se você deseja armazenar em cache apenas arquivos GIF, especifique *.gif na seção de cache do dispatcher.any arquivo de configuração.

### Como excluir arquivos do cache?

É possível excluir arquivos do cache usando uma solicitação HTTP. Quando a solicitação HTTP é recebida, o Dispatcher exclui os arquivos do cache. O Dispatcher armazena os arquivos em cache novamente somente quando recebe uma solicitação do cliente para a página. A exclusão de arquivos em cache dessa maneira é apropriada para sites que provavelmente não receberão solicitações simultâneas para a mesma página.

A solicitação HTTP tem a seguinte sintaxe:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

O Dispatcher exclui os arquivos e pastas em cache que têm nomes que correspondem ao valor do cabeçalho de Identificador de CQ. Por exemplo, um identificador de CQ de `/content/geomtrixx-outdoors/en` corresponde aos seguintes itens:

Todos os arquivos (de qualquer extensão de arquivo) nomeados em en no diretório geometrixx-outdoorsQualquer diretório nomeado `_jcr_content` abaixo do diretório en (que, se existir, contém renderizações em cache de subnós da página)O diretório en só será excluído se o diretório `CQ-Action` for `Delete` ou `Deactivate`.

Para obter mais detalhes sobre esse tópico, consulte [Invalidando manualmente o Cache](page-invalidate.md)do Dispatcher.

### Como faço para implementar o cache sensível a permissões?

Consulte a página [Cache de conteúdo](permissions-cache.md) seguro.

### Como proteger as comunicações entre as instâncias do Dispatcher e do CQ?

Consulte a Lista de verificação [de segurança do](security-checklist.md) Dispatcher e as páginas Lista de verificação [de segurança do](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security-checklist.html) AEM.

### Problema do Dispatcher `jcr:content` alterado para `jcr%3acontent`

**Pergunta**: Recentemente, enfrentamos um problema no nível do despachante em que uma das chamadas ajax que estava recebendo alguns dados do repositório do CQ estava `jcr:content` nela e foi codificada para `jcr%3acontent` resultar em um resultado errado.

**Resposta**: Use `ResourceResolver.map()` o método para obter um URL "Amigável" para ser usado/receber solicitações de e também para resolver o problema de armazenamento em cache com o Dispatcher. O método map() codifica os dois `:` pontos para sublinhados e o método resolve() os decodifica de volta ao formato legível SLING JCR. Você precisa usar o método map() para gerar o URL usado na chamada Ajax.

Mais: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Liberar o Dispatcher

### Como faço para configurar os agentes de liberação do Dispatcher em uma instância de Publicação?

Consulte a página [Replicação](https://helpx.adobe.com/content/help/en/experience-manager/6-4/sites/deploying/using/replication.html#ConfiguringyourReplicationAgents) .

### Como soluciono problemas de rubor do Dispatcher?

[Consulte este artigo](https://helpx.adobe.com/content/help/en/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) de solução de problemas que responde às seguintes perguntas:

* Como devo depurar uma situação em que nenhum conteúdo é salvo no cache do Dispatcher?
* Como faço para depurar uma situação em que os arquivos de cache não são atualizados?
* Como faço para depurar uma situação em que nada relacionado ao rubor do Dispatcher está funcionando?

Se as operações de exclusão estiverem fazendo com que o Dispatcher esvazie, [use a solução alternativa nesta publicação do blog da comunidade pelo Sensei Martin](https://mkalugin-cq.blogspot.in/2012/04/i-have-been-working-on-following.html).

### Como liberar ativos DAM do cache do Dispatcher?

Você pode usar o recurso "replicação em cadeia".  Com esse recurso ativado, o agente de liberação do dispatcher envia uma solicitação de liberação quando uma replicação é recebida do autor.

Para ativá-lo:

1. [Siga as etapas aqui](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) para criar agentes de descarga ao publicar
1. Vá para a configuração de cada um desses agentes e, na guia **Acionadores** , marque a caixa **Ao receber** .

## Diversos

Como o Dispatcher determina se um documento está atualizado?
Para determinar se um documento está atualizado, o Dispatcher executa estas ações:

Verifica se o documento está sujeito a invalidação automática. Caso contrário, o documento será considerado atualizado.
Se o documento estiver configurado para invalidação automática, o Dispatcher verificará se ele é mais antigo ou mais recente do que a última alteração disponível. Se for mais antiga, o Dispatcher solicitará a versão atual da instância do AEM e substituirá a versão no cache.

### Como o Dispatcher retorna documentos?

Você pode definir se o Dispatcher armazena um documento em cache usando o arquivo de configuração [do](dispatcher-configuration.md) Dispatcher `dispatcher.any`. O Dispatcher verifica a solicitação em relação à lista de documentos que podem ser armazenados em cache. Se o documento não estiver nessa lista, o Dispatcher solicitará o documento da instância do AEM.

A `/rules` propriedade controla quais documentos são armazenados em cache de acordo com o caminho do documento. Independentemente da `/rules` propriedade, o Dispatcher nunca armazena um documento em cache nas seguintes circunstâncias:

* Se o URI de solicitação contiver um ponto de interrogação `(?)`.
* Isso geralmente indica uma página dinâmica, como um resultado de pesquisa que não precisa ser armazenado em cache.
* A extensão do arquivo está ausente.
* O servidor Web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (isso pode ser configurado)
* Se a instância do AEM responder com os seguintes cabeçalhos:
   * no-cache
   * no-store
   * must-revalidate

O Dispatcher armazena arquivos em cache no servidor da Web como se fossem parte de um site estático. Se um usuário solicitar um documento em cache, o Dispatcher verificará se o documento existe no sistema de arquivos do servidor da Web. Em caso afirmativo, o Dispatcher retornará os documentos. Caso contrário, o Dispatcher solicitará o documento da instância do AEM.

>[!NOTE]
>
>Os métodos GET ou HEAD (para o cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. Para obter informações adicionais sobre o cache do cabeçalho de resposta, consulte a seção [Cache de Cabeçalhos](dispatcher-configuration.md#caching-http-response-headers) de Resposta HTTP.

### É possível implementar vários Dispatchers em uma configuração?

Sim. Nesses casos, verifique se ambos os Dispatchers podem acessar o site do AEM diretamente. Um Dispatcher não pode lidar com solicitações provenientes de outro Dispatcher.

