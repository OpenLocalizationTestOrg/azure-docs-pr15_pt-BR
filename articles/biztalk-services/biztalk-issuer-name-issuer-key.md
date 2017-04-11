<properties 
    pageTitle="Nome do emissor e chave emissor nos serviços do BizTalk | Microsoft Azure" 
    description="Saiba como recuperar o nome do emissor e chave emissor para barramento de serviço ou controle de acesso (ACS) nos serviços do BizTalk. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk serviços: Nome do emissor e chave emissor

Os serviços do Azure BizTalk usa o nome de emissor do barramento de serviço e chave emissor e o nome do emissor de controle de acesso e chave emissor. Especificamente:

Tarefa | Quais nome do emissor e chave emissor
--- | ---
Implantando seu aplicativo do Visual Studio | Nome do emissor de controle de acesso e chave emissor
Configurando o Portal de Serviços BizTalk Azure | Nome do emissor de controle de acesso e chave emissor
Criando LOB retransmissões com os serviços de adaptador do BizTalk no Visual Studio | Nome de emissor do barramento de serviço e chave emissor

Este tópico lista as etapas para recuperar o nome de emissor e chave emissor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome do emissor de controle de acesso e chave emissor
O nome do emissor de controle de acesso e a chave do emissor são usadas pelo seguinte:

- Seu aplicativo de serviço do Azure BizTalk criado no Visual Studio: para implantar com êxito seu aplicativo BizTalk Service no Visual Studio para o Azure, você insere o nome do emissor de controle de acesso e a chave do emissor. 
- O Portal de Serviços BizTalk Azure: Quando você cria um BizTalk Service e abra o Portal de serviços do BizTalk, seu nome de emissor de controle de acesso e chave emissor são automaticamente registrados para as implantações com os mesmos valores de controle de acesso.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Para copiar e colar o nome do emissor de controle de acesso e a chave do emissor

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Serviços BizTalk**.
3. Selecione seu serviço BizTalk. 
4. Selecione **As informações de Conexão** na barra de tarefas. O Namespace de controle de acesso, emissor padrão (nome do emissor) e chave padrão (chave de emissor) são listadas e podem ser copiado e colado.  

Para resumir:  
Nome do emissor = emissor padrão  
Chave emissor = chave padrão


Você também pode selecionar **Abrir Portal de gerenciamento do ACS** Obtenha os valores de controle de acesso:

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Serviços BizTalk**.
3. Selecione seu serviço BizTalk.
4. Selecione o botão de informações de Conexão e selecione **Abrir Portal de gerenciamento do ACS**.
5. No Portal em **Configurações de serviço**, selecione **Identidades de serviço**. Isso exibe sua identidade de serviço, que é o valor de nome de emissor do controle de acesso. Selecione o link de identidade do serviço para ver a senha, o que é o valor de chave emissor. Seus valores podem ser copiados.<br/><br/>
Por exemplo, de **Identidades de serviço**, consulte "proprietário". "Proprietário" é seu nome de emissor de controle de acesso. Quando você clica no link "proprietário", você verá a **senha**. Quando você clica no link "Senha", você vê o valor. Este valor de senha é sua chave de emissor de controle de acesso.  

Para resumir:  
Nome do emissor = nome de identidade do serviço  
Chave emissor = valor de senha

No painel de navegação esquerdo, você também pode selecionar o **Active Directory** para recuperar os valores de controle de acesso. 

> [AZURE.IMPORTANT]Quando um Namespace de controle de acesso é criado usando o **Active Directory**, uma identidade de serviço **não** é criada automaticamente. Quando você provisionar um BizTalk Service, um Namespace de controle de acesso, identidade do serviço denominada "proprietário" (nome do emissor), senha (chave de emissor,) e chave simétrica são criados automaticamente.<br /> 
[Como: serviço de gerenciamento de ACS usar para configurar o serviço de identidades](http://go.microsoft.com/fwlink/p/?LinkID=303942) fornece mais informações sobre identidades de serviço de controle de acesso.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome de emissor do barramento de serviço e chave emissor
Nome de emissor do barramento de serviço e chave emissor são usadas pelos serviços de adaptador BizTalk. Em seu projeto de Serviços BizTalk no Visual Studio, você pode usar os serviços de adaptador do BizTalk para se conectar a um sistema de linha de negócios (LOB) no local. Para se conectar, você cria a retransmissão LOB e insira os detalhes de sistema LOB. Ao fazer isso, você também inserir o nome do emissor de barramento de serviço e a chave do emissor.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para recuperar o nome do emissor de barramento de serviço e a chave do emissor

1. Entrar no [portal clássico Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **Barramento de serviço**.
3. Selecione seu namespace. Na barra de tarefas, selecione **Informações de Conexão**. Isso exibe o **Emissor padrão** (nome do emissor) e a **Chave padrão** (chave de emissor). Seus valores podem ser copiados.  

Para resumir:  
Nome do emissor = emissor padrão  
Chave emissor = chave padrão

## <a name="next"></a>Próximo
Tópicos de serviços do Azure BizTalk adicionais:

-  [Instalar os serviços do BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Os serviços do Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Consulte também
-  [Como: usar o serviço de gerenciamento do ACS para configurar identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk serviços: Desenvolvedor, Basic, Standard e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços do BizTalk: Portal de clássico do Azure usando de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços do BizTalk: Gráfico de Status de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Serviços BizTalk: Guias de painel, o Monitor e a escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk serviços: Backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços do BizTalk: otimização](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
