<properties
    pageTitle="Criar uma VM Linux usando o Portal do Azure | Microsoft Azure"
    description="Crie uma VM Linux usando o Portal do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Criar uma VM Linux no Azure usando o Portal


Este artigo mostra como usar o [portal do Azure](https://portal.azure.com/) para criar uma máquina Virtual Linux.

Os requisitos são:

- [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Arquivos de chave públicos e privados SSH](virtual-machines-linux-mac-create-ssh-keys.md)


1. Conectado ao portal do Azure com sua identidade de conta do Azure, clique em **+ nova** no canto superior esquerdo:

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Clique em **máquinas virtuais** no **Marketplace** e em seguida lista de imagens do **Ubuntu servidor 14.04 LTS** de **Aplicativos em destaque** .  Verifique se na parte inferior do modelo de implantação é `Resource Manager` e, em seguida, clique em **criar**.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Na página **Noções básicas** , insira:
    - um nome para a máquina virtual
    - um nome de usuário para o usuário Admin
    - o tipo de autenticação definido como **chave pública SSH**
    - sua chave pública SSH como uma cadeia de caracteres (do seu `~/.ssh/` diretório)
    - um recurso de nome do grupo ou selecione um grupo existente

    e clique em **Okey** continuar e escolha o tamanho de máquina virtual; ele deve ser algo parecido com o seguinte:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Escolha o tamanho de **DS1** , que instala Ubuntu em uma SSD Premium e clique em **Selecionar** para definir as configurações.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. Em **configurações**, mantenha os padrões para os valores de rede e de armazenamento e clique em **Okey** para exibir o resumo.  Observe o tipo de disco foi definida como Premium SSD escolhendo DS1, o **S** notates SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Confirme as configurações para sua nova VM Ubuntu e clique em **Okey**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Abra o painel de Portal e em **interfaces de rede** escolha sua NIC

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Abrir o menu de endereços IP público nas configurações do NIC

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH para o IP público utilizando sua chave pública SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Próximas etapas

Agora você criou uma Linux VM rapidamente para usar para fins de testes ou demonstração. Para criar uma VM Linux personalizadas para sua infraestrutura, você pode seguir qualquer um dos seguintes artigos.

- [Criar uma VM Linux no Azure usando modelos](virtual-machines-linux-cli-deploy-templates.md)
- [Criar um SSH protegido Linux máquina virtual no Azure usando modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM Linux utilizando a CLI do Azure](virtual-machines-linux-create-cli-complete.md)
