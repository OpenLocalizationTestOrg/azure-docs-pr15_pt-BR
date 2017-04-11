<properties
    pageTitle="Passo a passo de arquivo morto de Hubs de evento Azure | Microsoft Azure"
    description="Exemplo que usa o SDK do Azure Python para demonstrar usando o recurso de arquivo morto de Hubs de evento."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Explicação passo a passo de arquivamento de Hubs de evento: Python

Arquivamento de Hubs de evento é um novo recurso de Hubs de evento que permite que você fornecer automaticamente os dados de fluxo no seu Hub de evento para uma conta de armazenamento de Blob do Azure de sua escolha. Isso facilita executar o fluxo de dados em tempo real de processamento em lotes. Este artigo descreve como usar o evento Hubs arquivamento com Python. Para obter mais informações sobre o arquivamento de Hubs de evento, consulte o [artigo de visão geral](event-hubs-archive-overview.md).

Este exemplo usa o SDK do Azure Python para demonstrar usando o recurso de arquivamento. O sender.py envia simulada telemetria ambiental para Hubs de evento no formato JSON. O Hub de evento está configurado para usar o recurso de arquivo morto para gravar esses dados para armazenamento em lotes de blob. O archivereader.py, em seguida, lê esses blobs e cria um arquivo de acréscimo por dispositivo e grava os dados em arquivos. csv.

O que será obtido

1.  Criar uma conta de armazenamento de Blob do Azure e um contêiner de blob dentro dela, usando o portal do Azure

2.  Criar um namespace Hub de evento, usando o portal do Azure

3.  Criar um Hub de evento com o recurso de arquivo morto habilitado, usando o portal do Azure

4.  Enviar dados para o Hub de evento com um script de Python

5.  Ler os arquivos do arquivo e processá-los com outro script de Python

Pré-requisitos

1.  Python 2.7.x

2.  Uma assinatura do Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

1.  Faça logon no [portal do Azure][].

2.  No painel de navegação esquerdo do portal, clique em novo e em seguida, clique em dados + armazenamento e clique em conta de armazenamento.

3.  Preencha os campos na lâmina de conta de armazenamento e clique em **criar**.

    ![][1]

4.  Depois que você vê a mensagem **Implantações foi bem-sucedida** , clique em nova conta de armazenamento e na lâmina **Essentials** clique **Blobs**. Quando a lâmina de **serviço Blob** é aberta, clique em **+ contêiner** na parte superior. Nomeie o contêiner **arquivamento**e, em seguida, feche a lâmina **Blob serviço** .

5.  Clique **nas teclas de acesso** na lâmina esquerda e copie o nome da conta de armazenamento e o valor de **CHAVE1**. Salve esses valores no bloco de notas ou algum outro local temporário.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script de Python para enviar eventos para seu Hub de evento

1.  Abra o editor de Python favorito, como o [Visual Studio código][].

2.  Crie um script chamado **sender.py**. Esse script enviará 200 eventos a seu Hub de evento. Eles são simples leituras ambientais enviadas em JSON.

3.  Cole o seguinte código no sender.py:

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Atualize o código anterior para usar seu nome de namespace e valores-chave obtido quando você criou o namespace Hubs de evento.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Criar um script de Python para ler seus arquivos de arquivamento

1.  Preencha a lâmina e clique em **criar**.

2.  Crie um script chamado **archivereader.py**. Esse script lerá os arquivos mortos e criar um arquivo por dispositivo para gravar os dados apenas para esse dispositivo.

3.  Cole o seguinte código no archivereader.py:

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Certifique-se colar os valores apropriados para o seu nome de conta de armazenamento e chave na chamada para `startProcessing`.

## <a name="run-the-scripts"></a>Executar os scripts

1.  Abra um prompt de comando que tenha Python no seu caminho e execute esses comandos para instalar pacotes de pré-requisito Python:

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Se você tiver uma versão anterior do armazenamento do azure ou azure você pode precisar usar o **– Atualizar** opção

    Você também precisa executar o seguinte (não é necessário na maioria dos sistemas):

    ```
    pip install cryptography
    ```

2.  Altere o diretório para onde você salvou sender.py e archivereader.py e executar este comando:

    ```
    start python sender.py
    ```
    
    Isso inicia um novo processo de Python para executar o remetente.

3. Agora, aguarde alguns minutos para que o arquivo seja executado. Digite o seguinte comando para sua janela de comando original:

    ```
    python archivereader.py
    ```

Este processador de arquivamento usa o diretório local para baixar todos os blobs de conta/contêiner de armazenamento. Ele processará qualquer que não estão vazias e gravar os resultados como arquivos. csv para o diretório local.

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre o evento Hubs visitando os links a seguir:

- [Visão geral de Hubs de evento arquivar][]
- Um [aplicativo de exemplo que usa o evento Hubs][]completo.
- A amostra de [escala processamento de eventos com Hubs de evento][] .
- [Visão geral de Hubs de evento][]
 

[Portal do Azure]: https://portal.azure.com/
[Visão geral de Hubs de evento arquivar]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Código do Visual Studio]: https://code.visualstudio.com/
[Visão geral de Hubs de evento]: event-hubs-overview.md
[aplicativo de exemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar o processamento de eventos com Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
