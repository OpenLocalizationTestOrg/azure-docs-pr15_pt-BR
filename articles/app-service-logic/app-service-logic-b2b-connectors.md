<properties 
    pageTitle="Conectores de Business-to-Business e aplicativos de API nos aplicativos de lógica | Microsoft Azure" 
    description="Aprenda a criar e configurar conectores EDI, EDIFACT, AS2 e TPM; arquitetura de microservices" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Conectores de Business-to-Business e aplicativos de API

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Lógica aplicativos inclui muitos aplicativos de API do BizTalk que são importantes aos ambientes de integração. Esses aplicativos de API são baseados em conceitos e ferramentas usadas no BizTalk Server, mas agora estão disponíveis como parte dos aplicativos de lógica. 

Uma categoria desses aplicativos de API são os aplicativos de API do Business-to-Business (B2B). Usando esses aplicativos de API B2B, você pode facilmente adicionar parceiros, criar contratos e faça tudo como você faria local usando EDI, AS2 e EDIFACT.  

Esses aplicativos de API B2B oferecem recursos "Acionador" e "Ação". Um disparador inicia uma nova instância com base em um evento específico, como a chegada de um X12 mensagem de um parceiro. Uma ação é o resultado, como após receber um X12 da mensagem e, em seguida, enviar a mensagem usando AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>O que é um conector para empresas ou API aplicativos
O recurso de Business-to-Business (B2B) inclui aplicativos de API existente, pré-criados que permitem que diferentes empresas, divisões, aplicativos, e assim por diante para se comunicar usando AS2, EDI e EDIFACT. 

Os aplicativos de API B2B incluem: 

Conector ou aplicativos de API | Descrição
--- | ---
Gerenciamento de parceiros de negociação BizTalk | Um App API cria relações de business-to-business (B2B) usando contratos e parceiros. Essas relações de utilizam o AS2, EDIFACT e X12 protocolo.<br/><br/>O aplicativo de API TPM é o requisito de base do conector AS2 e a X12 ou EDIFACT API aplicativos. 
Conector AS2 | Um conector que recebe e envia mensagens usando o transporte AS2. O conector transportes dados de forma segura e confiável pela Internet.
BizTalk EDIFACT | Um API App recebe e envia mensagens usando EDIFACT. EDIFACT é também conhecido como UN/EDIFACT (United Nations/eletrônica Interchange para administração de dados, comércio e transporte) e é amplamente usado em setores.
BizTalk X12 | Um App API que recebe e envia mensagens usando o X12 protocolo. X12 é também conhecida como ASC X 12 (capacitação padrões comitê X12) e é amplamente usado em setores. 


Usando esses aplicativos de API, você pode concluir EDI diferentes tarefas de mensagens. Por exemplo, utilizando o conector AS2, você pode com segurança receber e enviar diferentes tipos de mensagens (EDI, XML, arquivos simples, e assim por diante) para um cliente, uma divisão dentro da sua empresa, como recursos humanos ou qualquer pessoa que usa AS2. 

Você pode criar quantos aplicativos de API conforme desejado e criá-los facilmente. Você também pode reutilizar uma única API App dentro de vários cenários ou fluxos de trabalho.

Você pode fazer isso sem escrever nenhum código. Então vamos começar. 


## <a name="requirements-to-get-started"></a>Requisitos para começar
Quando você cria aplicativos de API B2B, há alguns recursos necessários. Esses itens devem ser criados por você antes que eles podem ser usados em outros aplicativos de API. Esses requisitos incluem: 

Requisito | Descrição
--- | ---
Banco de dados do SQL Azure | Armazena itens B2B incluindo parceiros, esquemas, certificados e agreeements. Cada um dos aplicativos da API do B2B requer seu próprio banco de dados do SQL Azure. <br/><br/>**Observação** Copie a cadeia de conexão para este banco de dados.<br/><br/>[Criar um banco de dados do SQL Azure](../sql-database/sql-database-get-started.md)
Contêiner de armazenamento de Blob Azure | Propriedades de mensagem armazena quando AS2 arquivamento está habilitado. Se não precisar AS2 arquivamento de mensagens, não é necessário um contêiner de armazenamento. <br/><br/>**Observação** Se você estiver ativando o arquivamento, copie a cadeia de conexão para o armazenamento de Blob.<br/><br/>[Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md)
Namespace do barramento de serviço e seus valores de chave | Armazena X12 e EDIFACT lotes de dados. Se não precisar lotes, um namespace barramento de serviço não é necessário.<br/><br/>**Observação** Se você estiver ativando o processamento em lotes, copie esses valores.<br/><br/>[Criar um Namespace de barramento de serviço](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instância TPM | Uma instância de gerenciamento de parceiro de negociação (TPM) BizTalk é necessária para criar um conector AS2 e X12 ou EDIFACT API do aplicativo. Quando você cria o aplicativo de API do TPM, você está criando a instância de TPM. <br/><br/>**Observação** Sabe o nome do seu aplicativo de API do TPM. 


## <a name="create-the-api-apps"></a>Criar aplicativos API
Aplicativos de API B2B podem ser criados usando o portal do Azure ou APIs REST. 


### <a name="create-the-api-apps-using-rest-apis"></a>Criar aplicativos API usando APIs REST
[Consulte a documentação sobre como usar as APIs REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Criar aplicativos B2B API no Portal do Azure
No portal do Azure, você pode criar aplicativos de API B2B ao criar lógica de aplicativos, aplicativos Web ou celular. Ou, você pode criar um usando seu próprio blade. Os dois lados são fáceis para que ele depende de suas necessidades ou preferências. Alguns usuários preferem criar todos os aplicativos de API B2B com suas propriedades específicas primeiro. Em seguida, criar aplicativos lógica aplicativos/Web Apps/Mobile e adicione os aplicativos de API B2B que você criou.  

As etapas a seguir criam aplicativos B2B API usando a lâmina API aplicativos.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Criar o BizTalk negociação aplicativos de API do parceiro gerenciamento (TPM)

> [AZURE.NOTE] Uma instância de gerenciamento de parceiro de negociação (TPM) BizTalk é necessária para criar um conector AS2 e X12 ou EDIFACT API do aplicativo. Quando você cria o aplicativo de API do TPM, você está criando a instância de TPM.

As etapas a seguir criam a instância TPM:

1. No portal do Azure Startboard (a Home page), selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API existente e conectores. Você também pode **Pesquisar** para os aplicativos de API B2B específicos.
2. Selecione **BizTalk negociação gerenciamento de parceiro**. Na nova lâmina, selecione **criar**. 
3. Insira as propriedades: 

    Propriedade | Descrição
--- | ---
Nome | Digite qualquer nome para a instância TPM. Por exemplo, você pode nomear *AccountsPayableTPM*.
Configurações do pacote | Insira a **Cadeia de Conexão de banco de dados** do ADO.NET o banco de dados do Azure SQL que você criou. <br/><br/>Quando você copia a cadeia de conexão, a senha não é adicionada à cadeia de caracteres de conexão. Certifique-se de digitar a senha na cadeia de conexão.
Plano de serviço de aplicativo | Lista seu plano de pagamento. Você pode alterar se você precisar de mais ou menos recursos.
Nível de preço | Propriedade de somente leitura que lista a categoria de preços de sua assinatura do Azure. 
Grupo de recursos | Criar um novo ou use um grupo existente. Todos os aplicativos de API e conectores para seus aplicativos de lógica, aplicativos Web e aplicativos Mobile devem estar no mesmo grupo de recursos. <br/><br/>[Usando grupos de recursos](../azure-resource-manager/resource-group-overview.md) explica a essa propriedade. 
Assinatura | Propriedade de somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda seu serviço Azure. 
Adicionar a Startboard | Selecione esta opção para adicionar o aplicativo de API B2B ao seu Starboard (a home page).

4. Selecione **criar**. 

Após o aplicativo de API do TPM (TPM instância) é criado, você pode criar conector AS2 e/ou a X12 ou EDIFACT API aplicativos. 


#### <a name="create-the-as2-connector"></a>Criar o conector AS2

1. No portal do Azure Startboard (a Home page), selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API existente e conectores. Você também pode **Pesquisar** para os aplicativos de API B2B específicos.
2. Selecione **AS2 conector**. Na nova lâmina, selecione **criar**. 
3. Insira as propriedades: 

    Propriedade | Descrição
--- | ---
Nome | Digite qualquer nome para o conector AS2. Por exemplo, você pode nomear *AS2Connector*.
Configurações do pacote | Insira as configurações específicas que App API, como o nome da instância de TPM. <br/><br/>Consulte [Adicionar configurações de pacote de AS2](#AddAS2Conn) neste tópico para as propriedades específicas. 
Plano de serviço de aplicativo | Lista seu plano de pagamento. Você pode alterar se você precisar de mais ou menos recursos.
Nível de preço | Propriedade de somente leitura que lista a categoria de preços de sua assinatura do Azure. 
Grupo de recursos | Criar um novo ou use um grupo existente. [Usando grupos de recursos](../azure-resource-manager/resource-group-overview.md) explica a essa propriedade. 
Assinatura | Propriedade de somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda seu serviço Azure. 
Adicionar a Startboard | Selecione esta opção para adicionar o aplicativo de API B2B ao seu Starboard (a home page).

    **<a name="AddAS2Conn"></a>Conector AS2 configurações do pacote**

    Propriedade | Descrição
--- | --- 
Cadeia de Conexão de banco de dados | Insira a cadeia de conexão de ADO.NET o banco de dados do Azure SQL que você criou. Quando você copia a cadeia de conexão, a senha não é adicionada à cadeia de caracteres de conexão. Certifique-se de digitar a senha na cadeia de conexão antes de colar.
Habilitar o arquivamento para mensagens de entrada | Opcional. Habilite essa propriedade armazenar as propriedades da mensagem de uma mensagem de entrada AS2 recebidos de um parceiro. 
Cadeia de Conexão do armazenamento de blob do Microsoft Azure  | Insira a cadeia de conexão para o contêiner de armazenamento de Blob do Azure que você criou. Quando o arquivamento está habilitado, as mensagens codificadas e decodificadas são armazenadas neste contêiner de armazenamento.
Nome da instância TPM | Insira o nome do **Gerenciamento de parceiros de negociação BizTalk** App API você criou anteriormente. Quando você cria o conector AS2, esse conector executa somente os contratos AS2 dentro essa instância específica do TPM.

4. Selecione **criar**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Criar aplicativos API X12 ou EDIFACT

1. No portal do Azure Startboard (a Home page), selecione **Marketplace**. **Aplicativos de API** lista todos os aplicativos de API existente e conectores. Você também pode **Pesquisar** para os aplicativos de API B2B específicos.
2. Selecione **BizTalk X 12** ou **BizTalk EDIFACT**. Na nova lâmina, selecione **criar**. 
3. Insira as propriedades: 

    Propriedade | Descrição
--- | ---
Nome | Digite qualquer nome para o aplicativo de API B2B. Por exemplo, você pode nomear *EDI850APIApp*.
Configurações do pacote | Insira as configurações específicas que App API, como o nome da instância de TPM. <br/><br/>Consulte [X12 ou configurações de pacote EDIFACT](#AddX12) neste tópico para as propriedades específicas. 
Plano de serviço de aplicativo | Lista seu plano de pagamento. Você pode alterar se você precisar de mais ou menos recursos.
Nível de preço | Propriedade de somente leitura que lista a categoria de preços de sua assinatura do Azure. 
Grupo de recursos | Criar um novo ou use um grupo existente. [Usando grupos de recursos](../azure-resource-manager/resource-group-overview.md) explica a essa propriedade. 
Assinatura | Propriedade de somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda seu serviço Azure. 
Adicionar a Startboard | Selecione esta opção para adicionar o aplicativo de API B2B ao seu Starboard (a home page).

    **<a name="AddX12"></a>Configurações de pacote de aplicativos de API X12 e EDIFACT**  

    Propriedade | Descrição
--- | --- 
Cadeia de Conexão de banco de dados | Insira a cadeia de conexão de ADO.NET o banco de dados do Azure SQL que você criou. Quando você copia a cadeia de conexão, a senha não é adicionada à cadeia de caracteres de conexão. Certifique-se de digitar a senha na cadeia de conexão antes de colar.
Namespace do barramento de serviço | Insira o namespace de barramento de serviço que você criou. Necessário somente quando lotes está habilitado. 
Nome da tecla de acesso compartilhado do serviço barramento Namespace | Insira o namespace de barramento de serviço tecla de acesso que você criou. Necessário somente quando lotes está habilitado. 
Valor de chave de acesso compartilhada do serviço barramento Namespace | Insira namespace barramento de serviço do valor de chave de acesso que você criou. Necessário somente quando lotes está habilitado. 
Nome da instância TPM | Insira o nome do **Gerenciamento de parceiros de negociação BizTalk** App API você criou anteriormente. Quando você cria a X12 ou aplicativos de API de EDIFACT, essa API App executa somente os contratos X12/EDFIACT dentro essa instância específica do TPM.

4. Selecione **criar**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Adicionar seu parceiros, contratos, certificados e esquemas 
No portal do Azure, abra seu aplicativo de API do TPM. Na seção **componentes** , adicione seus parceiros, contratos, certificados e esquemas. 

Você também pode adicionar contratos a conectores AS2, X12 aplicativos de API e EDIFACT API aplicativos. 


## <a name="monitor-your-api-apps"></a>Monitore seus aplicativos de API
No portal do Azure, abra seu aplicativo de API do TPM. Na seção **operações** , você pode exibir as operações de gerenciamento diferentes. Por exemplo, você pode:

- Eventos de erro e informativo de modo de exibição
- Exibir a contagem de thread e uso de memória do processo de trabalho (w3wp)
- Exibir os logs de servidor de aplicativos e da web

Mais no [Monitor seus aplicativos de lógica](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Adicionar aplicativos API ao seu aplicativo 
Serviço de aplicativo do Microsoft Azure expõe diferentes tipos de aplicativos que podem usar esses aplicativos de API B2B. Você pode criar um novo ou adicionar seus aplicativos de API B2B existente a lógica de aplicativos, aplicativos Mobile ou um Web Apps. 

Dentro de seu aplicativo, simplesmente selecionando os aplicativos de API B2B da Galeria automaticamente adiciona a seu aplicativo.  

> [AZURE.IMPORTANT] Para adicionar conectores e aplicativos de API que você criou anteriormente, crie a lógica de aplicativos, aplicativos Mobile ou Web Apps no mesmo grupo de recursos. 

As etapas a seguir adicionam aplicativos B2B API a lógica de aplicativos, aplicativos móveis ou aplicativos Web: 

1. No portal do Azure Startboard (página inicial), vá para o **Marketplace**e procure por seu valor lógico, Mobile ou Web Apps. 

    Se você estiver criando um novo aplicativo, pesquise lógica de aplicativos, aplicativos móveis ou aplicativos Web. Selecione o aplicativo e na lâmina novo, selecione **criar**. [Criar um aplicativo de lógica](app-service-logic-create-a-logic-app.md) lista as etapas. 

2. Abra seu aplicativo e selecione **disparadores e ações**. 

3. Na **Galeria**, selecione o aplicativo de API B2B, que adiciona automaticamente a seu aplicativo. Você também pode criar um novo aplicativo API B2B.

    > [AZURE.IMPORTANT] O conector de AS2 e X12, EDIFACT API aplicativos exigem uma instância de TPM. Para que se você estiver criando novos aplicativos de API B2B, criar o aplicativo de API TPM primeiro e, em seguida, criar o conector de AS2, X12 API App ou EDIFACT API do aplicativo. 

4. Selecione **Okey** para salvar suas alterações. 

>[AZURE.NOTE] Para começar com aplicativos do Azure lógica antes de se inscrever para uma conta do Azure, [Tente lógica de aplicativos](https://tryappservice.azure.com/?appservice=logic). Imediatamente, você pode criar um aplicativo de lógica de curta duração starter. Não há cartões de crédito obrigatório; Não há compromissos.

## <a name="more-b2b-resources"></a>Mais recursos de B2B

[Criando um processo de B2B](app-service-logic-create-a-b2b-process.md)<br/>
[Criar um contrato de parceiro comercial](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[O que são conectores e BizTalk API aplicativos](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>Leia sobre aplicativos de lógica e aplicativos Web
[Quais são os aplicativos de lógica?](app-service-logic-what-are-logic-apps.md)<br/>
[Sites e aplicativos da Web em um serviço de aplicativo do Azure](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Conectores de mais

[Lista de aplicativos de API e conectores](app-service-logic-connectors-list.md)<br/><br/>
[O que são conectores e BizTalk API aplicativos](app-service-logic-what-are-biztalk-api-apps.md) 
