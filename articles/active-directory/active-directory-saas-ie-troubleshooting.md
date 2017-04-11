<properties
    pageTitle="A extensão do painel de acesso de solução de problemas para o Internet Explorer | Microsoft Azure"
    description="Como usar a política de grupo para implantar o complemento do Internet Explorer para o portal de meus aplicativos."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>A extensão do painel de acesso de solução de problemas para o Internet Explorer

Este artigo ajudará você a solucionar os problemas a seguintes:

- Você não consegue acessar seus aplicativos por meio do portal de meus aplicativos durante o uso do Internet Explorer.
- Você verá a mensagem "Instalar Software" Embora você já tiver instalado o software.

Se você for um administrador, consulte também: [como implantar a extensão do painel de acesso do Internet Explorer usando a política de grupo](active-directory-saas-ie-group-policy.md)

##<a name="run-the-diagnostic-tool"></a>Executar a ferramenta de diagnóstico

Você pode diagnosticar problemas de instalação com a extensão do painel de acesso ao baixar e executar a ferramenta de diagnóstico do painel de acesso:

1. [Clique aqui para baixar a ferramenta de diagnóstico.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Abra o arquivo e pressione o botão **extrair todos** .

    ![Pressione extrair todos](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Pressione o botão **extrair** para continuar.

    ![Pressione extrair](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Para executar a ferramenta, clique com botão direito no arquivo chamado **AccessPanelExtensionDiagnosticTool**, selecione **Abrir com > Microsoft Windows com base em Script Host**.

    ![Abrir com > Microsoft Windows com base em Host de scripts](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Você verá a seguinte janela de diagnóstico, que descreve o que pode estar errado com a sua instalação.

    ![Uma amostra da janela de diagnóstico](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Clique em "**Sim**" para permitir que o programa corrija os problemas que foram encontrados.

7. Para salvar essas alterações, feche todas as janelas do Internet Explorer e abra o Internet Explorer novamente.<br />Se você ainda não consegue acessar seus aplicativos, tente as etapas abaixo.

##<a name="check-that-the-access-panel-extension-is-enabled"></a>Verifique se a extensão do painel de acesso está habilitada

Para verificar se a extensão do painel de acesso está ativada no Internet Explorer:

1. No Internet Explorer, clique no **ícone de engrenagem** no canto superior direito da janela. Selecione **Opções da Internet**.<br />(Em versões mais antigas do Internet Explorer, você pode descobrir isso em **Ferramentas > Opções da Internet**.

    ![Vá para Ferramentas > Opções da Internet](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Na guia **programas** , clique no botão **Gerenciar complementos** .

    ![Clique em Gerenciar complementos](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. Nesta caixa de diálogo, selecione a **Extensão do painel de acesso** e, em seguida, clique no botão **Habilitar** .

    ![Clique em Habilitar](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Para salvar essas alterações, feche todas as janelas do Internet Explorer e abra o Internet Explorer novamente.

##<a name="enable-extensions-for-inprivate-browsing"></a>Ativar extensões de Navegação InPrivate

Se você estiver usando o modo de Navegação InPrivate:

1. No Internet Explorer, clique no **ícone de engrenagem** no canto superior direito da janela. Selecione **Opções da Internet**.<br />(Em versões mais antigas do Internet Explorer, você pode descobrir isso em **Ferramentas > Opções da Internet**.

    ![Uma amostra da janela de diagnóstico](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Vá para a guia **privacidade** , **desmarque** a caixa de seleção rotulada **Desabilitar barras de ferramentas e extensões quando começa a navegação InPrivate**</p>

    ![Desmarque desativar barras de ferramentas e extensões quando começa a navegação InPrivate](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Para salvar essas alterações, feche todas as janelas do Internet Explorer e abra o Internet Explorer novamente.

##<a name="uninstall-the-access-panel-extension"></a>Desinstalar a extensão do painel de acesso

Para desinstalar a extensão do painel de acesso do seu computador:

1. No teclado, pressione a **tecla do Windows** para abrir o menu Iniciar. Quando o menu estiver aberto, você pode digitar algo para realizar uma pesquisa. Digite "Painel de controle" e abra o **Painel de controle** quando aparecer nos resultados da pesquisa.

    ![Pesquisa do painel de controle](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. No canto superior direito do painel de controle, altere a opção de **modo de exibição por** para **ícones grandes**. Localize e clique no botão **programas e recursos** .

    ![Chang o modo de exibição para mostrar ícones grandes](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. Na lista, selecione a **Extensão do painel de acesso**e então clique no botão **desinstalar** .

    ![Clique em Desinstalar](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Você pode tentar instalar a extensão novamente para ver se o problema foi resolvido.

Se você encontrar problemas Desinstalando a extensão, você também pode removê-la usando a ferramenta [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Acesso de aplicativo e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md)
- [Como implantar a extensão do painel de acesso do Internet Explorer usando a política de grupo](active-directory-saas-ie-group-policy.md)
