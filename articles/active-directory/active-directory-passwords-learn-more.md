<properties
    pageTitle="Saiba mais: Gerenciamento de senha do Azure AD | Microsoft Azure"
    description="Tópicos avançados no gerenciamento de senha do Azure AD, incluindo como funciona write-back de senha, segurança de write-back de senha, como a redefinição de senhas works portal e quais dados são usados pela redefinição de senha."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>Saiba mais sobre o gerenciamento de senha

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

Se você já implantou o gerenciamento de senha ou se apenas olhando para saber mais sobre técnicas realmente que interessa de como ele funciona antes de implantar, nesta seção você terá uma boa visão geral dos conceitos técnicos por trás do serviço. Vamos abordar o seguinte:

* [**Visão geral de write-back de senha**](#password-writeback-overview)
  - [Como funciona write-back de senha](#how-password-writeback-works)
  - [Cenários com suporte para write-back de senha](#scenarios-supported-for-password-writeback)
  - [Modelo de segurança de write-back de senha](#password-writeback-security-model)
* [**Como a senha redefinir trabalho portal?**](#how-does-the-password-reset-portal-work)
  - [Quais dados são usados pela redefinição de senha?](#what-data-is-used-by-password-reset)
  - [Como acessar senha Redefinir dados para seus usuários](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Visão geral de write-back de senha
Senha write-back é um componente de [Conexão do Azure Active Directory](active-directory-aadconnect.md) que pode ser habilitado e usado pelos assinantes atuais do Azure Active Directory Premium. Para obter mais informações, consulte [Azure Active Directory edições](active-directory-editions.md).

Senha write-back permite que você configurar seu locatário de nuvem para escrever senhas para você Active Directory local.  Ele impeça que você precise configurar e gerenciar uma solução de redefinição de senha de autoatendimento complicado local e ele oferece uma maneira conveniente de baseado em nuvem para seus usuários redefinir suas senhas no local onde eles estão.  Continue a ler para alguns dos principais recursos do Write-back de senha:

- **Comentários zero atraso.**  Senha write-back é uma operação síncrona.  Os usuários serão notificados imediatamente se a senha dele não atendeu política ou não pôde ser redefinir ou alteradas por qualquer motivo.
- **Suporte para redefinição de senhas de usuários usando o AD FS ou outras tecnologias de Federação.**  Com write-back de senha, desde que as contas de usuário federado são sincronizadas em seu locatário do Azure AD, eles serão capazes de gerenciar seus locais senhas AD da nuvem.
- **Suporte para redefinição de senhas de usuários usando a sincronização de hash de senha.** Quando o serviço de redefinição de senha detecta que uma conta de usuário sincronizadas está habilitada hash para sincronização de senha, redefinimos local desta conta e a senha de nuvem simultaneamente.
- **Suporta a alteração de senhas do painel de acesso e o Office 365.**  Quando federado ou seria de sincronização de senha os usuários se alterar suas senhas expiradas ou não expirado, vamos escrever as senhas volta ao seu ambiente do AD local.
- **Suporte a gravação de senhas quando um administrador redefini-los da** [**Portal de gerenciamento do azure**](https://manage.windowsazure.com).  Sempre que um redefinições de administrador a senha de um usuário no [Portal de gerenciamento do Azure](https://manage.windowsazure.com), se esse usuário federado ou sincronização de senha, podemos definirá a senha que o administrador seleciona em seu anúncio local, também.  Isso não é suportado atualmente no Portal de administração do Office.
- **Impõe seu local políticas de senha do AD.**  Quando um usuário redefine sua senha, podemos Certifique-se de que ela atende seu local Diretiva AD antes de confirmá-la ao diretório.  Isso inclui histórico, complexidade, idade, filtros de senha e quaisquer outras restrições de senha que você definiu no seu AD local.
- **Não requer quaisquer regras de firewall de entrada.**  Senha write-back usa uma retransmissão de barramento de serviço do Azure como um canal de comunicação subjacentes, significando que você não precisa abrir qualquer portas de entrada em seu firewall para esse recurso funcione.
- **Não há suporte para contas de usuário que existem dentro de grupos protegidos no seu Active Directory local.** Para obter mais informações sobre grupos protegidas, consulte [contas protegidas e grupos do Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Como funciona write-back de senha
Senha write-back tem três componentes principais:

- Serviço de nuvem de redefinição de senha (Isso também é integrado ao páginas de alteração de senha do Azure AD)
- Retransmissão de barramento de serviço do Azure específica do locatário
- Ponto de extremidade de redefinição de senha de local

Eles se encaixam conforme descrito no diagrama abaixo:

  ![][001]

Quando um hash federado ou senha sincroniza seria usuário vem redefinir ou alterar sua senha na nuvem, ocorre o seguinte:

1.  Vamos verificar qual tipo de senha o usuário tem.  Se podemos ver que a senha é gerenciada no local, em seguida, garantimos que o serviço de write-back estiver em execução.  Se for, nós podemos permitir que o usuário continuar, se não estiver, informar ao usuário que a senha dele não é possível redefinir aqui.
2.  Em seguida, o usuário passa as entradas de autenticação adequado e chegue à tela Redefinir senha.
3.  O usuário seleciona uma nova senha e confirma a ele.
4.  Após clicar em enviar, podemos criptografar a senha de texto sem formatação com uma chave simétrica que foi criada durante o processo de configuração de write-back.
5.  Após criptografar a senha, podemos incluí-lo em uma carga que é enviada através de um canal HTTPS para a retransmissão de barramento de serviço específico de locatário (que estamos também configurados para você durante o processo de configuração de write-back).  Nesta retransmissão é protegida por uma senha gerada aleatoriamente que sabe apenas sua instalação local.
6.  Depois que a mensagem chega barramento de serviço, o ponto de extremidade de redefinição de senha é ativado para cima e vê que ela tem uma solicitação de redefinição pendentes automaticamente.
7.  O serviço, em seguida, procura o usuário em questão usando o atributo de âncora de nuvem.  Para esta pesquisa para o sucesso, o objeto de usuário deve existir no espaço do conector AD, ele deve ser vinculado ao objeto MV correspondente e ele deve ser vinculado ao objeto de conector AAD correspondente. Por fim, em ordem de sincronização encontrar essa conta de usuário, o link de objeto de conector do AD para MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.  Isso é necessária porque quando a chamada for recebida da nuvem, o mecanismo de sincronização usa o atributo cloudAnchor para procurar o objeto do espaço de conector AAD, então, segue o link de volta para o objeto de MV e segue o link de volta para o objeto do AD. Porque pode haver vários objetos AD (floresta vários) para o mesmo usuário, o mecanismo de sincronização depende do `Microsoft.InfromADUserAccountEnabled.xxx` link escolher correto.
8.  Depois que a conta de usuário for encontrada, podemos tentar redefinir a senha diretamente na floresta AD apropriada.
9.  Se a operação de conjunto de senha for bem-sucedida, podemos dizer ao usuário a senha dele foi modificada e que eles podem ir na sua maneira feliz.
10. Se a senha definida operação falha, estamos retornar o erro para o usuário e permitir que ela tente novamente.  A operação pode falhar porque o serviço foi para baixo, porque a senha que eles selecionados não atendeu políticas de organização, porque não foi possível encontrar o usuário no AD local, ou qualquer número de motivos.  Temos uma mensagem específica para muitos desses casos e informar ao usuário que ele podem fazer para resolver o problema.

### <a name="scenarios-supported-for-password-writeback"></a>Cenários com suporte para write-back de senha
A tabela abaixo descreve quais cenários com suporte para quais versões do nossos recursos de sincronização.  Em geral, é altamente recomendável que você instale a versão mais recente do [Azure AD Connect](active-directory-aadconnect.md#install-azure-ad-connect) se você quiser usar senha write-back.

  ![][002]

### <a name="password-writeback-security-model"></a>Modelo de segurança de write-back de senha
Senha write-back é um serviço altamente seguro e robusto.  Para garantir que suas informações estiver protegidas, podemos habilitar um modelo de segurança hierárquico 4 descrito abaixo.

- **Retransmissão de barramento de serviço específico do locatário** – quando você configura o serviço, podemos configurar uma retransmissão de barramento de serviço locatário específico que está protegida por uma senha forte gerada aleatoriamente que Microsoft nunca tenha acesso ao.
- **Bloqueado para baixo, chave de criptografia de senha forte criptografia,** – após a retransmissão de barramento de serviço é criada, podemos criar uma chave simétrica forte que usamos para criptografar a senha, como se trata durante a transmissão.  Esta tecla reside apenas no repositório de secreta da sua empresa na nuvem, que é muito bloqueado e auditado, assim como qualquer senha no diretório.
- **TLS padrão do setor** – quando uma senha reconfigure ou altere a operação ocorre na nuvem, podemos levar a senha de texto sem formatação e criptografá-la com sua chave pública.  Podemos então joga que em uma mensagem HTTPS que é enviada por um canal criptografado usando certificados SSL da Microsoft para a retransmissão de barramento de serviço.  Após essa mensagem chega em barramento de serviço, seu agente local é ativado para cima, autentica barramento de serviço usando a senha forte gerada anteriormente, seleciona a mensagem criptografada, descriptografa usando a chave privada que podemos gerados e tentativas para definir a senha por meio da API do AD DS SetPassword.  Esta etapa é o que nos permitem impor sua política de senha AD local (complexidade, idade, histórico, filtros, etc.) na nuvem.
- **Políticas de expiração de mensagem** – por fim, se por algum motivo a mensagem fica no serviço carregador porque seu serviço local é pressionada, ele será expirou e removido após alguns minutos para aumentar a segurança ainda mais.

## <a name="how-does-the-password-reset-portal-work"></a>Como a senha redefinir trabalho portal?
Quando um usuário navega para a redefinição de senhas portal, um fluxo de trabalho é desconexão acidental para determinar se essa conta de usuário é válida, que organização que usuários pertence, onde é gerenciada a senha do usuário, e se o usuário é licenciado para usar o recurso ou não.  Leia as etapas abaixo para saber mais sobre a lógica por trás da página de redefinição de senha.

1.  Usuário clica no não consegue acessar o link de conta ou vai diretamente para o [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  Usuário insere uma id de usuário e passa um captcha.
3.  Azure AD verifica se o usuário é capaz de usar esse recurso, fazendo o seguinte:
    - Verifica se o usuário tem esse recurso habilitado e uma licença do Azure AD atribuído.
        - Se o usuário não tem esse recurso habilitado ou uma licença atribuída, o usuário é solicitado a entrar em contato com seu administrador para redefinir sua senha.
    - Verificações que o usuário tenha direita desafiam dados definidos na sua conta de acordo com a política de administrador.
        - Se a política requer apenas um desafio, ele é verificado que o usuário tenha os dados apropriados definidos para pelo menos um dos desafios habilitados pela diretiva de administrador.
          - Se o usuário não estiver configurado, o usuário é aconselhável entrar em contato com seu administrador para redefinir sua senha.
        - Se a política requer dois desafios, ele é verificado que o usuário tenha os dados apropriados definidos para pelo menos duas dos desafios habilitados pela diretiva de administrador.
          - Se o usuário não estiver configurado, nós o usuário é aconselhável contate seu administrador para redefinir sua senha.
    - Verifica se ou não a senha do usuário é gerenciada no local (federado ou tinha de sincronização de hash de senha).
       - Se Write-back é implantado e a senha do usuário é gerenciada no local, o usuário tem permissão para prosseguir para autenticar e redefinir sua senha.
       - Se Write-back não é implantado e a senha do usuário é gerenciada no local, o usuário será solicitado entrar em contato com seu administrador para redefinir sua senha.
4.  Se for determinado que o usuário é possível com êxito redefinir sua senha, o usuário será orientado durante o processo de redefinição.

Saiba mais sobre como implantar senha write-back em [Introdução: gerenciamento de senha do Azure AD](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Quais dados são usados pela redefinição de senha?
A tabela a seguir descreve onde e como esses dados são usados durante a redefinição de senha e foi projetados para ajudá-lo a decidir quais opções de autenticação são apropriadas para sua organização. Esta tabela mostra também quaisquer requisitos de formatação para situações em que você está fornecendo dados em nome de usuários de caminhos de entrada que não validar dados.

> [AZURE.NOTE] Telefone do Office não aparece no portal do registro como os usuários estão atualmente não possam editar essa propriedade no diretório.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nome do método de contato</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de dados do Active Directory do Azure</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Usados / configurável onde?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos de formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefone comercial</p>
            </td>
            <td>
              <p>Número de telefone</p>
              <p>Por exemplo, Set-MsolUser - UserPrincipalName JWarner@contoso.com - o número de telefone "+1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Usadas em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Definível de:</p>
              <p>Número de telefone é configurável do PowerShell, DirSync, Portal de gerenciamento do Azure e o Portal de administração do Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (por exemplo, 1234567890 + 1)</p>
              <ul>
                <li class="unordered">
Deve fornecer um código de país/região<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve fornecer um código de área (quando aplicável)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Forneça um + na frente do país código<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve ter um espaço entre o código do país e o resto do número<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Extensões não forem compatíveis, se você tiver qualquer extensões especificadas, podemos irá separá-lo do número antes de expedir a chamada telefônica.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefone celular</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OU</p>
              <p>MobilePhone</p>
              <p>(Autenticação que telefone será usado se houver dados presentes, caso contrário, isso volta ao campo celular).</p>
              <p>Por exemplo, Set-MsolUser - UserPrincipalName JWarner@contoso.com - MobilePhone "+1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Usadas em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Portal de registro</p>
              <p>Definível de: </p>
              <p>AuthenticationPhone é configurável do portal de registro de redefinição de senha ou portal de registro MFA.</p>
              <p>MobilePhone é configurável do PowerShell, DirSync, Portal de gerenciamento do Azure e o Portal de administração do Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (por exemplo, 1234567890 + 1)</p>
              <ul>
                <li class="unordered">
Deve fornecer um código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve fornecer um código de área (quando aplicável).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve ter fornecer código um + na frente do país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Deve ter um espaço entre o código do país e o resto do número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Extensões não forem compatíveis, se você tiver qualquer extensões especificadas, podemos ignorá-la ao expedir a chamada telefônica.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Email alternativo</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OU</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(Autenticação que email será usado se houver dados presentes, caso contrário, isso volta para o campo de Email alternativo).</p>
              <p>Observação: o campo de email alternativo é especificado como uma matriz de cadeias de caracteres no diretório.  Usamos a primeira entrada nesta matriz.</p>
              <p>Por exemplo, Set-MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Usadas em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Portal de registro</p>
              <p>Definível de: </p>
              <p>AuthenticationEmail é configurável do portal de registro de redefinição de senha ou portal de registro MFA.</p>
              <p>AlternateEmail é configurável do PowerShell, o Portal de gerenciamento do Azure e o Portal de administração do Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>ou甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Emails deverão seguir a formatação padrão como por.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Unicode emails são suportados.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Perguntas e respostas segurança</p>
            </td>
            <td>
              <p>Não disponível para modificar diretamente no diretório.</p>
            </td>
            <td>
              <p>Usadas em:</p>
              <p>Portal de redefinição de senha</p>
              <p>Portal de registro </p>
              <p>Definível de: </p>
              <p>A única maneira de definir perguntas de segurança é por meio do Portal de gerenciamento do Azure.</p>
              <p>A única maneira de definir respostas para perguntas de segurança para um determinado usuário é por meio do Portal de registro.</p>
            </td>
            <td>
              <p>Perguntas sobre segurança têm um máximo de 200 caracteres e um mínimo de 3 caracteres</p>
              <p>Respostas têm um máximo de 40 caracteres e um mínimo de 3 caracteres</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>Como acessar senha Redefinir dados para seus usuários
####<a name="data-settable-via-synchronization"></a>Dados configuráveis através da sincronização
Os campos a seguir podem ser sincronizados do local:

* Telefone celular
* Telefone comercial

####<a name="data-settable-with-azure-ad-powershell"></a>Dados configuráveis com o Azure AD PowerShell
Os campos a seguir são acessíveis com Azure AD PowerShell & API do gráfico:

* Email alternativo
* Telefone celular
* Telefone comercial
* Telefone de autenticação
* Email de autenticação

####<a name="data-settable-with-registration-ui-only"></a>Dados configuráveis com apenas ao registro de interface do usuário
Os seguintes campos só são acessíveis via o registro SSPR UI (https://aka.ms/ssprsetup):

* Perguntas e respostas segurança

####<a name="what-happens-when-a-user-registers"></a>O que acontece quando um usuário registra?
Quando um usuário registra, a página de registro serão **sempre** definir os seguintes campos:

* Telefone de autenticação
* Email de autenticação
* Perguntas e respostas segurança

Se você tiver fornecido um valor para **celular** ou **Email alternativo**, os usuários podem imediatamente usar aqueles redefinir suas senhas, mesmo se eles ainda não registrado no serviço.  Além disso, os usuários serão consulte esses valores ao registrar pela primeira vez e modificá-las se desejarem.  No entanto, depois que eles registram com êxito, esses valores serão persistentes nos campos **Telefone de autenticação** e de **Email de autenticação** , respectivamente.

Isso pode ser uma maneira útil para desbloquear grandes números de usuários para usar SSPR enquanto ainda permitindo que os usuários validar essas informações durante o processo de registro.

####<a name="setting-password-reset-data-with-powershell"></a>Dados com o PowerShell de redefinição de senha de configuração
Você pode definir valores para os seguintes campos com o Azure AD PowerShell.

* Email alternativo
* Telefone celular
* Telefone comercial

Para começar, você precisará primeiro [baixar e instalar o módulo do PowerShell do Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Quando você tiver instalado, você pode seguir as etapas abaixo para configurar cada campo.

#####<a name="alternate-email"></a>Email alternativo
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Telefone celular
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Telefone comercial
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Dados com o PowerShell de redefinição de senha de leitura
Você pode ler os valores para os seguintes campos com o Azure AD PowerShell.

* Email alternativo
* Telefone celular
* Telefone comercial
* Telefone de autenticação
* Email de autenticação

Para começar, você precisará primeiro [baixar e instalar o módulo do PowerShell do Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule).  Quando você tiver instalado, você pode seguir as etapas abaixo para configurar cada campo.

#####<a name="alternate-email"></a>Email alternativo
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Telefone celular
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Telefone comercial
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>Telefone de autenticação
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>Email de autenticação
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Links para senha redefinir documentação
A seguir são links para todas as páginas de documentação de redefinição de senha do Azure AD:

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
* [**Como ele funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os diferentes seis componentes do serviço e o que cada
* [**Introdução**](active-directory-passwords-getting-started.md) - Aprenda a permitem aos usuários reiniciar e alterar suas senhas de nuvem ou local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar a aparência e a aparência e o comportamento do serviço às necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) – Saiba como implantar rapidamente e efetivamente gerenciar senhas em sua organização
* [**Obter ideias**](active-directory-passwords-get-insights.md) - Saiba mais sobre nossos recursos de geração de relatórios integrados
* [**Perguntas Frequentes**](active-directory-passwords-faq.md) - Obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente solucionar problemas com o serviço



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
