<properties 
    pageTitle="Conectar a uma conta de DocumentDB com suporte ao protocolo para MongoDB | Microsoft Azure" 
    description="Saiba como se conectar a uma conta de DocumentDB com suporte ao protocolo para MongoDB, agora disponível para visualização. Conecte usando a cadeia de caracteres de conexão do MongoDB." 
    keywords="cadeia de conexão do MongoDB"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Como se conectar a uma conta de DocumentDB com suporte ao protocolo para MongoDB

Saiba como se conectar a uma conta do Azure DocumentDB com suporte ao protocolo para MongoDB usando o formato padrão de URI de cadeia de conexão MongoDB.  

## <a name="get-the-accounts-connection-string-information"></a>Obter informações de cadeia de conexão da conta

1. Em uma nova janela, entre no [Portal do Azure](https://portal.azure.com).
2. Na barra de **Navegação à esquerda** da lâmina conta, clique em **Cadeia de caracteres de Conexão**. Para navegar para a **Conta Blade**, sobre o Jumpbar clique em **Mais serviços**, clique **DocumentDB (NoSQL)** e, em seguida, selecione a conta de DocumentDB com suporte ao protocolo para MongoDB.

    ![Captura de tela da lâmina todas as configurações](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. A lâmina de **Informações de cadeia de Conexão** é aberto e tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão previamente construído.

    ![Captura de tela da lâmina de cadeia de conexão](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de cadeia de Conexão

É importante observar que DocumentDB suporta o MongoDB conexão cadeia URI formato padrão, com alguns requisitos específicos: DocumentDB contas exigem autenticação e comunicação segura via SSL.  Assim, o formato de cadeia de conexão é:

    mongodb://username:password@host:port/[database]?ssl=true

Onde os valores desta cadeia estão disponíveis na lâmina cadeia de Conexão mostrada acima.

- Nome de usuário (obrigatório)
    - Nome da conta de DocumentDB
- Senha (obrigatória)
    - DocumentDB a senha da conta
- Host (obrigatório)
    - Conta de FQDN do DocumentDB
- Porta (obrigatória)
    - 10250
- Banco de dados (opcional)
    - O banco de dados padrão usado pela conexão
- SSL = true (obrigatório)

Por exemplo, considere a conta que mostra as informações de cadeia de Conexão acima.  Uma cadeia de conexão válida é:
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>Conectando-se com o driver c# para MongoDB
Como mencionado anteriormente, todas as contas de DocumentDB exigem autenticação e comunicação segura via SSL. Enquanto o formato URI de cadeia de conexão MongoDB suporta um ssl = parâmetro de cadeia de consulta true, trabalhando com o MongoDB c# driver requer o uso do objeto MongoClientSettings ao criar um MongoClient.  Considerando as informações de conta acima, o trecho de código a seguir mostra como conectar-se à conta e trabalhar com o banco de dados de "Tarefas".

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Próximas etapas


- Saiba como [usar MongoChef](documentdb-mongodb-mongochef.md) com uma conta de DocumentDB com protocolo suporte para MongoDB.
- Explore DocumentDB com suporte ao protocolo para MongoDB [amostras](documentdb-mongodb-samples.md).

 
