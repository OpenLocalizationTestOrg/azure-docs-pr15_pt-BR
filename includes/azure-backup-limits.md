 (compartimentos de backup<properties
   título da página = "Azure Backup limits table"
   Descrição = "descreve os limites de sistema para Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


Os seguintes limites se aplicam ao Backup do Azure.

| Identificador de limite | Limite padrão |
|---|---|
|Número de servidores/máquinas que pode ser registrado em relação a cada compartimento|50 para Windows Client/Server/SCDPM <br/> 200 para VMs IaaS|
|Tamanho da fonte de dados para dados armazenados em armazenamento de cofre Azure|54400 GB máx<sup>1</sup>|
|Número de compartimentos de backup que podem ser criados em cada assinatura do Azure|25 (compartimentos de backup) <br/> Serviços de recuperação de 25 cofre por região|
|Número de vezes que o backup pode ser agendado por dia|3 por dia para o Windows Server/Client <br/> 2 por dia para SCDPM <br/> Uma vez por dia para VMs IaaS|
|Discos de dados conectados a uma máquina virtual Azure para backup|16|

- <sup>1</sup> O limite 54400 GB não se aplicam ao backup de VM IaaS.

