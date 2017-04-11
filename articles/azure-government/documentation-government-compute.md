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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Computação de governo Azure

##  <a name="virtual-machines"></a>Máquinas virtuais

Para obter detalhes sobre esse serviço e como usá-lo, consulte [Tamanhos de máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variações

As seguintes SKUs de máquina virtual são disponibilidade geral (GA) no Azure governo:

SKU DE MÁQUINA VIRTUAL|EUA Gov VA|EUA Gov IA|Anotações
---|---|---|---
R|GA|GA|Nenhum
Dv1|GA|-|Nenhum
DSv1|GA|-|Nenhum
Dv2|GA|GA|15 é em breve
F|GA|GA|Nenhum
G|Planejado|-|Nenhum

###  <a name="data-considerations"></a>Considerações de dados

As informações a seguir identificam o limite de governo do Azure para máquinas virtuais do Azure:

| Dados regulamentadas/controlados permitidos | Dados regulamentadas/controlados não permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dados inseridos, armazenados e processadas dentro de uma máquina virtual pode conter dados de exportação controlada. Binários dentro de máquinas virtuais do Azure em execução. Autenticadores estáticos, como senhas e PINs de cartão inteligente para obter acesso a componentes de plataforma Windows Azure. Chaves particulares de certificados usados para gerenciar os componentes de plataforma Windows Azure. Cadeias de caracteres de conexão de SQL.  Outros segurança informações/segredos, como certificados, chaves de criptografia, chaves mestre e chaves de armazenamento armazenadas nos serviços do Azure.  | Metadados não é permitido para conter dados de exportação controlada. Esses metadados incluem todos os dados de configuração inseridos durante a criação e a manutenção de máquina Virtual do Azure.  Não digite dados regulamentadas/controlados nos seguintes campos: nomes de funções, grupos de recursos, nomes de implantação, nomes de recursos, marcas de recurso de locatários  

## <a name="next-steps"></a>Próximas etapas

Para informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
