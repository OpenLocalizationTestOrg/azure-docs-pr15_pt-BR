Embora seja possível colar um URI MongoLab no seu código, é recomendável configurá-lo no ambiente para facilidade de gerenciamento. Dessa forma, se o URI mudar, você pode atualizá-la por meio do Portal do Azure sem ir para o código.


1. No Portal do Azure, selecione **Web Apps**.
1. Clique no nome do aplicativo web na lista de aplicativos Web.  
![WebAppEntry][entry-website]  
Exibe o painel de Web App.

1. Na barra de menus, clique em **Configurar** .  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Role até a seção de cadeias de caracteres de Conexão.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Para **nome**, digite MONGOLAB_URI.
1. No **valor**, cole a cadeia de conexão que obtivemos na seção anterior.
1. Selecione **personalizado** na lista suspensa **tipo** (em vez de **SQLAzure**padrão).
1. Clique em **Salvar** na barra de ferramentas.  
![SaveWebApp][button-website-save]

**Observação:** Azure adiciona o **CUSTOMCONNSTR\_ ** prefixo a essa variável, que é o motivo pelo qual o código acima referências **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
