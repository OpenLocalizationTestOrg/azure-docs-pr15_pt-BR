<properties 
    pageTitle="Integração suas identidades do local com o Active Directory do Azure."
    description="Este é o Azure AD Connect que descreva o que é e por que você faria usá-lo."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Autenticação multifator de construção em aplicativos personalizados (SDK)

> [AZURE.IMPORTANT]  Se você deseja baixar o SDK, você precisará criar um provedor de autenticação multifator Azure, mesmo se tiver o Azure MFA, AAD Premium ou EMS licenças.  Se você cria um provedor de autenticação multifator Azure para essa finalidade e já tem licenças, é necessário para criar o provedor com o modelo **Por usuário habilitado** e vincular o provedor para a pasta que contém as licenças Azure MFA, Azure AD Premium ou EMS.  Isso garante que você não é cobrado a menos que tenha mais usuários únicos usando o SDK do que o número de licenças que você possui.

O Azure multifator autenticação Software Development Kit (SDK) permite construir chamada telefônica e texto verificação mensagem diretamente para os processos de entrada ou transação de aplicativos em seu locatário do Azure AD.

O SDK de autenticação multifator está disponível para c#, Visual Basic (.NET), Java, Perl, PHP e Ruby. O SDK fornece uma embalagem fina ao redor de autenticação multifator. Ele inclui tudo o que precisa para escrever seu código, incluindo arquivos código-fonte comentário, arquivos de exemplo e um arquivo Leiame detalhado. Cada SDK também inclui um certificado e chave privada para criptografar transações exclusiva para seu provedor de autenticação multifator. Contanto que você tenha um provedor, você pode baixar o SDK em tantos formatos e idiomas, conforme necessário.

A estrutura das APIs no SDK autenticação multifator é bastante simple. Você fazer uma única função chamada para uma API com os parâmetros de opção de vários fatores, como o modo de verificação e dados de usuário, como o número de telefone para chamar ou o número PIN para validar. As APIs traduzem a chamada de função em solicitações de serviços da web para o serviço de autenticação baseada em nuvem do Azure multifator. Todas as chamadas devem incluir uma referência para o certificado particular que está incluído em cada SDK.

Como as APIs não tem acesso aos usuários registrado no Active Directory do Azure, você deve fornecer informações do usuário, como números de telefone e códigos PIN, em um arquivo ou banco de dados. Além disso, as APIs não fornecem recursos de gerenciamento de inscrição ou usuário, então você precisa criar esses processos em seu aplicativo.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Baixe a autenticação multifator Azure SDK

Baixar o SDK do Azure multifator requer um [Provedor de autenticação multifator Azure](multi-factor-authentication-get-started-auth-provider.md).  Isso exige uma assinatura completa Azure, mesmo se MFA do Azure, Azure AD Premium ou do pacote de mobilidade do Enterprise licenças pertencem.  Para baixar o SDK, você deve navegar até o Portal de gerenciamento de multifator por gerenciar o provedor de autenticação multifator diretamente ou clicando no link **"Acesse o portal"** na página de configurações de serviço do MFA.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Para baixar o SDK de autenticação multifator Azure de portal do Azure


1. Entre portal do Azure como administrador.
2. À esquerda, selecione Active Directory.
3. Na página do Active Directory, na parte superior, clique em **Provedores de autenticação multifator**
4. Na parte inferior, clique em **Gerenciar**
5. Isso abrirá uma nova página.  À esquerda, na parte inferior, clique em SDK.
<center>![Baixar](./media/multi-factor-authentication-sdk/download.png)</center>
6. Selecione o idioma desejado e clique em um os links de download associado.
7. Salve o download.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Para baixar o SDK de autenticação multifator Azure via as configurações de serviço


1. Entre portal do Azure como administrador.
2. À esquerda, selecione Active Directory.
3. Clique duas vezes em sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
5. Em autenticação multifator selecione **Gerenciar configurações de serviço**
![baixar](./media/multi-factor-authentication-sdk/download2.png)
6. Na página de configurações de serviços, na parte inferior da tela, clique em **Ir para o portal**.
![Baixar](./media/multi-factor-authentication-sdk/download3a.png)
7. Isso abrirá uma nova página.  À esquerda, na parte inferior, clique em SDK.
8. Selecione o idioma desejado e clique em um os links de download associado.
9. Salve o download.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Conteúdo da autenticação multifator Azure SDK
Dentro o SDK, você encontrará os seguintes itens:

- **Leiame**. Explica como usar as APIs de autenticação multifator em um aplicativo de novo ou existente.
- **Arquivos de origem** para autenticação multifator
- **Certificado de cliente** que você usa para se comunicar com o serviço de autenticação multifator
- **Chave privada** para o certificado
- **Chame resultados.** Uma lista de códigos de resultado de chamada. Para abrir esse arquivo, use um aplicativo com formatação de texto, como o WordPad. Use os códigos de resultado de chamada para testar e solucionar problemas a implementação de autenticação multifator em seu aplicativo. Eles não são códigos de status de autenticação.
- **Exemplos.** Código de amostra para uma implementação de trabalho básica de autenticação multifator.


>[AZURE.WARNING]O certificado de cliente é um certificado particular exclusivo gerado especialmente para você. Não compartilhar ou perderá esse arquivo. É sua chave para garantir a segurança de suas comunicações com o serviço de autenticação multifator.

## <a name="code-sample-standard-mode-phone-verification"></a>Código de exemplo: Verificação de telefone de modo padrão

Este exemplo de código mostra como usar as APIs no SDK de autenticação multifator do Azure para adicionar verificação de chamada de voz do modo padrão ao seu aplicativo. Modo padrão é uma chamada telefônica que o usuário responde pressionando a tecla #.

Este exemplo usa o c# 2.0 multifator autenticação SDK do .NET em um aplicativo ASP.NET básico com c# lógica de servidor, mas o processo é muito semelhante para implementações simples em outros idiomas. Porque o SDK inclui arquivos de origem, arquivos não executáveis, você pode criar os arquivos e referência-los ou incluí-las diretamente em seu aplicativo.

>[AZURE.NOTE]Ao implementar autenticação multifator, use os fatores adicionais como verificação secundária ou terceira para complementar seu método de autenticação primária. Esses métodos não foram projetados para serem usados como métodos de autenticação primária.

### <a name="code-sample-overview"></a>Visão geral de amostra de código
Este código de exemplo para um aplicativo de demonstração de web muito simples usa uma chamada telefônica com uma resposta de chave # para concluir a autenticação do usuário. Este fator de chamada telefônica é conhecido na autenticação multifator como modo padrão.

O código do lado do cliente não inclui todos os elementos específicos de autenticação multifator. Como os fatores de autenticação adicionais são independentes da autenticação primária, você pode adicioná-los sem alterar a interface de logon existente. As APIs no SDK multifator permitem personalizar a experiência do usuário, mas você não precisa alterar nada em todos os.

O código de servidor adiciona autenticação de modo padrão na etapa 2. Ele cria um objeto PfAuthParams com os parâmetros que são necessários para verificação de modo padrão: nome de usuário, número e modo e o caminho para o certificado de cliente (CertFilePath), que é requerido em cada chamada de telefone. Para ver uma demonstração de todos os parâmetros no PfAuthParams, consulte o arquivo de exemplo no SDK.

Em seguida, o código passa o objeto PfAuthParams para a função pf_authenticate(). O valor de retorno indica o sucesso ou falha da autenticação. Os parâmetros out, callStatus e identificação de erro, contêm informações de resultado de chamada adicional. Os códigos de resultado de chamada estão documentados no arquivo de resultados de chamada no SDK.

Esta implementação mínima pode ser escrita em uma apenas algumas linhas. No entanto, no código de produção, você incluiria mais sofisticadas de tratamento de erro, código de banco de dados adicionais e uma experiência de usuário avançado.

### <a name="web-client-code"></a>Código de cliente da Web

A seguir está o código do cliente da web para uma página de demonstração.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Código de servidor

O seguinte código do lado do servidor, autenticação multifator é configurada e executar na etapa 2. Modo padrão (MODE_STANDARD) é uma chamada telefônica à qual o usuário responde pressionando a tecla #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
