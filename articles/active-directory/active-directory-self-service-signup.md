<properties
    pageTitle="O que é a inscrição de autoatendimento para o Azure? | Microsoft Azure"
    description="Uma inscrição de autoatendimento de visão geral do Azure, como gerenciar o processo de inscrição e como assumir um nome de domínio DNS."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>O que é a inscrição de autoatendimento para o Azure?

Este tópico explica o processo de inscrição de autoatendimento e como assumir um nome de domínio DNS.  

## <a name="why-use-self-service-signup"></a>Por que usar inscrição de autoatendimento?

- Obtenha clientes aos serviços querem mais rápido.
- Crie ofertas baseada em email para um serviço.
- Crie fluxos de inscrição baseada em email que rapidamente permitem aos usuários criar identidades usando seus aliases de email de trabalho fácil de lembrar.
- Diretórios Azure não gerenciados podem ser transformados em diretórios gerenciados posteriormente e ser reutilizados para outros serviços.

## <a name="terms-and-definitions"></a>Termos e definições

+ **Autoatendimento inscrever**: Este é o método pelo qual um usuário se inscreve para um serviço de nuvem e tem uma identidade criada automaticamente para que elas no Azure Active Directory (AD Azure) com base em seu domínio de email.
+ **Directory do Azure não gerenciado**: Este é o diretório onde identidade é criada. Um diretório não gerenciado é um que não tenha nenhum administrador global.
+ **Usuário verificado por email**: Este é um tipo de conta de usuário no Azure AD. Um usuário que tem uma identidade criada automaticamente após a inscrição para uma oferta de autoatendimento é conhecido como um usuário verificado por email. Um usuário verificado por email é um membro regular de um diretório marcado com creationmethod = EmailVerified.

## <a name="user-experience"></a>Experiência do usuário

Por exemplo, digamos que um usuário cujo email for Dan@BellowsCollege.com recebe arquivos confidenciais via email. Os arquivos tem sido protegidos pelo gerenciamento de direitos do Azure (Azure RMS). Mas organização de Paulo, faculdade fole, não se inscreveu para Azure RMS, nem implantou o RMS do Active Directory. Nesse caso, Dan pode se inscrever para uma assinatura gratuita do RMS para indivíduos para ler os arquivos protegidos.

Se Dan é o primeiro usuário com um endereço de email do BellowsCollege.com para se inscrever este autoatendimento oferecendo, e em seguida, será criado um diretório não gerenciado para BellowsCollege.com no Azure AD. Se outros usuários do domínio BellowsCollege.com inscrever-se para esta oferta ou uma oferta de autoatendimento semelhante, eles também terá contas de usuário verificadas email criadas no mesmo diretório não gerenciado no Azure.

## <a name="admin-experience"></a>Experiência de administração

Um administrador que possui o nome de domínio DNS de um diretório Azure não gerenciado pode assumir ou mesclar o diretório depois comprovar a propriedade. As próximas seções explicam a experiência de administração de forma mais detalhada, mas aqui está um resumo:

- Quando você adota sobre um diretório Azure não gerenciado, você simplesmente se tornar o administrador global do diretório não gerenciado. Às vezes, isso se chama um controle interno.
- Quando você mescla um diretório Azure não gerenciado, você adicionar o nome de domínio DNS do diretório não gerenciado para seu diretório Azure gerenciado e um mapeamento de recursos para usuários é criado assim os usuários podem continuar acessar serviços sem interrupção. Às vezes, isso se chama um controle externo.

## <a name="what-gets-created-in-azure-active-directory"></a>O que é criado no Active Directory do Azure?

#### <a name="directory"></a>diretório

- Um diretório do Active Directory do Azure para o domínio é criado, um diretório por domínio.
- O diretório do Azure AD não tem nenhum administrador global

#### <a name="users"></a>Usuários

- Para cada usuário que se inscreve, um objeto de usuário é criado no diretório Azure AD.
- Cada objeto de usuário está marcado como externo.
- Cada usuário recebe acesso ao serviço que eles inscreveram.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Como reivindicar uma Azure AD autoatendimento diretório para um domínio me pertence?

Você pode solicitar um Azure AD autoatendimento diretório executando a validação de domínio. Validação de domínio prova que o domínio pertence criando registros de DNS.

Há duas maneiras de fazer um controle DNS de um diretório de Azure AD:

- tomada de controle interna (administrador descobre um diretório Azure não gerenciado e deseja transformar em um diretório gerenciado)
- controle externo (administrador tenta adicionar um novo domínio ao seu diretório Azure gerenciado)

Você pode estar interessado na validação que você possui um domínio porque estão assumindo um diretório não gerenciado depois de um usuário realizado inscrição de autoatendimento, ou você pode adicionar um novo domínio para um diretório gerenciado existente. Por exemplo, você tem um domínio chamado contoso.com e você deseja adicionar um novo domínio denominado contoso.co.uk ou contoso.uk.

## <a name="what-is-domain-takeover"></a>O que é o controle de domínio?  

Esta seção aborda como validar que você possui um domínio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Qual é a validação de domínio e por que ele é usado?

Para executar operações em um diretório, Azure AD requer que você valide a propriedade do domínio DNS.  Validação do domínio permite declarar o diretório e seja promover o diretório de autoatendimento para um diretório gerenciado, ou mesclar a pasta de autoatendimento em um diretório gerenciado existente.

## <a name="examples-of-domain-validation"></a>Exemplos de validação de domínio

Há duas maneiras de fazer um controle DNS de um diretório:

+ controle interno (por exemplo, um administrador descobre um diretório de autoatendimento, não gerenciado e quer transformar em diretório gerenciado)
+ tomada de controle externa (por exemplo, um administrador tenta adicionar um novo domínio para um diretório gerenciado)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Controle interno - promover um diretório de autoatendimento, não gerenciado para ser um diretório gerenciado

Quando você fizer controle interno, o diretório obtém convertido de um diretório não gerenciado para um diretório gerenciado. Você precisa concluir a validação de nome de domínio DNS, onde você pode criar um registro MX ou um registro TXT na zona DNS. Essa ação:

+ Valida que o domínio pertence
+ Faz com que o diretório gerenciado
+ Torna o administrador global do diretório

Digamos que um administrador de TI da faculdade fole descobre que os usuários a escola se inscreverem ofertas de autoatendimento. Como o proprietário registrado do DNS nome BellowsCollege.com, o administrador de TI pode validar a propriedade do nome DNS no Azure e então assumir o diretório não gerenciado. Diretório se tornará um diretório gerenciado e o administrador de TI é atribuído a função de administrador global para o diretório BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Controle externo - mesclar um diretório de autoatendimento em um diretório gerenciado existente

Em um controle externo, você já tem um diretório gerenciado e desejar que todos os usuários e grupos a partir de um diretório não gerenciado para ingressar nesse diretório gerenciado, em vez de dois próprios separam diretórios.

Como administrador de um diretório gerenciado, adicionar um domínio e esse domínio acontece ter um diretório não gerenciado associado a ele.

Por exemplo, digamos que você é um administrador de TI e você já tem um diretório gerenciado para Contoso.com, um nome de domínio que está registrado em sua organização. Descubra o que os usuários de sua organização tiveram executada autoatendimento para cima para uma oferta usando o nome de domínio de email user@contoso.co.uk, que é outro nome de domínio que possui de sua organização. Esses usuários têm contas em um diretório não gerenciado para contoso.co.uk.

Você não quiser gerenciar dois diretórios separados, para que você mesclar o diretório não gerenciado para contoso.co.uk em seu diretório gerenciado por TI existente para contoso.com.

Controle externo segue o mesmo processo de validação de DNS da tomada de controle interno.  Diferença sendo: usuários e serviços são remapeados para o diretório gerenciado por TI.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Qual é o impacto de execução de um controle externo?

Com um controle externo, um mapeamento de recursos para usuários é criado para que os usuários possam continuar acessar serviços sem interrupção. Muitos aplicativos, incluindo o RMS para indivíduos, processa o mapeamento de recursos para usuários bem e os usuários podem continuar acessar esses serviços sem alteração. Se um aplicativo não manipular o mapeamento de recursos para usuários efetivamente, controle externo pode ser bloqueado explicitamente para impedir os usuários de uma experiência ruim.

#### <a name="directory-takeover-support-by-service"></a>suporte de controle de diretório pelo serviço

Os seguintes serviços suportam controle:

- RMS


Os seguintes serviços oferecerá em breve suporte a tomada de controle:

- PowerBI

A seguir não e exige ação de administrador adicionais para migrar os dados de usuário após um controle externo.

- SharePoint/OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Como executar um controle de nome de domínio DNS

Você tem algumas opções realizar uma validação de domínio (e fazer um controle se desejar):

1.  Portal de gerenciamento do Azure

    Um controle é disparado, fazendo uma adição de domínio.  Se já existe um diretório para o domínio, você terá a opção de realizar um controle externo.

    Entre portal do Azure usando suas credenciais.  Navegue para o diretório existente e depois para **Adicionar domínio**.

2.  Office 365

    Você pode usar as opções na página [Gerenciar domínios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) no Office 365 para trabalhar com seus domínios e registros DNS. Consulte [Verificar seu domínio no Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    As etapas a seguir são necessárias para executar uma validação usando o Windows PowerShell.

    Etapa    |   Usar o cmdlet
    ------- | -------------
    Criar um objeto de credencial | Get-Credential
    Conectar ao Azure AD | Conectar-MsolService
    obter uma lista de domínios   | Get-MsolDomain
    Criar um desafio  | Get-MsolDomainVerificationDns
    Criar registro DNS   | Fazer isso no seu servidor DNS
    Verifique se o desafio    | Confirmar-MsolEmailVerifiedDomain

Por exemplo:

1. Conectar ao Azure AD usando as credenciais que foram usadas para responder a oferta de autoatendimento: módulo de importação MSOnline $msolcred = get-credential conectar-msolservice-$msolcred de credencial

2. Obter uma lista de domínios:

    Get-MsolDomain

3. Execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio:

    Get-MsolDomainVerificationDns – nome_do_domínio *seu_nome_de_domínio* – modo DnsTxtRecord

    Por exemplo:

    Get-MsolDomainVerificationDns – nome_do_domínio contoso.com – modo DnsTxtRecord

4. Copie o valor (o desafio) que é retornado este comando.

    Por exemplo:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. No seu namespace DNS público, crie um registro txt DNS que contém o valor que você copiou na etapa anterior.

    O nome para este registro é o nome do domínio pai, portanto se você criar este registro de recurso usando a função DNS do Windows Server, deixe a colagem de registro nome em branco e apenas o valor na caixa de texto

6. Execute o cmdlet MsolDomain confirmar para confirmar o desafio:

    Confirmar-MsolEmailVerifiedDomain - DomainName *seu_nome_de_domínio*

    Por exemplo:

    Confirmar-MsolEmailVerifiedDomain - DomainName contoso.com

Um desafio bem-sucedida retorna para o prompt sem um erro.

## <a name="how-do-i-control-self-service-settings"></a>Como eu controlo configurações de autoatendimento?

Administradores tem dois controles de autoatendimento hoje. Eles podem controlar:

- Se os usuários podem participar do diretório via email.
- Se os usuários podem licenciar próprios para aplicativos e serviços.


### <a name="how-can-i-control-these-capabilities"></a>Como eu controlo esses recursos?

Um administrador pode configurar esses recursos usando esses parâmetros de cmdlet Set-MsolCompanySettings do Azure AD:

+ **AllowEmailVerifiedUsers** controla se um usuário pode criar ou ingressar em um diretório não gerenciado. Se você definir esse parâmetro como $false, nenhum usuário verificado por email pode ingressar no diretório.
+ **AllowAdHocSubscriptions** controla a capacidade para os usuários executarem autoatendimento para cima. Se você definir esse parâmetro para $false, nenhum usuário poderá realizar inscrição de autoatendimento.


### <a name="how-do-the-controls-work-together"></a>Como os controles funcionam juntos?

Esses dois parâmetros podem ser usados em conjunto para definir um controle mais preciso sobre autoatendimento para cima. Por exemplo, o seguinte comando permitirá que os usuários executem autoatendimento para cima, mas somente se os usuários já tem uma conta no Azure AD (em outras palavras, os usuários que seriam necessário uma conta de email-verificado seja criado não podem executar autoatendimento para cima):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

O fluxograma a seguir explica todas as combinações diferentes para esses parâmetros e as condições resultantes para o diretório e autoatendimento para cima.

![][1]

Para obter mais informações e exemplos de como usar esses parâmetros, consulte [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Consulte também

-  [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

-  [PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Referência de cmdlets do Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
