<properties
   pageTitle="Causas comuns de funções de serviço de nuvem reciclagem | Microsoft Azure"
   description="Uma função de serviço de nuvem que inesperadamente reciclagem pode causar tempo de inatividade significativo. Aqui estão alguns problemas comuns que causam funções para reciclagem, que podem ajudá-lo a reduzir o tempo de inatividade."
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
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que causam funções a Lixeira

Este artigo descreve algumas das causas comuns de problemas de implantação e fornece dicas de solução para ajudá-lo a resolver esses problemas. Uma indicação de que existe um problema com um aplicativo é quando a instância da função falha ao iniciar ou -circula entre os estados ao inicializar, ocupados e interromper.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de tempo de execução ausentes

Se uma função em seu aplicativo depende de qualquer assembly que não faz parte do .NET Framework ou o Azure gerenciado biblioteca, você deve incluir explicitamente esse assembly no pacote do aplicativo. Tenha em mente que outras estruturas Microsoft não estão disponíveis no Azure por padrão. Se sua função depende de tal uma estrutura, você deve adicionar esses módulos para o pacote de aplicativo.

Antes de criar e empacotar seu aplicativo, verifique o seguinte:

- Se usando o Visual studio, verifique se que a propriedade **Cópia Local** é definida como **True** para cada assembly referenciado no seu projeto que não faz parte do SDK do Azure ou do .NET Framework.

- Verifique se que o arquivo Web. config não faz referência a quaisquer conjuntos não utilizados no elemento de compilação.

- A **Ação de compilação** de cada arquivo. cshtml está definido para o **conteúdo**. Isso garante que os arquivos aparecerão corretamente no pacote e permite que outros arquivos referenciados apareça no pacote.

## <a name="assembly-targets-wrong-platform"></a>Plataforma incorreta do assembly destinos

Azure é um ambiente de 64 bits. Portanto, conjuntos de .NET compilados para um destino de 32 bits não funcionarão no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Função emite exceções não tratadas durante a inicialização ou parar

As exceções que são geradas pelos métodos da classe [RoleEntryPoint] , que inclui o [OnStart], [OnStop]e [Executar] métodos, são exceções não tratadas. Se uma exceção não tratada ocorrer em um desses métodos, a função será Lixeira. Se a função é reciclagem repetidamente, ele pode ser lançar uma exceção não tratada sempre que ele tenta iniciar.

## <a name="role-returns-from-run-method"></a>Função retorna do método de execução

O método [Executar] destina-se a ser executada indefinidamente. Se seu código substitui o método [Executar] , ele deve dormir indefinidamente. Se o método [execute] retorna, a função reciclagem.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Configuração de DiagnosticsConnectionString incorreta

Se o aplicativo usa diagnóstico do Azure, seu arquivo de configuração do serviço deve especificar o `DiagnosticsConnectionString` configuração. Essa configuração deverá especificar uma conexão HTTPS à sua conta de armazenamento do Azure.

Para garantir que seu `DiagnosticsConnectionString` configuração está correta antes de implantar seu pacote de aplicativo no Azure, verifique o seguinte:  

- O `DiagnosticsConnectionString` definindo pontos para uma conta de armazenamento válido do Azure.  
  Por padrão, essa configuração aponta para a conta de armazenamento emulada, portanto você deve explicitamente alterar essa configuração antes de implantar o pacote de aplicativo. Se você não alterar essa configuração, uma exceção é lançada quando a instância da função tenta iniciar o monitor de diagnóstico. Isso pode causar a instância de função Lixeira indefinidamente.

- A cadeia de conexão é especificada no seguinte [formato](../storage/storage-configure-connection-string.md). (O protocolo deve ser especificado como HTTPS.) Substitua *MyAccountName* com o nome de sua conta de armazenamento e *MyAccountKey* com a chave de acesso:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se você estiver desenvolvendo seu aplicativo usando ferramentas do Azure para Microsoft Visual Studio, você pode usar as [páginas de propriedade](https://msdn.microsoft.com/library/ee405486) para definir este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificado exportado não inclui chave privada

Para executar uma função da web em SSL, você deve garantir que seu certificado de gerenciamento exportado inclui a chave privada. Se você usar o *Gerenciador de certificado do Windows* para exportar o certificado, certifique-se de selecionar **Sim** para a opção de **Exportar a chave privada** . O certificado deve ser exportado no formato PFX, que é o único formato suportado atualmente.

## <a name="next-steps"></a>Próximas etapas

Ler mais [artigos de solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Exiba mais função reciclagem cenários em [série de blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Executar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
