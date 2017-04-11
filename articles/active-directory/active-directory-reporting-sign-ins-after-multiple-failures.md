<properties
    pageTitle="Entrar ins depois várias falhas"
    description="Um relatório que indica os usuários que tiveram entrado com êxito após várias consecutivas Falha na entrada em tentativas."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Suplementos de entrada após várias falhas
Este relatório indica os usuários que tiveram entrado com êxito após várias consecutivas Falha na entrada em tentativas. Possíveis causas incluem:

- Usuário tinha esquecido sua senha</li><li>Usuário for vítima de um detecção bruta de senha bem-sucedida forçar ataque

Resultados deste relatório mostrará o número de consecutivas entrar tentativas feitas antes da entrar bem-sucedido e um carimbo de hora associado com a primeira bem-sucedida entrar.

**Configurações do relatório**: você pode configurar o número mínimo de entrada falhou consecutivas em tentativas que devem ocorrer antes que ela possa ser exibida no relatório. Quando você fizer alterações a esta configuração é importante observar que essas alterações não serão aplicadas a qualquer assinar falha existente ins que atualmente aparecem no seu relatório existente. No entanto, eles serão aplicados a todos os futuros sinal-ins. Alterações a este relatório só podem ser feitas por administradores licenciados.


![Entrar ins depois várias falhas](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
