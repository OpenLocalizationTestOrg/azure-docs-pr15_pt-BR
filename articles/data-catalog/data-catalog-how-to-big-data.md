<properties
   pageTitle="Como trabalhar com fontes de dados de 'dados grande' | Microsoft Azure"
   description="Artigo de instruções realce padrões para usar o catálogo de dados do Azure com fontes de dados de 'dados grande', inclusive o armazenamento de Blob do Azure, Lucerne de dados do Azure e Hadoop HDFS."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Como trabalhar com fontes de dados grande no catálogo de dados do Azure

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados corporativos. Em outras palavras, **Catálogo de dados do Azure** é tudo sobre pessoas ajudando descobrir, entender e usar fontes de dados e ajudar as empresas para obter mais valor de suas fontes de dados existentes, incluindo dados grandes.

**Catálogo de dados do Azure** oferece suporte para o registro do armazenamento de Blog do Azure blobs, diretórios bem como Hadoop HDFS arquivos e pastas. A natureza semiestruturada dessas fontes de dados fornece grande flexibilidade, mas também significa que os usuários devem considerar como fontes de dados são organizadas para obter o valor máximo de registrando-os com o **Catálogo de dados do Azure**.

## <a name="directories-as-logical-data-sets"></a>Diretórios como lógicos conjuntos de dados

Um padrão comum para organizar as fontes de dados grande é tratar diretórios como lógicos conjuntos de dados. Diretórios de nível superior são usados para definir um conjunto de dados, enquanto as subpastas definem partições e os arquivos que eles contêm armazenam os dados em si.

Um exemplo desse padrão pode ser:

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

Neste exemplo, vehicle_maintenance_events e location_tracking_events representam lógicos conjuntos de dados. Cada uma dessas pastas contém os arquivos de dados que são organizados por ano e mês em subpastas. Cada uma dessas pastas possam conter centenas ou milhares de arquivos.

Nesse padrão, registrando arquivos individuais com o **Catálogo de dados do Azure** provavelmente não faz sentido. Em vez disso, registre as pastas que representam os conjuntos de dados que seja significativos para os usuários que trabalham com os dados.

## <a name="reference-data-files"></a>Arquivos de dados de referência

Um padrão de complementar é armazenar conjuntos de dados de referência como arquivos individuais. Esses conjuntos de dados pode ser considerados como lado 'pequeno' de dados grandes e geralmente são semelhantes às dimensões em um modelo de dados analíticos. Arquivos de dados de referência contêm os registros que são usados para fornecer contexto para a maior parte dos arquivos de dados armazenados em outro lugar no repositório de dados grande.

Um exemplo desse padrão poderia ser:

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Quando uma cientista analista ou dados está trabalhando com os dados contidos nas estruturas de diretório maiores, os dados nesses arquivos de referência podem ser usados para fornecer informações mais detalhadas para entidades citadas apenas por nome ou a ID do conjunto de dados maior.

Nesse padrão, ele faça sentido para registrar os arquivos de dados de referência individuais com o **Catálogo de dados do Azure**. Cada arquivo representa um conjunto de dados, e cada uma delas pode ser anotada e descoberta individualmente.

## <a name="alternate-patterns"></a>Padrões de alternativos

Os padrões de descrito acima são apenas duas formas possíveis que um armazenamento de dados grande pode ser organizado, mas cada implementação serão diferente. Independentemente de como suas fontes de dados são estruturadas, ao registrar fontes de dados grande com o **Catálogo de dados do Azure**, enfocar registrar os arquivos e pastas que representam os conjuntos de dados que serão de valor para outras pessoas dentro de sua organização. Registrar todos arquivos e pastas pode truncar o catálogo, tornando mais difícil para os usuários que precisam de localizar.

## <a name="summary"></a>Resumo
Registrando fontes de dados com o **Catálogo de dados do Azure** torna mais fácil de descobrir e entender. Registrando e anotando os arquivos de dados grande e diretórios que representam lógicos conjuntos de dados, você pode ajudar os usuários localizar e usar as fontes de dados grande que precisam.
