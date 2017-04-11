<properties
    pageTitle="Criar uma conta do Azure lote | Microsoft Azure"
    description="Saiba como criar uma conta do Azure lote no portal do Azure para executar cargas de trabalho paralelas grande escala na nuvem"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Crie uma conta de lote do Azure usando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [Gerenciamento de lote .NET](batch-management-dotnet.md)

Saiba como criar uma conta do Azure lote no [portal do Azure][azure_portal]e onde encontrar importantes de propriedades da conta, como as teclas de acesso e URLs de conta. Também discutimos lote preços e vincular uma conta de armazenamento do Azure a sua conta de lote para que você possa usar [pacotes de aplicativos](batch-application-packages.md) e [persistir saída de trabalho e tarefa](batch-task-output.md).

## <a name="create-a-batch-account"></a>Criar uma conta de lote

1. Entre [portal do Azure][azure_portal].

2. Clique em **novo** > **Calcular** > **serviço de lote**.

    ![Lote no mercado][marketplace_portal]

3. A lâmina de **Nova conta de lote** é exibida. Consulte itens *um* até *e* abaixo para obter descrições de cada elemento de blade.

    ![Criar uma conta de lote][account_portal]

    a. **Nome da conta**: um nome exclusivo para sua conta de lote. Esse nome deve ser exclusivo dentro da região Azure que a conta for criada (consulte *local* abaixo). Ele pode conter somente caracteres minúsculos, números e deve ser 3-24 caracteres de comprimento.

    b. **Assinatura**: uma assinatura no qual deseja criar a conta de lote. Se você tiver apenas uma assinatura, ela é selecionada por padrão.

    c. **Grupo de recursos**: um recurso existente agrupar para sua nova conta de lote ou, opcionalmente, crie um novo.

    d. **Local**: região de um Azure no qual deseja criar a conta de lote. Apenas as regiões compatíveis com a assinatura e o grupo de recursos são exibidas como opções.

    e. **Conta de armazenamento** (opcional): uma conta de armazenamento de **finalidade geral** associar (link) à sua nova conta de lote. Consulte [conta de armazenamento do Azure vinculado](#linked-azure-storage-account) abaixo para obter mais detalhes.

4. Clique em **criar** para criar a conta.

  O portal indica que é **Implantando** a conta e, após a conclusão, uma notificação de **implantações bem-sucedida** aparece em *notificações*.

## <a name="view-batch-account-properties"></a>Exibir propriedades de conta de lote

Depois que a conta foi criada, você pode abrir a **lâmina de conta de lote** para acessar suas propriedades e configurações. Você pode acessar todas as configurações de conta e propriedades usando o menu à esquerda da lâmina lote conta.

![Blade de conta de lote no portal do Azure][account_blade]

* **URL de conta do lote**: aplicativos que você cria com o [desenvolvimento de lote APIs](batch-technical-overview.md#batch-development-apis) precisam de uma URL de conta gerenciar recursos e executar trabalhos na conta. Uma URL de conta do lote tem o seguinte formato:

    `https://<account_name>.<region>.batch.azure.com`

![URL de conta de lote no portal][account_url]

* **Teclas de acesso**: seus aplicativos também precisará uma tecla de acesso ao trabalhar com recursos em sua conta do lote. Para exibir ou gerar teclas de acesso da sua conta de lote, insira `keys` na caixa de **pesquisa** menu esquerda na lâmina lote conta, selecione **teclas**.

    ![Teclas de conta de lote no portal do Azure][account_keys]

## <a name="pricing"></a>Preços

Contas de lote são oferecidas apenas em um "gratuito camada," que significa que você não é cobrados da conta de lote em si. Cobrado para os recursos de computação Azure subjacente que suas soluções de lote consumam e para os recursos consumidos por outros serviços quando suas cargas de trabalho executado. Por exemplo, cobrado para os nós de computação em seus pools e para os dados que você armazene em armazenamento do Azure como entrada ou saída de suas tarefas. Da mesma forma, se você usar o recurso de [pacotes de aplicativos](batch-application-packages.md) do lote, cobrado para os recursos de armazenamento do Azure usados para armazenar seus pacotes de aplicativos. Consulte [lote preços] [ batch_pricing] para obter mais informações.

## <a name="linked-azure-storage-account"></a>Conta vinculada de armazenamento do Azure

Conforme mencionado anteriormente, você pode vincular (opcionalmente) uma conta de armazenamento de **finalidade geral** à sua conta do lote. O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote usa o armazenamento de blob em um objetivo geral vinculado conta de armazenamento, como a biblioteca de [Lote arquivo convenções .NET](batch-task-output.md) . Esses recursos opcionais ajudarão-lo a implantar os aplicativos que executar suas tarefas em lotes e manter os dados que eles produzem.

Lote atualmente suporta *apenas* o tipo de conta de armazenamento de **finalidade geral** conforme descrito na etapa 5, [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account), no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Quando você vincula uma conta de armazenamento do Azure à sua conta de lote, ser certeza link *apenas* uma conta de armazenamento de **finalidade geral** .

![Criando uma conta de armazenamento de "Finalidade geral"][storage_account]

Recomendamos que você crie uma conta de armazenamento para uso exclusivo por sua conta de lote.

>[AZURE.WARNING] Tome cuidado ao gerar as teclas de acesso de uma conta de armazenamento vinculada. Gerar apenas uma chave de armazenamento de conta e clique em **Chaves de sincronização** na lâmina de conta de armazenamento vinculada. Aguarde cinco minutos para permitir que as teclas para se propagarem para os nós de computação na sua pools, em seguida, gerar e sincronizar a outra chave, se necessário. Se você gerar ambas as chaves ao mesmo tempo, sua nós de computação não será capazes de sincronizar qualquer tecla e eles perderão o acesso à conta de armazenamento.

  ![Gerar chaves de conta de armazenamento][4]

## <a name="batch-service-quotas-and-limits"></a>Limites e cotas de serviço de lote

Esteja ciente de que como com sua assinatura do Azure e outros serviços do Azure, determinados [cotas e limites](batch-quota-limit.md) se aplicam a lote contas. Cotas atuais para uma conta de lote aparecem no portal na conta de **Propriedades**.

![Cotas de conta de lote no portal do Azure][quotas]

Tenha essas cotas em mente enquanto você estiver criando e suas cargas de trabalho em lotes de dimensionamento. Por exemplo, se seu pool não alcançar o número de destino de nós de computação que você especificou, você talvez atingiu o limite de cota de núcleo para sua conta de lote.

Observe também que você não está restrito para uma única conta de lote para sua assinatura do Azure. Você pode executar várias cargas de trabalho de lote em uma única conta de lote ou distribuir suas cargas de trabalho entre contas de lote na mesma assinatura, mas em diferentes regiões Azure.

Muitos dessas cotas podem ser aumentados simplesmente com uma solicitação de suporte de produto gratuito enviada no portal do Azure. Para obter detalhes sobre como solicitar aumentos de cotas, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Outras opções de gerenciamento de conta em lotes

Além de usar o portal do Azure, você também pode criar e gerenciar contas de lote com o seguinte:

* [Cmdlets do PowerShell em lotes](batch-powershell-cmdlets-get-started.md)
* [CLI Azure](../xplat-cli-install.md)
* [Gerenciamento de lote .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximas etapas

* Consulte [Visão geral do recurso de lote do Azure](batch-api-basics.md) para saber mais sobre os recursos e os conceitos de serviço de lote. O artigo discute os recursos de lote primários como pools, nós de computação, trabalhos e tarefas e fornece uma visão geral dos recursos do serviço que permitem a execução de carga de trabalho de computação em grande escala.

* Aprenda as Noções básicas do desenvolvimento de um aplicativo habilitado para lotes usando a [biblioteca de cliente .NET de lote](batch-dotnet-get-started.md). O [artigo introdutório](batch-dotnet-get-started.md) orienta você a um aplicativo de trabalho que usa o serviço de lote para executar uma carga de trabalho em vários nós de computação e inclui usando o armazenamento do Azure para recuperação e transferência de arquivo de carga de trabalho.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Gerar chaves de conta de armazenamento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
