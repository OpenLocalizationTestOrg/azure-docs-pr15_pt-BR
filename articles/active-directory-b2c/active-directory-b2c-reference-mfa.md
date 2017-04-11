<properties
    pageTitle="Active Directory do Azure B2C: Autenticação multifator | Microsoft Azure"
    description="Como habilitar a autenticação multifator em aplicativos voltados para o consumidor protegidos por Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure B2C diretório ativo: Habilitar a autenticação multifator em seus aplicativos voltados para o consumidor

Azure Active Directory (AD Azure) B2C integra-se diretamente com a [Autenticação multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md) para que você possa adicionar uma segunda camada de segurança para experiências de inscrição e entrar em seus aplicativos voltados para o consumidor. E você pode fazer isso sem escrever uma única linha de código. Atualmente, oferecemos suporte verificação de mensagens de telefonema e texto. Se você já criou políticas de inscrição e entrar, você ainda pode habilitar a autenticação multifator.

> [AZURE.NOTE]
Autenticação multifator também pode ser habilitada quando você criar políticas de inscrição e entrar, não apenas editando políticas existentes.

Este recurso ajuda os aplicativos a lidar com situações como estas:

- Você não exija a autenticação multifator acessar um aplicativo, mas você solicitar acesso a outra. Por exemplo, o consumidor pode entrar em um aplicativo de seguro automática com uma conta social ou local, mas deve verificar o número de telefone antes de acessar o aplicativo de seguro residencial registrado no mesmo diretório.
- Você não exija a autenticação multifator acessar um aplicativo em geral, mas você solicitar acesso as partes confidenciais dentro dela. Por exemplo, o consumidor pode entrar em um aplicativo bancário com uma conta de social ou local e seleção saldo da conta, mas deve verificar o número de telefone antes de tentar uma transferência.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificar a diretiva de inscrição para habilitar a autenticação multifator

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição**.
3. Clique em sua política de inscrição (por exemplo, "B2C_1_SiUp") para abri-lo.
4. Clique em **autenticação multifator** e ativar o **estado** para **ativado**. Clique em **Okey**.
5. Clique em **Salvar** na parte superior da lâmina.

Você pode usar o recurso de "Executar agora" na política para verificar a experiência do consumidor. Confirme o seguinte:

Uma conta de cliente será criada no diretório antes que a etapa de autenticação multifator ocorra. Durante a etapa, o consumidor é solicitado a fornecer seu número de telefone e verificá-lo. Se a verificação for bem-sucedida, o número de telefone está anexado à conta de cliente para uso posterior. Mesmo que o consumidor cancela ou elimine, ele ou ela pode ser solicitada a verificar um número de telefone novamente durante o próximo logon do (com autenticação multifator habilitado).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificar sua política entrar para ativar a autenticação multifator

1. [Siga estas etapas para navegar até a lâmina de recursos B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de entrada**.
3. Clique em sua política entrar (por exemplo, "B2C_1_SiIn") para abri-lo. Clique em **Editar** na parte superior da lâmina.
4. Clique em **autenticação multifator** e ativar o **estado** para **ativado**. Clique em **Okey**.
5. Clique em **Salvar** na parte superior da lâmina.

Você pode usar o recurso de "Executar agora" na política para verificar a experiência do consumidor. Confirme o seguinte:

Quando o consumidor sinais em (usando uma conta social ou local), se um número de telefone verificado é anexado à conta do consumidor, ele ou ela será solicitada a verificá-lo. Se nenhum número de telefone estiver conectado, o consumidor é solicitado a fornecer um e verificá-lo. Em verificação bem-sucedida, o número de telefone é anexado à conta consumidor para uso posterior.

## <a name="multi-factor-authentication-on-other-policies"></a>Autenticação multifator em outras políticas

Conforme descrito para diretivas de inscrição & entrada acima, também é possível habilitar a autenticação multifator na inscrição ou as políticas de redefinição de senha e políticas de entrada. Ele estará disponível em breve em políticas de edição de perfil.
