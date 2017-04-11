<properties
    pageTitle="Azure AD Connect: Topologias com suporte | Microsoft Azure"
    description="Este tópico detalha topologias compatíveis e sem suportadas do Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Conectar topologias para Azure AD
O objetivo deste tópico é descrever topologias de Azure AD com a sincronização do Azure AD Connect como a solução de chave de integração e locais diferentes. Ele descreve as configurações de compatíveis e sem suportadas.

Legenda para imagens no documento:

Descrição | Ícone
-----|-----
Floresta do Active Directory local| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory com importação filtrada| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Servidor de sincronização do Azure AD Connect| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect sincronização "preparação modo servidor"| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync com FIM2010 ou MIM2016| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Servidor de sincronização do Azure AD Connect, detalhada| ![Sincronização](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Directory do Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Cenário sem suporte | ![Sem suporte](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Único floresta, um locatário do Azure AD
![Única floresta única locatário](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é uma única floresta local, com um ou vários domínios e uma única Azure AD de locatários. Para a autenticação do Azure AD, a sincronização de senha é usada. A instalação expressa do Azure AD Connect suporta somente essa topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Única floresta, vários servidores de sincronização para um locatário do Azure AD
![Única floresta filtrada sem suporte](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Não há suporte para ter vários servidores de sincronização do Azure AD Connect conectados ao mesmo locatário Azure AD, exceto para um [servidor de teste](#staging-server). Isso não é suportado, mesmo se eles são configurados para sincronizar mutuamente conjunto de objetos. Você tenha considerado esta se você não puder alcançar todos os domínios da floresta de um único servidor ou distribuir carga por vários servidor.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Várias florestas, único locatário Azure AD
![Vários floresta única locatário](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com várias florestas do Active Directory local. Há vários motivos para ter mais de uma floresta do Active Directory local. Exemplos típicos são designs com florestas conta-recurso e como resultado após uma fusão ou aquisição.

Quando você tiver várias florestas, todas as florestas deve ser acessível pelo único Azure AD Connect servidor de sincronização. Você não precisa incluir o servidor em um domínio. Se for necessário alcançar floresta tudo, o servidor pode ser colocado em uma rede DMZ.

O Assistente de instalação do Azure AD Connect oferece várias opções para consolidar os usuários representados em várias florestas. O objetivo é que um usuário seja representado apenas uma vez no Azure AD. Há algumas topologias comuns que podem ser configuradas no caminho de instalação personalizada no Assistente de instalação. Selecione a opção correspondente que representa a topologia na página **identifica exclusivamente seus usuários**. A consolidação está configurada somente para os usuários. Grupos duplicados não são consolidados com a configuração padrão.

Topologias comuns são discutidas na próxima seção: [topologias separadas](#multiple-forests-separate-topologies), [malha completa](#multiple-forests-full-mesh-with-optional-galsync)e [Recurso de conta](#multiple-forests-account-resource-forest).

A configuração padrão do Azure AD Connect sincronização pressupõe:

1. Os usuários têm apenas uma conta habilitada e floresta onde se encontra essa conta é usada para autenticar o usuário. Essa suposição é ambas as para sincronização de senha e para a federação. UserPrincipalName e sourceAnchor/immutableID vêm desta floresta.
2. Os usuários têm apenas uma caixa de correio.
3. Da floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis no Exchange endereço GAL (lista Global). Se não houver nenhuma caixa de correio do usuário, qualquer floresta pode ser usada para contribuir esses valores de atributos.
4. Se você tiver uma caixa de correio vinculada, em seguida, há também outra conta em uma floresta diferente usada para entrar.

Se seu ambiente não corresponder a essas suposições, acontece o seguinte:

- Se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o mecanismo de sincronização escolhe uma e ignorar a outra.
- Uma caixa de correio vinculada com nenhuma outra conta ativa não é exportada ao Azure AD. A conta de usuário não é representada por um membro em qualquer grupo. Uma caixa de correio vinculada DirSync sempre seria representada como uma caixa de correio normal. Essa alteração intencionalmente é um comportamento diferente para melhor suporte florestas vários cenários.

Mais detalhes podem ser encontrados em [Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um locatário do Azure AD
![Vários floresta Multi sincronização sem suporte](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Não há suporte para ter mais de um servidor de sincronização de conectar-se do Azure AD conectado a uma única locatário Azure AD. A exceção é o uso de um [servidor de teste](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Várias florestas – topologias separadas
**Os usuários são representados apenas uma vez em todos os diretórios**

![Floresta de vários usuários uma vez](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Vários floresta separados topologias](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Nesse ambiente, todas as florestas locais são tratados como entidades separadas e nenhum usuário estaria presente em qualquer outra floresta.
Cada floresta tem sua própria organização do Exchange e não há nenhum GALSync entre as florestas. Essa topologia poderia ser a situação após uma fusão/aquisição ou em uma organização onde cada unidade de negócios está operando isolados uns dos outros. Essas florestas estão na mesma organização no Azure AD e aparecem com um GAL unificado.
Nesta figura, cada objeto em cada floresta é representado uma vez no metaverso e agregado no locatário de destino Azure AD.

### <a name="multiple-forests--match-users"></a>Várias florestas – usuários de correspondência
**Identidades de usuário existem em vários diretórios**

Comum todos esses cenários é que distribuição e grupos de segurança podem conter uma mistura de usuários, contatos e FSPs (objetos de segurança externa)

FSPs são usados em ADICIONA para representar os membros de outras florestas em um grupo de segurança. Todos os FSPs serão resolvidos para o objeto real no Azure AD.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Várias florestas – completas mescla com GALSync opcional
**Identidades de usuário existem em vários diretórios. Coincidir usando: atributo de email**

![Floresta de vários usuários Mail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Malha completa multi floresta](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite aos usuários e recursos estar localizados em qualquer floresta e comumente deve haver relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, pode opcionalmente haver uma solução local de GALSync. Cada usuário seria representado como um contato em todas as outras florestas. GALSync normalmente é implementado usando o Gerenciador de identidades Forefront 2010 ou o Gerenciador de identidades do Microsoft 2016. Azure AD Connect não pode ser usado para GALSync local.

Neste cenário, objetos de identidade são associados usando o atributo de email. Um usuário com uma caixa de correio em uma floresta está associado com os contatos em outras florestas.

### <a name="multiple-forests--account-resource-forest"></a>Várias florestas – conta-recurso floresta
**Identidades de usuário existem em vários diretórios. Coincidir usando: atributos ObjectSID e msExchMasterAccountSID**

![Floresta de vários usuários ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Floresta de vários AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Em uma topologia de floresta conta-recurso, você tem uma ou mais florestas conta com contas de usuário ativo. Você também pode ter uma ou mais florestas de recurso com contas desativadas.

Neste cenário um (ou mais) **floresta recurso** confia em todas as **florestas de conta**. Floresta do recurso normalmente tem um esquema de AD estendido com o Exchange e o Lync. Todos os serviços do Exchange e o Lync, bem como outros serviços compartilhados estão localizados nesta floresta. Os usuários têm uma conta de usuário desabilitada nesta floresta e a caixa de correio está vinculada à floresta conta.

## <a name="office-365-and-topology-considerations"></a>O Office 365 e considerações de topologia
Algumas cargas de trabalho do Office 365 precisam certas restrições topologias com suporte. Se você planeja usar qualquer um desses, em seguida, leia o tópico de topologias com suporte para a carga de trabalho.

Carga de trabalho |  
--------- | ---------
Exchange Online | Se houver mais de um Exchange organização local (ou seja, Exchange foi implantado mais de uma floresta), você deve usar o Exchange 2013 SP1 ou posterior. Detalhes podem ser encontradas aqui: [implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype for Business | Se você usa várias florestas local, há suporte para apenas a topologia de floresta conta-recurso. Detalhes para topologias com suporte podem ser encontradas aqui: [requisitos ambientais do Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Servidor de teste
![Servidor de teste](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect oferece suporte para instalar um segundo servidor no **modo de teste**. Um servidor nesse modo lê dados de todos os diretórios conectados, mas não gravar nada diretórios conectados. Ele está usando o ciclo de sincronização normal e, portanto, tem uma cópia atualizada dos dados da identidade. No caso de um onde o servidor primário falha, você pode alternar para o servidor de teste. Você pode fazer isso no Assistente do Azure AD Connect. Este segundo servidor preferência pode estar localizado em um data center diferente, desde que nenhuma infraestrutura é compartilhada com o servidor primário. Você deve copiar manualmente qualquer alteração de configuração feita no servidor principal para o segundo servidor.

Um servidor intermediário também pode ser usado para testar uma nova configuração personalizada e o efeito em seus dados. Você pode visualizar as alterações e ajustar a configuração. Quando você estiver satisfeito com a nova configuração, você pode fazer o servidor de teste o servidor ativo e definir o antigo servidor ativo no modo de teste.

Este método também pode ser usado para substituir o servidor de sincronização do active. Preparar o novo servidor e configurá-la em modo de teste. Certificar-se de que está em bom estado, desativar modo (tornando-ativo), de teste e desligar o servidor ativo no momento.

É possível ter mais de um servidor de teste quando você quiser ter vários backups em centros de dados diferentes.

## <a name="multiple-azure-ad-tenants"></a>Vários locatários do Azure AD
A Microsoft recomenda ter um locatário único no Azure AD para uma organização.
Antes de você planeja usar vários locatários do Azure AD, esses tópicos abrangem cenários comuns, permitindo que você use um único locatário.

Tópico |  
--------- | ---------
Delegação usando unidades administrativas | [Gerenciamento de unidades administrativas no Azure AD](active-directory-administrative-units-management.md)

![Multi floresta Multi locatário](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Não há uma relação de 1:1 entre um servidor de sincronização do Azure AD Connect e um locatário do Azure AD. Para cada site principal do Azure AD, você precisa de uma instalação de servidor de sincronização do Azure AD Connect. As instâncias de locatário do Azure AD são por design isolada e usuários em um não poderão ver os usuários no outro locatário. Se destina-se essa separação, esta é uma configuração com suporte, mas caso contrário, você deve usar o único modelo de locatário do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez em um locatário do Azure AD
![Única floresta filtrada](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Nesta topologia, um servidor de sincronização do Azure AD Connect está conectado a cada locatário Azure AD. Os servidores de sincronização do Azure AD Connect devem ser configurados para filtragem assim cada tem um conjunto mutuamente exclusivo de objetos para operar em. Por exemplo, você pode fazer cada servidor para um determinado domínio ou unidade Organizacional. Um domínio DNS só pode ser registrado em um único Inquilinos do Azure AD. Os UPNs os usuários dos locais AD deve usar também namespaces separada. Por exemplo, na imagem acima três UPN separada sufixos são registrados no local AD: contoso.com, fabrikam.com e brinquedos.com. Os usuários em cada domínio do AD local usar um namespace diferente.

Não há nenhum GALsync entre as instâncias de locatário do Azure AD. O catálogo de endereços no Exchange Online e o Skype for Business somente mostra os usuários no mesmo locatário.

Essa topologia tem os seguintes cenários com suporte restrições caso contrário:

- Apenas um dos locatários do Azure AD pode habilitar Exchange híbrido com o Active Directory local.
- Dispositivos Windows 10 só podem ser associados um locatário do Azure AD.

O requisito de mutuamente conjunto de objetos também se aplica ao write-back. Alguns recursos de write-back não são compatíveis com essa topologia desde que esses recursos pressupõem que a configuração de um único local:

-   Grupo write-back com configuração padrão
-   Write-back do dispositivo

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes em um locatário do Azure AD
![Única floresta Multi locatário incompatíveis](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Única floresta vários conectores sem suporte](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- Ele não há suporte para sincronizar o mesmo usuário vários locatários do Azure AD.
- Isso não é suportado para alterar uma configuração para fazer com que usuários em um anúncio Azure apareça como contatos no outro locatário Azure AD.
- Ele não há suporte para modificar a sincronização do Azure AD Connect para se conectar a vários locatários do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALsync usando write-back
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD locatários são por design isolada.

- Ele não há suporte para alterar a configuração de sincronização do Azure AD Connect para ler dados de outro locatário Azure AD.
- Não há suporte para exportar os usuários como contatos para outro local AD usando a sincronização do Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync com o servidor de sincronização de local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Há suporte para usar FIM2010/MIM2016 locais para usuários de GALsync entre duas organizações do Exchange. Os usuários de uma organização aparece como estrangeiras usuários/contatos na outra organização. Em seguida, podem ser sincronizados esses anúncios de locais diferentes para seus próprios locatários Azure AD.

## <a name="next-steps"></a>Próximas etapas
Para saber como instalar o Azure AD Connect para esses cenários, consulte [instalação personalizada do Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
