<properties
    pageTitle="Como migrar de um RemoteApp VNET para um VNET Azure | Microsoft Azure"
    description="Saiba como migrar de um RemoteApp VNET para um VNET do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="how-to-migrate-a-hybrid-collection-from-a-remoteapp-vnet-to-an-azure-vnet"></a>Como migrar um conjunto de híbrido de um RemoteApp VNET para um VNET do Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Boa notícia! Podemos habilitou a implantar as coleções de RemoteApp híbrido diretamente em seu Azure virtuais redes existentes (VNETs) em vez de criar VNETs RemoteApp específicas. Isso permite aproveitar os recursos mais recentes do VNET (como rota expressa) e dar acesso direto à rede de seus conjuntos de híbrido para outros serviços Azure e máquinas virtuais implantadas para esse VNET.  (Isso apresenta melhor desempenho e configuração mais fácil de configurações de VNET para VNET).


Digamos que você já tiver criado um conjunto de RemoteApp chamado *OriginalCollection* com um RemoteApp VNET chamado *RemoteAppVNET*híbrido. Estas são as etapas para migrá-la para um novo VNET do Azure chamado *AzureVNET*.

1.  Na guia **redes** no [portal de gerenciamento](http://manage.windowsazure.com/), crie um VNET chamado *AzureVNET*, usando o mesmo local, a configuração de DNS e o espaço de endereço (para pelo menos uma das sub-redes *AzureVNET* ) como você usou para *RemoteAppVNET*.
2.  Configurar *AzureVNET* para qualquer um dos host ou ter conectividade de rede para implantação do Active Directory *OriginalCollection* é domínio associado.
3.  Na guia **RemoteApps** , crie um novo conjunto de RemoteApp chamado *Novo conjunto*. (Use a opção de **criar com VNET** , não **Criar rápida**.)
3.  Configure *NewCollection* para ser implantado a sub-rede no *AzureVNET*.
4.  Configure *NewCollection* para usar a mesma imagem e informações de associação de domínio como você usou para *OriginalCollection*.
5.  Após algumas horas, *NewCollection* aparecerá na sua lista de coleções com um estado ativo.

Agora, se você não precisa migrar informações do usuário da coleção original para o novo conjunto, siga estas etapas Avançar:

6.  Exclua *OriginalCollection*.
7.  Exclua *RemoteAppVNET*.

E pronto!

Como alternativa, se você precisar migrar informações do usuário da coleção original para o novo conjunto, siga estas etapas Avançar:

6.  Enviar um email para [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) com sua ID do Azure assinatura, o nome do seu conjunto de original e o nome da sua nova coleção e peça-lhe para migrar suas informações de usuário.
7.  Em 2 dias úteis a equipe de RemoteApp moverá a lista de acesso de usuário e todos os documentos do usuário e configurações de usuário da coleção original para o novo conjunto.
8.  Exclua *OriginalCollection*.
9.  Exclua *RemoteAppVNET*.

E agora, pronto!

Se você tiver dúvidas ou precisa de ajuda especial, envie um email [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
