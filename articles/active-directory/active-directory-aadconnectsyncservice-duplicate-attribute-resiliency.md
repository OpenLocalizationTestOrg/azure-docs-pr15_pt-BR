<properties
    pageTitle="Resiliência de atributo de sincronização e duplicar de identidade | Microsoft Azure"
    description="Novo comportamento de como lidar com objetos com conflitos de nome UPN ou ProxyAddress durante a sincronização de diretórios usando o Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Resiliência de atributo de sincronização e duplicar de identidade
Resiliência duplicados do atributo é um recurso do Active Directory do Azure que elimina conflito causado por **UserPrincipalName** e **ProxyAddress** conflitos durante a execução de uma das ferramentas de sincronização da Microsoft.

Esses dois atributos geralmente são necessários para ser exclusivo entre todos os **usuário**, **grupo**ou **contato** objetos em um determinado locatário do Active Directory do Azure.

> [AZURE.NOTE] Somente usuários podem ter UPNs.

O novo comportamento que este recurso permite que está na parte de nuvem do pipeline de sincronização, portanto é cliente independente e relevante para qualquer produto de sincronização do Microsoft incluindo Azure AD Connect, DirSync e MIM + conector. O termo genérico "cliente de sincronização" é usado neste documento para representar qualquer um desses produtos.

## <a name="current-behavior"></a>Comportamento atual
Se houver uma tentativa de provisionar um novo objeto com um valor de nome UPN ou ProxyAddress que viole essa restrição de exclusividade, Active Directory do Azure bloqueia esse objeto está sendo criada. Da mesma forma, se um objeto for atualizado com um nome UPN ou ProxyAddress não exclusivo, a atualização falhará. A atualização ou uma tentativa de provisionamento será repetida pelo cliente de sincronização após cada ciclo de exportação e continua falha até que o conflito seja resolvido. Um email de relatório de erro é gerado após cada tentativa e um erro será registrado pelo cliente de sincronização.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com resiliência de atributo duplicado
Em vez de completamente falhando provisionar ou atualizar um objeto com um atributo duplicado, Active Directory do Azure "coloca em quarentena" o atributo duplicado que viole a restrição de exclusividade. Se esse atributo é necessário para provisionar, como o UserPrincipalName, o serviço atribui um valor de espaço reservado. O formato desses valores temporários é  
"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***".  
Se o atributo não for necessário, como um **ProxyAddress**, o Active Directory do Azure simplesmente coloca em quarentena o atributo de conflito e prossegue com a criação do objeto ou a atualização.

Após colocar em quarentena o atributo, informações sobre o conflito são enviadas no mesmo email de relatório de erro usado no comportamento antigo. No entanto, essa informação aparece apenas no relatório de erro uma vez, quando a quarentena acontece, ele não continuar a estar conectado em emails futuras. Além disso, desde que a exportação para esse objeto concluída com êxito, o cliente de sincronização não registra um erro e não repetir a criar / operação durante a sincronização subsequentes ciclos de atualização.

Para oferecer suporte a esse comportamento um novo atributo foi adicionado para as classes de objeto do usuário, grupo e contato:  
**DirSyncProvisioningErrors**

Este é um atributo de valores múltiplos que é usado para armazenar os atributos conflitantes que violem a restrição de exclusividade devem eles adicionados normalmente. Uma tarefa de timer do plano de fundo foi habilitada no Active Directory do Azure que é executado a cada hora para procurar conflitos de atributo duplicado que foram resolvidos e remove automaticamente os atributos em questão de quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Habilitando resiliência de atributo duplicado
Resiliência duplicados do atributo será o novo comportamento padrão em todos os locatários do Active Directory do Azure. Ele é exibido em por padrão para todos os locatários que habilitado sincronização pela primeira vez em 22 de agosto de 2016 ou posterior. Locatários que habilitado sincronização anteriores a essa data terá o recurso habilitado em lotes. Esta implementação começará no setembro de 2016 e uma notificação por email será enviada para o contato de notificação técnica do cada locatário com a data específica quando o recurso será ativado.

Depois de resiliência de atributo duplicado foi ativada não pode ser desabilitado.

Para verificar se o recurso é habilitado para seu locatário, você pode fazer isso ao baixar a versão mais recente do módulo Azure Active Directory PowerShell e em execução:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Se você quiser proativamente para habilitar o recurso antes que ele está ativado para seu locatário, você pode fazer isso ao baixar a versão mais recente do módulo Azure Active Directory PowerShell e em execução:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificando objetos com DirSyncProvisioningErrors
Existem atualmente dois métodos para identificar objetos que possuem esses erros devido a conflitos de propriedade duplicadas, Azure Active Directory PowerShell e o Portal de administração do Office 365. Existem planos para estender adicionais portal baseado em relatórios no futuro.

### <a name="azure-active-directory-powershell"></a>PowerShell do Active Directory do Azure
Para os cmdlets do PowerShell neste tópico, o seguinte é verdadeiro:

- Todos os seguintes cmdlets diferenciam maiusculas de minúsculas.
- O **– ErrorCategory PropertyConflict** sempre deve ser incluído. Atualmente, não há nenhum outro tipo de **ErrorCategory**, mas isso pode ser estendido no futuro.

Primeiro, introdução executando **MsolService conectar** e inserindo as credenciais para um administrador locatário.

Em seguida, use os cmdlets e os operadores a seguir para exibir erros de maneiras diferentes:

1. [Ver todos os](#see-all)

2. [Por tipo de propriedade](#by-property-type)

3. [Por valor conflitante](#by-conflicting-value)

4. [Usando uma cadeia de caracteres de pesquisa](#using-a-string-search)

5. [Classificada](#sorted)

6. [Em uma quantidade limitada ou todos](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Ver todos os
Uma vez conectado, para ver uma lista geral de provisionamento de atributo erros no locatário execute:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isso produz um resultado semelhante ao seguinte:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>Por tipo de propriedade
Para ver erros por tipo de propriedade, adicione o sinalizador **- PropertyName** com o argumento **UserPrincipalName** ou **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor conflitante
Para ver os erros relacionados a uma propriedade específica adicionar o sinalizador **- PropertyValue** (**- PropertyName** deve ser usado também ao adicionar esse sinalizador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>Usando uma cadeia de caracteres de pesquisa
Para fazer uma pesquisa de cadeia de caracteres ampla use o sinalizador **- SequênciaDePesquisa** . Isso pode ser usado independentemente de todos os sinalizadores acima, com exceção de **-ErrorCategory PropertyConflict**, que sempre será necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Em uma quantidade limitada ou todos
1. **MaxResults <Int> ** podem ser usados para limitar a consulta como um número específico de valores.

2. **Todos** podem ser usados para garantir que todos os resultados são recuperados no caso que existe um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administração do Office 365

Você pode exibir erros de sincronização de diretório no Centro de administração do Office 365. O relatório no portal do Office 365 exibe apenas os objetos de **usuário** que tenham esses erros. Ele não mostra informações sobre conflitos entre **os grupos** e **Contatos**.


![Usuários ativos] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Usuários ativos")

Para obter instruções sobre como exibir erros de sincronização de diretório no Centro de administração do Office 365, consulte [identificar erros de sincronização de diretório no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Relatório de erro de sincronização de identidade
Quando um objeto com um conflito de atributo duplicado é tratado com esse novo comportamento uma notificação está incluída no email relatório de erro de sincronização de identidade padrão que é enviado para a notificação técnica de contato para o site principal. No entanto, há uma alteração importante nesse comportamento. No passado, informações sobre um conflito de atributo duplicado seriam incluídas em cada relatório de erro subsequentes até que o conflito foi resolvido. Com esse novo comportamento, a notificação de erro para um determinado conflito só aparecer uma vez - no momento que o atributo conflitante está em quarentena.

Aqui está um exemplo da aparência a notificação de email para um conflito de ProxyAddress:  
    ![Usuários ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Resolvendo conflitos
Resolução e estratégia táticas para esses erros de solução de problemas não deve diferir da maneira como o atributo duplicado erros foram tratados no passado. A única diferença é que a tarefa de timer varre através do locatário no lado do serviço para adicionar automaticamente o atributo em questão o objeto correto quando o conflito foi resolvido.

O artigo a seguir descreve várias estratégias de solução de problemas e resolução: [impedir a sincronização de diretório no Office 365 de atributos inválidos ou duplicados](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhuma dessas questões conhecidas provoca degradação do serviço ou perda de dados. Várias delas são estéticos, outras pessoas causam padrão "*pré-resiliência*" erros de atributo duplicado a ser lançada em vez de colocar em quarentena o atributo de conflito e outro faz com que determinados erros exigem manual extra fix-up.

**Comportamento de núcleo:**

1. Objetos com configurações de atributo específico continuam recebendo erros de exportação em vez dos atributos duplicados sendo em quarentena.  
Por exemplo:

    a. Novo usuário for criado no AD com um UPN de **Joe@contoso.com** e ProxyAddress**smtp:Joe@contoso.com**

    b. As propriedades do objeto em conflito com um grupo existente, onde está o ProxyAddress **SMTP:Joe@contoso.com**.

    c. Ao exportar, um erro de **conflito de ProxyAddress** é lançado em vez de ter os atributos de conflito em quarentena. A operação será repetida após cada ciclo de sincronização subsequentes, como seria antes do recurso de resiliência foi ativado.

2. Se dois grupos são criados no local com o mesmo endereço SMTP, uma falha ao provisionar na primeira tentativa com um erro de **ProxyAddress** duplicado padrão. No entanto, o valor duplicado corretamente em quarentena durante o próximo ciclo de sincronização.

**Relatório de Portal do office**:

1. A mensagem de erro detalhadas para dois objetos em um conjunto de conflito de nome UPN é o mesmo. Isso indica que eles tiveram ambos UPN alterado / em quarentena, quando na verdade apenas deles tinham quaisquer dados alterados.

2. A mensagem de erro detalhadas para um conflito de nome UPN mostra o displayName errado para um usuário que teve seu UPN alterado/em quarentena. Por exemplo:

    a. Sincroniza **o usuário A** primeiro com **UPN = User@contoso.com **.

    b. **Usuário B** tentou ser sincronizado seguida com **UPN = User@contoso.com **.

    c. **Usuário do B** UPN é alterado para **User1234@contoso.onmicrosoft.com** e **User@contoso.com** é adicionada ao **DirSyncProvisioningErrors**.

    d. A mensagem de erro para o **Usuário B** deve indicar que **um usuário** já tenha **User@contoso.com** como um UPN, mas ele mostra displayName próprio **Do usuário B** .



**Relatório de erro de sincronização de identidade**:

O link para obter *etapas sobre como resolver esse problema* está incorreto:  
    ![Usuários ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

Ele deve apontar para [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Consulte também

- [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)

- [Identificar erros de sincronização de diretório no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
