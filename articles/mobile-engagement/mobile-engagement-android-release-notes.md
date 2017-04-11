<properties
    pageTitle="Integração do Android SDK do Azure contrato móvel"
    description="Últimas atualizações e procedimentos para Android SDK para contrato de celular do Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Notas de versão

## <a name="423-08102016"></a>4.2.3 (10/08/2016)

- Nenhum bloqueio WIFI mais.
- Corrigi um bloqueio ao chamar getDeviceId antes de inicialização (bug introduzido em 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)

- Melhorias de estabilidade.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)

- Segurança: desabilite o acesso de arquivo local de exibição da web.
- Segurança: remover `EngagementPreferenceActivity` classe que estende obsoleto e inseguras `PreferenceActivity` classe.
- Segurança: alcance atividades agora estão documentadas para usar `exported="false"`, esse sinalizador também pode ser usado em versões anteriores do SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)

- O SDK está licenciado em MIT.
- Permitem especificar um identificador de dispositivo personalizado no momento da inicialização SDK.

## <a name="415-02012016"></a>4.1.5 (02/01/2016)

- Melhorias de estabilidade.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)

- Melhorias de estabilidade.

## <a name="413-1292015"></a>4.1.3 (9/12/2015)

- Melhorias de estabilidade.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)

- Melhorias de estabilidade.

## <a name="411-11042015"></a>4.1.1 (04/11/2015)

- Melhorias de estabilidade.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)

- Manipular o novo modelo de permissão para o Android M.
- Agora é possível configurar os recursos de localização em tempo de execução em vez de usar `AndroidManifest.xml`.
- Corrigir um erro de permissão: se você usar `ACCESS_FINE_LOCATION`, em seguida, `ACCESS_COARSE_LOCATION` não é mais necessária.
- Melhorias de estabilidade.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)

-   Alterações de protocolo interno para facilitar a análise e push mais confiável.
-   Envio nativo (GCM/ADM) agora também é usado para em notificações do aplicativo para que você deve configurar as credenciais de envio nativo para qualquer tipo de campanha de envio.
-   Corrigir a notificação de panorama: fossem exibidos 10s somente após sendo enviado.
-   Corrigir um erro no modo de exibição de web: clicando em um link também estava executando a URL da ação padrão.
-   Corrigi uma falha rara relacionada a gerenciamento de armazenamento local.
-   Corrigi o gerenciamento de cadeia de caracteres de configuração dinâmico.
-   Atualize EULA.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)

-   Versão inicial do Azure contrato móvel
-   configuração de appId é substituída por uma configuração de cadeia de conexão.
-   Removido API para enviar e receber mensagens XMPP aleatório de entidades XMPP aleatório.
-   Removido API para enviar e receber mensagens entre dispositivos.
-   Aprimoramentos de segurança.
-   Controle Google Play e SmartAd removido.
