<properties 
    pageTitle="Integração do Enterprise com EDIFACT | Microsoft Azure" 
    description="Saiba como usar contratos EDIFACT para criar aplicativos de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="jonfan"/>

# <a name="enterprise-integration-with-edifact"></a>Integração do Enterprise com EDIFACT 

> [AZURE.NOTE] Esta página aborda os recursos EDIFACT dos aplicativos de lógica. Para obter informações sobre X12 clique [aqui](app-service-logic-enterprise-integration-x12.md).

## <a name="create-an-edifact-agreement"></a>Criar um contrato EDIFACT 
Antes que você pode trocar mensagens EDIFACT, você precisa criar um contrato EDIFACT e armazená-lo em sua conta de integração. As etapas a seguir o orientará durante o processo de criação de um contrato EDIFACT.

### <a name="heres-what-you-need-before-you-get-started"></a>Veja aqui o que é necessário antes de começar
- Uma [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definido na sua assinatura do Azure  
- Pelo menos dois [parceiros](./app-service-logic-enterprise-integration-partners.md) já definido em sua conta de integração  

>[AZURE.NOTE]Ao criar um contrato, o conteúdo nas mensagens que você vai que receber/enviar para e do parceiro deve corresponder ao tipo de contrato.    


Depois de ter [criado uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionado parceiros](./app-service-logic-enterprise-integration-partners.md), você pode criar um contrato EDIFACT seguindo estas etapas:  

### <a name="from-the-azure-portal-home-page"></a>Do Azure home page do portal

Depois de fazer logon no [portal do Azure](http://portal.azure.com "Azure portal"):  
1. Selecione **Procurar** no menu à esquerda.  

>[AZURE.TIP]Se você não vir o link **Procurar** , talvez você precise expandir o menu primeiro. Faça isto selecionando o link **Mostrar menu** localizada na parte superior esquerda do menu recolhido.  

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)    
2. Digite *integração* na caixa de pesquisa de filtro e selecione **Contas de integração** da lista de resultados.       
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)    
3. Na lâmina **Contas de integração** que será aberta, selecione a conta de integração no qual você criará o contrato. Se você não vir qualquer integração contas listas, [criar uma primeira](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)  
4.  Selecione o bloco de **contratos** . Se você não vir o bloco de contratos, adicioná-lo primeiro.   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)     
5. Selecione o botão **Adicionar** na lâmina contratos que se abre.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)  
6. Digite um **nome** para seu contrato e selecione o **tipo de contrato** EDIFACT, **Host parceiro**, **Identidade do Host**, **Parceiro de convidado**, **Identidade de convidado**, na lâmina contratos que se abre.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)  
7. Depois de definir as propriedades de contrato, selecione **Configurações de receber** para configurar como mensagens recebidas via este contrato devem ser tratadas.  
8. O controle de configurações de recebimento é dividido em seções a seguir, incluindo identificadores, confirmação, esquemas, números de controle, validação, configurações internas e processamento em lotes. Configure essas propriedades com base em seu contrato com o parceiro que você vai ser trocando mensagens com. Aqui está um modo de exibição desses controles, configurá-los com base em como você deseja que este contrato para identificar e manipular as mensagens de entrada:  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)  
9. Selecione o botão de **Okey** para salvar suas configurações.  

### <a name="identifiers"></a>Identificadores

|Propriedade|Descrição |
|---|---|
|UNB6.1 (referência destinatário senha)|Insira um valor alfanumérico que varia entre 1 e 14 caracteres.|
|UNB6.2 (referência destinatário qualificador)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de dois caracteres.|

### <a name="acknowledgments"></a>Confirmações 

|Propriedade|Descrição |
|----|----|
|Confirmação da mensagem (CONTRL)|Marque esta caixa de seleção para retornar uma confirmação (CONTRL) técnica ao remetente troca. A confirmação é enviada ao remetente troca baseado nas configurações de enviar para o contrato.|
|Confirmação (CONTRL)|Marque esta caixa de seleção para retornar uma confirmação (CONTRL) funcional para o remetente de troca a confirmação é enviada ao remetente troca baseado nas configurações de enviar para o contrato.|

### <a name="schemas"></a>Esquemas

|Propriedade|Descrição |
|----|----|
|UNH2.1 (TIPO)|Selecione um tipo de conjunto de transação.|
|UNH2.2 (VERSÃO)|Insira o número de versão de mensagem. (Mínimo, um caractere; máximo, três caracteres).|
|UNH2.3 (LANÇAMENTO)|Insira o número de versão de mensagem. (Mínimo, um caractere; máximo, três caracteres).|
|UNH2.5 (CÓDIGO ATRIBUÍDO ASSOCIADO)|Insira o código atribuído. (Máximo, seis caracteres. Deve ser alfanumérico).|
|UNG2.1 (ID DE REMETENTE DE APLICATIVO)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de 35 caracteres.|
|UNG2.2 (APLICATIVO REMETENTE QUALIFICADOR DE CÓDIGO)|Insira um valor alfanumérico, com um máximo de quatro caracteres.|
|ESQUEMA|Selecione o esquema anteriormente carregado da sua conta de integração associado que você deseja usar.|

### <a name="control-numbers"></a>Números de controle

|Propriedade|Descrição |
|----|----|
|Proibir o número de controle de troca de duplicatas|Marque esta caixa de seleção Bloquear cruzamentos duplicados. Se selecionado, a ação de decodificar EDIFACT verifica que o número de controle de troca (UNB5) para a troca recebido não coincidir com um número de controle de troca processados anteriormente. Se uma correspondência for detectada, então o a troca não é processada.
|Verificar se há UNB5 duplicados cada (dias)|Se você optou por não permitir números de controle de troca duplicados, você pode especificar o número de dias em que a verificação é executada, fornecendo o valor apropriado para a opção **Verificar UNB5 duplicados cada (dias)** .|
|Proibir duplicatas de número de controle de grupo|Marque esta caixa de seleção Bloquear cruzamentos com números de controle de grupo duplicado (UNG5).|
|Proibir transação conjunto controle números duplicatas|Marque esta caixa de seleção Bloquear cruzamentos com números de controle de conjunto de transação duplicadas (UNH1).|
|Número de controle de confirmação EDIFACT|Para designar os números de referência de conjunto de transação a ser usado em uma confirmação, digite um valor para o prefixo, um intervalo de números de referência e um sufixo.|

### <a name="validations"></a>Validações

|Propriedade|Descrição |
|----|----|
|Tipo de mensagem|Especifique o tipo de mensagem. Que cada linha de validação for concluída, outra será adicionada automaticamente. Se nenhuma regra forem especificada, a linha marcada como padrão é usado para validação.|
|Validação de EDI|Marque esta caixa de seleção para executar a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazio e separadores à direita.|
|Validação estendida|Marque esta caixa de seleção para ativar a validação de (XSD) estendida de cruzamentos recebido do remetente troca. Isso inclui validação de comprimento do campo, opcionalidade e contagem de repetição além de validação de tipo de dados XSD.|
|Permitir zeros iniciais/finais|Selecione **Permitir** para permitir zeros; líderes/à direita Zeros **NotAllowed** para não permitir líderes/zeros à direita, ou **Aparar** para aparar os principais e à direita.|
|Cortar zeros iniciais/finais|Marque esta caixa de seleção para aparar quaisquer zeros à direita ou à esquerda|
|Política à direita do separador|Selecione **Não permitido** se não quiser permitir que delimitadores e separadores à direita de uma troca recebida do remetente troca. Se a troca contiver delimitadores e separadores à direita, ele é declarado inválidos. Selecione **opcional** para aceitar cruzamentos com ou sem separadores e delimitadores à direita. Selecione **obrigatória** se a troca recebida deve conter à direita delimitadores e separadores.|

### <a name="internal-settings"></a>Configurações internas

|Propriedade|Descrição |
|----|----|
|Criar marcas XML vazias se separadores à direita são permitidos|Marque esta caixa de seleção para que o remetente de troca incluir marcas XML vazias para separadores à direita.|
|Processamento de lote de entrada|As opções incluem:</br></br>**Troca de divisão como conjuntos de transação - suspender conjuntos de transação em erro**: analisa cada transação definir em uma troca em um documento XML separado aplicando o envelope apropriado para o conjunto de transação. Com essa opção, se um ou mais conjuntos de transação na troca falham na validação, em seguida, apenas os conjuntos de transação estão suspenso. Troca de divisão como conjuntos de transação - suspender troca no erro: analisa cada transação definir em um intercâmbio em um documento XML separado aplicando o envelope apropriado. Com essa opção, se um ou mais transação define na troca falham na validação, então a troca inteira será suspenso.</br></br>**Troca preservar - suspender conjuntos de transação em erro**: deixa a troca intactos, criando um documento XML para a troca de lote inteiro. Com essa opção, se um ou mais conjuntos de transação na troca falham na validação, em seguida, apenas os conjuntos de transação estão suspenso, enquanto todos os outros conjuntos de transação são processados.</br></br>**Troca preservar - suspender troca erro**: deixa a troca intactos, criando um documento XML para a troca de lote inteiro. Com essa opção, se um ou mais transação define na troca falham na validação, então a troca inteira está suspenso.|

Seu contrato está pronto para lidar com as mensagens de entrada em conformidade com as configurações selecionadas.

Para configurar as configurações de lidar com as mensagens enviadas para os parceiros:  
10. Selecione **Configurações de envio** para configurar como as mensagens enviadas por meio deste Contrato devem ser tratadas.  

O controle de configurações de envio é dividido em seções a seguir, inclusive identificadores, confirmação, esquemas, Envelopes, conjuntos de caracteres separadores, números de controle e validação. 

Aqui está um modo de exibição desses controles. Faça as seleções com base em como você deseja lidar com as mensagens enviadas para os parceiros via este contrato:   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)    
11. Selecione o botão de **Okey** para salvar suas configurações.  

### <a name="identifiers"></a>Identificadores
|Propriedade|Descrição |
|----|----|
|UNB1.2 (versão de sintaxe)|Selecione um valor entre **1** e **4**.|
|UNB2.3 (inversa roteamento endereço de remetente)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de 14 caracteres.|
|UNB3.3 (roteamento inversa destinatário endereço)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de 14 caracteres.|
|UNB6.1 (referência destinatário senha)|Insira um valor alfanumérico com um mínimo e máximo 14 caracteres.|
|UNB6.2 (referência destinatário qualificador)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de dois caracteres.|
|UNB7 (ID de aplicativo de referência)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de 14 caracteres|

### <a name="acknowledgment"></a>Confirmação
|Propriedade|Descrição |
|----|----|
|Confirmação da mensagem (CONTRL)|Marque esta caixa de seleção se o parceiro hospedado espera receber para receber uma confirmação (CONTRL) técnica. Esta configuração especifica que o parceiro hospedado, quem está enviando a mensagem, solicita uma confirmação do parceiro de convidado.|
|Confirmação (CONTRL)|Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação (CONTRL) funcional. Esta configuração especifica que o parceiro hospedado, quem está enviando a mensagem, solicita uma confirmação do parceiro de convidado.|
|Gerar SG1/SG4 loop para conjuntos de transação aceita|Se você escolheu solicitar uma confirmação funcional, marque esta caixa de seleção Forçar geração de SG1/SG4 loops em confirmações CONTRL funcionais para conjuntos de transação aceito.|

### <a name="schemas"></a>Esquemas
|Propriedade|Descrição |
|----|----|
|UNH2.1 (TIPO)|Selecione um tipo de conjunto de transação.|
|UNH2.2 (VERSÃO)|Insira o número de versão de mensagem.|
|UNH2.3 (LANÇAMENTO)|Insira o número de versão de mensagem.|
|ESQUEMA|Selecione o esquema para usar. Esquemas estão localizados na sua conta de integração. Para acessar seus esquemas, primeiro vincule a sua conta de integração para o seu aplicativo de lógica.|

### <a name="envelopes"></a>Envelopes
|Propriedade|Descrição |
|----|----|
|UNB8 (código de prioridade de processamento)|Insira um valor em ordem alfabética que não está mais de um caractere de comprimento.|
|UNB10 (contrato de comunicação)|Insira um valor alfanumérico com no mínimo de um caractere e um máximo de 40 caracteres.|
|UNB11 (teste indicador)|Marque esta caixa de seleção para indicar que a troca gerada é dados de teste|
|Aplicar UNA segmento (conselhos de cadeia de caracteres de serviço)|Marque esta caixa de seleção para gerar um segmento UNA para a troca sejam enviadas.|
|Aplicar UNG segmentos (cabeçalho de grupo de função)|Marque esta caixa de seleção para criar segmentos de agrupamento no cabeçalho de grupo funcional as mensagens enviadas para o parceiro de convidado. Os seguintes valores são usados para criar os segmentos UNG:</br></br>Para **UNG1**, insira um valor alfanumérico com no mínimo de um caractere e um máximo de seis caracteres.</br></br>Para **UNG2.1**, insira um valor alfanumérico com no mínimo de um caractere e um máximo de 35 caracteres.</br></br>Para **UNG2.2**, insira um valor alfanumérico, com um máximo de quatro caracteres.</br></br>Para **UNG3.1**, insira um valor alfanumérico com no mínimo de um caractere e um máximo de 35 caracteres.</br></br>Para **UNG3.2**, insira um valor alfanumérico, com um máximo de quatro caracteres.</br></br>Para **UNG6**, insira um valor alfanumérico com um mínimo e máximo de três caracteres.</br></br>Para **UNG7.1**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de três caracteres.</br></br>Para **UNG7.2**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de três caracteres.</br></br>Para **UNG7.3**, insira um valor alfanumérico com um mínimo de 1 caractere e um máximo de 6 caracteres.</br></br>Para **UNG8**, insira um valor alfanumérico com um mínimo de um caractere e um máximo de 14 caracteres.|

### <a name="character-sets-and-separators"></a>Conjuntos de caracteres e separadores
Diferente do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores a ser usado para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um esquema de determinada mensagem, então o conjunto de caracteres padrão é usado.

|Propriedade|Descrição |
|----|----|
|UNB1.1 (identificador de sistema)|Selecione o caractere EDIFACT definido para ser aplicado a troca de saída.|
|Esquema|Selecione um esquema na lista suspensa. Como cada linha é concluída uma nova linha será adicionada. Para o esquema selecionado, selecione os separadores conjunto a ser usado:</br></br>**Separador de elemento do componente** – Insira um único caractere para separar os elementos de dados compostos.</br></br>**Separador de elemento de dados** – Insira um único caractere para separar os elementos de dados simples em elementos de dados compostos.</br></br></br></br>**Caractere de substituição** – Marque esta caixa de seleção se a carga de dados contêm caracteres que também são usados como dados, segmento ou separadores de componente. Você pode inserir um caractere de substituição. Ao gerar a mensagem de saída EDIFACT, todas as instâncias de caracteres separadores nos dados de carga são substituídas por caractere especificado.</br></br>**Terminador de segmento** – Insira um único caractere para indicar o final de um segmento EDI.</br></br>**Sufixo** – selecione o caractere que é usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador segmento pode estar em branco. Se o terminador segmento estiver vazio, você deve designar um sufixo.|

### <a name="control-numbers"></a>Números de controle
|Propriedade|Descrição |
|----|----|
|UNB5 (número de controle de troca)|Digite um prefixo, um intervalo de valores para o número de controle de troca e um sufixo. Esses valores são usados para gerar uma saída troca. O prefixo e sufixo são opcionais. é necessário o número de controle. O número de controle é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos.|
|UNG5 (número de controle do grupo)|Digite um prefixo, um intervalo de valores para o número de controle de troca e um sufixo. Esses valores são usados para gerar o número de controle de grupo. O prefixo e sufixo são opcionais. é necessário o número de controle. O número de controle é incrementado para cada nova mensagem até que o valor máximo seja atingido; o prefixo e sufixo permanecem os mesmos.|
|UNH1 (número de referência do cabeçalho da mensagem)|Digite um prefixo, um intervalo de valores para o número de controle de troca e um sufixo. Esses valores são usados para gerar o número de referência de cabeçalho da mensagem. O prefixo e sufixo são opcionais. é necessário o número de referência. O número de referência é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos.|

### <a name="validations"></a>Validações
|Propriedade|Descrição |
|----|----|
|Tipo de mensagem|Esta opção habilita a validação no receptor troca. Essa validação executa a validação de EDI em elementos de dados do conjunto de transação, validando tipos de dados, restrições de comprimento e elementos de dados vazio e separadores de treinamento.|
|Validação de EDI|Marque esta caixa de seleção para executar a validação de EDI em tipos de dados, conforme definido pelas propriedades EDI do esquema, restrições de comprimento, elementos de dados vazio e separadores à direita.|
|Validação estendida|Selecionar essa opção permite validação estendida da cruzamentos recebido do remetente troca. Isso inclui validação de comprimento do campo, opcionalidade e contagem de repetição além de validação de tipo de dados XSD. Você pode habilitar a validação de extensão sem habilitar validação de EDI, ou vice-versa.|
|Permitir iniciais/finais zeros|Selecionar essa opção especifica que uma troca EDI recebida do participante não falham na validação se um elemento de dados em uma troca EDI não de acordo com seu requisito de comprimento devido ou espaços à direita, mas está de acordo com suas necessidades de comprimento quando eles são removidos.|
|Cortar zeros iniciais/finais|Selecionar essa opção poderão aparar os zeros à direita e à esquerda.|
|À direita do separador|Selecionar essa opção especifica uma troca EDI recebida do participante não falham na validação se um elemento de dados em uma troca EDI não de acordo com suas necessidades de comprimento devido zeros à esquerda (ou à direita) ou espaços à direita, mas está de acordo com suas necessidades de comprimento quando eles são removidos.</br></br>Selecione **Não permitido** se não quiser permitir que delimitadores e separadores à direita de uma troca recebida do remetente troca. Se a troca contiver delimitadores e separadores à direita, ele é declarado inválidos.</br></br>Selecione **opcional** para aceitar cruzamentos com ou sem separadores e delimitadores à direita.</br></br>Selecione **obrigatória** se a troca recebida deve conter à direita delimitadores e separadores.|

Após selecionar **Okey** na lâmina aberta:  
12. Selecione o bloco de **contratos** na lâmina conta de integração e você verá o contrato recém-adicionado listado.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)   

## <a name="learn-more"></a>Saiba Mais
- [Saiba mais sobre o pacote de integração do Enterprise] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa")  
