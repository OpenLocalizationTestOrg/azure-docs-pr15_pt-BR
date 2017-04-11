<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de design - definir uma estratégia de adoção de identidade híbrida | Microsoft Azure"
    description="Com o controle de acesso condicional, o Azure Active Directory verifica as condições específicas que você escolher quando a autenticação do usuário e antes de permitir acesso ao aplicativo. Depois que essas condições forem atendidas, o usuário autenticado e permissão para acessar o aplicativo."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir uma estratégia de adoção de identidade híbrido

Nesta tarefa, você deverá definir a estratégia de adoção de identidade híbrido para sua solução de identidade híbrido atender a requisitos de negócios que foram discutida:

- [Determinar as necessidades de negócios](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Determinar os requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir estratégia de necessidades de negócios
Os primeiros endereços de tarefa determinar os negócios organizações necessidades.  Isso pode ser muito amplo e mudança gradual do escopo pode ocorrer se você não estiver cuidado.  No início de mantenha a simplicidade, mas lembre-se sempre planejar para um design que irá acomodar e facilitar a alteração no futuro.  Independentemente se é um design simples ou um muito complexo, o Active Directory do Azure é a plataforma de Microsoft Identity compatível com o Office 365, Microsoft Online Services e nuvem aplicativos cientes.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três cenários de integração principal que são identidades de nuvem, identidades sincronizadas e identidades federadas.  Você deve planejar adotando uma dessas estratégias de integração.  A estratégia escolhida pode variar e podem incluir as decisões no escolhendo uma opção, que tipo de experiência do usuário que você deseja fornecer, você tem algumas da infraestrutura existente já in-loco e qual é o custo mais eficaz.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Os cenários definidos na figura acima são:

- **Identidades de nuvem**: estas são identidades que existem somente na nuvem.  No caso do Azure AD, seria residem especificamente no seu diretório Azure AD.
- **Sincronizados**: estas são identidades que existem locais e na nuvem.  Usando o Azure AD Connect, esses usuários são criados ou Unidos com contas do Azure AD existentes.  O hash de senha do usuário é sincronizado do ambiente local na nuvem no que é chamado hash de senha.  Quando usar sincronizada a uma limitação é que se um usuário estiver desabilitado no ambiente local, pode levar até 3 horas para esse status da conta apareça no Azure AD.  Isso ocorre porque o intervalo de tempo de sincronização.
- **Federados**: essas identidades existem ambos os locais e na nuvem.  Usando o Azure AD Connect, esses usuários são criados ou Unidos com contas do Azure AD existentes.  
 
>[AZURE.NOTE]
Para obter mais informações sobre a sincronização opções leia [integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).

A tabela a seguir ajudará a determinar as vantagens e desvantagens de cada uma das seguintes estratégias:

| Estratégia         | Vantagens                                                                                                                                                                                                                                                  | Desvantagens                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identidades de nuvem** | Mais fácil de gerenciar para pequena empresa. <br> Nada para instalar o hardware adicional não no local necessário<br>Desabilitado facilmente se o usuário sair da empresa                                                                                                   | Os usuários precisarão entrar ao acessar cargas de trabalho na nuvem <br> Senhas podem ou não ser o mesmo para identidades nuvem e local                                                                                                                                                                                                                     |
| **Sincronizados**     | Senha local será autenticar ambos os locais e na nuvem diretórios <br>Mais fácil de gerenciar para empresas de pequenas, médias ou grandes <br>Os usuários podem ter logon único (SSO) para alguns recursos <br> Método de Microsoft preferido para sincronização <br> Mais fácil de gerenciar | Alguns clientes podem relutar sincronizar seus diretórios com a nuvem de conclusão boletins específicos da empresa                                                                                                                                                                                                                                                  |
| **Federado**        | Os usuários podem ter logon único (SSO) <br>Se um usuário é encerrado ou deixa, a conta pode ser desabilitada imediatamente e acesso revogado,<br> Oferece suporte a avançados cenários que não podem ser realizadas com sincronizados                                           | Mais etapas para configurar <br> Manutenção superior <br> Pode exigir hardware adicional para a infraestrutura de STS <br> Pode exigir hardware adicional para instalar o servidor de Federação. É necessário software adicional se for usado o AD FS <br> Exigir configuração considerável para SSO <br> Ponto crítico de falha se o servidor de federação para baixo, os usuários não poderão se autenticar |

### <a name="client-experience"></a>Experiência do cliente
A estratégia que você usa determinarão a experiência de entrada do usuário.  As tabelas a seguir fornecem informações sobre quais os usuários devem esperar sua experiência de entrada seja.  Observe que nem todos os provedores de identidade federada suportam SSO em todos os cenários.

**Aplicativos de rede ingressaram em um domínio e privada**:
 

|                              | Identidade sincronizada      | Identidade federada                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Navegadores da Web                 | Autenticação baseada em formulários | logon único, às vezes, deve fornecer a ID da organização |
| Outlook                      | Solicitar credenciais     | Solicitar credenciais                                       |
| Skype for Business (Lync)    | Solicitar credenciais     | logon único do Lync, solicitados credenciais do Exchange   |
| SkyDrive Pro                 | Solicitar credenciais     | logon único                                               |
| Office Pro Plus assinatura | Solicitar credenciais     | logon único                                               |

**Externo ou não confiáveis fontes**:

|                              | Identidade sincronizada      | Identidade federada                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Navegadores da Web                 | Autenticação baseada em formulários |  Autenticação baseada em formulários |
| Outlook, Skype for Business (Lync), Skydrive Pro, assinatura do Office| Solicitar credenciais     | Solicitar credenciais                                       |
| O Exchange ActiveSync    | Solicitar credenciais     | logon único do Lync, solicitado a fornecer credenciais do Exchange   |
| Aplicativos móveis                 | Solicitar credenciais     | Solicitar credenciais                                               |

Se você determinar da tarefa 1 que você tenha um 3º IdP ou estão indo para usar um para fornecer a federação com o Azure AD de terceiros, você precisa conhecer os seguintes recursos com suporte:
- Qualquer provedor de SAML 2.0 compatível para o perfil SP Lite pode oferecer suporte a autenticação Azure AD e aplicativos associados
- Dá suporte à autenticação passiva, o que facilita auth OWA, SPO, etc.
- Clientes do Exchange Online podem ser suportados pelo SAML 2.0 Enhanced cliente perfil (ECP)

Você também deve estar ciente de que recursos não estarão disponíveis:

- Sem suporte do Web Services confiabilidade/Federation, todos os outros clientes ativos interromperá
 - Isso significa que nenhuma cliente do Lync, o cliente de OneDrive, assinatura do Office, o Office Mobile antes do Office 2016
- Transição do Office para autenticação passiva permitirá-los para dar suporte a IdPs de 2.0 SAML puro, mas ainda estará suporte em uma base por cliente


>[AZURE.NOTE]
Para a lista mais atualizada, leia o artigo http://aka.ms/ssoproviders.

## <a name="define-synchronization-strategy"></a>Definir estratégia de sincronização
Nesta tarefa, você definirá as ferramentas que serão usadas para sincronizar da organização local dados para a nuvem e o que você deve usar de topologia.  Porque, a maioria das organizações usam o Active Directory, informações sobre como usar o Azure AD Connect para as perguntas acima de endereço são fornecidas em alguns detalhes.  Para ambientes que não têm o Active Directory, há informações sobre como usar o FIM 2010 R2 ou 2016 de MIM para ajudar a planejar essa estratégia.  No entanto, lançamentos futuros do Azure AD Connect aceitará diretórios LDAP, portanto, dependendo de sua linha do tempo, essas informações podem ser capazes de auxiliar.

###<a name="synchronization-tools"></a>Ferramentas de sincronização
Passar dos anos, várias ferramentas de sincronização tem existentes e usada para vários cenários.  Atualmente o Azure AD Connect é ir para a ferramenta de escolha para todos os cenários com suporte.  AAD Sync e DirSync também ainda estão em torno de e até mesmo podem estar presentes em seu ambiente agora. 

>[AZURE.NOTE]
Para obter as informações mais recentes sobre os recursos compatíveis de cada ferramenta, leia o artigo de [comparação de ferramentas de integração de diretório](active-directory-hybrid-identity-design-considerations-tools-comparison.md) .  

### <a name="supported-topologies"></a>Topologias com suporte
Ao definir uma estratégia de sincronização, a topologia usado deve ser determinada. Dependendo as informações que foi determinadas na etapa 2, você pode determinar que topologia é a correta para usar. A única floresta, única topologia do Azure AD é o mais comum e consiste em uma única floresta do Active Directory e uma única instância do Azure AD.  Isso vai ser usado na maioria dos cenários e a topologia esperada ao usar instalação Azure AD conectar Express conforme mostrado na figura abaixo.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Única floresta cenário é muito comum para grandes e até mesmo pequenas organizações ter várias florestas, conforme mostrado na Figura 5.

>[AZURE.NOTE]
Para obter mais informações sobre os locais diferentes e topologias Azure AD com Azure AD Connect sincronização leia o artigo [topologias para Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Cenário de vários floresta

Se esse o caso, em seguida, a topologia de multi-forest-single Azure AD deve ser considerada se os itens a seguir forem verdadeiros:

- Os usuários têm apenas 1 identidade florestas todas – identificação exclusivo usuários na seção abaixo descreve isso mais detalhadamente.
- A autenticação do usuário para a floresta em que sua identidade está localizada
- UPN e âncora de origem (id imutável) se torne desta floresta
- Todas as florestas estão acessíveis por Azure AD Connect – isso significa que ele não precise ser domínio ingressou e pode ser colocado em um DMZ se isso facilita isso.
- Os usuários têm apenas uma caixa de correio
- A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis no Exchange endereço GAL (lista Global)
- Se não houver nenhuma caixa de correio do usuário, em seguida, qualquer floresta pode ser usada para contribuir esses valores
- Se você tiver uma caixa de correio vinculada, em seguida, há também outra conta em uma floresta diferente usada para entrar.

>[AZURE.NOTE]
Objetos que existem em ambos os locais e na nuvem estão "conectados" por meio de um identificador exclusivo. No contexto de sincronização de diretório, este identificador exclusivo é conhecido como o SourceAnchor. No contexto de Single Sign-On, isso é conhecido como o ImmutableId. [Conceitos de design do Azure AD Connect](active-directory-aadconnect-design-concepts.md#sourceanchor) para mais considerações sobre o uso do SourceAnchor.

Se acima não forem verdadeiras e você tiver mais de uma conta ativa ou mais de uma caixa de correio, o Azure AD Connect escolha uma e ignorar a outra.  Se você vinculou caixas de correio, mas nenhuma outra conta, essas contas não serão exportadas Azure AD e que o usuário não será um membro de grupos.  Isso é diferente de como ele foi no passado com o DirSync e há suporte intencional melhor esses cenários de vários florestas. Um cenário de vários floresta é mostrado na figura abaixo.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Vários floresta cenário com vários Azure AD**

É recomendável ter apenas um único diretório no Azure AD para uma organização, mas é suportado entre um servidor de sincronização do Azure AD Connect e um diretório do Azure AD é mantida uma relação de 1:1.  Para cada instância do Azure AD, você precisará de uma instalação do Azure AD Connect.  Além disso, Azure AD, por design está isolada e os usuários em uma instância do Azure AD não poderão ver os usuários em outra instância.

É possível e compatível para conectar-se uma instância de local do Active Directory a vários diretórios do Azure AD, conforme mostrado na figura abaixo:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Cenário de filtragem de floresta**

Para fazer isso a seguir deve ser verdadeiras:

- Os servidores de sincronização do Azure AD Connect devem ser configurados para filtragem para cada tiverem um conjunto mutuamente exclusivo de objetos.  Isso feito, por exemplo, por escopo cada servidor para um determinado domínio ou unidade Organizacional.
- Um domínio DNS só pode ser registrado em um único diretório do Azure AD portanto o UPNs os usuários dos locais AD deve usar namespaces separada
- Os usuários em uma instância do Azure AD só será capazes de ver os usuários de sua instância.  Eles não poderão ver usuários em outras instâncias
- Somente um dos diretórios Azure AD pode habilitar Exchange híbrido com o local AD
- Exclusividade comum também se aplica para write-back.  Isso tornará alguns recursos de write-back não é compatíveis com essa topologia desde que esses pressupõem que a configuração de um único local.  Isso inclui:
 - Agrupar regravação com configuração padrão
 - Regravação de dispositivo


Lembre-se de que a seguir não é suportado e não deve ser escolhido como uma implementação:

- Não há suporte para ter vários servidores de sincronização do Azure AD Connect se conectando ao mesmo diretório Azure AD, mesmo se eles estiverem configurados para sincronizar conjunto mutuamente exclusivo do objeto
- Ele não há suporte para sincronizar o mesmo usuário a vários diretórios do Azure AD. 
- Ele também não oferece suporte para alterar uma configuração para usuários em um anúncio Azure apareça como contatos no outro diretório do Azure AD. 
- Também é sem suporte para modificar a sincronização do Azure AD Connect para se conectar a vários diretórios do Azure AD.
- Diretórios do Azure AD são por design isolada. Isso não é suportado para alterar a configuração de sincronização do Azure AD Connect ler dados de outra pasta de Azure AD em uma tentativa de construir um GAL comuns e unificado entre os diretórios. Ele também não oferece suporte para exportar os usuários como contatos para outro local AD usando a sincronização do Azure AD Connect.


>[AZURE.NOTE]
Se sua organização restringe computadores na rede de conexão com a Internet, este artigo lista os pontos de extremidade (FQDNs, IPv4 e IPv6 intervalos de endereços) que você deve incluir em sua saída permite listas e Internet Explorer zona de Sites confiáveis do cliente computadores garantir que seus computadores com êxito podem usar o Office 365. Para obter mais informações, leia [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).

## <a name="define-multi-factor-authentication-strategy"></a>Definir estratégia de autenticação multifator
Nesta tarefa, você definirá a estratégia de autenticação multifator usar.  Azure autenticação multifator vem em duas versão diferente.  Uma é um baseado em nuvem e a outra é com base usando o servidor do Azure MFA local.  Com base na avaliação você anteriormente, você pode determinar qual solução é o correto para sua estratégia.  Use a tabela a seguir para determinar qual opção de design melhor atender a requisitos de segurança da sua empresa:

Opções de design de vários fatores:

| Ativo para proteger                                               | MFA na nuvem | MFA no local |
|---------------------------------------------------------------|------------------|----------------|
| Aplicativos da Microsoft                                                | Sim              | Sim            |
| Aplicativos de SaaS na Galeria de aplicativo                                  | Sim              | Sim            |
| Aplicativos do IIS publicados por meio de Proxy de aplicativo do Azure AD         | Sim              | Sim            |
| Aplicativos do IIS não publicados através do Proxy de aplicativo do Azure AD | Não               | Sim            |
| Acesso remoto como VPN, RDG                                     | Não               | Sim            |

Embora você talvez estabeleceu uma solução para sua estratégia, você ainda precisa usar a avaliação do acima sobre onde os usuários estão localizados.  Isso pode causar a solução alterar.  Use a tabela a seguir para ajudá-lo a determinar isto:

| Local do usuário                                                       | Opção de design preferencial                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Active Directory do Azure                                              | Multi-FactorAuthentication na nuvem |
| Azure AD e AD usando a federação com o AD FS no local             | Ambos                                    |
| Azure AD e AD usando o Azure AD Connect sem sincronização de senha no local | Ambos                                    |
| Azure AD e locais usando o Azure AD Connect com sincronização de senha  | Ambos                                    |
| AD local                                                      | Servidor de autenticação multifator      |

>[AZURE.NOTE]
Você também deve garantir que a opção de design de autenticação multifator que você selecionou suporta os recursos necessários para o seu design.  Para obter mais informações, leia [Escolher a solução de segurança de vários fatores para você](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## <a name="multi-factor-auth-provider"></a>Provedor de autenticação multifator
Autenticação multifator está disponível por padrão para os administradores globais que têm um locatário do Active Directory do Azure. No entanto, se quiser estender a autenticação multifator para todos os seus usuários e/ou deseja os administradores globais sejam capazes de aproveitar os recursos de vantagem como o portal de gerenciamento, saudações personalizadas e relatórios, então você adquira e configurar o provedor de autenticação multifator.

>[AZURE.NOTE]
Você também deve garantir que a opção de design de autenticação multifator que você selecionou suporta os recursos necessários para o seu design. 

##<a name="next-steps"></a>Próximas etapas
[Determinar os requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)
