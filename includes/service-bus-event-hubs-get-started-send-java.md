## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para Hubs de evento

A biblioteca de cliente Java para Hubs de evento está disponível para uso em projetos Maven do [Repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)e pode ser referenciada usando a seguinte declaração de dependência dentro de seu arquivo de projeto Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Para diferentes tipos de ambientes de compilação, você pode obter explicitamente mais recentes disponibilizados arquivos JAR do [Repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ou do [ponto de distribuição de lançamento no GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Para um fornecedor de evento simples, importe o pacote de *com.microsoft.azure.eventhubs* para as classes de cliente Hubs de evento e o pacote de *com.microsoft.azure.servicebus* para classes de utilitário como exceções comuns que são compartilhadas com o cliente de mensagens de barramento de serviço do Azure. 

Para o exemplo a seguir, primeiro crie um novo projeto de Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. A classe será chamada ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Substitua os valores usados quando você criou o Hub de evento namespace e nomes de Hub de evento.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Em seguida, crie um evento singular transformando uma cadeia de caracteres em sua codificação de byte UTF-8. Em seguida, podemos criar uma nova instância de cliente do evento Hubs da cadeia de conexão e enviar a mensagem.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 
