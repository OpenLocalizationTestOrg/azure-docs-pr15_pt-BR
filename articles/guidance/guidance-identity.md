<properties
   pageTitle="Gerenciando identidades no Azure | Microsoft Azure"
   description="Explica e compara os diferentes métodos disponíveis para o gerenciamento de identidade em sistemas híbrido que abrangem o limite no local/nuvem com o Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Gerenciando identidades no Azure

Na maioria dos sistemas corporativos com base no Windows, você usará o Active Directory (AD) oferecer serviços de gerenciamento de identidade para seus aplicativos. AD funciona bem em um ambiente local, mas quando você estende sua infraestrutura de rede na nuvem, você tem algumas decisões importantes para tornar sobre como gerenciar a identidade. Você deve expandir seus domínios locais para incorporar VMs na nuvem? Você deve criar novos domínios na nuvem e em caso afirmativo, como? Você deve implementar sua própria floresta na nuvem ou você deve fazer uso do Azure Active Directory (AAD)?

Este artigo descreve algumas opções comuns para os desafios causados por esse cenário de reunião e ajuda a que determinar qual solução será melhor atender às suas necessidades de acordo com suas necessidades.

## <a name="using-azure-active-directory"></a>Usando o Active Directory do Azure

Você pode usar AAD para criar um domínio AD na nuvem e vinculá-lo para um local domínio do AD. AAD permite que você configurar o logon único (SSO) para usuários que executam aplicativos acessados por meio da nuvem.

[! [0]][0]

AAD é uma maneira simples para implementar um domínio de segurança na nuvem. Ele é utilizado por vários aplicativos da Microsoft, como o Microsoft Office 365. 

Benefícios do uso de AAD:

- Não é necessário para manter uma infraestrutura do Active Directory na nuvem. AAD inteiramente é gerenciado e mantido pela Microsoft.

- AAD fornece as mesmas informações de identidade que está disponível no local.

- Autenticação pode ocorrer no Azure, reduzindo a necessidade de aplicativos externos e usuários entrar em contato com o domínio local.

Pontos a considerar ao usar AAD:

- Serviços de identidade limitam-se a usuários e grupos. Não há nenhuma capacidade para autenticar contas de serviço e computador.

- Você deve configurar a conectividade com o seu domínio local para manter o diretório AAD sincronizado. 

- Você é responsável por aplicativos que os usuários podem acessar na nuvem por meio de AAD de publicação.

Para obter informações detalhadas, leia [Implementando o Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>Usando o Active Directory na nuvem associados a uma floresta local

Você pode hospedar serviços AD Directory (AD DS) no local, mas em um cenário de híbrido onde os elementos de um aplicativo estão localizados no Azure pode ser mais eficiente para replicar essa funcionalidade e o repositório do AD para a nuvem. Essa abordagem pode ajudar a reduzir a latência causada enviando autenticação e solicitações de autorização local da nuvem de volta para o AD DS em execução no local. 

[! [1]][1]

Essa abordagem requer que você crie seu próprio domínio na nuvem e associá-lo à floresta local. Criar VMs para hospedar os serviços do AD DS.

Benefícios de usar um domínio separado na nuvem:

- Fornece a capacidade de autenticar o usuário, o serviço e computador contas locais e na nuvem.

- Fornece acesso às mesmas informações identidade que está disponível no local.

- Não é necessário para gerenciar uma floresta separada do AD; o domínio na nuvem pode pertencer à floresta local.

- Você pode aplicar a política de grupo definida pelos objetos de GPO local para o domínio na nuvem.

Considerações para usar um domínio separado na nuvem:

- Requer que você criar e gerenciar seus próprios servidores AD DS e o domínio na nuvem.

- Pode haver uma latência de sincronização entre os servidores de domínio na nuvem e os servidores que executam o local.

Para obter informações sobre como configurar essa arquitetura, consulte [Estendendo Active Directory Directory Services (ADICIONA) no Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>Usando o Active Directory com uma floresta separada

Uma organização que executa o Active Directory (AD) local pode ter uma floresta que consiste em vários domínios diferentes. Você pode usar domínios para oferecer isolamento entre áreas funcionais que devem ser mantidos separados, possivelmente por razões de segurança, mas você pode compartilhar informações entre domínios estabelecendo relações de confiança.

[! [2]][2]

Uma organização que utiliza domínios separados pode tirar proveito do Azure por meio da realocação de um ou mais desses domínios em uma floresta separada na nuvem. Como alternativa, uma organização talvez queira manter todos os recursos de nuvem logicamente distinto desses mantido local e armazenar informações sobre recursos de nuvem em seu próprio diretório, como parte de uma floresta também mantida na nuvem.

Benefícios do uso de uma floresta separada na nuvem:

- Você pode implementar identidades local e separar identidades somente Azure.

- Não é necessário para duplicar o local floresta do AD no Azure, reduzindo os efeitos de latência de rede.

Considerações sobre:

- Autenticação de identidades locais na nuvem executa rede extra *saltos* para o local servidores AD.

- Requer que você implementar seus próprios servidores AD DS e floresta na nuvem e estabelecer as relações de confiança apropriado entre florestas.

O documento [Criando uma floresta de recurso de serviços de diretório do Active Directory (ADICIONA) no Azure] [ adds-forest-in-azure] descreve como implementar essa abordagem mais detalhadamente.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>Usando o Active Directory Federation Services (ADFS) com o Azure

ADFS pode executar no local, mas em um cenário de híbrido onde os aplicativos estão localizados no Azure pode ser mais eficiente para implementar essa funcionalidade na nuvem, conforme mostrado abaixo.

[! [3]][3]

Essa arquitetura é especialmente útil para:

- Soluções que utilizam federada autorização para expor aplicativos da web para organizações de parceiros.

- Sistemas que permitem o acesso de navegadores da web em execução fora da firewall organizacional.

- Sistemas que permitem aos usuários acesso aos aplicativos web conectando-se autorizados dispositivos externos como computadores remotos, notebooks e outros dispositivos móveis. 

Benefícios do uso de ADFS com o Azure:

- Você pode aproveitar aplicativos compatíveis com declarações.

- Ele oferece a capacidade de confiar parceiros externos para autenticação.

- Ele fornece compatibilidade com grande conjunto de protocolos de autenticação.

Considerações para usar o AD FS com o Azure:

- Ela requer que você implementar seus próprios servidores Proxy de aplicativo do ADFS Web, ADFS e ADICIONA na nuvem.

- Essa arquitetura pode ser complexa de configurar.

Para obter informações detalhadas, leia [Implementando Active Directory Federation Services (ADFS) no Azure][adfs-in-azure].

## <a name="next-steps"></a>Próximas etapas

Os recursos a seguir explicam como implementar as arquiteturas descritas neste artigo.

- [Implementação do Active Directory do Azure][implementing-aad]
- [Estendendo serviços de diretório do Active Directory (ADICIONA) para o Azure][extending-adds]
- [Criando uma floresta de recurso de serviços de diretório do Active Directory (ADICIONA) no Azure][adds-forest-in-azure]
- [Implementando serviços de Federação do Active Directory (AD FS) no Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Arquitetura de identidade de nuvem usando o Active Directory do Azure"
[1]: ./media/guidance-identity/figure2.png "Arquitetura de rede híbrida com o Active Directory protegida"
[2]: ./media/guidance-identity/figure3.png "Arquitetura de rede híbrida com separada AD domínios e florestas protegida"
[3]: ./media/guidance-identity/figure4.png "Arquitetura de rede híbrida com ADFS protegida"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md