<properties
 pageTitle="Modelos de aplicativo de lógica | Microsoft Azure"
 description="Saiba como usar modelos de aplicativo lógica previamente criados para ajudá-lo a começar"
 authors="kevinlam1"
 manager="dwrede"
 editor=""
 services="app-service\logic"
 documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="klam"/>

# <a name="logic-app-templates"></a>Modelos de aplicativo de lógica

## <a name="what-are-logic-app-templates"></a>Quais são os modelos de aplicativo de lógica

Um modelo de aplicativo de lógica é um aplicativo de lógica pré-criados que você pode usar para começar rapidamente criar seu próprio fluxo de trabalho. 

Esses modelos são uma boa maneira de descobrir diversos padrões que podem ser criados usando aplicativos de lógica. Você pode usar esses modelos como-está ou modificá-los para ajustar seu cenário.

## <a name="overview-of-available-templates"></a>Visão geral dos modelos disponíveis

Existem muitos modelos disponíveis atualmente publicados na plataforma de aplicativo de lógica. Algumas categorias de exemplo, bem como o tipo de conectores usados nelas, está listado abaixo.

### <a name="enterprise-cloud-templates"></a>Modelos de nuvem corporativos
Modelos que se integram Dynamics CRM, Salesforce, caixa, Azure Blob e outros conectores às suas necessidades de nuvem corporativos. Alguns exemplos do que pode ser feito com esses modelos incluem organizando seus clientes em potencial e fazer backup de seus dados de arquivo corporativo.

### <a name="enterprise-integration-pack-templates"></a>Modelos de pacote de integração de empresa
Configurações de VETER (validar, extrair, transformar, enriquecer, rotear) canais, recebendo um X12 EDI documento sobre AS2 e transformando-o para XML, bem como mensagem X12 e AS2 manuseio.

### <a name="protocol-pattern-templates"></a>Modelos de padrão de protocolo
Estes modelos consistem em aplicativos de lógica que contêm padrões de protocolo como solicitação-resposta sobre HTTP, bem como as integrações entre FTP e SFTP. Use estes como elas existem ou como base para a criação de padrões de protocolo mais complexos.  

### <a name="personal-productivity-templates"></a>Modelos de produtividade pessoal
Padrões para ajudar a melhorar a produtividade pessoal incluem modelos que defina lembretes diárias, itens de trabalho importante se transformam em listas de tarefas pendentes e automatizam tarefas longas para baixo até uma etapa de aprovação de usuário único.

### <a name="consumer-cloud-templates"></a>Modelos de nuvem do consumidor
Modelos simples que se integram ao serviços de mídia social, como email, basicamente capaz de reforçar iniciativas de marketing de mídia social, margem de atraso e Twitter. Eles também incluem modelos como copiar nublado, que podem ajudar a aumentar a produtividade salvando o tempo gasto em tarefas repetitivas normalmente. 

## <a name="how-to-create-a-logic-app-using-a-template"></a>Como criar um aplicativo de lógica usando um modelo 

Para começar a usar um modelo de aplicativo de lógica, vá para o designer de aplicativo de lógica. Se você estiver inserindo o designer abrindo um aplicativo de lógica existente, o aplicativo de lógica carrega automaticamente no modo de exibição designer. No entanto, se estiver criando um novo aplicativo de lógica, você verá a tela abaixo.  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

Nessa tela, você pode optar por iniciar com um aplicativo de lógica em branco ou um modelo pré-criados. Se você selecionar um dos modelos, são fornecidos com informações adicionais. Neste exemplo, usamos o modelo *quando um novo arquivo é criado no Dropbox, copiá-lo no OneDrive* .  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

Se você optar por usar o modelo, basta selecione o botão *usar este modelo* . Você será solicitado a entrar suas contas com base nos quais conectores utiliza o modelo. Ou, se você tiver estabelecido uma conexão com esses conectores anteriormente, é possível selecionar continuar conforme mostrado abaixo.  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

Após estabelecer a conexão e selecionar *continuar*, o aplicativo de lógica é aberta no modo de exibição designer.  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

No exemplo acima, assim como com muitos modelos, alguns dos campos de propriedade obrigatória podem ser preenchido dentro os conectores; No entanto, alguns ainda podem exigir um valor para poder corretamente implantar o aplicativo de lógica. Se você tentar implantar sem inserir alguns dos campos ausentes, você será notificado com uma mensagem de erro.

Se quiser retornar para o Visualizador de modelo, selecione o botão de *modelos* na barra de navegação superior. Alternando para o Visualizador do modelo, você perde qualquer andamento não salvo. Antes de mudança de volta no Visualizador do modelo, você verá uma mensagem de aviso notifica sobre isso.  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>Como implantar um aplicativo de lógica criado a partir de um modelo

Quando você tiver carregado o seu modelo e feitas as alterações desejadas, selecione Salvar botão no canto superior esquerdo. Isso salva e publica seu aplicativo de lógica.  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

Se você quiser mais informações sobre como adicionar mais etapas em um modelo de aplicativo de lógica existente ou fazer edições em geral, leia mais em [criar um aplicativo de lógica](app-service-logic-create-a-logic-app.md).