<properties
   pageTitle="Azure AD Connect: Pré-requisitos e hardware | Microsoft Azure"
   description="Este tópico descreve os pré-requisitos e requisitos de hardware do Azure AD Connect"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Conectar os pré-requisitos para Azure AD
Este tópico descreve os pré-requisitos e os requisitos de hardware do Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, há algumas coisas que você precisa.

### <a name="azure-ad"></a>Azure AD
- Uma assinatura do Azure ou uma [assinatura de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/). Isso só é necessário para acessar o portal do Azure e não para usando o Azure AD Connect. Se você estiver usando o PowerShell ou do Office 365 não é necessário uma assinatura do Azure usar Azure AD Connect. Se você tiver uma licença do Office 365, você também pode usar o portal do Office 365. Com uma licença do Office 365 paga você também pode chegar no portal do Azure no portal do Office 365.
    - Você também pode usar a funcionalidade de visualização do Azure AD no [portal do Azure](https://portal.azure.com). Este portal não exige uma licença do Azure.
- [Adicionar e verificar o domínio](active-directory-add-domain.md) você planeja usar no Azure AD. Por exemplo, se você planeja usar contoso.com para seus usuários e verifique se esse domínio tiver sido verificado e você não estiver usando apenas o domínio padrão de contoso.onmicrosoft.com.
- Um locatário do Azure AD permite por objetos padrão 50 mil. Quando você verificar seu domínio, o limite é aumentado para 300 objetos k. Se você precisar ainda mais objetos no Azure AD você precisar abrir um caso de suporte para o limite aumentado ainda mais. Se você precisar de mais de 500 objetos k é necessário uma licença, como o Office 365, Azure AD básica, Azure AD Premium ou do pacote de mobilidade do Enterprise.

### <a name="prepare-your-on-premises-data"></a>Preparar seus dados locais
- Examine [os recursos de sincronização opcionais que você pode habilitar no Azure AD](active-directory-aadconnectsyncservice-features.md) e avaliar quais recursos que você deve ativar.

### <a name="on-premises-servers-and-environment"></a>Ambiente e os servidores de local
- O AD esquema versão floresta nível funcional e deve ser Windows Server 2003 ou posterior. Controladores de domínio podem executar qualquer versão desde os requisitos de nível de esquema e floresta forem atendidos.
- Se você planeja usar o recurso **Write-back senha** controladores de domínio deve ser no Windows Server 2008 (com SP mais recente) ou posterior. Se seus controladores de domínio estão em 2008 (pré-R2) você também deve aplicar o [hotfix KB2386717](http://support.microsoft.com/kb/2386717).
- O controlador de domínio usado pelo Azure AD deve ser gravável. Não há suporte para usar um RODC (controlador de domínio somente leitura) e Azure AD Connect não siga qualquer redirecionamentos de gravação.
- Azure AD Connect não pode ser instalado no Small Business Server ou Windows Server Essentials. O servidor deve estar usando o Windows Server standard ou melhor.
- O servidor do Azure AD Connect deve ter uma interface de gráfica completo instalado. Não há suporte para instalar em server core.
- Azure AD Connect deve ser instalado no Windows Server 2008 ou posterior. Este servidor pode ser um controlador de domínio ou um servidor membro se usando configurações express. Se você usar configurações personalizadas, o servidor também pode ser autônomo e não precisa ser parte de um domínio.
- Se você instalar o Azure AD Connect no Windows Server 2008, certifique-se aplique-o mais recente do Windows Update. A instalação não é possível começar com um servidor sem patch.
- Se você planeja usar o recurso de **sincronização de senha**, o servidor do Azure AD Connect deve ser no Windows Server 2008 R2 SP1 ou posterior.
- O servidor do Azure AD Connect deve ter [.NET Framework 4.5.1](#component-prerequisites) ou posterior e [Microsoft PowerShell 3.0](#component-prerequisites) ou posterior instalado.
- Se os serviços de Federação do Active Directory está sendo implantado, os servidores onde estão instalados do AD FS ou Proxy de aplicativo Web devem ser Windows Server 2012 R2 ou posterior. [Gerenciamento remoto do Windows](#windows-remote-management) deve estar habilitado nesses servidores para a instalação remota.
- Se os serviços de Federação do Active Directory está sendo implantado, você precisa de [Certificados SSL](#ssl-certificate-requirements).
- Se os serviços de Federação do Active Directory está sendo implantado, você precisa configurar a [resolução de nome](#name-resolution-for-federation-servers).
- Azure AD Connect requer um banco de dados do SQL Server para armazenar dados de identidade. Por padrão, um SQL Server 2012 Express LocalDB (uma versão light do SQL Server Express) é instalado e a conta de serviço do serviço é criada no computador local. SQL Server Express tem um limite de tamanho de 10GB que permite que você gerencie aproximadamente 100.000 objetos. Se você precisar gerenciar um volume maior de objetos de diretório, você precisa apontar o Assistente de instalação para uma instalação diferente do SQL Server.
- Se você usa um SQL Server separado, aplicam esses requisitos:
    - Azure AD Connect suporta todas as versões do Microsoft SQL Server do SQL Server 2008 (com SP4) para SQL Server 2014. Banco de dados SQL do Microsoft Azure está **não têm suporte** como um banco de dados.
    - Você deve usar um agrupamento SQL maiusculas de minúsculas. Estes são identificados com um \_CI_ em seu nome. É **não têm suporte** para usar um agrupamento diferencia maiusculas de minúsculas, identificado por \_CS_ em seu nome.
    - Você só pode ter um mecanismo de sincronização por instância de banco de dados. É **não têm suporte** para compartilhar a instância de banco de dados com a sincronização FIM/MIM, DirSync ou Azure AD Sync.

### <a name="accounts"></a>Contas
- Uma conta de Administrador Global do Azure AD para o diretório do Azure AD que você deseja integrar. Isso deve ser uma **conta da escola ou organização** e não pode ser uma **conta da Microsoft**.
- Se você usa configurações de express ou atualizar de DirSync, você deve ter uma conta de administrador da empresa para seu Active Directory local.
- [Contas no Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) se você usar o caminho de instalação de configurações personalizadas.

### <a name="azure-ad-connect-server-configuration"></a>Configuração de servidor do Azure AD Connect
- Se os administradores globais tiverem MFA habilitado, a URL **https://secure.aadcdn.microsoftonline-p.com** deve ser na lista de sites confiáveis. Você será solicitado para adicionar à lista de sites confiáveis se não for adicionado antes que você seja solicitado para um desafio MFA. Você pode usar o Internet Explorer para adicioná-lo aos seus sites confiáveis.

### <a name="connectivity"></a>Conectividade
- O servidor do Azure AD Connect precisa resolução DNS para intranet e internet. O servidor DNS deve ser capaz de resolver nomes tanto para seu local Active Directory, bem como os pontos de extremidade do Azure AD.
- Se você tiver firewalls na Intranet e você precisar abrir portas entre servidores do Azure AD Connect e controladores de domínio, consulte [Portas de conexão do Azure AD](active-directory-aadconnect-ports.md) para obter mais informações.
- Se seu proxy ou firewall limite que podem ser acessadas URLs, então as URLs documentadas em [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) deve ser aberto.
    - Se você estiver usando o Microsoft Cloud na Alemanha ou na nuvem do Microsoft Azure Government, consulte [Considerações sobre instâncias de serviço de sincronização do Azure AD Connect](active-directory-aadconnect-instances.md) para URLs.
- Azure AD Connect é por padrão usando TLS 1.0 para se comunicar com o Azure AD. Você pode alterar isso para TLS 1.2 seguindo as etapas em [habilitar TLS 1.2 para Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
- Se você estiver usando um proxy de saída para conexão com a Internet, a seguinte configuração no arquivo **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** deve ser adicionada para o Assistente de instalação e a sincronização do Azure AD Connect sejam capazes de se conectar à Internet e Azure AD. Este texto deve ser inserido na parte inferior do arquivo. Nesse código, &lt;PROXYADRESS&gt; representa o nome de host ou endereço IP do proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Se seu servidor proxy requer autenticação, a [conta de serviço](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) deve estar localizada no domínio e você deve usar o caminho de instalação de configurações personalizadas para especificar uma [conta de serviço personalizado](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). Você também precisa uma alteração diferentes Machine. config. Com esta alteração no Machine. config o Assistente de instalação e o mecanismo de sincronização respondem a solicitações de autenticação do servidor proxy. No Assistente de instalação de todas as páginas, excluindo a página de **Configurar** , o assinado em credenciais do usuário são usadas. Na página **Configurar** no final do Assistente de instalação, o contexto é alternado para a [conta de serviço](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) que foi criado por você. A seção config deve ter esta aparência.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Consulte o MSDN para obter mais informações sobre o [proxy padrão do elemento](https://msdn.microsoft.com/library/kd3cf2ex.aspx).

Se você tiver problemas de conectividade, consulte [Solucionar problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Outros
- Opcional: Uma teste conta de usuário para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos de componente

### <a name="powershell-and-net-framework"></a>PowerShell e .net Framework
Azure AD Connect depende Microsoft PowerShell e .NET Framework 4.5.1. Você precisa nesta versão ou uma versão posterior instalada no seu servidor. Dependendo da sua versão do Windows Server, faça o seguinte:

- Windows Server 2012R2
  - Microsoft PowerShell é instalado por padrão, nenhuma ação é necessária.
  - .NET framework 4.5.1 e versões posteriores são oferecidas através do Windows Update. Verifique se que você instalou as últimas atualizações para o Windows Server no painel de controle.
- 2008R2 do Windows Server e o Windows Server 2012
  - Versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4.0**, disponíveis no [Centro de Download da Microsoft](http://www.microsoft.com/downloads).
  - .NET framework 4.5.1 e versões posteriores estão disponíveis no [Centro de Download da Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - A versão mais recente do PowerShell com suporte está disponível no **Windows Management Framework 3.0**, disponíveis no [Centro de Download da Microsoft](http://www.microsoft.com/downloads).
 - .NET framework 4.5.1 e versões posteriores estão disponíveis no [Centro de Download da Microsoft](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitar TLS 1.2 para Azure AD Connect
Azure AD Connect está usando TLS 1.0 por padrão para criptografar a comunicação entre o servidor do mecanismo de sincronização e o Azure AD. Você pode alterar isso configurando aplicativos .net usar TLS 1.2 por padrão no servidor. Mais informações sobre TLS 1.2 podem ser encontradas no [2960358 de aviso de segurança do Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 não pode ser ativado no Windows Server 2008. É necessário o Windows Server 2008R2 ou posterior. Certifique-se de que o hotfix .net 4.5.1 instalado para seu sistema operacional, consulte [2960358 de aviso de segurança do Microsoft](https://technet.microsoft.com/security/advisory/2960358). Você pode ter esta ou versão posterior já instalado no seu servidor.
2. Se você usar o Windows Server 2008R2, verifique se a que TLS 1.2 está habilitado. No servidor do Windows Server 2012 e versões posteriores, TLS 1.2 já deve estar ativada.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Para todos os sistemas operacionais, defina esta chave do registro e reinicie o servidor.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Se você também deseja habilitar TLS 1.2 entre o servidor do mecanismo de sincronização e um servidor SQL remoto, verifique se que você tem as versões necessárias instaladas para [TLS 1.2 suporte para o Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos para federação instalação e configuração

### <a name="windows-remote-management"></a>Gerenciamento remoto do Windows
Ao usar o Azure AD Connect para implantar os serviços de Federação do Active Directory ou o Proxy de aplicativo da Web, verifique os requisitos para garantir a conectividade e configuração terá êxito:

- Se o servidor de destino é integrado ao domínio, certifique-se de que gerenciado remoto do Windows está habilitado
    - Em uma janela de comando PSH elevada, use o comando`Enable-PSRemoting –force`
- Se o servidor de destino for uma máquina WAP unida fora do domínio, existem alguns requisitos adicionais
    - Na máquina de destino (máquina WAP):
         - Garantir o winrm (Windows Remote Management / WS-Management) serviço está sendo executado através do snap-in de serviços
         - Em uma janela de comando PSH elevada, use o comando`Enable-PSRemoting –force`
    - No computador em que a execução do assistente (se o computador de destino não-Unido ou não confiável domínio):
        - Em uma janela de comando PSH elevada, use o comando`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - No Gerenciador de servidores:
             - Adicionar host DMZ WAP ao pool de máquina (Gerenciador do servidor -> Gerenciar -> Adicionar servidores … usar a guia DNS)
             - Guia de todos os servidores do Gerenciador de servidores: servidor WAP clique com botão direito e escolha gerenciar como …, insira as credenciais de local (não domínio) para máquina WAP
             - Para validar a conectividade PSH remota, na guia Server Manager todos os servidores: servidor WAP clique com botão direito e escolha o Windows PowerShell.  Uma sessão remota do PSH deve abrir para garantir que podem ser estabelecidas sessões remota do PowerShell.

### <a name="ssl-certificate-requirements"></a>Requisitos de certificado SSL
**Importante:** altamente recomenda-se para usar o mesmo certificado SSL entre todos os nós do seu farm do AD FS, além de todos os servidores de proxy de aplicativo Web.

- O certificado deve ser um X509 certificado.
- Você pode usar um certificado autoassinado em servidores de Federação em um ambiente de laboratório de teste. No entanto, para um ambiente de produção, recomendamos que você obtenha o certificado de uma autoridade de certificação pública.
    - Se usar um certificado que não é confiável publicamente, certifique-se de que o certificado instalado em cada servidor de Proxy de aplicativo Web é confiável no servidor local e em todos os servidores de Federação
- A identidade do certificado deve corresponder ao nome de serviço de Federação (por exemplo, sts.contoso.com).
    - A identidade é uma extensão de nome alternativo (SAN) de assunto do tipo dNSName ou, se não houver nenhuma entrada de SAN, o nome de assunto especificado como um nome comum.  
    - Várias entradas de SAN podem estar presentes no certificado, desde que um deles corresponde ao nome de serviço de Federação.
    - Se você estiver planejando usar participar do local de trabalho, uma SAN adicional é necessária para o valor **enterpriseregistration.** seguido o sufixo do nome do usuário Principal (UPN) de sua organização, por exemplo, **enterpriseregistration.contoso.com**.
- Não há suporte para certificados com base em chaves de geração (CNG) próxima CryptoAPI e provedores de armazenamento de chave. Isso significa que você deve usar um certificado com base em um CSP (provedor de serviços de criptografia) e não um KSP (provedor de armazenamento de chaves).
- Há suporte para certificados de curinga.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nomes de servidores de Federação
- Configure registros DNS para o nome de serviço de Federação do AD FS (por exemplo, sts.contoso.com) para a intranet (seu servidor DNS interno) e o extranet (público DNS por meio de seu registrador de domínio). Para a intranet DNS registro garantir que você use A registros e não CNAME. Isso é necessário para autenticação do windows funcionar corretamente da máquina unidas domínio.
- Se você estiver implantando mais de um servidor do AD FS ou servidor Proxy de aplicativo da Web, certifique-se que você tenha configurado seu balanceador de carga e que os registros DNS para o nome de serviço de Federação do AD FS (por exemplo, sts.contoso.com) apontem para o balanceador de carga.
- Para autenticação integrada do windows trabalhar para aplicativos de navegador usando o Internet Explorer na sua intranet, certifique-se de que o nome de serviço de Federação do AD FS (por exemplo, sts.contoso.com) é adicionado à zona da intranet no IE. Isso pode ser controlado por meio da política de grupo e implantado em todos os computadores de domínio associado.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect componentes de suporte
A seguir está uma lista dos componentes que Azure AD Connect instala no servidor onde o Azure AD Connect está instalado. Esta lista é para uma instalação de Express básica.  Se você optar por usar um SQL Server diferente na página de serviços de sincronização de instalação, SQL Express LocalDB não está instalado localmente.

- Azure AD Connect integridade
- Assistente Microsoft Online Services entrar para profissionais de TI (instalado mas nenhuma dependência nele)
- Utilitários de linha de comando do Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Microsoft SQL Server 2012 Native Client
- Pacote do Microsoft Visual C++ 2013 redistribuição

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware do Azure AD Connect
A tabela a seguir mostra os requisitos mínimos para o computador de sincronização do Azure AD Connect.

| Número de objetos no Active Directory | CPU | Memória | Tamanho do disco rígido |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10.000 – 50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50.000 – 100,000 | 1,6 GHz | 16 GB | 100 GB |
| Para 100.000 ou mais objetos a versão completa do SQL Server é necessária|  |  |  |
| 100,000 a 300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300.000 – 600.000 | 1,6 GHz | 32 GB | 450 GB |
| Mais de 600.000 | 1,6 GHz | 32 GB | 500 GB |

Os requisitos mínimos para computadores que executam o AD FS ou servidores de aplicativos Web é o seguinte:

- CPU: Duplo núcleo 1,6 GHz ou superior
- MEMÓRIA: 2GB ou superior
- Azure VM: Configuração A2 ou superior

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
