<properties
   pageTitle="Atualizar um cluster de estrutura de serviço do Azure | Microsoft Azure"
   description="Atualize o código de estrutura de serviço e/ou configuração que executa um cluster de estrutura de serviço, incluindo a configuração de modo de atualização de cluster, atualizando certificados, adicionando portas do aplicativo, fazendo patches de sistema operacional, e assim por diante. O que você pode esperar quando as atualizações são executadas?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Atualizar um cluster de estrutura de serviço do Azure

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autônomo](service-fabric-cluster-upgrade-windows-server.md)

Para qualquer sistema moderno, design para possibilidade de atualização é chave para atingir o sucesso a longo prazo do seu produto. Um cluster de estrutura de serviço do Azure é um recurso que você possui, mas parcialmente é gerenciada pela Microsoft. Este artigo descreve o que é gerenciado automaticamente e o que você pode configurar por conta própria.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controlando a versão de tecidos que é executado em seu Cluster

Você pode definir seu cluster para receber atualizações automáticas tecidos, quando a Microsoft lançar uma nova versão ou escolha uma versão de tecidos com suporte que deseja que o seu cluster no.

Você pode fazer isso definindo a configuração de cluster "upgradeMode" no portal ou usando o Gerenciador de recursos no momento da criação ou posterior em um cluster ao vivo 

>[AZURE.NOTE] Certifique-se de manter o seu cluster executando uma versão de tecidos compatíveis sempre. Como e quando podemos anunciar o lançamento de uma nova versão da estrutura de serviço, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias da data. as novas versões estão anunciado [no blog da equipe de estrutura de serviço](https://blogs.msdn.microsoft.com/azureservicefabric/ ). A nova versão está disponível para escolher depois. 

14 dias anteriores a expiração da versão que seu cluster está em execução, um evento de integridade é gerado coloca seu cluster em um estado de integridade de aviso. Cluster permanece em um estado de aviso até você atualiza para uma versão compatível tecidos.


### <a name="setting-the-upgrade-mode-via-portal"></a>Definindo o modo de atualização por meio do portal 

Você pode definir o cluster como automático ou manual quando você estiver criando o cluster.

![Create_Manualmode][Create_Manualmode]

Você pode configurar o cluster para automático ou manual quando em um cluster ao vivo, usando a experiência de gerenciar. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Atualizando para uma nova versão em um cluster que está definido para o modo Manual via portal.
 
Para atualizar para uma nova versão, tudo o que você precisa fazer é selecionar a versão disponível na lista suspensa e salvar. A atualização de tecidos obtém inicializada automaticamente. As diretivas de integridade de cluster (uma combinação de integridade de nó e a integridade todos os aplicativos executados no cluster) sejam atendidas durante a atualização.

Se as diretivas de integridade de cluster não forem atendidas, a atualização é revertida. Role para baixo este documento para ler mais sobre como definir essas políticas de integridade personalizado. 

Depois que você tiver corrigido os problemas que resultou na reversão, é necessário iniciar a atualização novamente, seguindo as mesmas etapas antes.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Definindo o modo de atualização por meio de um modelo do Gerenciador de recursos 

Adicionar a configuração de "upgradeMode" para a definição de recurso Microsoft.ServiceFabric/clusters e definir o "clusterCodeVersion" para uma das versões compatíveis tecidos conforme mostrado abaixo e implantar o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Atualizando para uma nova versão em um cluster que está definido para o modo Manual por meio de um modelo do Gerenciador de recursos.
 
Quando o cluster está no modo Manual, para atualizar para uma nova versão, altere o "clusterCodeVersion" para uma versão compatível e implantá-lo. A implantação do modelo, é ativado da atualização tecidos obtém inicializados automaticamente. As diretivas de integridade de cluster (uma combinação de integridade de nó e a integridade todos os aplicativos executados no cluster) sejam atendidas durante a atualização.

Se as diretivas de integridade de cluster não forem atendidas, a atualização é revertida. Role para baixo este documento para ler mais sobre como definir essas políticas de integridade personalizado. 

Depois que você tiver corrigido os problemas que resultou na reversão, é necessário iniciar a atualização novamente, seguindo as mesmas etapas antes.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obter a lista de todos os versão disponível para todos os ambientes para uma determinada assinatura

Execute o seguinte comando e você verá uma saída semelhante a esta.

"supportExpiryUtc" informa seu quando uma versão está expirando ou expirou. A versão mais recente não tem uma data válida - possui um valor de "9999-12-31T23:59:59.9999999", que significa apenas que a data de vencimento ainda não está definida.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportamento de atualização de tecidos quando o modo de atualização de cluster é automático

A Microsoft mantém o código de estrutura e a configuração que é executado em um cluster Azure. Podemos executar atualizações automáticas de monitoradas ao software de forma conforme necessário. Essas atualizações podem ser código, configuração ou ambos. Para certificar-se de que seu aplicativo é afetada nenhum impacto ou impacto mínimo devido a essas atualizações, podemos executar as atualizações nas fases a seguir:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Uma atualização é executada usando todas as diretivas de integridade de cluster

Durante esta fase, as atualizações continuar um domínio de atualização uma vez e os aplicativos que foram executados no cluster continuarão sendo executados sem qualquer tempo de inatividade. As diretivas de integridade de cluster (uma combinação de integridade de nó e a integridade todos os aplicativos executados no cluster) sejam atendidas durante a atualização.

Se as diretivas de integridade de cluster não forem atendidas, a atualização é revertida. Em seguida, um email é enviado para o proprietário da assinatura. O email contém as seguintes informações:

- Notificação de que tivemos que reverter uma atualização de cluster.
- Sugerido ações corretivas, se houver.
- O número de dias (n) até que podemos executar fase 2.

Vamos tentar executar a atualização do mesma alguns mais vezes em caso de falha de todas as atualizações por razões de infraestrutura. Após os n dias da data de que email foi enviado, podemos continuar a fase 2.

Se as diretivas de integridade de cluster forem atendidas, a atualização é considerada bem-sucedida e marcada como concluída. Isso pode acontecer durante a atualização inicial ou qualquer uma da execução repetida atualização nesta fase. Não há nenhuma confirmação de email de uma execução bem-sucedido. Isso é para evitar o envio de você muitos emails – receber um email deve ser consideradas como uma exceção ao normal. Esperamos que a maioria das atualizações cluster tenha êxito sem afetar sua disponibilidade de aplicativo.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Uma atualização é executada usando somente diretivas de integridade padrão

As políticas de integridade nesta fase são definidas de forma que o número de aplicativos que foram eficaz, no início da atualização permanece o mesmo para a duração do processo de atualização. Como fase 1, as atualizações de fase 2 continuar um domínio de atualização uma vez e os aplicativos que foram executados no cluster continuarão sendo executados sem qualquer tempo de inatividade. As diretivas de integridade de cluster (uma combinação de integridade de nó e a integridade todos os aplicativos executados no cluster) sejam atendidas para a duração da atualização.

Se as diretivas de integridade de cluster em vigor não forem atendidas, a atualização é revertida. Em seguida, um email é enviado para o proprietário da assinatura. O email contém as seguintes informações:

- Notificação de que tivemos que reverter uma atualização de cluster.
- Sugerido ações corretivas, se houver.
- O número de dias (n) até que podemos executar fase 3.

Vamos tentar executar a atualização do mesma alguns mais vezes em caso de falha de todas as atualizações por razões de infraestrutura. Um email de lembrete é enviado a alguns dias antes n dias estão acima. Depois de n dias da data de que email foi enviado, podemos passar à fase 3. Os emails que enviaremos na fase 2 devem ser levados a sério e ações corretivas devem ser seguidas.

Se as diretivas de integridade de cluster forem atendidas, a atualização é considerada bem-sucedida e marcada como concluída. Isso pode acontecer durante a atualização inicial ou qualquer uma da execução repetida atualização nesta fase. Não há nenhuma confirmação de email de uma execução bem-sucedido.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Uma atualização é executada usando diretivas de integridade rigorosas

Essas políticas de integridade nesta fase são voltadas para a conclusão da atualização em vez da integridade dos aplicativos. Atualizações de cluster poucos acabam nesta fase. Se seu cluster obtém para esta fase, há uma boa chance de que seu aplicativo se tornará não íntegro e/ou perderá disponibilidade.

Da mesma forma que as outras duas fases, atualizações de fase 3 continuar um domínio de atualização por vez.

Se as diretivas de integridade de cluster não forem atendidas, a atualização é revertida. Vamos tentar executar a atualização do mesma alguns mais vezes em caso de falha de todas as atualizações por razões de infraestrutura. Depois disso, o cluster é fixado, para que ele não receberão mais suporte e/ou atualizações.

Um email com essa informação é enviado para o proprietário da assinatura, juntamente com as ações corretivas. Não esperamos qualquer clusters para entrar em um estado em que a fase 3 falhou.

Se as diretivas de integridade de cluster forem atendidas, a atualização é considerada bem-sucedida e marcada como concluída. Isso pode acontecer durante a atualização inicial ou qualquer uma da execução repetida atualização nesta fase. Não há nenhuma confirmação de email de uma execução bem-sucedido.

## <a name="cluster-configurations-that-you-control"></a>Configurações de cluster que você controle

Além da capacidade de definir o cluster modo de atualização, aqui estão as configurações que você pode alterar em um cluster ao vivo.

### <a name="certificates"></a>Certificados

Você pode adicionar novas ou excluir certificados para o cliente através do portal e cluster facilmente. Consulte [Este documento para obter instruções detalhadas](service-fabric-cluster-security-update-certs-azure.md)

![Captura de tela que mostra impressões digitais de certificado no portal do Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Portas de aplicativo

Você pode alterar portas de aplicativo alterando as propriedades de recurso de Balanceador de carga que são associadas com o tipo de nó. Você pode usar o portal, ou você pode usar o Gerenciador de recursos do PowerShell diretamente.

Para abrir uma nova porta em todas as VMs em um tipo de nó, faça o seguinte:

1. Adicione um novo teste ao balanceador de carga apropriado.

    Se você implantou o cluster usando o portal, os balanceadores de carga são nomeados "LB-nome do grupo-NodeTypename recurso", um para cada tipo de nó. Como os nomes de Balanceador de carga são exclusivos somente dentro de um grupo de recursos, é melhor se você procurá-los em um grupo de recursos específicos.

    ![Captura de tela que mostra a adição de um teste para um balanceador de carga no portal.][AddingProbes]

2. Adicione uma nova regra ao balanceador de carga.

    Adicione uma nova regra ao mesmo balanceador de carga usando o teste que você criou na etapa anterior.

    ![Adicionando uma nova regra para um balanceador de carga no portal.][AddingLBRules]


### <a name="placement-properties"></a>Propriedades de posicionamento

Para cada um dos tipos de nó, você pode adicionar propriedades de posicionamento personalizado que você deseja usar em seus aplicativos. NodeType é uma propriedade padrão que você pode usar sem adicioná-la explicitamente.

>[AZURE.NOTE] Para obter detalhes sobre o uso de restrições de posicionamento, propriedades de nó e como defini-los, consulte a seção "Posicionamento restrições e nó propriedades" no Gerenciador de recursos de Cluster do serviço tecidos documento em [Descrevendo seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Métricas de capacidade

Para cada um dos tipos de nó, você pode adicionar métricas de capacidade personalizado que você deseja usar em seus aplicativos a carga de relatório. Para obter detalhes sobre o uso de métricas de capacidade ao relatório carregar, consulte os documentos do Gerenciador de recursos do serviço tecidos Cluster no [Descrevendo seu Cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [métricas e carga](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Configurações de atualização de tecidos - as diretivas de integridade

Você pode especificar as diretivas de integridade personalizada para atualização de tecidos. Se você configurou seu cluster às atualizações automáticas tecidos, essas políticas são aplicadas a fase-1 das atualizações automáticas tecidos.
Se você configurou seu cluster tecidos Manual atualizações, essas políticas são aplicadas sempre que você selecionar uma nova versão acionar o sistema para iniciar a atualização de tecidos no seu cluster. Se você não substitui as políticas, os padrões são usados.

Você pode especificar as diretivas de integridade personalizados ou revisar as configurações atuais em lâmina "atualização tecidos", selecionando as configurações avançadas de atualização. Examine a imagem a seguir sobre como. 

![Gerenciar diretivas de integridade personalizado][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personalizar as configurações de tecidos para o seu cluster

Consulte [configurações do serviço tecidos cluster tecidos](service-fabric-cluster-fabric-settings.md) no que e como você pode personalizá-los.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Patches de sistema operacional nas VMs que constituem o cluster

Esse recurso está planejado para o futuro como um recurso automatizado. Mas, atualmente, você é responsável patch suas VMs. Você deve fazer esta máquina um virtual por vez, para que não tomadas mais de uma de cada vez.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Atualizações de sistema operacional nas VMs que constituem o cluster

Se você precisa atualizar a imagem de sistema operacional nas máquinas virtuais do cluster, você deve fazê-lo máquina um virtual por vez. Você é responsável para essa atualização, não há atualmente nenhuma automação para isso.

## <a name="next-steps"></a>Próximas etapas
- Saiba como personalizar algumas das [configurações do serviço tecidos cluster tecidos](service-fabric-cluster-fabric-settings.md)
- Saiba como [dimensionar seu cluster e reduzir](service-fabric-cluster-scale-up-down.md)
- Saiba mais sobre [atualizações de aplicativo](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG