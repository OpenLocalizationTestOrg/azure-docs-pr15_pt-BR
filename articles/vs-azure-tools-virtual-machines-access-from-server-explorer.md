<properties
   pageTitle="Acessando o Azure máquinas virtuais do Server Explorer | Microsoft Azure"
   description="Obtenha uma visão geral de como visualizar, criar e gerenciar Azure VMs (máquinas virtuais) no Server Explorer no Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Acessando o Azure máquinas virtuais do Server Explorer

Usando o Server Explorer no Visual Studio, você pode exibir informações sobre suas máquinas virtuais hospedadas por Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Acessando máquinas virtuais no Server Explorer

Se você tiver máquinas virtuais hospedadas por Azure, você pode acessá-los no Server Explorer. Você deve primeiro entrar à sua assinatura do Azure para exibir seus serviços móveis. Para entrar, abra o menu de atalho para o nó Azure no Server Explorer e escolha **conectar-se ao Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Para obter informações sobre suas máquinas virtuais

1. No Server Explorer, escolha uma máquina virtual e, em seguida, escolha a tecla F4 para mostrar sua janela de propriedades.

    A tabela a seguir mostra quais propriedades estão disponíveis, mas eles são todos somente leitura. Para alterá-las, use o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Propriedade|Descrição|
  	|---|---|
  	|Nome DNS|A URL com o endereço de Internet da máquina virtual.|
  	|Ambiente|Para uma máquina virtual, o valor desta propriedade é sempre produção.|
  	|Nome|O nome da máquina virtual.|
  	|Tamanho|O tamanho da máquina virtual, que reflete a quantidade de memória e espaço em disco disponível. Para obter mais informações, consulte como: configurar tamanhos de máquina Virtual.|
  	|Status|Os valores incluem iniciando, iniciado, interromper, parar e Status de recuperação. Se o Status de recuperação for exibido, o status atual é desconhecido. Os valores desta propriedade diferem dos valores que são usados no [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|A identificação de assinatura de sua conta do Azure. Você pode mostrar essas informações no [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885) exibindo as propriedades para uma assinatura.|

1. Escolha um nó de ponto de extremidade e, em seguida, exibir a janela de **Propriedades** .

1. A tabela a seguir descreve as propriedades disponíveis de pontos de extremidade, mas eles são somente leitura. Para adicionar ou editar os pontos de extremidade para uma máquina virtual, use o [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|Propriedade|Descrição|
  	|---|---|
  	|Nome|Um identificador para o ponto de extremidade.|
  	|Porta de particular|A porta para acesso de rede interna para seu aplicativo.|
  	|Protocolo|O protocolo que usa a camada de transporte para este ponto de extremidade, TCP ou UDP.|
  	|Porta pública|A porta que é usada para o acesso público ao seu aplicativo.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar funções Azure no Visual Studio, consulte [Usando a área de trabalho remota com funções do Azure](vs-azure-tools-remote-desktop-roles.md).
