<properties
    pageTitle="Suporte a recurso de entre origens compartilhamento (CORS) | Microsoft Azure"
    description="Saiba como habilitar o suporte de CORS para os serviços de armazenamento do Microsoft Azure."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Recurso de entre origens compartilhamento (CORS) suporte para os serviços de armazenamento do Azure

Começando com a versão 2013-08-15, os serviços de armazenamento do Azure suportam entre origens recurso Compartilhamento (CORS) para os serviços do Blob, tabela, fila e arquivo. CORS é um recurso HTTP que permite que um aplicativo da web em execução em um domínio acessar recursos em outro domínio. Navegadores da Web implementam uma restrição de segurança conhecida como [diretiva de mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy) que impede a uma página da web de chamada APIs em um domínio diferente; CORS fornece uma maneira segura para permitir que um domínio (o domínio de origem) para chamar APIs em outro domínio. Consulte a [especificação de CORS](http://www.w3.org/TR/cors/) para obter detalhes sobre CORS.

Você pode definir regras CORS individualmente para cada um dos serviços de armazenamento, ligando para [Definir propriedades de serviço de Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx)e [Definir propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx). Depois que você definir as regras CORS para o serviço, e em seguida, uma solicitação de adequadamente autenticada feita contra o serviço de um domínio diferente será avaliada para determinar se ele é permitido de acordo com as regras que você especificou.

>[AZURE.NOTE] Observe que CORS não é um mecanismo de autenticação. Qualquer solicitação feita em relação a um recurso de armazenamento quando CORS está habilitado deve possuir uma assinatura de autenticação adequada, ou deve ser feita em relação a um recurso público.

## <a name="understanding-cors-requests"></a>Noções básicas sobre solicitações CORS

Uma solicitação CORS de um domínio de origem pode consistir em duas solicitações separadas:

- Uma solicitação de comprovação, que consulta as restrições de CORS impostas pelo serviço. A solicitação de comprovação é necessária, a menos que o método de solicitação é um [método simples](http://www.w3.org/TR/cors/), significando GET, cabeça ou POSTAGEM.

- A solicitação real, feita contra o recurso desejado.

### <a name="preflight-request"></a>Solicitação de comprovação

As consultas de solicitação comprovação as restrições de CORS que foram estabelecidas para o serviço de armazenamento pelo proprietário da conta. O navegador da web (ou outro agente de usuário) envia uma solicitação de opções que inclui os cabeçalhos de solicitação, domínio método e origem. O serviço de armazenamento avalia a operação pretendida com base em um conjunto pré-configurado de regras CORS que especificar quais cabeçalhos de solicitação, métodos de solicitação e domínios de origem podem ser especificados em uma solicitação real em relação a um recurso de armazenamento.

Se CORS estiver habilitado para o serviço e há uma regra CORS que corresponda à solicitação de comprovação, o serviço responde com código de status 200 (Okey) e inclui os cabeçalhos de controle de acesso necessários na resposta.

Se CORS não está habilitado para o serviço ou nenhuma regra CORS corresponde a solicitação de comprovação, o serviço responderá com código de status 403 (proibido).

Se a solicitação de opções não contiver os cabeçalhos CORS necessários (os cabeçalhos de origem e acesso-controle-método de solicitação), o serviço responderá com código de status 400 (solicitação incorreta).

Observe que uma solicitação de comprovação é avaliada contra o serviço (Blob, fila e tabela) e não o recurso solicitado. O proprietário da conta deve ter habilitada CORS como parte das propriedades de serviço de conta, para que a solicitação tenha êxito.

### <a name="actual-request"></a>Solicitação real

Depois que a solicitação de comprovação é aceita e a resposta é retornada, o navegador enviará a solicitação real com o recurso de armazenamento. O navegador nega a real solicitação imediatamente se a solicitação de comprovação é rejeitada.

A solicitação real será tratada como normal solicitação ao serviço de armazenamento. A presença do cabeçalho da origem indica que a solicitação é uma solicitação CORS e o serviço verificará as regras de CORS correspondência. Se uma correspondência for encontrada, os cabeçalhos de controle de acesso são adicionados à resposta e enviados de volta para o cliente. Se uma correspondência não for encontrada, os cabeçalhos de controle de acesso CORS não são retornados.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Habilitando CORS para os serviços de armazenamento do Azure

Regras de CORS são definidas no nível de serviço, então você precisa habilitar ou desabilitar CORS para cada serviço (Blob, fila e tabela) separadamente. Por padrão, CORS está desabilitado para cada serviço. Para habilitar CORS, você precisa definir as propriedades de serviço apropriado usando a versão 2013-08-15 ou posterior e adicionar regras de CORS nas propriedades de serviço. Para obter detalhes sobre como habilitar ou desabilitar CORS para um serviço e como definir regras CORS, consulte [Definir propriedades de serviço de Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx)e [Definir propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx).

Aqui está um exemplo de uma única regra CORS, especificado por meio de uma operação de definir propriedades de serviço:

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Cada elemento incluído na regra CORS é descrito a seguir:

- **AllowedOrigins**: os domínios de origem que têm permissão para fazer uma solicitação ao serviço de armazenamento via CORS. O domínio de origem é o domínio do qual se origina a solicitação. Observe que a origem deve ser uma correspondência exata de maiusculas e minúsculas com a origem que envia a idade de usuário para o serviço. Você também pode usar o caractere curinga ' *' para permitir que todos os domínios de origem fazer solicitações via CORS. No exemplo acima, os domínios [http://www.contoso.com](http://www.contoso.com) e [http://www.fabrikam.com](http://www.fabrikam.com) podem fazer solicitações contra o serviço usando CORS.

- **AllowedMethods**: os métodos (verbos de solicitação HTTP) que pode usar o domínio de origem para uma solicitação CORS. No exemplo acima, apenas solicitações de colocar e obter são permitidas.

- **AllowedHeaders**: os cabeçalhos de solicitação que o domínio de origem pode especificar na solicitação CORS. No exemplo acima, todos os cabeçalhos de metadados, começando com x-ms-metadados, x-ms-meta-alvo e x-ms-meta-abc são permitidos. Observe que o caractere curinga ' *' indica que qualquer início de cabeçalho com o prefixo especificado é permitido.

- **ExposedHeaders**: os cabeçalhos de resposta que podem ser enviados na resposta à solicitação de CORS e expostos pelo navegador para o emissor de solicitação. No exemplo acima, o navegador é instruções para expor qualquer começando de cabeçalho x-ms-meta.

- **MaxAgeInSeconds**: O tempo máximo que um navegador deve armazenar em cache as opções de comprovação solicitar.

Os serviços de armazenamento do Azure suportam especificando prefixados cabeçalhos para elementos **AllowedHeaders** tanto o **ExposedHeaders** . Para permitir que uma categoria de cabeçalhos, você pode especificar um prefixo comum para essa categoria. Por exemplo, especificando *x-ms-meta** como um cabeçalho prefixado estabelece uma regra que corresponderão todos os cabeçalhos que começam com x-ms-meta.

As seguintes limitações se aplicam às regras CORS:

- Você pode especificar até cinco regras CORS por serviço de armazenamento (Blob, tabela e fila).
- O tamanho máximo de todas as CORS regras configurações na solicitação, excluindo marcas XML, não deve exceder 2 KB.
- O comprimento de um cabeçalho permitido, cabeçalho exposto ou origin permitido não deve exceder 256 caracteres.
- Cabeçalhos permitidos e cabeçalhos expostos podem ser:
  - Cabeçalhos literais, onde o nome de cabeçalho exato é fornecido, como **x-ms-meta-processada**. Um máximo de 64 cabeçalhos literais pode ser especificado na solicitação.
  - Prefixo cabeçalhos, onde um prefixo do cabeçalho é fornecido, como o **x-ms-metadados***. Especifica um prefixo dessa maneira permite ou expõe qualquer cabeçalho que começa com o prefixo determinado. Um máximo de dois cabeçalhos prefixados pode ser especificado na solicitação.
- Os métodos (ou verbos HTTP) especificado no elemento **AllowedMethods** devem estar de acordo com os métodos suportados pelo serviço de armazenamento do Azure APIs. Métodos suportados são excluir, GET, cabeça, mala direta, POSTAGEM, opções e COLOCAÇÃO.

## <a name="understanding-cors-rule-evaluation-logic"></a>Noções básicas sobre lógica de avaliação de regra CORS

Quando um serviço de armazenamento recebe uma solicitação de comprovação ou real, ele será avaliado solicitação com base em regras CORS que ter estabelecido para o serviço via a operação de definir propriedades de serviço apropriada. Regras CORS são avaliadas na ordem em que elas foram definidas no corpo da solicitação da operação de definir propriedades de serviço.

Regras CORS são avaliadas da seguinte maneira:

1. Primeiro, o domínio de origem da solicitação é comparado os domínios listados para o elemento **AllowedOrigins** . Se o domínio de origem é incluído na lista ou todos os domínios permitidos com o caractere curinga ' *', em seguida, continua de avaliação de regras. Se o domínio de origem não for incluído, a solicitação falhará.

2. Em seguida, o método (ou verbo HTTP) da solicitação é comparado os métodos listados no elemento **AllowedMethods** . Se o método for incluído na lista, avaliação de regras continua; Caso contrário, a solicitação falhará.

3. Se a solicitação corresponde a uma regra no seu domínio de origem e seu método, essa regra está selecionada para a solicitação e nenhuma regra adicional é avaliada de processo. Antes da solicitação pode êxito, no entanto, qualquer cabeçalhos especificados na solicitação de são comparados os cabeçalhos listados no elemento **AllowedHeaders** . Se os cabeçalhos enviados não coincidem com os cabeçalhos permitidos, a solicitação falhará.

Desde que as regras são processadas na ordem em que eles estão presentes no corpo da solicitação, práticas recomendadas recomendam que você especificar as regras mais restritivas relacionadas com origens primeiro na lista, para que elas são avaliadas primeiro. Especifique as regras que são menos restritivas – por exemplo, uma regra para permitir todas as origens – no final da lista.

### <a name="example--cors-rules-evaluation"></a>Exemplo – CORS avaliação de regras

O exemplo a seguir mostra um corpo da solicitação parcial de uma operação definir regras CORS para os serviços de armazenamento. Consulte [Definir propriedades de serviço de Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx)e [Definir propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx) para obter detalhes sobre construindo a solicitação.

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

Em seguida, considere as solicitações CORS a seguir:

Solicitação||| Resposta||
---|---|---|---|---
**Método** |**Origem** |**Cabeçalhos de solicitação** |**Correspondência da regra** |**Resultado**
**COLOCAR** | http://www.contoso.com |x-ms-blob-tipo de conteúdo | Primeira regra |Sucesso
**Obter** | http://www.contoso.com| x-ms-blob-tipo de conteúdo | Segunda regra |Sucesso
**Obter** | http://www.contoso.com| x-ms-blob-tipo de conteúdo | Segunda regra | Falha

A primeira solicitação corresponde a primeira regra – as origens permitidas corresponde ao domínio de origem, o método corresponde os métodos permitidos e o cabeçalho corresponde os cabeçalhos permitidos – e portanto é bem sucedida.

A segunda solicitação não coincidir com a primeira regra porque o método não coincidir com os métodos permitidos. No entanto, ele, correspondem a segunda regra, para que ele é bem sucedida.

A solicitação de terceira corresponde a segunda regra em seu domínio de origem e método, não há mais regras são avaliadas. No entanto, o *cabeçalho x-ms-cliente-solicitação-id* não é permitido pela segunda regra, para que a solicitação falhar, apesar do fato de que a semântica da terceira regra seria permitido ela tenha êxito.

>[AZURE.NOTE] Embora este exemplo mostra uma regra de menos restritiva antes de um mais restritivo, em geral a prática recomendada é lista as regras mais restritivas primeiro.

## <a name="understanding-how-the-vary-header-is-set"></a>Noções básicas sobre como o cabeçalho de variar é definido

*Variar* cabeçalho é um cabeçalho HTTP/1.1 padrão consiste em um conjunto de campos de cabeçalho de solicitação que informar o agente de navegador ou usuário sobre os critérios que foram selecionados pelo servidor para processar a solicitação. *Variar* cabeçalho é usado principalmente para armazenamento em cache por proxies, navegadores e CDNs, que usá-lo para determinar como a resposta deve ser armazenados em cache. Para obter detalhes, consulte a especificação para o [cabeçalho de variar](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando o navegador ou outro agente de usuário armazenará a resposta de uma solicitação CORS, o domínio de origem está no cache como a origem permitida. Quando um segundo domínio envia a mesma solicitação para um recurso de armazenamento enquanto o cache estiver ativo, o agente de usuário recupera o domínio de origem em cache. O segundo domínio não coincidir com o domínio em cache, para que a solicitação falhará quando ele teria êxito caso contrário. Em alguns casos, o armazenamento do Azure define o cabeçalho de variar a **origem** para instruir o agente de usuário para enviar a solicitação CORS subsequente para o serviço, quando o domínio solicitando difere a origem em cache.

Armazenamento do Azure define o cabeçalho de *variar* como **origem** para solicitações GET/cabeça reais nos seguintes casos:

- Quando a origem de solicitação corresponde exatamente a origem permitida definida por uma regra CORS. Para ser uma correspondência exata, a regra CORS não pode incluir um caractere curinga ' * ' caractere.

- Não há nenhuma regra correspondente a origem de solicitação, mas CORS está habilitada para o serviço de armazenamento.

No caso onde uma solicitação GET/cabeça corresponde a uma regra CORS que permite que todas as origens, a resposta indica que todas as origens são permitidas, e o cache de agente de usuário permitirá solicitações subsequentes de qualquer domínio de origem enquanto o cache estiver ativo.

Observe que para solicitações usando métodos diferente GET/cabeça, os serviços de armazenamento não definirá cabeçalho variar, desde que as respostas para esses métodos não são armazenadas em cache por agentes de usuário.

A tabela a seguir indica o armazenamento do Azure como responderá às solicitações GET/cabeça com base em casos a mencionado anteriormente:

Solicitação|Configuração de conta e o resultado da avaliação de regra|||Resposta|||
---|---|---|---|---|---|---|---|---
**Cabeçalho de origem presente na solicitação** | **Regras CORS especificadas para este serviço** | **Regra de correspondência existe que permite que todas as origens (*)** | **Regra de correspondência existe para correspondência exata de origem** | **Resposta inclui variar cabeçalho definido como origem** | **Resposta inclui acesso controle-permitido-origem: "*"** | **Resposta inclui acesso-controle-expostos-cabeçalhos**
Não|Não|Não|Não|Não|Não|Não
Não|Sim|Não|Não|Sim|Não|Não
Não|Sim|Sim|Não|Não|Sim|Sim
Sim|Não|Não|Não|Não|Não|Não
Sim|Sim|Não|Sim|Sim|Não|Sim
Sim|Sim|Não|Não|Sim|Não|Não
Sim|Sim|Sim|Não|Não|Sim|Sim


## <a name="billing-for-cors-requests"></a>Cobrança para solicitações CORS

Solicitações de comprovação bem-sucedidas serão cobradas se você habilitou CORS para qualquer um dos serviços de armazenamento para sua conta (chamando [Definir propriedades de serviço de Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Definir propriedades de serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx)ou [Definir propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar encargos, considere a possibilidade de configuração do elemento de **MaxAgeInSeconds** no regras CORS para um valor grande para que o agente de usuário armazena a solicitação.

Solicitações de comprovação malsucedidas não serão cobradas.

## <a name="next-steps"></a>Próximas etapas

[Definir propriedades de serviço de Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Definir propriedades de serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx)

[Definir as propriedades de serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificação de compartilhamento de recursos de entre origens de W3C](http://www.w3.org/TR/cors/)
