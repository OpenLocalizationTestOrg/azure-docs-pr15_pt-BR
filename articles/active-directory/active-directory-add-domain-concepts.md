<properties
    pageTitle="Visão geral conceitual dos nomes de domínio personalizado no Active Directory do Azure | Microsoft Azure"
    description="Explica a estrutura conceitual para usar nomes de domínio personalizado do Azure Active directory, incluindo federação de single sign-on"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="conceptual-overview-of-custom-domain-names-in-azure-active-directory"></a>Visão geral conceitual dos nomes de domínio personalizado no Active Directory do Azure

Um nome de domínio é uma parte importante do identificador para muitos recursos de diretório: ela faz parte de um usuário nome ou endereço de email para um usuário, parte do endereço para um grupo e pode ser parte do aplicativo URI de identificação para um aplicativo. Um recurso do Azure Active Directory (AD Azure) pode incluir um nome de domínio que já está verificado serem pertencentes a pasta que contém o recurso. Somente um administrador global pode executar tarefas de gerenciamento de domínio no Azure AD.

Nomes de domínio no Azure AD são globalmente exclusivos. Um nome de domínio pode ser usado por um único Azure AD. Se um diretório de Azure AD verificou um nome de domínio, nenhum outro diretório do Azure AD pode verificar ou use esse mesmo nome de domínio.

## <a name="initial-and-custom-domain-names"></a>Nomes de domínio inicial e personalizados

Cada nome de domínio no Azure AD é um nome de domínio inicial ou um nome de domínio personalizado.

Cada Azure AD vem com um nome de domínio inicial na contoso.onmicrosoft.com formulário. Esse nome de domínio de nível terceiro, neste exemplo, "contoso.onmicrosoft.com," foi estabelecida quando o diretório foi criado, geralmente o administrador que criou o diretório. O nome de domínio inicial de um diretório não pode ser alterado ou excluído. O nome de domínio inicial, enquanto totalmente funcional, foi projetado principalmente para ser usado como um mecanismo de inicialização até um nome de domínio personalizado será verificado.

Na maioria dos ambientes de produção, um diretório tem pelo menos um domínio personalizado verificado, como "contoso.com", e é esse domínio personalizado que é visível para os usuários finais. Um nome de domínio personalizado é um nome de domínio pertencente e usado por essa organização, como "contoso.com", for usa como o seu site de hospedagem. Esse nome de domínio é familiar para funcionários porque ela é parte do nome do usuário que usar para entrar rede corporativa, ou para enviar e recuperar emails.

Antes que ele pode ser usado por Azure AD, o nome de domínio personalizado deve ser adicionado ao seu diretório e verificado.

## <a name="verified-and-unverified-domain-names"></a>Nomes de domínio verificado e não verificados

O nome de domínio inicial de um diretório implicitamente é avaliado conforme verificado pelo Azure AD. Quando um administrador adiciona um nome de domínio personalizado para um anúncio Azure, é inicialmente em um estado não verificado. Azure AD não permitirá quaisquer recursos de diretório para usar um nome de domínio não verificados. Isso garante que apenas uma pasta pode usar um nome de domínio específico e que a organização usa o nome de domínio realmente possui esse nome de domínio.

Azure AD verifica a propriedade de um nome de domínio procurando por uma entrada específica no arquivo de zona DNS (serviço) de nome do domínio para o nome de domínio. Para verificar a propriedade de um nome de domínio, o administrador obtém a entrada DNS do Azure AD que Azure AD procurará e adiciona essa entrada para o arquivo de zona DNS para o nome de domínio. Arquivo de zona DNS é mantido por no registrador de nomes de domínio desse domínio. As etapas para verificar um domínio são mostradas no artigo para [Adicionar um domínio personalizado para seu diretório Azure AD](active-directory-add-domain.md).

Adicionar uma entrada DNS para o arquivo de zona para o nome de domínio não afeta outros serviços de domínio como email ou hospedagem na web.

## <a name="federated-and-managed-domain-names"></a>Nomes de domínio federado e gerenciado

Um nome de domínio personalizado no Azure AD pode ser configurado para dar aos usuários um sinal federado na experiência entre o Active Directory local e o Azure AD. Configurando um domínio para a federação requer atualizações para os recursos privilegiados no Azure AD e também para o Active Directory do Windows Server. Configurando que um domínio federado deve ser concluído do Azure AD Connect ou usando o PowerShell. Federar um domínio personalizado não puder ser iniciada a partir do portal clássico Azure. [Assista a este vídeo para aprender sobre a configuração do AD FS para o usuário entrar com Azure AD Connect](http://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect).

Domínios que não esteja federados são chamados domínios gerenciados. O domínio inicial para um diretório do Azure AD implicitamente é avaliado como um domínio gerenciado.

## <a name="primary-domain-names"></a>Nomes de domínio primário

O nome de domínio primário para um diretório é o nome de domínio previamente selecionado como o valor padrão para a parte 'domínio' de nome de usuário, quando um administrador cria um novo usuário do [Azure portal clássico](https://manage.windowsazure.com/) ou outro portal como o portal de administração do Office 365. Um diretório pode ter apenas um nome de domínio primário. Um administrador pode alterar o nome de domínio primário seja qualquer verificado domínio personalizado não federado, ou para o domínio inicial.

## <a name="domain-names-in-azure-ad-and-other-microsoft-online-services"></a>Nomes de domínio no Azure AD e outros serviços Online da Microsoft

Um nome de domínio deve ser verificado no Azure AD antes que ele pode ser usado por outro serviço Online da Microsoft como o Exchange Online, SharePoint Online e Intune. Esses outros serviços normalmente requerem um administrador para adicionar uma ou mais entradas DNS específicas para o serviço.

Aplicativo web Azure usa seu próprio mecanismo para confirmar a posse de um domínio. Um domínio deve ser verificado para uso com o Azure AD, mesmo se ele foi anteriormente verificado para uso pelo aplicativo web Azure em uma assinatura que depende desse Azure AD. Aplicativo web Azure pode usar um nome de domínio que foi confirmado em um diretório diferente do diretório que protege o aplicativo web.

## <a name="managing-domain-names"></a>Gerenciar nomes de domínio

Tarefas de gerenciamento de domínio podem ser concluídas de portal clássico do Azure e do PowerShell. Muitas tarefas podem ser concluídas usando a API do Azure AD gráfico (no modo de visualização público).

-   [Adicionando e verificando um nome de domínio personalizado](active-directory-add-domain.md)

-   [Gerenciamento de domínios no portal de clássico do Azure](active-directory-add-manage-domain-names.md)

-   [Usando o PowerShell para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Usar a API do Azure AD Graph para gerenciar nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)
