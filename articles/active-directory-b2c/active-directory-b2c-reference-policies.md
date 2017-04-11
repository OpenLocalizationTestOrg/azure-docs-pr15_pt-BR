<properties
    pageTitle="Azure B2C diretório ativo: Estrutura de políticas extensível | Microsoft Azure"
    description="Um tópico na estrutura de política extensível da Azure Active Directory B2C e sobre como criar vários tipos de política"
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
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-extensible-policy-framework"></a>Azure B2C diretório ativo: Estrutura de políticas extensível

## <a name="the-basics"></a>Noções básicas

A estrutura de política extensível do Azure Active Directory (AD Azure) B2C é a força de núcleo do serviço. Políticas de totalmente descrevem consumidor experiências de identidade como inscrição, entrar ou perfil de edição. Por exemplo, uma política de inscrição permite controlar comportamentos Configurando as seguintes configurações:

- Tipos de conta (contas sociais como o Facebook ou contas locais como o endereço de email) que os consumidores podem usar para se inscrever para o aplicativo.
- Atributos (por exemplo, nome, CEP e tamanho do calçado) a serem coletados do consumidor durante a inscrição.
- Uso de autenticação multifator.
- A aparência e funcionalidade de todas as páginas de inscrição.
- Informações (que manifestos como declarações em um token) que o aplicativo recebe quando a política de execução terminar.

Você pode criar várias diretivas de diferentes tipos em seu locatário e usá-los em seus aplicativos, conforme necessário. Diretivas podem ser reutilizadas em aplicativos. Isso permite que os desenvolvedores definir e modificar consumidor identidade experiências com pouca ou nenhuma alteração ao seu código.

Diretivas estão disponíveis para uso por meio de uma interface de desenvolvedor simples. Seu aplicativo dispara uma política usando uma solicitação de autenticação HTTP padrão (passando um parâmetro de política na solicitação) e recebe um símbolo personalizado como resposta. Por exemplo, a única diferença entre as solicitações de invocar uma política de inscrição e aqueles invocar uma política de entrada são o nome de política usado no parâmetro de cadeia de caracteres de consulta de "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Para obter mais detalhes sobre a estrutura de políticas, consulte esta [postagem de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-policy"></a>Criar uma política de inscrição

Para ativar a inscrição em seu aplicativo, você precisará criar uma política de inscrição. Essa política descreve as experiências que os consumidores irão durante a inscrição e o conteúdo de tokens que o aplicativo receberão inscrições bem-sucedido.

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. O **nome** determina o nome de inscrição política usado pelo seu aplicativo. Por exemplo, insira "SiUp".
5. Clique em **provedores de identidade** e selecione "Inscrição de Email". Opcionalmente, você também pode selecionar provedores de identidade sociais, se já configurado. Clique em **Okey**.
6. Clique em **atributos de inscrição**. Aqui você escolhe atributos que você deseja coletar do consumidor durante a inscrição. Por exemplo, selecione "País/região", "Nome de exibição" e "CEP". Clique em **Okey**.
7. Clique em **declarações de aplicativo**. Aqui você escolhe declarações que você deseja que sejam retornados em tokens enviados de volta para seu aplicativo após uma experiência de inscrição bem-sucedido. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP", "O usuário é o novo" e "ID de objeto do usuário".
8. Clique em **criar**. Observe que a política que acabou de criar aparece como "**B2C_1_SiUp**" (a **B2C\_1\_ ** fragmento é automaticamente adicionado) na lâmina **políticas de inscrição** .
9. Abra a política clicando em "**B2C_1_SiUp**".
10. Selecione "Contoso B2C o aplicativo" no menu suspenso **aplicativos** e `https://localhost:44321/` no **URL de resposta / redirecionar URI** suspensa.
11. Clique em **Executar agora**. Abre uma nova guia do navegador e você pode percorrer a experiência de consumidor de se inscrever para o seu aplicativo.

    > [AZURE.NOTE]
    Ele ocupa em um minuto para criação de políticas e atualizações entrem em vigor.

## <a name="create-a-sign-in-policy"></a>Criar uma política de entrada

Para habilitar entrar em seu aplicativo, você precisará criar uma política de entrada. Essa política descreve as experiências que os consumidores irão durante a entrada e o conteúdo de símbolos que o aplicativo receberá para inscrições bem-sucedido.

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de entrada**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. O **nome** determina o nome de política de entrada usado pelo seu aplicativo. Por exemplo, insira "SiIn".
5. Clique em **provedores de identidade** e selecione "Entrar de conta Local". Opcionalmente, você também pode selecionar provedores de identidade sociais, se já configurado. Clique em **Okey**.
6. Clique em **declarações de aplicativo**. Aqui você escolhe declarações desejado retornado em tokens enviados de volta para seu aplicativo após uma experiência de entrada com êxito. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP" e "ID de objeto do usuário". Clique em **Okey**.
7. Clique em **criar**. Observe que a política que acabou de criar aparece como "**B2C_1_SiIn**" (a **B2C\_1\_ ** fragmento é automaticamente adicionado) na lâmina **diretivas de entrada** .
8. Abra a política clicando em "**B2C_1_SiIn**".
9. Selecione "Contoso B2C o aplicativo" no menu suspenso **aplicativos** e `https://localhost:44321/` no **URL de resposta / redirecionar URI** suspensa.
10. Clique em **Executar agora**. Abre uma nova guia do navegador e você pode percorrer a experiência de consumidor de entrar em seu aplicativo.

    > [AZURE.NOTE]
    Ele ocupa em um minuto para criação de políticas e atualizações entrem em vigor.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou entrar

Esta política controla as duas experiências consumidor de inscrição & entrar com uma única configuração. Os consumidores são levou para o caminho certo (inscrição ou entrar), dependendo do contexto. Ele também descreve o conteúdo de símbolos que o aplicativo receberá inscrições bem-sucedida ou suplementos de entrada.  Um exemplo de código para a política de inscrição ou entrada está [disponível aqui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição ou entrar**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. O **nome** determina o nome de inscrição política usado pelo seu aplicativo. Por exemplo, insira "SiUpIn".
5. Clique em **provedores de identidade** e selecione "Inscrição de Email". Opcionalmente, você também pode selecionar provedores de identidade sociais, se já configurado. Clique em **Okey**.
6. Clique em **atributos de inscrição**. Aqui você escolhe atributos que você deseja coletar do consumidor durante a inscrição. Por exemplo, selecione "País/região", "Nome de exibição" e "Código Postal". Clique em **Okey**.
7. Clique em **declarações de aplicativo**. Aqui você escolhe declarações que você deseja que sejam retornados em tokens enviados de volta para seu aplicativo após uma experiência bem-sucedida de inscrição ou entrar. Por exemplo, selecione "Nome de exibição", "Provedor de identidade", "CEP", "O usuário é o novo" e "ID de objeto do usuário".
8. Clique em **criar**. Observe que a política que acabou de criar aparece como "**B2C_1_SiUpIn**" (a **B2C\_1\_ ** fragmento é automaticamente adicionado) na lâmina **políticas ou de inscrição entrar** .
9. Abra a política clicando em "**B2C_1_SiUpIn**".
10. Selecione "Contoso B2C o aplicativo" no menu suspenso **aplicativos** e `https://localhost:44321/` no **URL de resposta / redirecionar URI** suspensa.
11. Clique em **Executar agora**. Abre uma nova guia do navegador e você poderá executar por meio de experiência do consumidor ou de inscrição entrar conforme configurado.

    > [AZURE.NOTE]
    Ele ocupa em um minuto para criação de políticas e atualizações entrem em vigor.

## <a name="create-a-profile-editing-policy"></a>Criar um política de edição de perfil

Para ativar a edição no seu aplicativo de perfil, você precisará criar um perfil de política de edição. Essa política descreve as experiências que os consumidores irão durante a edição de perfil e o conteúdo de símbolos que o aplicativo receberá na conclusão bem-sucedida.

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de edição de perfil**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. O **nome** determina o nome de política usado pelo seu aplicativo de edição de perfil. Por exemplo, insira "SiPe".
5. Clique em **provedores de identidade** e selecione "Endereço de Email". Opcionalmente, você também pode selecionar provedores de identidade sociais, se já configurado. Clique em **Okey**.
6. Clique em **atributos de perfil**. Aqui você escolhe atributos que o consumidor pode visualizar e editar. Por exemplo, selecione "País/região", "Nome de exibição" e "CEP". Clique em **Okey**.
7. Clique em **declarações de aplicativo**. Aqui você escolhe declarações que você deseja que sejam retornados em tokens enviados de volta para seu aplicativo após um experiência de edição de perfil bem-sucedida. Por exemplo, selecione "Nome de exibição" e "Código Postal".
8. Clique em **criar**. Observe que a política que acabou de criar aparece como "**B2C_1_SiPe**" (a **B2C\_1\_ ** fragmento é automaticamente adicionado) na lâmina **políticas de edição de perfil** .
9. Abra a política clicando em "**B2C_1_SiPe**".
10. Selecione "Contoso B2C o aplicativo" no menu suspenso **aplicativos** e `https://localhost:44321/` no **URL de resposta / redirecionar URI** suspensa.
11. Clique em **Executar agora**. Abre uma nova guia do navegador e você poderá executar por meio do perfil consumidor experiência em seu aplicativo de edição.

    > [AZURE.NOTE]
    Ele ocupa em um minuto para criação de políticas e atualizações entrem em vigor.
    
## <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

Para habilitar senha refinadas redefinir em seu aplicativo, você precisará criar uma política de redefinição de senha. Observe que o locatário para toda a redefinição de senha opção especificada [aqui](active-directory-b2c-reference-sspr.md) é aplicável para políticas de entrada. Essa política descreve as experiências que os consumidores irão durante a redefinição de senha e o conteúdo de símbolos que o aplicativo receberá na conclusão bem-sucedida.

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de redefinição de senha**.
3. Clique em **+ Adicionar** na parte superior da lâmina.
4. O **nome** determina que a redefinição de senhas nome de política usado pelo seu aplicativo. Por exemplo, insira "SSPR".
5. Clique em **provedores de identidade** e selecione "Redefinir a senha usando o endereço de email". Clique em **Okey**.
6. Clique em **declarações de aplicativo**. Aqui você escolhe declarações que você deseja que sejam retornados em tokens enviados de volta para seu aplicativo após um bem-sucedido redefinição de senha experiência. Por exemplo, selecione "ID de objeto do usuário".
7. Clique em **criar**. Observe que a política que acabou de criar aparece como "**B2C_1_SSPR**" (a **B2C\_1\_ ** fragmento é automaticamente adicionado) na lâmina **políticas de redefinição de senha** .
8. Abra a política clicando em "**B2C_1_SSPR**".
9. Selecione "Contoso B2C o aplicativo" no menu suspenso **aplicativos** e `https://localhost:44321/` no **URL de resposta / redirecionar URI** suspensa.
10. Clique em **Executar agora**. Abre uma nova guia do navegador e você pode percorrer a experiência de consumidor de redefinição de senha em seu aplicativo.

    > [AZURE.NOTE]
    Ele ocupa em um minuto para criação de políticas e atualizações entrem em vigor.

## <a name="additional-resources"></a>Recursos adicionais

- [Token, sessão e configuração de logon único](active-directory-b2c-token-session-sso.md).
