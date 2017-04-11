<properties 
    pageTitle="Como configurar um serviço de nuvem (portal clássico) | Microsoft Azure" 
    description="Saiba como configurar os serviços de nuvem no Azure. Saiba como atualizar a configuração de serviço de nuvem e configurar o acesso remoto a instâncias de função." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-configure-cloud-services"></a>Como configurar os serviços de nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-configure-portal.md)
- [Azure portal clássico](cloud-services-how-to-configure.md)

Você pode configurar as configurações mais comumente usadas para um serviço de nuvem no portal de clássico do Azure. Ou, se você deseja atualizar seus arquivos de configuração diretamente, baixar um arquivo de configuração do serviço para atualizar, carregue o arquivo atualizado e atualizar o serviço de nuvem com as alterações de configuração. De qualquer forma, as atualizações de configuração são enviadas para todas as instâncias de função.

O portal de clássico Azure também permite que você habilitar [Conexão de área de trabalho remota para uma função nos serviços de nuvem do Azure](cloud-services-role-enable-remote-desktop.md)

Azure só pode garantir disponibilidade do serviço 99,95% durante as atualizações de configuração se você tiver pelo menos duas instâncias de função para cada função. Que permite que uma máquina virtual processar solicitações de cliente, enquanto o outro está sendo atualizado. Para obter mais informações, consulte [Os contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço de nuvem

1. No [portal do Azure clássico](http://manage.windowsazure.com/), clique em **Serviços de nuvem**, clique no nome do serviço de nuvem e, em seguida, clique em **Configurar**.

    ![Página de configuração](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Na página **Configurar** , você pode configurar o monitoramento, atualizar as configurações de função e escolha o sistema operacional convidado e a família para instâncias de função. 

2. **Monitoramento**, definir o nível de monitoramento detalhado ou mínimo e configurar as cadeias de caracteres de conexão de diagnóstico necessários para monitoramento detalhado.

3. Para funções de serviço (agrupadas por função), você pode atualizar as configurações a seguir:
    
    >**Configurações**  
    >Modifique os valores das diversas configurações especificadas nos elementos *ConfigurationSettings* do arquivo de configuração (.cscfg) do serviço.
    >
    >**Certificados**  
    >Altere a impressão digital do certificado que está sendo usado em criptografia SSL para uma função. Para alterar um certificado, você deve primeiro carregar o novo certificado (na página **certificados** ). Em seguida, atualize a impressão digital na cadeia de certificado exibida nas configurações de função.

4. No **sistema operacional**, você pode alterar a família de sistemas operacionais ou a versão para instâncias de função ou escolha **automática** para ativar atualizações automáticas da versão atual do sistema operacional. As configurações do sistema operacional se aplicam a funções da web e funções de trabalho, mas não afetam máquinas virtuais.

    Durante a implantação, a versão mais recente do sistema operacional é instalada em todas as instâncias de função, e os sistemas operacionais são atualizados automaticamente por padrão. 
    
    Se você precisar para seu serviço de nuvem para executar em uma versão de sistema operacional diferente devido a requisitos de compatibilidade no seu código, você pode escolher uma família de sistemas operacionais e versão. Quando você escolhe uma versão do sistema operacional específico, atualizações automática do sistema operacional para o serviço de nuvem estão suspensos. Você precisará garantir que os sistemas operacionais receber atualizações.
    
    Se você resolver problemas de compatibilidade de todos os seus aplicativos tem com a versão mais recente do sistema operacional, você pode habilitar atualizações automáticas de sistema operacional definindo a versão do sistema operacional para **automático**. 
    
    ![Configurações do sistema operacional](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para salvar suas configurações e enviá-los para as instâncias de função, clique em **Salvar**. (Clique em **Descartar** para cancelar as alterações). **Salvar** e **Descartar** são adicionados à barra de comando depois de alterar uma configuração.

## <a name="update-a-cloud-service-configuration-file"></a>Atualizar um arquivo de configuração do serviço de nuvem

1. Baixe um arquivo de configuração de serviço de nuvem (.cscfg) com a configuração atual. Na página **Configurar** para o serviço de nuvem, clique em **Download**. Em seguida, clique em **Salvar**ou em **Salvar como** para salvar o arquivo.

2. Depois de atualizar o arquivo de configuração do serviço, carregar e aplicar as atualizações de configuração:

    1. Na página **Configurar** , clique em **carregar**.
    
        ![Carregar configuração](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. No **arquivo de configuração**, use **Procurar** para selecionar o arquivo de .cscfg atualizado.
    
    3. Se seu serviço de nuvem contém as funções que têm apenas uma instância, marque a caixa de seleção **Aplicar configuração, mesmo se uma ou mais funções contêm uma única instância** para ativar as atualizações de configuração para as funções continuar.
    
        A menos que você define pelo menos duas instâncias de cada função, o Azure não garante pelo menos 99,95% disponibilidade do seu serviço de nuvem durante atualizações de configuração de serviço. Para obter mais informações, consulte [Os contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/).
    
    4. Clique em **Okey** (marca de seleção). 


## <a name="next-steps"></a>Próximas etapas

* Saiba como [implantar um serviço na nuvem](cloud-services-how-to-create-deploy.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage.md).
* [Habilitar a Conexão de área de trabalho remota para uma função em serviços de nuvem Azure](cloud-services-role-enable-remote-desktop.md)
* Configure [certificados ssl](cloud-services-configure-ssl-certificate.md).
