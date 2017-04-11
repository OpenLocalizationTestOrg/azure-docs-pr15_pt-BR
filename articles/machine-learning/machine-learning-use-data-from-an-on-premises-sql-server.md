<properties
pageTitle="Usar dados de um banco de dados do SQL Server no local em aprendizado de máquina | Azure"
description="Use dados de um banco de dados do SQL Server local para executar análises avançadas com aprendizado de máquina do Azure."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Execute análise avançada com aprendizado de máquina do Azure usando dados de um banco de dados do SQL Server no local


Muitas vezes empresas que funcionam com dados locais gostaria de aproveitar a escala e agilidade de nuvem para sua cargas de trabalho de aprendizado de máquina. Mas eles não querem interromper seus processos de negócios atuais e fluxos de trabalho movendo seus dados locais na nuvem. Azure aprendizado de máquina agora dá suporte a ler seus dados de um banco de dados do SQL Server local e, em seguida, treinamento e pontuação de um modelo com esses dados. Você não tem mais copiar manualmente e sincronizar os dados entre seu servidor de locais e na nuvem. Em vez disso, o módulo de **Importar dados** no Azure Studio de aprendizado de máquina agora pode ler diretamente do seu banco de dados do SQL Server local para seu treinamento e pontuação trabalhos. 

Este artigo fornece uma visão geral de como ingresso local dados do SQL server em aprendizado de máquina do Azure. Ele pressupõe que você está familiarizado com os conceitos de aprendizado de máquina do Azure como espaços de trabalho, módulos, conjuntos de dados, experiências, *etc*..

> [AZURE.NOTE] Este recurso não está disponível para espaços de trabalho gratuitos. Para obter mais informações sobre preços de aprendizado de máquina e camadas, consulte [Preços de aprendizado de máquina do Azure](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Instalar o Gateway de gerenciamento de dados da Microsoft

Para acessar um banco de dados do SQL Server local no aprendizado de máquina do Azure, você precisa baixar e instalar o Gateway de gerenciamento de dados da Microsoft. Quando você configurar a conexão de gateway no Studio de aprendizado de máquina terá a oportunidade de baixar e instalar o gateway usando a caixa de diálogo **Download e o gateway de dados do registro** descrita a seguir.

Você também pode instalar o Gateway de gerenciamento de dados antecedência ao baixar e executar o pacote de instalação MSI a partir do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Escolha a versão mais recente, selecionando 32 bits ou 64 bits, conforme apropriado para o seu computador. O MSI também pode ser usado para atualizar um Gateway de gerenciamento de dados existente para a versão mais recente, com todas as configurações preservadas.

O gateway tem os seguintes pré-requisitos:

- As versões de sistema operacional Windows com suporte são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
- A configuração recomendada para o computador do gateway é pelo menos de 2 GHz, 4 cores, 8 GB de RAM e disco de 80 GB.
- Se o computador host hibernação, o gateway não poderá responder às solicitações de dados. Portanto, configure um plano de energia apropriado no computador antes de instalar o gateway. A instalação do gateway exibe uma mensagem se o computador está configurado para hibernação.
- Como atividade de cópia ocorre em uma frequência específico, o uso de recursos (CPU, memória) na máquina também segue o mesmo padrão com pico e tempo ocioso. Utilização de recursos também depende muito a quantidade de dados sendo movidos. Quando vários trabalhos de cópia estão em andamento você irá observar o uso do recurso subir horários de pico. Enquanto a configuração mínima listada acima é tecnicamente suficiente, talvez você queira uma configuração com mais recursos que a configuração mínima dependendo de sua carga específica para movimentação de dados.

Considere o seguinte ao configurar e usar um Gateway de gerenciamento de dados:

- Você pode instalar apenas uma instância do Gateway de gerenciamento de dados em um único computador.
- Você pode usar um único gateway para várias fontes de dados local.
- Você pode conectar vários gateways em computadores diferentes à mesma fonte de dados local.
- Configure um gateway para apenas um espaço de trabalho por vez. Gateways não podem ser compartilhados entre espaços de trabalho neste momento.
- Você pode configurar vários gateways para um único espaço de trabalho. Por exemplo, convém usar um gateway que está conectado a suas fontes de dados de teste durante o desenvolvimento e um gateway de produção quando você estiver pronto para colocar em operação.
- O gateway não precisam estar na mesma máquina como fonte de dados, mas ficar mais atenta à fonte de dados reduz o tempo para o gateway para se conectar à fonte de dados. Recomendamos que você instale o gateway em um computador diferente daquela que hospeda a fonte de dados local para que o gateway e fonte de dados não competem por recursos.
- Se você já tiver um gateway instalado no seu computador atende cenários Power BI ou fábrica de dados do Azure, instale um gateway separado para aprendizado de máquina do Azure em outro computador. 

    > [AZURE.NOTE] Você não consegue executar o Gateway de gerenciamento de dados e Power BI Gateway no mesmo computador.

- Você precisa usar o Gateway de gerenciamento de dados para aprendizado de máquina do Azure, mesmo se você estiver usando a rota expressa do Azure para outros dados. Você deve tratar sua fonte de dados como uma fonte de dados local (que está atrás de um firewall) mesmo quando você usar rota expressa e usar o Gateway de gerenciamento de dados para estabelecer a conectividade entre aprendizado de máquina e a fonte de dados. 

Você pode encontrar informações detalhadas sobre pré-requisitos de instalação, etapas de instalação e dicas de solução de problemas no artigo, [mover dados entre fontes locais e nuvem com o Gateway de gerenciamento de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), começando com a seção, [Considerações para usar o Gateway de gerenciamento de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Dados de ingresso do seu banco de dados do SQL Server no local em aprendizado de máquina do Azure


Este passo a passo, você irá configurar um Gateway de gerenciamento de dados em um espaço de trabalho de aprendizado de máquina do Azure, configurá-lo e leia os dados de um banco de dados do SQL Server local.

> [AZURE.TIP] Antes de começar, Desabilitar Bloqueador de pop-up do seu navegador para `studio.azureml.net`. Se você estiver usando o navegador Google Chrome, baixe e instale um dos vários plug-ins disponíveis no Google Chrome WebStore [Clique uma vez extensão de aplicativo](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Etapa 1: Criar um gateway

A primeira etapa é criar e configurar o gateway para acessar o banco de dados do SQL no local.

1. Faça login [Studio de aprendizado de máquina do Azure](https://studio.azureml.net/Home/) e selecione o espaço de trabalho que você deseja trabalhar.

2. Clique a lâmina **configurações** à esquerda e clique na guia **GATEWAYS dados** na parte superior.

3. Clique em **Novo GATEWAY de dados** na parte inferior da tela.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. Na caixa de diálogo **novo gateway de dados** , insira o **Nome do Gateway** e, opcionalmente, adicione uma **Descrição**. Clique na seta no canto inferior direita para ir para a próxima etapa da configuração.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. No Download e registre dados gateway diálogo, copie a chave do registro de GATEWAY para a área de transferência.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Se você ainda não tiver baixou e instalou o Gateway de gerenciamento de dados da Microsoft, clique em **Download gateway de gerenciamento de dados**. Isso leva você ao Center de Download da Microsoft, onde você pode selecionar a versão de gateway que precisar, baixá-lo e instalá-lo. Você pode encontrar informações detalhadas sobre pré-requisitos de instalação, etapas de instalação e dicas de solução de problemas nas seções início do artigo [mover dados entre fontes locais e nuvem com o Gateway de gerenciamento de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Depois que o gateway está instalado, o Gerenciador de configuração do Gateway de gerenciamento de dados será aberto e a caixa de diálogo **registrar gateway** é exibida. Colar a **Chave do registro de Gateway** que você copiou para a área de transferência e clique em **registrar**.

8. Se você já tiver um gateway instalado, execute o Gerenciador de configuração do Gateway de gerenciamento de dados, clique em **Alterar chave**, colar a  **Chave do registro de Gateway** que você copiou para a área de transferência e clique **Okey**.

9. Quando a instalação estiver concluída, a caixa de diálogo **registrar o gateway** para o Gerenciador de configuração do Microsoft Data Management Gateway é exibida. Cole a chave de registro de GATEWAY que você copiou para a área de transferência acima e clique em **registrar**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. A configuração do gateway é concluída quando os valores a seguir são definidos na guia **página inicial** no Gerenciador de configuração do Microsoft Data Management Gateway:

    - **Nome do gateway** e **nome da instância** são definidas para o nome do gateway.

    - **Registro** está definido como **registrado**.

    - **Status** está definido como **iniciado**.

    - A barra de status na parte inferior exibe **conectado ao serviço de nuvem do Gateway de gerenciamento de dados** junto com uma marca de seleção verde.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure Studio de aprendizado de máquina também é atualizado quando o registro é bem-sucedido.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. Na caixa de diálogo **baixar e registrar o gateway de dados** , clique na marca de seleção para concluir a configuração. A página de **configurações** exibe o status do gateway como "Online". No painel à direita, você encontrará status e outras informações úteis.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. Na opção Gerenciador de configuração do Gateway de gerenciamento de dados Microsoft para a guia de **certificado** . O certificado especificado nesta guia é usado para criptografia/descriptografia credenciais para o armazenamento de dados local que você especificar no portal. Este é o certificado padrão gerado. A Microsoft recomenda alterar isso para seu próprio certificado que você fizer backup do seu sistema de gerenciamento de certificado. Clique em **Alterar** para usar seu próprio certificado em vez disso.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (opcional) Se você deseja habilitar log detalhado para solucionar problemas com o gateway, no Gerenciador de configuração do Gateway de Microsoft dados gerenciamento alternar para a guia de **Diagnóstico** e marque a opção **Habilitar o registro extenso para fins de solução de problemas** . As informações de registro podem ser encontradas no Visualizador de eventos do Windows nos **Logs aplicativos e serviços**  - &gt; nó do **Gateway de gerenciamento de dados** . Você também pode usar a guia de **Diagnóstico** para testar a conexão a uma fonte de dados local usando o gateway.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Isso conclui o gateway configurar o processo de aprendizado de máquina do Azure.
Agora você está pronto para usar seus dados locais.

Você pode criar e configurar vários gateways no Studio para cada espaço de trabalho. Por exemplo, você pode ter um gateway que você deseja se conectar as fontes de dados de teste durante o desenvolvimento e um gateway diferente para suas fontes de dados de produção. Aprendizado de máquina Azure fornece flexibilidade para configurar vários gateways dependendo do seu ambiente corporativo. Atualmente, não é possível compartilhar um gateway entre espaços de trabalho e apenas um gateway pode ser instalado em um único computador. Para mais considerações ao instalar o gateway, consulte [Considerações para usar o Gateway de gerenciamento de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) no artigo, [mover dados entre fontes locais e nuvem com o Gateway de gerenciamento de dados](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Etapa 2: Use o gateway para ler dados de uma fonte de dados local

Depois de configurar o gateway, você pode adicionar um módulo de **Importar dados** para um experimento que insere os dados do banco de dados do SQL Server local.

1.  No Studio de aprendizado de máquina, selecione a guia **experiências** , clique em **+ nova** no canto inferior esquerdo e selecione **Experimento em branco** (ou selecione uma das várias experiências de amostra disponíveis).

2.  Localize e arraste o módulo de **Importar dados** para a tela experimento.

3.  Abaixo da tela, clique em **Salvar como** . Digite "Azure máquina aprendizagem local SQL Server Tutorial" para o nome do experimento, selecione o espaço de trabalho e clique na marca de seleção **Okey** .

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Clique no módulo de **Importar dados** para selecioná-lo, no painel **Propriedades** à direita da tela, selecione "Banco de dados local SQL" na lista suspensa **fonte de dados** .

5.  Selecione o **gateway dados** instalado e registrado. Você pode configurar o gateway outro selecionando "(Adicionar novo Gateway de dados...)".

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Insira o **nome do servidor de banco de dados** do SQL e o **nome do banco de dados**, juntamente com a **consulta de banco de dados** SQL que você deseja executar.

7.  Clique em **valores de Enter** em **nome de usuário e senha** e insira suas credenciais de banco de dados. Você pode usar autenticação integrada do Windows ou a autenticação do SQL Server dependendo de como seu local SQL Server está configurado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    A mensagem "valores necessários" será alterado para "conjunto de valores" com uma marca de seleção verde. Basta inserir as credenciais de uma vez, a menos que as informações de banco de dados ou a senha é alterado. Azure aprendizado de máquina usa o certificado que você forneceu quando você instalou o gateway para criptografar as credenciais na nuvem. Azure nunca armazenará credenciais locais sem criptografia.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Clique em **Executar** para executar o experimento.

Quando termina o experimento em execução, você pode visualizar os dados importados do banco de dados clicando na porta de saída do módulo **Importar dados** e selecionando **Visualizar**.

Depois de terminar de desenvolver seu experimento, você pode implantar e colocar em operação o seu modelo. Usando o serviço de execução de lote, dados do banco de dados de SQL Server local configurado no módulo **Importar dados** serão lidos e usados para pontuação. Embora você possa usar o serviço de resposta de solicitação de pontuação dados locais, recomendamos utilizar o [suplemento do Excel](machine-learning-excel-add-in-for-web-services.md) em vez disso. Atualmente, escrevendo para um local SQL Server por meio de **Exportar dados** do banco de dados não há suporte em suas experiências ou serviços web publicado.

