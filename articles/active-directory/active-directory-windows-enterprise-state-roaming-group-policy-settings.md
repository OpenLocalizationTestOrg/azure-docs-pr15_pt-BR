<properties
    pageTitle="Configurações de política e o MDM grupo | Microsoft Azure"
    description="Fornece informações sobre a política de grupo e do dispositivo móvel configurações de gerenciamento (MDM) que devem ser usadas em dispositivos corporativos. Essas diretivas são aplicadas ao dispositivo inteira do usuário."
    services="active-directory"
    keywords="o que são configurações de MDM para Roaming de estado de empresa, Roaming de estado de empresa, nuvem do windows e política de grupo"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Configurações de política de grupo e o MDM

Use estes política de grupo e configurações de gerenciamento (MDM) de dispositivo móvel apenas em dispositivos corporativos porque essas políticas são aplicadas ao dispositivo inteira do usuário. Aplicar uma política de MDM para desabilitar a sincronização de configurações para um pessoal, dispositivo de propriedade do usuário afetará negativamente o uso do dispositivo. Além disso, outras contas de usuário no dispositivo também serão afetadas pela política.

Empresas que deseja gerenciar roaming para pessoais dispositivos (não gerenciados) podem usar o portal do Azure para habilitar ou desabilitar roaming, em vez de usar a política de grupo ou MDM.
As tabelas a seguir descrevem as configurações de política disponíveis.

## <a name="mdm-settings"></a>Configurações do MDM
Aplicam as configurações da política MDM para Windows 10 e Windows 10 Mobile.  Suporte do Windows 10 Mobile existe somente para a conta da Microsoft com base em roaming via OneDrive conta de usuário.  Consulte a seção "Dispositivos e pontos de extremidade" para obter detalhes sobre quais dispositivos têm suporte para sincronização do Azure AD com base.

| Nome                               | Descrição                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Permitir a Conexão de conta da Microsoft | Permite que os usuários autenticados usando uma conta da Microsoft do dispositivo |
| Permitir minhas configurações de sincronização             | Permite que os usuários transitar configurações do Windows e dados de aplicativo; Desativar essa política desativará sincronização bem como backups em dispositivos móveis                  |

## <a name="group-policy-settings"></a>Configurações de política de grupo
As configurações de política de grupo se aplicam a dispositivos Windows 10 que fazem parte de um domínio Active Directory. A tabela também inclui configurações herdadas que apareceriam gerenciar as configurações de sincronização, mas não funcionam para o Enterprise estado móvel para Windows 10, que são indicadas com 'não usar' na descrição.

| Nome                                | Descrição |
|-------------------------------------|-------------|
| Contas: Contas do Microsoft de bloco  |Esta configuração de política impede que os usuários adicionando novas contas do Microsoft neste computador|
| Não sincronizar                         |Impede que os usuários para mover as configurações do Windows e dados de aplicativo|
| Não sincronizar personalizar             |Desativa sincronizando do grupo temas|
| Não sincronizar configurações do navegador        |Desativa sincronização do grupo do Internet Explorer|
| Não sincronizar senhas               |Desativa sincronizando do grupo de senhas|
| Não sincronizar outras configurações do Windows  |Desativa sincronização de grupo de configurações de outras janelas|
| Não sincronizar a personalização da área de trabalho |Não use; não tem efeito|
| Não sincronizar em conexões limitadas  |Desativa móveis no limitadas conexões, como celular 3G|
| Não sincronizar aplicativos                    |Não use; não tem efeito|
|Não sincronizar configurações do aplicativo             |Desativa roaming de dados de aplicativo|
|Não sincronizar configurações de início           |Não use; não tem efeito|


## <a name="related-topics"></a>Tópicos relacionados
- [Visão geral de Roaming do Enterprise estado](active-directory-windows-enterprise-state-roaming-overview.md)
- [Habilitar o estado da empresa roaming no Active Directory do Azure](active-directory-windows-enterprise-state-roaming-enable.md)
- [Configurações e dados roaming perguntas Frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Referência de configurações móvel do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
