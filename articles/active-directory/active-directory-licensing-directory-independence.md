<properties
   pageTitle="Adicionar e gerenciar vários diretórios do Active Directory do Azure | Microsoft Azure"
   description="Instruções e práticas recomendadas para adicionar e gerenciar sua diretórios do Active Directory do Azure, explicando diretórios como um recursos totalmente independentes"
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

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Adicionar e gerenciar vários diretórios do Active Directory do Azure

No Azure Active Directory (AD Azure), cada diretório é um recurso totalmente independente: um ponto, completa e logicamente independente de outros diretórios que você gerencia. Não há nenhuma relação de pai-filho entre diretórios. Essa independência entre diretórios inclui independência de recurso, independência administrativa e independência de sincronização.

##<a name="resource-independence"></a>Independência de recurso

Se você criar ou excluir um recurso em um diretório, ele não tem impacto em qualquer recurso em outro diretório, com a exceção parcial dos usuários externos, descrito a seguir. Se você usar um domínio personalizado 'contoso.com' com um diretório, ele não pode ser usado com qualquer outro diretório.

##<a name="administrative-independence"></a>Independência administrativa

Se um usuário não administrativo do diretório 'Contoso' cria um diretório de teste 'Test' então:
- Por padrão, o usuário que cria um diretório é adicionado como um usuário externo no novo diretório e atribuído a função de administrador global no diretório.
- Os administradores do diretório 'Contoso' têm sem privilégios administrativos diretos ao diretório 'Test', a menos que um administrador de 'Test' especificamente conceder-lhes esses privilégios. Os administradores dos 'Contoso' podem controlar o acesso ao diretório 'Test' se eles controlam a conta de usuário que criou 'Test'.
- Se você alterar (Adicionar ou remover) uma função de administrador para um usuário em um diretório, a alteração não afeta qualquer função de administrador que o usuário pode ter em outro diretório.

##<a name="synchronization-independence"></a>Independência de sincronização

Você pode configurar cada diretório Azure AD independentemente para obter dados sincronizados de uma única instância deles:
  - A ferramenta de sincronização de diretórios (DirSync), para sincronizar dados com uma única floresta do AD.
  - O Azure Active Directory Connector para Gerenciador de identidades Forefront, para sincronizar dados com um ou mais florestas local e/ou fontes de dados não Azure AD.

##<a name="add-an-azure-ad-directory"></a>Adicionar um diretório de Azure AD

Para adicionar um diretório de Azure AD no portal de clássico do Azure, selecione a extensão do Active Directory do Azure à esquerda e toque em **Adicionar**.

> [AZURE.NOTE]   Ao contrário de outros recursos Azure, os diretórios não são recursos de filho de uma assinatura do Azure. Se você cancelar ou permitir que sua assinatura do Azure expire, você ainda pode acessar seus dados de diretório usando o PowerShell do Azure, a API de gráfico do Azure ou outras interfaces como o Centro de administração do Office 365. Você também pode associar outra assinatura com o diretório.

Para obter uma visão geral dos problemas de licenciamento do Azure AD e práticas recomendadas, consulte [o que é Azure Active Directory licenciamento?](active-directory-licensing-what-is.md).
