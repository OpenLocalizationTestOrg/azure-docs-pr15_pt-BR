
<properties 
    pageTitle="Como usar sua assinatura do Office 365 com o Azure RemoteApp | Microsoft Azure"
    description="Saiba como você pode usar sua assinatura do Office 365 no Azure RemoteApp para compartilhar aplicativos do Office."
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-your-office-365-subscription-with-azure-remoteapp"></a>Como usar sua assinatura do Office 365 com o Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Você sabia que você pode usar sua assinatura do Office 365 existente no Azure RemoteApp para compartilhar aplicativos do Office na nuvem? Leia mais para obter informações sobre o Office 365 + RemoteApp Azure opções, incluindo links para artigos sobre o Office 365 que ajudarão a aproveitar ao máximo sua assinatura.

## <a name="how-do-i-use-office-365-accounts-for-azure-remoteapp"></a>Como para usar contas do Office 365 para Azure RemoteApp?
Fazer check-out novo artigo Pedro para todas as informações: [como usar o Azure RemoteApp com contas de usuário do Office 365](remoteapp-o365user.md)

## <a name="can-i-use-my-office-365-subscription-to-run-office-applications-in-azure-remoteapp"></a>Pode usar minha assinatura do Office 365 para executar os aplicativos do Office no Azure RemoteApp?

Sim! Na verdade, usar sua assinatura do Office 365 é a única maneira de colocar seus aplicativos do Office para RemoteApp do Azure.

(Observação: se sua implantação do Azure RemoteApp for entregue por um parceiro de hospedagem, ele poderá fornecer licenças do Office com base em um [Contrato de licença de provedor de serviço](http://www.microsoft.com/en-us/Licensing/licensing-programs/spla-program.aspx))


O importante sobre sua assinatura do Office 365 é que ele permite que você use a mesma licença de usuário em diversas plataformas e ambientes, inclusive a nuvem Azure. Quando você usar os aplicativos do Office em Azure RemoteApp você não precisa adquirir licenças adicionais ou configurar suas licenças existentes de nenhuma forma especial. Tudo que você precisa é uma assinatura do Office 365 que inclui o [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

O Office 365 ProPlus permite a [ativação de computador compartilhado](https://technet.microsoft.com/library/Dn782860.aspx) - esse recurso permite ativação temporária baseada no usuário do Office no virtual e ambientes de nuvem como o Azure RemoteApp (e os serviços de área de trabalho remota).

Quais planos do Office 365 incluem o Office 365 ProPlus? Confira a tabela [disponibilidade do serviço dentro de cada plano](https://technet.microsoft.com/library/office-365-plan-options.aspx) . Observe que nem todos os planos incluem o Office 365 ProPlus (por exemplo, o plano do Office 365 Business). Se seu plano não, considere atualizar para um plano que faz (por exemplo, Office 365 Education E3).

## <a name="ok-so-how-are-my-office-365-proplus-licenses-used-with-azure-remoteapp"></a>Okey, como são meu Office 365 licenças ProPlus usadas com o Azure RemoteApp?

Cada licença de usuário para o Office 365 ProPlus permite que um único usuário ativar a aplicativos do Office em até 5 computadores plus tablets e telefones. Cada ativação está registrada com o usuário até que elas desativar o Office no dispositivo. (Os usuários podem gerenciar seus dispositivos no [portal do Office 365](https://portal.office365.com/)).

Com o Azure RemoteApp um único usuário pode fazer logon em vários computadores no mesmo dia sem perceber. Isso ocorre porque o serviço gerencia automaticamente e escalas de recursos na nuvem, enquanto o usuário vê apenas os aplicativos e os programas que você compartilhou. Para este cenário Office 365 ProPlus oferece um modo de ativação do computador compartilhado - isso significa que o usuário não precisa fazer qualquer gerenciamento de licenças para acessar esses recursos e que os computadores individuais não contam para o limite de ativação do 5 computador.

Como você (o administrador) atribuir licenças do Office 365 ProPlus aos seus usuários, eles podem usar o Office em seus dispositivos pessoais, bem como por meio de seu conjunto de RemoteApp do Azure.

## <a name="which-office-applications-can-i-use-with-office-365-and-azure-remoteapp"></a>Quais aplicativos do Office pode usar com o Office 365 e Azure RemoteApp?

Você pode usar sua assinatura do Office 365 para ativar e compartilhar o Office 2013 em implantações de RemoteApp do Azure. Não suportamos o uso de outras versões do Office com o Azure RemoteApp. Isso inclui o Office 2003, o Office 2007, o Office 2010 e o Office 2016.

## <a name="what-about-visio-pro-or-project-pro"></a>E quanto do Visio Pro ou projeto Pro?

A Office 365 ProPlus imagem incluída em sua assinatura RemoteApp inclui o Visio Pro e Project Pro. Mas você não pode usar sua assinatura do Office 365 ProPlus para ativar esses programas - cada tiverem licença própria. Você pode ativá-los no [portal do Office 365](https://portal.office365.com/). 

Você não precisa esses programas de licença se não desejar usá-los. Basta ative os programas que você deseja usar - e ignorar os outros. Você ainda verá-los na imagem, mas você não pode usá-los. 

## <a name="how-do-i-get-started-with-office-365-and-azure-remoteapp"></a>Como faço para começar a com o Office 365 e Azure RemoteApp?

Agora que você conhece os detalhes do licenciamento do Office 365, vamos começar a usá-lo no Azure RemoteApp - é muito fácil:

Quando você cria sua coleção de Azure RemoteApp, use a imagem **Office 365 ProPlus (assinatura obrigatória)** .

![Imagem de RemoteApp Azure com o Office 365 Pro Plus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Esta imagem contém a versão mais recente do Windows Server e o Office 365 ProPlus. Depois de configurar seu conjunto (incluindo publicação apps), os usuários simplesmente login Azure RemoteApp (usando seu cliente RemoteApp) e fornecem suas credenciais do Office 365 para todos os aplicativos do Office. Licenças serão entregues automaticamente sem qualquer conjunto para cima ou gerenciamento necessários.

## <a name="can-i-create-a-custom-image-with-office-365-proplus"></a>Pode criar uma imagem personalizada com o Office 365 ProPlus?

Você pode criar uma imagem personalizada para o conjunto que contém o Office 365 ProPlus. Há 2 opções - usar a imagem da Galeria Azure que fornecemos ou você pode criar sua própria imagem personalizada e instalar o Office 365 ProPlus lá.

### <a name="use-the-azure-gallery-image"></a>Usar a imagem da Galeria Azure

A maneira mais fácil para implantar o Office 365 ProPlus para um conjunto é [começar com uma das imagens Galeria Azure](remoteapp-image-on-azurevm.md) incluído em sua assinatura do Azure RemoteApp. Verifique se que você escolher a imagem do **Windows Server Desktop Host da sessão remota com o Office 365 ProPlus pré-instalado** . Em seguida, instale quaisquer outros aplicativos que você deseja na imagem e estiver pronto.

### <a name="use-a-custom-image"></a>Use uma imagem personalizada

Você sempre pode criar uma imagem personalizada, você pode criar uma [Máquina virtual do Azure](remoteapp-image-on-azurevm.md) ou [Crie a imagem localmente](remoteapp-create-custom-image.md) e carregue-o no Azure. Nos dois casos, certifique-se de que instalar o Office 365 ProPlus usando o nó de ativação do computador compartilhado. Use a [Ferramenta de implantação do Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) e siga as [instruções](https://technet.microsoft.com/library/Dn782858.aspx) de instalação.  

### <a name="disable-automatic-updates-for-office-365-proplus-in-your-custom-image---important"></a>Desativar as atualizações automáticas para o Office 365 ProPlus em sua imagem personalizada - importante

Sua imagem personalizada é usada pelo RemoteApp do Azure como um modelo para adicionar recursos adicionais como a demanda de seus usuários aumenta. Para evitar atrasos e problemas de conexão, desabilite as atualizações automáticas do Office na imagem. Se você não fizer isso, cada recurso criado com esse modelo será atualizado automaticamente quando ele é iniciado. Em vez disso, use o processo de RemoteApp Azure padrão para atualizar sua imagem personalizada. Dessa maneira você atualizar os aplicativos do Office uma vez na imagem do modelo e deixe Azure RemoteApp cuidam de obter as atualizações para os usuários.

Para desativar as atualizações automáticas, adicione o seguinte para o arquivo de configuração da ferramenta de implantação do Office:

        <Updates Enabled="FALSE" />

Agora o seu arquivo de configuração deve conter essas linhas:
    
        <Display Level="NONE" AcceptEULA="TRUE" />
        <Property Name="SharedComputerLicensing" Value="1" />
        <Updates Enabled="FALSE" />

## <a name="so-how-can-i-update-an-image-with-office-365-proplus"></a>Como é possível atualizar uma imagem com o Office 365 ProPlus?

Há vários motivos para atualizar a imagem em seu conjunto. Aqui estão apenas alguns:

- Obter as atualizações mais recentes do Windows 
- Obtenha as últimas atualizações do aplicativo Office 365 ProPlus
- Atualizar seu aplicativo personalizado
- Alterar outras configurações para a própria imagem

Para ver as etapas de ponta a ponta para atualizar seu conjunto para usar a imagem que você atualizou, vá [aqui](remoteapp-update.md). Mas, para obter informações sobre como atualizar a imagem e o Office 365 ProPlus, confira as informações a seguir.

Você tem duas opções para atualizar sua imagem - substitua sua imagem com uma completamente nova ou manualmente atualizar sua imagem existente.

### <a name="replace-your-image-with-the-latest-azure-gallery-image--add-customizations"></a>Substitua sua imagem com a imagem mais recente do Azure Galeria + adicionar personalizações
Com essa opção, você deixar Microsoft cuidam das atualizações do Windows Server e o Office 365 ProPlus. Em vez de atualizar a imagem existente, você vai criar uma imagem completamente nova com base na imagem da Galeria mais recente. Em seguida, repita as etapas que você realizou antes de personalizar a imagem-instalar aplicativos personalizados, modificar a configuração de imagem, etc.

As imagens de galeria são atualizadas regularmente, para que você possa ter fácil, sabendo que seus aplicativos do Windows Server e o Office 365 ProPlus estão atualizados. Claro, a desvantagem é que você terá de aplicar suas personalizações sempre que receber uma nova imagem. Você pode criar scripts para automatizar a configuração de suas personalizações.

### <a name="manually-update-your-existing-image"></a>Atualizar manualmente sua imagem existente

Com essa opção, você pode usar ferramentas padrão do Windows para aplicar as atualizações para a imagem. Para o Office 365 ProPlus, use a ferramenta de implantação do Office para baixar e instalar as últimas atualizações ou versões do Office 365 ProPlus.

> [AZURE.IMPORTANT] Lembre-se: desabilitar as Office 365 ProPlus atualizações automáticas.

Precisa de mais informações sobre como usar a ferramenta de implantação do Office para atualizações?

- [Implante o clique para executar para produtos do Office 365 usando a ferramenta de implantação do Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Implantando e atualizando o Office 365 ProPlus usando a ferramenta de implantação do Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vídeo)
- [Definir configurações de atualização para o Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)
