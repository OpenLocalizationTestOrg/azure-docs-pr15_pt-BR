<properties
    pageTitle="Atividade de entrada irregular"
    description="Um relatório que inclui assinar ins que foram identificados como anômalos por nossa algoritmos de aprendizado de máquina."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="irregular-sign-in-activity"></a>Atividade de entrada irregular

Suplementos de entrada irregulares são aquelas que foram identificados por nossa algoritmos, com base em uma condição de "Impossível viagem" combinado com um sinal de anômalos no local e o dispositivo de aprendizado de máquina. Isso pode indicar que um hacker tenha entrado com êxito usando essa conta.
Enviaremos uma notificação por email para os administradores globais se encontramos 10 ou mais anormais entrar eventos dentro de uma extensão de 30 dias ou menos. Lembre-se de incluir aad-alerts-noreply@mail.windowsazure.com em sua lista de remetentes confiáveis.
