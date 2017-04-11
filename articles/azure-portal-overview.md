<properties
    pageTitle="Visão geral de portal do Microsoft Azure"
    description="Saiba como usar o portal do Microsoft Azure."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Visão geral de portal do Microsoft Azure

O portal do Microsoft Azure é um local central onde você pode provisionar e gerenciar seus recursos Azure.  Este tutorial irá se familiarizar com o portal e mostram como usar alguns desses recursos principais:
- Um **marketplace abrangente** que permite que você navegue por milhares de itens da Microsoft e de outros fornecedores que podem ser comprados e/ou provisionados.
- Uma **experiência de procurar unificado e escaláveis** que torna mais fácil localizar os recursos de seu interesse e realizar várias operações de gerenciamento.
- **Páginas de gerenciamento consistente** (ou blades) que permitem que você gerencie variedade do Azure de serviços por meio de uma maneira consistente de expor configurações, ações, cobrança informações, dados de monitoramento e o uso de saúde e muito mais.
- Uma **experiência pessoal** que permite que você crie uma tela de iniciar personalizada que mostra as informações que você deseja ver sempre que você faça logon.  Você também pode personalizar qualquer uma das lâminas gerenciamento que contêm blocos.

 ![Orientação do Azure UI Portal][UIOrientation]

## <a name="before-you-get-started"></a>Antes de começar

Você precisará de uma assinatura válida do Azure para dar uma olhada neste tutorial.  Se você não tiver uma, depois de [se inscrever para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) hoje.  Quando você tiver uma assinatura, você pode acessar o portal em [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Como criar um recurso

Azure tem um mercado com milhares de itens que você pode criar de um só lugar.  Digamos que você deseja criar uma nova Windows Server 2012 VM.  O + novo hub é seu ponto de entrada em um conjunto curated de categorias em destaque do Marketplace.  Cada categoria tem um conjunto pequeno de itens de destaque juntamente com um link para o marketplace completo que mostra todas as categorias e pesquisa. Para criar esse VM novo do Windows Server 2012, execute as seguintes ações:  

1.  Windows Server 2012 é destacada, você poderá selecioná-la da categoria de computação.  
2.  Preencha algumas entradas básicas em um formulário.
3.  Clique em 'Criar' e sua máquina virtual começará provisionar imediatamente.

O hub de notificações irá alertá-lo quando o recurso foi criado e abrirá uma lâmina de gerenciamento (você pode sempre procurar aos recursos mais tarde).

![Categorias de portal][PortalCategories]


## <a name="how-to-find-your-resources"></a>Como encontrar seus recursos

Você sempre pode fixar recursos frequentemente acessados à sua startboard, mas você pode precisar procurar algo que você não acessar com frequência.  O hub de procurar mostrado abaixo é a maneira de acessar todos os seus recursos.  Você pode filtrar por assinatura, escolha/redimensionar colunas e navegar para as lâminas de gerenciamento clicando em itens individuais.

![Procurar Hub][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Como gerenciar e acesso de representante a um recurso

Deste lâmina que você pode se conectar à máquina virtual usando a área de trabalho remota, monitorar métricas de chave de desempenho, controlar o acesso a essa máquina virtual usando o acesso baseado em função (RBAC), configure a máquina virtual e executar outras tarefas de gerenciamento importantes.  Delegação de acesso baseado em função é essencial para o gerenciamento em escala.  Clique [aqui](./active-directory/role-based-access-control-configure.md) para saber mais sobre ele. Para conceder acesso a um recurso, execute as seguintes ações:

1.  Navegue até o recurso.
2.  Clique em 'Todas as configurações' na seção Essentials.
3.  Na lista de configurações, clique em 'Usuários'.
4.  Clique em 'Adicionar' na barra de comando.
5.  Escolha um usuário e uma função.

![Gerenciando um recurso][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Como personalizar um blade do recurso

Azure configure previamente os blades para seus recursos, mas as peças nessas lâminas são contrário para o controle.  Você pode facilmente ir para personalizar o modo para adicionar, remover, redimensionar ou reorganizar os blocos. Para personalizar uma lâmina, execute as seguintes ações:

1.  Navegue até o recurso.
2.  Clique na '…' na parte superior da lâmina você deseja personalizar.
3.  Clique em 'Adicionar partes'.
4.  Comece arrastando e soltando partes.  

![Personalizando lâminas][CustomizeBlades]

## <a name="how-to-get-help"></a>Como obter ajuda

Se você tiver um problema, estamos aqui para você.  O portal tem uma página de Ajuda e suporte que você pode apontar na direção correta.  Dependendo do seu [plano de suporte](https://azure.microsoft.com/support/plans/), você também pode criar tíquetes de suporte diretamente no portal.  Depois de criar um tíquete, você pode gerenciar o ciclo de vida a permissão de dentro do portal. Você pode acessar a Ajuda e suporte página navegando para procurar -> Ajuda + suporte.  

![Ajuda e suporte][HelpSupport]

## <a name="summary"></a>Resumo

Vamos revisar o que você aprendeu neste tutorial:
- Você aprendeu a inscrever-se, faça uma assinatura e navegue até o portal
- Você tem a usá-lo com o portal de interface do usuário e aprendeu a criar e navegar nos recursos
- Você aprendeu a criar um recurso e navegar nos recursos
- Você aprendeu sobre as estrutura ou gerenciamento lâminas e como você pode gerenciar consistentemente diferentes tipos de recursos
- Você aprendeu como personalizar o portal para trazer as informações que me preocupar com para a frente e Central
- Você aprendeu a controlar o acesso aos recursos usando acesso baseado em função (RBAC)
- Você aprendeu como obter ajuda e suporte

O portal do Microsoft Azure simplifica radicalmente desenvolver e gerenciar seus aplicativos na nuvem.  Dê uma olhada no [blog de gerenciamento](https://azure.microsoft.com/blog/topics/management/) para manter atualizados à medida que estamos constantemente [ouvir comentários](https://feedback.azure.com/forums/223579-azure-preview-portal/) e fazer melhorias.  [Blog do ScottGu](http://weblogs.asp.net/scottgu) é outro ótimo lugar para procurar todas as atualizações do Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
