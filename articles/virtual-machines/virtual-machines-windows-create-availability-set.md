<properties
    pageTitle="Criar um conjunto de disponibilidade de máquina virtual | Microsoft Azure"
    description="Saiba como criar uma disponibilidade definida para suas máquinas virtuais usando o portal Azure ou PowerShell usando o modelo de implantação do Gerenciador de recursos."
    keywords="conjunto de disponibilidade"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade 

Quando usando o portal, se quiser que sua máquina virtual para fazer parte de uma disponibilidade definida, você precisa criar a disponibilidade definida primeiro.

Para obter mais informações sobre como criar e usar conjuntos de disponibilidade, consulte [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Usar o portal para criar uma disponibilidade definir antes de criar suas VMs

1. No menu hub, clique em **Procurar** e selecione **conjuntos de disponibilidade**.

2. Sobre a **disponibilidade define blade**, clique em **Adicionar**.

    ![Captura de tela que mostra o botão Adicionar para criar uma nova disponibilidade definida.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Na lâmina **criar disponibilidade definida** , preencha as informações para o conjunto.

    ![Captura de tela que mostra as informações que você precise inserir para criar o conjunto de disponibilidade.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nome** - o nome deve ser 1-80 caracteres constituídas em números, letras, períodos, sublinhados e traços. O primeiro caractere deve ser uma letra ou número. O último caractere deve ser uma letra, número ou sublinhado.
    - **Domínios de falha** - domínios de falha definem o grupo de máquinas virtuais que compartilham uma opção de origem e de rede de energia comuns. Por padrão, as VMs são separadas por até três domínios de falha e podem ser alteradas para entre 1 e 3.
    - **Atualizar domínios** - atualização cinco domínios são atribuídos por padrão e isso pode ser definidos entre 1 e 20. Atualizar domínios indicam grupos de máquinas virtuais e hardware físico subjacente que pode ser reiniciado ao mesmo tempo. Por exemplo, se podemos especificar cinco atualizar domínios, quando mais de cinco máquinas virtuais são configuradas dentro de um único conjunto de disponibilidade, a máquina virtual sexta será colocado em mesmo domínio atualização da máquina virtual primeiro, sétimo na mesma UD como a segunda máquina virtual e assim por diante. A ordem da reinicialização poderá não ser sequencial, mas somente uma atualização de domínio será ser reiniciado uma vez.
    - **Assinatura** - selecione a assinatura usar se você tiver mais de um.
    - **Grupo de recursos** – selecione um grupo de recursos existente clicando na seta e selecionando um grupo de recursos na lista suspensa para baixo. Você também pode criar um novo grupo de recursos, digitando um nome. O nome pode conter qualquer um dos seguintes caracteres: letras, números, pontos, traços, sublinhados e abertura ou parêntese de fechamento. O nome não pode terminar em um período. Todas as VMs no grupo disponibilidade precisam ser criados no mesmo grupo de recursos como o conjunto de disponibilidade.
    - **Local** - selecione um local na lista suspensa.

4. Quando você terminar inserir as informações, clique em **criar**. Depois que o grupo de disponibilidade tiver sido criado, você pode vê-lo na lista depois de atualizar o portal.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Usar o portal para criar uma máquina virtual e uma disponibilidade definir ao mesmo tempo

Se você estiver criando uma nova máquina virtual usando o portal, você também pode criar uma nova disponibilidade definido para a máquina virtual ao criar a máquina virtual primeira no conjunto.

![Captura de tela que mostra o processo para criar uma nova disponibilidade definir enquanto você cria a máquina virtual.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Adicionar uma nova VM para um conjunto existente de disponibilidade

Para cada máquina virtual adicional que você criar que deve pertencer no conjunto, certifique-se de que você criá-lo no mesmo **grupo de recursos** e, em seguida, selecione a disponibilidade existente definida na etapa 3. 

![Captura de tela mostrando como selecionar uma disponibilidade existente definida para usar para sua máquina virtual.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Usar o PowerShell para criar um conjunto de disponibilidade

Este exemplo cria uma disponibilidade definir no grupo de recursos **RMResGroup** no local **Oeste EUA** . Isso deve ser feito antes de criar a primeira máquina virtual que será no conjunto.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Para obter mais informações, consulte [AzureRmAvailabilitySet de novo](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Solução de problemas

- Quando você cria uma máquina virtual, se o conjunto de disponibilidade desejado não estiver na lista suspensa no portal do que você pode ter sido criado em um grupo de recurso diferente. Se você não souber o grupo de recursos para sua disponibilidade definida, vá para o menu de hub e clique em Procurar > disponibilidade define para ver uma lista dos seus conjuntos de disponibilidade e quais grupos de recursos pertencem a.


## <a name="next-steps"></a>Próximas etapas

Adicione armazenamento adicional para sua máquina virtual adicionando um adicional [disco de dados](virtual-machines-windows-attach-disk-portal.md).
