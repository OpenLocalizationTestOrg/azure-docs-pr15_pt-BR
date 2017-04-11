<properties 
    pageTitle="Conhecido redes | Microsoft Azure" 
    description="Configurando redes conhecidas, você pode evitar ter endereços IP que pertencem a sua organização incluída no ins do sinal de várias geografia e ins de entrada de endereços IP com relatórios de atividades suspeitas." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Redes conhecidas


Você pode usar o acesso do Azure Active Directory e relatórios de uso para obter visibilidade a integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório melhor pode determinar onde os possíveis riscos de segurança podem estar para que eles possam planejar adequadamente a reduzir esses riscos.

É possível que os relatórios "*ins de entrada de várias geografia*" e "*ins de entrada de endereços IP com atividades suspeitas*" incorretamente sinalizar endereços IP que realmente pertencem a sua organização. 

Por exemplo, isso pode acontecer quando: 

- Um usuário em sua Belo Horizonte office entrou no remotamente seu centro de dados em San Francisco aciona o relatório "Assinar ins de várias geografia" 

- Um usuário de sua organização tenta logon várias vezes com disparadores uma senha incorreta o relatório "Entrar ins de endereços IP com atividades suspeitas" 

Para impedir a geração de relatórios de segurança muito clara de nesses casos, você deve adicionar conhecidos intervalos de endereços IP à lista de endereço IP público de sua organização.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para adicionar intervalos de endereços IP públicos de sua organização, execute as seguintes etapas: 

1.  Logon no [portal de gerenciamento do Azure](https://manage.windowsazure.com).

2.  No painel esquerdo, clique em **Active Directory**. <br><br>![Como funciona a descoberta de aplicativo de nuvem](./media/active-directory-known-networks/known-netwoks-01.png)

3.  Na guia **diretório** , selecione seu diretório.

4.  No menu na parte superior, clique em **Configurar**. <br><br>![Como funciona a descoberta de aplicativo de nuvem](./media/active-directory-known-networks/known-netwoks-02.png)

5.  Na guia Configurar, vá para **seu intervalos de endereços IP públicos organizações** <br><br>![Como funciona a descoberta de aplicativo de nuvem](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Clique em **Adicionar intervalos de endereços IP conhecido**.

7.  Adicionar seu intervalos de endereços na caixa de diálogo que aparece e clique no botão de seleção quando terminar. <br><br>![Como funciona a descoberta de aplicativo de nuvem](./media/active-directory-known-networks/known-netwoks-04.png)


**Recursos adicionais**


* [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md)
* [Suplementos de entrada de endereços IP com atividades suspeitas](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Suplementos de entrada de várias geografia](active-directory-reporting-sign-ins-from-multiple-geographies.md)


