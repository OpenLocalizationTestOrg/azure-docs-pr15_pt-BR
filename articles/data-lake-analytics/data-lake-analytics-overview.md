<properties 
   pageTitle="Visão geral de análise de Lucerne de dados do Microsoft Azure | Azure" 
   description="Lucerne a análise de dados é um serviço de computação de dados grande do Azure que permite usar dados para orientar seu negócio usando a obtenção de informações obtida com seus dados na nuvem, independentemente de onde ele é e, independentemente de seu tamanho. Dados Lucerne Analytics permite a isso no mais simples, mais flexível e mais econômica maneira possível. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Visão geral de análise de Lucerne de dados do Microsoft Azure

## <a name="what-is-azure-data-lake-analytics"></a>O que é Azure dados Lucerne Analytics?

A análise de Lucerne do Azure dados é um novo serviço, criado para facilitar a análise de dados grande. Serviço permite que você se concentrar em escrever, executando e gerenciando trabalhos, em vez operacional infraestrutura distribuída. Em vez de implantação, configuração e ajuste de hardware, você escreve consultas para transformar seus dados e extrair informações valiosas. O serviço de análise pode manipular trabalhos de qualquer escala instantaneamente simplesmente definindo a discagem para quanto power necessárias. Você só paga seu trabalho quando ele está executando, tornando mais econômico. O serviço de análise suporta Azure Active Directory, permitindo que você simplesmente gerenciar o acesso e funções, integradas ao seu sistema de identidade de local. Ele também inclui U-SQL, uma linguagem que unifica as vantagens de SQL com potência significativa de código do usuário. Scalable distribuído em tempo de execução do U-SQL permite com eficiência analisar dados da loja e entre servidores SQL Azure, o banco de dados do SQL Azure e o depósito de dados do SQL Azure.


## <a name="key-capabilities"></a>Principais recursos

- **Dimensionamento dinâmico** 

    Lucerne a análise de dados foi projetada desde o início para nuvem dimensionamento e desempenho.  Ele dinamicamente provisiona recursos e permite que você fazer a análise terabytes ou até mesmo exabytes de dados. Quando o trabalho for concluído, ele termina recursos automaticamente e pagar somente para a capacidade de processamento usada. Conforme você aumenta ou diminuir o tamanho dos dados armazenados ou a quantidade de computação usada, não é necessário regravar código. Isso permite que você se concentrar em sua lógica de negócios somente e não em como você processar e armazena grandes conjuntos de dados. 

- **Desenvolva mais rápido, depurar e otimizar inteligente usando ferramentas familiares**

    A análise de dados Lucerne tem profunda integração com o Visual Studio, para que você pode usar ferramentas familiares para executar, depurar e ajustar seu código. Visualizações de seus trabalhos U-SQL permitem que você veja como o seu código é executado em escala, para que você pode identificar facilmente o desempenho afunilamentos e otimizar custos. 

- **U-SQL: simples e familiares, poderoso e extensível**

    A análise de dados Lucerne inclui U-SQL, uma linguagem de consulta que se estende a natureza declarativa, familiar simple do SQL com a potência expressiva do c#. Linguagem SQL U baseia-se no mesmo runtime distribuído que alimenta os sistemas de grande volume dentro da Microsoft. Agora, milhões de desenvolvedores SQL e .NET podem processar e analisar todos os seus dados com as habilidades que já têm.

- **Integra-se com seus investimentos em TI**

    Lucerne a análise de dados pode usar seus investimentos de TI existentes para identidade, gerenciamento, segurança e data warehouse. Isso simplifica gestão de dados e facilita a estender seus aplicativos de dados atual. Lucerne a análise de dados é integrada com o Active Directory para permissões e gerenciamento de usuários e vem com o monitoramento e auditoria interna.

- **Possível e econômica**

    Lucerne a análise de dados é uma solução econômica para executar cargas de trabalho de grande volume. Pagar uma base por trabalho quando dados são processados. Nenhum hardware, licenças ou contratos de suporte de serviço específicas são necessários. O sistema automaticamente dimensionada para cima ou para baixo à medida que o trabalho é iniciado e for concluído, significando que você nunca pagar mais do que o que é necessário. 

- **Funciona com todos os seus dados do Azure**

    A análise de dados Lucerne pode trabalhar com um número de fontes de dados do Azure: armazenamento de Blob do Azure, banco de dados do SQL Azure e claro Lucerne a análise de dados é especialmente otimizado para trabalhar com o Azure dados Lucerne Store - fornecendo cargas de trabalho de grande volume de nível mais alto de desempenho, produtividade e paralelização para você.

## <a name="see-also"></a>Consulte também

- Introdução
    - [Começar a usar a análise de Lucerne dados usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
    - [Começar a usar a análise de Lucerne dados usando o PowerShell do Azure](data-lake-analytics-get-started-powershell.md)
    - [Começar a usar a análise de Lucerne dados usando o SDK do Azure .NET](data-lake-analytics-get-started-net-sdk.md)
    - [Desenvolver scripts U-SQL usando ferramentas de Lucerne de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Começar a usar o idioma de análise de Lucerne de dados U-SQL Azure](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL & desenvolvimento
    - [Começar a usar o idioma de análise de Lucerne de dados U-SQL Azure](data-lake-analytics-u-sql-get-started.md)
    - [Usar funções de janela U-SQL para trabalhos de análise de Lucerne de dados do Azure](data-lake-analytics-use-window-functions.md)
    - [Desenvolver operadores de definidas pelo usuário U-SQL para trabalhos de dados Lucerne Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gerenciamento
    - [Gerenciar Azure dados Lucerne análise usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
    - [Gerenciar Azure dados Lucerne análise usando o PowerShell do Azure](data-lake-analytics-manage-use-powershell.md)
    - [Monitorar e solucionar problemas de trabalhos de análise de Lucerne de dados do Azure usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Acesse os logs de diagnóstico para análise de Lucerne de dados do Azure](data-lake-analytics-diagnostic-logs.md)

- Tutorial de ponta a ponta
    - [Use tutoriais interativos Azure dados Lucerne Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analisar os logs de site usando a análise de Lucerne de dados do Azure](data-lake-analytics-analyze-weblogs.md)

- Informe o que você pensa
    - [Comentar nossos registros acumulados documentação](data-lake-analytics-documentation-backlog.md)
    - [Enviar uma solicitação de recurso](http://aka.ms/adlafeedback)
    - [Obtenha ajuda nos fóruns](http://aka.ms/adlaforums)


