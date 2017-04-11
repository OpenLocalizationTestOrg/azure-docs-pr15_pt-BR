<properties
    pageTitle="Gerenciando aplicativos com o Active Directory do Azure | Microsoft Azure"
    description="Este artigo os benefícios da integração do Active Directory do Azure com locais, nuvem e aplicativos SaaS."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/10/2016"
      ms.author="markvi"/>

# <a name="managing-applications-with-azure-active-directory"></a>Gerenciando aplicativos com o Active Directory do Azure

Além de fluxo de trabalho real ou conteúdo, empresas têm dois requisitos básicos para todos os aplicativos:

1. Para aumentar a produtividade, aplicativos devem ser fáceis de descobrir e acessar

2. Para habilitar a segurança e controle, a organização precisa supervisão em quem pode e realmente está acessando cada aplicativo e controle

No mundo dos aplicativos de nuvem isso pode melhor ser obtido usando identidade para controlar "*quem tem permissão para fazer o quê*".

Na terminologia de computação:

- *Quem* é conhecido como *identidade* - o gerenciamento de usuários e grupos

- *O que* é conhecido como *gerenciamento de acesso* – o gerenciamento de acesso a recursos protegidos

Ambos os componentes juntas são conhecidos como *identidade e gerenciamento de acesso (IAM)*, que é definido pelo grupo [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) como "*a disciplina de segurança que permite que os indivíduos certos acessar os recursos certos na parte direita vezes pelos motivos certos*".

Okey, o que é o problema? Se IAM *não gerenciado* em um só lugar com uma solução integrada:

- Os administradores de identidade têm individualmente, criar e atualizar contas de usuário em todos os aplicativos separadamente, uma atividade redundante e demorada.

- Os usuários precisam memorizar várias credenciais para acessar os aplicativos que precisam trabalhar com. Como resultado, os usuários tendem a anotar suas senhas ou use outras soluções de gerenciamento de senha que apresenta outros riscos de segurança de dados.

- Atividades redundantes, demoradas reduzem a quantidade de tempo que os usuários e administradores estão trabalhando em atividades de negócios que aumentam sua lucratividade.

Portanto, o que geralmente evita que organizações adotando soluções IAM integradas?

- Soluções mais técnicas são baseadas em plataformas de software que precisam ser implantado e adaptados por cada organização para seus próprios aplicativos.

- Aplicativos de nuvem geralmente são adotados em uma taxa maior que o departamento de TI pode integrar com soluções IAM existentes.

- Segurança e monitoramento ferramentas exigem personalização adicional e integração para atingir abrangente E2E cenários.

## <a name="azure-active-directory-integrated-with-applications"></a>Active Directory do Azure integrado aos aplicativos

Active Directory do Azure é abrangente de identidade como uma solução de serviço (IDaaS) da Microsoft que:

- Habilita IAM como um serviço de nuvem 

- Fornece gerenciamento de acesso central, o logon único (SSO) e relatórios 

- Gerenciamento de acesso integrado oferece suporte para [milhares de aplicativos](https://azure.microsoft.com/marketplace/active-directory/) na Galeria de aplicativo, incluindo Salesforce, Google Apps, caixa, Concur e muito mais. 


Com o Azure Active Directory, todos os aplicativos que você publicar para seus parceiros e clientes (comercial ou consumidor) têm a mesma identidade e acessar os recursos de gerenciamento.<br> Isso permite reduzir significativamente os custos operacionais.

O que acontece se você precisa implementar um aplicativo que ainda não estiver listado na Galeria de aplicativo? Embora seja um pouco mais demorado que configurando SSO para aplicativos da Galeria de aplicativo, Azure AD oferece um assistente que ajuda você a com a configuração.

O valor do Azure AD ultrapassa "apenas" aplicativos em nuvem. Você também pode usá-lo com aplicativos de local fornecendo acesso remoto seguro. Com acesso remoto seguro, você pode eliminar à necessidade de VPNs ou outras implementações de gerenciamento de acesso remoto tradicionais.

Fornecendo gerenciamento central de acesso e logon único (SSO) para todos os seus aplicativos, Azure AD oferece a solução para os problemas de segurança e produtividade de dados principal.

- Os usuários podem acessar vários aplicativos com um logon dando mais tempo a renda negócios ou gerar atividades de operações concluídas.

- Administradores de identidade podem gerenciar o acesso aos aplicativos em um só lugar.

A vantagem para o usuário e para a sua empresa é óbvia. Vamos dar uma olhada mais atenta os benefícios de um administrador de identidade e a organização.

## <a name="integrated-application-benefits"></a>Benefícios do aplicativo integrado

O processo SSO tem duas etapas:

- Autenticação, o processo de validação a identidade do usuário.

- Autorização, a decisão para habilitar ou bloquear o acesso a um recurso com uma política de acesso.

Ao usar o Azure AD para gerenciar aplicativos e habilitar o SSO:

- Autenticação é feita no local (por exemplo, AD) ou conta Azure AD do usuário.

- Autorização executa na Azure AD atribuição e proteção diretiva garantindo a experiência do usuário final consistente e permitindo que você adicionar atribuição, locais e MFA condições em qualquer aplicativo, independentemente de seus recursos internos.

É importante entender que a maneira como a autorização é aprovado no aplicativo de destino varia dependendo de como o aplicativo foi integrado com o Azure AD.

- **Aplicativos integrados previamente pelo provedor de serviços** Como o Office 365 e o Azure, estes são aplicativos criado diretamente no Azure AD e confiar para os recursos de gerenciamento de identidades e acesso abrangentes. Acesso a esses aplicativos está habilitado por meio de informações de diretório e emissão de token.

- **Aplicativos integrados previamente pela Microsoft e aplicativos personalizados** Estes são os aplicativos de nuvem independentes que dependem de um diretório de aplicativo interno e podem operar independentemente dos AD Azure. Acesso a esses aplicativos está habilitado por emitir uma credencial específico do aplicativo mapeada para uma conta do aplicativo. Dependendo dos recursos do aplicativo, a credencial pode ser um token de Federação ou o nome de usuário e a senha de uma conta que foi provisionada anteriormente no aplicativo.

- **Aplicativos locais** Aplicativos publicados através do proxy de aplicativo do Azure AD principalmente permitindo o acesso a aplicativos de local. Esses aplicativos contam uma central no diretório local como o Active Directory do Windows Server. Acesso a esses aplicativos está habilitado por disparo o proxy para fornecer o conteúdo do aplicativo para o usuário final mantendo o requisito de logon local.

Por exemplo, se um usuário ingressar em sua organização, você precisa criar uma conta do usuário no Azure AD para as operações de logon principais. Se esse usuário requer acesso a um aplicativo gerenciado como Salesforce, você também precisa criar uma conta para esse usuário no Salesforce e vinculá-lo à conta Azure para fazer com que o SSO funcionar. Quando o usuário deixa sua organização, é aconselhável excluir a conta do Azure AD e todas as contas de um correspondente para o IAM armazena os aplicativos que o usuário tem acesso.

## <a name="access-detection"></a>Detecção de acesso

Departamentos de TI nas empresas modernas, muitas vezes não são cientes de todos os aplicativos de nuvem que estão sendo usados. Em conjunto com detecção de aplicativo de nuvem, o Azure AD fornece uma solução para detectar esses aplicativos.

## <a name="account-management"></a>Gerenciamento de conta

Normalmente, o gerenciamento de contas em vários aplicativos é um processo manual realizado por IT ou suporte pessoal na organização. Azure AD totalmente automatizada gerenciamento de conta em todos os aplicativos integrado de provedor de serviço e esses aplicativos integrados previamente pela Microsoft provisionamento automatizado de usuário de suporte ou SAML JIT.

## <a name="automated-user-provisioning"></a>Provisionamento automatizado de usuário

Alguns aplicativos fornecem interfaces de automação para criação e remoção (ou desativação) das contas. Se um provedor de oferecer tal interface, ele é utilizado pelo Azure AD. Isso reduz os custos operacionais porque tarefas administrativas acontecem automaticamente e melhora a segurança do seu ambiente porque diminui as chances de acesso não autorizado.

## <a name="access-management"></a>Gerenciamento de acesso

Usando o Azure AD você pode gerenciar o acesso aos aplicativos usando individuais ou regra controlado pelo atribuições. Você também pode delegar acessar management para as pessoas certas na organização garantir o melhor supervisão e reduzindo a carga sobre o suporte técnico.

## <a name="on-premises-applications"></a>Aplicativos de local

Interna no aplicativo proxy permite que você publique seus aplicativos de local para seus usuários, resultando em consistente acesso experiência com o aplicativo em nuvem modernos e os benefícios dos recursos de monitoramento, relatórios e segurança do Azure AD.

## <a name="reporting-and-monitoring"></a>Monitoramento e geração de relatórios

Azure AD oferece recursos que permitem que você sabe de monitoramento e geração de relatórios previamente integrados quem tem acesso aos aplicativos e quando eles realmente usada.

## <a name="related-capabilities"></a>Recursos relacionados

Com o Azure AD, você pode proteger seus aplicativos com políticas de acesso granular e MFA previamente integrado. Para saber mais sobre o Azure MFA consulte [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Guia de Introdução

Para começar a integração de aplicativos com o Azure AD, dê uma olhada a [integração do Active Directory do Azure com aplicativos obtendo o guia de Introdução](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Consulte também

[Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
