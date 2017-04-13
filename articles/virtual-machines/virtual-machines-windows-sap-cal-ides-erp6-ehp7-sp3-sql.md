<properties 
pageTitle="Implantando o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure | Microsoft Azure" 
description="Implantando o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Implantando o SAP IDES EHP7 SP3 para SAP ERP 6.0 no Microsoft Azure 

Este artigo descreve como implantar IDES SAP em execução com SQL Server e o sistema operacional Windows no Microsoft Azure via SAP nuvem aparelho Library 3.0. As capturas de tela mostram o processo passo a passo. Implantar outras soluções na lista funciona da mesma maneira de uma perspectiva de processo. Uma tem apenas selecionar uma solução diferente.

Começar com biblioteca de dispositivo de nuvem do SAP (SAP CAL) vá [aqui](https://cal.sap.com/). Não há um blog do SAP sobre o novo [SAP nuvem aparelho Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


As capturas de tela a seguintes mostram passo a passo como implantar IDES do SAP no Microsoft Azure. O processo funciona da mesma maneira para obter outras soluções.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

A primeira imagem mostra todas as soluções que estão disponíveis no Microsoft Azure. Realçada solução baseada em Windows SAP IDES que só está disponível no Azure foi escolhida para percorrer o processo.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Primeiro, uma nova conta do SAP CAL tem a ser criado. Há duas opções para Azure - padrão Azure e Azure no continente China operado por 21Vianet de parceiro.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Em seguida, é preciso insira a ID do Azure assinatura que pode ser encontrada no portal do Azure - Consulte também ainda mais para baixo como obtê-lo. Posteriormente, um certificado de gerenciamento Azure precisa ser baixado.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

O novo Azure um portal localiza o item "Assinaturas" no lado esquerdo. Clique em para mostrar todas as assinaturas ativas para o usuário.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Selecionando uma das assinaturas e escolhendo "Certificados de gerenciamento" explica que lá são um novo conceito usando "entidades de serviço" para o novo modelo do Gerenciador de recursos do Azure.
SAP CAL não adaptado ainda para este novo modelo e ainda requer o modelo "clássico" e o portal do Azure primeiro para trabalhar com certificados de gerenciamento.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Aqui um pode ver o portal do Azure primeiro. O upload de um certificado de gerenciamento fornece SAP CAL as permissões para criar máquinas virtuais dentro de uma assinatura de cliente. Em "Assinaturas" guia uma pode encontrar a identificação de assinatura que tem a ser inserido no portal do SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

Na guia segunda, em seguida, é possível carregar o certificado de gerenciamento baixada antes de CAL do SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Uma pequena caixa de diálogo pop-up para selecionar o arquivo de certificado baixado.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Depois que o certificado foi carregado a conexão entre CAL do SAP e a Azure assinatura do cliente pode ser testado dentro CAl do SAP. Uma pequena mensagem deve pop-up que informa que a conexão é válido.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Após a configuração de uma conta, é preciso selecionar uma solução que deve ser implantada e cria uma ocorrência.
Com o modo "básico", é realmente simples. Digite um nome de ocorrência, escolha uma região Azure e definir a senha mestre para a solução.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Após alguns minutos, dependendo do tamanho e complexidade da solução (uma estimativa é fornecida pela SAP CAL), ele é mostrado como "ativo" e pronto para uso. É muito simple.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

Examinando alguns detalhes da solução um pode ver qual tipo de VMs foram implantados. Nesse caso, há um único Azure máquina virtual do tamanho D12 que foi criado por CAL do SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

No portal do Azure, a máquina virtual podem ser encontrada começando com o mesmo nome da instância que foi fornecido no CAL do SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Agora é possível conectar-se à solução através do botão conectar no portal do SAP CAL. A caixa de diálogo pouco contém um link para o guia do usuário que descreve todas as credenciais padrão para funcionar com a solução.
[Aqui](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) está o link para o guia para a solução IDES.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Outra opção é fazer login para a máquina virtual Windows e comece, por exemplo, a interface de usuário do SAP pré-configurado.





