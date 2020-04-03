---
source-git-commit: 053f90d9a0574c7d470cec6794498919e3340713
translation-type: tm+mt

---
# Diretrizes para contribuir com a documentação do Adobe Experience Manager

## Filosofia da documentação

Sabemos que os usuários do Adobe Experience Manager estão trabalhando em ambientes altamente competitivos, lutando para criar experiências digitais que os separarão de seus concorrentes. Portanto, é vital que, quando a Adobe fornecer novas ferramentas avançadas no AEM, essas ferramentas sejam complementadas com documentação precisa e clara para permitir que o cliente aproveite imediatamente seu investimento no AEM e maximize o ROI.

O objetivo da documentação do AEM é colocar a documentação nas mãos dos usuários do AEM o mais rápido possível. Portanto, priorizamos a documentação precisa e utilizável e nos esforçamos para atualizá-la continuamente e aprimorá-la.

## Contribuições de documentação

Para melhorar continuamente a documentação do AEM, a comunidade inteira de usuários do AEM é bem-vinda para contribuir com a documentação. Seja por meio de solicitações ou problemas, as melhorias na documentação podem ser correções, esclarecimentos, expansões e exemplos adicionais.

## Normas de documentação

Embora possamos receber contribuições para nossa documentação, qualquer contribuição para a documentação do AEM, seja na forma de solicitação por push ou problema, deve estar em conformidade com nossos padrões de contribuição e documentação.

As contribuições que não cumprem estes padrões podem ser rejeitadas.

### Nós documento casos de uso padrão.

A documentação do AEM cobre casos de uso padrão. Casos de uso além do escopo de instalação padrão e uso do produto não fazem parte da documentação do AEM.

### Geralmente, não documento erros nem as suas soluções.

A documentação do AEM cobre casos de uso padrão. Por essa razão, os erros, os efeitos causados por erros e as soluções alternativas para erros geralmente não estão documentados.

As exceções a essa regra se aplicam às notas de versão onde os problemas conhecidos podem ser listados com possíveis soluções que foram aprovadas pelo Gerenciamento de produtos AEM.

### As contribuições para a documentação não se destinam a responder a questões técnicas.

Quaisquer ideias que você precisar melhorar a documentação do AEM são bem-vindas como contribuições. No entanto, comentários, problemas e solicitações de baixa automática são destinados apenas a *contribuições* . Eles não devem ser usados para responder suas perguntas sobre como usar o AEM, implementar seu projeto do AEM ou resolver problemas técnicos.

Quaisquer dúvidas sobre o uso do AEM ou erros técnicos que possam ter sido relatados por meio do processo de suporte normal por meio do portal [de suporte corporativo da](https://helpx.adobe.com/contact/enterprise-support.ec.html) Experience Cloud ou discutidos na comunidade [do](https://forums.adobe.com/community/experience-cloud/marketing-cloud/experience-manager)Experience Manager.

***As contribuições para a documentação do AEM não são uma substituição do Atendimento*** ao cliente da Adobe, e qualquer contribuição que buscar respostas para perguntas relacionadas ao suporte será rejeitada.

### As contribuições devem mencionar claramente as páginas de documentação afetadas.

Se você criar um problema para sugerir melhorias na documentação, deverá incluir links para as páginas afetadas. Caso crie um problema usando o link **Editar esta página** em uma página de documentação, o problema será criado automaticamente com um link para a página.

Isso não se aplica a solicitações de baixa automática, pois as solicitações de baixa automática por sua natureza fazem referência às páginas afetadas.

## Diretrizes de documentação

Solicitamos que qualquer contribuição para nossa documentação siga determinadas diretrizes de estilo.

Seguir essas diretrizes facilita a revisão de sua contribuição e, portanto, a integração à nossa documentação é mais rápida.

### Idioma e estilo

#### Idioma

* A documentação do AEM foi criada e mantida em inglês dos EUA.
* Mantenha as frases o mais simples possível.
* Mantenha o idioma claro e conciso.

Lembre-se de que os leitores da documentação do AEM são do mundo inteiro e não é de se esperar que sejam falantes nativos ou fluentes do inglês. Evite coloquialismos e mantenha a linguagem o mais clara e simples possível.

#### Siga o Manual de estilo da Microsoft

[O Manual de estilo](https://docs.microsoft.com/en-us/style-guide/welcome/) da Microsoft é um guia de estilo de documentação disponível gratuitamente, que foca na documentação do software e a documentação do AEM segue este guia sempre que possível.

### Formatação

| Item | Estilo |
|---|---|
| Elemento ou opção da interface do usuário | **negrito** |
| Nome do arquivo, caminho, entrada do usuário, valores de parâmetro | `monospaced` |
| Código, linha de comando | ```Code Block``` |

### Capturas de tela

As capturas de tela devem ser utilizadas de forma sensata e apenas quando uma descrição textual for insuficiente.

Marcadores ou outras anotações em capturas de tela (como quadros vermelhos, setas ou texto) não devem ser usados. Dessa forma, as capturas de tela são mais fáceis de reutilizar ou replicar em versões localizadas da documentação.

### Referências específicas à versão

Tente evitar referências diretas a uma versão específica em todo o conteúdo da documentação, sempre que possível. Isso torna a documentação mais flexível e extensível para versões futuras.

### Uso do dia, AEM, CQ, CRX

O produto sempre deve ser referenciado pelo nome completo **Adobe Experience Manager** pela primeira vez em um artigo e pode, a partir de então, ser chamado de **AEM**.

O Dia, o Day Software, o CQ e o CRX não devem ser usados, exceto quando inevitável, como em nomes de classe ou referência ao histórico do AEM.