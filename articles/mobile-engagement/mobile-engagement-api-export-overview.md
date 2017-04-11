<properties
    pageTitle="Visão geral de API de exportação do compromisso móvel"
    description="Aprenda as Noções básicas sobre como exportar seus dados brutos gerados por dispositivos do usuário utilizá-la em suas próprias ferramentas"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Visão geral de API de exportação do compromisso móvel

## <a name="introduction"></a>Introdução

Neste documento, você aprenderá as Noções básicas sobre como exportar seus dados brutos gerados por dispositivos do usuário utilizá-la em suas próprias ferramentas.

## <a name="pre-requisites"></a>Pré-requisitos

Exportar dados brutos do contrato Mobile requer:

- Configuração de autenticação de API sejam capazes de usar as APIs (consulte [configuração manual de autenticação](mobile-engagement-api-authentication-manual.md)),
- Use as APIs REST ou [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
- Uma conta de armazenamento do Azure.

>[AZURE.NOTE] Também aconselhamos excelente [Microsoft Azure Storage Explorer](http://storageexplorer.com/), pelo menos durante a fase de desenvolvimento pois fornece uma interface de usuário fácil de usar para interagir com o armazenamento do Azure.

## <a name="what-can-be-exported"></a>O que podem ser exportado?

Contrato de Mobile permite aos usuários coletar muitos tipos de dados e, portanto, tem vários tipos de exportação adequado para esses tipos de dados diferentes.
Há 2 tipos essenciais de exportação:

- Instantâneo: usado normalmente para exportar dados que representa um estado e para o qual o contrato de celular não tem um histórico. Isso inclui marcas (aplicativo-info), tokens ou comentários de campanha de envio por exemplo. Como consequência esses tipos de exportação não estão relacionados a uma data.
- histórica: esse tipo de exportação é usado para dados que acumulam ao longo do tempo, como eventos ou atividades, por exemplo.

A tabela abaixo descreve rigorosamente todas as possíveis exportações:

| Tipo de exportação | Tipo de dados | Descrição                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Instantâneo    | Notificações por push      | Gera uma exportação de comentários de envio por Push campanhas em uma base por deviceid/ID de usuário                                                              |
| Instantâneo    | Marca       | Gera uma exportação das marcas (aplicativo-info) associadas a cada dispositivos                                                                       |
| Instantâneo    | Dispositivo    | Gera uma exportação da maioria dos dados sobre dispositivos, como os technicals (modelo, localidade, fuso horário, …) marcas, primeira vez visto,... |
| Instantâneo    | Token     | Gera uma exportação de todos os tokens válidos                                                                                                 |
| Histórica  | Atividade  | Gera uma exportação de todas as atividades para cada dispositivos em um determinado período de tempo                                                           |
| Histórica  | Evento     | Gera uma exportação de todas as atividades para cada dispositivos em um determinado período de tempo                                                           |
| Histórica  | Trabalho       | Gera uma exportação de todos os trabalhos para cada dispositivos em um determinado período de tempo                                                                 |
| Histórica  | Erro     | Gera uma exportação de todos os erros para cada dispositivos em um determinado período de tempo                                                               |

## <a name="how-does-it-work"></a>Como ele funciona?

Exportações forem longas executando tarefas que pode produzir grandes arquivos de dados. Por que motivo, eles não podem ser chamados para retornar imediatamente um arquivo para download.
Para exportar dados do contrato de celular, será necessário criar um **Trabalho de exportação** via API onde você especificar geralmente:

- O tipo de exportação (instantâneo ou histórica)
- O tipo de dados
- O **Contêiner de armazenamento do Azure** (incluindo uma SAS válida com acesso de gravação) onde o resultado da exportação será gravado.

Observe que pode levar alguns minutos para o seu trabalho ser iniciado e, em seguida, ele pode ser executado a partir alguns segundos para aplicativos pequenos várias horas para os aplicativos com muitos usuários ou atividade.

Depois que o trabalho é criado, é possível verificar o seu status para ver se ele ainda está sendo executado ou se ele tiver concluído.

Depois que o trabalho é bem-sucedida, o arquivo de dados resultante está disponível no contêiner de armazenamento fornecido.
