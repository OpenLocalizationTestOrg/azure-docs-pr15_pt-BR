<properties
   pageTitle="Tamanho da pasta TEMP padrão é muito pequeno para uma função | Microsoft Azure"
   description="Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar ficar sem espaço."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Tamanho da pasta TEMP padrão é muito pequeno em uma função de web/trabalhador de serviço de nuvem

O diretório temporário padrão de uma função trabalhador ou web do serviço de nuvem tem um tamanho máximo de 100 MB, os quais podem se tornar completo em algum momento. Este artigo descreve como evitar falta de espaço para o diretório temporário.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que executar sem espaço?

As variáveis de ambiente do Windows padrão TEMP e TMP estão disponíveis para o código que é executado em seu aplicativo. TEMP e TMP apontam para um único diretório que tenha um tamanho máximo de 100 MB. Quaisquer dados armazenados nesse diretório não são mantidos no ciclo de vida do serviço de nuvem; Se as instâncias de função em um serviço de nuvem são recicladas, o diretório será limpo.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema

Implemente uma das seguintes alternativas:

- Configurar um recurso de armazenamento local e acessá-lo diretamente, em vez de usar TEMP ou TMP. Para acessar um recurso de armazenamento local de código que está em execução dentro de seu aplicativo, chame o método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) . 

- Configurar um recurso de armazenamento local e, em seguida, aponte os diretórios TEMP e TMP para apontar para o caminho do recurso armazenamento local. Essa modificação deve ser executada dentro do método [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

O exemplo de código a seguir mostra como modificar diretórios de destino de TEMP e TMP de dentro do método OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Ler um blog que descreve [como aumentar o tamanho da pasta Azure Web função ASP.NET temporária](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Ler mais [artigos de solução de problemas](/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas de função de serviço de nuvem usando dados de diagnóstico do Azure PaaS computador, assista a [série de blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
