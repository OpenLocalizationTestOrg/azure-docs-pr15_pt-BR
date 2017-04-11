<properties
    pageTitle="Serviços de domínio Active Directory do Azure: Recursos | Microsoft Azure"
    description="Recursos dos serviços de domínio Active Directory do Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Serviços de domínio do Azure AD

## <a name="features"></a>Recursos
Os seguintes recursos estão disponíveis nos serviços de domínio do Azure AD gerenciados domínios.

- **Experiência de implantação simples:** Você pode habilitar serviços de domínio do Azure AD do seu locatário do AD Azure usando apenas alguns cliques. Independentemente se seu locatário do Azure AD está um locatário de nuvem ou sincronizado com o diretório local, seu domínio gerenciado pode ser provisionado rapidamente.

- **Suporte a associação de domínio:** Você pode facilmente computadores de junção de domínio na rede virtual Azure que seu domínio gerenciado está disponível no. A experiência de junção de domínio no cliente do Windows e sistemas operacionais de servidor funciona perfeitamente contra domínios atendidos pelos serviços de domínio do Azure AD. Você também pode usar associação de domínio automatizada ferramentas contra tais domínios.

- **Instância de um domínio por diretório do Azure AD:** Você pode criar um único domínio do Active Directory para cada diretório do Azure AD.

- **Criar domínios com nomes personalizados:** Você pode criar domínios com nomes personalizados (por exemplo, ' contoso100.com') usando os serviços de domínio do Azure AD. Você pode usar qualquer um dos nomes de domínio verificado ou não verificados. Opcionalmente, você também pode criar um domínio com o sufixo de domínio interno (isto é, ' *. onmicrosoft.com') oferecidas pelo seu diretório Azure AD.

- **Integrado ao Azure AD:** Você não precisa configurar ou gerenciar a replicação para os serviços de domínio do Azure AD. Contas de usuário, membros do grupo e credenciais de usuário (senhas) do seu diretório do Azure AD ficam disponíveis automaticamente nos serviços de domínio do Azure AD. Novos usuários, grupos ou alterações nos atributos de seu locatário do Azure AD ou seu diretório local são sincronizadas automaticamente aos serviços de domínio do Azure AD.

- **Autenticação NTLM e Kerberos:** Com suporte para autenticação NTLM e Kerberos, você pode implantar aplicativos que dependem de autenticação integrada do Windows.

- **Use suas credenciais/senhas corporativas:** Senhas de usuários em seu locatário do Azure AD trabalham com os serviços de domínio do Azure AD. Usuários podem usar suas credenciais corporativas em máquinas de associação de domínio, login interativamente ou pela área de trabalho remota e autenticar o domínio gerenciado.

- **Ligação LDAP & LDAP ler suporte:** Você pode usar os aplicativos que dependem vínculos LDAP para autenticar usuários em domínios atendidos pelos serviços de domínio do Azure AD. Além disso, os aplicativos que usam operações de leitura LDAP para atributos de usuário/computador de consulta do diretório também podem trabalhar com os serviços de domínio do Azure AD.

- **Seguro LDAP (LDAPS):** Você pode habilitar o acesso ao diretório sobre LDAP seguro (LDAPS). Acesso seguro de LDAP está disponível dentro da rede virtual por padrão. No entanto, você também pode habilitar o acesso seguro de LDAP pela internet.

- **Política de grupo:** Você pode usar um único interno GPO cada para os usuários e computadores contêineres para impor a conformidade com necessários políticas de segurança para contas de usuário e computadores de domínio.

- **Gerenciar o DNS:** Membros do grupo 'Administradores de DC AAD' podem gerenciar o DNS do seu domínio gerenciado usando ferramentas familiares de administração do DNS como o snap-in MMC de administração de DNS.

- **Criar unidades organizacionais personalizado (organizacionais):** Membros do grupo 'Administradores de DC AAD' podem criar unidades organizacionais personalizados no domínio gerenciado. Esses usuários recebem privilégios administrativos totais sobre unidades organizacionais personalizados, para que eles podem adicionar/remover contas de serviço, computadores, grupos etc essas OUs personalizadas.

- **Disponível em vários regiões Azure:** Consulte a página de [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) conheça as regiões Azure em que os serviços de domínio do Azure AD está disponível.

- **Alta disponibilidade:** Os serviços de domínio do Azure AD oferece alta disponibilidade do seu domínio. Este recurso oferece a garantia de maior tempo de atividade de serviço e resiliência a falhas. Ofertas de monitoramento de integridade interna automatizado correção de falhas por bagunçar novas instâncias para substituir instâncias com falha e fornecer o serviço contínuo para seu domínio.

- **Usar ferramentas de gerenciamento familiar:** Você pode usar ferramentas familiares de gerenciamento do Active Directory do Windows Server como a Central Administrativa do Active Directory ou o Active Directory do PowerShell administrar domínios gerenciados.
