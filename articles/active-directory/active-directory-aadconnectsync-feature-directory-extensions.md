<properties
   pageTitle="Sincronização do Azure AD Connect: extensões de diretório | Microsoft Azure"
   description="Este tópico descreve o recurso de extensões de diretório do Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: extensões de diretório
Extensões de diretório permite que você estenda o esquema no Azure AD com seus próprios atributos do Active Directory local. Esse recurso permite que você crie aplicativos LOB consumindo atributos que você continuar a gerenciar locais. Esses atributos podem ser consumidos por meio de [extensões de directory do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Você pode ver os atributos disponíveis usando [Azure AD Graph explorer](https://graphexplorer.cloudapp.net) e [Explorador do Microsoft Graph](https://graphexplorer2.azurewebsites.net/) , respectivamente.

No momento nenhuma carga de trabalho do Office 365 consome esses atributos.

Configurar os atributos adicionais que você deseja sincronizar no caminho configurações personalizadas no Assistente de instalação.
![Assistente de extensão de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) a instalação mostra atributos a seguir, que são candidatos válidos:

- Tipos de objeto do usuário e grupo
- Atributos de valor único: cadeia de caracteres, booliano, inteiro, binário
- Atributos de valores múltiplos: cadeia de caracteres, binário

A lista de atributos é leitura do cache criado durante a instalação do Azure AD Connect. Se você tiver estendido esquema do Active Directory com atributos adicionais, o [esquema deve ser atualizada](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) antes esses novos atributos são visíveis.

Um objeto pode ter até 100 atributos de extensões de diretório. O comprimento máximo é 250 caracteres. Se um valor de atributo for maior, será truncado pelo mecanismo de sincronização.

Durante a instalação do Azure AD Connect, um aplicativo é registrado onde esses atributos estão disponíveis. Você pode ver esse aplicativo no portal do Azure.  
![Aplicativo de extensão do esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Esses atributos agora estão disponíveis por meio do gráfico:  
![Gráfico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Os atributos são precedidos com extensão\_{AppClientId}\_. O AppClientId tem o mesmo valor para todos os atributos no diretório Azure AD.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
