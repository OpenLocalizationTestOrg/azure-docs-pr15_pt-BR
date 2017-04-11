<properties
   pageTitle="Como criar um tíquete para SQL Data Warehouse | Microsoft Azure"
   description="Como criar um tíquete no armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Como criar um tíquete para SQL Data Warehouse
 
Se você está tendo problemas com o SQL Data Warehouse, crie um suporte tíquete para que nossa equipe de engenharia pode ajudá-lo.

## <a name="create-a-support-ticket"></a>Crie um tíquete de suporte

1. Abra o [portal do Azure][].

2. Na tela inicial, clique no bloco **Ajuda + suporte** .

    ![Ajuda + suporte](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. No menu Ajuda + blade de suporte, clique em **Criar solicitação de suporte**.

    ![Nova solicitação de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Selecione o **tipo de solicitação**.

    ![Tipo de solicitação](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Por padrão, cada SQL server (por exemplo, myserver.database.windows.net) tem uma **Cota de DTU** de 45.000. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota criando um tíquete e selecionando *cota* como o tipo de solicitação. Para calcular sua DTU necessidades, multiplique o 7.5 pelo total [que DWU][] necessário. Por exemplo, você gostaria de hospedar dois DW6000s em um SQL server, você deve solicitar uma cota DTU de 90.000.  Você pode exibir seu consumo de DTU atual da lâmina do servidor SQL no portal. Bancos de dados pausados e cancelamento pausados contam a cota DTU. 

5. Selecione a **assinatura** que hospeda o banco de dados com o problema que você está relatando.

    ![Assinatura](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Selecione **o SQL Data Warehouse** como o recurso.

    ![Recurso](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Selecione o [plano de suporte do Azure][].

    - Suporte de **cobrança, gerenciamento de cota e de assinatura** está disponível em todos os níveis de suporte.
    - Suporte de **correção de quebra** é fornecido por meio de [desenvolvedor][], [Standard][], [Professional direta][] ou suporte [Premier][] . Quebra-corrigir problemas são problemas enfrentados pelos clientes durante o uso do Azure onde não há uma expectativas razoável Microsoft causou o problema.
    - **Consultoria de desenvolvedor** e **Serviços de consultoria** estão disponíveis nos níveis de suporte [Premier][] e [Professional diretas][] . 
    
    Se você tiver um Premier plano de suporte, você também poderá reportar SQL Data Warehouse problemas no [portal online da Microsoft Premier][]relacionados.  Consulte o [plano de suporte do Azure][plano de suporte do Azure] para saber mais sobre os diversos planos de suporte, incluindo escopo, tempos de resposta, preços, etc.  Para perguntas frequentes sobre o Azure suporte, consulte [Perguntas frequentes de suporte do Azure][].  

    ![Plano de suporte](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Selecione o **tipo de problema** e **categoria**.

    ![Categoria de tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Descreva o problema e escolha o nível de impacto de negócios.

    ![Descrição do problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Suas **informações de contato** para este tíquete serão preenchidos previamente. Atualize esta opção se for necessário.

    ![Informações de contato](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Clique em **criar** para enviar a solicitação de suporte.


## <a name="monitor-a-support-ticket"></a>Monitorar um tíquete

Depois que você enviou a solicitação de suporte, a equipe de suporte Azure entrará em contato com você. Para verificar o status da solicitação e detalhes, clique em **Gerenciar solicitações de suporte** no painel.

![Verificar status](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Outros recursos

Além disso, você pode se conectar com a comunidade do SQL Data Warehouse [Estouro de pilha][] ou no [Fórum do MSDN de depósito de dados do Azure SQL][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portal do Azure]: https://portal.azure.com/
[Plano de suporte do Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Desenvolvedor]: https://azure.microsoft.com/support/plans/developer/  
[Padrão]: https://azure.microsoft.com/support/plans/standard/  
[Direct profissional]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Perguntas frequentes de suporte Azure]: https://azure.microsoft.com/support/faq/
[Portal on-line da Microsoft Premier]: https://premier.microsoft.com/
[Estouro de pilha]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Fórum de MSDN de depósito de dados do SQL Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

