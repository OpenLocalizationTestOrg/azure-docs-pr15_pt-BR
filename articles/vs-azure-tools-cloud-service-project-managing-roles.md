<properties
   pageTitle="Gerenciar funções na nuvem Azure services projetos com o Visual Studio | Microsoft Azure"
   description="Saiba como adicionar novas funções ao seu projeto de serviço de nuvem Azure ou remover funções existentes dele usando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Gerenciar funções nos projetos de serviços de nuvem Azure com o Visual Studio

Após ter criado seu projeto de serviço de nuvem Azure, você pode adicionar novas funções a ele ou remover funções existentes dela. Você também pode importar um projeto existente e convertê-la em uma função. Por exemplo, você pode importar um aplicativo web ASP.NET e designá-la como uma função web.

## <a name="adding-or-removing-roles"></a>Adicionando ou removendo funções

**Para adicionar uma função**

No **Solution Explorer**, abra o menu de atalho para o nó **funções** no seu projeto de serviço de nuvem e escolha **Adicionar**. Você pode selecionar uma função de web existente ou trabalhador da solução atual ou criar um novo projeto de função web ou colega. Ou você pode selecionar um projeto apropriado, como um projeto de aplicativo de web ASP.NET e associá-lo a um projeto de função.

**Para remover uma associação de função**

No nó **funções** do projeto de serviço de nuvem no Solution Explorer, abra o menu de atalho para a função remover e escolha **Remover**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Removendo e adicionando funções em seu serviço de nuvem

Se você remove uma função de seu projeto de serviço de nuvem, mas decide posteriormente adicionar a função de volta para o projeto, apenas a declaração de função e atributos básicos, como informações de diagnóstico e de pontos de extremidade, serão adicionados. Sem recursos adicionais ou referências são adicionadas ao arquivo ServiceDefinition.csdef ou para o arquivo de ServiceConfiguration. Se você quiser adicionar essas informações, você precisará adicioná-la manualmente volta para esses arquivos.

Por exemplo, você pode remover uma função de serviço web e mais tarde você decidir adicionar essa função volta para sua solução. Se você fizer isso, ocorrerá um erro. Para evitar esse erro, você precisa adicionar o `<LocalResources>` elemento mostrado no seguinte XML volta para o arquivo de ServiceDefinition.csdef. Use o nome da função serviço web que você adicionou volta para o projeto como parte do nome do atributo para o **<LocalStorage>** elemento. Neste exemplo, o nome da função serviço web é **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como configurar funções no Visual Studio lendo [Configurar as funções para um serviço de nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
