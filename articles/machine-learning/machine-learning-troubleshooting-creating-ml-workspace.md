<properties
    pageTitle="Solução de problemas: Criar e se conectar a um espaço de trabalho de aprendizado de máquina | Microsoft Azure"
    description="Soluções para problemas comuns na criação e se conectar a um espaço de trabalho de aprendizado de máquina do Azure"
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
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guia de solução de problemas: criar e se conectar a um espaço de trabalho de aprendizado de máquina

Este guia fornece as soluções para alguns frequentemente encontrados desafios quando você estiver configurando a espaços de trabalho de aprendizado de máquina do Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Proprietário do espaço de trabalho

Quando você cria um novo espaço de trabalho de aprendizado de máquina, a identificação que você insere no campo proprietário do espaço de trabalho deve ser uma conta da Microsoft válida (antigo Windows Live ID), por exemplo, john-contoso@live.com ou john-contoso@hotmail.com. Ele não pode ser uma conta não sejam da Microsoft, como sua conta de email corporativo. Para criar uma conta gratuita da Microsoft, acesse [www.live.com](http://www.live.com).

Observe que a conta usada para entrar no portal do Azure para criar o espaço de trabalho não tem automaticamente permissão para *Abrir* espaço de trabalho, a menos que você especifique essa conta como o proprietário. Para abrir um espaço de trabalho no Studio de aprendizado de máquina, você deve estar conectado Account da Microsoft que foi definido como o proprietário do espaço de trabalho, ou você precisa receber um convite do proprietário para ingressar no espaço de trabalho. A partir do portal clássico Azure, entretanto, você pode *Gerenciar* o espaço de trabalho, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para obter mais informações sobre como gerenciar um espaço de trabalho, consulte [Gerenciar um espaço de trabalho de aprendizado de máquina do Azure].

[Gerenciar um espaço de trabalho de aprendizado de máquina do Azure]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidos

Aprendizado de máquina está disponível atualmente em um número limitado de regiões. Se sua assinatura não incluir uma dessas regiões, você poderá ver a mensagem de erro, "Você tem nenhum nas regiões permitidos."

Para solicitar que uma região seja adicionada à sua assinatura, selecione **Contate o suporte do Microsoft** a partir do Portal de clássico do Azure, escolha **cobrança** como o tipo de problema e siga os prompts para enviar sua solicitação.

![Contate o suporte da Microsoft][screen1]

## <a name="storage-account"></a>Conta de armazenamento

O serviço de aprendizado de máquina precisa de uma conta de armazenamento para armazenar dados. Você pode usar uma conta de armazenamento existente, ou você pode criar uma nova conta de armazenamento quando você cria o novo espaço de trabalho de aprendizado de máquina (se você tiver cota para criar uma nova conta de armazenamento).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Criar espaço de trabalho][screen2]

Após o novo espaço de trabalho de aprendizado de máquina é criado, você pode entrar Studio de aprendizado de máquina usando a conta da Microsoft que você especificou como o proprietário do espaço de trabalho. Se você encontrar a mensagem de erro "Espaço de trabalho não encontrada" (semelhante ao seguinte captura de tela), use as etapas a seguir para excluir cookies do seu navegador.

![Espaço de trabalho não encontrado][screen3]

**Para excluir cookies do navegador**

Se você usar o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

![Opções da Internet][screen4]

Na guia **Geral** , clique em **Excluir...**

![Guia Geral][screen5]

Na caixa de diálogo **Excluir histórico de navegação** , verifique se **que os Cookies e dados de site** está selecionada e clique em **Excluir**.

![Excluir cookies][screen6]

Depois que os cookies são excluídos, reinicie o navegador e vá para a página de [Aprendizado de máquina do Microsoft Azure](https://studio.azureml.net) . Quando for solicitado um nome de usuário e senha, insira a mesma conta da Microsoft que você especificou como o proprietário do espaço de trabalho.

Nosso objetivo é tornar a experiência de aprendizado de máquina mais contínua possível. Envie quaisquer comentários e problemas no [Fórum de aprendizado de máquina do Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para ajudar a servir melhor.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
