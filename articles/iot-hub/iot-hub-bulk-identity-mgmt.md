<properties
 pageTitle="Importação e exportação de identidades de dispositivo do IoT Hub | Microsoft Azure"
 description="Conceitos e .NET trechos para gerenciamento de massa de identidades de dispositivo IoT Hub de código"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

# <a name="bulk-management-of-iot-hub-device-identities"></a>Em massa de gerenciamento de identidades de dispositivo do IoT Hub

Cada hub IoT tem um registro de identidade do dispositivo que você pode usar para criar recursos de cada dispositivo no serviço, como uma fila que contenha mensagens de nuvem para dispositivo em trânsito. O registro do dispositivo de identidade também permite controlar o acesso a pontos de extremidade voltados para o dispositivo. Este artigo descreve como importar e exportar identidades de dispositivos em massa para e de um registro de identidade do dispositivo.

Importar e exportar operações ocorrem no contexto de *trabalhos* que permitem executar operações de serviço de massa em relação a um hub IoT.

A classe **RegistryManager** inclui as **ExportDevicesAsync** e métodos de **ImportDevicesAsync** que usam a estrutura de **trabalho** . Esses métodos permitem que você exportar, importar e sincronizar na íntegra um registro do dispositivo de hub IoT.

## <a name="what-are-jobs"></a>O que são trabalhos?

Operações de registro do dispositivo identidade usam o sistema de **trabalho** quando a operação:

*  Tem um potencialmente muito tempo de execução em comparação com operações de tempo de execução padrão, ou
*  Retorna uma grande quantidade de dados para o usuário.

Nesses casos, em vez de uma única chamada de API aguardando ou bloqueando no resultado da operação, a operação assíncrona cria um **trabalho** para que o hub IoT. A operação, em seguida, retorna imediatamente um objeto de **JobProperties** .

O trecho de código c# seguinte mostra como criar um trabalho de exportação:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Em seguida, você pode usar a classe **RegistryManager** para consultar o estado do **trabalho** usando os metadados **JobProperties** retornados.

O trecho de código c# seguinte mostra como poll cada cinco segundos para ver se o trabalho terminou em execução:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Dispositivos de exportação

Use o método **ExportDevicesAsync** para exportar na íntegra um registro do dispositivo de hub IoT a um contêiner de blob do [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) usando uma [Assinatura de acesso compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

Este método permite que você crie backups confiáveis de suas informações de dispositivo em um contêiner de blob que você controle.

O método **ExportDevicesAsync** requer dois parâmetros:

*  Uma *cadeia de caracteres* que contém um URI de um contêiner de blob. Esse URI deve conter um token SAS que concede acesso de gravação para o contêiner. O trabalho cria um blob de bloco neste contêiner para armazenar os dados de dispositivo de exportação serializado. O token SAS deve incluir estas permissões:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  *Booliano* que indica se você deseja excluir chaves de autenticação dos seus dados de exportação. Se **false**, chaves são incluídas em exportação de autenticação de saída; Caso contrário, as teclas são exportadas como **Nulo**.

O trecho de código c# seguinte mostra como iniciar um trabalho de exportação que inclui chaves de autenticação do dispositivo dos dados de exportação e, em seguida, poll para conclusão:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

O trabalho armazena sua saída no contêiner de blob fornecida como um blob de bloco com o nome **Devices**. Os dados de saída consistem em JSON serializado dados do dispositivo, com um dispositivo por linha.

O exemplo a seguir mostra os dados de saída:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se precisar de acesso a dados no código, você pode facilmente desserializar esses dados usando a classe **ExportImportDevice** . O trecho de código c# seguinte mostra como ler as informações de dispositivo que foi exportadas anteriormente para um blob de bloco:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Você também pode usar o método **GetDevicesAsync** da classe **RegistryManager** para buscar uma lista dos seus dispositivos. No entanto, essa abordagem tem um limite rígido de 1000 no número de objetos de dispositivo que são retornados. O caso de uso esperado para o método **GetDevicesAsync** é para cenários de desenvolvimento depuração de auxílio e não é recomendado para cargas de trabalho de produção.

## <a name="import-devices"></a>Dispositivos de importação

O método **ImportDevicesAsync** da classe **RegistryManager** permite que você execute operações de importação e sincronização em massa de um registro do dispositivo de hub IoT. Como o método de **ExportDevicesAsync** , o método **ImportDevicesAsync** usa a estrutura de **trabalho** .

Tome cuidado usando o método de **ImportDevicesAsync** porque além de provisionamento novos dispositivos no registro de identidade do dispositivo, ele também pode atualizar e excluir dispositivos existentes.

> [AZURE.WARNING]  Uma operação de importação não pode ser desfeita. Sempre faça backup de seus dados existentes usando o método de **ExportDevicesAsync** para outro contêiner de blob antes de fazer alterações em massa seu registro de identidade do dispositivo.

O método **ImportDevicesAsync** usa dois parâmetros:

*  Uma *cadeia de caracteres* que contém um URI de um contêiner de blob do [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) para usar como *entrada* para o trabalho. Esse URI deve conter um token SAS que concede acesso de leitura para o contêiner. Este contêiner deve conter um blob com o nome **Devices** que contém os dados de dispositivo serializado para importar para o seu registro de identidade do dispositivo. Os dados de importação devem conter informações de dispositivo no mesmo formato JSON que o trabalho de **ExportImportDevice** usa quando cria um blob **Devices** . O token SAS deve incluir estas permissões:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  Uma *cadeia de caracteres* que contém um URI de um contêiner de blob do [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) para usar como *saída* do trabalho. O trabalho cria um blob de bloco neste contêiner para armazenar qualquer informação de erro da importação **trabalho**concluída. O token SAS deve incluir estas permissões:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Os dois parâmetros podem apontar para o mesmo contêiner de blob. Os parâmetros separados simplesmente habilitar mais controle sobre seus dados como o contêiner de saída exige permissões adicionais.

O trecho de código c# seguinte mostra como iniciar um trabalho de importação:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>Comportamento de importação

Você pode usar o método **ImportDevicesAsync** para executar as seguintes operações em massa no registro de identidade do dispositivo:

-   Registro em massa de novos dispositivos
-   Exclusões de massa dos dispositivos existentes
-   Em massa alterações de status (ativar ou desativar dispositivos)
-   Atribuição de massa de novas chaves de autenticação do dispositivo
-   Em massa autonova geração de chaves de autenticação do dispositivo

Você pode executar qualquer combinação das operações anteriores dentro de uma única chamada **ImportDevicesAsync** . Por exemplo, você pode registrar novos dispositivos e excluir ou atualizar dispositivos existentes ao mesmo tempo. Quando usado juntamente com o método de **ExportDevicesAsync** , você pode migrar completamente todos os seus dispositivos de um hub de IoT para outro.

Use a propriedade opcional **importMode** dos dados de serialização de importação para cada dispositivo para controlar o importação processo por dispositivo. A propriedade **importMode** tem as seguintes opções:

| importMode |  Descrição |
| -------- | ----------- |
| **createOrUpdate** | Se não existir um dispositivo com a **id**de especificada, ele registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes são substituídas com os dados de entrada fornecidos sem se preocupar com o valor de **ETag** . |
| **criar** | Se não existir um dispositivo com a **id**de especificada, ele registrado recentemente. <br/>Se o dispositivo já existir, um erro é escrito o arquivo de log. |
| **Atualizar** | Se já existe um dispositivo com a **id**de especificada, as informações existentes são substituídas com os dados de entrada fornecidos sem se preocupar com o valor de **ETag** . <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **updateIfMatchETag** | Se já existe um dispositivo com a **id**de especificada, as informações existentes serão substituídas com os dados de entrada fornecidos somente se houver uma correspondência **ETag** . <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se houver uma incompatibilidade de **ETag** , um erro é escrito o arquivo de log. |
| **createOrUpdateIfMatchETag** | Se não existir um dispositivo com a **id**de especificada, ele registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas com os dados de entrada fornecidos somente se houver uma correspondência **ETag** . <br/>Se houver uma incompatibilidade de **ETag** , um erro é escrito o arquivo de log. |
| **Excluir** | Se já existe um dispositivo com a **id**de especificada, ele será excluído sem se preocupar com o valor de **ETag** . <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **deleteIfMatchETag** | Se já existe um dispositivo com a **id**de especificada, ele será excluído somente se houver uma correspondência **ETag** . Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se houver uma incompatibilidade de ETag, um erro é escrito o arquivo de log. |

> [AZURE.NOTE] Se os dados de serialização não explicitamente definam um sinalizador de **importMode** para um dispositivo, o padrão é **createOrUpdate** durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importar exemplo de dispositivos – em massa provisionamento de dispositivo 

O exemplo de código c# seguir ilustra como gerar várias identidades do dispositivo que:

- Inclua chaves de autenticação.
- Gravar essas informações de dispositivo um blob de bloco.
- Importe os dispositivos para o registro do dispositivo de identidade.

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemplo de dispositivos de importação – exclusão em massa

O exemplo de código a seguir mostra como excluir os dispositivos que você adicionou usando o código de exemplo anterior:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="getting-the-container-sas-uri"></a>Obtendo o contêiner SAS URI


O exemplo de código a seguir mostra como gerar um [URI SAS](../storage/storage-dotnet-shared-access-signature-part-2.md) com ler, gravar e excluir permissões para um contêiner de blob:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a executar operações em massa contra o registro de identidade do dispositivo em um hub IoT. Siga estes links para saber mais sobre como gerenciar Azure IoT Hub:

- [Métricas de uso][lnk-metrics]
- [Operações de monitoramento][lnk-monitor]

Para explorar os recursos do IoT Hub, consulte:

- [Guia Desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md