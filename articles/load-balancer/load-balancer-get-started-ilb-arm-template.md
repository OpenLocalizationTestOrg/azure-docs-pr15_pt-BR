<properties
   pageTitle="Criar um balanceador de carga interno usando um modelo no Gerenciador de recursos | Microsoft Azure"
   description="Aprenda a criar um balanceador de carga interno usando um modelo no Gerenciador de recursos"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-a-template"></a>Criar um balanceador de carga interno usando um modelo

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implante o modelo usando o clique para implantar

O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém o padrão de valores usadas para gerar o cenário descrito acima. Para implantar este modelo usando o clique para distribuir, siga [Este link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), clique em **implantar no Azure**, substitua os valores de parâmetro padrão, se necessário e siga as instruções no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implante o modelo usando o PowerShell

Para implantar o modelo que você baixou usando o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o PowerShell do Azure, veja [como instalar e configurar o Azure PowerShell](../../articles/powershell-install-configure.md) e siga as instruções totalmente ao final para entrar no Azure e selecione sua assinatura.
2. Baixe o arquivo de parâmetros no disco local.
3. Edite o arquivo e salvá-lo.
4. Execute o cmdlet **New-AzureRmResourceGroupDeployment** para criar um grupo de recursos usando o modelo.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implantar o modelo usando a CLI do Azure

Para implantar o modelo usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou CLI do Azure, consulte [instalar e configurar o CLI do Azure](../../articles/xplat-cli-install.md) e siga as instruções para cima até o ponto onde você selecionar sua conta do Azure e assinatura.
2. Execute o comando de **modo config azure** para alternar para modo de Gerenciador de recursos, conforme mostrado abaixo.

        azure config mode arm

    Aqui está a saída esperada para o comando acima:

        info:    New mode is arm

3. Abra o arquivo de parâmetro, selecione seu conteúdo e salvá-lo em um arquivo em seu computador. Neste exemplo, podemos salvou o arquivo de parâmetros *parameters.json*.

4. Execute o comando **criar de implantação do azure grupo** para implantar o novo balanceador de carga interno usando os arquivos de modelo e um parâmetro que você baixou e modificado acima. A lista mostrada após a saída explica os parâmetros usados.

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição de Balanceador de carga usando afinidade IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite TCP ociosas para seu balanceador de carga](load-balancer-tcp-idle-timeout.md)



