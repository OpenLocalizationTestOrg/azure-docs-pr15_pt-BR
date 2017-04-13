<properties
   pageTitle="Azure Máquina Virtual DotNet Core Tutorial 1 | Microsoft Azure"
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automatizar implantações de aplicativos para máquinas virtuais do Azure

Esta série de quatro partes detalha implantar e configurar o recurso Azure e usando o Azure recurso Gerenciar modelos de aplicativos. Nesta série, uma amostra de modelo é implantada e o modelo de implantação examinados. O objetivo desta série é instruir no relacionamento entre recursos Azure e fornecer mãos na experiência implantando modelos totalmente integrado do Gerenciador de recursos do Azure. Este documento presume um nível básico de conhecimento com o Gerenciador de recursos do Azure, antes de iniciar este tutorial familiarizar-se com os conceitos básicos do Gerenciador de recursos do Azure.

## <a name="music-store-application"></a>Aplicativo de armazenamento de música

O exemplo usado nesta série é um .net aplicativo Core simular uma loja de música experiência de compra. Este aplicativo pode ser implantado em um Linux ou Windows sistema virtual, implantações de amostra foram criadas para ambos. O aplicativo inclui um aplicativo da web e um banco de dados do SQL. Antes de ler os artigos desta série, implante o aplicativo usando o botão de implantação encontrado nesta página. Quando totalmente implantado, a arquitetura do aplicativo / Azure se parece com o diagrama a seguir. 

O modelo do Gerenciador de recursos de armazenamento de música pode ser encontrado aqui, [Modelo de armazenamento de música do Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Aplicativo do repositório de música](./media/virtual-machines-windows-dotnet-core/music-store.png)

Cada um desses componentes, incluindo o modelo associar JSON é examinada nos seguintes quatro artigos.

- [**Arquitetura do aplicativo**](./virtual-machines-windows-dotnet-core-2-architecture.md) – componentes de aplicativo, como sites da web e bancos de dados precisam ser hospedados em recursos do computador Azure como máquinas virtuais e bancos de dados do SQL Azure. Este documento percorre a necessidade de computação de mapeamento, recursos Azure e implantando esses recursos com um modelo do Gerenciador de recursos do Azure. 

- [**Acesso e segurança**](./virtual-machines-windows-dotnet-core-3-access-security.md) – quando hospedando aplicativos no Azure, é necessário considerar como o aplicativo é acessado e acesso de componentes de aplicativo como diferentes entre si. Este documento detalha fornecendo e proteger o acesso entre os componentes de aplicativo e acesso à internet para um aplicativo.

- [**Disponibilidade e escala**](./virtual-machines-windows-dotnet-core-4-availability-scale.md) – disponibilidade e escala se referem a capacidade de aplicativos para permanecer em execução durante o tempo de inatividade de infraestrutura e a capacidade de dimensionar recursos de computação para atender a demanda de aplicativo. Este documento detalhes os componentes necessários para implantar uma balanceamento de carga e o aplicativo altamente disponível.

- [**Implantação do aplicativo**](./virtual-machines-windows-dotnet-core-5-app-deployment.md) - ao implantar aplicativos em máquinas virtuais do Azure, o método pelo qual os binários do aplicativo são instalados na máquina Virtual deve ser considerado. Este documento detalha automatizar instalação de aplicativo usando extensões de Script do Azure Máquina Virtual personalizada.

O objetivo ao desenvolver modelos do Gerenciador de recursos do Azure é automatizar a implantação da infraestrutura do Azure e a instalação e configuração de quaisquer aplicativos que está sendo hospedado neste infraestrutura Azure. Seguindo estes artigos fornece um exemplo desta experiência.

## <a name="deploy-the-music-store-application"></a>Implantar o aplicativo de armazenamento de música

O aplicativo do repositório de música pode ser implantado com esse botão.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

O modelo do Gerenciador de recursos do Azure requer os seguintes valores de parâmetro.

|Nome do parâmetro |Descrição   |
|---|---|
|ADMINUSERNAME   | Nome de usuário de administrador que é usado na máquina virtual e o banco de dados do SQL Azure.  |
|ADMINPASSWORD | Senha que é usada na máquina Virtual de Azure e banco de dados SQL.  |
|NUMBEROFINSTANCES | O número de máquinas virtuais a ser criado. Cada uma dessas máquinas virtuais hospedar o aplicativo da web de repositório de música e todo o tráfego é carga equilibrada-los. |
|PUBLICIPADDRESSDNSNAME | Nome DNS exclusivo associado com o endereço IP público. |

Quando tiver concluído a implantação do modelo, navegue até o endereço IP público utilizando qualquer navegador de internet. O .net site Core música será apresentada.

## <a name="next-steps"></a>Próximas etapas

<hr>

[Etapa 1 - arquitetura de aplicativo com modelos de Gerenciador de recursos do Azure](./virtual-machines-windows-dotnet-core-2-architecture.md)

[Etapa 2 - segurança em modelos de Gerenciador de recursos do Azure e acesso](./virtual-machines-windows-dotnet-core-3-access-security.md)

[Etapa 3 - disponibilidade e escala no Azure modelos do Gerenciador de recursos](./virtual-machines-windows-dotnet-core-4-availability-scale.md)

[Etapa 4 - implantação do aplicativo com modelos de Gerenciador de recursos do Azure](./virtual-machines-windows-dotnet-core-5-app-deployment.md)


