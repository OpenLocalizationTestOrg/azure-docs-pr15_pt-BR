<properties
    pageTitle="Reimplantar pilha Azure | Microsoft Azure"
    description="Reimplante pilha Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Reimplantar pilha Azure

Para reimplantar pilha do Azure, você deve começar do zero conforme descrito abaixo.

## <a name="steps-to-redeploy-azure-stack"></a>Etapas para reimplantar pilha do Azure

1. Reinicie o host para o sistema operacional original (instalado em hardware vazio). Isso não é a configuração padrão no menu de inicialização, portanto você deve usar KVM ou console local para selecioná-lo durante a reinicialização (durante a instalação, você denominada "Inicialização do VHD" SO para "AzureStack TP2", isso ajudará a identificar quais é o sistema operacional).

    Não é necessário remover a entrada de inicialização existente (o novo suporte script "PrepareBootFromVHD.ps1" cuida para você.)

2. Se você não tiver KVM ou gostaria de escolher o sistema operacional de inicialização antes de reinicializar:
    
    1. Localize o script.\BootMenuNoKVM.ps1. Este arquivo está disponível com os outros scripts de suporte fornecidos juntamente com esta compilação.
    
    2. Execute o script com privilégios elevados. Selecione o nome do sistema operacional de Host Original. Isso iniciará o host para o sistema operacional de host original sem exigir acesso KVM.
    
    3. Quando o script for concluído, você será solicitado a confirmar a reinicialização.

    - Se houver outros usuários conectados, esse comando falhará.

    - Execute o seguinte comando: reiniciar o computador-forçar 
 
3. Exclua o arquivo CloudBuilder.vhdx que foi usado como parte da implantação anterior.

    Você não precisa excluir o Pool de armazenamento existente da distribuição TP2 anterior. O script de implantação detecta e limpa o existente e cria novos.

5. Reimplante copiem uma nova cópia do CloudBuilder.vhdx, inicialização para ele, etc.

## <a name="next-steps"></a>Próximas etapas

[Conectar-se a pilha Azure](azure-stack-connect-azure-stack.md)
