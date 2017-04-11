<properties
    pageTitle="Azure cmdlets do Active Directory para definir as configurações de grupo | Microsoft Azure"
    description="Como gerenciar as configurações de grupos usando cmdlets do Active Directory do Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure cmdlets do Active Directory para definir as configurações de grupo

As seguintes configurações para grupos unificados podem ser configuradas no seu diretório:

1.  Classificações: a lista separada por vírgulas de classificações que os usuários podem definir em um grupo. Exemplos seria "Classificados", "Segredo" e "Confidencial".

2.  URL de diretrizes de uso: uma URL que aponta usuários para os termos de uso para usar grupos de Unificação de mensagens, conforme definido pela sua organização. Essa URL aparecerá na interface do usuário onde os usuários utilizam grupos.

3.  Agrupar criação habilitada: se nenhum, alguns ou todos os usuários têm permissão para criar grupos de Unificação. Quando definida como on, todos os usuários podem criar grupos. Quando definido para desativado, nenhum usuário pode criar grupos. Quando desativado, você também pode especificar um grupo de segurança cujos usuários que ainda têm permissão para criar grupos.

Essas configurações são definidas usando um configurações e objetos de SettingsTemplate. Inicialmente, você não verá quaisquer objetos de configurações no diretório. Isso significa que o diretório está configurado com as configurações padrão. Para alterar as configurações padrão, você criará um novo objeto de configurações usando um modelo de configurações. Configurações modelos são definidos pela Microsoft.

Você pode baixar o módulo contendo os cmdlets usados para essas operações do [site do Microsoft Connect](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Criar configurações no nível do diretório

Estas etapas criam configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Se você não souber qual SettingTemplate usar, esse cmdlet retorna a lista de modelos de configurações:

    `Get-MsolAllSettingTemplate`

    ![Lista de modelos de configurações](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Para adicionar uma URL de orientação de uso, primeiro é necessário para obter o objeto de SettingsTemplate que define o valor de URL de orientação de uso; ou seja, o modelo de Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Em seguida, crie um novo objeto de configurações com base nesse modelo:

    `$setting = $template.CreateSettingsObject()`

4. Em seguida, atualize o valor de orientação de uso:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Finalmente, aplique as configurações:

    `New-MsolSettings –SettingsObject $setting`

    ![Adicionar uma URL de orientação de uso](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Aqui estão as configurações definidas no SettingsTemplate a Group.Unified.

 **Configuração**                          | **Descrição**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Tipo: cadeia de caracteres<li>Padrão: ""                  | Uma lista delimitada por vírgulas de valores de classificação válida que podem ser aplicados aos grupos de Unificação.                
 <ul><li>EnableGroupCreation<li>Tipo: booliana<li>Padrão: True              | O sinalizador que indica se a criação de grupo de unificado é permitida no diretório.                               
 <ul><li>GroupCreationAllowedGroupId<li>Tipo: cadeia de caracteres<li>Padrão: ""         | GUID do grupo de segurança que é permitido criar grupos Unified mesmo quando EnableGroupCreation = = false.
 <ul><li>UsageGuidelinesUrl<li>Tipo: cadeia de caracteres<li>Padrão: ""                  | Um link para as diretrizes de uso do grupo.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Ler as configurações no nível do diretório

Estas etapas ler as configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Ler todas as configurações de diretório existentes:

    `Get-MsolAllSettings`

2. Ler todas as configurações para um grupo específico:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Leia as configurações do diretório específico usando SettingId GUID:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID de ID de configurações](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Atualizar configurações no nível do diretório

Estas etapas atualize as configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Obter o objeto de configurações existente:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Obtenha o valor que você deseja atualizar:

    `$value = $Setting.GetSettingsValue()`

3. Atualize o valor:

    `$value["AllowToAddGuests"] = "false"`

4. Atualize a configuração:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Remover configurações no nível do diretório

Esta etapa Remove configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe de cmdlet

Você pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Referência de objeto do SettingsTemplate (Group.Unified SettingsTemplate objeto)

- "nome": "EnableGroupCreation", "tipo": ". Boolean", "ValorPadrão": "true", "Descrição": "Um sinalizador booliano que indica se o recurso de criação de grupo de unificado está ativado."

- "nome": "GroupCreationAllowedGroupId", "tipo": "GUID", "ValorPadrão": "", "Descrição": "GUID do grupo de segurança que está na lista branca para criar grupos de unificado".

- "nome": "ClassificationList", "tipo": "System", "ValorPadrão": "", "Descrição": "Uma delimitado por vírgulas lista de valores de classificação válida que podem ser aplicados aos grupos de Unificação."

- "nome": "UsageGuidelinesUrl", "tipo": "System", "ValorPadrão": "", "Descrição": "Um link para as diretrizes de uso de grupo".

nome | tipo | defaultValue | Descrição
----------  | ----------  | ---------  | ----------
"EnableGroupCreation"  | ". Boolean"  | "true"  | "Um sinalizador booliano que indica se o recurso de criação de grupo de unificado está ativado."
"GroupCreationAllowedGroupId"  | "GUID"  | ""  | "GUID do grupo de segurança que está na lista branca para criar grupos de Unificação."
"ClassificationList"  | "System"  | ""  | "Uma delimitado por vírgulas lista de valores de classificação válida que podem ser aplicados aos grupos unificadas."
"UsageGuidelinesUrl"  | "System"  | ""  | "Um link para as diretrizes de uso de grupo".

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar mais documentação do Azure Active Directory PowerShell em [Cmdlets do Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Instruções adicionais do gerente de programa do Microsoft Rob de Jong estão disponível no [Blog de grupos de Pedro](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Gerenciando o acesso aos recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
