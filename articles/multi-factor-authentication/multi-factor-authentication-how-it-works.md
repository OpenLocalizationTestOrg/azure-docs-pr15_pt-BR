<properties 
    pageTitle="Autenticação multifator Azure - como ele funciona"
    description="Azure autenticação multifator ajuda a proteger o acesso a dados e aplicativos atendendo demanda do usuário para um processo de entrada simples. Ele fornece mais segurança exigindo uma segunda forma de autenticação e autenticação forte por meio de uma variedade de opções de verificação de fácil."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Como funciona a autenticação multifator do Azure

A segurança de autenticação multifator reside em sua abordagem em camadas. Comprometer vários fatores de autenticação apresenta um desafio significativo por usuários mal-intencionados. Mesmo se um invasor saber a senha do usuário, ele é inútil sem também ter posse do dispositivo confiável. O usuário deve perder o dispositivo, a pessoa que localiza ela não poderá usá-lo, a menos que ele ou ela também conhece a senha do usuário.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure autenticação multifator ajuda a proteger o acesso a dados e aplicativos atendendo demanda do usuário para um processo de entrada simples.  Ele fornece mais segurança exigindo uma segunda forma de autenticação e autenticação forte por meio de uma variedade de opções de verificação de fácil:

- chamada telefônica
- mensagem de texto
- notificação de aplicativo móvel, permitindo que os usuários escolham o método preferir
- código de verificação do aplicativo móvel
- 3º tokens de JURAMENTO de festa

Para obter informações adicionais oh como ele funciona ver o vídeo a seguir.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Métodos disponíveis para autenticação multifator
Quando um usuário entra, uma verificação adicional é enviada para o usuário.  A seguir são uma lista dos métodos que podem ser usados para essa verificação segunda.

Método de verificação  | Descrição
------------- | ------------- |
Chamada telefônica | Uma chamada for realizada para telefone inteligente de um usuário solicitando-los para verificar se eles estão entrando no pressionando o sinal #.  Isso concluirá o processo de verificação.  Essa opção é configurável e pode ser alterada para um código que você especificar.
Mensagem de texto | Uma mensagem de texto será enviada para o telefone inteligente de um usuário com um código de 6 dígitos.  Digite este código para concluir o processo de verificação.
Notificação de aplicativo móvel | Uma solicitação de verificação será enviada para o telefone inteligente de um usuário solicitando concluída a verificação, selecionando Verificar no aplicativo móvel. Isso ocorrerá se você selecionou notificação de aplicativo como o método de verificação principal.  Se eles receberam quando eles não são entrar, eles podem optar por reportá-lo como fraude.
Código de verificação com o aplicativo móvel | Um código de verificação será enviado para o aplicativo móvel que estiver em execução no Smartphone de um usuário.  Isso ocorrerá se você selecionou um código de verificação como seu método de verificação principal.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis do Azure multi-Factor Authentication
Azure autenticação multifator está disponível em três versões diferentes.  A tabela a seguir descreve cada um desses mais detalhadamente.

Versão  | Descrição
------------- | ------------- |
Autenticação multifator para Office 365 | Esta versão funciona exclusivamente com aplicativos do Office 365 e é gerenciado a partir do portal do Office 365. Para que os administradores agora podem ajudar a proteger seus recursos do Office 365 usando autenticação multifator.  Esta versão vem com uma assinatura do Office 365.
Autenticação multifator para administradores do Azure | O mesmo subconjunto dos recursos de autenticação multifator para o Office 365 estará disponível sem custo para todos os administradores Azure. Cada conta administrativa de uma assinatura do Azure agora pode obter proteção adicional habilitando esta funcionalidade de autenticação multifator core. Para que um administrador que deseja acessar o portal do Azure para criar uma máquina virtual, um site, gerenciar o armazenamento, serviços móveis ou qualquer outro serviço de Azure pode adicionar autenticação multifator à sua conta de administrador.
Azure autenticação multifator | Azure autenticação multifator oferece o conjunto mais sofisticado de recursos. <br><br>Ele fornece opções de configuração adicionais por meio do portal de gerenciamento do Azure, avançadas relatórios e suporte para um intervalo de locais e na nuvem aplicativos. Azure autenticação multifator pode ser comprada como uma licença independente e vem dentro do Azure Active Directory Premium e pacote de mobilidade do Enterprise. <br><br>Ele também pode ser adquirido em uma base de consumo, criando um provedor de autenticação multifator Azure em uma assinatura do Azure.
##<a name="feature-comparison-of-versions"></a>Comparação de recursos de versões
A tabela a seguir fornece uma lista dos recursos que estão disponíveis em várias versões do Azure multi-Factor Authentication.


Recurso  | Autenticação multifator para o Office 365 (incluído no Office 365 SKUs)|Autenticação multifator para administradores do Azure (inclusa na assinatura Azure) | Azure autenticação multifator (incluído no Azure AD Premium e pacote de mobilidade do Enterprise)
------------- | :-------------: |:-------------: |:-------------: |
Os administradores podem proteger contas com MFA| * | * (Disponível somente para contas de administrador do Azure)|*
Aplicativo móvel como um segundo fator|* | * | *
Telefonema como um segundo fator|* | * | *
SMS como um segundo fator|* | * | *
Senhas de aplicativos para clientes que não há suporte para MFA|* | * | *
Controle de administração sobre métodos de autenticação| *| *| *
Modo PIN| | | *
Alerta de fraude| | | *
Relatórios MFA| | | *
Ignorar avulsa| | | *
Saudações personalizadas para chamadas telefônicas| | | *
Personalização do ID do chamador para chamadas telefônicas| | | *
Confirmação de evento| | | *
IPs confiáveis| | | *
Suspender MFA para dispositivos lembrados (Public Preview)| | | *
MFA SDK| | | *
MFA para aplicativos locais usando o MFA server| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Como obter autenticação multifator do Azure

Se você quiser a funcionalidade completa oferecida pela autenticação multifator do Azure em vez de apenas os fornecidos para usuários do Office 365 e administradores Azure, há várias opções para colocá-lo:

1.  Comprar licenças de autenticação multifator de Azure e atribuí-las aos seus usuários.
2.  Comprar licenças que possui autenticação multifator de Azure agrupados neles como Azure Active Directory Premium ou o pacote de mobilidade da empresa e atribuí-las aos seus usuários.
3.  Crie um provedor de autenticação multifator Azure dentro da assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, você pode inscrever-se para uma assinatura de avaliação do Azure. Assinaturas de avaliação precisará ser convertido em assinaturas regulares antes de expiração de avaliação.

Ao usar um provedor de autenticação multifator Azure há dois modelos de uso disponíveis que são cobrados por meio de sua assinatura do Azure:


- **Por usuário**. Geralmente corporativo que deseja habilitar a autenticação multifator para um número fixo de funcionários que precisa regularmente de autenticação.
- **Por autenticação**. Geralmente corporativo que deseja habilitar a autenticação multifator para um grande grupo de usuários externos que raramente precisam de autenticação.

Para obter detalhes sobre preços consulte [preços do Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Escolha o por estação ou modelo baseado em consumo adequado para sua organização.   Para obter consulte Introdução [Introdução](multi-factor-authentication-get-started.md)
