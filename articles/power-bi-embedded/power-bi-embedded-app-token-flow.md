<properties
   pageTitle="Autenticação e autorização com o Power BI inserida"
   description="Autenticação e autorização com o Power BI inserida"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Autenticação e autorização com o Power BI inserida

O serviço Power BI incorporado usa **chaves** e **Tokens de aplicativo** para autenticação e autorização, em vez de autenticação de usuário final explícitas. Nesse modelo, o seu aplicativo gerencia autenticação e autorização para seus usuários finais. Quando necessário, o seu aplicativo cria e envia os Tokens de aplicativo que informa nosso serviço para renderizar o relatório solicitado. Este design não exige seu aplicativo para usar o Active Directory do Azure para autenticação de usuário e a autorização, embora ainda seja possível.

## <a name="two-ways-to-authenticate"></a>Duas maneiras para autenticar

**Chave** - você pode usar teclas para todas as chamadas de API REST para inserida do Power BI. As teclas podem ser encontradas no **portal do Azure** clicando em **todas as configurações** e, em seguida, **as teclas de acesso**. Sempre trate sua chave como se fosse uma senha. Estas teclas tem permissões para fazer qualquer API REST a chamada em um conjunto específico de espaço de trabalho.

Para usar uma chave em uma chamada REST, adicione o cabeçalho de autorização a seguir:            

    Authorization: AppKey {your key}

**Símbolo de aplicativo** - tokens são usados para todas as solicitações de incorporação de aplicativo. Eles foram projetados para ser executado lado do cliente, para que eles estiverem restrito a um único relatório e é uma prática recomendada para definir uma hora de expiração.

Aplicativo tokens são um JWT (JSON Web Token) assinado por uma das suas chaves.

O token de seu aplicativo pode conter as seguintes declarações:

| Declaração      | Descrição        |
|--------------|------------|
| **ver**      | A versão do token de aplicativo. 0.2.0 é a versão atual.       |
| **AUD**      | O destinatário pretendido do token. Para uso Power BI incorporado: "https://analysis.windows.net/powerbi/api".  |
| **ISS**      |  Uma cadeia de caracteres que indica o aplicativo que emitiu o token.    |
| **tipo**     | O tipo de token de aplicativo que está sendo criada. Atual o único tipo suportado é **incorporar**.   |
| **WCN**      | Nome do conjunto de espaço de trabalho o token está sendo emitido para.  |
| **trabalho**      | O token de ID do espaço de trabalho está sendo emitido para.  |
| **eliminar**      | O token de ID de relatório está sendo emitido para.     |
| **nome de usuário** (opcional) |  Usado com RLS, isso é uma cadeia de caracteres que pode ajudar a identificar o usuário quando aplicando regras RLS. |
| **funções** (opcional)   |   Uma cadeia de caracteres que contém as funções para selecionar quando aplicando regras de segurança de nível de linha. Se passando mais de uma função, eles devem ser passados como uma matriz de sequência.    |
| **Exp** (opcional)    |   Indica o tempo em que o token irá expirar. Esses devem ser passados como carimbos de hora do Unix.   |
| **NBF** (opcional)    |   Indica o tempo em que o token inicia sendo válida. Esses devem ser passados como carimbos de hora do Unix.   |

Um token de aplicativo de amostra ficará assim:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Quando decodificar, ele ficará assim:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>Veja aqui como funciona o fluxo

1. Copie as teclas de API para seu aplicativo. Você pode obter as teclas no **Portal do Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Token de declarações uma declaração e tem um tempo de expiração.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Token obtém assinado com um teclas de acesso de API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Solicitações de usuário para exibir um relatório.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Token é validada com um teclas de acesso de API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI incorporado envia um relatório ao usuário.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Depois de **Inserido do Power BI** envia um relatório para o usuário, o usuário pode exibir o relatório no seu aplicativo personalizado. Por exemplo, se você importou a [amostra de analisar PBIX de dados de vendas](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), o aplicativo da web de exemplo teria esta aparência:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Consulte também
- [Introdução ao Microsoft Power BI incorporado amostra](power-bi-embedded-get-started-sample.md)
- [Cenários comuns de Microsoft Power BI inserida](power-bi-embedded-scenarios.md)
- [Introdução ao Microsoft Power BI inserida](power-bi-embedded-get-started.md)
