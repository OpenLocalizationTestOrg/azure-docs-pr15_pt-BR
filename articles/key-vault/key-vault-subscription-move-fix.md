<properties
    pageTitle="Alterar a identificação de locatário do cofre chave após mover uma assinatura | Microsoft Azure"
    description="Saiba como alternar a identificação de locatário para um cofre chave após uma assinatura é movida para um locatário diferentes"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Alterar um ID de locatário do cofre chave depois de mover uma assinatura
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>P: minha assinatura foi movida de um locatário para locatário B. Como alterar a identificação de locatário para minha cofre chave existente e definir ACLs corretas para objetos no locatário B?

Quando você cria um novo cofre chave em uma assinatura, ele está vinculado automaticamente para a identificação de locatário do Active Directory do Azure padrão para essa assinatura. Todas as entradas de política de acesso também estão vinculadas a essa identificação de locatário Quando você move sua assinatura do Azure de locatário r para locatário B, seu compartimentos de chave existentes são inacessíveis por entidades (usuários e aplicativos) no locatário B. Para corrigir esse problema, você precisa:

- Alterar a ID de locatário associado todos existentes chaves compartimentos nesta inscrição para locatário B.
- Remova todas as entradas de política de acesso existentes.
- Adicionar novas entradas de política de acesso que estão associadas a locatário B.

Por exemplo, se você tiver cofre chave 'myvault' em uma assinatura que foi movida de locatário A para B, aqui do locatário como alterar a identificação de locatário do cofre chave e remover as políticas de acesso antigo.

<pre>
$vaultResourceId = (get-AzureRmKeyVault - VaultName myvault). Identificação de recurso $vault = Get-AzureRmResource – ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() Set-AzureRmResource - ResourceId $vaultResourceId-propriedades $vault. Propriedades
</pre>

Porque este cofre foi no locatário r antes, o valor original do **$vault. Properties.TenantId** é A de locatário, enquanto **(Get-AzureRmContext). Tenant.TenantId** é locatário B.

Agora que seu cofre está associado com a ID de locatário correto e antigo entradas de política de acesso são removidas, defina o acesso novato entradas de política com [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Próximas etapas

Se você tiver dúvidas sobre Cofre de chave do Azure, visite os [Fóruns do Azure chave cofre](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
