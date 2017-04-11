<properties
   pageTitle="Azure Active Directory ao relatar: Guia de Introdução | Microsoft Azure"
   description="Lista os vários relatórios disponíveis nos relatórios do Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Introdução aos relatórios do Azure Active Directory

## <a name="what-it-is"></a>O que é

Azure Active Directory (AD Azure) inclui segurança, atividade e relatórios de auditoria para seu diretório. Aqui está uma lista dos relatórios incluídos:

### <a name="security-reports"></a>Relatórios de segurança

- Suplementos de sinal de fontes desconhecidas
- Suplementos de entrada após várias falhas
- Suplementos de entrada de várias geografia
- Suplementos de entrada de endereços IP com atividades suspeitas
- Atividade de entrada irregular
- Suplementos de entrada de dispositivos possivelmente infectados
- Usuários com atividade anormal entrar

### <a name="activity-reports"></a>Relatórios de atividade

- Uso do aplicativo: Resumo
- Uso do aplicativo: detalhadas
- Painel de controle do aplicativo
- Erros de provisionamento de conta
- Dispositivos de usuário individual
- Atividade de usuário individual
- Relatório de atividades de grupos
- Relatório de atividade de registro de redefinição de senha
- Atividade de redefinição de senha

### <a name="audit-reports"></a>Relatórios de auditoria

- Relatório de auditoria de diretório

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, confira [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md).



## <a name="how-it-works"></a>Como ele funciona


### <a name="reporting-pipeline"></a>Pipeline de relatório

O pipeline de subordinação consiste em três etapas principais. Sempre que um usuário entra ou uma autenticação é feita, acontece o seguinte:

- Primeiro, o usuário é autenticado (com sucesso ou não) e o resultado é armazenado em bancos de dados de serviço do Active Directory do Azure.
- Em intervalos regulares, entrada recentes todos os suplementos são processadas. Neste ponto, nosso segurança e algoritmos de atividade anormal estiver pesquisando todas as entrada recentes ins para atividades suspeitas.
- Após o processamento, os relatórios são escritos, cache e served no portal de clássico do Azure.

### <a name="report-generation-times"></a>Tempos de geração de relatórios

Devido ao grande volume de autenticação e ins processados pela plataforma do Azure AD de entrada, os mais recentes sinal-ins processados são, em média, um horas antigos. Em casos raros, pode levar até 8 horas para processar os suplementos de entrada mais recentes.

Você pode encontrar o mais recente processado entrar examinando o texto de ajuda na parte superior de cada relatório.

![Texto de ajuda na parte superior de cada relatório](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, confira [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md).



## <a name="getting-started"></a>Guia de Introdução


### <a name="sign-into-the-azure-classic-portal"></a>Entrar no portal de clássico do Azure

Primeiro, você precisará entrar no [portal clássico Azure](https://manage.windowsazure.com) como global ou administrador de conformidade. Você também deve ser um administrador de serviço de assinatura Azure ou colegas ou estar usando o "acesso ao Azure AD" assinatura do Azure.

### <a name="navigate-to-reports"></a>Navegue para relatórios

Para exibir relatórios, navegue até a guia Relatórios na parte superior do seu diretório.

Se esta for sua primeira vez exibindo os relatórios, você precisará aceitar uma caixa de diálogo antes de você pode exibir os relatórios. Isso é para garantir que ele seja aceitável para administradores na sua organização para visualizar esses dados, que podem ser considerados informações privadas em alguns países.

![Caixa de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Explore cada relatório

Navegar em cada relatório para ver os dados coletados e os sinal de suplementos processados. Você pode encontrar uma [lista de todos os relatórios aqui](active-directory-reporting-guide.md).

![Todos os relatórios](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Baixar os relatórios como CSV

Cada relatório pode ser baixado como arquivo CSV (valor separado por vírgula). Você pode usar esses arquivos no Excel, PowerBI ou programas para posterior analisar seus dados de análise de terceiros.

Para baixar qualquer relatório como um CSV, navegue até o relatório e clique em "Download" na parte inferior.

![Botão baixar](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, confira [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md).





## <a name="next-steps"></a>Próximas etapas

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personalizar alertas para entrada anômalos na atividade

Navegue até a guia "Configurar" do seu diretório.

Role até a seção "Notificações".

Ativar ou desativar a seção "Notificações de entrada-ins anormais de Email".

![A seção notificações](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrar com o Azure AD API de relatório

Consulte [Introdução com a API de relatórios](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Envolva autenticação multifator em usuários

Selecione um usuário em um relatório.

Clique no botão "Habilitar MFA" na parte inferior da tela.

![O botão de autenticação multifator na parte inferior da tela](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, confira [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md).




## <a name="learn-more"></a>Saiba Mais


### <a name="audit-events"></a>Eventos de auditoria

Saiba mais sobre quais eventos são auditados no diretório no [Azure Active Directory relatar eventos de auditoria](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Integração da API

Consulte [Introdução com a API de relatórios](active-directory-reporting-api-getting-started.md) e a [documentação de referência de API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Entrar em contato

Email [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) para comentários, ajuda ou quaisquer dúvidas.

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, confira [Exibir seus relatórios do access e o uso](active-directory-view-access-usage-reports.md).
