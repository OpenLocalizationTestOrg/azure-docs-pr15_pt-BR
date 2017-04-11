<properties 
    pageTitle="Como configurar a autenticação comum TLS para Web App" 
    description="Saiba como configurar seu aplicativo web para usar autenticação de certificado de cliente em TLS." 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Como configurar a autenticação comum TLS para Web App

## <a name="overview"></a>Visão geral ##
Você pode restringir o acesso ao seu aplicativo web Azure habilitando diferentes tipos de autenticação para ele. Uma maneira de fazer isso é autenticar usando um certificado de cliente quando a solicitação for sobre SSL/TLS. Esse mecanismo é chamado autenticação comum TLS ou certificado de cliente autenticação e este artigo em detalhes como configurar o seu aplicativo web para usar a autenticação de certificado de cliente.

> **Observação:** Se você acessar seu site em HTTP e HTTPS não, você não receberá qualquer certificado de cliente. Portanto se seu aplicativo requer certificados de cliente que você não deve permitir solicitações ao seu aplicativo sobre HTTP.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>Configurar o Web App para autenticação de certificado de cliente ##
Configurar o seu aplicativo web para exigir certificados de cliente, que você precisa adicionar a configuração de sites de clientCertEnabled para o aplicativo web e defini-lo como true. Essa configuração não está disponível atualmente por meio da experiência de gerenciamento no Portal e a API REST precisarão ser usados para fazer isso.

Você pode usar a [ferramenta de ARMClient](https://github.com/projectkudu/ARMClient) para facilitar a elaborar a chamada API REST. Após você fazer logon com a ferramenta, você precisará emita o seguinte comando:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
Substituir tudo em {} com informações para seu aplicativo web e criando um arquivo chamado enableclientcert.json com o JSON seguinte conteúdo:

> {"local": "Meu aplicativo local da Web",   
>   "propriedades": {  
>     "clientCertEnabled": true}}  

Certifique-se de alterar o valor de "local" para onde está localizado o aplicativo web por exemplo, Centro Norte dos EUA ou oeste EUA etc.

> **Observação:** Se você executar ARMClient do Powershell, você precisará de escape a @ símbolo para o arquivo JSON com uma escala de volta '.

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Acessando o certificado do cliente de seu aplicativo Web ##
Se você estiver usando o ASP.NET e configurar seu aplicativo para usar a autenticação de certificado de cliente, o certificado estarão disponível por meio da propriedade **HttpRequest.ClientCertificate** . Para outras pilhas de aplicativos, o certificado de cliente estará disponível em seu aplicativo por meio de um valor de codificação base64 no cabeçalho da solicitação "X-ARR-ClientCert". Seu aplicativo pode criar um certificado desse valor e usá-lo para fins de autenticação e a autorização em seu aplicativo.

## <a name="special-considerations-for-certificate-validation"></a>Considerações especiais de validação de certificado ##
O certificado de cliente que é enviado para o aplicativo não prossegue nenhuma validação pela plataforma Azure Web Apps. Validar este certificado é responsabilidade do aplicativo web. Aqui está o código ASP.NET de exemplo que valida propriedades de certificado para fins de autenticação.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
