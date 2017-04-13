<properties
   pageTitle="Como manter um endereço IP virtual constante para um serviço de nuvem | Microsoft Azure"
   description="Saiba como garantir que o endereço IP virtual (VIP) do seu serviço de nuvem Azure não muda."
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

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Como manter um endereço IP virtual constante para um serviço de nuvem

Quando você atualiza um serviço de nuvem que está hospedado no Azure, talvez seja necessário garantir que o endereço IP virtual (VIP) do serviço não muda. Muitos serviços de gerenciamento de domínio usam o sistema de nome de domínio (DNS) para registrar nomes de domínio. DNS só funciona se o VIP permanece o mesmo. Você pode usar o **Assistente de publicação** em ferramentas do Azure para garantir que o VIP do seu serviço de nuvem não é alterado quando você atualizá-lo. Para obter mais informações sobre como usar o gerenciamento de domínios DNS para serviços de nuvem, consulte [Configurar um nome de domínio personalizado para um serviço de nuvem Azure](./cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicar um serviço de nuvem sem alterar seu VIP

O VIP de um serviço de nuvem é alocado quando você primeiro implantá-lo no Azure em um ambiente específico, como o ambiente de produção. VIP não altera a menos que você excluir a implantação explicitamente ou que seja excluída implicitamente pelo processo de atualização de implantação. Para manter o VIP, você não deve excluir sua implantação e você também deve garantir que o Visual Studio não exclui a sua implantação automaticamente. Você pode controlar o comportamento especificando configurações de implantação no **Assistente de publicação**, que oferece suporte a várias opções de implantação. Você pode especificar uma implantação clara ou uma implantação de atualização, que pode ser incremental ou simultâneo, e os dois tipos de implantações de atualização reter VIP. Para obter definições desses tipos diferentes de implantação, consulte [Publicar Assistente de aplicativo do Azure](vs-azure-tools-publish-azure-application-wizard.md).  Além disso, você pode controlar se a implantação anterior de um serviço de nuvem é excluída se ocorrer um erro. VIP pode ser alterada inesperadamente se você não definir essa opção corretamente.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Para atualizar um serviço de nuvem sem alterar seu VIP

1. Após implantar seu serviço de nuvem pelo menos uma vez, abra o menu de atalho para o nó do seu projeto Azure e escolha **Publicar**. O Assistente de **Aplicativo do Azure publicar** aparece.

1. Na lista de assinaturas, escolha aquele ao qual você deseja implantar e escolha o botão **Avançar** . A página de **configurações** do assistente aparece.

1. Na guia **Configurações comuns** , verifique se o nome do serviço de nuvem à qual você está implantando, o **ambiente**, a **Configuração de compilação**e a **Configuração do serviço** são corretos.

1. Na guia **Configurações avançadas** , verifique se a conta de armazenamento e o rótulo de implantação estão corretos, que a caixa de seleção **Excluir a implantação em caso de falha** está desmarcada e que a caixa de seleção de atualização de **implantação** está selecionada. Marcando a caixa de seleção de atualização de **implantação** , você garante que sua implantação não será excluída e seu VIP não seja perdida quando você republicar seu aplicativo. Desmarcando **Excluir a implantação na caixa de seleção Falha**, você garantir que seu VIP não seja perdida se ocorrer um erro durante a implantação.

1. Para obter mais especificar como você deseja que as funções sejam atualizadas, escolha o link **configurações** ao lado da caixa de **atualização de implantação** e escolha a opção de atualização simultâneas ou atualização incremental na caixa de diálogo **implantação atualizar** configurações. Se você escolher atualização incremental, cada instância é atualizada após o outro, para que o aplicativo fique sempre disponível. Se você escolher atualização simultâneas, todas as instâncias são atualizadas ao mesmo tempo. Atualizando simultâneo é mais rápido, mas seu serviço pode não estar disponível durante o processo de atualização.

1. Quando estiver satisfeito com as configurações, escolha o botão **próximo** .

1. Na página **Resumo** do assistente, verifique suas configurações e, em seguida, escolha o botão **Publicar** .

  >[AZURE.WARNING] Se a implantação falhar, você deve por falha de endereço e reimplantar imediatamente, para evitar deixar seu serviço de nuvem em um estado corrompido.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a publicação no Azure do Visual Studio, consulte o [Assistente de aplicativo do Azure publicar](vs-azure-tools-publish-azure-application-wizard.md).
