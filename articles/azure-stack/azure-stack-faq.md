<properties
    pageTitle="Perguntas frequentes do Azure pilha | Microsoft Azure"
    description="Pilha Azure perguntas frequentes."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Perguntas frequentes do Azure pilha

## <a name="deployment"></a>Implantação

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>Eu preciso para formatar discos meus dados antes de iniciar ou reiniciar uma instalação?

Discos devem estar em formato bruto. Se você reinstalar o sistema operacional, será necessário verificar se o pool de armazenamento antigos ainda estiver presente e excluir usando as seguintes etapas:

1. Abra o Gerenciador de servidor.
2. Selecione Pools de armazenamento.
3. Ver se um pool de armazenamento está listado.
4. Clique com botão direito **pool de armazenamento** quando listado e habilitar leitura / gravação.
5. Clique com botão direito **do disco rígido Virtual** (canto inferior esquerdo) e selecione Excluir.
6. **Pool de armazenamento** de atalho e clique em Excluir.
7. Iniciar o script do Azure pilha novamente e verifique se que a verificação de disco passa.

Opcionalmente, o script a seguir pode ser usado:

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>Pode usar todos os discos SSD para o pool de armazenamento na instalação VDC?

Essa configuração não é suportada nesta versão.  Para obter mais informações, consulte o [guia de requisitos](azure-stack-deploy.md) para obter mais informações.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>Pode usar NVMe discos de dados para o VDC de pilha do Microsoft Azure?

Enquanto armazenamento espaços direto suporta discos de NVMe, o Azure pilha suporta somente um subconjunto dos tipos de unidade possíveis e combinações possíveis para armazenamento espaços direto. 

### <a name="how-can-i-reinstall-azure-stack"></a>Como reinstalar o Azure pilha?
Você pode seguir as etapas no [guia de reimplantação](azure-stack-redeploy.md).  

## <a name="tenant"></a>Locatário

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>Pode implantar meus próprios imagens como um locatário?

Sim, assim como no Azure, um locatário pode carregar imagens na pilha do Azure, além de usar as imagens do administrador do serviço. Para obter uma visão geral, consulte o [Adicionando uma imagem de máquina virtual](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Teste

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>Pode usar virtualização aninhada para testar o VDC de pilha do Microsoft Azure?

Virtualização aninhada não é suportada nem testada com 2 de visualização técnica do Azure pilha.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Pilha de Azure suporta discos dinâmicos para máquinas virtuais?

Pilha Azure não suporta discos dinâmicos.

## <a name="next-steps"></a>Próximas etapas

[Solução de problemas](azure-stack-troubleshooting.md)
