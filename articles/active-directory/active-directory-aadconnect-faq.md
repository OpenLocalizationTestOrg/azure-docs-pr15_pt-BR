<properties
    pageTitle="Azure AD Connect: Perguntas Frequentes | Microsoft Azure"
    description="Esta página tem perguntas frequentes sobre Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>Azure AD Connect perguntas Frequentes

## <a name="general-installation"></a>Gerais de instalação
**P: instalação funcionará se o Administrador Global do Azure AD tiver 2FA habilitado?**  
Com as compilações de fevereiro de 2016, isso é suportado.

**P: existe uma maneira para instalar o Azure AD Connect autônoma?**  
Só há suporte para instalar o Azure AD Connect usando o Assistente de instalação. Não há suporte para uma instalação autônoma e silenciosa.

**P: posso ter uma floresta onde um domínio não pode ser contatado. Como instalar o Azure AD Connect?**  
Com as compilações de fevereiro de 2016, isso é suportado.

**P: o agente de integridade do AD DS funciona em server core?**  
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o PowerShell commandlet a seguir: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Rede
**P: posso ter um firewall, o dispositivo de rede ou algo que limita as conexões de tempo máximo pode ficar aberto na minha rede. Quanto tempo deve meu limite do lado do cliente ser ao usar o Azure AD Connect?**  
Todos os software de rede, dispositivos físicos ou qualquer outra coisa que limita o tempo máximo conexões permaneçam abertos deve usar um limite de pelo menos 5 minutos (300 segundos) para conectividade entre o servidor onde o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Isso também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: são SLDs (único rótulo domínios) com suporte?**  
Não, Azure AD Connect não suporta os locais florestas/domínios usando SLDs.

**P: são "pontilhada" NetBios nomeados têm suporte?**  
Não, Azure AD Connect não suporta florestas/domínios do local onde o nome de NetBios contém um período "." no nome.

## <a name="federation"></a>Federação
**P: o que fazer se eu receber um email que me pedindo para renovar minha certificado do Office 365**  
Use as orientações que estão descrita no tópico sobre como renovar o certificado [Renovar certificados](active-directory-aadconnect-o365-certs.md) .

**P: posso ter "Atualizar automaticamente terceira parte" configurar para a terceira parte do Office 365. É necessário realizar qualquer ação quando meu automaticamente o certificado de autenticação de token passa sobre?**  
Use as orientações descrito no artigo [Renovar certificados](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: é possível para renomear o servidor após a instalação do Azure AD Connect?**  
Não. Alterar o nome do servidor fará com que o mecanismo de sincronização não sejam capazes de se conectar ao banco de dados SQL e o serviço não será possível iniciar.

## <a name="identity-data"></a>Dados de identidade
**P: o atributo UPN (userPrincipalName) no Azure AD não coincidir com o UPN local - por que?**  
Consulte estes artigos:

- [Nomes de usuário no Office 365, Azure ou Intune não corresponderem o UPN local ou a ID de logon alternativo](https://support.microsoft.com/en-us/kb/2523192)
- [Alterações não sincronizadas pela ferramenta de sincronização do Azure Active Directory depois de alterar o nome UPN de uma conta de usuário para usar um domínio federado diferentes](https://support.microsoft.com/en-us/kb/2669550)

Você também pode configurar Azure AD para permitir que o mecanismo de sincronização atualizar o userPrincipalName conforme descrito em [recursos de serviço de sincronização do Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Configuração personalizada
**P: onde os cmdlets do PowerShell para Azure AD Connect documentados?**  
Excepto os cmdlets documentados neste site, outros cmdlets do PowerShell encontrados no Azure AD Connect não são suportados para uso do cliente.

* *P: posso usar "exportação/servidor importar" encontrados no *Gerenciador de serviço de sincronização* para mover configuração entre servers? * *  
Não. Esta opção não recuperará todas as configurações e não deve ser usada. Você deve usar o Assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Consulte [Mover configuração personalizada de ativo para servidor de teste](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Solução de problemas
**P: como posso obter ajuda com o Azure AD Connect?**

[Pesquisa na Base de Conhecimento Microsoft (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Pesquise a Base de Conhecimento Microsoft (KB) técnico soluções para problemas comuns de quebra-fix sobre o suporte para Azure AD Connect.

[Fóruns do Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Você pode pesquisar e procurar técnicos perguntas e respostas da comunidade ou faça sua própria pergunta clicando [aqui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Azure AD Connect atendimento ao cliente](https://manage.windowsazure.com/?getsupport=true)

- Use este link para obter suporte através do portal Azure.
