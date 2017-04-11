<properties 
    pageTitle="Análise de fluxo: Girar credenciais de logon de entradas e saídas | Microsoft Azure" 
    description="Saiba como atualizar as credenciais para a análise de fluxo de entradas e saídas."
    keywords="credenciais de logon"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Girar credenciais de login de entradas e saídas em trabalhos de análise de fluxo

##<a name="abstract"></a>Resumo
A análise de fluxo Azure hoje não permite que substituindo as credenciais em uma entrada/saída enquanto o trabalho está em execução.

Análise de fluxo de Azure dá suporte ao retomar um trabalho da última saída, queremos compartilhar todo o processo para minimizar o tempo de latência entre a interrupção e iniciando do trabalho e girando as credenciais de login.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Parte 1 - Preparar o novo conjunto de credenciais:
Essa parte é aplicável aos seguintes entradas/saídas:

* Armazenamento de blob
* Hubs de evento
* Banco de dados SQL
* Armazenamento de tabela

Para outras entradas/saídas, prossiga com a parte 2.

###<a name="blob-storagetable-storage"></a>Armazenamento de blob do armazenamento/tabela
1.  Vá para a extensão de armazenamento no portal de gerenciamento do Azure:  
![graphic1][graphic1]
2.  Localize o armazenamento usado pelo seu trabalho e vá para ela:  
![graphic2][graphic2]
3.  Clique no comando de gerenciamento de chaves de acesso:  
![graphic3][graphic3]
4.  Entre a chave primária de acesso e a chave de acesso secundária, **Escolha aquele não usado pelo seu trabalho**.
5.  Recriar visita:  
![graphic4][graphic4]
6.  Copie a chave gerada recentemente:  
![graphic5][graphic5]
7.  Continue a parte 2.

###<a name="event-hubs"></a>Hubs de evento
1.  Vá para a extensão de barramento de serviço no portal de gerenciamento do Azure:  
![graphic6][graphic6]
2.  Localize o Namespace de barramento de serviço usado pelo seu trabalho e vá para ela:  
![graphic7][graphic7]
3.  Se seu trabalho usa uma política de acesso compartilhado no Namespace de barramento de serviço, ir para a etapa 6  
4.  Vá para a guia Hubs de evento:  
![graphic8][graphic8]
5.  Localize o Hub de evento usado pelo seu trabalho e vá para ela:  
![graphic9][graphic9]
6.  Vá para a guia Configurar:  
![graphic10][graphic10]
7.  Na lista suspensa Nome da política, localize a política de acesso compartilhado usada pelo seu trabalho:  
![graphic11][graphic11]
8.  Entre a chave primária e a chave secundária, **Escolha aquele não usado pelo seu trabalho**.  
9.  Recriar visita:  
![graphic12][graphic12]
10. Copie a chave gerada recentemente:  
![graphic13][graphic13]
11. Continue a parte 2.  

###<a name="sql-database"></a>Banco de dados SQL

>[AZURE.NOTE] Observação: você precisará se conectar ao serviço de banco de dados SQL. Vamos mostrar como fazer isso usando a experiência de gerenciamento no portal de gerenciamento do Azure, mas você pode optar por usar uma ferramenta de cliente como o SQL Server Management Studio também.

1.  Vá para a extensão de bancos de dados SQL no portal de gerenciamento do Azure:  
![graphic14][graphic14]
2.  Localize o banco de dados do SQL usados pelo seu link de trabalho e **clique no servidor** na mesma linha:  
![graphic15][graphic15]
3.  Clique no comando de gerenciar:  
![graphic16][graphic16]
4.  Tipo de banco de dados mestre:  
![graphic17][graphic17]
5.  Digite seu nome de usuário, senha e clique em fazer logon:  
![graphic18][graphic18]
6.  Clique em nova consulta:  
![graphic19][graphic19]
7.  Tipo na consulta a seguir substituindo < login_name > com o nome de usuário e substituindo <enterStrongPasswordHere> com sua nova senha:  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Clique em executar:  
![graphic20][graphic20]
9.  Volte à etapa 2 e esse tempo clicar o banco de dados:  
![graphic21][graphic21]
10. Clique no comando de gerenciar:  
![graphic22][graphic22]
11. Digite seu nome de usuário, senha e clique em Logon:  
![graphic23][graphic23]
12. Clique em nova consulta:  
![graphic24][graphic24]
13. Digite a seguinte consulta substituindo < Nome_de_usuário > com um nome pelo qual você deseja identificar este login no contexto deste banco de dados (você pode fornecer o mesmo valor que você forneceu para < login_name >, por exemplo) e substituindo < login_name > com seu novo nome de usuário:  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Clique em executar:  
![graphic25][graphic25]
15. Agora você deve fornecer o seu novo usuário com as mesmas funções e privilégios de usuário original tinha.
16. Continue a parte 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Parte 2: Interromper o trabalho de análise de fluxo
1.  Vá para a extensão de análise de fluxo no portal de gerenciamento do Azure:  
![graphic26][graphic26]
2.  Localize seu trabalho e vá para ela:  
![graphic27][graphic27]
3.  Vá para a guia de entradas ou saídas com base em se você estiver girando as credenciais em uma entrada ou em uma saída.  
![graphic28][graphic28]
4.  Clique no comando de parada e confirme que o trabalho parou:  
![graphic29][graphic29] esperar o trabalho parar.
5.  Localize a entrada/saída que você deseja girar credenciais em e vá para ela:  
![graphic30][graphic30]
6.  Vá para a parte 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: As credenciais de trabalho de análise de fluxo de edição

###<a name="blob-storagetable-storage"></a>Armazenamento de blob do armazenamento/tabela
1.  Localize o campo de chave da conta de armazenamento e cole sua chave gerada recentemente nele:  
![graphic31][graphic31]
2.  Clique no comando Salvar e confirme a salvar as alterações:  
![graphic32][graphic32]
3.  Um teste de conexão será iniciado automaticamente quando você salvar as alterações, verifique se isto é passou com êxito.
4.  Vá para a parte 4.

###<a name="event-hubs"></a>Hubs de evento
1.  Localize o campo de chave de política de Hub de evento e cole sua chave gerada recentemente nele:  
![graphic33][graphic33]
2.  Clique no comando Salvar e confirme a salvar as alterações:  
![graphic34][graphic34]
3.  Um teste de conexão será iniciado automaticamente quando você salvar as alterações, certifique-se de que ele tenha passado com êxito.
4.  Vá para a parte 4.

###<a name="power-bi"></a>Power BI
1.  Clique em autorização renovar:  
* ![graphic35][graphic35]
* Você receberá a confirmação a seguir:  
* ![graphic36][graphic36]
2.  Clique no comando Salvar e confirme a salvar as alterações:  
![graphic37][graphic37]
3.  Um teste de conexão será iniciado automaticamente quando você salvar suas alterações, verifique se ele passou com êxito.
4.  Vá para a parte 4.

###<a name="sql-database"></a>Banco de dados SQL
1.  Localizar os campos nome de usuário e senha e colar seu recém-criado conjunto de credenciais neles:  
![graphic38][graphic38]
2.  Clique no comando Salvar e confirme a salvar as alterações:  
![graphic39][graphic39]
3.  Um teste de conexão será iniciado automaticamente quando você salvar as alterações, certifique-se de que ele tenha passado com êxito.  
4.  Vá para a parte 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>Parte 4: Iniciando o trabalho da última vez interrompido
1.  Navegue longe a entrada/saída:  
![graphic40][graphic40]
2.  Clique no comando de iniciar:  
![graphic41][graphic41]
3.  Escolha a última vez interrompido e clique em Okey:  
 ![graphic42][graphic42]
4.  Vá para a parte 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Parte 5: Removendo o antigo conjunto de credenciais
Essa parte é aplicável aos seguintes entradas/saídas:
* Armazenamento de blob
* Hubs de evento
* Banco de dados SQL
* Armazenamento de tabela

###<a name="blob-storagetable-storage"></a>Armazenamento de blob do armazenamento/tabela
Repita a parte 1 para a tecla de acesso que era usado pelo seu trabalho para renovar a chave de acesso agora não utilizados.

###<a name="event-hubs"></a>Hubs de evento
Repita a parte 1 para a chave que era usada pelo seu trabalho para renovar a chave agora não utilizada.

###<a name="sql-database"></a>Banco de dados SQL
1.  Volte à janela consulta da parte 1 etapa 7 e digite a seguinte consulta, substituindo < previous_login_name > com o nome de usuário que era usado pelo seu trabalho:  
`DROP LOGIN <previous_login_name>`  
2.  Clique em executar:  
    ![graphic43][graphic43]  

Você deve receber a confirmação a seguir: 

    Command(s) completed successfully.

## <a name="get-help"></a>Obter ajuda
Para obter assistência, tente nosso [Fórum de análise de fluxo do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure fluxo Analytics](stream-analytics-introduction.md)
- [Começar a usar a análise de fluxo do Azure](stream-analytics-get-started.md)
- [Trabalhos de análise de fluxo de Azure de escala](stream-analytics-scale-jobs.md)
- [Referência de linguagem de consulta do fluxo Azure Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência de API REST do gerenciamento de análise de fluxo Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
