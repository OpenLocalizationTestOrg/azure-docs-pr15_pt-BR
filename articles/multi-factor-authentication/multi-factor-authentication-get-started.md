<properties
    pageTitle="Servidor do Azure MFA nuvem vs | Microsoft Azure"
    description="Escolha a solução de secutiry de autenticação multifator que é ideal para você solicitando que am i tentando proteger e onde estão meus usuários localizados.  Escolha nuvem, servidor de MFA ou do AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Escolha a solução de autenticação de vários fatores do Azure para você

Porque há vários tipos de autenticação de vários fatores Azure (MFA), podemos deve responder a algumas perguntas para descobrir qual versão é a correta para usar.  Essas perguntas são:

-   [O que estou tentando proteger](#what-am-i-trying-to-secure)
-   [Onde estão localizados os usuários](#where-are-the-users-located)
- [Quais recursos eu preciso?](#what-featured-do-i-need)

As seções a seguir orientam sobre determinando cada uma destas respostas.

## <a name="what-am-i-trying-to-secure"></a>O que estou tentando proteger?

Para determinar a solução de verificação em duas etapas correto, primeiro vamos deve responda à pergunta dos quais são você está tentando seguro com um segundo método de autenticação.  É um aplicativo que está no Azure?  Ou um sistema de acesso remoto?  Determinando o que estamos tentando seguro, podemos pode responder a pergunta de onde a autenticação multifator precisa ser ativado.  


O que você está tentando proteger| Autenticação multifator na nuvem|Servidor de autenticação multifator
------------- | :-------------: | :-------------: |
Aplicativos Microsoft primários|● |● |
Aplicativos de SaaS na Galeria de aplicativo|● |● |
Aplicativos do IIS publicados por meio de Proxy de aplicativo do Azure AD|● |● |
Aplicativos do IIS não publicados através do Proxy de aplicativo do Azure AD | |● |
Acesso remoto como VPN, RDG| |● |



## <a name="where-are-the-users-located"></a>Onde estão localizados os usuários

Em seguida, olhando onde nossos usuários estão localizados ajuda a determinar a solução correta a ser usado, se na nuvem ou locais usando o servidor de MFA.



Local do usuário| Autenticação multifator na nuvem|Servidor de autenticação multifator
------------- | :-------------: | :-------------: |
Active Directory do Azure|● | |
Azure AD e AD usando a federação com o AD FS no local|● |● |
Azure AD e AD usando o DirSync, Azure AD Sync, Azure AD Connect - sem sincronização de senha no local|● |● |
Azure AD e AD usando o DirSync, Azure AD Sync, Azure AD Connect - com sincronização de senha no local|● | |
Active Directory no local| |● |

## <a name="what-features-do-i-need"></a>Quais recursos eu preciso?

A tabela a seguir é uma comparação dos recursos que estão disponíveis com autenticação multifator na nuvem e com o servidor de autenticação multifator.

 | Autenticação multifator na nuvem | Servidor de autenticação multifator
------------- | :-------------: | :-------------: |
Notificação de aplicativo móvel como um segundo fator | ● | ● |
Código de verificação de aplicativo móvel como um segundo fator | ● | ●
Telefonema como segundo fator | ● | ●
SMS unidirecionais como segundo fator | ● | ●
SMS bidirecional como segundo fator |  | ●
Tokens de hardware como segundo fator |  | ●
Senhas de aplicativos para clientes que não há suporte para MFA | ● |  
Controle de administração sobre métodos de autenticação | ● | ●
Modo PIN |  | ●
Alerta de fraude | ● | ●
Relatórios MFA | ● | ●
Ignorar avulsa |  | ●
Saudações personalizadas para chamadas telefônicas | ● | ●
ID do chamador personalizáveis para chamadas telefônicas | ● | ●
IPs confiáveis | ● | ●
Lembre-se de MFA para dispositivos confiáveis  | ● |  
Acesso condicional | ● | ●
Cache |  | ●

Agora que podemos determinar se deseja usar autenticação multifator de nuvem ou o MFA Server local, podemos começar configurando e usando a autenticação multifator do Azure. **Selecione o ícone que representa seu cenário!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
