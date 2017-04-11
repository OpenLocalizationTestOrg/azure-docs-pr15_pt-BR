<properties
    pageTitle="Relatórios do Azure autenticação multifator"
    description="Descreve como usar o recurso de autenticação multifator de Azure - relatórios."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios no Azure autenticação multifator

Azure autenticação multifator fornece vários relatórios que podem ser usados por você e sua organização. Esses relatórios podem ser acessados por meio do Portal de gerenciamento de autenticação multifator, que requer que você tenha uma licença do provedor de MFA do Azure, ou um MFA do Azure, Azure AD Premium ou pacote de mobilidade do Enterprise. A seguir está uma lista dos relatórios disponíveis.

Você pode acessar relatórios por meio do portal de gerenciamento do Azure.

Nome| Descrição
:------------- | :------------- |
Uso | Uso da relatórios exibir informações sobre o uso geral, usuário resumo e detalhes do usuário.
Status do servidor|Este relatório exibe o status dos servidores de autenticação multifator associada a sua conta.
Histórico de usuário bloqueados|Esses relatórios mostram o histórico de solicitações para bloquear ou desbloquear usuários.
Histórico de usuário ignorados|Mostra o histórico de solicitações para ignorar a autenticação multifator para número de telefone de um usuário.
Alerta de fraude|Mostra um histórico de alertas de fraude enviadas durante o intervalo de datas que você especificou.
Na fila|Relatórios de listas na fila de processamento e seus status. Um link para baixar ou exibir o relatório é fornecido quando o relatório estiver concluído.

## <a name="to-view-reports"></a>Exibir relatórios

1.  Faça logon no http://azure.microsoft.com
2.  À esquerda, selecione Active Directory.
3.  Selecione uma das seguintes opções:
    - **Opção 1**: clique na guia de provedores de autenticação multifator. Selecione seu provedor MFA e clique no botão Gerenciar na parte inferior.
    - **Opção 2**: selecione o diretório e clique na guia Configurar. Na seção autenticação multifator, selecione Gerenciar configurações de serviço. Na parte inferior da página Configurações de serviço de MFA, clique em Ir para o link de portal.
4.  No Portal de gerenciamento do Azure multifator autenticação, você verá o modo de exibição de uma seção de relatório no painel esquerdo. A partir daqui, você pode selecionar os relatórios descritos acima.

<center>![Nuvem](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionais**

* [Para usuários](./end-user/multi-factor-authentication-end-user.md)
* [Autenticação multifator Azure no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
