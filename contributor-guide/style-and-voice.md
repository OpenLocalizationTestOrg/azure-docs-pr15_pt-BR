<properties title="" pageTitle="Escrever documentação do Azure - estilo e voz roteiro" description="Informações de estilo e voz para ajudá-lo a criar conteúdo técnico para o Centro de documentação Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="12/16/2014" ms.author="glenga" />

#<a name="writing-azure-documentation---style-and-voice-cheat-sheet"></a>Escrever documentação do Azure - estilo e voz roteiro

Veja aqui uma folha de enganar que contém ponteiros sobre como escrever artigos técnicos para os serviços do Azure e tecnologias. Estas diretrizes se aplicam se você estiver criando nova documentação ou atualizando documentação existente.

No mínimo, faça:

- Verificação ortográfica e gramatical seus tópicos, mesmo se tiver recortar e colar no Word fazê-lo.
- Use uma voz casual e amigável, como você estava falando com outra pessoa individuais.
- Use frases simples. Eles são mais fácil de compreender e mais facilmente, eles são convertidos pela tradutores humanos e máquina.

As seções a seguir contêm um mais detalhes:

+ [Use uma voz amigável para o cliente]
+ [Considere a possibilidade de localização e tradução automática]
+ [Outros problemas de estilo e voz para observar]


##<a name="use-a-customer-friendly-voice"></a>Use uma voz amigável para o cliente

Podemos desejam siga esses princípios quando podemos escrever conteúdo técnico para Azure. Talvez não sempre recebemos lá, mas precisamos continue tentando!

- **Use palavras diárias**: tente usar linguagem natural, as palavras que seus clientes usam; ser menos formais, mas não menos técnicos; Forneça exemplos que explicam novos conceitos.

- **Escrever de maneira concisa**: não percam palavras e não explique acima. Ser afirmativa e não use palavras extras ou muitas qualificadores. Manter as frases curtas e concisas. Manter seu tópico de foco. Se uma tarefa tem um qualificador, colocá-lo no início da sentença ou parágrafo. Além disso, mantenha o número de notas para mínimo. Use uma captura de tela quando ele pode salvar palavras.

- **Fácil de examinar**: colocar as coisas mais importantes primeiro. Use seções para divida procedimentos longos em grupos mais gerenciáveis de etapas (procedimentos com mais de 12 etapas provavelmente são muito). Use uma captura de tela quando ele adiciona clareza.

- **Mostrar forma solidária**: Use um tom de apoio no artigo e manter isenções mínimo. Honestamente destacar áreas que serão frustrantes aos clientes. Certificar-se de que o artigo se concentra no que é relevante para o cliente, não basta dar uma palestra técnica.

##<a name="consider-localization-and-machine-translation"></a>Considere a possibilidade de localização e tradução automática
Nossos artigos técnicos são convertidos em muitos outros idiomas e alguns são modificados para mercados específicos. As pessoas também esteja usando tradução automática na web para ler os artigos técnicos. Portanto, escreva com as seguintes diretrizes em mente:

- **Verifique se o artigo não contém nenhuma gramática, ortografia ou erros de pontuação**: isso é algo que devemos fazer em geral. Teclado de redução 2.0 possui um verificador ortográfico básica, mas você também deve Colar (HTML renderizado) conteúdo do artigo no Word, que tem uma verificação ortográfica e gramatical mais robusto.

- **Tornar as mais breve possível de frases**: frases de compostas ou cadeias de cláusulas dificultam a tradução. Divida sentenças se você pode fazer isso sem sendo muito redundantes ou estranho com sons. Realmente não queremos artigos escritos no idioma artificial ou.

- **Construção de sentença simples e consistente de uso**: consistência é melhor para tradução. Evitar parentheticals e asides e ter o assunto como perto do início da sentença possível. Confira alguns tópicos publicados - se o tópico tiver um amigável, fácil de ler estilo, usá-lo como um modelo.

- **Uso de maiusculas e palavras consistente de uso**: novamente, consistência é fundamental. Azure usa a capitalização de frase para títulos, portanto nunca maiuscula na palavra se ele não estiver no início de uma frase ou um substantivo PRI.

- **Incluir "palavras pequenas"**: palavras que consideramos pequenas e sem importância em inglês porque eles são compreendidos para contexto (como "a", "a" "que" e "é") são fundamentais para tradução automática - certificar-se de que você incluí-los!

##<a name="other-style-and-voice-issues-to-watch-for"></a>Outros problemas de estilo e voz para observar

- Não quebra etapas com comentários ou asides.

- Para obter etapas que incluem trechos de código, coloque informações adicionais sobre a etapa o código como comentários. Isso reduz a quantidade de texto que as pessoas precisam ler e as informações da chave são copiadas para o projeto de código para lembrá-pessoas do que o código está fazendo quando eles consultá-lo posteriormente.

- O nome do produto oficial é "Microsoft Azure", mas podemos quase sempre apenas dizer "Azure", como "Azure Mobile Services".

- Não crie abreviações que começam com "MA" ou "R." Basta usar "Azure" na primeira referência antes de um nome de serviço ou recurso e, em seguida, solte-(por exemplo, "Azure Mobile Services" fica "Serviços Mobile" após o primeiro uso). Evite abreviações em geral, eles apenas confundir as pessoas.

- Azure usa a capitalização de frase para todos os títulos.

- Use "entrar" e não "log-in."

- Inclua as palavras "seguir" ou "da seguinte maneira" em cada frase que precede um trecho de código ou lista.

- "Banco de dados SQL" é o recurso Azure. Um "banco de dados SQL" é uma instância de banco de dados em execução no banco de dados SQL.

- Armazenamento do Azure inclui vários "data management services" que incluem o serviço de tabela, o serviço de Blob e o serviço de fila. (Ele não tem chamado o "serviço de armazenamento de tabela do Microsoft Azure").




###<a name="contributors-guide-links"></a>Links de guia dos colaboradores

- [Artigo de visão geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)



<!--Anchors-->
[Use uma voz amigável para o cliente]: #use-a-customer-friendly-voice
[Considere a possibilidade de localização e tradução automática]: #consider-localization-and-machine-translation
[outros problemas de estilo e voz para observar]: #other-style-and-voice-issues-to-watch-for
