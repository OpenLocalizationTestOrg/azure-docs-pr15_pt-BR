
<properties
    pageTitle="Usando o Azure AD conectar integridade com o AD FS | Microsoft Azure"
    description="Esta é a página de integridade de conectar-se do Azure AD como monitorar seu local infraestrutura do AD FS."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>Usando o Azure AD conectar integridade com o AD FS
A documentação a seguir é específica de monitoramento sua infraestrutura do AD FS com o Azure AD conectar integridade. Para obter informações sobre o monitoramento do Azure AD Connect (Sincronizar) com o Azure AD conectar integridade, consulte [Usando o Azure AD conectar integridade para sincronização](active-directory-aadconnect-health-sync.md). Além disso, para obter informações sobre monitoramento de serviços de domínio Active Directory com o Azure AD conectar integridade, consulte [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertas do AD FS
A seção de alertas de integridade conectar Azure AD fornece a lista de alertas ativos. Cada alerta inclui informações relevantes, etapas de resolução e links para a documentação relacionada.

Você pode clicar duas vezes um alerta ativo ou resolvido, para abrir um novo blade com informações adicionais, etapas que você pode tomar para resolver o alerta e links para a documentação relevante. Você também pode exibir dados históricos sobre alertas que foram resolvidas no passado.

![Azure AD Connect Portal de integridade](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>Análise de uso do AD FS
Azure AD conectar integridade análise de uso analisa o tráfego de autenticação de seus servidores de Federação. Você pode clicar duas vezes na caixa de análise de uso, para abrir a lâmina de análise de uso, que mostra várias métricas e agrupamentos.

>[AZURE.NOTE] Para usar a análise de uso com o AD FS, você deve garantir que o AD FS auditoria está habilitado. Para obter mais informações, consulte [Habilitar auditoria do AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Azure AD Connect Portal de integridade](./media/active-directory-aadconnect-health/report1.png)

Para selecionar métricas adicionais, especificar um intervalo de tempo, ou para alterar o agrupamento, clique com botão direito no gráfico de análise de uso e selecione Editar gráfico. Você pode especificar o intervalo de tempo, selecione uma métrica diferente e alterar o agrupamento. Você pode exibir a distribuição do tráfego de autenticação com base em "métricas" diferentes e agrupar cada métrica usando parâmetros relevantes "Agrupar por" descritos na tabela a seguir:

| Métrica | Agrupar por | Significa que o agrupamento e por que ele é útil? |
| ------ | -------- | -------------------------------------------- |
| Solicitações de total: O número total de solicitações processadas pelo serviço de Federação | Todos os | Mostra a contagem do número total de solicitações sem agrupamento. |
|  | Aplicativo | As solicitações de totais com base na terceira parte alvo os grupos. Este agrupamento é útil entender qual aplicativo está recebendo quanto porcentagem de tráfego total. |
|  | Servidor | O total de solicitações com base no servidor que processado a solicitação de grupos. Este agrupamento é útil para entender a distribuição de carga do tráfego total. |
|  | Junção do local de trabalho | Agrupa o total de solicitações com base em se eles são provenientes dispositivos que estão unida do local de trabalho (conhecido). Este agrupamento é útil entender se os recursos são acessados usando dispositivos que são desconhecidos para a infraestrutura de identidade. |
|  | Método de autenticação | O total de solicitações com base no método de autenticação usado para autenticação de grupos. Este agrupamento é útil entender o método de autenticação comum que é usado para autenticação. A seguir é os métodos de autenticação possíveis <ol> <li>Autenticação integrada do Windows (Windows)</li> <li>(Formulários) autenticação baseada em formulários</li> <li>SSO (logon único)</li> <li>Autenticação (certificado) de certificado X509</li> <br>Se os servidores de Federação receberem a solicitação com um Cookie SSO, essa solicitação é contada como SSO (logon único). Nesses casos, se o cookie for válido, o usuário não é solicitado a fornecer credenciais e obtém acesso perfeito para o aplicativo. Esse comportamento é comum se você tiver várias partes terceira protegidos por servidores de Federação. |
|  | Local de rede | Agrupa as solicitações de totais com base no local de rede do usuário. Pode ser qualquer um dos intranet ou extranet. Este agrupamento é útil saber qual a porcentagem do tráfego é proveniente da intranet versus extranet. |
| Total de solicitações de falha: O número total falha solicitações processadas pelo serviço de Federação. <br> (Esta métrica só está disponível no AD FS para o Windows Server 2012 R2)| Tipo de erro | Mostra o número de erros com base nos tipos de erro predefinidos. Este agrupamento é útil entender os tipos comuns de erros. <ul><li>Nome de usuário ou senha incorreto: erros devido a senha ou o nome de usuário incorreta.</li> <li>"Bloqueio extranet": falhas devido as solicitações recebidas de um usuário que foi bloqueado de extranet </li><li> "Expirado senha": falhas devido a usuários que fizerem logon com uma senha expirada.</li><li>"Desativado conta": falhas devido a usuários que fizerem logon com uma conta desabilitada.</li><li>"Autenticação de dispositivo": falhas devido a usuários falhando autenticar usando autenticação do dispositivo.</li><li>"Autenticação de certificado do usuário": falhas devido a usuários falhando autenticar devido a um certificado inválido.</li><li>"MFA": falhas devido a usuário falhando autenticar usando autenticação multifator.</li><li>"Outro credencial": "Autorização de emissão": falhas devido a falhas de autorização.</li><li>"Emissão delegação": falhas devido a erros de delegação de emissão.</li><li>"Token aceitação": falhas devido a ADFS rejeitando o token de um provedor de identidade de terceiros.</li><li>"Protocolo": falha devido a erros de protocolo.</li><li>"Desconhecida": capturar todos. Quaisquer outras falhas que não se encaixam nas categorias definidas.</li> |
|  | Servidor | Grupos os erros com base no servidor. Este agrupamento é útil para entender a distribuição de erro em servidores. Distribuição desigual poderia ser um indicador de um servidor em um estado com defeito. |
|  | Local de rede | Grupos os erros com base no local de rede das solicitações (intranet vs extranet). Este agrupamento é útil para entender o tipo de solicitações que estão falhando. |
|  | Aplicativo | Agrupa as falhas com base no aplicativo de destino (parte confiável). Este agrupamento é útil entender qual aplicativo de destino está vendo o número mais de erros. |
| Contagem de usuário: o número médio de usuários exclusivos ativos no sistema | Todos os | Esta métrica fornece uma contagem do número médio de usuários usando o serviço de federação na fatia tempo selecionado. Os usuários não estiverem agrupados. <br>A média depende da fatia de tempo selecionada. |
|  | Aplicativo | O número médio de usuários com base no aplicativo de destino (parte confiável) os grupos. Este agrupamento é útil entender quantos usuários estão usando o aplicativo. |


## <a name="performance-monitoring-for-ad-fs"></a>Monitorar o desempenho de AD FS
Azure AD conectar integridade monitoramento do desempenho fornece informações de monitoramento em métricas. Marcando a caixa de monitoramento, abre uma nova lâmina com informações detalhadas sobre as métricas.


![Azure AD Connect Portal de integridade](./media/active-directory-aadconnect-health/perf1.png)


Selecionando a opção de filtro na parte superior da lâmina, você pode filtrar por servidor para ver métricas de um servidor individual. Para alterar métricas, clique com botão direito no gráfico de monitoramento sob a lâmina de monitoramento e selecione Editar gráfico. Em seguida, da lâmina do novo que será aberta, você pode selecionar métricas adicionais da lista suspensa e especificar um intervalo de tempo para exibir os dados de desempenho.

## <a name="reports-for-ad-fs"></a>Relatórios do AD FS
Azure AD conectar integridade fornece relatórios sobre o desempenho do AD FS e a atividade. Esses relatórios ajudam os administradores percepção atividades em seus servidores do AD FS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Principais 50 usuários com falhas de logon de nome de usuário/senha

Um dos motivos comuns de uma solicitação de autenticação com falha em um servidor do AD FS é uma solicitação com credenciais inválidas, ou seja, um nome de usuário incorreto ou senha. Geralmente acontece aos usuários devido a senhas complexas, senhas esquecidas ou erros de digitação.

Mas há outros motivos que podem resultar em um número inesperado de solicitações sendo tratado por seus servidores do AD FS, tais como: um aplicativo que caches credenciais de usuário e as credenciais expirarem ou um usuário mal-intencionado tentar entrar em uma conta com uma série de senhas conhecidas. Estes dois exemplos razões são válidas que podem levar a um aumento nas solicitações.

Azure AD conectar integridade do ADFS fornece um relatório sobre superiores 50 usuários com tentativas de login falhas devido a senha ou o nome de usuário inválido. Este relatório é alcançado processando os eventos de auditoria gerados por todos os servidores do AD FS os farms

![Azure AD Connect Portal de integridade](./media/active-directory-aadconnect-health-adfs/report1a.png)

Dentro este relatório você tem fácil acesso às seguintes partes das informações:

- N º total de solicitações com falha com o nome de usuário/senha errada nos últimos 30 dias
- N º médio de usuários que falhou com um logon de nome de usuário/senha incorreta por dia.


Clicando nessa parte leva você para a lâmina de relatório principal que fornece detalhes adicionais. Este blade inclui um gráfico com informações de tendência para ajudar a estabelecer uma linha de base sobre solicitações com o nome de usuário incorreto ou senha. Além disso, ele fornece a lista dos principais 50 usuários com o número de tentativas.

O graph fornece as seguintes informações:

- O número total de falhas de logon devido a uma nome de usuário/senha incorreta em uma base por dia.
- O # total de usuários exclusivos que falhou logon em uma base por dia.
- Endereço IP do cliente da última solicitação

![Azure AD Connect Portal de integridade](./media/active-directory-aadconnect-health-adfs/report3a.png)

O relatório fornece as seguintes informações:

| Item de relatório | Descrição
| ------ | -------- |
|ID de usuário| Mostra a ID de usuário que foi usada. Esse valor é o que o usuário digitou, que em alguns casos é o usuário errado ID sendo usado.|
|Tentativas| Mostra o número total de tentativas para esse ID de usuário específica. A tabela é classificada com o maior número de tentativas em ordem decrescente.|
|Última falha| Mostra o carimbo de hora quando ocorreu a última falha.
|Última falha de IP | Mostra o endereço IP do cliente da solicitação de mau mais recente.|



>[AZURE.NOTE] Este relatório é atualizado automaticamente após cada duas horas com as novas informações coletadas no momento. Como resultado, tentativas de login nas duas últimas horas podem não ser incluídas no relatório.



## <a name="related-links"></a>Links relacionados

* [Azure AD Connect integridade](active-directory-aadconnect-health.md)
* [Instalação do agente de integridade do Azure AD Connect](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operações de integridade](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD conectar Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integridade perguntas Frequentes](active-directory-aadconnect-health-faq.md)
* [Histórico de versões de integridade do Azure AD Connect](active-directory-aadconnect-health-version-history.md)
