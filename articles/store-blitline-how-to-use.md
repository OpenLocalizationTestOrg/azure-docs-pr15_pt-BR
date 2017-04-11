<properties 
    pageTitle="Como usar Blitline para imagem processamento - Azure recurso guia" 
    description="Aprenda a usar o serviço de Blitline para processar imagens dentro de um aplicativo do Azure." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Como usar Blitline com o Azure e armazenamento do Azure

Este guia mostrará como acessar serviços Blitline e como enviar trabalhos a Blitline.

## <a name="what-is-blitline"></a>O que é Blitline?

Blitline é uma serviço que fornece processamento de imagem de nível empresarial em uma fração do preço que custaria para criá-la de processamento de imagens baseado na nuvem.

O fato é que o processamento de imagem foi feito repetidamente, geralmente recriado desde o início para cada site. Sabemos isso porque criamos-los milhões de vezes muito. Um dia, que podemos decidiu que talvez é hora nós apenas fazer isso para todos. Nós sabemos como fazer isso, para fazê-lo de forma rápida e eficiente e salvar todos trabalho enquanto isso.

Para obter mais informações, consulte [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>O que Blitline não é...

Para esclarecer o que é útil para Blitline, muitas vezes é mais fácil identificar o Blitline que não faz antes de passar para a frente.

- Blitline não tem widgets HTML para carregar imagens. Você deve ter imagens disponíveis publicamente ou com permissões restritas para Blitline alcançar.

- Blitline não faz processamento, como Aviary.com de imagem ao vivo

- Blitline não aceitar carregamentos de imagem, é possível enviar as imagens Blitline diretamente. Você deve enviados por push para o armazenamento do Azure ou outros locais Blitline suporta e, em seguida, diga-Blitline onde obtê-los.

- Blitline é amplamente paralela e não faz qualquer processamento síncrono. Significando que você deve conceder-em uma postback_url e podemos dizer quando são fizemos processamento.

## <a name="create-a-blitline-account"></a>Criar uma conta de Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Como criar um trabalho de Blitline

Blitline usa JSON para definir as ações que deseja fazer em uma imagem. Este JSON é composta de alguns campos simples.

O exemplo mais simples é da seguinte maneira:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Aqui temos JSON que o levarão a uma imagem de "src" "… boys.jpeg" e então redimensione a imagem para 240 x 140.

A ID de aplicativo é algo que você pode encontrar no seu guia **Informações de conexão** ou **Gerenciar** no Azure. É o identificador secreta que permite executar trabalhos em Blitline.

O parâmetro "Salvar" identifica informações sobre onde você quer colocar a imagem quando podemos ter processado-lo. Nesse caso simples, nós ainda não tiver definido uma. Se nenhum local for definido Blitline armazenará-localmente (e temporariamente) em um local de nuvem exclusivo. Você poderá obter nesse local do JSON retornado por Blitline quando você faz o Blitline. O identificador de "imagem" é necessário e é retornado quando identificar este particular Salvar imagem.

Você pode encontrar mais informações sobre as *funções* suportamos aqui: <http://www.blitline.com/docs/functions>

Você também pode encontrar documentação sobre as opções de trabalho aqui: <http://www.blitline.com/docs/api>

Depois que você tiver seu JSON tudo o que você precisa fazer é **POST** -lo para`http://api.blitline.com/job`

Você obterá JSON volta que esta aparência:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Isso informa que Blitline recebeu sua solicitação, ele tem colocá-lo em uma fila de processamento e quando tiver concluído a imagem ficará disponível em: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_aplicativo\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Como salvar uma imagem à sua conta de armazenamento do Azure

Se você tiver uma conta de armazenamento do Azure, você pode facilmente ter Blitline as imagens processadas por push em seu contêiner Azure. Adicionando um "azure_destination" você definir o local e permissões para Blitline por push para.

Aqui está um exemplo:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Preenchendo os valores de CAPITALIZED com seus próprios, você pode enviar este JSON para http://api.blitline.com/job e a imagem de "src" será processada com um filtro de desfoque e então enviada para você destino Azure.

###<a name="please-note"></a>Observe o seguinte:

As associações de segurança devem conter o url SAS inteiro, incluindo o nome de arquivo do arquivo de destino.

Exemplo:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Você também pode ler a última edição de documentos do armazenamento do Azure do Blitline [aqui](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Próximas etapas

Visite blitline.com para ler sobre todos os nossos outros recursos:

* Ponto de extremidade do Blitline API Docs <http://www.blitline.com/docs/api>
* Funções de Blitline API <http://www.blitline.com/docs/functions>
* Exemplos de Blitline API <http://www.blitline.com/docs/examples>
* Terceira parte Nuget biblioteca <http://nuget.org/packages/Blitline.Net>
