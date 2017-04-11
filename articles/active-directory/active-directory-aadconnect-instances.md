<properties
    pageTitle="Azure AD Connect: Instâncias de serviço de sincronização | Microsoft Azure"
    description="Esta página documentos considerações especiais para instâncias do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerações especiais sobre instâncias
Azure AD Connect é mais comumente usada com a instância de todo o mundo do Azure AD e o Office 365. Mas também existem outras instâncias e elas têm requisitos diferentes para URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Alemanha de nuvem da Microsoft
A [Microsoft Cloud Alemanha](http://www.microsoft.de/cloud-deutschland) é uma nuvem soberana operada por um objeto de confiança de dados alemão.

Este nuvem está no modo de visualização. Muitos dos cenários que você normalmente pode fazer por você mesmo, como verificar domínios, deve ser feito pelo operador. Entre em contato com o representante da Microsoft para obter mais informações sobre como participar na visualização.

URLs abrir no servidor proxy |
--- |
\*. microsoftonline.de |
\*. no windows.net |
+ Listas de certificados revogados |

Quando você entra no seu diretório Azure AD você deve usar uma conta no domínio de onmicrosoft.de.

Recursos atualmente não presentes na Alemanha do Microsoft Cloud:

- Integridade de conectar-se do Azure AD não está disponível.
- As atualizações automáticas não está disponível.
- Senha write-back não está disponível.

## <a name="microsoft-azure-government-cloud"></a>Nuvem do Microsoft Azure Government
A [nuvem do Microsoft Azure Government](https://azure.microsoft.com/features/gov/) é uma nuvem do governo dos EUA.

Este nuvem tem sido suportado por versões anteriores do DirSync. De compilação 1.1.180 do Azure AD Connect a próxima geração da nuvem é suportada. Esta geração está usando pontos de extremidade com base somente EUA e tem uma lista diferente de URLs para abrir no seu servidor proxy.

URLs abrir no servidor proxy |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Listas de certificados revogados |

Azure AD Connect não será capaz de detectar automaticamente que o diretório do Azure AD está localizado na nuvem governamentais. Em vez disso, você precisa executar as seguintes ações quando você instala o Azure AD Connect.

1. Inicie a instalação do Azure AD Connect.
2. Assim que você vir a primeira página onde você deve para aceitar o EULA, não continue mas deixe o Assistente de instalação em execução.
3. Iniciar regedit e alterar a chave do registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` ao valor `2`.
4. Vá para o Assistente de instalação do Azure AD Connect, aceite o EULA e continue. Durante a instalação, certifique-se de usar o caminho de instalação de **configuração personalizada** (e não Express instalação). Depois, continue a instalação como de costume.

Recursos atualmente não presentes na nuvem Microsoft Azure Government:

- Integridade de conectar-se do Azure AD não está disponível.
- As atualizações automáticas não está disponível.
- Senha write-back não está disponível.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
