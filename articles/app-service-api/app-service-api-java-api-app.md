<properties
    pageTitle="Construir e implantar um aplicativo de API Java no serviço de aplicativo do Azure"
    description="Aprenda a criar um pacote de aplicativo Java API e implantá-lo ao serviço de aplicativo do Azure."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Construir e implantar um aplicativo de API Java no serviço de aplicativo do Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar um aplicativo Java e implantá-lo aos aplicativos de API de serviço de aplicativo do Azure usando [gito]. As instruções neste tutorial podem ser seguidas em qualquer sistema operacional que é capaz de executar Java. O código neste tutorial é criado usando [Maven]. [Jax-RS] é usado para criar o serviço RESTful e é gerado com base na especificação de metadados [Swagger] usando o [Editor de Swagger].

## <a name="prerequisites"></a>Pré-requisitos

1. [Kit do desenvolvedor Java 8] \(ou posterior)
1. [Maven] instalado em sua máquina de desenvolvimento
1. [Gito] instalado em sua máquina de desenvolvimento
1. Uma assinatura paga ou [avaliação gratuita] do [Microsoft Azure]
1. Um aplicativo de teste HTTP como [carteiro]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold API usando Swagger.IO

Usando o editor de swagger.io online, você pode inserir código Swagger JSON ou YAML que representa a estrutura da sua API. Uma vez que a área de superfície de API projetada, você pode exportar código para uma variedade de plataformas e estruturas. Na próxima seção, o código de scaffolded será modificado para incluir a funcionalidade de simulação. 

Esta demonstração começará com um corpo de Swagger JSON que você vai colar no editor de swagger.io, que será usado para gerar código fazendo uso do JAX-RS para acessar um ponto de extremidade da API REST. Em seguida, você vai editar o código scaffolded para retornar dados de simulação, simular uma API REST construído sobre um mecanismo de persistência de dados.  

1. Copie o seguinte código de Swagger JSON para sua área de transferência:

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Navegue até o [Online Swagger Editor]. Uma vez lá, clique no item de menu **arquivo -> Colar JSON** .

    ![Item de menu colar JSON][paste-json]

1. Cole os contatos lista API Swagger JSON que você copiou anteriormente. 

    ![Colar código JSON para Swagger][pasted-swagger]

1. Exiba as páginas de documentação e resumo de API processado no editor. 

    ![Modo de exibição Swagger gerado Docs][view-swagger-generated-docs]

1. Selecione a opção de menu **JAX-RS-> Gerar Server** para scaffold o código de servidor que você editar posteriormente para adicionar implementação fictícia. 

    ![Gerar Item de Menu de código][generate-code-menu-item]

    Depois que o código é gerado, você vai ser fornecido um arquivo ZIP baixar. Este arquivo contém o código scaffolded pelo gerador de código de Swagger e todos associados scripts de construção. Descompacte a toda a biblioteca em uma pasta em sua estação de trabalho de desenvolvimento. 

## <a name="edit-the-code-to-add-api-implementation"></a>Editar o código para adicionar implementação API

Nesta seção, você vai substituir implementação de servidor do código gerado Swagger com seu código personalizado. O novo código retornará um entidades de lista de matrizes de contato para o cliente da chamada. 

1. Abra o arquivo de modelo de *Contact.java* , que está localizado na pasta *src/geração/java/io/swagger/modelo* , usando o [Código do Visual Studio] ou o editor de texto favorito. 

    ![Arquivo de modelo de contato aberto][open-contact-model-file]

1. Adicione o seguinte construtor para a classe de **contato** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Abra o arquivo de implementação de serviço *ContactsApiServiceImpl.java* , que está localizado na pasta *src/principal/java/io/swagger/api/implementação* , usando o [Código do Visual Studio] ou o editor de texto favorito.

    ![Arquivo de código do serviço de contato aberto][open-contact-service-code-file]

1. Substitua o código no arquivo com esse novo código para adicionar uma implementação simulação para o código do serviço. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Abra um prompt de comando e altere o diretório para a pasta raiz do seu aplicativo.

1. Execute o seguinte comando Maven para construir o código e executá-lo usando o servidor de aplicativo Jetty localmente. 

        mvn package jetty:run

1. Você deve ver a janela de comando refletir que Jetty iniciou seu código na porta 8080. 

    ![Arquivo de código do serviço de contato aberto][run-jetty-war]

1. Use [carteiro] para fazer uma solicitação para "obter todos os contatos" método de API no api/http://localhost:8080/contatos.

    ![Chamar os API de contatos][calling-contacts-api]

1. Use [carteiro] para fazer uma solicitação para o método de API "obter contato específico" localizado em http://localhost:8080/api/contatos/2.

    ![Chamar os API de contatos][calling-specific-contact-api]

1. Por fim, crie o arquivo de Java guerra (arquivo Web), executando o seguinte comando Maven em seu console. 

        mvn package war:war

1. Depois que o arquivo de guerra é criado, ele será colocado na pasta de **destino** . Navegue para a pasta de **destino** e renomeie o arquivo de guerra para **ROOT.war**. (Verifique se que a capitalização corresponde neste formato).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Por fim, execute os seguintes comandos da pasta raiz do seu aplicativo para criar uma pasta **implantar** usar para implantar o arquivo de guerra no Azure. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicar a saída para o serviço de aplicativo do Azure

Nesta seção você vai aprender a criar uma nova API App usando o Portal do Azure, preparar que App API para hospedar aplicativos Java e implantar o arquivo de guerra recém-criado serviço de aplicativo do Azure para executar o seu novo App API. 

1. Crie um novo aplicativo de API no [portal do Azure], clicando no item de menu **Novo -> Web + Mobile -> aplicativo de API** , inserindo os detalhes do seu aplicativo e, em seguida, clicando em **criar**.

    ![Criar um novo App API][create-api-app]

1. Depois que seu aplicativo de API tiver sido criado, abra blade de **configurações** do seu aplicativo e clique no item de menu de **configurações do aplicativo** . Selecione as versões mais recentes do Java das opções disponíveis, e em seguida, selecione Tomcat mais recente do menu **contêiner da Web** e clique em **Salvar**.

    ![Configurar Java na lâmina API App][set-up-java]

1. Clique no item de menu de configurações de **credenciais de implantação** e forneça um nome de usuário e a senha que você deseja usar para publicar arquivos sua API App. 

    ![Definir credenciais de implantação][deployment-credentials]

1. Clique no item de menu de configurações de **fonte de implantação** . Uma vez lá, clique no botão **Escolher fonte** , selecione a opção de **Repositório de gito Local** e clique em **Okey**. Isso criará um repositório gito em execução no Azure, que tem uma associação com sua API App. Cada vez que você confirmar o código para a ramificação *mestre* de repositório gito, seu código será publicado em sua instância de API App ao vivo. 

    ![Configurar um novo repositório gito local][select-git-repo]

1. Copie a URL do repositório gito novo para sua área de transferência. Salve esta como ele será importante em breve. 

    ![Configurar um novo repositório gito para o aplicativo][copy-git-repo-url]

1. Gito enviar o arquivo de guerra para o repositório online. Para fazer isso, navegue para a pasta de **implantação** que você criou anteriormente para que você pode facilmente confirmar o código para cima até o repositório em execução no seu serviço de aplicativo. Quando você estiver na janela do console e navega para a pasta onde se encontra a pasta webapps, execute os seguintes comandos gito para iniciar o processo e disparar uma implantação. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Depois que você emitiu a solicitação de **envio** , você será solicitado a senha que você criou anteriormente para a credencial de implantação. Depois de inserir suas credenciais, você deve ver seu portal exibir que a atualização foi implantada.

1. Se você usar novamente o carteiro para acertar o App API recentemente implantado em execução no serviço de aplicativo do Azure, você verá que o comportamento é consistente e que agora ele está retornando dados de contato como esperado e usar as alterações de código simples para o Swagger.io scaffolded código Java. 

    ![Usar a API do Java contatos REST live no Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você conseguiu iniciar com um arquivo de Swagger JSON e alguns scaffolded código Java obtido do editor de Swagger.io. A partir daí, suas alterações simples e um gito implantar processo resultou em ter um aplicativo de API funcional escrito em Java. Mostra o tutorial próxima como [consumir aplicativos da API do JavaScript clientes, usando CORS][App Service API CORS]. Tutoriais posteriores na série mostram como implementar autenticação e autorização.

Para criar nesse exemplo, você pode saber mais sobre o [SDK de armazenamento para Java] para manter os blobs JSON. Ou, você poderia usar o [Documento DB Java SDK] para salvar seus dados de contato para o banco de dados do Azure documento. 

Para obter mais informações sobre como usar Java no Azure, consulte o [Java Developer Center].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portal do Azure]: https://portal.azure.com/
[Documento Java DB SDK]: ../documentdb/documentdb-java-application.md
[avaliação gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Gito]: http://www.git-scm.com/
[Central de desenvolvedores de Java]: /develop/java/
[Kit do desenvolvedor Java 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[JAX-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Editor de Swagger on-line]: http://editor.swagger.io/
[Carteiro]: https://www.getpostman.com/
[Armazenamento SDK para Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Editor de swagger]: http://editor.swagger.io/
[Código do Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
