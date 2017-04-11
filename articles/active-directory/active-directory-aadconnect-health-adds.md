
<properties
    pageTitle="Usando o Azure AD conectar integridade com o AD DS | Microsoft Azure"
    description="Esta é a página de integridade de conectar-se do Azure AD que abordará como monitorar AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Usando o Azure AD conectar integridade com o AD DS
A documentação a seguir é específica de serviços de domínio Active Directory com o Azure AD conectar integridade de monitoramento. As versões com suporte do AD DS são: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

Para obter mais informações sobre o monitoramento do AD FS com o Azure AD conectar integridade, consulte [Usando o Azure AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md). Além disso, para obter informações sobre monitoramento Azure AD Connect (Sincronizar) com o Azure AD conectar integridade consulte [Usando o Azure AD conectar integridade para sincronização](active-directory-aadconnect-health-sync.md).

![Azure AD Connect integridade para o AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Alertas para Azure AD conectar integridade para o AD DS
A seção de alertas dentro do Azure AD conectar funcionamento para o AD DS, fornece uma lista dos alertas ativas e resolvidas, relacionados aos controladores de domínio. Selecionar um alerta ativo ou resolvido abre um novo blade com informações adicionais, juntamente com as etapas de resolução e links para a documentação de suporte. Cada tipo de alerta pode ter uma ou mais instâncias, que correspondem a cada um dos controladores de domínio afetados por esse alerta específica. Na parte inferior da lâmina alerta, você pode clicar duas vezes um controlador de domínio afetado para abrir uma lâmina adicional com mais detalhes sobre essa instância alerta.

Dentro este blade, você pode habilitar notificações de email para alertas e alterar o intervalo de tempo no modo de exibição. Expandir o intervalo de tempo permite que você veja alertas resolvidos anteriores.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Painel de controladores de domínio
Esse painel fornece uma visão topológica de seu ambiente, juntamente com o status de integridade de cada um dos seus controladores de domínio monitorar e chaves métricas operacionais. As métricas apresentadas ajudam a identificar rapidamente, controladores de domínio que podem exigir mais investigação. Por padrão, apenas um subconjunto das colunas é exibido. No entanto, você pode encontrar todo o conjunto de colunas disponíveis, clicando no comando de colunas. Selecionando as colunas que você mais se preocupar, transforma esse painel em um único e fácil colocar para exibir a integridade do seu ambiente do AD DS.

![Controladores de domínio](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Controladores de domínio podem ser agrupados por seus respectivo domínio ou site, que é útil para entender a topologia de ambiente. Por fim, se você clicar duas vezes no cabeçalho de blade, o painel maximiza para utilizar a imobiliária de tela disponíveis. Este modo de exibição maior é útil quando várias colunas são exibidas.

## <a name="replication-status-dashboard"></a>Painel de Status de replicação
Esse painel fornece um modo de exibição o status de replicação e a topologia de replicação controladores de domínio monitorar. O status da tentativa de replicação mais recente estiver listado, juntamente com a documentação útil para qualquer erro encontrado. Você pode clicar duas vezes um controlador de domínio com um erro, para abrir um novo blade com informações como: detalhes sobre o erro, recomendado etapas de resolução e links para documentação de solução de problemas.

![Status de replicação](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitoramento
Este recurso fornece tendências gráficas de contadores de desempenho diferentes, que são coletadas continuamente de cada um dos controladores de domínio monitorar. Desempenho de um controlador de domínio pode ser comparado com facilidade entre todos os outros controladores de domínio monitorar na sua floresta. Além disso, você pode ver vários contadores de desempenho lado a lado, que é útil quando a solução de problemas no seu ambiente.

![Monitoramento](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Por padrão, podemos ter pré-selecionados quatro contadores de desempenho; No entanto, você pode incluir outras pessoas, clicando no comando de filtro e marcando ou desmarcando qualquer contador de desempenho desejado. Além disso, você pode clicar duas vezes um gráfico de contador de desempenho para abrir uma nova lâmina, que inclui os pontos de dados para cada um dos controladores de domínio monitorar.

## <a name="related-links"></a>Links relacionados

* [Azure AD Connect integridade](active-directory-aadconnect-health.md)
* [Instalação do agente de integridade do Azure AD Connect](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operações de integridade](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD conectar integridade para sincronização](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect integridade perguntas Frequentes](active-directory-aadconnect-health-faq.md)
* [Histórico de versões de integridade do Azure AD Connect](active-directory-aadconnect-health-version-history.md)
