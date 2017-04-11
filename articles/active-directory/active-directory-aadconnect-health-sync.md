
<properties
    pageTitle="Usando o Azure AD conectar integridade com sincronização | Microsoft Azure"
    description="Esta é a página de integridade de conectar-se do Azure AD que discutimos como para monitorar a sincronização do Azure AD Connect."
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

# <a name="using-azure-ad-connect-health-for-sync"></a>Usando o Azure AD conectar integridade para sincronização
A documentação a seguir é específica de monitoramento Azure AD Connect (Sincronizar) com o Azure AD conectar integridade.  Para obter informações sobre o monitoramento do AD FS com o Azure AD conectar integridade consulte [Azure usando AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md). Além disso, para obter informações sobre monitoramento de serviços de domínio Active Directory com o Azure AD conectar integridade consulte [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect integridade de sincronização](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas para Azure AD conectar saúde de sincronização
O Azure AD conectar integridade seção alertas para sincronização fornece a lista de alertas ativos. Cada alerta inclui informações relevantes, etapas de resolução e links para a documentação relacionada. Selecionando um alerta ativo ou resolvido, você verá um novo blade com informações adicionais, bem como as etapas que você pode tomar para resolver o alerta e links para documentação adicional. Você também pode exibir dados históricos sobre alertas que foram resolvidas no passado.

Selecionando um alerta que será fornecido com informações adicionais, bem como as etapas você pode tomar para resolver o alerta e links para documentação adicional.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Limitado avaliação de alertas
Se Azure AD Connect não está usando a configuração padrão (por exemplo, se a filtragem de atributo for alterada da configuração padrão para uma configuração personalizada), o agente de integridade de conectar-se do Azure AD não carregar os eventos de erro relacionados ao Azure AD Connect.

Isso limita a avaliação de alertas pelo serviço. Você prefere verá uma faixa que indica essa condição no Portal do Azure em seu serviço.

![Azure AD Connect integridade de sincronização](./media/active-directory-aadconnect-health-sync/banner.png)

Você pode alterar isso clicando em "Configurações" e permitindo agente de integridade de conectar-se do Azure AD carregar todos os logs de erro.

![Azure AD Connect integridade de sincronização](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Compreensão de sincronização
Os administradores frequentemente deseja saber sobre o tempo necessário para sincronizar as alterações Azure AD e a quantidade de alterações ocorrendo. Este recurso oferece uma maneira fácil de visualizar isso usando o abaixo gráficos:   

- Latência de operações de sincronização
- Tendência de alteração de objeto

### <a name="sync-latency"></a>Latência de sincronização
Este recurso fornece uma tendência gráfica de latência das operações de sincronização (importar, exportar, etc.) para conectores.  Isso fornece uma maneira rápida e fácil de compreender não apenas a latência das suas operações (maiores se você tiver um conjunto grande de alterações ocorrendo), mas também uma maneira de detectar problemas na latência que pode exigir mais investigação.

![Latência de sincronização](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Por padrão, somente a latência da operação de 'Exportação' para o conector do Azure AD é mostrada.  Para ver mais operações o conector ou para exibir as operações de outros conectores, clique com botão direito no gráfico, selecione gráfico de editar ou clique no botão "Editar gráfico de latência" e escolha a operação específica e conectores.

### <a name="sync-object-changes"></a>Alterações de sincronização do objeto
Este recurso fornece uma tendência gráfica do número de alterações que estão sendo avaliada e exportados para o Azure AD.  Hoje, é difícil tentando coletar essas informações nos logs de sincronização.  O gráfico oferece a você, não apenas uma maneira mais simples de monitorar o número de alterações que estão ocorrendo no seu ambiente, mas também um modo de exibição visual das falhas de que estão ocorrendo.

![Latência de sincronização](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Relatório de erros de sincronização no nível do objeto (prévia)
Este recurso fornece um relatório de erros de sincronização que podem ocorrer quando os dados de identidade são sincronizados entre o Windows Server AD e Azure AD usando Azure AD Connect.

- O relatório aborda erros gravados pelo cliente de sincronização (Azure AD Connect versão 1.1.281.0 ou superior)
- Ele inclui os erros que ocorreram na última operação de sincronização no mecanismo de sincronização. ("Exportar" no conector do Azure AD.)
- Agente de integridade de conectar AD Azure para sincronização deve ter conectividade de saída para os pontos de extremidade necessários para o relatório para incluir os dados mais recentes. Consulte a [seção requisitos](active-directory-aadconnect-health-agent-install.md#Requirements) para obter detalhes.
- O relatório é **atualizado após cada 30 minutos** usando os dados carregados pelo agente de integridade de conectar-se do Azure AD para sincronizar.
Ele fornece os seguintes recursos principais

    - Categorização de erros
    - Lista de objetos com erro por categoria
    - Todos os dados sobre os erros em um só lugar
    - Lado a comparação lado de objetos com erro devido a um conflito
    - Baixe o relatório de erro como um CVS (em breve)

### <a name="categorization-of-errors"></a>Categorização de erros
O relatório categoriza os erros de sincronização existentes nas categorias a seguir:

| Categoria | Descrição |
| -------------- | ----------- |
| Atributo duplicado | Erros ao Azure AD Connect tenta criar ou atualizar objetos com valores duplicados de um ou mais atributos no Azure AD que deve ser exclusivo em um locatário, como proxyAddresses, UserPrincipalName. |
| Incompatibilidade de dados | Erros quando a correspondência de suaves falha corresponder objetos que resultam em erros de sincronização. |
| Falha de validação de dados | Erros devido a dados inválidos, como caracteres sem suporte em atributos críticos como UserPrincipalName, formate erros que falham na validação antes de serem gravadas no Azure AD.|
| Atributo grande | Erros quando um ou mais atributos são maiores do que o tamanho permitido, o tamanho ou a contagem.|
| Outros | Todos os outros erros que não se encaixam nas categorias acima. Com base nos comentários, essa categoria será dividida em subcategorias.

![Resumo do relatório de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![sincronizar categorias de relatório de erro](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos com erro por categoria
Fazer buscas detalhadas em cada categoria fornecerá a lista de objetos tendo o erro dessa categoria.
![Lista de relatório de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalhes do erro
Acompanhar dados está disponível no modo de exibição detalhado para cada erro

- Identificadores para o *Objeto do AD* envolvidos
- Identificadores para o *Objeto do Azure AD* envolvidos (conforme aplicável)
- Descrição de erro e como corrigir
- Artigos relacionados

![Detalhes do relatório de erro de sincronização](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Baixe o relatório de erro como CSV
Esse recurso é em breve. Fique atento a mais atualizações.



## <a name="related-links"></a>Links relacionados
* [Solucionando problemas de erros durante a sincronização](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Resiliência de atributo duplicado](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect integridade](active-directory-aadconnect-health.md)
* [Instalação do agente de integridade do Azure AD Connect](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operações de integridade](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect integridade perguntas Frequentes](active-directory-aadconnect-health-faq.md)
* [Histórico de versões de integridade do Azure AD Connect](active-directory-aadconnect-health-version-history.md)
