<properties 
    pageTitle="Contrato de móvel Azure - integração back-end" 
    description="Conectar-se o contrato de celular do Azure com um back-end do SharePoint para criar campanhas do SharePoint" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement---api-integration"></a>Contrato de móvel Azure - integração da API

Em um sistema automatizado de marketing, criando e ativando as campanhas de marketing também ocorrerem automaticamente. Para essa finalidade - Azure Mobile contrato habilita a criação de tais campanhas de marketing automatizadas usando APIs também. 

Normalmente clientes usarem a interface de front-end do contrato de celular para criar comunicados/votações etc como parte de suas campanhas de marketing. No entanto, como as campanhas de marketing se tornam desenvolvidas, há precisam aproveitar os dados bloqueados nos sistemas de back-end (como qualquer sistema CMS como o SharePoint ou sistema CRM) para que possa ser criado um pipeline totalmente automatizado que cria campanhas em contrato Mobile dinamicamente com base nos dados fluindo em dos sistemas back-end. 

![][5]

Este tutorial percorre tal cenário onde um usuário de negócios do SharePoint preenche uma lista do SharePoint com dados de marketing e um processo automatizado seleciona itens da lista e se conecta com o sistema de contrato Mobile usando as APIs REST disponíveis para criar uma campanha de marketing a partir dos dados do SharePoint. 
 
> [AZURE.IMPORTANT] Em geral, você pode usar este exemplo como ponto de partida para entender como chamar API REST Mobile contrato como ele detalha os dois principais aspectos de chamar APIs - parâmetros de autenticação e passar. 

## <a name="sharepoint-integration"></a>Integração com o SharePoint
1. Lista do SharePoint de exemplo é semelhante ao seguinte. **Título**, **categoria**, **NotificationTitle**, **mensagem** e **URL** são usados para criar o lançamento. Não há uma coluna chamada **IsProcessed** que é usado pelo processo de automação de exemplo na forma de um programa de console. Você pode executar este console programa como um WebJob do Azure para que você pode programá-la ou você pode usar o fluxo de trabalho do SharePoint ao programa criando e ativando o lançamento quando um item é inserido na lista do SharePoint diretamente. Neste exemplo, usamos o programa de console que percorre os itens na lista do SharePoint e cria anúncio no Azure Mobile contrato para cada um deles e depois a marca finalmente o sinalizador **IsProcessed** para ser verdadeiros na criação de anúncio bem-sucedido.

    ![][1]

2. Estamos usando o código do exemplo *autenticação remota no SharePoint Online usando o modelo de objeto do cliente* [aqui](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) para autenticar com a lista do SharePoint.
 
3. Uma vez autenticado, podemos percorrer os itens da lista para descobrir todos os itens criados recentemente (que terá **IsProcessed** = false). 

        static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Integração com o contrato de celular
1.  Depois que encontramos um item que requer processamento - podemos extrair as informações necessárias para criar um comunicado de item de lista e chamada `CreateAzMECampaign` criá-lo e subsequentemente `ActivateAzMECampaign` para ativá-lo. Esses são essencialmente chamadas API REST chamar no back-end do contrato de celular. 

2.  As APIs REST do Mobile contrato exigem um **cabeçalho HTTP de autorização de esquema de autenticação básica** que é composta a `ApplicationId` e o `ApiKey` que você obtém o portal do Azure. Certifique-se de que você está usando a chave da seção de **chaves de api** e *não* da seção **sdk teclas** . 

    ![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Para criar o anúncio type campanha - consulte a [documentação](https://msdn.microsoft.com/library/azure/mt683750.aspx). Você precisa certificar-se de que você está especificando a campanha `kind` como *comunicado* e a [carga](https://msdn.microsoft.com/library/azure/mt683751.aspx) e passando como FormUrlEncodedContent. 

        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Quando você tiver o lançamento criado, você verá algo semelhante ao seguinte no portal do contrato Mobile (Observe que o estado = rascunho e ativado = n/d)

    ![][3]

5. `CreateAzMECampaign`cria uma campanha de lançamento e retorna sua Id ao chamador. `ActivateAzMECampaign`requer esta Id como o parâmetro para ativar a campanha. 

        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Quando você tiver o lançamento ativado, você verá algo parecido com o seguinte no portal do contrato Mobile:

    ![][4]

7. Assim que a campanha obtém ativada, todos os dispositivos que satisfaçam o critério na campanha iniciará vendo notificações. 

8. Você também notará que o item da lista marcado com IsProcessed = false foi definida como True depois que a campanha de anúncio é criada.  

Este exemplo criado uma campanha de anúncio simples especificando principalmente as propriedades necessárias. Você pode personalizá-la tanto quanto possível a partir do portal usando as informações [aqui](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 
