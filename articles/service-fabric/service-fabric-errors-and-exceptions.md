<properties
   pageTitle="Exceções FabricClient comuns lançadas | Microsoft Azure"
   description="Descreve as exceções e erros que podem ser lançados pelas APIs FabricClient ao executar o aplicativo e as operações de gerenciamento de cluster comuns."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceções e erros ao trabalhar com as APIs FabricClient comuns
As APIs [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permitem que os administradores de cluster e aplicativo executar tarefas administrativas em um aplicativo de serviço tecidos, serviço ou cluster. Por exemplo, implantação do aplicativo, atualização e remoção, verificar a integridade de um cluster ou um serviço de teste. Os desenvolvedores de aplicativos e administradores de cluster podem usar as APIs FabricClient desenvolver ferramentas para gerenciar o cluster de estrutura de serviço e os aplicativos.

Há vários tipos de operações que podem ser realizadas usando FabricClient.  Cada método pode lançar exceções para erros devido a entrada incorreta, erros de tempo de execução ou problemas de infraestrutura temporárias.  Consulte a documentação de referência de API para encontrar quais exceções são lançadas por um método específico. Há algumas exceções, no entanto, que podem ser lançadas por várias APIs [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) diferentes. A tabela a seguir lista as exceções que são comuns entre as APIs FabricClient.

|Exceção| Lançada quando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|O objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) está em um estado fechado. Descarte do objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) está usando e criar um novo objeto de [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) . |
|[System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|A operação expirou. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) é retornado quando a operação leva mais de MaxOperationTimeout para ser concluída.|
|[System. UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|A verificação de acesso para a operação falhou. E_ACCESSDENIED será retornado.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Ocorreu um erro de tempo de execução ao executar a operação. Qualquer um dos métodos FabricClient potencialmente podem lançar [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx), a propriedade de [código de erro](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica a causa exata de exceção. Códigos de erro são definidos na enumeração [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) .|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|A operação falhou devido a uma condição de erro temporária algum tipo de. Por exemplo, uma operação pode falhar porque um quorum de réplicas temporariamente não está acessível. Exceções temporárias correspondem às operações com falha que podem ser recuperadas.|

Alguns erros [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) comuns que podem ser retornados em um [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Erro| Condição|
|---------|:-----------|
|CommunicationError|Um erro de comunicação causou a operação falhar, repita a operação.|
|InvalidCredentialType|O tipo de credencial é inválido.|
|InvalidX509FindType|O X509FindType é inválido.|
|InvalidX509StoreLocation|O X509 local de armazenamento é inválido.|
|InvalidX509StoreName|O X509 store nome é inválido.|
|InvalidX509Thumbprint|O X509 sequência de impressão digital de certificado é inválida.|
|InvalidProtectionLevel|O nível de proteção é inválido.|
|InvalidX509Store|O X509 repositório de certificados não pode ser aberto.|
|InvalidSubjectName|O nome de assunto é inválido.|
|InvalidAllowedCommonNameList|O formato da cadeia de caracteres de lista Nome comum é inválido. Ele deve ser uma lista separada por vírgulas.|
