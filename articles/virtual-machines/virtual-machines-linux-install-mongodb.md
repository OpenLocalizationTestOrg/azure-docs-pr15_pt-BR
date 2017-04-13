<properties
   pageTitle="Instalar o MongoDB em uma máquina virtual Linux | Microsoft Azure"
   description="Saiba como instalar e configurar o MongoDB em um computador de virtual Linux no Azure usando o modelo de implantação do Gerenciador de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Instalar e configurar o MongoDB em uma máquina virtual de Linux no Azure
[MongoDB](http://www.mongodb.org) é um popular código-fonte aberto alto desempenho NoSQL banco de dados. Este artigo mostra como instalar e configurar o MongoDB em uma VM Linux no Azure usando o modelo de implantação do Gerenciador de recursos. Exemplos são mostrados como detalhes para:

- [Instalar e configurar uma instância do MongoDB básica manualmente](#manually-install-and-configure-mongodb-on-a-vm)
- [Criar uma instância de MongoDB básica usando um modelo do Gerenciador de recursos](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Criar um MongoDB complexo sharded cluster com réplica define usando um modelo do Gerenciador de recursos](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer o seguinte:

- uma conta do Microsoft Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
- o [Azure CLI](../xplat-cli-install.md) conectado com`azure login`
- o Azure CLI *deve estar* no Gerenciador de recursos do Azure usando o modo`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar o MongoDB em uma máquina virtual manualmente
MongoDB [fornecem instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distros Linux incluindo Red Hat / CentOS, SUSE, Ubuntu e Debian. O exemplo a seguir cria um `CoreOS` máquina virtual usando uma chave SSH armazenada em `.ssh/azure_id_rsa.pub`. Responda as solicitações para nome da conta de armazenamento, nome DNS e credenciais de administrador:

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Faça logon na máquina virtual usando o endereço IP público exibido no final da etapa de criação de máquina virtual anterior:

```bash
ssh ops@40.78.23.145
```

Para adicionar fontes de instalação para MongoDB, crie um `yum` arquivo de repositório da seguinte forma:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Abra o arquivo de repo MongoDB para edição. Adicione as seguintes linhas:

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Instalar MongoDB usando `yum` da seguinte maneira:

```bash
sudo yum install -y mongodb-org
```

Por padrão, SELinux é aplicado em imagens CentOS que impede que você acesse MongoDB. Ferramentas de gerenciamento de política de instalar e configurar SELinux para permitir MongoDB operando em sua porta TCP padrão 27017 da seguinte maneira. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço de MongoDB da seguinte maneira:

```bash
sudo service mongod start
```

Verificar a instalação MongoDB conectando-se usando local `mongo` cliente:

```bash
mongo
```

Agora teste a instância MongoDB adicionando alguns dados e, em seguida, pesquisa:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se desejar, configure MongoDB para iniciar automaticamente durante a reinicialização do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar uma instância de MongoDB básica em CentOS usando um modelo
Você pode criar uma instância de MongoDB básica em uma única VM CentOS usando o modelo de início rápido Azure seguintes do Github. Este modelo usa a extensão de Script personalizado para Linux para adicionar um `yum` repositório para sua máquina virtual de CentOS recém-criado e instale MongoDB.

- [Instância do MongoDB básica em CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

O exemplo a seguir cria um grupo de recursos com o nome `myResourceGroup` no `WestUS` região. Insira seus próprios valores da seguinte maneira:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] A CLI do Azure retorna ao prompt de dentro de alguns segundos de criação de implantação, mas a instalação e configuração leva alguns minutos para ser concluída. Verificar o status da implantação com `azure group deployment show myResourceGroup`, inserir o nome do seu grupo de recursos adequadamente. Aguarde até que o `ProvisioningState` mostra 'Êxito' antes de tentar SSH para a máquina virtual.

Depois que a implantação for concluída, SSH para a máquina virtual. Obter o endereço IP do seu uso de máquina virtual do `azure vm show` comando como no exemplo seguinte:

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

Próximo ao final da saída, o `Public IP address` é exibida. SSH para sua máquina virtual com o endereço IP da sua máquina virtual:

```bash
ssh ops@138.91.149.74
```

Verificar a instalação MongoDB conectando-se usando local `mongo` cliente da seguinte forma:

```bash
mongo
```

Agora teste a instância adicionando alguns dados e pesquisando da seguinte maneira:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um Cluster de Sharded MongoDB complexos em CentOS usando um modelo
Você pode criar um cluster de sharded MongoDB complexo usando o modelo de início rápido Azure seguintes do Github. Este modelo segue as [práticas recomendadas do MongoDB cluster sharded](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer redundância e alta disponibilidade. O modelo cria duas fragmentos, com três nós em cada conjunto de réplica. Uma réplica de servidor de configuração definida com três nós também é criada, mais dois `mongos` servidores de roteador para fornecer consistência para aplicativos de entre os fragmentos.

- [MongoDB fragmentação Cluster CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Implantando este cluster de sharded MongoDB complexa requer mais de 20 cores, que normalmente é a contagem de núcleo padrão por região para uma assinatura. Abra uma solicitação de suporte Azure para aumentar o total de core.

O exemplo a seguir cria um grupo de recursos com o nome `myResourceGroup` no `WestUS` região. Insira seus próprios valores da seguinte maneira:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] A CLI do Azure retorna ao prompt de dentro de alguns segundos de criação de implantação, mas a instalação e configuração podem levar uma hora para ser concluída. Verificar o status da implantação com `azure group deployment show myResourceGroup`, ajustando o nome do seu grupo de recursos adequadamente. Aguarde até que o `ProvisioningState` mostra 'Êxito' antes de conectar a VMs.


## <a name="next-steps"></a>Próximas etapas
Nesses exemplos, você se conectar à instância MongoDB localmente a máquina virtual. Se você quiser conectar à instância do MongoDB de outra máquina virtual ou rede, certifique-se de que o apropriado [são criadas regras de grupo de segurança de rede](virtual-machines-linux-nsg-quickstart.md).

Para obter mais informações sobre a criação de uso de modelos, consulte [Visão geral do Gerenciador de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Os modelos de Gerenciador de recursos do Azure usam a extensão de Script personalizado para baixar e executar scripts em suas VMs. Para obter mais informações, consulte [usando a extensão de Script personalizado do Azure com máquinas virtuais do Linux](virtual-machines-linux-extensions-customscript.md).