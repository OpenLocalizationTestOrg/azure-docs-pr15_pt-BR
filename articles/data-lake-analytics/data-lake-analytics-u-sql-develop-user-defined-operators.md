<properties 
   pageTitle="Desenvolver operadores definidos pelo usuário de U-SQL para trabalhos de análise de Lucerne de dados do Azure | Azure" 
   description="Aprenda a desenvolver operadores definidos pelo usuário para ser usado e reutilizadas em trabalhos de análise de Lucerne de dados. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Desenvolver operadores definidos pelo usuário de U-SQL para trabalhos de análise de Lucerne de dados do Azure

Aprenda a desenvolver operadores definidos pelo usuário para ser usado e reutilizada em trabalhos de análise de Lucerne de dados. Você irá desenvolver um operador personalizado para converter nomes de país/região.

##<a name="prerequisites"></a>Pré-requisitos

- Visual Studio 2015, Visual Studio 2013 atualizar 4 ou Visual Studio 2012 com Visual C++ instalado 
- Microsoft Azure SDK para .NET versão 2.5 ou acima.  Instalá-lo usando o Web platform installer.
- Uma conta de dados Lucerne Analytics.  Consulte [Introdução ao Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-get-started-portal.md).
- Percorra o tutorial de [Introdução ao Azure dados Lucerne Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md) .
- Conectar ao Azure, consulte [Introdução ao Azure dados Lucerne Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Carregar os dados de origem, consulte [Introdução ao Azure dados Lucerne Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definir e usar o operador definido pelo usuário em U-SQL

**Criar e enviar um trabalho de U-SQL** 

1. No menu **arquivo** , clique em **novo**e, em seguida, clique em **projeto**.
2. Selecione o tipo de **Projeto de U-SQL** .

    ![novo projeto do Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Clique em **Okey**. Visual studio cria uma solução com um arquivo de Script.usql.
4. No **Solution Explorer**, expanda Script.usql e clique duas vezes em **Script.usql.cs**.
5. Cole o seguinte código no arquivo:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. Abra Script.usql e cole o seguinte script U-SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. No **Solution Explorer**, clique com botão direito **Script.usql**e clique em **Criar Script**.
6. No **Solution Explorer**, clique com botão direito **Script.usql**e clique em **Enviar Script**.
7. Se você ainda não se conectar à sua assinatura do Azure, você será prompt inserir suas credenciais de conta do Microsoft Azure.
7. Clique em **Enviar**. Resultados de envio e link de trabalho estão disponíveis na janela de resultados quando o envio é concluído.
8. Você deve clicar no botão Atualizar para ver o status do trabalho mais recente e atualizar a tela.

**Para ver a saída de trabalho**

1. No **Server Explorer**, expanda **Azure**, expandir **Dados Lucerne Analytics**, expandir sua conta de dados Lucerne Analytics, expanda **Contas de armazenamento**, o armazenamento padrão de atalho e clique em **Explorer**. 
2. Expanda exemplos, expanda saídas e clique duas vezes em **CSV**.


##<a name="see-also"></a>Consulte também

- [Começar a usar a análise de Lucerne dados usando o PowerShell](data-lake-analytics-get-started-powershell.md)
- [Começar a usar a análise de Lucerne dados usando o portal do Azure](data-lake-analytics-get-started-portal.md)
- [Usar ferramentas de Lucerne de dados para o Visual Studio para desenvolver aplicativos U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
