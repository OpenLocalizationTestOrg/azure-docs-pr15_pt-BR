<properties
    pageTitle="Como usar a API (Python) - guia de recursos de gerenciamento de serviço"
    description="Saiba como executar tarefas comuns de gerenciamento de serviço programaticamente do Python."
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>Como usar o serviço de gerenciamento do Python

> [AZURE.NOTE] API de gerenciamento de serviço está sendo substituído com a nova API de gerenciamento de recursos, atualmente disponível em uma versão de visualização.  Consulte a [documentação de gerenciamento de recursos do Azure](http://azure-sdk-for-python.readthedocs.org/) para obter detalhes sobre como usar a nova API de gerenciamento de recursos do Python.

Este guia mostra como executar tarefas comuns de gerenciamento de serviço programaticamente do Python. A classe **ServiceManagementService** no [SDK do Azure para Python](https://github.com/Azure/azure-sdk-for-python) suporta acesso através de programação para grande parte da funcionalidade de relacionados a gerenciamento de serviços que está disponível no [portal clássico Azure] [ management-portal] (como **criar, atualizar e excluir os serviços de nuvem, implantações, serviços de gerenciamento de dados e máquinas virtuais**). Essa funcionalidade pode ser útil na criação de aplicativos que precisam de acesso de programação ao gerenciamento de serviço.

## <a name="WhatIs"> </a>o que é gerenciamento de serviço
A API de gerenciamento de serviço fornece acesso através de programação a muita a funcionalidade de gerenciamento de serviço disponível por meio do [portal clássico Azure][management-portal]. O SDK do Azure para Python permite que você gerencie suas contas de armazenamento e serviços de nuvem.

Para usar a API de gerenciamento de serviço, você precisa [criar uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceitos
O SDK do Azure para Python envolve a [API de gerenciamento de serviço do Azure][svc-mgmt-rest-api], que é uma API REST. Todas as operações de API são executadas sobre SSL e mutuamente autenticadas usando x. 509 v3 certificados. O serviço de gerenciamento pode ser acessado a partir de um serviço em execução no Azure ou diretamente pela Internet de qualquer aplicativo que possa enviar uma solicitação HTTPS e recebe uma resposta HTTPS.

## <a name="Installation"> </a>Instalação

Todos os recursos descritos neste artigo estão disponíveis no `azure-servicemanagement-legacy` pacote, que você pode instalar usando pip. Para obter mais detalhes sobre a instalação (por exemplo, se você estiver começando a usar o Python), consulte este artigo: [Instalando Python e o SDK do Azure](../python-how-to-install.md)

## <a name="Connect"> </a>Como: conectar-se ao gerenciamento de serviço
Para conectar o ponto de extremidade do serviço gerenciamento, você precisará de sua ID do Azure assinatura e um certificado de gerenciamento válido. Você pode obter sua ID de assinatura por meio do [portal clássico Azure][management-portal].

> [AZURE.NOTE] Desde o SDK do Azure para Python v0.8.0, agora é possível usar certificados criados com OpenSSL quando em execução no Windows.  Requer Python 2.7.4 ou posterior. Recomendamos que os usuários usem OpenSSL em vez de. pfx, desde suporte para. pfx certificados provavelmente serão removidos no futuro.

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de gerenciamento no Mac/Windows/Linux (OpenSSL)
Você pode usar [OpenSSL](http://www.openssl.org/) para criar o seu certificado de gerenciamento.  Você realmente precisa criar dois certificados, um para o servidor (um `.cer` arquivo) e outra para o cliente (uma `.pem` arquivo). Para criar o `.pem` de arquivos, executar:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o `.cer` certificado, executar:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obter mais informações sobre certificados Azure, consulte [Visão geral de certificados para serviços de nuvem do Azure](./cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros de OpenSSL, consulte a documentação em [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Depois que você criou esses arquivos, você precisará carregar o `.cer` arquivo Azure via a ação "Carregamento" da guia "Configurações" do [Azure portal clássico][management-portal], e você precisa tomar nota onde você salvou o `.pem` arquivo.

Depois de você obteve sua ID de assinatura, criou um certificado e carregado o `.cer` arquivo no Azure, você pode conectar-se para o ponto de extremidade do Azure gerenciamento passando a id da assinatura e o caminho para o `.pem` arquivo para **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gerenciamento no Windows (MakeCert)

Você pode criar um certificado autoassinado gerenciamento em seu computador usando `makecert.exe`.  Abra um **prompt de comando do Visual Studio** como **administrador** e use o seguinte comando, substituindo *AzureCertificate* com o nome do certificado que você gostaria de usar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o `.cer` de arquivo e instala-lo no repositório de certificados **pessoais** . Para obter mais detalhes, consulte [Visão geral de certificados para serviços de nuvem do Azure](./cloud-services-certs-create.md).

Depois que você criou o certificado, você precisará carregar o `.cer` arquivo Azure via a ação "Carregamento" da guia "Configurações" do [Azure portal clássico][management-portal].

Depois de você obteve sua ID de assinatura, criou um certificado e carregado o `.cer` arquivo no Azure, você pode conectar-se para o ponto de extremidade do Azure gerenciamento passando a id da assinatura e o local do certificado em seu armazenamento de certificado **pessoal** para **ServiceManagementService** (novamente, substitua *AzureCertificate* com o nome do seu certificado):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um objeto **ServiceManagementService** . A classe **ServiceManagementService** é a classe primária usada para gerenciar os serviços do Azure.

## <a name="ListAvailableLocations"> </a>Como: lista locais disponíveis

Para listar os locais disponíveis para serviços de hospedagem, use o **lista\_locais** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando você cria um serviço de nuvem ou serviço de armazenamento que você precisará fornecer um local válido. O **lista\_locais** método sempre retorna uma lista atualizada dos locais disponíveis no momento. Até o momento, os locais disponíveis são:

- Europa Ocidental
- Norte da Europa
- Sudeste Asiático
- Da Ásia Oriental
- Centro dos EUA
- Centro Norte dos EUA
- Centro Sul dos EUA
- Oeste EUA
- Leste EUA
- Japão Leste
- Japão Oeste
- Brasil Sul
- Austrália Oriental
- Austrália Sudeste

## <a name="CreateCloudService"> </a>Como: criar um serviço de nuvem

Quando você cria um aplicativo e executá-lo no Azure, o código e configuração juntas são chamados um Azure [serviço em nuvem][] (conhecido como um *serviço hospedado* em versões anteriores do Azure). O **criar\_hospedado\_service** método permite que você crie um novo serviço hospedado, fornecendo um nome de serviço hospedado (que deve ser exclusivo no Azure), um rótulo (automaticamente codificado como base64), uma descrição e um local.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Você pode listar todos os serviços hospedados para sua assinatura com o **lista\_hospedado\_serviços** método:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Se desejar obter informações sobre um determinado serviço hospedado, você poderá fazer isso passando o nome de serviço hospedado para o **obter\_hospedado\_service\_propriedades** método:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Após ter criado um serviço na nuvem, você pode implantar seu código Service com a **criar\_implantação** método.

## <a name="DeleteCloudService"> </a>Como: excluir um serviço de nuvem

Você pode excluir um serviço na nuvem, passando o nome de serviço para o **Excluir\_hospedado\_service** método:

    sms.delete_hosted_service('myhostedservice')

Antes de excluir um serviço, todas as implantações do serviço devem ser excluídas primeiro. (Consulte [como: excluir uma implantação](#DeleteDeployment) para obter detalhes.)

## <a name="DeleteDeployment"> </a>Como: excluir uma implantação

Para excluir uma implantação, use o **Excluir\_implantação** método. O exemplo a seguir mostra como excluir uma implantação denominada `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Como: criar um serviço de armazenamento

Um [serviço de armazenamento](../storage/storage-create-storage-account.md) fornece acesso a Azure [Blobs](../storage/storage-python-how-to-use-blob-storage.md), [tabelas](../storage/storage-python-how-to-use-table-storage.md)e [filas](../storage/storage-python-how-to-use-queue-storage.md). Para criar um serviço de armazenamento, você precisa de um nome para o serviço (entre 3 e 24 caracteres minúsculos e exclusivo dentro do Azure), uma descrição, um rótulo (até 100 caracteres, automaticamente codificados como base64) e um local. O exemplo a seguir mostra como criar um serviço de armazenamento especificando um local.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Observação no exemplo anterior que o status do **criar\_armazenamento\_conta** operação pode ser recuperada passando o resultado retornado por **criar\_armazenamento\_conta** para o **obter\_operação\_status** método.  

Você pode listar suas contas de armazenamento e suas propriedades com o **lista\_armazenamento\_contas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Como: excluir um serviço de armazenamento

Você pode excluir um serviço de armazenamento, passando o nome de serviço de armazenamento para o **Excluir\_armazenamento\_conta** método. Excluindo um serviço de armazenamento exclui todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Como: lista de sistemas operacionais disponíveis

Para listar os sistemas operacionais que estão disponíveis para serviços de hospedagem, use o **lista\_operacional\_sistemas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Como alternativa, você pode usar o **lista\_operacional\_sistema\_famílias** método, que os sistemas operacionais pela família de grupos:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Como: criar uma imagem de sistema operacional

Para adicionar uma imagem de sistema operacional para o repositório de imagem, use o **Adicionar\_SO\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Para listar as imagens de sistema operacional que estão disponíveis, use o **lista\_SO\_imagens** método. Ele inclui todas as imagens de plataforma e imagens de usuário:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Como: excluir uma imagem de sistema operacional

Para excluir uma imagem de usuário, use o **Excluir\_SO\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Como: criar uma máquina virtual

Para criar uma máquina virtual, primeiro é necessário criar um [serviço na nuvem](#CreateCloudService).  Em seguida, crie a implantação de máquina virtual usando o **criar\_virtual\_máquina\_implantação** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Como: excluir uma máquina virtual

Para excluir uma máquina virtual, primeiro você excluir a implantação usando o **Excluir\_implantação** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço de nuvem pode ser excluído usando a **Excluir\_hospedado\_service** método:

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Como: Criar uma máquina Virtual a partir de uma imagem capturada Máquina Virtual

Para capturar uma imagem de máquina virtual, primeiro você chamar o **capturar\_máquina virtual\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Em seguida, para garantir que você com êxito capturar a imagem, use o **lista\_máquina virtual\_imagens** api e verifique se a imagem é exibida nos resultados:

    images = sms.list_vm_images()

Para criar para concluir a máquina virtual usando a imagem capturada, use o **criar\_virtual\_máquina\_implantação** método como antes, mas desta vez passar o vm_image_name em vez disso

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Para saber mais sobre como capturar uma máquina Virtual Linux, consulte [como capturar uma máquina Virtual Linux.](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

Para saber mais sobre como capturar uma máquina Virtual Windows, consulte [como capturar uma máquina Virtual Windows.](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"> </a>Próximos passos

Agora que você aprendeu os conceitos básicos do gerenciamento de serviços, você pode acessar a [documentação de referência de API concluída para o SDK do Azure Python](http://azure-sdk-for-python.readthedocs.org/) e executar tarefas complexas facilmente para gerenciar o aplicativo de python.

Para obter mais informações, consulte o [Python Developer Center](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[serviço de nuvem]:/services/cloud-services/

