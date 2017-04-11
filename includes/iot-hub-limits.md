A tabela a seguir lista os limites associados com os níveis de serviço diferente (S1, S2, S3, F1). Para obter informações sobre o custo de cada *unidade* em cada camada, consulte [IoT Hub preços](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 padrão | S2 padrão | S3 padrão | F1 livre |
| -------- | ----------- | ----------- | ----------- | ------- |
| Mensagens/dia | 400.000 | 6,000,000   | 300,000,000 | 8.000   |
| Unidades máximas | 200    | 200         | 200         | 1       |

> [AZURE.NOTE] Se você prevê usando mais de 200 unidades com um hub de camada S1 ou S2 ou S3, entre em contato com o suporte da Microsoft.

A tabela a seguir lista os limites que se aplicam aos recursos de IoT Hub:

| Recurso | Limite |
| -------- | ----- |
| Máximo pago hubs IoT por assinatura Azure | 10 |
| Máximos hubs IoT gratuitos por assinatura Azure | 1 |
| Número máximo de identidades do dispositivo<br/>  retornado em uma única chamada | 1000 |
| Retenção de máximo do IoT Hub mensagens para mensagens de dispositivo à nuvem | 7 dias |
| Tamanho máximo de mensagem de dispositivo à nuvem | 256 KB |
| Tamanho máximo do lote de dispositivo à nuvem | 256 KB |
| Máximo de mensagens no lote de dispositivo à nuvem | 500 |
| Tamanho máximo de mensagem de nuvem para dispositivo | 64 KB |
| TTL máximo para mensagens de nuvem para dispositivo | 2 dias |
| Contagem de entrega máximo de nuvem para dispositivo <br/> mensagens | 100 |
| Contagem de entrega máximo para mensagens de comentários <br/> em resposta a uma mensagem de nuvem para dispositivo | 100 |
| TTL máximo mensagens de comentários no <br/> resposta a uma mensagem de nuvem para dispositivo | 2 dias |

> [AZURE.NOTE] Se precisar de mais de 10 hubs IoT pagos em uma assinatura do Azure, entre em contato com o suporte da Microsoft.

O serviço de IoT Hub acelera solicitações quando as cotas a seguintes são excedidas:

| Aceleração | Valor por hub |
| -------- | ------------- |
| Operações de registro de identidade <br/> (criar, recuperar, lista, atualizar, excluir), <br/> importação/exportação individual ou em massa | min/5000/unidade (para S3) <br/> 100/min/unidade (S1 e S2). |
| Conexões de dispositivo | sec/6000/unidade (para S3), 120/seg/unidade (S2), s/12/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envia dispositivo à nuvem | sec/6000/unidade (para S3), 120/seg/unidade (S2), s/12/unidade (para S1). <br/>Mínimo de 100/seg. |
| Envia de nuvem para dispositivo | min/5000/unidade (para S3), 100/min/unidade (S1 e S2). |
| Nuvem para dispositivo recebe | min/50000/unidade (para S3), 1000/min/unidade (S1 e S2). |
| Operações de carregamento de arquivo | arquivo de 5000 carregar min/notificações/unidade (para S3), carregamento de arquivo 100 notificações/min/unidade (para S1 e S2). <br/> 10000 URIs de SAS pode ser check-out para uma conta de armazenamento do Azure ao mesmo tempo.<br/> 10 SAS URIs/dispositivo pode ser check-out de uma só vez. |
