<properties
   pageTitle="Configurando denominada credenciais de autenticação | Microsoft Azure"
   description="Saiba como para fornecer credenciais que o Visual Studio pode usar para autenticar solicitações ao Azure para publicar um aplicativo no Azure do Visual Studio ou para monitorar a um serviço de nuvem existente.. "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="setting-up-named-authentication-credentials"></a>Configurando credenciais de autenticação nomeado

Para publicar um aplicativo no Azure do Visual Studio ou para monitorar a um serviço de nuvem existente, você deve fornecer credenciais que Visual Studio pode usar para autenticar solicitações no Azure. Há vários lugares no Visual Studio, onde você pode entrar para fornecer essas credenciais. Por exemplo, do Gerenciador de servidores, você pode abrir o menu de atalho para o nó do **Azure** e escolha **conectar ao Azure**. Quando você entra, as informações de assinatura associadas a sua conta do Azure estão disponíveis no Visual Studio, e há mais que você precisa fazer nada.

Ferramentas Azure também dá suporte a uma forma mais antiga de fornecer credenciais, usando o assinatura (arquivo. publishsettings). Este tópico descreve este método, que ainda é suportado no Azure SDK 2.2.

Os itens a seguir são necessários para autenticação do Azure.

- Sua ID de assinatura

- Um certificado x. 509 v3

>[AZURE.NOTE] O comprimento da chave do x. 509 v3 certificado deve ser pelo menos 2048 bits. Azure irá rejeitar qualquer certificado que não atender a esse requisito ou que não é válido.

Visual Studio usa seu ID da assinatura junto com os dados de certificado como credenciais. As credenciais apropriadas são referenciadas no arquivo de assinatura (arquivo. publishsettings), que contém uma chave pública para o certificado. O arquivo de assinatura pode conter credenciais para mais de uma assinatura.

Você pode editar as informações de assinatura na caixa de diálogo **Novo/Editar assinatura** , conforme explicado mais adiante neste tópico.

Se você quiser criar um certificado por conta própria, você pode consultar as instruções em [criar e carregar um certificado de gerenciamento do Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e carregue manualmente o certificado [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Essas credenciais que requer o Visual Studio para gerenciar seus serviços de nuvem não são as mesmas credenciais que são necessários para autenticar uma solicitação em relação aos serviços de armazenamento do Azure.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modificar ou exportar as credenciais de autenticação no Visual Studio

Você também pode configurar, modificar ou exportar suas credenciais de autenticação na caixa de diálogo **Nova assinatura** , que aparece se você executar qualquer uma das seguintes ações:

- No **Server Explorer**, abrir o menu de atalho para o nó do **Azure** , escolha **Gerenciar assinaturas**, escolha a guia **certificados** e escolha o botão **novo** ou em **Editar** .

- Quando você publica um serviço de nuvem Azure do Assistente de **Aplicativo do Azure publicar** , escolha **Gerenciar** na lista **Escolher sua assinatura** , e em seguida, escolha a guia certificados e, em seguida, escolha o botão **novo** ou em **Editar** .

Este procedimento pressupõe que a caixa de diálogo **Nova assinatura** é aberta.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Configurar credenciais de autenticação no Visual Studio

1. Em **Selecione um certificado existente** para lista de autenticação, escolha um certificado.

1. Escolha o botão **copiar o caminho completo** . O caminho para o certificado (arquivo. cer) é copiado para a área de transferência.

    >[AZURE.IMPORTANT] Para publicar seu aplicativo do Azure do Visual Studio, você deve carregar este certificado [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Para carregar o certificado para o [portal de clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Escolha o link de Portal Azure.

         Abre o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885) .

    1. Entre [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, em seguida, escolha o botão de **Serviços de nuvem** .

    1. Escolha o serviço de nuvem que interessa a você.

        Abre a página para o serviço.

    1. Na guia **certificados** , escolha o botão **carregar** .

    1. Cole o caminho completo do arquivo. cer que você acabou de criar e, em seguida, insira a senha que você especificou.
