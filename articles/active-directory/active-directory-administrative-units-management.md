<properties
   pageTitle="Gerenciamento de unidades administrativas no Active Directory do Azure"
   description="Usando unidades administrativas mais granular delegação de permissões no Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gerenciamento de unidades administrativas no Azure AD - Public Preview

Este artigo descreve unidades administrativas – um novo recipiente Azure Active Directory dos recursos que podem ser usados para delegar permissões administrativas sobre subconjuntos de usuários e aplicar políticas para um subconjunto de usuários. No Active Directory do Azure, unidades administrativas permitem que os administradores central delegar permissões a administradores regionais ou para definir a política em um nível granular.

Isso é útil em organizações com divisões independentes, por exemplo, uma grande universidade que consiste em muitos escolas autônomas (Business school, escola de engenharia e assim por diante) que são independentes uns dos outros. Tais divisões têm suas próprias administradores de TI que controlam o acesso, gerenciar usuários e definir políticas especificamente para seu divisão. Administradores da Central deseja poder conceder esses divisões permissões de administradores sobre os usuários de suas divisões específicos. Mais especificamente, usando este exemplo, um administrador central pode, por exemplo, crie uma unidade administrativa de uma escola particular (Business school) e preenchê-lo com apenas os usuários da escola de negócios. Um administrador central pode adicionar da equipe de TI do Business school a uma função de escopo, em outras palavras, conceda a equipe de TI de permissões administrativas do Business escola somente por unidade administrativa da escola de negócios.

> [AZURE.IMPORTANT]
> Você pode criar e usar unidades administrativas somente se você habilitar o Azure Active Directory Premium. Para obter mais informações, consulte [Introdução ao Azure AD Premium](active-directory-get-started-premium.md).

Do ponto de vista do administrador central, uma unidade administrativa é um objeto de diretório que pode ser criado e preenchido com recursos. **Nesta versão, esses recursos podem ser somente os usuários.** Depois de criado e preenchido, a unidade administrativa pode ser usada como um escopo para restringir a permissão concedida apenas sobre os recursos contidos na unidade administrativa.

## <a name="managing-administrative-units"></a>Gerenciando unidades administrativas

Nesta versão de visualização, você pode criar e gerenciar unidades administrativas usando os cmdlets do Active Directory módulo Azure para Windows PowerShell.

Para obter mais informações sobre requisitos de software e instalar o módulo Azure AD e para obter informações sobre os cmdlets do módulo Microsoft Azure AD para o gerenciamento de unidades administrativas, incluindo sintaxe, descrições de parâmetro e exemplos, consulte [Gerenciar Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Próximas etapas
[Edições do Azure Active Directory](active-directory-editions.md)
