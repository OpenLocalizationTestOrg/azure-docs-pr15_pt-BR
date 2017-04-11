<properties
   pageTitle="Introdução ao Azure lote CLI | Microsoft Azure"
   description="Obtenha uma rápida introdução para os comandos de lote no Azure CLI de gerenciamento de recursos de serviço de lote do Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Introdução ao Azure lote CLI

A Interface de linha entre plataformas Azure (Azure CLI) permite que você gerencie suas contas de lote e recursos como pools, trabalhos e tarefas no Linux, Mac e Windows conchas de comando. Com a CLI do Azure, você pode executar e script muitas das mesmas tarefas que você executará com o lote de APIs, portal do Azure e cmdlets do PowerShell do lote.

Este artigo baseia-se no Azure CLI versão 0.10.5.

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar o Azure CLI](../xplat-cli-install.md)

* [Conectar a CLI Azure à sua assinatura do Azure](../xplat-cli-connect.md)

* Alternar para **modo de Gerenciador de recursos**:`azure config mode arm`

>[AZURE.TIP] Recomendamos que você atualize sua instalação do Azure CLI com frequência para tirar proveito das atualizações do serviço e aprimoramentos.

## <a name="command-help"></a>Ajuda do comando

Você pode exibir texto de ajuda para cada comando na CLI Azure anexando `-h` como a única opção após o comando. Por exemplo:

* Para obter ajuda para o `azure` de comando, digite:`azure -h`
* Para obter uma lista de todos os comandos de lote na CLI, use:`azure batch -h`
* Para obter ajuda sobre como criar uma conta de lote, insira:`azure batch account create -h`

Se estiver em dúvida, use o `-h` opção de linha de comando para obter ajuda sobre qualquer comando de CLI do Azure.

## <a name="create-a-batch-account"></a>Criar uma conta de lote

Uso:

    azure batch account create [options] <name>

Exemplo:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Cria uma nova conta de lote com os parâmetros especificados. Você deve especificar pelo menos um local, grupo de recursos e nome da conta. Se você ainda não tiver um grupo de recursos, crie um executando `azure group create`e especifique uma das regiões Azure (como "Oeste EUA") para o `--location` opção. Por exemplo:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] O nome de conta do lote deve ser exclusivo dentro da região Azure que a conta for criada. Ele pode conter apenas caracteres alfanuméricos minúsculas e deve ser 3-24 caracteres de comprimento. Você não pode usar caracteres especiais, como `-` ou `_` em nomes de conta de lote.

### <a name="linked-storage-account-autostorage"></a>Conta de armazenamento vinculado (autostorage)

(Opcionalmente) você pode vincular uma conta de armazenamento de **finalidade geral** à sua conta do lote quando criá-lo. O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote usa o armazenamento de blob em um objetivo geral vinculado conta de armazenamento, como a biblioteca de [Lote arquivo convenções .NET](batch-task-output.md) . Esses recursos opcionais ajudarão-lo a implantar os aplicativos que executar suas tarefas em lotes e manter os dados que eles produzem.

Para vincular uma conta existente do armazenamento do Azure para uma nova conta de lote quando criá-lo, especifique o `--autostorage-account-id` opção. Esta opção requer a identificação do recurso totalmente qualificado da conta de armazenamento.

Primeiro, mostre detalhes da sua conta de armazenamento:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Use o valor de **Url** para o `--autostorage-account-id` opção. O valor de Url começa com "/ assinaturas /" e contém seu caminho de recursos e de ID de assinatura para a conta de armazenamento:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Excluir uma conta de lote

Uso:

    azure batch account delete [options] <name>

Exemplo:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Exclui a conta de lote especificada. Quando solicitado, confirme que você deseja remover a conta (remoção de conta pode demorar algum tempo para ser concluída).

## <a name="manage-account-access-keys"></a>Gerenciar as teclas de acesso de conta

É necessário uma tecla de acesso para [criar e modificar recursos](#create-and-modify-batch-resources) na sua conta do lote.

### <a name="list-access-keys"></a>Teclas de acesso de lista

Uso:

    azure batch account keys list [options] <name>

Exemplo:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Lista as chaves de conta para a conta de lote determinada.

### <a name="generate-a-new-access-key"></a>Gerar uma nova chave de acesso

Uso:

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemplo:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Gera novamente a chave de conta especificada para a conta de lote determinada.

## <a name="create-and-modify-batch-resources"></a>Criar e modificar recursos de lote

Você pode usar a CLI do Azure para criar, ler, atualizar e excluir (CRUD) lote recursos como pools, calcular nós, trabalhos e tarefas. Essas operações CRUD exigem seu nome de conta do lote, a tecla de acesso e o ponto de extremidade. Você poderá especificá-los com o `-a`, `-k`, e `-u` opções ou definir [variáveis de ambiente](#credential-environment-variables) que CLI usa automaticamente (se preenchidas).

### <a name="credential-environment-variables"></a>Variáveis de ambiente de credencial

Você pode definir `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, e `AZURE_BATCH_ENDPOINT` variáveis de ambiente em vez de especificando `-a`, `-k`, e `-u` opções na linha de comando para cada comando executar. A CLI do lote utiliza essas variáveis (se definido) para que você pode omitir o `-a`, `-k`, e `-u` opções. O resto deste artigo pressupõe uso dessas variáveis de ambiente.

>[AZURE.TIP] Listar suas chaves com `azure batch account keys list`e exibir o ponto de extremidade da conta com `azure batch account show`.

### <a name="json-files"></a>Arquivos JSON

Quando você cria recursos de lote como pools e trabalhos, você pode especificar um arquivo JSON que contém a configuração do recurso novo em vez de passando seus parâmetros opções da linha de comando. Por exemplo:

`azure batch pool create my_batch_pool.json`

Enquanto você pode executar várias operações de criação de recurso usando apenas as opções de linha de comando, alguns recursos exigem um arquivo no formato JSON que contém os detalhes do recurso. Por exemplo, você deve usar um arquivo JSON se desejar especificar arquivos de recurso para uma tarefa de início.

Para encontrar o JSON necessário para criar um recurso, consulte a [referência de API REST de lote] [ rest_api] documentação no MSDN. Cada tópico "Adicionar *tipo de recurso"*contém exemplo JSON para criar o recurso, o que você pode usar como modelos para seus arquivos JSON. Por exemplo, JSON para a criação de pool pode ser encontrado em [Adicionar um pool a uma conta][rest_add_pool].

>[AZURE.NOTE] Se você especificar um arquivo JSON quando você cria um recurso, todos os outros parâmetros que você especificar na linha de comando para o recurso são ignorados.

## <a name="create-a-pool"></a>Criar um pool

Uso:

    azure batch pool create [options] [json-file]

Exemplo (configuração de máquina Virtual):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemplo (configuração de serviços de nuvem):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Cria um pool de nós de computação no serviço em lotes.

Conforme mencionado na [Visão geral do recurso de lote](batch-api-basics.md#pool), você tem duas opções quando você seleciona um sistema operacional para os nós no seu pool: **Configuração de máquina Virtual** e **Configuração de serviços de nuvem**. Use o `--image-*` opções para criar pools de configuração de máquina Virtual, e `--os-family` criar pools de configuração de serviços de nuvem. Não é possível especificar `--os-family` e `--image-*` opções.

Você pode especificar pool [pacotes de aplicativos](batch-application-packages.md) e a linha de comando para um [Iniciar tarefa](batch-api-basics.md#start-task). Para especificar arquivos de recursos para a tarefa de início, no entanto, você deve usar um [arquivo JSON](#json-files).

Exclua um pool com:

    azure batch pool delete [pool-id]

>[AZURE.TIP] Verifique a [lista de imagens de máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images) em valores apropriados para o `--image-*` opções.

## <a name="create-a-job"></a>Criar um trabalho

Uso:

    azure batch job create [options] [json-file]

Exemplo:

    azure batch job create --id "job001" --pool-id "pool001"

Adiciona um trabalho para a conta de lote e especifica o pool no qual executar suas tarefas.

Exclua um trabalho com:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Lista pools, trabalhos, tarefas e outros recursos

Cada tipo de recurso de lote suporta um `list` comando que sua conta em lotes de consultas e lista recursos desse tipo. Por exemplo, você pode listar os pools na sua conta e as tarefas em um trabalho:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Listando recursos com eficiência

Para consultar mais rápido, você pode especificar as opções de cláusula **Selecione**, **Filtrar**e **Expandir** para `list` operações. Use essas opções para limitar a quantidade de dados retornados pelo serviço de lote. Como toda a filtragem ocorre servidor, somente os dados que você está interessado em cruza o fio. Use essas cláusulas para economizar largura de banda (e, portanto, tempo) quando você executa operações de lista.

Por exemplo, isso retornará somente os pools cujos ids começar com "renderTask":

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

A CLI lote dá suporte a todos os três cláusulas; suportados pelo serviço lote:

* `--select-clause [select-clause]`Retornar um subconjunto de propriedades para cada entidade
* `--filter-clause [filter-clause]`Retornar somente entidades que correspondem a expressão de OData especificada
* `--expand-clause [expand-clause]`Obter as informações de entidade em uma única chamada REST subjacente. Cláusula expandir oferece suporte apenas a `stats` propriedade neste momento.

Para obter detalhes sobre as três cláusulas e executar consultas de lista com eles, consulte [consultar o serviço do Azure lote com eficiência](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Gerenciamento de pacote de aplicativos

Pacotes de aplicativos oferecem uma maneira simplificada para implantar os nós de computação em seus pools de aplicativos. Com a CLI do Azure, você pode carregar pacotes de aplicativos, gerenciar versões de pacote e excluir pacotes.

Para criar um novo aplicativo e adicionar uma versão do pacote:

**Criar** um aplicativo:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Adicionar** um pacote de aplicativos:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Ativar** o pacote:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Defina a **versão padrão** do aplicativo:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Implantar um pacote de aplicativo

Quando você cria um novo pool, você pode especificar um ou mais pacotes de aplicativo para implantação. Quando você especifica um pacote no momento da criação de pool, ele é implantado em cada nó como o pool de junções de nó. Pacotes também são implantados quando um nó é reiniciado ou recriar a imagem.

Especificar o `--app-package-ref` opção ao criar um pool para implantar um pacote de aplicativos para nós do pool ao entrarem o pool. O `--app-package-ref` opção aceita uma lista de separados por ponto-e-vírgula de ids do aplicativo para implantar os nós de computação.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Quando você cria um pool usando opções de linha de comando, você atualmente não é possível especificar *qual* versão do pacote de aplicativo para implantar os nós de computação, por exemplo "1.10-beta3". Portanto, você deve primeiro especificar uma versão padrão para o aplicativo com `azure batch application set [options] --default-version <version-id>` antes de criar o pool (consulte a seção anterior). No entanto, você pode especificar uma versão de pacote para o pool se você usar um [arquivo JSON](#json-files) em vez de opções de linha de comando ao criar o pool.

Você pode encontrar mais informações sobre os pacotes de aplicativo na [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md).

>[AZURE.IMPORTANT] Você deve [vincular uma conta de armazenamento do Azure](#linked-storage-account-autostorage) à sua conta de lote para usar os pacotes do aplicativo.

### <a name="update-a-pools-application-packages"></a>Pacotes de aplicativo de um pool de atualização

Para atualizar os aplicativos atribuídos a um pool existente, execute o `azure batch pool set` comando com a `--app-package-ref` opção:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Para implantar o novo pacote de aplicativo para calcular nós já presentes em um pool existente, você deverá reiniciar ou imagem em nós:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Você pode obter uma lista de nós em um pool, juntamente com suas identificações de nó, com `azure batch node list`.

Lembre-se que você deve já configurou o aplicativo com uma versão padrão antes da implantação (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

Esta seção destina-se a fornecer recursos para usar ao solucionar problemas de CLI do Azure. Ele não necessariamente resolve todos os problemas, mas pode ajudá-lo restringir a causa e aponte para ajudar a recursos.

* Use `-h` para obter o **texto de ajuda** para qualquer comando CLI

* Use `-v` e `-vv` para exibir a saída de comando **detalhada** ; `-vv` é "extra" detalhado e exibe o real restante solicitações e respostas. Essas opções são úteis para exibir o resultado de erro completo.

* Você pode exibir a **saída de comando como JSON** com a `--json` opção. Por exemplo, `azure batch pool show "pool001" --json` exibe as propriedades do pool001 formato JSON. Você pode copiar e modificar essa saída para usar em uma `--json-file` (consulte [arquivos JSON](#json-files) neste artigo).

* [Fórum de lote no MSDN] [ batch_forum] é um recurso de grande ajuda e monitorados de perto por membros da equipe de lote. Certifique-se de publicar suas perguntas lá, se você tiver problemas ou gostaria de ajuda com uma operação específica.

* Operação de recurso não cada lote é suportada atualmente pela CLI Azure. Por exemplo, você não pode especificar atualmente um pacote de aplicativo *versão* para um pool, apenas a identificação de pacote. Nesses casos, talvez você precise fornecer um `--json-file` para o comando em vez de usar opções de linha de comando. Certifique-se de se manter atualizado com a versão mais recente do CLI para retomar aperfeiçoamentos futuros.

## <a name="next-steps"></a>Próximas etapas

*  Consulte [implantação do aplicativo com pacotes de aplicativos do Azure lote](batch-application-packages.md) para descobrir como usar esse recurso para gerenciar e implantar os aplicativos que você executar em nós de computação de lote.

* Veja [o serviço do lote de consulta com eficiência](batch-efficient-list-queries.md) mais sobre como reduzir o número de itens e o tipo de informação que é retornado para consultas ao lote.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx