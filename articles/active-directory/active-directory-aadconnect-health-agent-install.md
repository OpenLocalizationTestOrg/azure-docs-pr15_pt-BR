<properties
    pageTitle="Instalação do agente de integridade do AD conectar Azure | Microsoft Azure"
    description="Esta é a página de integridade de conectar-se do Azure AD que descreva a instalação do agente do AD FS e sincronização."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>Instalação do agente de integridade do Azure AD Connect

Este documento orienta você por meio de instalando e configurando o Azure AD conectar agentes de integridade do. Você pode baixar os agentes de [aqui](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Requisitos
A tabela a seguir é uma lista de requisitos para uso Azure AD conectar integridade.

| Requisito | Descrição|
| ----------- | ---------- |
|Azure AD Premium| Azure AD conectar Health é um recurso do Azure AD Premium e exige Azure AD Premium. </br></br>Para obter mais informações, consulte [Introdução ao Azure AD Premium](active-directory-get-started-premium.md) </br>Para iniciar uma avaliação gratuita de 30 dias, consulte [Iniciar uma avaliação.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Você deve ser um administrador global do seu Azure AD para introdução ao Azure AD conectar integridade|Por padrão, somente os administradores globais podem instalar e configurar os agentes de integridade para começar, acessar o portal e realizar operações dentro do Azure AD conectar funcionamento. Para obter mais informações, consulte [Administrando seu diretório Azure AD](active-directory-administer.md). <br><br> Usando o controle de acesso com base em função, você pode permitir acesso ao Azure AD conectar integridade para outros usuários em sua organização. Para obter mais informações, consulte [controle de acesso com base em função do Azure AD conectar integridade.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importante:** A conta usada ao instalar os agentes deve ser uma conta corporativa ou escolar. Ele não pode ser uma conta da Microsoft. Para obter mais informações, consulte [Inscreva-se no Azure como uma organização](sign-up-organization.md)
|O agente de integridade de conectar-se de AD Azure é instalado em cada servidor de destino| Azure AD conectar integridade exige uma instalação de agente em servidores de destino, para fornecer os dados que são exibidos no portal. </br></br>Por exemplo, para obter dados de sua infraestrutura de local do AD FS, o agente deve ser instalado nos servidores do AD FS, Proxy do AD FS e Proxy de aplicativo Web. Da mesma forma, para acessar dados em seu local infraestrutura do AD DS, o agente deve ser instalado em controladores de domínio. </br></br>**Importante:** A conta usada ao instalar os agentes deve ser uma conta corporativa ou escolar. Ele não pode ser uma conta da Microsoft. Para obter mais informações, consulte [Inscreva-se no Azure como uma organização](sign-up-organization.md)|
|Conectividade de saída para os pontos de extremidade do serviço Azure|Durante a instalação e tempo de execução, o agente requerem conectividade com pontos de extremidade de serviço de integridade de conectar-se do Azure AD. Se a conectividade de saída é bloqueada, certifique-se de que os seguintes pontos de extremidade são adicionados à lista de permitidos: </br></br><li>& 42;. blob.Core.Windows.NET </li><li>& 42;. Queue.Core.Windows.NET</li><li>adhsprodwus.ServiceBus.Windows.NET - porta: 5671 </li><li>https://Management.Azure.com </li><li>https://S1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.AD.msft.NET/</li><li>https://login.Windows.NET</li><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Portas do firewall no servidor executando o agente.| O agente requer as seguintes portas do firewall estar abertos na ordem para o agente para se comunicar com os pontos de extremidade do serviço de integridade do Azure AD.</br></br><li>TCP/UDP porta 443</li><li>Portas TCP/UDP 5671</li>
|Permitir que os seguintes sites se segurança aprimorada do IE está ativada| Se a segurança aprimorada IE estiver habilitada, os seguintes sites devem ser permitidos no servidor que vai tem o agent instalado.</br></br><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://login.Windows.NET</li><li>O servidor de federação para a sua organização confiável para Active Directory do Azure. Por exemplo: https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalando o Azure AD conectar agente de integridade do AD FS
Para iniciar a instalação do agente, clique duas vezes no arquivo de .exe que você baixou. Na primeira tela, clique em instalar.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health-requirements/install1.png)

Após a conclusão da instalação, clique em configurar agora.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health-requirements/install2.png)

Um prompt de comando for iniciado, seguido por alguns PowerShell que executa AzureADConnectHealthADFSAgent de registro. Quando solicitado a entrar no Azure, vá em frente e entre.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health-requirements/install3.png)


Após entrar, PowerShell continuará. Quando ela for concluída, você pode fechar PowerShell e a configuração está concluída.

Neste ponto, os serviços devem ser iniciados automaticamente permitindo que o agente de monitoramento e coletar dados. Se você não tiver atendido todos os pré-requisitos descritos nas seções anteriores, avisos são exibidos na janela do PowerShell. Certifique-se de concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir é um exemplo desses erros.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar que o agente foi instalado, procure os seguintes serviços no servidor. Se você concluiu a configuração, eles já devem estar executando. Caso contrário, eles são interrompidos até que a configuração está concluída.

- Azure AD Connect integridade AD FS diagnóstico serviço
- Serviço de obtenção de informações de integridade do AD FS do Azure AD Connect
- Azure AD Connect integridade AD FS serviço de monitoramento

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalação do agente em servidores do Windows Server 2008 R2

Etapas para servidores do Windows Server 2008 R2:

1. Certifique-se de que o servidor está executando o Service Pack 1 ou mais.
1. Desative IE ESC para instalação do agente:
1. Instale o Windows PowerShell 4.0 em cada um dos servidores com antecedência instalando o agente de integridade do AD. Para instalar o Windows PowerShell 4.0:
 - Instale o [Microsoft .NET Framework 4,5](https://www.microsoft.com/download/details.aspx?id=40779) usando o link a seguir para baixar o instalador offline.
 - Instalar o PowerShell ISE (de recursos do Windows)
 - Instalar o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instalar o Internet Explorer versão 10 ou superior no servidor. (Necessárias pelo serviço de integridade autenticar, usando suas credenciais de administrador do Azure).
1. Para obter mais informações sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Habilitar a auditoria do AD FS

> [AZURE.NOTE] Esta seção aplica-se somente para os servidores de Federação do AD FS.

Para o recurso de análise de uso coletar e analisar dados, o agente de integridade de conectar-se do Azure AD precisa as informações os Logs de auditoria do AD FS. Esses registros não estão ativados por padrão. Use os procedimentos a seguir para habilitar a auditoria do AD FS e para localizar os logs de auditoria do AD FS, nos seus servidores do AD FS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Para habilitar a auditoria do AD FS 2.0

1. Clique em **Iniciar**, aponte para **programas**, aponte para **Ferramentas administrativas**e clique em **Diretiva de segurança Local**.
2. Navegue até a pasta de **Gerenciamento de direitos de segurança Settings\Local diretivas** e clique duas vezes em Gerar auditoria de segurança.
3. Na guia **Configuração de segurança Local** , verifique se a conta de serviço 2.0 do AD FS está listada. Se não estiver presente, clique em **Adicionar usuário ou grupo** e adicioná-lo à lista e clique em **Okey**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Feche diretiva de segurança Local e, em seguida, abra o snap-in de gerenciamento. Para abrir o snap-in de gerenciamento, clique em **Iniciar**, aponte para **programas**, aponte para **Ferramentas administrativas**e clique em AD FS 2.0 gerenciamento.
6. No painel de ações, clique em Editar propriedades de serviço de Federação.
7. Na caixa de diálogo **Propriedades do serviço de Federação** , clique na guia **eventos** .
8. Marque as caixas de seleção **auditorias com êxito** e **auditorias de falha** .
9. Clique em **Okey**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para habilitar a auditoria do AD FS no Windows Server 2012 R2

1. Abra **Diretiva de segurança Local** abrindo o **Gerenciador de servidor** na tela Iniciar, ou Gerenciador de servidor na barra de tarefas da área de trabalho, clique em **Política de segurança de ferramentas/Local**.
2. Navegue até a pasta de **Segurança Settings\Local diretivas atribuição de direitos** e clique duas vezes em **Gerar auditorias de segurança**.
3. Na guia **Configuração de segurança Local** , verifique se a conta de serviço do AD FS está listada. Se não estiver presente, clique em **Adicionar usuário ou grupo** e adicioná-lo à lista e clique em **Okey**.
4. Para habilitar a auditoria, abra um prompt de comando com privilégios elevados e execute o seguinte comando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Feche **Diretiva de segurança Local**e, em seguida, abra o snap-in de **Gerenciamento do AD FS** (no Gerenciador do servidor, clique em ferramentas e selecione Gerenciamento do AD FS).
6. No painel de ações, clique em **Editar propriedades de serviço de Federação**.
7. Na caixa de diálogo Propriedades do serviço de federação, clique na guia **eventos** .
8. Marque as caixas de seleção **auditorias com êxito e falha auditorias** e clique em **Okey**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar a auditoria do AD FS logs


1. Abra o **Visualizador de eventos**.
2. Vá para Logs do Windows e selecione **segurança**.
3. À direita, clique em **Registros atuais do filtro**.
4. Em fonte de evento, selecione **Auditoria do AD FS**.

![Logs de auditoria do AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Se uma política de grupo que está desabilitando o AD FS auditoria existir, o agente de integridade de conectar-se de AD Azure é possível coletar informações. Certifique-se de que você não tiver uma política de grupo que pode ser desabilitar a auditoria.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalando o agente de integridade de conectar-se do Azure AD para sincronizar
O agente de integridade de conectar-se do Azure AD para sincronização é instalado automaticamente na versão mais recente do Azure AD Connect. Para usar o Azure AD Connect para sincronização, é necessário baixar a versão mais recente do Azure AD Connect e instalá-lo. Você pode baixar a versão mais recente [aqui](http://www.microsoft.com/download/details.aspx?id=47594).

Para verificar que o agente foi instalado, procure os seguintes serviços no servidor. Se você concluiu a configuração, eles já devem estar executando. Caso contrário, eles são interrompidos até que a configuração está concluída.

- Serviço de obtenção de informações de sincronização de integridade do Azure AD Connect
- Azure AD Connect monitorando o serviço de sincronização de integridade

![Verificar o Azure AD Connect integridade de sincronização](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Lembre-se de que usar o Azure AD conectar integridade exige Azure AD Premium. Se você não tiver o Azure AD Premium, você não consegue concluir a configuração no portal do Azure. Para obter mais informações, consulte a [página requisitos](active-directory-aadconnect-health-agent-install.md#requirements).


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Integridade de conectar manual Azure AD para o registro de sincronização
Se o Azure AD conectar integridade para o registro do agente de sincronização falhar após a instalação do Azure AD Connect com êxito, você pode usar o seguinte comando do PowerShell para registrar manualmente o agente.

>[AZURE.IMPORTANT] Usando este comando do PowerShell só é necessário se o registro do agente falha após a instalação do Azure AD Connect.

O PowerShell seguinte comando é necessário somente quando o registro do agente de integridade falha mesmo após uma instalação e configuração bem-sucedidas do Azure AD Connect. Os serviços do Azure AD conectar integridade iniciará após o agente foi registrado com êxito.

Você pode registrar manualmente o agente de integridade de conectar-se do Azure AD para sincronizar usando o seguinte comando do PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando leva seguintes parâmetros:

- AttributeFiltering: $true (padrão) - se Azure AD Connect não está sincronizando o atributo padrão definida e tenha sido personalizado para usar um conjunto de atributo filtrada. $false caso contrário.
- StagingMode: $false (padrão) - se o servidor do Azure AD Connect não estiver no modo de $true de teste se o servidor está configurado para estar no modo de teste.

Quando solicitado para autenticação, você deve usar a mesma conta de administrador global (tais como admin@domain.onmicrosoft.com) que foi usada para configurar o Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalando o Azure AD conectar agente de integridade para o AD DS
Para iniciar a instalação do agente, clique duas vezes no arquivo de .exe que você baixou. Na primeira tela, clique em instalar.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Após a conclusão da instalação, clique em configurar agora.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Um prompt de comando for iniciado, seguido por alguns PowerShell que executa AzureADConnectHealthADDSAgent de registro. Quando solicitado a entrar no Azure, vá em frente e entre.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Após entrar, PowerShell continuará. Quando ela for concluída, você pode fechar PowerShell e a configuração está concluída.

Neste ponto, os serviços devem ser iniciados automaticamente permitindo que o agente de monitoramento e coletar dados. Se você não tiver atendido todos os pré-requisitos descritos nas seções anteriores, avisos são exibidos na janela do PowerShell. Certifique-se de concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de tela a seguir é um exemplo desses erros.

![Verifique se Azure AD Connect integridade para o AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para verificar que o agente foi instalado, procure os seguintes serviços no controlador de domínio.

- Serviço de obtenção de informações de integridade AD DS do Azure AD Connect
- Azure AD Connect integridade AD DS serviço de monitoramento

Se você concluiu a configuração, esses serviços já devem estar executando. Caso contrário, eles são interrompidos até que a configuração está concluída.

![Verificar a integridade do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>Instalando o Azure AD conectar agente de integridade para o AD DS em Server Core.
Depois de instalar o arquivo de .exe, você pode concluir o processo de registro, usando o seguinte comando do PowerShell:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar o Azure AD conectar agentes de integridade para usar HTTP Proxy
Você pode configurar o Azure AD conectar agentes de integridade para trabalhar com um HTTP Proxy.

>[AZURE.NOTE]
- Usar "Netsh WinHttp definir ProxyServerAddress" não é suportado como o agente usa System.Net para fazer solicitações de web em vez do Microsoft Windows HTTP Services.
- O endereço de Http Proxy configurado é usado para passagem mensagens criptografadas do Https.
- Não há suporte para proxies autenticados (usando HTTPBasic).

### <a name="change-health-agent-proxy-configuration"></a>Configuração de Proxy do agente de integridade de alteração
Você tem as seguintes opções para configurar Azure AD conectar integridade agente para usar um HTTP Proxy.

>[AZURE.NOTE]Todos os serviços do Azure AD conectar agente de integridade devem ser reiniciados, para que as configurações de proxy a ser atualizada. Execute o seguinte comando:<br>
Restart-Service AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importar configurações de proxy existente

##### <a name="import-from-internet-explorer"></a>Importar do Internet Explorer
Configurações de proxy HTTP do Internet Explorer podem ser importadas, para ser usada pelo Azure AD conectar agentes de integridade do. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importar do WinHTTP
Configurações de proxy do WinHTTP podem ser importadas, para ser usado pelo Azure AD conectar agentes de integridade do. Em cada um dos servidores que executam o agente de integridade, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificar os endereços Proxy manualmente
Você pode especificar manualmente um servidor proxy em cada um dos servidores que executam o agente de integridade, executando o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemplo: *Set-AzureAdConnectHealthProxySettings - HttpsProxyAddress myproxyserver: 443*

- "address" pode ser um nome de servidor resolvido DNS ou um endereço IPv4
- "porta" pode ser omitida. Se omitido, então, 443 é escolhido como porta padrão.

#### <a name="clear-existing-proxy-configuration"></a>Limpar configuração de proxy existente
Você pode limpar a configuração de proxy existente executando o seguinte comando:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Ler as configurações de proxy atual
Você pode ler as configurações de proxy atualmente configurado, execute o seguinte comando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testar a conectividade para o Azure AD conectar o serviço de integridade
É possível que problemas podem surgir que causam o agente de integridade de conectar-se do Azure AD perder a conectividade com o serviço de integridade de conectar-se do Azure AD. Isto inclui problemas de rede, problemas de permissão ou vários outros motivos.

Se o agente não for possível enviar dados para o serviço de integridade de conectar-se do Azure AD por mais de duas horas, ela é indicada com o seguinte alerta no portal: "dados do serviço de integridade não são atualizados." Você pode confirmar se o afetado Azure AD Connect agente de integridade é capaz de carregar dados para o serviço de integridade de conectar-se do Azure AD executando o seguinte comando do PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

O parâmetro de função atualmente tem os seguintes valores:

- ADFS
- Sincronização
- ADICIONA

Você pode usar o sinalizador - ShowResults no comando exibir logs detalhados. Use o exemplo a seguir:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Para usar a ferramenta de conectividade, primeiro você deve concluir o registro de agente. Se você não conseguir concluir o registro do agente, certifique-se de que você atingiu todos os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) de integridade de conectar-se do Azure AD. Este teste de conectividade é executado por padrão durante o registro do agente.



## <a name="related-links"></a>Links relacionados

* [Azure AD Connect integridade](active-directory-aadconnect-health.md)
* [Azure AD Connect operações de integridade](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD conectar Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integridade perguntas Frequentes](active-directory-aadconnect-health-faq.md)
* [Histórico de versões de integridade do Azure AD Connect](active-directory-aadconnect-health-version-history.md)
