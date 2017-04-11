<properties 
    pageTitle="Como fazer uma chamada telefônica de Twilio (.NET) | Microsoft Azure" 
    description="Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos no .NET." 
    services="" 
    documentationCenter=".net" 
    authors="devinrader" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="05/04/2016" 
    ms.author="microsofthelp@twilio.com"/>




# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Como fazer uma chamada telefônica usando Twilio em uma função da web no Azure

Este guia demonstra como usar Twilio para fazer uma chamada de uma página da web hospedado no Azure. O aplicativo resultante pede ao usuário para valores de telefonema, conforme mostrado na seguinte captura de tela.

![Formulário de chamada Azure usando Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Pré-requisitos

Você precisará fazer o seguinte para usar o código neste tópico:

1. Adquirir uma conta de Twilio e a autenticação de token. Para começar a usar Twilio, inscreva-se em [https://www.twilio.com/try-twilio][try_twilio]. Você pode avaliar preço no [http://www.twilio.com/pricing][twilio_pricing]. Para obter informações sobre a API fornecida pelo Twilio, consulte [http://www.twilio.com/voice/api][twilio_api].
2. Adicione a biblioteca de Twilio .NET à sua função web. Consulte "para adicionar as bibliotecas de Twilio ao seu projeto de função web," neste tópico.

Você deve estar familiarizado com a criação de uma função web básico no Azure.

## <a name="howtocreateform"></a>Como: criar um formulário da web para fazer uma chamada

<a id="use_nuget"></a>Para adicionar as bibliotecas de Twilio ao seu projeto de função web:

1.  Abra sua solução no Visual Studio.
2.  Clique com botão direito **referências**.
3.  Clique em **Gerenciar pacotes do NuGet**.
4.  Clique em **Online**.
5.  Na caixa de pesquisa online, digite *twilio*.
6.  Clique em **instalar** o pacote de Twilio.

O código a seguir mostra como criar um formulário da web para recuperar dados de usuário para fazer uma chamada. Neste exemplo, uma função de web do ASP.NET chamada **TwilioCloud** é criada.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Como: criar o código para fazer a chamada
O código a seguir, que é chamado quando o usuário conclui o formulário, cria a mensagem de chamada e gera a chamada. Neste exemplo, o código é executado no manipulador de eventos onclick do botão no formulário. (Use sua conta de Twilio e a autenticação de token em vez de valores de espaço reservado atribuídos a **accountSID** e **authToken** no código a seguir).

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

A chamada é feita e o ponto de extremidade de Twilio, versão da API e o status de chamada são exibidos. A captura de tela a seguir mostra a saída de uma execução de amostra.

![Resposta de chamada Azure usando Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Mais informações sobre TwiML podem ser encontradas no [http://www.twilio.com/docs/api/twiml][twiml]. Mais informações sobre &lt;diga&gt; e outros verbos Twilio podem ser encontrados em [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Próximas etapas
Este código foi fornecido para mostrar a funcionalidade básica usando Twilio em uma função de web do ASP.NET no Azure. Antes de implantar no Azure em produção, você talvez queira adicionar mais de tratamento de erro ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, você pode usar o armazenamento de Blob do Azure ou uma instância de banco de dados do Azure SQL para armazenar números de telefone e texto de chamadas. Para obter informações sobre como usar blobs no Azure, veja [como usar o serviço de armazenamento de Blob do Azure no .NET][howto_blob_storage_dotnet]. Para obter informações sobre como usar o banco de dados SQL, veja [como usar o banco de dados do Azure SQL em aplicativos .NET][howto_sql_azure_dotnet].
* Você pode usar RoleEnvironment.getConfigurationSettings para recuperar a ID da conta Twilio e token de autenticação de definições de configuração da sua implantação, em vez de embutir valores em seu formulário. Para obter informações sobre a classe de RoleEnvironment, consulte [Namespace Microsoft.WindowsAzure.ServiceRuntime][azure_runtime_ref_dotnet].
* Leia as diretrizes de segurança de Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].
* Saiba mais sobre Twilio em [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Consulte também
* [Como usar o Twilio para voz e os recursos do SMS do Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
