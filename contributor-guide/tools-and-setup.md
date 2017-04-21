<properties
pageTitle="Instalar e configurar ferramentas de autoria em GitHub"
description="Ferramentas e etapas para obter configurada para a criação de conteúdo Azure no GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Instalar e configurar ferramentas de autoria em GitHub

Siga as etapas neste artigo para configurar ferramentas por contribuir para a documentação técnica Azure. Os colaboradores casuais e eventuais provavelmente podem usar o GitHub UI descrito na etapa 2.

Se você estiver familiarizado com gito, talvez você queira examinar terminologia gito: [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). Além disso, esse thread StackOverflow contém um glossário de termos de gito você encontrará neste conjunto de etapas: [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Conteúdo

- [Criar uma conta de GitHub e configurar o seu perfil](#create-a-github-account-and-set-up-your-profile)
- [Inscreva-se no Disqus](#sign-up-for-disqus)
- [Determine se você realmente precisa seguir o restante destas etapas](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Permissões no GitHub](#permissions-in-github)
- [Instalar gito para Windows](#install-git-for-windows)
- [Habilitar a autenticação de dois fatores](#enable-two-factor-authentication)
- [Instalar um editor de redução](#install-a-markdown-editor)
- [Configurar Atom](#configure-atom)
- [Bifurcação repositório e copiá-lo com seu computador](#fork-the-repository-and-copy-it-to-your-computer)
- [Configurar seu nome de usuário e o email localmente](#configure-your-user-name-and-email-locally)
- [Próximas etapas](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Criar uma conta de GitHub e configurar o seu perfil

Para contribuir para o conteúdo técnico Azure, você precisará de uma conta de [GitHub](http://www.github.com) .

Se você for um colaborador da Microsoft, você precisa configurar sua conta de GitHub para que você está claramente identificados como funcionário da Microsoft. Configure seu perfil da seguinte maneira:

- **Imagem de perfil**: uma imagem da você (obrigatório)
- **Nome**: seu nome e sobrenome (obrigatório)
- **Email**: seu endereço de email da Microsoft (opcional)
- **Empresa**: Microsoft Corporation (obrigatório)
- **Local**: listar seu local (opcional)

Seu perfil deve ser semelhante a este perfil:

<p align="center">
 ![Exemplo de perfil de GitHub](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>Inscreva-se no Disqus

Cada artigo técnico Azure publicado tem um fluxo de comentário fornecido pelo serviço Disqus.

 ![Logotipo de Discuta](./media/tools-and-setup/discus.png)

Se você for um funcionário da Microsoft e se você for o autor do ou um colaborador para um artigo, você precisa se inscrever no Disqus para que você possa participar no fluxo de comentário para o artigo.

1. Inscreva-se em uma conta em [http://www.disqus.com/](http://www.disqus.com/)
2. Preencha o seu perfil da seguinte maneira:

 - **Nome completo**: seu nome completo como exibido na sua listagem de catálogo de endereços do Microsoft, além das informações entre colchetes, que é seu alias plus @MSFT. Formato: *primeiro último [alias@MSFT] *
 - **Local**: seu local
 - **Bio curto**: seu cargo

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Determine se você realmente precisa seguir o restante destas etapas

Você não precisará seguir todas as etapas neste artigo. Isso depende da classificação da contribuição de conteúdo que você deseja ou precisa fazer.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Enviar uma alteração somente texto em um artigo existente

Se você só precisa ou deseja fazer atualizações textuais um artigo existente, você provavelmente não é necessário seguir o restante das etapas. Você pode usar o editor de redução baseado na web do GitHub para enviar suas alterações. Basta clicar no link de GitHub no artigo que você deseja modificar:

 ![Exemplo de perfil de GitHub](./media/tools-and-setup/contributetogit.png)

 Clique no ícone de edição na versão GitHub do artigo

 ![Exemplo de perfil de GitHub](./media/tools-and-setup/editicon.PNG)

 Que abre o editor de web fáceis de usar que torna mais fácil enviar alterações. Você não precisa acompanhar as outras etapas neste artigo.

###<a name="all-other-changes"></a>Todas as outras alterações
A UI GitHub dá suporte à criação de novos arquivos e arrastar e soltar imagens. No entanto, quando você trabalha na interface de usuário, gerenciar ramificações pode ser confuso portanto, geralmente recomendamos que você instale as ferramentas e aprender os comandos para criar e gerenciar artigos. Se você quiser usar a interface do usuário, consulte:

- [Criando arquivos em Github](https://github.com/blog/1327-creating-files-on-github)
- [Carregar arquivos para seus repositórios](https://github.com/blog/2105-upload-files-to-your-repositories)

Os seguintes tipos de trabalho, é altamente recomendável para instalar e saiba como usar as ferramentas:

 - Fazer alterações importantes para um artigo
 - Criar e publicar um novo artigo
 - Adicionando novas imagens ou atualizando imagens
 - Atualizando um artigo durante um período de dias sem publicação alterações a cada esses dias
 - Criando conteúdo para uma versão que precisa ser publicado em um determinado dia em um determinado tempo

##<a name="permissions-in-github"></a>Permissões no GitHub

Qualquer pessoa com uma conta de GitHub pode contribuir para conteúdo técnico Azure por meio de nosso repositório público em [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). Sem permissões especiais são necessárias.

Se você for um PM Microsoft ou gravador que está trabalhando no Azure conteúdo, você deve trabalhar no nosso particular do repositório de conteúdo, azure-conteúdo-pr. Visite [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) para solicitar as permissões de leitura que permitirá que você faça contribuição por meio de repo a privada - entrar no GitHub usando o botão > clique Azure > clique em **ingressar uma equipe** ou **ingressar em outra equipe**e em seguida, procure e ingressar no grupo **azure de conteúdo de leitura** .

## <a name="install-git-for-windows"></a>Instalar gito para Windows

Instale gito para Windows do [http://git-scm.com/download/win](http://git-scm.com/download/win). Este download instala o sistema de controle de versão gito e instala gito Bash, o aplicativo de linha de comando que você usará para interagir com seu repositório gito local.

Você pode aceitar as configurações padrão; Se você quiser os comandos disponíveis dentro da linha de comando do Windows, selecione a opção que permite que ele.

<p align="center">
 ![Exemplo de perfil de GitHub](./media/tools-and-setup/gitbashinstall.png)

(Observação: isso não é a mesma que "Github para Windows". "Github para Windows" é uma ferramenta diferente baseado em interface gráfica que também funcionará se você quiser ler sobre si mesmo. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Habilitar a autenticação de dois fatores

Você precisa habilitar a autenticação de dois fatores (2FA) em sua conta do GitHub se você estiver trabalhando no repositório de conteúdo particular. É necessário no repositório particular.

Para permitir isso, siga as instruções em ambas as seguintes GitHub tópicos da Ajuda:

- [Informações sobre autenticação de dois fatores](https://help.github.com/articles/about-two-factor-authentication/)
- [Criando um token de acesso para uso de linha de comando](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Quando você cria o símbolo, selecione todos os escopos disponíveis na criação de token UI ([para obter detalhes sobre cada escopo](https://developer.github.com/v3/oauth/#scopes))

Depois de habilitar 2FA, você precisa digitar o token de acesso em vez de sua senha GitHub no prompt de comando ao tentar acessar um repositório GitHub da linha de comando. O token de acesso não é o código de autenticação que você obtém em uma mensagem de texto quando você configura 2FA. É uma cadeia de caracteres longa que esta aparência: fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Algumas observações sobre isto:

- Quando você cria seu token de acesso, salve-o em um arquivo de texto para tornar mais acessíveis quando precisar.

- Mais tarde, quando você precisa colar o token, sabe há duas maneiras para colar na linha de comando:

 - Clique no ícone no canto superior esquerdo da janela da linha de comando > Editar > Colar.
 - Clique com botão direito no ícone no canto superior esquerdo da janela e clique em Propriedades > Opções > modo de edição rápida. Isso configura a linha de comando para que você possa colar clicando na janela de linha de comando.

## <a name="install-a-markdown-editor"></a>Instalar um editor de redução

Podemos criar conteúdo usando a notação de simples "redução" nos arquivos, em vez de complexo "marcação" (HTML, XML, etc.). Portanto, você precisará instalar um editor de redução.

- **Atom**: a maioria de nós usa o editor de redução de Atom do GitHub: [http://atom.io](http://atom.io). Ele não exige uma licença para uso comercial. Ela tem a verificação ortográfica.

- **O bloco de notas**: você pode usar o bloco de notas para uma opção muito simples.

- **Prosa**: Este é um editor de redução de origem leve, elegante, on-line e abrir que oferece uma visualização. Visite [http://prose.io](http://prose.io) e autorizar prosa no seu repositório.

- **[Código do visual Studio](https://www.visualstudio.com/products/code-vs.aspx)** - entrada da Microsoft neste espaço.

## <a name="configure-atom"></a>Configurar Atom

Se você usar Atom, você precisará configurar algumas coisas.

- Atom padronizado para usar 2 espaços para as guias, mas redução espera 4 espaços. Se você deixá-lo no padrão de dois, seu artigo ficarão ótimo no modo de visualização local, mas não quando ele é importado para Azure. Portanto, configurar Atom para usar 4 espaços - você pode encontrar essa configuração em arquivo > Configurações > configurações do Editor > guia tamanho.
- Você provavelmente também quiser ativar quebrar suave nesta seção também, qual é a mesma que "quebra automática" no bloco de notas.
- Para ativar a visualização de redução, clique em pacotes > visualização de redução > Alternar visualização. Você pode usar Ctrl-Shift-M para alternar a visualização do modo de exibição HTML.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Bifurcação repositório e copiá-lo com seu computador

1. Criar uma bifurcação do repositório no GitHub - vá para a parte superior direita da página e clique no botão de bifurcação. Se solicitado, selecione sua conta como o local onde a bifurcação deve ser criada. Isso cria uma cópia do repositório em sua conta gito Hub. Em geral, escritores técnicos e gerentes de programa precisam bifurcação azure-conteúdo-pr, o repo particular. Colaboradores da comunidade precisam bifurcação azure conteúdo, a repo público. Você só precisa bifurcação uma vez; Após a configuração primeira, se desejar copiar seu bifurcação para outro computador, você só precisa executar os comandos que acompanha desta seção para copiar o repo para seu computador.  Se você optar por criar bifurcações de ambos os repositórios, você precisará criar uma bifurcação para cada repositório.

2. Copie o pessoal Token de acesso obtido de [https://github.com/settings/tokens](https://github.com/settings/tokens). Você pode aceitar as permissões padrão para o símbolo.  Salve o Token de acesso pessoal em um arquivo de texto para reutilização posterior.

3. Em seguida, copie o repositório com seu computador com credenciais incorporadas na cadeia de comando.  Para fazer isso, abra gito Bash e executá-lo como um administrador. No prompt de comando, digite o seguinte comando.  Esse comando cria um diretório azure-content(-pr) em seu computador.  Se você estiver usando o local padrão, ele ficará em c:\users<your Windows user name>\azure-content(-pr).

Repo público:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Repo particular:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Por exemplo, este comando clonar poderia ser algo parecido com isto:

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Definir a conexão do repositório remoto e configurar credenciais

Crie uma referência para o repositório raiz inserindo esses comandos. Configura conexões para o repositório no GitHub para que você possa obter as alterações mais recentes em seu computador local e enviar as suas alterações de volta para GitHub. Esse comando também configura seu token localmente para que você não precisará inserir seu nome e senha cada vez que você tentar acessar o repo upstream e seu garfo GitHub.

Repo público:

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Repo particular:

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Isso geralmente demorada. Depois de fazer isso, você não precisa bifurcação novamente ou insira suas credenciais novamente. Você só precisa copiar as bifurcações para um computador local novamente se você configurar as ferramentas em outro computador.


## <a name="configure-your-user-name-and-email-locally"></a>Configurar seu nome de usuário e o email localmente

Para garantir que é exibidos corretamente como um colaborador, você precisa configurar o seu nome de usuário e o email localmente em gito.

1. Comece gito Bash e alternar para o azure-conteúdo ou azure-conteúdo-pr:

   ````
   cd azure-content
   ````

 ou

   ````
   cd azure-content-pr
   ````

2. Configure seu nome de usuário para que ela corresponda seu nome como você pode configurá-lo em seu perfil GitHub:

    ````
    git config --global user.name "John Doe"
    ````
3. Configurar o email para que ela corresponda o email primário designado em seu perfil GitHub; Se você for um funcionário MSFT, ele deve ser seu endereço de email MSFT:

    ````
    git config --global user.email "alias@example.com"
    ````
4. Tipo de `git config -l` e examine as configurações de locais para garantir que o nome de usuário e email na configuração estão corretas.

##<a name="next-steps"></a>Próximas etapas

- Entender o tipo de conteúdo que pertence a repo de conteúdo técnico e saber o que não pertence. Consulte as [orientações de canal conteúdo](./content-channel-guidance.md)!
- Siga [estas etapas para criar ou modificar um artigo e, em seguida, enviá-lo para publicação](./git-commands-for-master.md).
- Copie [o modelo de redução](../markdown templates/markdown-template-for-new-articles.md) como base para um novo artigo.
- Use [Esta lista de verificação para verificar sua solicitação de recepção atenderão aos critérios de qualidade](./contributor-guide-pr-criteria.md) para mesclagem.


###<a name="contributors-guide-navigation"></a>Navegação de guia dos colaboradores

- [Artigo de visão geral](./../README.md)
- [Índice de artigos de orientação](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
