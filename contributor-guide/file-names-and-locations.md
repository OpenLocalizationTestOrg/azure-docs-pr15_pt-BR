<properties title="" pageTitle="Nomes de arquivo e locais para artigos técnicos do Azure" description="Explica a estrutura de arquivo para artigos e as convenções de nomenclatura que você deverá seguir quando você cria um novo artigo." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Nomes de arquivo e locais para artigos técnicos do Azure

Em nosso repositório de conteúdo técnico, usamos uma única pasta ( **artigos** ) para todos os artigos. Há uma hierarquia de pasta - todos os artigos live na estrutura de arquivo simples. Se você criar pastas com artigos nelas, os artigos não podem ser publicados.

Em vez de usar uma estrutura de arquivo como um princípio organizar, podemos usar uma convenção de nomenclatura de arquivo estrito que identifica claramente tópicos e que contribui em direção a capacidade de descoberta na web.

Veja aqui o que você precisa saber:

+ [Regras]
+ [Padrão]
+ [Exemplos padrão]
+ [Conteúdo do Marketplace]
+ [Aprovação de nome de arquivo]

##<a name="rules"></a>Regras

- Nomes de arquivos podem conter apenas letras minúsculas, números e hifens. 
- Sem espaços ou caracteres de pontuação. Use os hifens para separar palavras e números no nome do arquivo.
- Não mais do que 80 caracteres - este é um limite de sistema de publicação
- Verbos de ação de uso específicos, como desenvolvem, compram, construir, solucionar problemas. Sem palavras - ing.
- Sem palavras pequenas - não incluir um e, a, em ou, etc.
- Todos os arquivos devem estar em redução e use a extensão de arquivo .md.
- Digite as palavras Evite abreviações não aprovadas ou desnecessárias em nomes de arquivo

Abreviações e initialisms em nomes de arquivo - diretrizes específicas:

- Não abreviar nomes de serviço Azure - as primeiras palavras do nome do arquivo devem ser o padrão, nome de serviço ou tecnologia Azure por extenso. 
-   Não permitir rm ou arm como abreviações em qualquer lugar em um nome de arquivo
- Outras abreviações padrão da indústria são aceitáveis conforme necessário em nomes de arquivo. 

##<a name="pattern"></a>Padrão

Aqui está o padrão geral:

 **Service-Platform-Language-Content-Product-Version.MD**

Use as partes do padrão que são aplicáveis e examine a lista de artigos no repositório para ter uma ideia de nomes existentes. Nomes que não começam com uma plataforma de desenvolvimento ou um nome de serviço é provavelmente suspeito e adiada por meio.

##<a name="standard-examples"></a>Exemplos padrão

Aqui estão alguns exemplos de nomes válidos que seguem o padrão. :

- nuvem-serviços-dotnet-contínuo-delivery.md
- Mobile-serviços-ios-get-started.md
- account.md gerenciar documentdb
- Mobile-Services-dotnet-backend-Get-Started-Settings-Sync.MD
- Active-Directory-Java-Authenticate-Users-Access-Control-Eclipse.MD
- Virtual-Machines-Install-Windows-Server-2008r2.MD
- cache-aspnet-sessão-provedor de estado
- Azure-SDK-dotnet-Release-Notes-2-8
- storsimple-Disaster-Recovery-Using-Azure-Site-Recovery

##<a name="marketplace-content"></a>Conteúdo do Marketplace

Para distinguir conteúdo que se concentra em contribuições de parceiro para o Azure marketplace, inicie os nomes de arquivo com "marketplace". Esse conteúdo não deve ser muito comum, como a maioria dos conteúdo de parceiro deve ser criado nos sites dos parceiros.

- Marketplace-MongoDB-Virtual-Machines-Install-Windows-Server-2008r2.MD

##<a name="file-name-approval"></a>Aprovação de nome de arquivo

É o trabalho de nosso grupo de revisores de solicitação de recepção para revisar os nomes de arquivo quando um novo arquivo é enviado para o repositório pela primeira vez. Revisores de solicitação de recepção devem revisar o nome do arquivo e fornecer comentários por meio do fluxo de comentário de solicitação de recepção se forem necessárias alterações. O nome do arquivo deve ser corrigidos antes que a solicitação de recepção seja aceito. Os colaboradores facilmente podem enviar a atualização para a solicitação de recepção pendente.

##<a name="folder-names-in-the-repo"></a>Nomes de pasta na repo

Pastas devem ser criadas somente para serviços, e o nome do arquivo deve corresponder a Libra massa de serviço. Use somente letras e hífens e todas as letras minúsculas. Obter a aprovação do administrador do repositório antes de criar uma nova pasta que não seja para um serviço de lançamento.

##<a name="changing-case-in-file-names"></a>Alterar maiusculas e minúsculas em nomes de arquivo

Sistemas operacionais Windows diferenciam maiusculas de minúsculas, portanto se você precisar alterar um nome de arquivo para corrigir maiusculas e minúsculas, é melhor fazer uma alteração sólida, a menos que você é capaz de fazer a alteração em um Linux ou Mac. Por exemplo:

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Use o seguinte comando para renomear um arquivo:
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Links de guia dos colaboradores

- [Artigo de visão geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)


<!--Anchors-->
[Regras]: #rules
[Padrão]: #pattern
[Exemplos padrão]: #standard-examples
[Conteúdo do Marketplace]: #marketplace-content
[Aprovação de nome de arquivo]: #file-name-approval
