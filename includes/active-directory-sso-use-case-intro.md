Organizações estão usando mais aplicativos de [Software como um serviço (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) para produtividade porque a tecnologia de nuvem e ferramentas estão se tornando mais prontamente disponíveis. Conforme aumenta o número de aplicativos SaaS, ele se tornará desafiador para os administradores para gerenciar contas e direitos de acesso e para os usuários a lembrar suas senhas diferentes. Gerenciar esses aplicativos individualmente cria trabalho extra e é menos seguro.


- Funcionários que têm para controlar muitas senhas tendem a usar métodos menos seguro de lembrá-las, escrever senhas ou usando as mesmas senhas em várias contas.

- Quando chega um novo funcionário ou um sai, todas as contas devem ser individualmente provisionadas ou eliminação provisionadas.

- Além disso, os funcionários podem começar a usar o SaaS aplicativos para seu trabalho sem passar pelo IT, o que significa que eles estão criando suas próprias contas em sistemas que os administradores de TI ainda não aprovada e não são monitoramento.  

É uma solução para todos esses desafios logon único (SSO). É a maneira mais simples de gerenciar vários aplicativos e fornecer aos usuários uma experiência de logon consistente. Azure Active Directory (AD Azure) fornece uma solução SSO robusta e tem muitos aplicativos integrados previamente disponíveis, com tutoriais para administradores configurar um novo aplicativo e Iniciar provisionamento usuários rapidamente.


## <a name="how-does-azure-active-directory-integrate-apps"></a>Como o Active Directory do Azure integrar aplicativos?  

Azure AD permite que você integre seus aplicativos e contas provisionadas. Isso pode ser feito por meio de uma das duas abordagens.

- Se o aplicativo for previamente integrado no aplicativo galeria, você pode dar uma olhada nesse portal para configurar os aplicativos e definir as configurações para permitir que o SSO. Para qualquer aplicativo de galeria, você pode começar por seguem as simples instruções passo a passo apresentadas na Galeria de aplicativo e no portal do Azure para habilitar o logon único.

- Se o aplicativo não estiver na galeria, você pode ainda configurar a maioria dos aplicativos no Azure AD como um aplicativo personalizado. Isso requer experiência um pouco mais técnica para configurar. Você pode adicionar qualquer aplicativo compatível com SAML 2.0 como um aplicativo federado, ou qualquer aplicativo que tenha uma baseado em HTML página de entrada como um aplicativo SSO de senha.

No caso onde os usuários criaram suas próprias contas para aplicativos SaaS que não são gerenciadas pelo IT, a ferramenta de [Descoberta de aplicativo de nuvem](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) fornece uma solução. Esta ferramenta monitora o tráfego da web para identificar quais aplicativos estão sendo usados em toda a organização e o número de pessoas que estão usando cada um deles. IT pode usar essas informações para saber quais aplicativos os usuários preferem e decida qual integrar Azure AD para o SSO.  

Quando você integra um aplicativo do Azure AD, você pode mapear identidades do aplicativo estabelecida dos usuários para suas respectivas identidades do Azure AD.  
