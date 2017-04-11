<properties 
    pageTitle="Atualizar os serviços de mídia após sem interrupção teclas de acesso de armazenamento | Microsoft Azure" 
    description="Este artigo oferece orientação sobre como atualizar os serviços de mídia após sem interrupção teclas de acesso de armazenamento." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako"
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>Serviços de mídia de atualização após sem interrupção teclas de acesso de armazenamento

Quando você cria uma nova conta de serviços de mídia do Azure, você também será solicitado a selecionar uma conta de armazenamento do Azure que é usada para armazenar seu conteúdo de mídia. Observe que você pode [Adicionar mais contas de armazenamento](meda-services-managing-multiple-storage-accounts.md) à sua conta de serviços de mídia.

Quando uma nova conta de armazenamento é criada, o Azure gera duas teclas de acesso para armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter suas conexões de armazenamento mais seguro, é recomendável para gerar periodicamente e girar sua chave de acesso de armazenamento. Duas teclas de acesso (primárias e secundárias) são fornecidas para habilitar manter conexões com a conta de armazenamento usando uma tecla de acesso enquanto você gerar tecla de acesso. Esse procedimento também é chamado de "sem interrupção teclas de acesso".

Serviços de mídia depende de uma chave de armazenamento fornecida a ele. Especificamente, os locators que são usados para transmitir ou baixar seus ativos dependem da tecla de acesso de armazenamento especificado. Quando uma conta de AMS é criada leva uma dependência na chave de acesso do armazenamento principal por padrão, mas como um usuário, você pode atualizar a chave de armazenamento que tem de AMS. Você deve certificar permitir que os serviços de mídia saibam qual chave usar seguindo as etapas descritas neste tópico. Além disso, sem interrupção teclas de acesso de armazenamento, você precisa certificar-se de atualizar seu locators portanto haverá sem interrupção no seu serviço de streaming (essa etapa também é descrita no tópico).

>[AZURE.NOTE]Se você tiver várias contas de armazenamento, você deve executar este procedimento com cada conta de armazenamento.
>
>Antes de executar as etapas descritas neste tópico em uma conta de produção, certifique-se de testá-los em uma conta de pré-produção.


## <a name="step-1-regenerate-secondary-storage-access-key"></a>Etapa 1: Gerar tecla de acesso de armazenamento secundário

Comece com gerando novamente a chave de armazenamento secundário. Por padrão, a chave secundária não é usada pelos serviços de mídia.  Para obter informações sobre como implementar o chaves de armazenamento, consulte [como: modo de exibição, copiar e as teclas de acesso de armazenamento gerar](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Etapa 2: Serviços de mídia de atualização para usar a nova chave de armazenamento secundário

Atualize serviços de mídia para usar a tecla de acesso de armazenamento secundário. Você pode usar um dos dois métodos a seguir para sincronizar a chave de armazenamento gerado novamente com os serviços de mídia.

- Usar o portal do Azure: para localizar o nome e a chave valores, acesse o portal do Azure e selecione sua conta. A janela configurações é exibida à direita. Na janela Configurações, selecione chaves. Dependendo de qual tecla de armazenamento desejado para os serviços de mídia para sincronizar com, selecione a chave primária sincronizar ou sincronizar botão chave secundário. Nesse caso, use a chave secundária.

- Use o gerenciamento de serviços de mídia API REST.

O exemplo de código a seguir mostra como construir a https://endpoint/**subscriptionId/serviços/serviços de*mídia/contas/accountName*/StorageAccounts/*storageAccountName*solicitação de /Key para sincronizar a chave de armazenamento especificado com os serviços de mídia. Nesse caso, o valor da chave de armazenamento secundário é usado. Para obter mais informações, consulte [como: API REST de gerenciamento de serviços do uso mídia](http://msdn.microsoft.com/library/azure/dn167656.aspx).
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

Após essa etapa, atualize locators existentes (que têm dependência a chave de armazenamento antigo) conforme mostrado na etapa seguinte.

>[AZURE.NOTE]Aguarde 30 minutos antes de executar quaisquer operações com os serviços de mídia (por exemplo, criando novas locators) para evitar que qualquer impacto em trabalhos pendentes.

##<a name="step-3-update-locators"></a>Etapa 3: Localizadores de atualização

>[AZURE.NOTE]Quando as teclas de acesso de armazenamento, você precisa certificar-se de atualizar seu locators existentes, então não há nenhuma interrupção no seu serviço de streaming.

Aguarde pelo menos 30 minutos após sincronizar a nova chave de armazenamento com AMS. Em seguida, você pode recriar sua locators OnDemand para que eles assumem dependência a chave de armazenamento especificado e manter a URL existente.

Observe que, quando você atualizar (ou recrie) um localizador SAS, a URL sempre será alterada.

>[AZURE.NOTE] Para certificar-se de que você preservar as URLs existentes das suas locators OnDemand, você precisa excluir o localizador existente e crie um novo com a mesma ID.

O exemplo de .NET abaixo mostra como recriar um localizador com a mesma ID.

particular ILocator RecreateLocator(CloudMediaContext context, ILocator locator) estático {/ / salvar propriedades de localizador existente.
var ativo = localizador. Ativo; var accessPolicy = localizador. AccessPolicy; var locatorId = localizador. ID; var startDate = localizador. Hora de início; var locatorType = localizador. Tipo; var locatorName = localizador. Nome;

Exclua localizador antigo.
Localizador. Delete ();

Se (localizador. ExpirationDateTime < = DateTime.UtcNow) {joga nova exceção (Format ("não é possível recriar o localizador Id = {0} porque seu tempo de expiração de localizador está no passado", localizador. ID)); }
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>Etapa 5: Gerar tecla de acesso do armazenamento principal

Gerar novamente a tecla de acesso do armazenamento principal. Para obter informações sobre como implementar o chaves de armazenamento, consulte [como: modo de exibição, copiar e as teclas de acesso de armazenamento gerar](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>Etapa 6: Serviços de mídia de atualização para usar a nova chave de armazenamento principal
    
Use o mesmo procedimento conforme descrito na [etapa 2](media-services-roll-storage-access-keys.md#step2) , só que desta vez sincronizar a nova tecla de acesso do armazenamento principal com a conta de serviços de mídia.

>[AZURE.NOTE]Aguarde 30 minutos antes de executar quaisquer operações com os serviços de mídia (por exemplo, criando novas locators) para evitar que qualquer impacto em trabalhos pendentes.

##<a name="step-7-update-locators"></a>Etapa 7: Localizadores de atualização  

Após 30 minutos, você pode recriar sua locators OnDemand para que eles assumem dependência a nova chave de armazenamento principal e manter a URL existente.

Use o mesmo procedimento descrito na [etapa 3](media-services-roll-storage-access-keys.md#step-3-update-locators).


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>Confirmações 

Podemos gostaria de confirmar as seguintes pessoas quem contribuíram para criar este documento: Cenk Dingiloglu, Gada Milão, Seva Titov.
