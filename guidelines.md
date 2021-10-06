---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
workflow-type: ht
source-wordcount: '752'
ht-degree: 100%

---
# Diretrizes para colaboração na documentação do Adobe Experience Manager

## Filosofia da documentação

Sabemos que os usuários do Adobe Experience Manager trabalham em ambientes extremamente competitivos, esforçando-se para criar experiências digitais que as destaquem de seus concorrentes. Portanto, é crucial que, ao oferecer novas ferramentas avançadas no AEM, a Adobe as complemente com documentação precisa e transparente para permitir que o cliente aproveite imediatamente seu investimento no AEM e potencialize o ROI.

O objetivo é colocar a documentação do AEM nas mãos de seus usuários assim que possível. Portanto, priorizamos uma documentação precisa e utilizável, e nos esforçamos para atualizá-la e aprimorá-la continuamente.

## Contribuições à documentação

Para melhorar continuamente a documentação do AEM, toda a comunidade de usuários do AEM é bem-vinda para contribuir em sua elaboração. Seja por meio de pull requests ou problemas, as melhorias na documentação podem ser correções, esclarecimentos, expansões e exemplos adicionais.

## Padrões de documentação

Embora contribuições à documentação do AEM sejam bem-vindas, sejam em formato de pull requests ou em formato de um problema, elas deverão estar em conformidade com nossos padrões de contribuição e de documentação.

As contribuições que não cumprirem esses padrões poderão ser rejeitadas.

### Nós documentamos casos de uso padrão.

A documentação do AEM abrange casos de uso padrão. Casos de uso que não se enquadrarem no escopo de instalação e de uso padrão do produto não farão parte da documentação do AEM.

### Geralmente não documentamos bugs e suas soluções.

A documentação do AEM abrange casos de uso padrão. Por essa razão, os bugs, seus efeitos e soluções alternativas geralmente não são documentados.

As exceções a essa regra aplicam-se às notas de versão, nas quais problemas conhecidos podem ser listados com possíveis soluções que foram aprovadas pelo Gerenciamento de produtos do AEM.

### As contribuições à documentação não se destinam a responder perguntas técnicas.

Quaisquer ideias para melhorar a documentação do AEM são bem-vindas como contribuições. No entanto, comentários, problemas e pull requests destinam-se somente a *contribuições*, não para responder suas perguntas sobre como usar o AEM, implementar seu projeto do AEM ou resolver problemas técnicos.

Quaisquer dúvidas sobre o uso do AEM ou erros técnicos devem ser notificados por meio do processo normal de suporte pelo [portal Experience Cloud Enterprise Support](https://helpx.adobe.com/br/contact/enterprise-support.ec.html) ou discutidas na [comunidade do Experience Manager](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager).

***As contribuições à documentação do AEM não substituem o Atendimento ao cliente da Adobe***. Logo, quaisquer contribuições que buscarem respostas a perguntas relacionadas a suporte serão rejeitadas.

### As contribuições devem mencionar claramente as páginas pertinentes à documentação.

Se você criar um problema para sugerir melhorias na documentação, deverá incluir links para as páginas afetadas. Caso crie um problema usando o link **Editar esta página** em uma página de documentação, o problema será criado automaticamente com um link para a página.

Isso não se aplica a pull requests, que já fazem referência às páginas afetadas.

## Diretrizes de documentação

Solicitamos que qualquer contribuição à nossa documentação siga determinados guias de estilo.

Seguir essas diretrizes facilita a revisão de sua contribuição, o que agiliza a integração à nossa documentação.

### Idioma e estilo

#### Idioma

* A documentação do AEM foi criada e mantida em inglês americano.
* Mantenha as sentenças o mais simples possível.
* Use linguagem clara e concisa.

Lembre-se de que os leitores da documentação do AEM estão espalhados ao redor do mundo, e não espera-se que sejam falantes nativos ou fluentes em inglês. Evite linguagem coloquial, mantendo-a a mais clara e simples possível.

#### Siga o Manual de estilo da Microsoft

[O Manual de estilo da Microsoft](https://docs.microsoft.com/pt-br/style-guide/welcome/) é um guia de estilo disponível gratuitamente. Ele se concentra na documentação de softwares, e a documentação do AEM o segue sempre que possível.

### Formatação

| Item | Estilo |
|---|---|
| Elemento ou opção da interface do usuário | **negrito** |
| Nome do arquivo, caminho, entrada do usuário, valores de parâmetro | `monospaced` |
| Código, linha de comando | ```Code Block``` |

### Capturas de tela

As capturas de tela devem ser utilizadas com critério e somente quando uma descrição textual for insuficiente.

Marcadores ou outras anotações em capturas de tela (como quadros vermelhos, setas ou texto) não devem ser usados. Dessa forma, as capturas de tela são mais facilmente reutilizadas ou replicadas em versões localizadas da documentação.

### Referências específicas à versão

Evite referências diretas a uma versão específica em todo o conteúdo da documentação, sempre que possível. Isso torna a documentação mais flexível e extensível para versões futuras.

### Uso do Day, AEM, CQ, CRX

O produto sempre deve ser mencionado com o nome completo, **Adobe Experience Manager**, na primeira vez em um artigo. Depois disso, podemos usar **AEM**.

Não se deve usar Day, Day Software, CQ e CRX, exceto quando for inevitável, como em nomes de classe ou em referência ao histórico do AEM.