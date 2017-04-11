<properties
   pageTitle="Orientação de resiliência de serviço | Microsoft Azure"
   description="Links para recuperação e proativa orientação de resiliência e disponibilidade para os serviços do Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

# <a name="microsoft-azure-service-resiliency-guidance"></a>Orientação de resiliência de serviço do Microsoft Azure
Microsoft Azure foi projetado para fornecer os recursos que necessários, quando você precisa delas. Como parte de um bom design e práticas operacionais, você deve saber tanto como projetar o uso dos serviços do Azure para obter alta disponibilidade como o que fazer se o seu aplicativo é afetado por uma interrupção do serviço. Para ajudar você nesse processo, este documento contém links para orientação de recuperação de desastres, bem como a orientação de design de vários serviços Azure.

##<a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres
A orientação de recuperação de desastres links abaixo pode fornecer as informações necessárias para ajudá-lo a obter o aplicativo novamente on-line rapidamente se você é afetados por uma interrupção do serviço Azure. Esses links foram criados para ajudá-lo a responder à pergunta, "Eu estou sendo afetados por uma interrupção do serviço Azure, o que posso fazer?"

##<a name="design-guidance"></a>Orientação de design
Os links de orientação de design abaixo são design e orientação arquitetônica que foi criada para ajudá-lo a entender melhor como usar cada serviço Azure em um modo que maximize o tempo de atividade do seu aplicativo. Esses links foram criados para ajudá-lo a responder à pergunta "Como para garantir que um bug, falha de hardware, interrupção do serviço ou outra falha não impacto sobre a disponibilidade geral do meu aplicativo?" Se não houver nenhuma orientações específicas para o serviço que você está procurando no momento, o artigo de [alta disponibilidade em aplicativos criados no Microsoft Azure](./resiliency-high-availability-azure-applications.md) pode ter informações adicionais que podem ajudá-lo em seu design. 

##<a name="service-guidance"></a>Orientações de serviço
| Serviço  | Guia de recuperação de desastres | Orientação de design |
|:---------|:--------------------------:|:------------------:|
| [Serviços de nuvem] (https://azure.microsoft.com/services/cloud-services/ "Serviços de nuvem do Azure")       | [link] (../cloud-services/cloud-services-disaster-recovery-guidance.md "Guia de recuperação de desastres de serviços de nuvem do Azure")   | Não disponível |
| [Chave cofre] (https://azure.microsoft.com/services/key-vault/ "Azure cofre chave")                      | [link] (../key-vault/key-vault-disaster-recovery-guidance.md "Orientação de recuperação de desastres Cofre de chave do Azure")        | Não disponível |
| [Armazenamento] (https://azure.microsoft.com/services/storage/ "Armazenamento do Azure")                            | [link] (../storage/storage-disaster-recovery-guidance.md "Guia de recuperação de desastres de armazenamento do Azure")          | Não disponível |
| [Bancos de dados SQL] (https://azure.microsoft.com/services/sql-database/ "Bancos de dados do SQL Azure")           | [link] (../sql-database/sql-database-disaster-recovery.md  "Orientação de recuperação de desastres de banco de dados do SQL Azure")    | [link] (../sql-database/sql-database-business-continuity.md "Visão geral da continuidade de negócios com o banco de dados do Azure SQL") |
| [Máquinas virtuais] (https://azure.microsoft.com/services/virtual-machines/ "Azure máquinas virtuais") | [link] (../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Guia de recuperação de desastres máquinas virtuais do Azure") | Não disponível |
| [Rede virtual] (https://azure.microsoft.com/services/virtual-network/ "Rede Virtual Azure")    | [link] (../virtual-network/virtual-network-disaster-recovery-guidance.md "Guia de recuperação de desastres de rede Virtual do Azure")  | Não disponível |

##<a name="next-steps"></a>Próximas etapas
Se você estiver procurando orientações que focaliza amplamente sistemas e soluções, leia [recuperação de dados e alta disponibilidade em aplicativos criados no Microsoft Azure](https://aka.ms/drtechguide).
