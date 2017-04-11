<properties
    pageTitle="Utilizando o conector de arquivo nos aplicativos de lógica | Serviço de aplicativo do Microsoft Azure"
    description="Como criar e configurar o conector de arquivo ou o aplicativo de API e usá-lo em um aplicativo de lógica no serviço de aplicativo do Azure"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Comece a usar o conector de arquivo e adicioná-lo ao seu aplicativo de lógica
>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica.

Conecte a um sistema de arquivo para carregar, baixar e muito mais arquivos em uma máquina host. Aplicativos de lógica podem disparar com base em uma variedade de fontes de dados e conectores de oferta para obter e processar dados. Você pode adicionar o conector de arquivo aos seus dados de fluxo de trabalho e o processo de negócios como parte deste fluxo de trabalho dentro de um aplicativo de lógica. 

O conector de arquivo usa o Gerenciador de Conexão híbrido para conectividade de híbrido com o sistema de arquivos de host.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Criando um conector de arquivo para o aplicativo de lógica ##
Para usar o conector de arquivo, você precisa primeiro criar uma instância do aplicativo de API de conector do arquivo. Isso pode ser feito da seguinte maneira:

1.  Abra o Azure Marketplace usando + nova opção no lado esquerdo do Portal do Azure.
2.  Procure por "conector de arquivo".
3.  Selecione o **Conector de arquivo (visualização)** nos resultados da pesquisa.
4.  Selecione o botão **criar**
5.  Configure o conector de arquivo da seguinte maneira:  
![][1]

    - **Nome** - dar um nome para o conector de arquivo
    - **Configurações do pacote**
        - **Pasta raiz** - especificar o caminho da pasta raiz em sua máquina host. Por exemplo D:\FileConnectorTest
        - **Cadeia de Conexão do serviço barramento** - fornecer uma cadeia de Conexão do barramento de serviço. Verifique se é o namespace do barramento de serviço do tipo padrão e não básica para permitir o uso do serviço barramento retransmissões.  Retransmissão de barramento de serviço é usado para conectar-se ao Gerenciador de Conexão de híbrido.
    - **Plano de serviço de aplicativo** - selecionar ou criar um plano de serviço de aplicativo
    - **Nível de preço** - escolha um nível de preços para o conector
    - **Grupo de recursos** - selecionar ou criar um grupo de recursos onde o conector deve residem
    - **Assinatura** - escolha uma assinatura que você deseja este conector ser criados em
    - **Local** - escolha a localização geográfica onde deseja que o conector para ser implantado

4. Clique em criar. Será criado um novo conector de arquivo

## <a name="configure-hybrid-connection-manager"></a>Configurar o Gerenciador de Conexão de híbrido ##
Depois que a instância de API App é criada, navegue até seu painel.  Isso pode ser feito clicando em Procurar > API aplicativos > selecione o conector de arquivo API App.  A partir daqui o Gerenciador de Conexão híbrido precisa ser configurado.
Para obter mais informações sobre como configurar e o Gerenciador de Conexão híbrido de solução de problemas, consulte [usando o Gerenciador de Conexão híbrido].

## <a name="using-the-file-connector-in-your-logic-app"></a>Utilizando o conector de arquivo em seu aplicativo de lógica ##
Depois que seu aplicativo de API é criado, você agora pode usar o conector de arquivo como uma ação para o aplicativo de lógica. Para fazer isso, você precisa:

1.  Criar um novo aplicativo de lógica e escolha o mesmo grupo de recursos que tem o conector de arquivo. Siga as instruções para [criar um novo aplicativo de lógica].

2.  Abra "Disparadores e ações" dentro do aplicativo de lógica criado para abrir os aplicativos de lógica Designer e configurar seu fluxo.

3.  O conector de arquivo apareceriam na seção "API aplicativos no grupo de recursos" na Galeria em à direita.

4.  Você pode soltar o aplicativo de API de conector de arquivo no editor clicando o "conector de arquivo". conector do arquivo expõe um disparador e 4 ações:  
![][5]

6.  Cada uma delas expõe determinadas propriedades. A imagem a seguir lista as propriedades do disparador e obter arquivo ação:  
![][6]

7. Depois que eles são configurados, o acionador e a ação podem ser usados no seu fluxo. Da mesma forma, outras ações podem ser configuradas também.

> [AZURE.NOTE] O disparador de arquivo o arquivo será excluído depois que ela é lida com êxito da pasta.

## <a name="file-connector-rest-apis"></a>Conector APIs REST de arquivo ##
Para usar o conector fora de um aplicativo de lógica, as APIs REST expostos pelo conector pode ser utilizadas. Você pode exibir este definições de API usando Procurar -> Api App -> conector de arquivo. Agora, clique nas lentes de definição de API sob a seção de resumo para exibir todas as APIs expostas por esse conector:  
![][7]

Detalhes das APIs podem ser encontrados no [conector de arquivo definição de API].

## <a name="do-more-with-your-connector"></a>Faça mais com o conector
Agora que o conector for criado, você pode adicioná-lo para um fluxo de trabalho de negócios usando um aplicativo de lógica. Consulte [quais são os aplicativos de lógica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se quiser começar a usar aplicativos do Azure lógica antes de se inscrever para uma conta do Azure, vá para o [aplicativo tente lógica](https://tryappservice.azure.com/?appservice=logic), onde você pode criar imediatamente um aplicativo de lógica de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

Exiba a referência de API de REST Swagger a [referência de aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode analisar segurança de controle e as estatísticas de desempenho ao conector. Consulte [Gerenciar e monitorar seus aplicativos de API e conector interno](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Criar um novo aplicativo de lógica]: app-service-logic-create-a-logic-app.md
[Arquivo de definição de API do conector]: https://msdn.microsoft.com/library/dn936296.aspx
[Usando o Gerenciador de Conexão híbrido]: app-service-logic-hybrid-connection-manager.md
