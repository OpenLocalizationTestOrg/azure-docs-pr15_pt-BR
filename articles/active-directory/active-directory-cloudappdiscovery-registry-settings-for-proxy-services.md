<properties 
    pageTitle="Configurações de registro de descoberta de aplicativo dos serviços de Proxy de nuvem | Microsoft Azure" 
    description="O objetivo deste tópico é fornecer as etapas que você precisa realizar para definir a porta necessária nos computadores que executam o agente de descoberta de aplicativo de nuvem." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Configurações de registro de descoberta de aplicativo de nuvem para serviços de Proxy

Por padrão, o agente de descoberta de aplicativo de nuvem é configurado para usar apenas as portas 80 ou 443. Se você estiver planejando instalar descoberta de aplicativo de nuvem em um ambiente com um servidor proxy que está usando uma porta personalizada (80 nem 443), você precisa configurar seus agentes para usar essa porta. A configuração baseia-se em uma chave do registro.


O objetivo deste tópico é fornecer as etapas que você precisa realizar para definir a porta necessária nos computadores que executam o agente de descoberta de aplicativo de nuvem.



**Para modificar a porta usada pelo computador executando o agente de descoberta de aplicativo de nuvem, execute as seguintes etapas:**


1. Inicie o editor do registro. <br> ![Executar](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Navegue até ou crie a seguinte chave do registro: <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud aplicativo Discovery\Endpoint** 

3. Crie um novo valor de **cadeia de caracteres múltipla** chamado **portas**. ![Novo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Para abrir a caixa de diálogo **Editar sequência múltipla** , clique duas vezes o valor de portas.


5. Na caixa valor de texto de dados, digite os seguintes valores e adicione todas as portas personalizadas que são usadas pela sua organização: <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Editar sequência múltipla](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Clique **Okey** para fechar a caixa de diálogo **Editar sequência múltipla** .



**Recursos adicionais**


* [Como pode descobrir aplicativos de nuvem unsanctioned que são usados em minha organização](active-directory-cloudappdiscovery-whatis.md) 


