---
title: Otimizar um site para o desempenho do cache
seo-title: Otimizar um site para o desempenho do cache
description: Saiba como projetar seu site para maximizar os benefícios do armazenamento em cache.
seo-description: O Dispatcher oferece uma série de mecanismos incorporados que podem ser usados para otimizar o desempenho. Saiba como projetar seu site para maximizar os benefícios do armazenamento em cache.
uuid: 2 d 4114 d 1-f 464-4 e 10-b 25 c-a 1 b 9 a 9 c 715 d 1
contentOwner: Usuário
products: SG_ EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: referência
discoiquuid: ba 323503-1494-4048-941 d-c 1 d 14 f 2 e 85 b 2
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
translation-type: tm+mt
source-git-commit: f35c79b487454059062aca6a7c989d5ab2afaf7b

---


# Otimizar um site para o desempenho do cache {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>As versões do Dispatcher são independentes do AEM. Você pode ter sido redirecionado para essa página se seguir um link para a documentação do Dispatcher incorporada à documentação de uma versão anterior do AEM.

O Dispatcher oferece uma série de mecanismos incorporados que podem ser usados para otimizar o desempenho. Esta seção informa como projetar seu site para maximizar os benefícios do armazenamento em cache.

>[!NOTE]
>
>Pode ajudar você a lembrar que o Dispatcher armazena o cache em um servidor Web padrão. Isso significa que você:
>
>* pode armazenar em cache tudo o que você pode armazenar como uma página e solicitar usando um URL
>* não pode armazenar outras coisas, como cabeçalhos HTTP, cookies, dados de sessão e dados de formulário.
>
>
Em geral, muitas estratégias de cache envolvem a seleção de bons urls e não dependem desses dados adicionais.

## Usar codificação de página consistente {#using-consistent-page-encoding}

Os cabeçalhos de solicitação HTTP não são armazenados em cache e podem ocorrer problemas se você armazenar informações de codificação de página no cabeçalho. Nessa situação, quando o Dispatcher serve uma página do cache, a codificação padrão do servidor da Web é usada para a página. Há duas maneiras de evitar esse problema:

* Se você usar apenas uma codificação, verifique se a codificação usada no servidor da Web é a mesma da codificação padrão do site do AEM.
* Use `<META>` uma tag na seção HTML `head` para definir a codificação, como no exemplo a seguir:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Evitar parâmetros de URL {#avoid-url-parameters}

Se possível, evite parâmetros de URL para páginas que você deseja armazenar em cache. Por exemplo, se você tiver uma galeria de imagens, o URL a seguir nunca será armazenado em cache (a menos que o Dispatcher esteja [configurado adequadamente](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

No entanto, é possível colocar esses parâmetros no URL da página, da seguinte forma:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Esse URL chama a mesma página e o mesmo modelo de gallery. html. Na definição do modelo, você pode especificar qual script renderiza a página ou pode usar o mesmo script para todas as páginas.

## Personalizar por URL {#customize-by-url}

Se você permitir que os usuários alterem o tamanho da fonte (ou qualquer outra personalização de layout), certifique-se de que as diferentes personalizações sejam refletidas no URL.

Por exemplo, os cookies não são armazenados em cache, portanto, se você armazena o tamanho da fonte em um cookie (ou mecanismo semelhante), o tamanho da fonte não é preservado para a página em cache. Como resultado, o Dispatcher retorna documentos de qualquer tamanho de fonte aleatório.

A inclusão do tamanho da fonte no URL como um seletor evita esse problema:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Para a maioria dos aspectos de layout, também é possível usar folhas de estilos e/ou scripts de cliente. Geralmente, isso funcionará muito bem com o armazenamento em cache.
>
>Isso também é útil para uma versão de impressão, na qual é possível usar um URL como: «
>
>`www.myCompany.com/news/main.print.html`
>
>Usando o script de script da definição do modelo, é possível especificar um script separado que renderiza as páginas de impressão.

## Invalidando arquivos de imagem usados como títulos {#invalidating-image-files-used-as-titles}

Se você renderizar títulos de página ou outro texto como imagens, será recomendável armazenar os arquivos para que sejam excluídos mediante uma atualização de conteúdo na página:

1. Coloque o arquivo de imagem na mesma pasta da página.
1. Use o seguinte formato de nomenclatura para o arquivo de imagem:

   `<page file name>.<image file name>`

Por exemplo, você pode armazenar o título da página myPage.html no arquivo mypage. title. gif. Esse arquivo será excluído automaticamente se a página for atualizada, portanto, qualquer alteração no título da página será automaticamente refletida no cache.

>[!NOTE]
>
>O arquivo de imagem não existe necessariamente na instância do AEM. É possível usar um script que cria dinamicamente o arquivo de imagem. O Dispatcher armazena o arquivo no servidor da Web.

## Invalidando arquivos de imagem usados para navegação {#invalidating-image-files-used-for-navigation}

Se você usar imagens para as entradas de navegação, o método é basicamente o mesmo com títulos, é um pouco mais complexo. Armazene todas as imagens de navegação com as páginas de destino. Se você usar duas imagens para normal e ativa, poderá usar os seguintes scripts:

* Um script que exibe a página como normal.
* Um script que processa solicitações &quot;. normais&quot; e retorna a imagem normal.
* Um script que processa solicitações &quot;. ativas&quot; e retorna a imagem ativada.

É importante criar essas imagens com a mesma alça de nomeação que a página, para garantir que uma atualização de conteúdo exclua essas imagens e a página.

Para páginas que não são modificadas, as imagens ainda permanecem no cache, embora as próprias páginas geralmente sejam invalidadas automaticamente.

## Personalização {#personalization}

O Dispatcher não pode armazenar dados personalizados em cache, portanto recomenda-se limitar a personalização ao local necessário. Para ilustrar por que:

* Se você usar uma página inicial gratuita, essa página precisará ser composta toda vez que um usuário o solicitar.
* Se, por outro lado, você oferecer uma escolha de 10 páginas iniciais diferentes, poderá armazenar em cache cada um deles, melhorando o desempenho.

>[!NOTE]
>
>Se você personalizar cada página (por exemplo, colocando o nome do usuário na barra de título) não poderá cachá-la, o que pode causar um impacto importante no desempenho.
>
>No entanto, se você precisar fazer isso, é possível:
>
>* use iframes para dividir a página em uma parte que é a mesma para todos os usuários e uma parte que é a mesma para todas as páginas do usuário. Em seguida, é possível colocar em cache ambas as partes.
>* usar javascript do cliente para exibir informações personalizadas. Entretanto, verifique se a página ainda é exibida corretamente se um usuário desativar o javascript.
>



## Conexões adesivas {#sticky-connections}

[Conexões adesivas](dispatcher.md#TheBenefitsofLoadBalancing) garantem que os documentos para um usuário sejam compostos no mesmo servidor. Se um usuário sair desta pasta e posteriormente retornar a ele, a conexão ainda será aberta. Defina uma pasta para manter todos os documentos que exigem conexões adesivas para o site. Tente não ter outros documentos nela. Isso afeta o balanceamento de carga se você usar páginas personalizadas e dados de sessão.

## Tipos MIME {#mime-types}

Há duas maneiras de um navegador determinar o tipo de arquivo:

1. Por sua extensão (por exemplo.html. gif.jpg etc)
1. Pelo tipo MIME que o servidor envia com o arquivo.

Para a maioria dos arquivos, o tipo MIME é implícito na extensão do arquivo. Ou seja:

1. Por sua extensão (por exemplo.html. gif.jpg etc)
1. Pelo tipo MIME que o servidor envia com o arquivo.

Se o nome do arquivo não tiver uma extensão, ele será exibido como texto sem formatação.

O tipo MIME faz parte do cabeçalho HTTP e, dessa forma, o Dispatcher não o armazena em cache. Se o aplicativo AEM retorna arquivos que não têm um arquivo reconhecido, mas dependem do tipo MIME, esses arquivos podem ser exibidos incorretamente.

Para garantir que os arquivos sejam armazenados em cache corretamente, siga estas orientações:

* Certifique-se de que os arquivos sempre tenham a extensão apropriada.
* Evitar scripts de envio de arquivos genéricos, que têm urls como download. jsp? arquivo = 2214. Grave novamente o script para usar urls contendo a especificação do arquivo; para o exemplo anterior seria baixar .2214. pdf.

