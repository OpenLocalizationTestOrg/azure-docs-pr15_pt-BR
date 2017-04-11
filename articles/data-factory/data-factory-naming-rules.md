<properties 
    pageTitle="Dados Factory - as regras de nomenclatura | Microsoft Azure" 
    description="Descreve as regras de nomenclatura para entidades de fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Dados Azure Factory - as regras de nomenclatura 
A tabela a seguir fornece as regras de nomenclatura para artefatos de fábrica de dados.



Nome | Exclusividade de nome | Verificações de validação
:--- | :-------------- | :----------------
Fábrica de dados | Exclusivas entre Microsoft Azure. Nomes diferenciam maiusculas de minúsculas, ou seja, MyDF e mydf referem-se à mesma fábrica dados. |<ul><li>Cada fábrica de dados está vinculada à assinatura do Azure exatamente um.</li><li>Nomes de objeto devem começar com uma letra ou um número e podem conter apenas letras, números e o caractere traço (-).</li><li>Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Traços consecutivos não são permitidos em nomes de contêiner.</li><li>Nome pode ser 3-63 caracteres.</li></ul>
Vinculada em tabelas/serviços/canais | Exclusivo com em uma fábrica de dados. Nomes diferenciam maiusculas de minúsculas. | <ul><li>Número máximo de caracteres no nome da tabela: 260.</li><li>Nomes de objeto devem começar com um número de letra ou um sublinhado (_).</li><li>Caracteres seguintes não são permitidas: ".", "+", "?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul>
Grupo de recursos | Exclusivas entre Microsoft Azure. Nomes diferenciam maiusculas de minúsculas. | <ul><li>Número máximo de caracteres: 1000.</li><li>Nome pode conter letras, dígitos e os seguintes caracteres: "-", "_",","e".".</li></ul>