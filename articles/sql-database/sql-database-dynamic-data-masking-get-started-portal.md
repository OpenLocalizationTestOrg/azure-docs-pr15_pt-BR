<properties
   pageTitle="Introdução ao SQL banco de dados dinâmicos dados Masking (Azure Portal clássico)"
   description="Como começar com SQL banco de dados dinâmicos dados Masking no Portal de clássico do Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# <a name="get-started-with-sql-database-dynamic-data-masking-azure-classic-portal"></a>Introdução ao SQL banco de dados dinâmicos dados Masking (Azure Portal clássico)

> [AZURE.SELECTOR]
- [Dados dinâmicos máscara - Portal do Azure](sql-database-dynamic-data-masking-get-started.md)

## <a name="overview"></a>Visão geral

SQL banco de dados dinâmicos dados Masking limita a exposição de dados confidenciais por masking-lo aos usuários sem privilégios. Máscara de dados dinâmicos há suporte para a versão de V12 do Azure SQL Database.

Máscara de dados dinâmicos ajuda a impedir o acesso não autorizado aos dados confidenciais permitindo que os clientes designar quanto dos dados confidenciais para revelar com impacto mínimo na camada de aplicativo. É um recurso de segurança baseada em política que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos de banco de dados designado, enquanto os dados no banco de dados não são alterados.

Por exemplo, um representante do serviço em um call center pode identificar os chamadores por vários dígitos do seu número de cartão de crédito ou o número da previdência social, mas esses itens de dados não devem ser totalmente expostas ao representante do serviço. Uma regra de máscara pode ser definida que máscaras tudo, mas os últimos quatro dígitos de qualquer número de seguridade social ou o número de cartão de crédito no resultado de conjunto de qualquer consulta. Como outro exemplo, uma máscara de dados apropriado pode ser definida para proteger os dados de informações de identificação pessoal (IIP), para que um desenvolvedor pode consultar ambientes de produção para fins de solução de problemas sem violar os regulamentos.

## <a name="sql-database-dynamic-data-masking-basics"></a>Noções básicas do SQL banco de dados dinâmicos dados Masking

Configurar dados dinâmicos masking política no Portal de clássico do Azure sob a guia Segurança e de auditoria do banco de dados.


> [AZURE.NOTE] Para configurar dados dinâmicos masking no Portal do Azure, consulte [Introdução ao SQL banco de dados dinâmicos dados Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md).


### <a name="dynamic-data-masking-permissions"></a>Permissões de máscara de dados dinâmicos

Máscara de dados dinâmicos pode ser configurada pelo administrador do banco de dados do Azure, administrador do servidor ou funções de diretor de segurança.

### <a name="dynamic-data-masking-policy"></a>Política de máscara de dados dinâmicos

* **Usuários SQL excluído da máscara** - um conjunto de usuários SQL ou identidades AAD que obterá dados sem máscara nos resultados da consulta SQL. Observe que os usuários com privilégios de administrador sempre serão excluídos de máscara e verão os dados originais sem qualquer máscara.

* **Masking regras** - um conjunto de regras que definem os campos indicados a ser mascarado e a função de máscara que será usada. Os campos indicados podem ser definidos usando um nome de esquema de banco de dados, o nome de tabela e o nome da coluna.

* **Masking funções** - um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Masking de função | Masking lógica |
|----------|---------------|
| **Padrão**  |**Máscara inteira acordo com os tipos de dados dos campos indicados**<br/><br/>• Use XXXX ou menos x se o tamanho do campo é menor que 4 caracteres para tipos de dados string (nchar, ntext, nvarchar).<br/>• Use um valor zero para os tipos de dados numéricos (bigint, bit, decimal, int, dinheiro, numérico, smallint, smallmoney, tinyint, flutuação, real).<br/>• Use 01-01-1900 para tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, tempo).<br/>• Para variante SQL, o valor padrão do tipo atual é usado.<br/>• Para o documento XML <masked/> é usado.<br/>• Use um valor vazio para os tipos de dados especiais (tabela de carimbo de hora, hierarchyid, GUID, binário, imagem, tipos de espacial varbinary).
| **Cartão de crédito** |**Masking método que expõe os últimos quatro dígitos dos campos indicados** e adiciona uma cadeia de caracteres constante como um prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Número da previdência social** |**Masking método que expõe os últimos quatro dígitos dos campos indicados** e adiciona uma cadeia de caracteres constante como um prefixo na forma de um número de seguridade social American.<br/><br/>XXX-XX-1234 |
| **Email** | **Masking método que expõe a primeira letra e substitui o domínio com XXX.com** usando um prefixo constante de cadeia de caracteres na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** | **Masking método que gera um número aleatório** de acordo com os limites selecionados e tipos de dados reais. Se os limites designados forem iguais, a função de máscara será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texto personalizado** | **Masking método que expõe o primeiro e último caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizada no meio. Se a cadeia de caracteres original é menor que o expostos prefixo e sufixo, apenas a cadeia de caracteres de preenchimento será usada.<br/>prefixo [preenchimento] sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-classic-portal"></a>Configurar a máscara de dados dinâmicos do banco de dados usando o Portal de clássico do Azure

1. Abra o Portal do clássico Azure em [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Clique em banco de dados que você deseja máscara e, em seguida, clique na guia **auditoria e segurança** .

3. Em **dados dinâmicos masking**, clique em **ativado** para permitir que os dados dinâmicos masking recurso.  

4. Digite os usuários SQL ou identidades AAD que devem ser excluídas da máscara e tem acesso aos dados confidenciais sem máscara. Isso deve ser uma lista separada por ponto e vírgula dos usuários. Observe que os usuários com privilégios de administrador sempre tenham acesso aos dados sem máscara originais.

    >[AZURE.TIP] Para torná-la para a camada de aplicativo possa exibir dados confidenciais para usuários do aplicativo privilegiado, adicione o usuário SQL ou identidade AAD que o aplicativo usa para consultar o banco de dados. É altamente recomendável que esta lista contêm um número mínimo de usuários privilegiados para minimizar exposição dos dados confidenciais.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. Na parte inferior da página na barra de menus, clique em **Adicionar MÁSCARA** para abrir a máscara janela de configuração da regra.

6. Selecione o **esquema**, a **tabela** e a **coluna** nas listas de menu suspenso para definir os campos designados que irá ser mascarados.

7. Escolha uma **Função MASKING** na lista de dados confidenciais masking categorias.

    ![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Clique em **Okey** dos dados masking janela de regra para atualizar o conjunto de masking regras dos dados dinâmicos masking política.

9. Clique em **Salvar** para salvar a política de máscara novo ou atualizado.


## <a name="set-up-dynamic-data-masking-for-your-database-using-transact-sql-statements"></a>Configurar dados dinâmicos masking para seu banco de dados usando instruções Transact-SQL

Consulte [dados dinâmicos Masking](https://msdn.microsoft.com/library/mt130841.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar dados dinâmicos masking para seu banco de dados usando cmdlets do Powershell

Consulte [Cmdlets de banco de dados do SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar a máscara de dados dinâmicos para seu banco de dados usando a API REST

Consulte [operações para bancos de dados do SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
