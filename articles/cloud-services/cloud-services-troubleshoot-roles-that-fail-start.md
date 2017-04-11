<properties
   pageTitle="Solucionar problemas de funções que falham ao iniciar | Microsoft Azure"
   description="Aqui estão alguns motivos comuns por uma função de serviço de nuvem pode falhar ao iniciar. Soluções para esses problemas também são fornecidas."
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

# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Solucionar problemas de funções de serviço de nuvem que falham ao iniciar

Aqui estão alguns problemas comuns e soluções relacionados aos serviços de nuvem do Azure funções que falham ao iniciar.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs ausentes ou dependências

Não responde funções e funções que são alternando entre **ao inicializar**, **ocupado**e **Interrompendo** estados podem ser causadas por DLLs ausentes ou montagens.

Sintomas de ausente DLLs ou conjuntos podem ser:

- Sua instância de função é percorrendo **ao inicializar**, **ocupado**e **Interrompendo** estados.
- Sua instância de função foi movido para **pronto** , mas se você navegar para o aplicativo da web, a página não aparecerá.

Há vários métodos recomendados para investigar esses problemas.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas DLL ausentes em uma função da web

Quando você navegar para um site que é implantado em uma web função e o navegador exibe um erro de servidor similar ao seguinte, ela pode indicar que uma DLL está ausente.

![Erro de servidor no aplicativo '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desativando erros personalizados

Informações de erro mais completas podem ser exibidas Configurando o Web. config para a função de web configurar o modo de erro personalizada como desativado e reimplantar o serviço.

Para exibir erros mais completos sem usar a área de trabalho remota:

1. Abra a solução no Microsoft Visual Studio.

2. No **Solution Explorer**, localize o arquivo Web. config e abri-lo.

3. No arquivo Web. config, localize a seção System. Web e adicione a seguinte linha:

    ```xml
    <customErrors mode="Off" />
    ```

4. Salve o arquivo.

5. Empacotar novamente e reimplantar o serviço.

Depois que o serviço é redistribuído, você verá uma mensagem de erro com o nome da DLL ou assembly ausente.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas exibindo o erro remotamente

Você pode usar a área de trabalho remota para acessar a função e exibir informações de erro mais completas remotamente. Use as etapas a seguir para exibir os erros usando a área de trabalho remota:

1. Certifique-se de que o Azure SDK 1.3 ou posterior está instalado.

2. Durante a implantação da solução usando o Visual Studio, escolha "Configurar Desktop conexões remoto...". Para obter mais informações sobre como configurar a conexão de área de trabalho remota, consulte [Usando a área de trabalho remota com funções do Azure](../vs-azure-tools-remote-desktop-roles.md).

3. No portal do Microsoft Azure clássico, depois que a instância mostra um status de **pronto**, clique em uma das instâncias de função.

4. Clique no ícone de **conectar-se** na área de **Acesso remoto** da faixa de opções.

5. Entrar na máquina virtual usando as credenciais que foram especificadas durante a configuração de área de trabalho remota.

6. Abra uma janela de comando.

7. Tipo de `IPconfig`.

8. Observe o valor de endereço IPV4.

9. Abra o Internet Explorer.

10. Digite o endereço e o nome do aplicativo da web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegando até o site agora retornará mais explícitas mensagens de erro:

* Erro de servidor no aplicativo '/'.

* Descrição: Ocorreu uma exceção não tratada durante a execução da solicitação da web atual. Examine o rastreamento de pilha para obter mais informações sobre o erro e onde ele originou no código.

* Detalhes de exceção: System.IO.FIleNotFoundException: não foi possível carregar arquivo ou assembly ' Microsoft.WindowsAzure.StorageClient, versão = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35' ou uma de suas dependências. O sistema não consegue encontrar o arquivo especificado.

Por exemplo:

![Erro de servidor explícitas no aplicativo '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas usando o emulador de computação

Você pode usar o emulador de computação do Microsoft Azure para diagnosticar e solucionar problemas de falta de dependências e erros de Web. config.

Para obter melhores resultados usando esse método de diagnóstico, você deve usar um computador ou máquina virtual que tem uma instalação limpa do Windows. Para melhor simular o ambiente do Azure, use o Windows Server 2008 R2 x64.

1. Instale a versão autônoma do [SDK do Azure](https://azure.microsoft.com/downloads/).

2. Na máquina de desenvolvimento, crie o projeto de serviço de nuvem.

3. No Windows Explorer, navegue até a pasta bin\debug do projeto de serviço de nuvem.

4. Copie o arquivo de pasta e .cscfg de .csx para o computador que você está usando para depurar os problemas.

5. Na máquina limpa, abra uma janela Prompt de comando do Azure SDK e digite `csrun.exe /devstore:start`.

6. No prompt de comando, digite `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Quando a função for iniciado, você verá informações de erro detalhadas no Internet Explorer. Você também pode usar ferramentas de solução de problemas padrão do Windows para diagnosticar o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas usando IntelliTrace

Para trabalhador e funções da web que usem .NET Framework 4, você pode usar o [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), que está disponível no [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Siga estas etapas para implantar o serviço com o IntelliTrace habilitado:

1. Confirme se o Azure SDK 1.3 ou posterior está instalado.

2. Implante a solução usando o Visual Studio. Durante a implantação, marque a caixa de seleção **Habilitar IntelliTrace para funções .NET 4** .

3. Depois que a instância for iniciado, abra o **Server Explorer**.

4. Expanda o **Azure\\serviços de nuvem** nó e localize a implantação.

5. Expanda a implantação até ver as instâncias de função. Clique com botão direito em uma das instâncias.

6. Escolha **Exibir logs do IntelliTrace**. O **IntelliTrace resumo** será aberta.

7. Localize a seção de exceções do resumo. Se houver exceções, a seção será rotulada **Dados de exceção**.

8. Expanda os **Dados de exceção** e procurar erros de **System.IO.FileNotFoundException** similares ao seguinte:

![Dados de exceção, arquivo ausente ou assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Ausente DLLs e montagens de endereço

Para resolver erros de assembly e DLL ausente, siga estas etapas:

1. Abra a solução no Visual Studio.

2. No **Solution Explorer**, abra a pasta de **referências** .

3. Clique em assembly identificado no erro.

4. No painel **Propriedades** , localize a **propriedade cópia Local** e defina o valor como **True**.

5. Reimplante o serviço de nuvem.

Depois de verificar que todos os erros foram corrigidos, você pode implantar o serviço sem marcando a caixa de seleção **Habilitar IntelliTrace para funções .NET 4** .

## <a name="next-steps"></a>Próximas etapas

Ler mais [artigos de solução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para serviços de nuvem.

Para saber como solucionar problemas de função de serviço de nuvem usando dados de diagnóstico do Azure PaaS computador, consulte [série de blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
