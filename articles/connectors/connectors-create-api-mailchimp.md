<properties
pageTitle="MailChimp | Microsoft Azure"
description="Crie aplicativos de lógica com o serviço de aplicativo do Azure. MailChimp é um serviço de SaaS que permite que os negócios gerenciar e automatizar atividades de marketing de email, incluindo o envio de emails de marketing, mensagens automatizadas e campanhas de destino."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-mailchimp-connector"></a>Introdução ao conector MailChimp

MailChimp é um serviço de SaaS que permite que os negócios gerenciar e automatizar atividades de marketing de email, incluindo o envio de emails de marketing, mensagens automatizadas e campanhas de destino.


>[AZURE.NOTE] Esta versão do artigo se aplica à versão do esquema de 2015-08-01-visualização de aplicativos de lógica.

Você pode começar criando um aplicativo de lógica agora, consulte [criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Disparadores e ações

O conector de MailChimp pode ser usado como uma ação; ela tem entrarão. Todos os conectores suportam de dados nos formatos JSON e XML.

 O conector de MailChimp tem os seguintes as ações e/ou entrarão disponível:

### <a name="mailchimp-actions"></a>Ações de MailChimp
Você pode executar estas ações:

|Ação|Descrição|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Criar uma nova campanha com base em um tipo de campanha, lista de destinatários e configurações de campanha (linha de assunto, título, from_name e reply_to)|
|[newList](connectors-create-api-mailchimp.md#newlist)|Criar uma nova lista na sua conta MailChimp|
|[AddMember](connectors-create-api-mailchimp.md#addmember)|Adicionar ou atualizar um membro da lista|
|[RemoveMember](connectors-create-api-mailchimp.md#removemember)|Exclua um membro de uma lista.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Atualizar informações de um membro de lista específica|
### <a name="mailchimp-triggers"></a>Gatilhos de MailChimp
Você pode ouvir esses evento:

|Disparadores | Descrição|
|--- | ---|
|Quando um membro foi adicionado a uma lista|Aciona um fluxo de trabalho quando um novo membro foi adicionado a uma lista|
|Quando uma nova lista é criada|Dispara um fluxo de trabalho quando uma nova lista é criada|


## <a name="create-a-connection-to-mailchimp"></a>Criar uma conexão para MailChimp
Para criar aplicativos de lógica com MailChimp, primeiro você deve criar uma **conexão** e fornecer os detalhes para as seguintes propriedades:

|Propriedade| Necessário|Descrição|
| ---|---|---|
|Token|Sim|Fornecer credenciais de MailChimp|

>[AZURE.INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos de lógica.

## <a name="reference-for-mailchimp"></a>Referência do MailChimp
Se aplica à versão: 1.0

## <a name="newcampaign"></a>newcampaign
Nova campanha: Criar uma nova campanha com base em um tipo de campanha, lista de destinatários e configurações de campanha (linha de assunto, título, from_name e reply_to)

```POST: /campaigns```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|newCampaignRequest| |Sim|corpo|Nenhum|Objeto de JSON para enviar no corpo com os novos parâmetros de solicitação de campanha|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="newlist"></a>newList
Nova lista: Criar uma nova lista na sua conta MailChimp

```POST: /lists```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|newListRequest| |Sim|corpo|Nenhum|Objeto de JSON para enviar no corpo com os novos parâmetros de solicitação de campanha|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="addmember"></a>AddMember
Adicionar membro à lista: Adicione ou atualize um membro da lista

```POST: /lists/{list_id}/members```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia de caracteres|Sim|caminho|Nenhum|A identificação exclusiva para a lista|
|newMemberInList| |Sim|corpo|Nenhum|Objeto de JSON para enviar no corpo com as novas informações de membro|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="removemember"></a>RemoveMember
Remover membro de lista: excluir um membro de uma lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia de caracteres|Sim|caminho|Nenhum|A identificação exclusiva para a lista|
|member_email|cadeia de caracteres|Sim|caminho|Nenhum|O endereço de email do membro excluir|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="updatemember"></a>updatemember
Atualizar informações de membro: atualizar as informações de um membro da lista específica

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia de caracteres|Sim|caminho|Nenhum|A identificação exclusiva para a lista|
|member_email|cadeia de caracteres|Sim|caminho|Nenhum|O endereço de email exclusivo do membro atualizar|
|updateMemberInListRequest| |Sim|corpo|Nenhum|Objeto de JSON para enviar no corpo com as informações de membro atualizado|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="onmembersubscribed"></a>OnMemberSubscribed
Quando um membro foi adicionado a uma lista: aciona um fluxo de trabalho quando um novo membro foi adicionado a uma lista

```GET: /trigger/lists/{list_id}/members```

| Nome| Tipo de dados|Necessário|Localizado em|Valor padrão|Descrição|
| ---|---|---|---|---|---|
|list_id|cadeia de caracteres|Sim|caminho|Nenhum|A identificação exclusiva para a lista|

#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|202|Aceita|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="oncreatelist"></a>OnCreateList
Quando uma nova lista é criada: dispara um fluxo de trabalho quando uma nova lista é criada

```GET: /trigger/lists```

Não existem parâmetros para esta chamada
#### <a name="response"></a>Resposta

|Nome|Descrição|
|---|---|
|200|Okey|
|202|Aceita|
|400|Solicitação inválida|
|401|Não autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro interno do servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## <a name="object-definitions"></a>Definições de objeto

### <a name="newcampaignrequest"></a>NewCampaignRequest


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|tipo|cadeia de caracteres|Sim |
|destinatários|não definido|Sim |
|Configurações|não definido|Sim |
|variate_settings|não definido|Não |
|Acompanhamento|não definido|Não |
|rss_opts|não definido|Não |
|social_card|não definido|Não |



### <a name="recipient"></a>Destinatário


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|list_id|cadeia de caracteres|Sim |
|segment_opts|não definido|Não |



### <a name="settings"></a>Configurações


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|subject_line|cadeia de caracteres|Sim |
|título|cadeia de caracteres|Não |
|from_name|cadeia de caracteres|Sim |
|reply_to|cadeia de caracteres|Sim |
|use_conversation|booliano|Não |
|to_name|cadeia de caracteres|Não |
|folder_id|número inteiro|Não |
|autenticar|booliano|Não |
|auto_footer|booliano|Não |
|inline_css|booliano|Não |
|auto_tweet|booliano|Não |
|auto_fb_post|matriz|Não |
|fb_comments|booliano|Não |



### <a name="variatesettings"></a>Variate_Settings


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|winner_criteria|cadeia de caracteres|Não |
|tempo_de_espera|número inteiro|Não |
|test_size|número inteiro|Não |
|subject_lines|matriz|Não |
|send_times|matriz|Não |
|from_names|matriz|Não |
|reply_to_addresses|matriz|Não |



### <a name="tracking"></a>Acompanhamento


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Abre|booliano|Não |
|html_clicks|booliano|Não |
|text_clicks|booliano|Não |
|goal_tracking|booliano|Não |
|ecomm360|booliano|Não |
|google_analytics|cadeia de caracteres|Não |
|clicktale|cadeia de caracteres|Não |
|SalesForce|não definido|Não |
|highrise|não definido|Não |
|Cápsula|não definido|Não |



### <a name="rssopts"></a>RSS_Opts


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|feed_url|cadeia de caracteres|Não |
|frequência|cadeia de caracteres|Não |
|constrain_rss_img|cadeia de caracteres|Não |
|cronograma|não definido|Não |



### <a name="socialcard"></a>Social_Card


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|image_url|cadeia de caracteres|Não |
|Descrição|cadeia de caracteres|Não |
|título|cadeia de caracteres|Não |



### <a name="segmentopts"></a>Segment_Opts


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|saved_segment_id|número inteiro|Não |
|CORRESP|cadeia de caracteres|Não |



### <a name="salesforce"></a>SalesForce


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|campanha|booliano|Não |
|anotações|booliano|Não |



### <a name="highrise"></a>Highrise


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|campanha|booliano|Não |
|anotações|booliano|Não |



### <a name="capsule"></a>Cápsula


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|anotações|booliano|Não |



### <a name="schedule"></a>Cronograma


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|hora|número inteiro|Não |
|daily_send|não definido|Não |
|weekly_send_day|cadeia de caracteres|Não |
|monthly_send_date|número|Não |



### <a name="dailysend"></a>Daily_Send


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|domingo|booliano|Não |
|segunda-feira|booliano|Não |
|Terça-feira|booliano|Não |
|quarta-feira|booliano|Não |
|quinta-feira|booliano|Não |
|sexta-feira|booliano|Não |
|Sábado|booliano|Não |



### <a name="campaignresponsemodel"></a>CampaignResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|tipo|cadeia de caracteres|Não |
|create_time|cadeia de caracteres|Não |
|archive_url|cadeia de caracteres|Não |
|status|cadeia de caracteres|Não |
|emails_sent|número inteiro|Não |
|send_time|cadeia de caracteres|Não |
|content_type|cadeia de caracteres|Não |
|destinatário|matriz|Não |
|Configurações|não definido|Não |
|variate_settings|não definido|Não |
|Acompanhamento|não definido|Não |
|rss_opts|não definido|Não |
|ab_split_opts|não definido|Não |
|social_card|não definido|Não |
|report_summary|não definido|Não |
|delivery_status|não definido|Não |
|_links|matriz|Não |



### <a name="absplitopts"></a>AB_Split_Opts


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|split_test|cadeia de caracteres|Não |
|pick_winner|cadeia de caracteres|Não |
|wait_units|cadeia de caracteres|Não |
|tempo_de_espera|número inteiro|Não |
|split_size|número inteiro|Não |
|from_name_a|cadeia de caracteres|Não |
|from_name_b|cadeia de caracteres|Não |
|reply_email_a|cadeia de caracteres|Não |
|reply_email_b|cadeia de caracteres|Não |
|subject_a|cadeia de caracteres|Não |
|subject_b|cadeia de caracteres|Não |
|send_time_a|cadeia de caracteres|Não |
|send_time_b|cadeia de caracteres|Não |
|send_time_winner|cadeia de caracteres|Não |



### <a name="reportsummary"></a>Report_Summary


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Abre|número inteiro|Não |
|unique_opens|número inteiro|Não |
|open_rate|número|Não |
|cliques|número inteiro|Não |
|subscriber_clicks|número|Não |
|click_rate|número|Não |



### <a name="deliverystatus"></a>Delivery_Status


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|habilitado|booliano|Não |
|can_cancel|booliano|Não |
|status|cadeia de caracteres|Não |
|emails_sent|número inteiro|Não |
|emails_canceled|número inteiro|Não |



### <a name="link"></a>Link


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|rel|cadeia de caracteres|Não |
|href|cadeia de caracteres|Não |
|método|cadeia de caracteres|Não |
|targetSchema|cadeia de caracteres|Não |
|esquema|cadeia de caracteres|Não |



### <a name="newlistrequest"></a>NewListRequest


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|nome|cadeia de caracteres|Sim |
|contato|não definido|Sim |
|permission_reminder|cadeia de caracteres|Sim |
|use_archive_bar|booliano|Não |
|campaign_defaults|não definido|Sim |
|notify_on_subscribe|cadeia de caracteres|Não |
|notify_on_unsubscribe|cadeia de caracteres|Não |
|email_type_option|booliano|Sim |
|visibilidade|cadeia de caracteres|Não |



### <a name="contact"></a>Contato


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|empresa|cadeia de caracteres|Sim |
|endereço 1|cadeia de caracteres|Sim |
|endereço 2|cadeia de caracteres|Não |
|Cidade|cadeia de caracteres|Sim |
|estado|cadeia de caracteres|Sim |
|ZIP|cadeia de caracteres|Sim |
|país/região|cadeia de caracteres|Sim |
|telefone|cadeia de caracteres|Sim |



### <a name="campaigndefaults"></a>Campaign_Defaults


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|from_name|cadeia de caracteres|Sim |
|from_email|cadeia de caracteres|Sim |
|Assunto|cadeia de caracteres|Não |
|idioma|cadeia de caracteres|Sim |



### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Sim |
|nome|cadeia de caracteres|Sim |
|contato|não definido|Sim |
|permission_reminder|cadeia de caracteres|Sim |
|use_archive_bar|booliano|Não |
|campaign_defaults|não definido|Sim |
|notify_on_subscribe|cadeia de caracteres|Não |
|notify_on_unsubscribe|cadeia de caracteres|Não |
|Date_Created|cadeia de caracteres|Não |
|list_rating|número inteiro|Não |
|email_type_option|booliano|Sim |
|subscribe_url_short|cadeia de caracteres|Não |
|subscribe_url_long|cadeia de caracteres|Não |
|beamer_address|cadeia de caracteres|Não |
|visibilidade|cadeia de caracteres|Não |
|módulos|matriz|Não |
|estatísticas|não definido|Não |
|_links|matriz|Não |



### <a name="stats"></a>Estatísticas


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|member_count|número inteiro|Não |
|unsubscribe_count|número inteiro|Não |
|cleaned_count|número inteiro|Não |
|member_count_since_send|número inteiro|Não |
|unsubscribe_count_since_send|número inteiro|Não |
|cleaned_count_since_send|número inteiro|Não |
|campaign_count|número inteiro|Não |
|campaign_last_sent|número inteiro|Não |
|merge_field_count|número inteiro|Não |
|avg_sub_rate|número|Não |
|avg_unsub_rate|número|Não |
|target_sub_rate|número|Não |
|open_rate|número|Não |
|click_rate|número|Não |
|last_sub_date|cadeia de caracteres|Não |
|last_unsub_date|cadeia de caracteres|Não |



### <a name="getlistsresponsemodel"></a>GetListsResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|listas|matriz|Não |
|total_items|número inteiro|Não |



### <a name="newmemberinlistrequest"></a>NewMemberInListRequest


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|email_type|cadeia de caracteres|Não |
|status|cadeia de caracteres|Sim |
|merge_fields|não definido|Não |
|interesses|cadeia de caracteres|Não |
|idioma|cadeia de caracteres|Não |
|VIP|booliano|Não |
|local|não definido|Não |
|endereço de email|cadeia de caracteres|Sim |



### <a name="firstandlastname"></a>FirstAndLastName


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|FNAME|cadeia de caracteres|Não |
|SNOME|cadeia de caracteres|Não |



### <a name="location"></a>Local


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|Latitude|número|Não |
|longitude|número|Não |



### <a name="memberresponsemodel"></a>MemberResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Não |
|endereço de email|cadeia de caracteres|Não |
|unique_email_id|cadeia de caracteres|Não |
|email_type|cadeia de caracteres|Não |
|status|cadeia de caracteres|Não |
|merge_fields|não definido|Não |
|interesses|cadeia de caracteres|Não |
|estatísticas|não definido|Não |
|ip_signup|cadeia de caracteres|Não |
|timestamp_signup|cadeia de caracteres|Não |
|ip_opt|cadeia de caracteres|Não |
|timestamp_opt|cadeia de caracteres|Não |
|member_rating|número inteiro|Não |
|last_changed|cadeia de caracteres|Não |
|idioma|cadeia de caracteres|Não |
|VIP|booliano|Não |
|email_client|cadeia de caracteres|Não |
|local|não definido|Não |
|last_note|não definido|Não |
|list_id|cadeia de caracteres|Não |
|_links|matriz|Não |



### <a name="lastnote"></a>Last_Note


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|note_id|número inteiro|Não |
|created_at|cadeia de caracteres|Não |
|criado por|cadeia de caracteres|Não |
|Observação|cadeia de caracteres|Não |



### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|membros|matriz|Não |
|list_id|cadeia de caracteres|Não |
|total_items|número inteiro|Não |



### <a name="object"></a>Objeto






### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|endereço de email|cadeia de caracteres|Não |
|email_type|cadeia de caracteres|Não |
|status|cadeia de caracteres|Sim |
|merge_fields|não definido|Não |
|interesses|cadeia de caracteres|Não |
|idioma|cadeia de caracteres|Não |
|VIP|booliano|Não |
|local|não definido|Não |



### <a name="getmembersresponsemodel"></a>GetMembersResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|membros|matriz|Não |
|list_id|cadeia de caracteres|Não |
|total_items|número inteiro|Não |



### <a name="adduserresponsemodel"></a>AddUserResponseModel


| Nome da propriedade | Tipo de dados | Necessário |
|---|---|---|
|ID|cadeia de caracteres|Sim |
|endereço de email|cadeia de caracteres|Sim |
|unique_email_id|cadeia de caracteres|Não |
|email_type|cadeia de caracteres|Não |
|status|cadeia de caracteres|Não |
|merge_fields|não definido|Sim |
|interesses|cadeia de caracteres|Não |
|estatísticas|não definido|Não |
|ip_signup|cadeia de caracteres|Não |
|timestamp_signup|cadeia de caracteres|Não |
|ip_opt|cadeia de caracteres|Não |
|timestamp_opt|cadeia de caracteres|Não |
|member_rating|número inteiro|Não |
|last_changed|cadeia de caracteres|Não |
|idioma|cadeia de caracteres|Não |
|VIP|booliano|Não |
|email_client|cadeia de caracteres|Não |
|local|não definido|Não |
|last_note|não definido|Não |
|list_id|cadeia de caracteres|Não |
|_links|matriz|Não |


## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
