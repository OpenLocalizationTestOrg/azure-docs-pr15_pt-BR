A tabela a seguir descreve cada uma das principais cotas, limites, padrões e limitações no Agendador do Azure.

|Recurso|Descrição de limite|
|---|---|
|**Tamanho de trabalho**|O tamanho máximo do trabalho é 16K. Se uma COLOCAÇÃO ou um PATCH resulta em um trabalho maior do que esses limites, será retornado um código de status 400 Solicitação incorreta.|
|**Tamanho da solicitação de URL**|Tamanho máximo da solicitação URL é 2048 caracteres.|
|**Tamanho do cabeçalho de agregação**|Tamanho máximo do cabeçalho agregada é 4096 caracteres.|
|**Contagem de cabeçalho**|Contagem de cabeçalho máximo é 50 cabeçalhos.|
|**Tamanho do corpo**|Tamanho máximo do corpo é 8192 caracteres.|
|**Intervalo de recorrência**|Intervalo de recorrência máximo é 18 meses.|
|**Hora de início da hora**|Máximo "hora de início da hora" é 18 meses.|
|**Histórico de trabalho**|Corpo de resposta máximo armazenado no histórico de trabalho é 2048 bytes.|
|**Frequência**|A cota de frequência máximo padrão é 1 hora em um conjunto de trabalho gratuito e 1 minuto em um conjunto de trabalho padrão. A frequência máx é configurável em um conjunto de trabalho para ser menor que o máximo. Todos os trabalhos no conjunto de trabalho limitam-se o valor definido no conjunto de trabalho. Se você tentar criar um trabalho com uma frequência mais alta que a frequência máxima no conjunto de trabalho solicitação falhará com um código de status 409 Conflito.|
|**Trabalhos**|A cota de trabalhos máximo padrão é 5 trabalhos em um conjunto de trabalho gratuito e 50 trabalhos em um conjunto de trabalho padrão. O número máximo de trabalhos é configurável em um conjunto de trabalho. Todos os trabalhos no conjunto de trabalho limitam-se o valor definido no conjunto de trabalho. Se você tentar criar mais trabalhos que a cota de trabalhos máximo, a solicitação falhará com um código de status 409 Conflito.|
|**Retenção de histórico de trabalho**|Histórico de trabalho é mantido por até 2 meses ou até as última 1000 execuções.|
|**Retenção de trabalhos com defeito e concluído**|Trabalhos com defeito e concluídos são mantidos por 60 dias.|
|**Tempo limite**|Não há um limite de solicitação (não configurável) estático de 60 segundos para ações de HTTP. Para mais operações em execução, siga protocolos assíncronos HTTP; Por exemplo, retornar um 202 imediatamente, mas continuar a trabalhar no plano de fundo.|
