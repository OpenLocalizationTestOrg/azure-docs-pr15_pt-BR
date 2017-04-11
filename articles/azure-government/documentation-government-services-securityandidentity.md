<properties
    pageTitle="Documentação do governo Azure | Microsoft Azure"
    description="Isso fornece uma comparação de recursos e orientações sobre como desenvolver aplicativos para o governo do Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Identidade e segurança do governo azure

##  <a name="key-vault"></a>Chave cofre
Para obter detalhes sobre esse serviço e como usá-lo, consulte o <a href="https://azure.microsoft.com/documentation/services/key-vault">documentação pública do Azure chave cofre.</a>

### <a name="data-considerations"></a>Considerações de dados
As informações a seguir identificam o limite de governo do Azure para Azure Cofre de chave:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos os dados criptografados com uma chave de Cofre de chave do Azure podem conter dados regulamentadas/controlados. | Metadados de chave cofre Azure não é permitido para conter dados de exportação controlada. Esses metadados incluem todos os dados de configuração inseridos quando criar e manter seu Cofre de chave.  Não digite dados regulamentadas/controlados nos seguintes campos: nomes de grupo de recursos, nomes de chave cofre, nome da assinatura |

Chave cofre estará disponível no governo do Azure. Como público, há sem extensão, chave cofre só está disponível através do PowerShell e CLI.

## <a name="next-steps"></a>Próximas etapas

Para informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
