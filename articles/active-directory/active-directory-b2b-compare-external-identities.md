<properties
   pageTitle="Comparação de recursos de gerenciamento de identidades externas usando o Active Directory do Azure | Microsoft Azure"
   description="Compara locatário vários aplicativos, B2C e colaboração do Azure Active Directory B2B para oferecer suporte a autenticação e a autorização de identidades externas"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Comparação de recursos de gerenciamento de identidades externas usando o Active Directory do Azure

Além de gerenciar o acesso de força de trabalho móvel para aplicativos SaaS, o Azure Active Directory (AD Azure) pode ajudar sua organização compartilhar recursos com parceiros de negócios e fornecer aplicativos para empresas e consumidores.

## <a name="developing-applications-for-businesses"></a>Desenvolver aplicativos para empresas

Você fornece um serviço ou aplicativo, como um serviço de folha de pagamento, para empresas? Azure AD fornece a plataforma de identidade que permite criar aplicativos que se integram perfeitamente com milhões de organizações que já configurou Azure AD como parte da implantação do Office 365 ou outros serviços de empresa.

**Exemplo:** Um distribuidor farmacêutico fornece suprimento médico e sistemas de informações para o setor de saúde. Eles necessários para um aplicativo de análise para práticas médicas e clientes desejados para gerenciar suas próprias identidades de campo. Esta empresa escolheu Azure AD como a plataforma de identidade com o aplicativo de gerenciamento de prática, fornecendo identidades do Azure AD aos seus clientes arroba até quando necessário. Para obter mais informações, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Permitindo o acesso de parceiros de negócios a seus recursos corporativos

Você tem parceiros de negócios ou para outras pessoas fora da sua empresa que precisam acessar os recursos da sua empresa, como um site do SharePoint ou seu sistema ERP? Azure AD permite que administradores para conceder a usuários externos (que podem ou não existir no Azure AD) single sign-on acesso aos aplicativos corporativos. Isso melhora a segurança como usuários perder o acesso ao deixam a organização do parceiro, enquanto você controla as políticas de acesso dentro de sua organização. Isso também simplifica a administração como você não precisa gerenciar um parceiro externo diretório ou por relações de Federação do parceiro.

**Exemplo:** Uma empresa de imagens fornece revendedores com foto serviços de imagem e opera a maior rede de varejo de quiosques de impressão. Eles escolheram Azure AD para habilitar milhares de usuários em seus parceiros de negócios de varejo usar suas próprias credenciais para baixar os materiais de marketing parceiro mais recentes e reordenar as fontes do fornecedor da empresa extranet de processamento de fotos. Para obter mais informações, consulte [colaboração do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Desenvolver aplicativos para consumidores

Você precisa de forma segura e econômica publicar aplicativos onlinehttps, como um frente de loja de varejo, milhões de consumidores? Azure AD fornece uma plataforma habilitando social, além de nome de usuário/senha entrar, marca autoatendimento inscrever e redefinição de senha de autoatendimento para consumidores de seu aplicativo. Aumenta conveniência para seus clientes, reduzindo a carga nos seus desenvolvedores.

**Exemplo:** O \#1 esportiva franquia no mundo necessário para entre em contato diretamente com seus ventiladores 450 milhões. Para fazer isso, elas criado um aplicativo móvel usando o Azure AD para armazenamento de autenticação e perfil do usuário. Ventiladores obter registro simplificado e entrar através do uso de contas social como o Facebook ou eles podem usar o nome de usuário/senhas tradicionais para uma experiência perfeita em iOS, Android e Windows telefones. Aproveitando os estabelecidos plataforma do Azure AD reduzido significativamente código personalizado enquanto concedendo franquia personalizado identidade visual e diminuindo preocupações com segurança, violações de dados e escalabilidade. Para obter mais informações, consulte [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Comparação de recursos do Azure AD

Cada um dos cenários já abordados neste artigo apresentada por recursos Azure AD. Esta tabela deve ajudar a esclarecer quais recursos são mais relevantes para você:

| **Considere este produto...**       | [Aplicativo de SaaS do Azure AD vários locatário](active-directory-developers-guide.md)    | [Colaboração do Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Se precisar fornecer …** | um serviço para empresas | acesso do parceiro aos meus aplicativos  | um serviço para consumidores |
| **E eu sou semelhante à...**  | Pharma distribuidor      | Imagem da empresa            | Franquia de esportes       |
| **Implantando um aplicativo para...**  | Gerenciamento de prática     | Fornecedor extranet          | Ventiladores de futebol            |
| **Direcionamento...**        | Escritórios médico        | Parceiros de negócios aprovada | Qualquer pessoa com email      |
| **Acessíveis quando...**      | Dê o seu consentimento administração de cliente | Meus convites de administração           | O consumidor se inscreve      |
