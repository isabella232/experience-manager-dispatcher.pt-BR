---
title: Otimização de um site para desempenho de cache
seo-title: Optimizing a Website for Cache Performance
description: Saiba como projetar seu site para potencializar os benefícios do armazenamento em cache.
seo-description: Dispatcher offers a number of built-in mechanisms that you can use to optimize performance. Learn how to design your web site to maximize the benefits of caching.
uuid: 2d4114d1-f464-4e10-b25c-a1b9a9c715d1
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: ba323503-1494-4048-941d-c1d14f2e85b2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: 2ca816ac0776d72be651b76ff4f45e0c3ed1450f
workflow-type: ht
source-wordcount: '1134'
ht-degree: 100%

---


# Otimização de um site para desempenho de cache {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para esta página se tiver seguido um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

O Dispatcher oferece vários mecanismos integrados que você pode usar para aprimorar o desempenho. Esta seção informa como projetar seu site para potencializar os benefícios do armazenamento em cache.

>[!NOTE]
>
>Pode ser útil ter em mente que o Dispatcher armazena o cache em um servidor da Web padrão. Isso significa que você:
>
>* pode armazenar em cache tudo que pode ser armazenado como uma página e solicitado usando um URL
>* não pode armazenar outras coisas, como cabeçalhos HTTP, cookies, dados de sessão e dados de formulário.

>
>Em geral, muitas estratégias de armazenamento em cache envolvem selecionar bons URLs e não depender desses dados adicionais.

## Uso de codificação de página consistente {#using-consistent-page-encoding}

Os cabeçalhos de solicitação HTTP não são armazenados em cache e, portanto, podem ocorrer problemas se você armazenar informações de codificação de página no cabeçalho. Nessa situação, quando o Dispatcher fornece uma página do cache, a codificação padrão do servidor Web é usada para a página. Há duas maneiras de evitar esse problema:

* Se você usar apenas uma codificação, verifique se a codificação usada no servidor Web é igual à codificação padrão do site do AEM.
* Use uma tag `<META>` na seção `head` do HTML para definir a codificação, como no exemplo a seguir:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Evitar parâmetros de URL {#avoid-url-parameters}

Se possível, evite parâmetros de URL para páginas que você deseja armazenar em cache. Por exemplo, se você tiver uma galeria de imagens, o URL a seguir nunca será armazenado em cache (a menos que o Dispatcher esteja [configurado adequadamente](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

No entanto, você pode colocar esses parâmetros no URL da página, da seguinte maneira:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Esse URL chama a mesma página e o mesmo modelo de gallery.html. Na definição do modelo, é possível especificar qual script renderiza a página ou usar o mesmo script para todas as páginas.

## Personalizar por URL {#customize-by-url}

Se você permitir que os usuários alterem o tamanho da fonte (ou qualquer outra personalização de layout), verifique se as diferentes personalizações são refletidas no URL.

Por exemplo, os cookies não são armazenados em cache. Dessa forma, se você armazenar o tamanho da fonte em um cookie (ou mecanismo semelhante), o tamanho da fonte não será preservado para a página em cache. Como resultado, o Dispatcher retorna documentos de qualquer tamanho de fonte aleatoriamente.

Incluir o tamanho da fonte no URL como um seletor evita esse problema:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Para a maioria dos aspectos de layout, também é possível usar folhas de estilos e/ou scripts do cliente. Normalmente, eles funcionam muito bem com o armazenamento em cache.
>
>Isso também é útil para uma versão impressa, na qual você pode usar um URL como: ``
>
>`www.myCompany.com/news/main.print.html`
>
>Usando o recurso de curinga de script da definição do modelo, você pode especificar um script separado que renderize as páginas de impressão.

## Invalidar arquivos de imagem usados como títulos {#invalidating-image-files-used-as-titles}

Se você renderizar títulos de página, ou outro texto, como imagens, é recomendável armazenar os arquivos para que eles sejam excluídos após uma atualização de conteúdo na página:

1. Coloque o arquivo de imagem na mesma pasta da página.
1. Use o seguinte formato de nomenclatura para o arquivo de imagem:

   `<page file name>.<image file name>`

Por exemplo, você pode armazenar o título da página myPage.html no arquivo myPage.title.gif. Esse arquivo será automaticamente excluído se a página for atualizada, de modo que qualquer alteração no título da página será refletida automaticamente no cache.

>[!NOTE]
>
>O arquivo de imagem não existe necessariamente fisicamente na instância do AEM. Você pode usar um script que cria dinamicamente o arquivo de imagem. O Dispatcher armazena o arquivo no servidor Web.

## Invalidar arquivos de imagem usados para navegação {#invalidating-image-files-used-for-navigation}

Se você usar imagens para as entradas de navegação, o método é basicamente o mesmo com títulos, apenas ligeiramente mais complexos. Armazene todas as imagens de navegação com as páginas de destino. Se você usar duas imagens para o normal e o ativo, poderá usar os seguintes scripts:

* Um script que exibe a página, como de costume.
* Um script que processa solicitações &quot;.normal&quot; e retorna a imagem normal.
* Um script que processa solicitações &quot;.active&quot; e retorna a imagem ativada.

É importante criar essas imagens com o mesmo identificador de nome da página, para garantir que uma atualização de conteúdo exclua essas imagens, bem como a página.

Para páginas que não são modificadas, as imagens ainda permanecem no cache, embora as próprias páginas sejam normalmente invalidadas automaticamente.

## Personalização {#personalization}

O Dispatcher não pode armazenar dados personalizados em cache, portanto, é recomendável limitar a personalização ao local necessário. Para ilustrar o motivo:

* Se você usar uma página inicial personalizável livremente, essa página deverá ser composta sempre que um usuário a solicitar.
* Se, por outro lado, você oferecer uma opção de 10 páginas iniciais diferentes, poderá armazenar cada uma delas em cache, melhorando o desempenho.

>[!NOTE]
>
>Se você personalizar cada página (por exemplo, colocando o nome do usuário na barra de título), não poderá armazená-la em cache, o que pode causar um grande impacto no desempenho.
>
>No entanto, se você precisar fazer isso, será possível:
>
>* usar iFrames para dividir a página em uma parte que seja a mesma para todos os usuários e uma parte que seja a mesma para todas as páginas do usuário. Depois, é possível armazenar em cache essas duas partes.
>* usar o JavaScript do lado do cliente para exibir informações personalizadas. No entanto, é necessário assegurar que a página ainda seja exibida corretamente se um usuário desativar o JavaScript.

>


## Conexões adesivas {#sticky-connections}

As [conexões adesivas](dispatcher.md#TheBenefitsofLoadBalancing) garantem que os documentos de um usuário sejam todos compostos no mesmo servidor. Se um usuário sair dessa pasta e posteriormente retornar a ela, a conexão ainda permanecerá. Defina uma pasta para armazenar todos os documentos que exigem conexões adesivas para o site. Tente não manter outros documentos nela. Isso impactará o balanceamento de carga se você usar páginas personalizadas e dados de sessão.

## Tipos MIME {#mime-types}

Há duas maneiras pelas quais um navegador pode determinar o tipo de arquivo:

1. Pela sua extensão (por exemplo, .html, .gif, .jpg etc.)
1. Pelo tipo MIME que o servidor envia com o arquivo.

Para a maioria dos arquivos, o tipo MIME está implícito na extensão de arquivo. Ou seja:

1. Pela sua extensão (por exemplo, .html, .gif, .jpg etc.)
1. Pelo tipo MIME que o servidor envia com o arquivo.

Se o nome do arquivo não tiver extensão, ele será exibido como texto sem formatação.

O tipo MIME faz parte do cabeçalho HTTP e, como tal, o Dispatcher não o armazena em cache. Se o aplicativo do AEM retornar arquivos que não têm um final de arquivo reconhecido, mas dependem do tipo MIME, esses arquivos podem ser exibidos incorretamente.

Para garantir que os arquivos sejam armazenados em cache corretamente, siga estas diretrizes:

* Certifique-se de que os arquivos sempre tenham a extensão adequada.
* Evite scripts de servidor de arquivos genéricos, que tenham URLs como download.jsp?file=2214. Regravar o script para usar URLs contendo a especificação do arquivo; para o exemplo anterior, seria download.2214.pdf.

