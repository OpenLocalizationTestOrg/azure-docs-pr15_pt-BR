<properties
   pageTitle="Obter percepções de dados do Centro de segurança do Azure com o Power BI | Microsoft Azure"
   description="O pacote de conteúdo do Centro de segurança do Azure Power BI torna mais fácil encontrar alertas de segurança, recomendações, ataque recursos e tendências, com base em um conjunto de dados que foi criado para o relatório."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Obter percepções de dados do Centro de segurança do Azure com o Power BI
O [Painel do Power BI](http://aka.ms/azure-security-center-power-bi) para o Centro de segurança do Azure permite que você visualize, analisar e filtrar recomendações e alertas de segurança de qualquer lugar, incluindo seu dispositivo móvel. Use o painel do Power BI para revelar as tendências e ataque padrões - alertas de segurança do modo de exibição por recurso ou endereço IP de origem e periféricos riscos de segurança por recurso ou idade. 

Você pode também combinar recomendações do Centro de segurança e alertas de segurança com outros dados de maneiras interessantes, por exemplo usando dados de [Logs de auditoria do Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) e [A auditoria do banco de dados do SQL Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Ambos oferecem painéis do Power BI e você também pode exportar dados para o Excel para fácil relatórios sobre o estado de segurança de seus recursos de nuvem.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Usando o painel da Central de segurança do Azure para acessar o Power BI
Você também pode usar o painel do Centro de segurança do Azure para acessar relatórios do Power BI. Siga as etapas para executar essa tarefa: 

1. No painel de tarefas do **Centro de segurança do Azure** , clique em botão **exploração no Power BI** .

    ![Conectar-se a Central de segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. A lâmina **Explorar no Power BI** abre no lado direito, conforme mostrado na tela a seguir:

    ![Conectar-se a Central de segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Se você estiver criando o painel do Power BI pela primeira vez, você pode escolher uma das opções a seguir na lâmina **Explorar no Power BI** : 

    - **Painel de obtenção de informações de segurança**: Escolha esta opção se você deseja criar um dashboard que inclui o status de segurança, segmentos e detecções. Essa opção é um mais comum para função DevOps que é responsável por analisar seu status de proteção e detectados alertas em assinaturas.
    - **Painel de gerenciamento de política**: Escolha esta opção se você deseja explorar política de gerenciamento e aplicação.  Essa opção é um mais comum para Central de TI mais dedicados gestão. Eles podem usar esse painel obter visibilidade e ideias no cumprimento da política de segurança em toda a empresa.
    - Se você já tiver um painel do Power BI, clique em **Ir para seu painel atual do Power BI**.

4. Para este exemplo, clique em opção do **Painel de obtenção de informações de segurança** . Se esta for a primeira vez, você está criando um painel do Power BI para o Centro de segurança for solicitado a instalar o pacote de conteúdo. Clique em **obter** botão na janela de **pacotes de conteúdo para o Power BI** conforme mostrado na tela a seguir:

    ![Painel de controle do Azure ideias de segurança do Centro de segurança](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. A janela de **conexão de obtenção de informações de segurança do Azure segurança Centro** aparecem. Verifique se o método de **autenticação** é **oAuth2** conforme mostrado abaixo e clique em botão **entrar** .
    
    ![Autenticação](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Você pode ser solicitado para autenticar novamente com suas credenciais do Azure. Depois de autenticar seu painel será criado. Depois de criado o painel você ver um relatório com a estrutura semelhante, conforme mostrado na tela a seguir:

    ![Painel do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] Uma atualização do relatório está agendada para ser feito diariamente. Caso você enfrentar uma falha nessa atualização, leia [Possíveis problemas de atualização do Power BI de centro de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), para obter mais informações sobre como solucionar problemas.

Aqui você pode ver o número de alertas de segurança e recomendações, bem como o número de VMs, bancos de dados do SQL Azure e recursos de rede monitorados pela Central de segurança do Azure.

Um link para o Centro de segurança do Azure redireciona para o portal do Azure. Os gráficos tornam mais fácil visualizar informações sobre as recomendações de segurança e alertas, incluindo:

- Estado de segurança do recurso
- Recomendações pendentes
- Recomendações de máquina virtual
- Alertas ao longo do tempo
- Recursos atacados
- IPs atacado

Atrás de cada gráfico, existem obtenção de informações adicionais. Selecione um bloco para ver mais informações. Por exemplo, o bloco de **Estado de segurança do recurso** mostra detalhes adicionais sobre pendentes recomendações por recursos conforme mostrado na tela a seguir:

![Recomendações](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Se você clicar em qualquer linha desse gráfico, as outras serão cinza-out e você se concentre apenas em você selecionou. Para retornar ao painel, clique em **Central de segurança do Azure** na opção de **painéis** no painel esquerdo desta página.

> [AZURE.NOTE] Se você gostaria de personalizar seus relatórios adicionando campos adicionais ou alterando visuais existentes, você pode editar o relatório. Para mais informações, leia [interagir com um relatório no modo de edição no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) .

Os blocos de **IPs de invasor** e **alertas ao longo do tempo, os recursos de ataques** terá a saída semelhante quando você clica em cada um dos-lo. Isso acontece porque o relatório agrega informações sobre essas três variáveis e chama **recursos sob ataque** conforme mostrado na tela a seguir:

![Recursos sob ataque](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

Neste ponto pode também salvar uma cópia deste relatório, imprimi-lo ou publicá-lo na web usando as opções disponíveis no menu **arquivo** .

![Menu Arquivo](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Explorar dados central de segurança do Azure com os serviços do Power BI

Conectar aos [Serviços de pacote de conteúdo do Power BI](https://msit.powerbi.com/groups/me/getdata/services) no Power BI e executar as seguintes etapas:

1. Na janela do **Pacote de conteúdo para o Power BI** você verá duas opções, conforme mostrado abaixo.

    ![Pacote de conteúdo para o Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Se já executado a primeira parte deste artigo, você verá apenas uma opção, o que é gerenciamento de política de centro de segurança do Azure.

2. Nesse exemplo, clique em **obter** no bloco **Do Azure gerenciamento de política de centro de segurança** .

3. Na janela **conectar-se ao gerenciamento de política de centro de segurança do Azure** , verifique se selecionar **oAuth2** no **Método de autenticação** suspenso conforme mostrado abaixo e clique em botão **entrar** .

    ![Janela de gerenciamento de política](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Você será redirecionado para uma página de autenticação onde você deve digitar as credenciais que você está usando para se conectar à Central de segurança do Azure. Após o processo de autenticação for concluído, o Power BI começarão a importação de dados para criar seus relatórios. Durante esse tempo, você pode ver a seguinte mensagem no canto direito de seu navegador:

    ![Conectar-se a Central de segurança do Azure usando o Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Quando o painel estiver sendo criado pela primeira vez pode levar mais tempo, principalmente para cenários em que você tiver várias assinaturas. 

5. Quando o processo for concluído, o painel central de segurança do Azure Power BI carregará com o relatório de **Gerenciamento de política** semelhante ao mostrado abaixo:

    ![Painel de gerenciamento de política](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a usar o Power BI na Central de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, consulte o seguinte:

- [Guia de operações e planejamento de centro de segurança do Azure](security-center-planning-and-operations-guide.md) — aprender a planejar a adoção do Centro de segurança do Azure.
- [Definir políticas de segurança na Central de segurança do Azure](security-center-policies.md) — Saiba como configurar as configurações de segurança na Central de segurança do Azure
- [Gerenciando e responder a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerenciar e responder a alertas de segurança
- [Perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md) — localizar perguntas frequentes sobre como usar o serviço
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar postagens no blog sobre segurança do Azure e conformidade
