<properties
 pageTitle="Enviar trabalhos a um pacote de HPC cluster no Azure | Microsoft Azure"
 description="Saiba como configurar um computador local para enviar trabalhos a um cluster de HPC Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Enviar trabalhos HPC de um computador local para um cluster de HPC Pack implantado no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configure um computador de cliente local para enviar os trabalhos com um cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) no Azure. Este artigo mostra como configurar um computador local com ferramentas de cliente para enviar o trabalho por HTTPS ao cluster no Azure. Dessa forma, vários usuários de cluster podem submeter trabalhos um cluster de HPC Pack baseado em nuvem, mas sem conexão diretamente para o nó principal máquina virtual ou acessar uma assinatura do Azure.

![Enviar um trabalho para um cluster no Azure][jobsubmit]

## <a name="prerequisites"></a>Pré-requisitos

* **Nó principal HPC Pack implantado em uma máquina virtual do Azure** - recomendamos que você use ferramentas automatizadas como um [modelo de início rápido Azure](https://azure.microsoft.com/documentation/templates/) ou um [script do PowerShell do Azure](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implantar o nó principal e cluster. É necessário o nome DNS do nó principal e as credenciais de um administrador de cluster para concluir as etapas neste artigo.

* **Computador cliente** - você precisa de um computador cliente Windows ou Windows Server que pode executar utilitários de cliente HPC Pack (consulte [requisitos do sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se você quiser usar o portal da web de HPC Pack ou API REST para enviar trabalhos, você pode usar qualquer computador cliente da sua escolha.

* **Mídia de instalação do HPC Pack** - instalar os utilitários de cliente HPC Pack, o pacote de instalação gratuita para a versão mais recente do pacote de HPC (HPC Pack 2012 R2) está disponível no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Certifique-se de que você baixe a mesma versão do HPC Pack que está instalado no nó principal máquina virtual.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Etapa 1: Instalar e configurar o web components no nó principal

Para habilitar uma interface REST enviar trabalhos ao cluster em HTTPS, certifique-se de que o web components HPC Pack configurado no nó principal HPC Pack. Se já não estiver instalados, primeiro instale o web components executando o arquivo de instalação HpcWebComponents.msi. Em seguida, configure os componentes executando o script do PowerShell HPC **Set-HPCWebComponents.ps1**.

Para obter procedimentos detalhados, consulte [instalar o Microsoft HPC Pack Web Components](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Alguns modelos de início rápido Azure para HPC Pack instalar e configurar o web components automaticamente. Se você usar o [script de implantação de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para criar o cluster, você poderá, opcionalmente, instalar e configurar o web components como parte da implantação.

**Para instalar o web components**

1. Conectar-se para o nó principal máquina virtual usando as credenciais de um administrador de cluster.

2. A partir da pasta de instalação do HPC Pack, execute HpcWebComponents.msi no nó principal.

3. Siga as etapas no Assistente para instalar o web components

**Configurar o web components**

1. No nó principal, inicie HPC PowerShell como administrador.

2. Para alterar o diretório para o local do script de configuração, digite o seguinte comando:

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Para configurar a interface REST e iniciar o serviço da Web HPC, digite o seguinte comando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Quando solicitado a selecionar um certificado, escolha o certificado que corresponde ao nome DNS público do nó principal. Por exemplo, se você implantar o nó principal máquina virtual usando o modelo clássico de implantação, o nome do certificado aparência CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Se você usar o modelo de implantação do Gerenciador de recursos, o nome do certificado se parece com CN =&lt;*HeadNodeDnsName*&gt;. &lt; *região*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Selecione este certificado posteriormente ao enviar trabalhos para o nó principal de um computador local. Não selecione ou configurar um certificado que corresponde ao nome do computador do nó principal no domínio do Active Directory (por exemplo, CN =*MyHPCHeadNode.HpcAzure.local*).

5. Para configurar o portal da web para envio de trabalho, digite o seguinte comando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Após o script for concluído, parar e reiniciar o serviço do Agendador de trabalho HPC digitando os seguintes comandos:

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Etapa 2: Instalar os utilitários de cliente HPC Pack em um computador local

Se você quiser instalar os utilitários de cliente HPC Pack em seu computador, baixe os arquivos de instalação do pacote de HPC (instalação completa) no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Quando você começa a instalação, escolha a opção de instalação para o **pacote de HPC utilitários de cliente**.

Para usar as ferramentas de cliente HPC Pack para enviar trabalhos para o nó principal máquina virtual, você também precisa exportar um certificado de nó principal e instale-o no computador cliente. O certificado deve estar no. Formato CER.

**Exportar o certificado de nó principal**

1. No nó principal, adicione o snap-in de certificados para um Console de gerenciamento da Microsoft para a conta de computador Local. Para obter etapas adicionar o snap-in, consulte [Adicionar o Snap-in de certificados a um MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Na árvore do console, expanda **certificados – computador Local** > **pessoal**e, em seguida, clique em **certificados**.

3. Localize o certificado que você configurou para os componentes web HPC Pack [etapa 1: instalar e configurar o web components no nó principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por exemplo, CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. O certificado de atalho e, em seguida, clique em **Todas as tarefas** > **Exportar**.

5. No Assistente de exportação de certificado, clique em **Avançar**e verifique se **não, não exportar a chave privada** está selecionada.

6. Siga as etapas restantes do Assistente para exportar o certificado em x. 509 de binário codificado por DER (. Formato CER).


**Para importar o certificado no computador cliente**


1. Copie o certificado que você exportou de nó principal para uma pasta no computador cliente.

2. No computador cliente, execute certmgr. msc.

3. No Gerenciador de certificado, expanda **Certificados – usuário atual** > **Autoridades de certificação raiz confiáveis**, **certificados**de atalho e clique em **Todas as tarefas** > **importação**.

4. No Assistente de importação de certificado, clique em **Avançar** e siga as etapas para importar o certificado que você exportou de nó principal para o armazenamento de autoridades de certificação raiz confiável.



>[AZURE.TIP] Talvez você veja um aviso de segurança, porque a autoridade de certificação no nó principal não é reconhecida pelo computador cliente. Para fins de teste, você pode ignorar este aviso e concluir a importação de certificado.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Etapa 3: Executar teste trabalhos no cluster

Para verificar sua configuração, tente executar trabalhos no cluster no Azure do computador local. Por exemplo, você pode usar ferramentas de interface gráfica do HPC Pack ou comandos de linha de comando para enviar trabalhos ao cluster. Você também pode usar um portal baseado na web para enviar trabalhos.


**Para executar comandos de envio de trabalho no computador cliente**


1. Em um computador cliente onde os utilitários de cliente HPC Pack estiverem instalados, inicie um Prompt de comando.

2. Digite um comando de amostra. Por exemplo, para listar todos os trabalhos no cluster, digite um comando semelhante a um destes procedimentos, dependendo o nome completo do DNS do nó principal:

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    ou
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Use o nome completo do DNS do nó principal, não o endereço IP, na URL Agendador. Se você especificar o endereço IP, um erro é exibido semelhante a "o certificado do servidor precisa para fazer com que uma cadeia válida de confiança ou ser colocados em armazenamento raiz confiável."

3. Quando solicitado, digite o nome de usuário (no formulário &lt;nome_do_domínio&gt;\\&lt;UserName&gt;) e a senha de administrador de cluster HPC ou outro usuário de cluster que você configurou. Você pode optar por armazenar as credenciais localmente para mais operações de trabalho.

    Uma lista de trabalhos é exibida.


**Usar o Gerenciador de trabalhos de HPC no computador cliente**

1. Se você anteriormente não armazenar credenciais de domínio para um usuário de cluster ao enviar um trabalho, você pode adicionar as credenciais no Gerenciador de credenciais.

    a. No painel de controle no computador cliente, inicie o Gerenciador de credenciais.

    b. Clique em **Credenciais do Windows** > **Adicionar uma credencial genérica**.

    c. Especifique o endereço de Internet (por exemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt; região&gt;.cloudapp.azure.com/HpcScheduler) e o nome de usuário (&lt;nome_do_domínio&gt;\\&lt;UserName&gt;) e a senha de administrador de cluster ou outro usuário de cluster que você configurou.

2. No computador cliente, inicie o Gerenciador de trabalho HPC.

3. Na caixa de diálogo **Selecionar nó de cabeça** , digite a URL para o nó principal no Azure (por exemplo, https://&lt;HeadNodeDnsName&gt;. cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt; região&gt;. cloudapp.azure.com).

    Gerenciador de trabalhos de HPC abre e mostra uma lista de trabalhos no nó principal.

**Usar o portal da web em execução no nó principal**

1. Inicie um navegador da web no computador cliente e insira um dos seguintes endereços, dependendo do nome completo do DNS do nó principal:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    ou
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Na caixa de diálogo de segurança exibida, digite as credenciais de domínio do administrador de cluster HPC. (Você também pode adicionar outros usuários de cluster em diferentes funções. Consulte [Gerenciar usuários do Cluster](https://technet.microsoft.com/library/ff919335.aspx).)

    Portal da web abre o modo de exibição de lista de trabalho.

3. Para enviar um trabalho de exemplo que retorna a cadeia de caracteres "Olá, mundo" do cluster, clique em **nova tarefa** no painel de navegação à esquerda.

4. Na página **Novo trabalho** , em **páginas de envio**, clique em **HelloWorld**. A página de envio de trabalho é exibida.

5. Clique em **Enviar**. Se solicitado, forneça as credenciais de domínio do administrador de cluster HPC. O trabalho é enviado e a ID de trabalho aparece na página **Meus trabalhos** .

6. Para exibir os resultados do trabalho enviado, clique a ID de trabalho e, em seguida, clique em **Exibir tarefas** para exibir a saída de comando (em **saída**).

## <a name="next-steps"></a>Próximas etapas

* Você também pode enviar trabalhos ao cluster Azure com a [API do HPC Pack REST](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Se você quiser enviar trabalhos de cluster de um cliente Linux, consulte a amostra de Python no [HPC Pack 2012 R2 SDK e o código de amostra](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
