---
title: Otimizando um site para o desempenho do cache
seo-title: Otimizando um site para o desempenho do cache
description: Saiba como criar seu site para maximizar os benefícios do cache.
seo-description: O Dispatcher oferece vários mecanismos integrados que você pode usar para otimizar o desempenho. Saiba como criar seu site para maximizar os benefícios do cache.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: Usuário
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: expedidor
content-type: referência
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: 2ca816ac0776d72be651b76ff4f45e0c3ed1450f

---


# Otimizando um site para o desempenho do cache {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

O Dispatcher oferece vários mecanismos integrados que você pode usar para otimizar o desempenho. Esta seção informa como criar seu site para maximizar os benefícios do armazenamento em cache.

>[!NOTE]
>
>Pode ajudar você a lembrar que o Dispatcher armazena o cache em um servidor da Web padrão. Isso significa que você:
>
>* pode armazenar em cache tudo o que você pode armazenar como uma página e solicitar usando um URL
>* não é possível armazenar outras coisas, como cabeçalhos HTTP, cookies, dados de sessão e dados de formulário.
>
>
Em geral, muitas estratégias de cache envolvem selecionar bons URLs e não depender desses dados adicionais.

## Usando codificação de página consistente {#using-consistent-page-encoding}

Os cabeçalhos de solicitação HTTP não são armazenados em cache e, portanto, podem ocorrer problemas se você armazenar informações de codificação de página no cabeçalho. Nessa situação, quando o Dispatcher envia uma página do cache, a codificação padrão do servidor da Web é usada para a página. Há duas maneiras de evitar esse problema:

* Se você usar apenas uma codificação, verifique se a codificação usada no servidor da Web é a mesma usada na codificação padrão do site do AEM.
* Use uma `<META>` tag na seção HTML `head` para definir a codificação, como no exemplo a seguir:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Evitar parâmetros de URL {#avoid-url-parameters}

Se possível, evite parâmetros de URL para as páginas que deseja armazenar em cache. Por exemplo, se você tiver uma galeria de imagens, o seguinte URL nunca será armazenado em cache (a menos que o Dispatcher esteja [configurado adequadamente](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

No entanto, você pode colocar esses parâmetros no URL da página, da seguinte forma:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Esse URL chama a mesma página e o mesmo modelo que gallery.html. Na definição do modelo, é possível especificar qual script renderiza a página ou usar o mesmo script para todas as páginas.

## Personalizar por URL {#customize-by-url}

Se você permitir que os usuários alterem o tamanho da fonte (ou qualquer outra personalização do layout), verifique se as diferentes personalizações são refletidas no URL.

Por exemplo, os cookies não são armazenados em cache, portanto, se você armazenar o tamanho da fonte em um cookie (ou mecanismo semelhante), o tamanho da fonte não será preservado para a página em cache. Como resultado, o Dispatcher retorna documentos de qualquer tamanho de fonte aleatoriamente.

A inclusão do tamanho da fonte no URL como seletor evita esse problema:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Para a maioria dos aspectos de layout, também é possível usar folhas de estilos e/ou scripts do cliente. Normalmente, eles funcionam muito bem com o cache.
>
>Isso também é útil para uma versão impressa, onde você pode usar um URL como: "
>
>`www.myCompany.com/news/main.print.html`
>
>Usando a globalização do script da definição do modelo, você pode especificar um script separado que renderiza as páginas de impressão.

## Invalidando arquivos de imagem usados como títulos {#invalidating-image-files-used-as-titles}

Se você renderizar títulos de página, ou outro texto, como imagens, é recomendável armazenar os arquivos para que sejam excluídos após uma atualização de conteúdo na página:

1. Coloque o arquivo de imagem na mesma pasta que a página.
1. Use o seguinte formato de nomeação para o arquivo de imagem:

   `<page file name>.<image file name>`

Por exemplo, você pode armazenar o título da página myPage.html no arquivo myPage.title.gif. Esse arquivo será excluído automaticamente se a página for atualizada, de modo que qualquer alteração no título da página será refletida automaticamente no cache.

>[!NOTE]
>
>O arquivo de imagem não existe fisicamente na instância do AEM. Você pode usar um script que cria dinamicamente o arquivo de imagem. O Dispatcher armazena o arquivo no servidor da Web.

## Invalidando Arquivos De Imagem Usados Para Navegação {#invalidating-image-files-used-for-navigation}

Se você usar imagens para as entradas de navegação, o método é basicamente o mesmo que com títulos, apenas ligeiramente mais complexo. Armazene todas as imagens de navegação com as páginas de destino. Se você usar duas imagens para normais e ativas, poderá usar os seguintes scripts:

* Um script que exibe a página, como de costume.
* Um script que processa solicitações ".normal" e retorna a imagem normal.
* Um script que processa solicitações ".ative" e retorna a imagem ativada.

É importante que você crie essas imagens com o mesmo identificador de nome da página, para garantir que uma atualização de conteúdo exclua essas imagens, bem como a página.

Para páginas que não são modificadas, as imagens ainda permanecem no cache, embora as próprias páginas geralmente sejam invalidadas automaticamente.

## Personalização {#personalization}

O Dispatcher não pode armazenar dados personalizados em cache, portanto, recomenda-se que você limite a personalização para onde ela for necessária. Para ilustrar por que:

* Se você usar uma página inicial personalizável livremente, essa página deverá ser composta sempre que um usuário a solicitar.
* Se, por outro lado, você oferecer uma opção de 10 páginas iniciais diferentes, você poderá armazenar cada uma delas em cache, melhorando assim o desempenho.

>[!NOTE]
>
>Se você personalizar cada página (por exemplo, colocando o nome do usuário na barra de título) não poderá armazená-la em cache, o que pode causar um grande impacto no desempenho.
>
>No entanto, se você precisar fazer isso, poderá:
>
>* use iFrames para dividir a página em uma parte que é a mesma para todos os usuários e uma parte que é a mesma para todas as páginas do usuário. Em seguida, é possível armazenar essas duas partes em cache.
>* use o JavaScript do lado do cliente para exibir informações personalizadas. No entanto, é necessário verificar se a página ainda é exibida corretamente se um usuário desativa o JavaScript.
>



## Conexões aderentes {#sticky-connections}

[As conexões](dispatcher.md#TheBenefitsofLoadBalancing) adesivas garantem que os documentos de um usuário sejam todos compostos no mesmo servidor. Se um usuário sair desta pasta e posteriormente retornar a ela, a conexão ainda permanecerá. Defina uma pasta para manter todos os documentos que exigem conexões aderentes para o site. Tente não ter outros documentos nele. Isso afeta o balanceamento de carga se você usar páginas personalizadas e dados de sessão.

## MIME Types {#mime-types}

Há duas maneiras de um navegador determinar o tipo de arquivo:

1. Pela sua extensão (por exemplo, .html, .gif, .jpg etc)
1. Pelo tipo MIME que o servidor envia com o arquivo.

Para a maioria dos arquivos, o tipo MIME está implícito na extensão do arquivo. ou seja:

1. Pela sua extensão (por exemplo, .html, .gif, .jpg etc)
1. Pelo tipo MIME que o servidor envia com o arquivo.

Se o nome do arquivo não tiver extensão, ele será exibido como texto sem formatação.

O tipo MIME faz parte do cabeçalho HTTP e, como tal, o Dispatcher não o armazena em cache. Se o aplicativo AEM retornar arquivos que não têm um arquivo reconhecido como encerrado, mas que dependem do tipo MIME, esses arquivos podem ser exibidos incorretamente.

Para garantir que os arquivos sejam armazenados em cache corretamente, siga estas diretrizes:

* Verifique se os arquivos sempre têm a extensão correta.
* Evite scripts de servidor de arquivos genéricos, que têm URLs como download.jsp?file=2214. regravar o script para usar URLs contendo a especificação do arquivo; para o exemplo anterior, este seria download.2214.pdf.

