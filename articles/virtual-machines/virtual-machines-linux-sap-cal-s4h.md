<properties 
pageTitle="Implantando HANA HANA S/4 ou BW/4 em uma máquina virtual Azure | Microsoft Azure" 
description="Implantando HANA S/4 ou HANA de p & b/4 em uma máquina virtual Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 


# <a name="deploying-s4-hana-or-bw4-hana-on-microsoft-azure"></a>Implantando HANA S/4 ou HANA de p & b/4 no Microsoft Azure 

Este artigo descreve como implantar HANA S/4 no Microsoft Azure via SAP nuvem aparelho Library 3.0.
As capturas de tela mostram o processo passo a passo. Implantando outras soluções baseadas em SAP HANA como HANA de p & b/4 funciona da mesma maneira de uma perspectiva de processo. Uma tem apenas selecionar uma solução diferente.

Começar com biblioteca de dispositivo de nuvem do SAP (SAP CAL) vá [aqui](https://cal.sap.com/). Não há um blog do SAP sobre o novo [SAP nuvem aparelho Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


As capturas de tela a seguintes mostram passo a passo como implantar HANA S/4 no Microsoft Azure. O processo funciona da mesma maneira para outras soluções likeBW/4 HANA.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

A primeira imagem mostra todas as soluções baseado em SAP CAL HANA que estão disponíveis no Microsoft Azure.
Exemplarily o "SAP S/4 HANA local edition" (solução na parte inferior da captura de tela) foi escolhida para percorrer o processo.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Primeiro, uma nova conta do SAP CAL tem a ser criado. Há duas opções para Azure - padrão Azure e Azure no continente China operado por 21Vianet de parceiro.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Em seguida, é preciso insira a ID do Azure assinatura que pode ser encontrada no portal do Azure - Consulte também ainda mais para baixo como obtê-lo. Posteriormente, um certificado de gerenciamento Azure precisa ser baixado.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

O novo Azure um portal localiza o item "Assinaturas" no lado esquerdo. Clique em para mostrar todas as assinaturas ativas para o usuário.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Selecionando uma das assinaturas e escolhendo "Certificados de gerenciamento" explica que lá são um novo conceito usando "entidades de serviço" para o novo modelo do Gerenciador de recursos do Azure.
SAP CAL não adaptado ainda para este novo modelo e ainda requer o modelo "clássico" e o portal do Azure primeiro para trabalhar com certificados de gerenciamento.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Aqui um pode ver o portal do Azure primeiro. O upload de um certificado de gerenciamento fornece SAP CAL as permissões para criar máquinas virtuais dentro de uma assinatura de cliente. Em "Assinaturas" guia uma pode encontrar a identificação de assinatura que tem a ser inserido no portal do SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Na guia segunda, em seguida, é possível carregar o certificado de gerenciamento baixada antes de CAL do SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Uma pequena caixa de diálogo pop-up para selecionar o arquivo de certificado baixado.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Depois que o certificado foi carregado a conexão entre CAL do SAP e a Azure assinatura do cliente pode ser testado dentro CAl do SAP. Uma pequena mensagem deve pop-up que informa que a conexão é válido.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Após a configuração de uma conta, é preciso selecionar uma solução que deve ser implantada e cria uma ocorrência.
Com o modo "básico", é realmente simples. Digite um nome de ocorrência, escolha uma região Azure e definir a senha mestre para a solução.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Após alguns minutos, dependendo do tamanho e complexidade da solução (uma estimativa é fornecida pela SAP CAL), ele é mostrado como "ativo" e pronto para uso. É muito simple.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Examinando alguns detalhes da solução um pode ver qual tipo de VMs foram implantados. Nesse caso, três VMs Azure de finalidade e tamanhos diferentes foram criadas.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

No portal do Azure, as máquinas virtuais podem ser encontradas começando com o mesmo nome da instância que foi fornecido no CAL do SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Agora é possível conectar-se à solução através do botão conectar no portal do SAP CAL. A caixa de diálogo pouco contém um link para o guia do usuário que descreve todas as credenciais padrão para funcionar com a solução.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Outra opção é fazer o login no cliente do máquina virtual do Windows e comece, por exemplo, a interface de usuário do SAP pré-configurado.







