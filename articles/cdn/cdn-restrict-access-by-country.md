<properties
    pageTitle="Restringir o acesso ao seu conteúdo do Azure CDN por país | Microsoft Azure"
    description="Saiba como restringir o acesso ao seu conteúdo de CDN Azure usando o recurso de filtragem de localização geográfica."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="casoper"/>

#<a name="restrict-access-to-your-content-by-country---verizon"></a>Restringir o acesso ao seu conteúdo por país - Verizon

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Padrão de Akamai](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Visão geral

Quando um usuário solicita seu conteúdo, por padrão, o conteúdo é atendido, independentemente de onde o usuário feitas esta solicitação de. Em alguns casos, você pode querer restringir o acesso ao seu conteúdo por país. Este tópico explica como usar o recurso de **Filtragem de geográfica** para configurar o serviço para permitir ou bloquear o acesso por país.

> [AZURE.IMPORTANT] Os produtos Verizon e Akamai fornecem a mesma funcionalidade de filtragem de localização geográfica, mas difere de interface do usuário. Este documento descreve a interface do **Azure CDN padrão da Verizon** e **Azure CDN Premium da Verizon**. Para localização geográfica filtragem com o **Azure CDN Standard do Akamai**, consulte [restringir o acesso ao seu conteúdo por país - Akamai](cdn-restrict-access-by-country-akamai.md).

Para obter informações sobre as considerações que se aplicam ao configurar esse tipo de restrição, consulte a seção [Considerações](cdn-restrict-access-by-country.md#considerations) no final do tópico.  

>[AZURE.NOTE] Depois que a configuração é configurada, será aplicada a todos os pontos de extremidade do **Azure CDN da Verizon** neste perfil CDN do Azure.

![Filtragem de país/região](./media/cdn-filtering/cdn-country-filtering.png)

##<a name="step-1-define-the-directory-path"></a>Etapa 1: Definir o caminho do diretório

Ao configurar um filtro de país/região, especifique o caminho relativo para o local para o qual os usuários serão permitidos ou acesso negados. Você pode aplicar a filtragem de país/região para todos os seus arquivos com "/" ou pastas selecionadas, especificando caminhos de diretório.

Filtro de caminho do diretório de exemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg

##<a name="step-2-define-the-action-block-or-allow"></a>Etapa 2: Definir a ação: bloquear ou permitir

**Bloco:** Os usuários de países/regiões especificados serão negados acesso aos ativos solicitados do caminho recursiva. Se não há outras opções de filtragem de país/região tem sido configuradas para esse local, em seguida, todos os outros usuários terão acesso.

**Permitir:** Somente usuários do países especificados poderão ser acesso a ativos solicitada do caminho recursiva.

##<a name="step-3-define-the-countries"></a>Etapa 3: Definir países/regiões

Selecione os países que você deseja bloquear ou permitir para o caminho. Para obter mais informações, consulte [Azure CDN de códigos de país/região Verizon](https://msdn.microsoft.com/library/mt761717.aspx).

Por exemplo, a regra de bloqueio /Photos/Strasbourg/filtre arquivos, incluindo:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Códigos de país/região

O recurso de **Filtragem de localização geográfica** usa códigos de país/região para definir os países da qual uma solicitação será permitida ou bloqueada para um diretório protegido. Você encontrará os códigos de país/região no [Azure CDN de códigos de país/região Verizon](https://msdn.microsoft.com/library/mt761717.aspx). Se você especificar "UE" (Europa) ou "PA" (Ásia Pacífico), um subconjunto de endereços IP que se originam de qualquer país/região em que regiões serão bloqueadas ou permitidas.


##<a id="considerations"></a>Considerações

- Pode levar até 90 minutos para alterações à sua configuração de filtragem de país entrem em vigor.
- Este recurso não dá suporte a caracteres curinga (por exemplo, ' *').
- O país associada com o caminho relativo de configuração de filtragem será aplicado repetidamente para o caminho.
- Apenas uma regra pode ser aplicada ao mesmo caminho relativo (você não pode criar vários filtros de país/região que apontam para o mesmo caminho relativo. Entretanto, uma pasta pode ter vários filtros de país. Isso é devido a natureza recursiva do filtros por país. Em outras palavras, uma subpasta de uma pasta configurada anteriormente pode ser atribuída um filtro de outro país.
