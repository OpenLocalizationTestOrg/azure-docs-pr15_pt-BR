<properties 
    pageTitle="Que tipo de coleção você precisa do Azure RemoteApp? | Microsoft Azure" 
    description="Saiba mais sobre os tipos de coleções disponíveis com o Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Que tipo de coleção você precisa do Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp permite que você compartilhe recursos e aplicativos com usuários em qualquer dispositivo. Podemos fazer isso criando coleções para manter os aplicativos e recursos e, em seguida, compartilhar esses conjuntos com usuários. Há 2 opções de outro conjunto, com rede diferente e opções de autenticação - que é adequado para você?

Vamos examinar as considerações diferentes e opções que você precisa fazer para obter o máximo proveito do seu conjunto de RemoteApp do Azure. 


## <a name="quick-differences-between-the-collection-types"></a>Rápida diferenças entre os tipos de coleção

|           | Nuvem | Híbrido |
|-----------|-------|--------|
|Usar um VNET existente| Sim| Sim|
|Requer contas conectadas AD (DirSync)| Não| Sim|
|Requer a associação de domínio| Não| Sim|
|Requer acessíveis para VNET do controlador de domínio| Não| Sim|

## <a name="cloud-collections"></a>Conjuntos de nuvem
- Rápido criar - coleção está provisionado rapidamente, significando que seus aplicativos obter aos usuários rapidamente.
- Trazer seus próprios aplicativos ou compartilhar nossa. Você pode usar uma imagem personalizada (criado a partir de uma máquina virtual do Azure) ou uma das imagens incluídas em sua assinatura.
- Não é necessário configurar uma conexão entre o seu conjunto e seu domínio local.
- Mas, opcionalmente, você pode usar seu próprio VNET do Azure para fornecer acesso ao seu ambiente local para o compartilhamento de dados ou usar autenticação não Windows recursos como o SQL Server (usando autenticação de banco de dados).


Okey, como criar uma?

- Somente nuvem? Criar com a opção de **Criação rápida** no portal.
- Nuvem + VNET? Criar usando a opção **criar com VNET** , mas não optar por ingressar em um domínio.

## <a name="hybrid-collections"></a>Conjuntos de híbrido
- Fornece acesso completo a rede local + VNET Azure.
- Inclui acesso de junção de domínio para aplicativos e dados. Aplicativos remotos podem autenticar em relação a seu Active Directory local -, eles poderão acessar recursos no seu domínio.
- Habilitar avançadas de monitoramento e gerenciamento com soluções existentes do System Center e políticas de grupo do Windows (por meio de uma imagem personalizada criada no Windows Server 2012 R2)
- Suporte a [rota expressa](https://azure.microsoft.com/services/expressroute/) para conectar seu VNET Azure ao seu VNET local.

Criar usando a opção **criar com VNET** e escolha para ingressar em um domínio.

## <a name="authentication-options"></a>Opções de autenticação
RemoteApp Azure suporta as contas do Active Directory do Azure e contas da Microsoft, mas não todos os conjuntos de todos os métodos de suporte. 

| Tipo de conta                      |                                                             | Nuvem | Nuvem + VNET | Híbrido |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Conta da Microsoft                 |                                                             | Sim   | Sim          | Não     |
| Azure Active Directory (AD Azure) |                                                             |       |              |        |
|                                   | Azure AD apenas                                               | Sim   | Sim          | Não     |
|                                   | AD Connect com a sincronização de senha                               | Sim   | Sim          | Sim    |
|                                   | AD Connect sem sincronização de senha                            | Sim   | Sim          | Não     |
|                                   | AD conectar-se com o AD FS                                       | Sim   | Sim          | Sim    |
|                                   | provedores de identidade de suporte do Azure do 3º terceiros (como Ping) | Sim   | Sim          | Sim    |
| Autenticação multifator       |                                                             | Sim   | Sim          | Sim    |



### <a name="cloud-and-cloud--vnet"></a>Nuvem e nuvem + VNET 
Com conjuntos de nuvem, você pode usar contas da Microsoft, contas do Azure AD ou uma combinação dos dois. Use as contas que funcionam melhor para seus usuários.

Não há nenhum requisito específico para usando contas da Microsoft. 

Se você quiser usar contas do Azure AD, você precisa certificar-se de que seu locatário do Azure AD corresponde a uma associada a sua assinatura. Quando você criou sua assinatura do Azure RemoteApp, locatário Azure AD que você estava usando foi automaticamente associado a sua assinatura. Qualquer usuário do Azure AD que conceder permissão precisa ser mesmo locatário. Se necessário, você pode [alterar o locatário do Azure AD](remoteapp-changetenant.md) associada a sua assinatura.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Híbrido (ou nuvem + Azure AD + AD)

Usando o Azure AD + Active Directory é um pré-requisito para um conjunto de híbrido local. Você precisa usar o AD Connect para integrar os dois diretórios. Mas, você tem algumas opções quando se trata de como você configurar AD Connect. 

Há 2 AD Connect cenários - usando a sincronização de senha ou federação do AD. Confira as [informações de conexão do AD](../active-directory/active-directory-aadconnect.md) para descobrir qual deles funcionar melhor para você.

Você também pode usar o Azure AD + AD com uma coleção de nuvem. Certifique-se de que você segue as mesmas etapas de configuração.

Fazer check-out [Azure AD + requisitos do Active Directory do Azure RemoteApp](remoteapp-ad.md) para ver as etapas necessárias para configurar o Azure AD e o Active Directory.

## <a name="go-create-your-collection"></a>Vá a criar sua coleção!
Okey, acho que descobrimos-la agora, portanto, há apenas uma coisa que resta a fazer - criar seu primeiro conjunto de RemoteApp do Azure.

[Criar um conjunto de nuvem](remoteapp-create-cloud-deployment.md) ou [criar um conjunto de híbrido](remoteapp-create-hybrid-deployment.md) - apenas obter criando.
