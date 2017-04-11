<properties
    pageTitle="Antes de implantar Azure pilha VDC | Microsoft Azure"
    description="Exiba os requisitos de hardware e o ambiente do Azure pilha VDC (administrador de serviço)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Pré-requisitos de implantação do Azure pilha

Antes de implantar Azure pilha VDC ([Prova de conceito](azure-stack-poc.md)), verifique se que seu computador atende aos seguintes requisitos.
Os requisitos de implantação de 2 de visualização técnica para o VDC são semelhantes aos necessários para Technical Preview 1. Portanto, você pode usar o mesmo hardware que você usou para a visualização de única caixa anterior.

## <a name="hardware"></a>Hardware

| Componente | Mínimo  | Recomendados |
|---|---|---|
| Unidades de disco: sistema operacional | 1 disco de sistema operacional com o mínimo de 200 GB disponível para partição do sistema (SSD ou unidade de disco rígido) | 1 disco de sistema operacional com o mínimo de 200 GB disponível para partição do sistema (SSD ou unidade de disco rígido) |
| Unidades de disco: geral Azure pilha VDC dados | 4 discos. Cada disco fornece um mínimo de 140 GB de capacidade (SSD ou unidade de disco rígido). Todos os discos disponíveis serão usados. | 4 discos. Cada disco fornece um mínimo de 250 GB de capacidade (SSD ou unidade de disco rígido). Todos os discos disponíveis serão usados.|
| Calcular: CPU | Duplo-soquete: 12 física Cores (total)  | Duplo-soquete: 16 física Cores (total) |
| Calcular: memória | 96 GB DE RAM  | 128 GB DE RAM |
| Calcular: BIOS | Hyper-V habilitada (com suporte SLAT)  | Hyper-V habilitada (com suporte SLAT) |
| Rede: NIC | Certificação do Windows Server 2012 R2 necessários para NIC; Não há recursos especializados obrigatórios | Certificação do Windows Server 2012 R2 necessários para NIC; Não há recursos especializados obrigatórios |
| Certificação do logotipo de hardware | [Certificados para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificados para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Configuração de unidade de disco de dados:** Todas as unidades de dados devem ser do mesmo tipo (SAS ou todos SATA) e capacidade. Se unidades de disco SAS forem usadas, as unidades de disco deve ser anexadas por meio de um único caminho (sem MPIO, suporte a vários caminhos é fornecido).

**Opções de configuração de HBA**
 
- (Preferencial) HBA Simple
- RAID HBA – adaptador deve ser configurado no modo de "passagem"
- RAID HBA – discos devem ser configurados como disco único, RAID-0

**Combinações de tipo de mídia e barramento com suporte**

-   DISCO RÍGIDO SATA

-   UNIDADE DE DISCO RÍGIDO SAS

-   UNIDADE DE DISCO RÍGIDO RAID

-   RAID SSD (se o tipo de mídia é não especificado/desconhecido\*)

-   SATA SSD + DISCO RÍGIDO SATA

-   SAS SSD + UNIDADE DE DISCO RÍGIDO SAS

\*Rack sem a capacidade de passagem não reconhece o tipo de mídia. Esses controladores irá marcar HD e SSD como não especificado. Nesse caso, o SSD será usado como armazenamento persistente em vez de dispositivos de armazenamento em cache. Portanto, você pode implantar o VDC do Microsoft Azure pilha nesses SSDs.

**Exemplo HBAs**: LSI 9207 8i, LSI-9300-8i ou LSI-9265-8i no modo de passagem

Exemplo de configurações de OEM está disponíveis.

## <a name="operating-system"></a>Sistema Operacional

| | **Requisitos**  |
|---|---|
| **Versão do sistema operacional** | Windows Server 2012 R2 ou posterior. A versão do sistema operacional não crítica antes de inicia a implantação, como você vai inicializar o computador host no VHD que está incluído em zip de instalação do Azure pilha. O sistema operacional e todos os patches necessários são já integrados a imagem. Não use quaisquer teclas ativar quaisquer instâncias do Windows Server usadas à VDC.|

## <a name="deployment-requirements-check-tool"></a>Ferramenta de verificação de requisitos de implantação

Depois de instalar o sistema operacional, você pode usar o [Verificador de implantação do Azure pilha Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar se o hardware atende a todos os requisitos.



## <a name="microsoft-azure-active-directory-accounts"></a>Contas do Active Directory do Microsoft Azure

A implantação do Microsoft Azure pilha VDC deve estar conectada ao Azure. Portanto, você deve preparar uma conta do Microsoft Azure Active Directory antes de executar a implantação script do PowerShell. Essa conta torna-se o Administrador Global do locatário do Active Directory do Azure. Ele será usado para provisionar e delegar aplicativos e objetos de serviço para todos os serviços do Azure pilha que interagem com o Active Directory do Azure e API de gráfico. Ele também será usado como o proprietário da assinatura do provedor padrão (que você pode alterar mais tarde). Você pode efetuar login no portal de administração do seu sistema de pilha Azure usando esta conta.

1. Crie uma conta do Azure AD que é o administrador de diretório para pelo menos um Azure Active Directory. Se você já tem um, você pode usá-lo. Caso contrário, você pode criar uma gratuitamente por [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (na China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> em vez disso.)

    Salve essas credenciais para uso na etapa 6 de [executar o script de implantação do PowerShell](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Essa conta de *administrador de serviço* pode configurar e gerenciar nuvens de recurso, contas de usuário, planos de locatário, cotas e preços. No portal do, eles podem criar site nuvens, nuvens privadas de máquina virtual, criar planos e gerenciar assinaturas de usuário.

2. [Criar](azure-stack-add-new-user-aad.md) pelo menos uma conta para que você entrar à VDC de pilha do Azure como um locatário.

  	| **Conta do Microsoft Azure Active Directory**  | **Suporte?** |
  	|---|---| 
  	| Conta corporativa ou escolar com assinatura válida do Azure pública  | Sim |
  	| Account da Microsoft com assinatura válida do Azure pública  | Não |
  	| Conta corporativa ou escolar com assinatura válida do Azure China  | Sim |
  	| Conta corporativa ou escolar conosco governo Azure assinatura válida  | Sim |


## <a name="network"></a>Rede

### <a name="switch"></a>Alternar

Uma porta disponível em um switch da máquina VDC.  

Máquina Azure pilha VDC suporta a conexão a uma mudança acesso tronco porta ou. Não há recursos especializados são necessárias no switch. Se você estiver usando uma porta de tronco ou se você precisa configurar um ID de VLAN, você precisa fornecer a identificação de VLAN como um parâmetro de implantação. Você pode ver exemplos na [lista de parâmetros de implantação](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Sub-rede

Não conecte máquina VDC para as seguintes sub-redes:
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Estas sub-redes são reservadas para as redes internas dentro do ambiente do Microsoft Azure pilha VDC.

### <a name="ipv4ipv6"></a>IPv4/IPv6

Somente IPv4 é suportada. Você não pode criar redes IPv6.

### <a name="dhcp"></a>DHCP

Verifique se há um servidor DHCP disponível na rede que a NIC se conecta. Se DHCP não estiver disponível, você deve preparar uma rede IPv4 estática adicional além usada pelo host. Você deve fornecer o endereço IP e o gateway como um parâmetro de implantação. Você pode ver exemplos na [lista de parâmetros de implantação](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Acesso à Internet

Pilha Azure requer acesso à Internet, diretamente ou através de um proxy transparente. Pilha Azure não dá suporte a configuração de um proxy da web para habilitar o acesso à Internet. O IP do host e o novo IP atribuída ao MAS-BGPNAT01 (por DHCP ou IP estático) devem ser capaz de acessar a Internet. Portas 80 e 443 são usadas em domínios de graph.windows.net e login.windows.net.

### <a name="telemetry"></a>Telemetria

Para dar suporte ao fluxo de dados de telemetria, porta 443 (HTTPS) deve ser aberta em sua rede. O ponto de extremidade do cliente é https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Próximas etapas

[Baixe o pacote de implantação do Azure pilha VDC](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implantar o Azure pilha VDC](azure-stack-run-powershell-script.md)
