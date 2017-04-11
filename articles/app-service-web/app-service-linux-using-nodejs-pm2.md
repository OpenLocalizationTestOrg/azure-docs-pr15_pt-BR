<properties 
    pageTitle="Usar a configuração de PM2 para NodeJS nos aplicativos Web no Linux | Microsoft Azure" 
    description="Usar a configuração de PM2 para NodeJS nos aplicativos Web no Linux" 
    keywords="serviço de aplicativo do Azure, aplicativo web, nodejs, pm2, linux, oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Usar a configuração de PM2 para Node nos aplicativos Web no Linux

Se você definir a pilha de aplicativo para Node Web Apps no Linux, você obterá a opção para definir um arquivo de inicialização Node conforme mostrado na imagem abaixo.

![][1]

Você pode usá-lo como

-   Especificar o script de inicialização para o aplicativo Node (por exemplo: /bin/server.js)
-   Especificar o arquivo de configuração de PM2 usar para seu aplicativo Node (por exemplo: /foo/process.json)

 >[AZURE.NOTE] Se quiser que seus processos de nó para reiniciar automaticamente quando determinados arquivos são modificados, você precisará usar a configuração de PM2. Caso contrário, seu aplicativo não será reiniciado quando ele recebe as notificações de alteração de coisas como implantação contínua quando o código do seu aplicativo for alterado.

Você pode verificar a node [documentação do arquivo de processo](http://pm2.keymetrics.io/docs/usage/application-declaration/) para todas as opções, mas abaixo está um exemplo do que você usaria como seu arquivo de process.json

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Informações importantes nesta configuração são 

-   A propriedade "script" Especifica script de início de seu aplicativo.
-   A propriedade "instâncias" Especifica quantas instâncias do processo de nó para iniciar. Se você estiver executando o aplicativo em tamanhos de máquina virtual maiores que têm várias cores, você deseja maximizar seus recursos definindo um valor mais alto aqui.
-   A matriz "inspeção" Especifica todos os arquivos cujas alterações você deseja reiniciar seus processos de nó.
-   Para o "watch_options", você precisa atualmente especificar "usePolling" como true devido a maneira como seu conteúdo de aplicativo é montado.


## <a name="next-steps"></a>Próximas etapas ##

* [O que é o serviço de aplicativo no Linux?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png