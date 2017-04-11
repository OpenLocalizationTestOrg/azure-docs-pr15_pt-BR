<properties
    pageTitle="Criar Azure BizTalk Services no portal do Azure | Microsoft Azure"
    description="Saiba como provisionar ou criar Azure BizTalk Services no portal do Azure; MABS, WABS"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Criar serviços BizTalk usando o portal do Azure

Crie Azure BizTalk Services no portal do Azure.

> [AZURE.TIP] Para entrar no portal do Azure, você precisará de uma conta do Azure e assinatura do Azure. Se você não tiver uma conta, você pode criar uma conta de avaliação gratuita em poucos minutos. Consulte [Azure avaliação gratuita](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Criar um serviço BizTalk
Dependendo da edição que você escolher, nem todas as configurações de BizTalk Service podem estar disponíveis.

1. Entrar no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação inferior, selecione **novo**:  
![Selecione o botão novo][NEWButton]

3. Selecione **Serviços de aplicativo** > **serviço BIZTALK** > **Criar personalizado**:  
![Selecione BizTalk Service e selecione Criar personalizado][NewBizTalkService]

4. Insira as configurações de BizTalk Service:

    <table border="1">
    <tr>
    <td><strong>Nome do serviço BizTalk</strong></td>
    <td>Você pode inserir qualquer nome mas seja específico. Alguns exemplos incluem:<br/><br/>
    <em>MyCompany</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>MyApplication</em>. biztalk.windows.net<br/><br/>". biztalk.windows.net" é adicionado automaticamente para o nome que você digitar. Isso cria uma URL que é usada para acessar seu BizTalk Service, como <strong>https://<em>myapplication</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edition</strong></td>
    <td>Se você estiver na fase de teste/desenvolvimento, escolha <strong>desenvolvedor</strong>. Se você estiver na fase de produção, use o <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">Serviços BizTalk: gráfico das edições</a> para determinar se <strong>Premium</strong>, <strong>padrão</strong>ou <strong>básica</strong> é a opção correta para seu cenário de negócios.
    </td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Selecione a região geográfica para hospedar seu BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>URL de domínio</strong></td>
    <td><strong>Opcional</strong>. Por padrão, a URL de domínio é <em>YourBizTalkServiceName</em>. biztalk.windows.net. Um domínio personalizado também pode ser inserido. Por exemplo, se seu domínio for <em>contoso</em>, você pode inserir: <br/><br/>
    <em>MyCompany</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>MyApplication</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Selecione a seta próxima.

5. Insira o armazenamento e as configurações de banco de dados:

    <table border="1">
    <tr>
    <td><strong>Conta de armazenamento de monitoramento/arquivamento</strong></td>
    <td>Selecione uma conta de armazenamento existente ou crie uma nova conta de armazenamento. <br/><br/>Se você criar uma nova conta de armazenamento, insira o <strong>Nome da conta de armazenamento</strong>.</td>
    </tr>
    <tr>
    <td><strong>Banco de dados de acompanhamento</strong></td>
    <td>Se você usar um banco de dados do SQL Azure existente, ele não pode ser usado por outro BizTalk Service. É necessário o nome de login e senha digitada quando que o servidor de banco de dados do SQL Azure foi criado.<br/><br/><strong>Dica</strong> Crie o banco de dados de rastreamento e a conta de armazenamento de monitoramento/arquivamento na mesma região como o BizTalk Service.</td>
    </tr>
    </table>
Selecione a seta próxima.

6. Insira as configurações de banco de dados:

    <table border="1">
    <tr>
    <td><strong>Nome</strong></td>
    <td>Disponível quando <strong>criar uma nova instância de banco de dados SQL</strong> está selecionada na tela anterior.
    <br/><br/>
   Insira um nome de banco de dados SQL a ser usado pelo seu BizTalk Service.</td>
    </tr>
    <tr>
    <td><strong>Servidor</strong></td>
    <td>Disponível quando <strong>criar uma nova instância de banco de dados SQL</strong> está selecionada na tela anterior.
    <br/><br/>
   Selecione um servidor de banco de dados SQL existente ou crie um novo servidor de banco de dados SQL.</td>
    </tr>
    <tr>
    <td><strong>Nome de login do servidor</strong></td>
    <td>Insira o nome de usuário de logon.</td>
    </tr>
    <tr>
    <td><strong>Senha de logon do servidor</strong></td>
    <td>Insira a senha de logon.</td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Disponível quando <strong>criar uma nova instância de banco de dados SQL</strong> está selecionada. Selecione a região geográfica para hospedar seu banco de dados do SQL.</td>
    </tr>
    </table>

Selecione a marca de seleção para concluir o assistente. O ícone de progresso aparece:  
![Ícone de progresso exibe quando concluído][ProgressComplete]

Ao concluir, o serviço de BizTalk Azure é criado e está pronto para seus aplicativos. As configurações padrão são suficientes. Se você quiser alterar as configurações padrão, selecione **Serviços BIZTALK** no painel de navegação à esquerda e selecione seu BizTalk Service. Configurações adicionais são exibidas nas [guias painel, o Monitor e a escala](biztalk-dashboard-monitor-scale-tabs.md) na parte superior.

Dependendo o estado de Service o BizTalk, existem algumas operações que não podem ser concluídas. Para obter uma lista dessas operações, vá para [Gráfico de estado dos serviços do BizTalk](biztalk-service-state-chart.md).


## <a name="post-provisioning-steps"></a>Pós-etapas de provisionamento

-  [Instalar o certificado em um computador local](#InstallCert)
-  [Adicionar um certificado pronto para produção](#AddCert)
-  [Obter o namespace do controle de acesso](#ACS)

#### <a name="InstallCert"></a>Instalar o certificado em um computador local
Como parte do BizTalk Service provisionamento, um certificado auto-assinado é criado e associado a sua assinatura BizTalk Service. Você deve baixar este certificado e instalá-lo nos computadores de onde você implantar aplicativos BizTalk Service ou envia mensagens para um ponto de extremidade do BizTalk Service.

1. Entrar no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BIZTALK** no painel de navegação à esquerda e selecione sua assinatura BizTalk Service.
3. Selecione a guia **painel** .
4. Selecione **baixar o certificado SSL**:  
![Modificar um certificado SSL][QuickGlance]
5. Clique duas vezes no certificado e execute o Assistente para instalar o certificado. Verifique se que você instalar o certificado sob o repositório de **Autoridades de certificação raiz confiável** .

#### <a name="AddCert"></a>Adicionar um certificado pronto para produção
O certificado auto-assinado que é criado automaticamente quando criar serviços BizTalk destina para uso em ambientes de desenvolvimento somente. Cenários de produção, substituí-la com um certificado pronto para produção.

1. Na guia **Painel de controle** , selecione o **Certificado SSL de atualização**.
2. Navegue até o certificado SSL particular (*CertificateName*. pfx) que inclua seu nome de BizTalk Service, insira a senha e, em seguida, clique na marca de seleção.

#### <a name="ACS"></a>Obter o namespace do controle de acesso

1. Entrar no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BIZTALK** no painel de navegação à esquerda e selecione seu BizTalk Service.
3. Na barra de tarefas, selecione **Informações de Conexão**:  
![Selecione as informações de Conexão][ACSConnectInfo]

4. Copie os valores de controle de acesso.

Quando você implanta um projeto de BizTalk Service do Visual Studio, você pode inserir este namespace de controle de acesso. Namespace de controle de acesso é criado automaticamente para o seu BizTalk Service.

Os valores de controle de acesso podem ser usados com qualquer aplicativo. Quando os serviços do Azure BizTalk é criado, esse namespace de controle de acesso controla a autenticação com sua implantação BizTalk Service. Se você quiser alterar a assinatura ou gerenciar namespace, selecione **ACTIVE DIRECTORY** no painel de navegação à esquerda e selecione seu namespace. Na barra de tarefas lista suas opções.

Clicando em **Gerenciar** para abrir o Portal de gerenciamento de controle de acesso. No Portal de gerenciamento de controle de acesso, o BizTalk Service usa **identidades de serviço**:  
![Portal de gerenciamento de controle de identidades de serviço ACS no acesso][ACSServiceIdentities]

A identidade do serviço de controle de acesso é um conjunto de credenciais que permitem que aplicativos ou clientes para autenticar diretamente com controle de acesso e receber um token.

> [AZURE.IMPORTANT]O BizTalk Service usa **proprietário** para a identidade do serviço padrão e o valor de **senha** . Se você usa o valor de chave simétrica em vez do valor de senha, o seguinte erro pode ocorrer.<br/><br/>*Não foi possível conectar para a conta de serviço de gerenciamento de controle de acesso com as credenciais especificadas*

[Gerenciando o ACS Namespace](https://msdn.microsoft.com/library/azure/hh674478.aspx) lista algumas diretrizes e recomendações.

## <a name="requirements-explained"></a>Requisitos explicados

Não se aplicam a esses requisitos para a edição livre.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>O que é necessário</strong></td>
        <td><strong>Por que você precisa</strong></td>
</tr>
<tr>
<td>Assinatura do Azure</td>
<td>A assinatura determina quem pode entrar no portal do Azure. O proprietário de conta cria a inscrição no <a HREF="https://account.windowsazure.com/Subscriptions">Azure assinaturas</a>.
<br/><br/>
A conta do Microsoft Azure pode ter várias assinaturas e pode ser gerenciada por qualquer pessoa que é permitido. Por exemplo, detentor sua conta do Azure cria uma inscrição chamada <em>BizTalkServiceSubscription</em> e fornece os administradores BizTalk dentro da sua empresa (por exemplo, ContosoBTSAdmins@live.com) acesso a essa assinatura. Neste cenário, os administradores de BizTalk entrar portal do Azure e ter direitos de administrador total para todos os serviços hospedados na assinatura, incluindo serviços de BizTalk do Azure. Os administradores de BizTalk não são os proprietários de conta do Azure e, portanto, não tem acesso a qualquer informação de cobrança.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Contas de armazenamento no portal do Azure e gerenciar assinaturas</a> fornece mais informações.
</td>
</tr>
<tr>
<td>Banco de dados do SQL Azure</td>
<td>Armazena as tabelas, exibições e procedimentos armazenados usados pelo BizTalk Service, incluindo os dados de controle.
<br/><br/>
Quando você cria um BizTalk Service, você pode usar um servidor de SQL Azure existente, banco de dados do SQL Azure, ou criar automaticamente um novo servidor ou banco de dados.
<br/><br/>
A escala de banco de dados SQL é configurada automaticamente. Normalmente, a escala padrão é suficiente para um BizTalk Service. Alterar a escala afeta preços. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">contas e cobrança no banco de dados do SQL Azure</a>
<br/><br/>
<strong>Anotações</strong>
<br/>
<ul>
<li> Quando você cria um novo Azure SQL Server e banco de dados, serviços do Azure é ativado automaticamente. O BizTalk Service requer os serviços do Azure ser habilitado.</li>
<li>Se você criar um novo banco de dados do Azure SQL em um servidor existente do SQL Azure, as regras de firewall do servidor não são alteradas. Como resultado, é possível de que outros serviços do Azure não são permitidos acesso aos bancos de dados do servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Azure namespace de controle de acesso</td>
<td>Autentica com Serviços BizTalk Azure. Quando você implanta um projeto de BizTalk Service do Visual Studio, você pode inserir este namespace de controle de acesso. Quando você cria um BizTalk Service, o namespace de controle de acesso é criado automaticamente.</td>
</tr>

<tr>
<td>Conta de armazenamento Azure</td>
<td>Fornece acesso a tabelas, blobs e filas usadas pelo seu BizTalk Service para salvar o seguinte:

<ul>
<li>Arquivos de log que monitorar o BizTalk Service. A saída de monitoramento também é exibida na guia **Monitoring** no portal do Azure.</li>
<li>Ao criar um X12 ou AS2 contrato entre parceiros, você pode habilitar o recurso de arquivamento armazenar propriedades de mensagem. Esses dados são salvos na conta de armazenamento.</li>
</ul>
<br/>
Quando você cria um BizTalk Service, você pode usar uma conta de armazenamento existente ou criar automaticamente uma nova conta de armazenamento.
<br/><br/>
As configurações de armazenamento padrão são suficientes para um BizTalk Service.
<br/><br/>
Quando você cria uma conta de armazenamento, uma chave primária e chave secundária são criados automaticamente. Estas teclas controlam o acesso à sua conta de armazenamento. O BizTalk Service usa automaticamente a chave primária.
<br/><br/>
Consulte o <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">armazenamento</a> para obter mais informações.
</td>
</tr>

<tr>
<td>Certificado SSL de particular</td>
<td>
Quando um serviço de BizTalk Azure é criado, um URL HTTPS que inclui seu nome de BizTalk Service também é criado. Essa URL é automaticamente configurado para usar um certificado autoassinado somente desenvolvimento. Para produção, é necessário um certificado SSL de particular.
<br/><br/>
<strong>Informações de certificado SSL importantes</strong>

<ul>
<li>A data de validade do certificado deve ser menor que 5 anos.</li>
<li>Todos os certificados particulares exigem uma senha. Saber essa senha e como uma prática recomendada, compartilhar essa senha com seus administradores.</li>
<li>Certificados autoassinados são usados em um ambiente de teste/desenvolvimento. Ao usar certificados autoassinados, importe o certificado para o seu armazenamento de certificado pessoal e o repositório de certificados de autoridades de certificação raiz confiável.</li>
</ul>
<br/>Ao enviar a solicitação de certificado de produção para sua autoridade de certificação, fornecer as seguintes propriedades de certificado:
<br/>

<ul>
<li><strong>Uso de chave avançado</strong>: no mínimo, o Azure BizTalk Services requer autenticação de servidor.</li>
<li><strong>Nome comum</strong>: insira o nome de domínio totalmente qualificado (FQDN) da URL do serviço BizTalk Azure. Consulte <a HREF="#BizTalk">criar um serviço BizTalk</a> neste artigo.</li>
</ul>
<br/>
Um certificado novo ou diferente pode ser adicionado após o BizTalk Service é criado.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Conexões de híbrido

Quando você cria um serviço de BizTalk Azure, na guia **Conexões híbrido** está disponível:

![Guia de conexões híbrido][HybridConnectionTab]

Híbrido conexões são usadas para se conectar a um site Azure ou serviço móvel do Azure para qualquer recurso local que usa uma porta TCP estática, como o SQL Server, MySQL, HTTP Web APIs, serviços de celular e a maioria dos serviços da Web personalizado.  Híbrido conexões e o serviço de adaptador BizTalk são diferentes. O serviço de adaptador BizTalk é usado para conectar o Azure BizTalk Services para um sistema de linha de negócios (LOB) local.

 Consulte [Conexões híbrido](integration-hybrid-connection-overview.md) para saber mais, incluindo a criar e gerenciar conexões de híbrido.


## <a name="next-steps"></a>Próximas etapas

Agora que um BizTalk Service é criado, você se familiarizar com os diferentes [Serviços BizTalk: guias Dashboard, monitorar e escala](biztalk-dashboard-monitor-scale-tabs.md). Seu BizTalk Service está pronto para seus aplicativos. Para iniciar a criação de aplicativos, vá para [Serviços de BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
- [Serviços do BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md)<br/>
- [Serviços do BizTalk: Gráfico de estado](biztalk-service-state-chart.md)<br/>
- [BizTalk serviços: Backup e restauração](biztalk-backup-restore.md)<br/>
- [Serviços do BizTalk: otimização](biztalk-throttling-thresholds.md)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](biztalk-issuer-name-issuer-key.md)<br/>
- [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Conexões de híbrido](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
