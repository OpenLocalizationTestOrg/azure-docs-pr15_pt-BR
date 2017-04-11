<properties
    pageTitle="Azure B2C diretório ativo: Personalização de interface de usuário | Microsoft Azure"
    description="Um tópico sobre os recursos de personalização de interface do usuário do Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure B2C diretório ativo: Personalizar a interface do usuário do Azure AD B2C (UI)

Experiência do usuário é fundamental em um aplicativo de voltados para o consumidor. Ele é a diferença entre um aplicativo BOM e um ótimo e entre consumidores simplesmente ativos e aquelas que realmente comprometidas. B2C do Azure Active Directory (AD Azure) permite que você personalize consumidor inscrição, entrar (*consulte a observação abaixo*), perfil editando, e páginas com controle de pixel perfeita de redefinição de senha.

> [AZURE.NOTE]
Atualmente, conta local entrar páginas, sua senha accompaning redefinir páginas e emails de verificação podem ser personalizados apenas usando o [recurso de identidade visual da empresa](../active-directory/active-directory-add-company-branding.md) e não pelos mecanismos descritos neste artigo.

Neste artigo, você lerá sobre:

- O recurso de personalização de interface de usuário de página.
- Uma ferramenta que ajudará você a testar o recurso de personalização de interface do usuário de página usando nosso conteúdo de amostra.
- Os elementos da interface de núcleo em cada tipo de página.
- Práticas recomendadas ao exercer esse recurso.

## <a name="the-page-ui-customization-feature"></a>O recurso de personalização de interface do usuário da página

Com o recurso de personalização de interface do usuário de página, você pode personalizar a aparência do consumidor inscrição, entrada de redefinição de senha e edição de perfil, páginas, (configurando [políticas](active-directory-b2c-reference-policies.md)). Seus clientes terão experiências perfeitas quando navegar entre seu aplicativo e páginas servidas por Azure AD B2C.

Ao contrário de outros serviços onde opções da interface do usuário são limitados ou estão disponíveis apenas via APIs, Azure AD B2C usa uma abordagem moderna (e mais simples) à personalização de interface do usuário da página.

Veja aqui como ele funciona: Azure AD B2C executa código no navegador do seu cliente e usa uma abordagem moderna chamada [Entre origens recurso Compartilhamento (CORS)](http://www.w3.org/TR/cors/) para carregar o conteúdo de uma URL que você especificar em uma política. Você pode especificar diferentes URLs para páginas diferentes. O código mescla elementos de interface do usuário do Azure AD B2C com o conteúdo carregado a partir de sua URL e exibe a página para o consumidor. Tudo o que você precisa fazer é:

1. Criar HTML5 bem formado conteúdo com um `<div id="api"></div>` elemento (precisa ser um elemento vazio) localizado em algum lugar na `<body>`. Este marcas de elemento onde o conteúdo do Azure AD B2C é inserido.
2. Hospede seu conteúdo em um ponto de extremidade HTTPS (com CORS permitidos).
3. Os elementos de interface do usuário que Azure AD B2C insere de estilo.

## <a name="test-out-the-ui-customization-feature"></a>Testar o recurso de personalização de interface do usuário

Se você quiser experimentar o recurso de personalização de interface do usuário usando nosso exemplo HTML e o conteúdo CSS, fornecemos [uma ferramenta auxiliar simples](active-directory-b2c-reference-ui-customization-helper-tool.md) que carrega e configura o conteúdo de amostra no seu armazenamento de Blob do Azure.

> [AZURE.NOTE]
Você pode hospedar seu conteúdo de interface do usuário em qualquer lugar: em servidores web, CDNs, AWS S3, sistemas de compartilhamento de arquivos, etc. Como o conteúdo estiver hospedado em um ponto de extremidade HTTPS publicamente disponível (com CORS permitida), você está pronto. Estamos usando o armazenamento de Blob do Azure apenas para ilustração.

### <a name="the-most-basic-html-content-for-testing"></a>O conteúdo HTML mais básico para teste

Mostrado abaixo é o HTML mais básico conteúdo que você pode usar para testar esse recurso. Use a mesma ferramenta auxiliar fornecida anteriormente para carregar e configurar esse conteúdo em seu armazenamento de Blob do Azure. Em seguida, você pode confirmar que os botões básicos não estilizado & campos de formulário em cada página são exibidos e funcional.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Os principais elementos de interface do usuário de cada tipo de página

Nas seções a seguir, você encontrará exemplos de fragmentos de HTML5 que Azure AD B2C mescla a `<div id="api"></div>` elemento localizado em seu conteúdo. **Não insira esses fragmentos no seu conteúdo HTML 5.** O serviço do Azure AD B2C insere-las em tempo de execução. Use estes exemplos para criar suas próprias folhas de estilo.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Azure conteúdo AD B2C inserido na "identidade provedor seleção página"

Esta página contém uma lista de provedores de identidade que o usuário pode escolher durante a inscrição ou entrar. Estes são os provedores de identidade social como o Facebook e do Google +, ou contas locais (com base no nome de usuário ou endereço de email).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Azure conteúdo AD B2C inserido na "página inscrição conta Local"

Esta página contém um formulário de inscrição que o usuário deve preencher quando se inscrever para uma conta local com base em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas de lista suspensa de seleção única e caixas de seleção múltipla.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure conteúdo de AD B2C inserido na "" Social inscrição página conta"

Esta página contém um formulário de inscrição que o consumidor deve preencher durante a inscrição no usando uma conta existente de um provedor de identidade social como Facebook ou Google +. Esta página é semelhante à conta local inscrição página (mostrada na seção anterior) com exceção dos campos de entrada de senha.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Azure conteúdo AD B2C inserido na "unificado ou de inscrição entrar página"

Esta página controla tanto inscrição & entrar de consumidores, que podem usar provedores de identidade social como o Facebook ou Google + ou contas locais.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Azure conteúdo AD B2C inserido na "autenticação multifator página"

Nesta página, os usuários podem verificar seus números de telefone (usando voz ou texto) durante a inscrição ou entrar.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Azure conteúdo AD B2C inserido na "" Erro página"

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Itens a lembrar ao criar seu próprio conteúdo

Se você estiver planejando usar o recurso de personalização de interface do usuário de página, revise as seguintes práticas recomendadas:

- Não copiar conteúdo de padrão do B2C do Azure AD e tentar modificá-lo. É melhor para construir seu conteúdo HTML5 do zero e usar o conteúdo padrão como referência.
- Em todas as páginas (exceto as páginas de erro) servidas pela entrar, inscrição e políticas de edição de perfil, folhas de estilo que você fornecer terá de substituir as folhas de estilos padrão que adicionamos nestas páginas na <head> fragmentos. Em todas as páginas servidas pela inscrição ou entrar e políticas de redefinição de senha e as páginas de erro em todas as diretivas, você precisará fornecer todo o estilo por conta própria.
- Por razões de segurança, nós não permitem que você inclua qualquer JavaScript no seu conteúdo. Mais do que você precisa devem estar disponíveis prontos para uso. Caso contrário, usar [Voz do usuário](http://feedback.azure.com/forums/169401-azure-active-directory) para solicitar uma nova funcionalidade.
- Versões de navegador com suporte:
    - Internet Explorer 11
    - Internet Explorer 10
    - Internet Explorer 9 (limitado)
    - Internet Explorer 8 (limitado)
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0
