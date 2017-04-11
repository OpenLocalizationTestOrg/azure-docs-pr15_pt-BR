<properties
   pageTitle="Histórico de versão do conector versão | Microsoft Azure"
   description="Este tópico lista todas as versões dos conectores para Gerenciador de identidades Forefront (FIM) e o Gerenciador de identidade da Microsoft (MIM)"
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
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Histórico de versão de versão do conector
Os conectores para Gerenciador de identidades Forefront (FIM) e o Gerenciador de identidade da Microsoft (MIM) são atualizados com frequência.

>[AZURE.NOTE]
Este tópico é somente no FIM e MIM. Esses conectores não têm suporte no Azure AD Connect.

Este tópico lista todas as versões dos conectores que foram lançadas.

Links relacionados:

- [Baixar conectores mais recentes](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentação de referência de [Conector de LDAP genérico](active-directory-aadconnectsync-connector-genericldap.md)
- Documentação de referência de [Conector de SQL genérico](active-directory-aadconnectsync-connector-genericsql.md)
- Documentação de referência de [Conector de serviços da Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- Documentação de referência de [Conector do PowerShell](active-directory-aadconnectsync-connector-powershell.md)
- Documentação de referência de [Conector do Lotus Domino](active-directory-aadconnectsync-connector-domino.md)

## <a name="111170"></a>1.1.117.0
Lançamento: 2016 março

**Novo conector**  
Versão inicial do [Conector de SQL genérico](active-directory-aadconnectsync-connector-genericsql.md).

**Novos recursos:**

- Conector LDAP genérico:
    - Adicionado suporte para importação de delta com Isode.
- Conector de serviços Web:
    - Atualizado a atividade de csEntryChangeResult e setImportErrorCode para permitir que os erros de nível de objeto a ser retornado voltar para o mecanismo de sincronização.
    - Atualizar os modelos de SAP6 e SAP6User para usar a nova funcionalidade de erro de nível de objeto.
- Conector do Lotus Domino:
    - Para exportar, você precisa de um certifier por catálogo de endereços. Agora você pode usar a mesma senha para todos os certifiers para facilitar o gerenciamento.

**Problemas corrigidos:**

- Conector LDAP genérico:
    - Para IBM Tivoli DS, alguns atributos de referência não foram detectados corretamente.
    - Para abrir LDAP durante a importação de delta, espaços em branco no início e no final de cadeias de caracteres foram truncados.
    - Para Novell e NetIQ, uma exportação que moveu um objeto entre as unidades organizacionais/contêineres e ao mesmo tempo renomeado o objeto falhou.
- Conector de serviços Web:
    - Se o serviço web tinha vários pontos de extremidade para a mesma ligação, em seguida, o conector não corretamente descobriu esses pontos de extremidade.
- Conector do Lotus Domino:
    - Uma exportação do atributo fullName para um banco de dados de email não funcionam.
    - Uma exportação que adicionou tanto removido membro de um grupo só exportados os membros adicionados.
    - Se um documento de anotações é inválido (o atributo isValid definida como false), em seguida, a falha de conector.

## <a name="older-releases"></a>Versões mais antigas
Antes de março de 2016, os conectores foram lançados como tópicos de suporte.

**LDAP genérico**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, setembro de 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, março de 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, janeiro de 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, setembro de 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, março de 2014

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, setembro de 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, setembro de 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, setembro de 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, março de 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, agosto de 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, fevereiro de 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, outubro de 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, agosto de 2013

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
