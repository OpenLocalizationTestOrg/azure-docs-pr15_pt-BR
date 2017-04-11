<properties
    pageTitle="Azure B2C diretório ativo: Escala de produção versus visualização B2C locatários | Microsoft Azure"
    description="Um tópico nos tipos de locatários do Azure Active Directory B2C"
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
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure B2C diretório ativo: Escala de produção versus visualização B2C locatários

Se você estiver planejando escrever um aplicativo de produção na B2C Azure Active Directory (AD Azure), você precisará ter certeza de que você tenha o locatário direita "tipo" para ir ao vivo em. Para ver o que você tem, siga estas etapas para [navegar até a lâmina de recursos B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure e procure em **tipo de locatário**.

## <a name="summary"></a>Resumo

Azure AD B2C é compatível com aplicativos de produção apenas em B2C de **escala de produção** locatários na América do Norte.

| Tipo de locatário | Países/regiões | Geralmente disponível? |
| ----------- | -------------- | --------------------- |
| **Locatário de produção escala** | Países/regiões da América do Norte | Sim |
| **Locatário de produção escala** | Todos os países/regiões, exceto América do Norte | Não |
| **Locatário de visualização** | Todos os países/regiões | Não |

> [AZURE.NOTE]
Azure locatários AD B2C (para consumidores) estão disponíveis atualmente em alguns países ou regiões onde locatários do Azure AD (para funcionários) estão disponíveis. Leia as seções a seguir para obter mais detalhes.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Locatário de B2C de produção escala na América do Norte

Se você [criou seu locatário B2C](active-directory-b2c-get-started.md) na América do Norte, ou seja, em um dos seguintes países ou regiões: United estados, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidade e Tobago e o **tipo de locatário** em sua interface de usuário de administrador B2C diz **escala de produção**, seu locatário pode ser usado para os aplicativos de produção.

> [AZURE.NOTE]
Locatários de produção escala são capazes de dimensionamento a 100 dos milhões de identidades por locatário.

![Captura de tela de um locatário de produção escala](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Visualizar B2C locatário em qualquer país/região

Se você criou um locatário B2C durante o período de visualização do Azure AD B2C, é provável que seu **locatário digite** diz **Visualizar locatário**. Se esse for o caso, você deve usar seu locatário somente para fins de teste e desenvolvimento e não para aplicativos de produção.

> [AZURE.IMPORTANT]
Há um caminho de migração de uma visualização B2C locatário para um locatário de B2C produção escala. Observe que há conhecidos problemas quando você excluir um locatário visualização B2C e recriar um locatário B2C de produção escala com o mesmo nome de domínio. Você precisa criar um locatário de B2C produção escala com um nome de domínio diferente.

![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Locatário de B2C de produção escala fora da América do Norte

Azure AD B2C atualmente não é geralmente disponível fora da América do Norte. No entanto você pode criar e usar locatários de produção escala, para desenvolvimento e teste fins, um dos seguintes países ou regiões: Algéria, Áustria, Azerbaijão, Bahrein, Belarus, Bélgica, Bulgária, Croácia, Chipre, República Tcheca, Dinamarca, Egito, Estônia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quênia, Kuwait, Letônia, Líbano, Liechtenstein, Lituania, Luxemburgo, Macedônio Macedônia, Malta, Montenegro, Marrocos, Holanda, Nigéria, Noruega , Omã, Paquistão, Polônia, Portugal, Catar, Romênia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovênia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, United Emirados Árabes Unidos e Reino Unido.

Após Azure AD B2C anunciar disponibilidade geral no acima países ou regiões, você pode continuar a usar esses locatários escala de produção e ative o sistema com os aplicativos de produção sem qualquer perda de dados.

## <a name="availability-of-b2c-tenants"></a>Disponibilidade de B2C locatários

B2C locatários não estão disponíveis atualmente no seguintes países ou regiões: Afeganistão, Argentina, Austrália, Brasil, Chile, Colômbia, Equador, RAE de Hong Kong, Índia, Indonésia, Iraque, Japão, Coreia, Malásia, Nova Zelândia, Paraguai, Peru, Filipina, Cingapura, cingalesa, Taiwan, Tailândia, Uruguai e Venezuela. Pretendemos para incluí-los no futuro.
