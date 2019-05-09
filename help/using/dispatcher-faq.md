---
title: Principais problemas do Dispatcher
seo-title: Principais problemas do AEM Dispatcher
description: Principais problemas do AEM Dispatcher
seo-description: Principais problemas do Adobe AEM Dispatcher
translation-type: tm+mt
source-git-commit: 76cffbfb616cd5601aed36b7076f67a2faf3ed3b

---


# Perguntas frequentes sobre os problemas do Dispatcher do AEM Dispatcher

![Configuração do Dispatcher](assets/CQDispatcher_workflow_v2.png)

## Introdução

### O que é o Dispatcher?

O Dispatcher é a ferramenta de balanceamento de cache e/ou cache do Adobe Experience Manager que ajuda a perceber um ambiente de criação da Web rápido e dinâmico. Para armazenamento em cache, o Dispatcher funciona como parte de um servidor HTTP, como o Apache, com o objetivo de armazenar (ou «armazenar em cache») o maior número do conteúdo estático do site do site e acessar o mecanismo de layout do site o mais infrequentemente possível. Em uma função de balanceamento de carga, o Dispatcher distribui solicitações do usuário (load) por diferentes instâncias do AEM (renderizações).

Para armazenamento em cache, o módulo Dispatcher usa a capacidade do servidor da Web de enviar conteúdo estático. O Dispatcher coloca os documentos em cache na raiz do documento do servidor Web.

### Como o Dispatcher executa armazenamento em cache?

O Dispatcher usa a capacidade do servidor da Web de enviar conteúdo estático. O Dispatcher armazena documentos em cache na raiz do documento do servidor da Web. O Dispatcher tem dois métodos principais para atualizar o conteúdo do cache quando alterações são feitas no site.

* **As atualizações** de conteúdo removem as páginas que foram alteradas, bem como os arquivos que estão diretamente associados a eles.
* **A Invalidação automática** invalida automaticamente as partes do cache que podem estar desatualizadas após uma atualização. Por exemplo, marca efetivamente as páginas relevantes como desatualizadas, sem excluir nada.

### Quais são os benefícios do balanceamento de carga?

O balanceamento de carga distribui solicitações do usuário (load) em várias instâncias do AEM. A seguinte lista descreve as vantagens do balanceamento de carga:

* **Maior potência de processamento**: Na prática, isso significa que o Dispatcher compartilha solicitações de documento entre várias instâncias do AEM. Como cada instância tem menos documentos para processar, você tem tempos de resposta mais rápidos. O Dispatcher mantém estatísticas internas para cada categoria de documento, para que possa estimar a carga e distribuir as consultas de forma eficiente.
* **Cobertura maior segura para falhas**: Se o Dispatcher não receber respostas de uma instância, ele automaticamente enviará solicitações para uma das outras instâncias. Assim, se uma instância ficar indisponível, o único efeito será um desaceleração do site, proporcional à potência computacional perdida.

>[!NOTE]
>
>Para obter mais detalhes, consulte a página Visão geral [do Dispatcher](dispatcher.md)

## Instalar e configurar

### Para onde posso baixar o módulo Dispatcher?

Você pode baixar o módulo mais recente do Dispatcher na página [Notas](release-notes.md) de versão do Dispatcher.

### Como instalo o módulo Dispatcher?

Refer to the [Installing Dispatcher](dispatcher-install.md) page

### Como posso configurar o módulo do Dispatcher?

Consulte a [página Configuração do Dispatcher](dispatcher-configuration.md) .

### Como faço para configurar o Dispatcher para a instância do autor?

Consulte [Uso do Dispatcher com uma Instância do autor](dispatcher.md#using-a-dispatcher-with-an-author-server) para obter as etapas detalhadas.

### Como faço para configurar o Dispatcher com vários domínios?

Você pode configurar o Dispatcher com vários domínios, desde que os domínios atendam às seguintes condições:

* O conteúdo da Web para ambos os domínios é armazenado em um único repositório do AEM
* Os arquivos no cache do Dispatcher podem ser invalidados separadamente para cada domínio

Ler [usando o Dispatcher com vários domínios](dispatcher-domains.md) para obter mais detalhes.

### Como faço para configurar o Dispatcher, de forma que todas as solicitações de um usuário sejam roteadas para a mesma instância de Publicação?

Você pode usar o [recurso de conexões](dispatcher-configuration.md#identifying-a-sticky-connection-folder-stickyconnectionsfor) aderentes, o que garante que todos os documentos para um usuário sejam processados na mesma instância do AEM. Esse recurso é importante se você usar páginas personalizadas e dados de sessão. Os dados são armazenados na instância. Portanto, as solicitações subsequentes do mesmo usuário devem retornar para essa instância, ou os dados são perdidos.

Como as conexões adesivas limitam a capacidade do Dispatcher de otimizar solicitações, você deve usar essa abordagem somente quando necessário. Você pode especificar a pasta que contém os documentos &quot;aderentes&quot;, assegurando que todos os documentos nessa pasta sejam processados na mesma instância para um usuário.

### Posso usar conexões adesivas e armazenar em cache no tandem?

Para a maioria das páginas que usam conexões aderentes, você deve desativar o armazenamento em cache. Caso contrário, a mesma instância da página será exibida a todos os usuários, independentemente do conteúdo da sessão.

Para alguns aplicativos, pode ser possível usar conexões adesivas e armazenar em cache. Por exemplo, se você exibir um formulário que grava dados em uma sessão, poderá usar conexões adesivas e armazenar em cache em tandem.

### Um Dispatcher e uma instância de publicação de AEM residem no mesmo computador físico?

Sim, se a máquina for suficientemente potente. No entanto, recomenda-se configurar o Dispatcher e a instância de publicação de AEM em máquinas diferentes.

Normalmente, a instância Publicar fica dentro do firewall e o Dispatcher reside no DMZ. Se você decidir ter a instância Publicar e o Dispatcher no mesmo computador físico, certifique-se de que as configurações do firewall proíbem acesso direto à instância Publicar de redes externas.

### Posso armazenar em cache apenas arquivos com extensões específicas?

Sim. Por exemplo, se você deseja armazenar em cache apenas arquivos GIF, especifique *. gif na seção cache do dispatcher. Qualquer arquivo de configuração.

### Como excluir arquivos do cache?

É possível excluir arquivos do cache usando uma solicitação HTTP. Quando a solicitação HTTP é recebida, o Dispatcher exclui os arquivos do cache. O Dispatcher armazena os arquivos novamente apenas quando recebe uma solicitação de cliente para a página. A exclusão de arquivos em cache dessa maneira é adequada para sites que não têm probabilidade de receber solicitações simultâneas para a mesma página.

A solicitação HTTP tem a seguinte sintaxe:

```
POST /dispatcher/invalidate.cache HTTP/1.1
CQ-Action: Activate
CQ-Handle: path-pattern
Content-Length: 0
```

O Dispatcher exclui os arquivos e pastas em cache com nomes que correspondem ao valor do cabeçalho CQ-Handle. Por exemplo, um identificador CQ de `/content/geomtrixx-outdoors/en` corresponde aos seguintes itens:

Todos os arquivos (de qualquer extensão de arquivo) nomeados para o diretório
geometrixx-outdoors Qualquer diretório chamado `_jcr_content` abaixo do diretório ene (que, se existir, contém representações em cache de subnodes da página)
O diretório en só será excluído se o `CQ-Action` for `Delete` ou `Deactivate`.

Para obter mais detalhes sobre este tópico, consulte [Invalidar manualmente o cache do Dispatcher](page-invalidate.md).

### Como implementar o armazenamento em cache com diferenciação de permissão?

Consulte a página [Cache de conteúdo](permissions-cache.md) protegido.

### Como faço para proteger comunicações entre as instâncias do Dispatcher e do CQ?

Consulte a [Lista de verificação de segurança do Dispatcher](security-checklist.md) e as [páginas da Lista de verificação](https://helpx.adobe.com/experience-manager/6-4/sites/administering/using/security-checklist.html) de segurança do AEM.

### Problema do Dispatcher `jcr:content` alterado para `jcr%3acontent`

**Pergunta**: Recentemente enfrentamos um problema no nível do dispatcher, que fazia com que uma chamada ajax que fazia alguns de um repositório do CQ de formulário de dados possuísse `jcr:content` um e-mail `jcr%3acontent` resultasse em um conjunto de resultados incorreto.

**Resposta**: Use `ResourceResolver.map()` o método para obter um URL &quot;amigável&quot; para ser usado/emitido, e também para resolver o problema de cache com o Dispatcher. O método map () codifica o `:` dois pontos em sublinhado e o método resolve () os decodifica de volta ao formato legível para SLING JCR. Você precisa usar o método mapa () para gerar o URL usado na chamada Ajax.

Outra leitura: [https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling](https://sling.apache.org/documentation/the-sling-engine/mappings-for-resource-resolution.html#namespace-mangling)

## Esvaziar o Dispatcher

### Como faço para configurar os agentes flush do Dispatcher em uma instância de Publicação?

Consulte a página [Replicação](https://helpx.adobe.com/content/help/en/experience-manager/6-4/sites/deploying/using/replication.html#ConfiguringyourReplicationAgents) .

### Como solucionar problemas de limpeza do Dispatcher?

[Consulte este artigo](https://helpx.adobe.com/content/help/en/experience-manager/kb/troubleshooting-dispatcher-flushing-issues.html) de solução de problemas que responde às seguintes perguntas:

* Como faço para depurar uma situação em que nenhum conteúdo está sendo salvo no cache do Dispatcher?
* Como depurar uma situação em que os arquivos do cache não estão sendo atualizados?
* Como depurar uma situação em que nada relacionado à limpeza do Dispatcher está funcionando?

Se as operações Excluir estiverem fazendo com que o Dispatcher seja esvaziado, [use a solução nessa comunidade do blog de Sensei Martin](https://mkalugin-cq.blogspot.in/2012/04/i-have-been-working-on-following.html).

### Como faço para separar os ativos DAM do cache do Dispatcher?

Você pode usar o recurso &quot;replicação da cadeia&quot;. Com esse recurso ativado, o agente flush do dispatcher envia uma solicitação de flush quando uma replicação é recebida do autor.

Para ativá-lo:

1. [Siga as etapas aqui](page-invalidate.md#invalidating-dispatcher-cache-from-a-publishing-instance) para criar agentes de limpeza na publicação
1. Vá para cada configuração do agente e, na guia **Acionadores** , marque a **caixa** Ativar.

## Diversos

Como o Dispatcher determina se um documento está atualizado?
Para determinar se um documento está atualizado, o Dispatcher executa estas ações:

Verifica se o documento está sujeito a invalidação automática. Caso contrário, o documento será considerado atualizado.
Se o documento estiver configurado para invalidação automática, o Dispatcher verifica se ele é mais antigo ou mais recente do que a última alteração disponível. Se for mais antiga, o Dispatcher solicita a versão atual da instância do AEM e substitui a versão no cache.

### Como o Dispatcher retorna documentos?

You can define whether the Dispatcher caches a document by using the [Dispatcher configuration](dispatcher-configuration.md) file, `dispatcher.any`. O Dispatcher verifica a solicitação na lista de documentos armazenados em cache. Se o documento não estiver nesta lista, o Dispatcher solicita o documento da instância do AEM.

A `/rules` propriedade controla quais documentos são armazenados em cache de acordo com o caminho do documento. Independentemente da `/rules` propriedade, o Dispatcher nunca armazena em cache um documento nas seguintes circunstâncias:

* Se o URI da solicitação contiver um `(?)`ponto de interrogação.
* Isso geralmente indica uma página dinâmica, como um resultado de pesquisa que não precisa ser armazenado em cache.
* A extensão do arquivo está ausente.
* O servidor Web precisa da extensão para determinar o tipo de documento (o tipo MIME).
* O cabeçalho de autenticação está definido (isto pode ser configurado)
* Se a instância do AEM responder com os seguintes cabeçalhos:
   * no-cache
   * sem armazenamento
   * must-revalidate

O Dispatcher armazena arquivos em cache no servidor da Web como se fizessem parte de um site estático. Se um usuário solicitar um documento em cache, o Dispatcher verificará se o documento existe no sistema de arquivos do servidor da Web. Em caso afirmativo, o Dispatcher retorna os documentos. Caso contrário, o Dispatcher solicita o documento da instância do AEM.

>[!NOTE]
>
>Os métodos GET ou HEAD (para os cabeçalho HTTP) podem ser armazenados em cache pelo Dispatcher. Para obter informações adicionais sobre armazenamento em cache de cabeçalho de resposta, consulte a [seção Cache de cabeçalhos](dispatcher-configuration.md#caching-http-response-headers) HTTP de cache.

### Posso implementar vários Dispatchers em uma configuração?

Sim. Nesses casos, verifique se os Dispatchers podem acessar diretamente o site do AEM. Um Dispatcher não pode lidar com solicitações vindo de outro Dispatcher.

