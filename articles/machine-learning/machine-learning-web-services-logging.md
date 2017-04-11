<properties 
    pageTitle="Registro em log para serviços da web de aprendizado de máquina | Microsoft Azure" 
    description="Saiba como habilitar o log de serviços da web de aprendizado de máquina. Registro em log fornece informações adicionais para ajudar a solucionar as APIs." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Habilitar o log de aprendizado de máquina serviços da web  

Este documento fornece informações sobre o recurso de log de serviços Web clássico. Habilitando o log nos serviços Web fornece informações adicionais, além de apenas um número de erro e uma mensagem, que pode ajudá-lo a solucionar problemas de suas chamadas para as APIs de aprendizado de máquina.  

Para habilitar o log nos serviços da Web no portal de clássico do Azure:   

1.  Entre no Portal [clássico Azure](https://manage.windowsazure.com/)
2.  Na coluna à esquerda de recursos, clique em **APRENDIZADO de máquina**.
3.  Clique em seu espaço de trabalho, em seguida, **WEB SERVICES**.
4.  Na lista de serviços Web, clique no nome do serviço da Web.
5.  Na lista de pontos de extremidade, clique no nome do ponto de extremidade.
6.  Clique em **Configurar**.
7.  Definir o **Nível de rastreamento de diagnóstico** *erro* ou *todos*, clique em **Salvar**.

Para habilitar o registro em log no portal do Azure serviços de Web de aprendizado de máquina.

1. Entrar no portal do [Azure serviços de Web de aprendizado de máquina](https://services.azureml.net) .
2. Clique em serviços Web clássico.
3.  Na lista de serviços Web, clique no nome do serviço da Web.
4.  Na lista de pontos de extremidade, clique no nome do ponto de extremidade.
5.  Clique em **Configurar**.
6.  Defina o **log** de *erro* ou *tudo*e, em seguida, clique em **Salvar**.

## <a name="the-effects-of-enabling-logging"></a>Os efeitos de habilitar registro em log

Quando o registro em log está habilitado, todos os erros do ponto de extremidade selecionado e o diagnóstico são registrados para a conta de armazenamento do Azure vinculada com espaço de trabalho do usuário. Você pode ver esta conta de armazenamento no portal do clássico Azure do modo de exibição do painel de controle (parte inferior da seção de resumo de geral rápida) de seu espaço de trabalho.  

Os logs podem ser visualizados usando qualquer uma das várias ferramentas disponíveis para uma conta de armazenamento do Azure 'explorar'. A maneira mais fácil pode ser simplesmente navegar para a conta de armazenamento no portal de clássico do Azure e clique em **CONTÊINERES**. Em seguida, você verá um contêiner chamado **ml de diagnóstico**. Este contêiner contém todas as informações de diagnóstico para todas as web serviço pontos de extremidade para todos os espaços de trabalho associados a essa conta de armazenamento. 
 
## <a name="log-blob-detail-information"></a>Informações detalhadas do log blob

Cada blob no contêiner de mantém as informações de diagnóstico para exatamente um destes procedimentos:

-   A execução do método execução de lote  
-   A execução do método solicitação-resposta  
-   Inicialização de um contêiner de solicitação-resposta
  
O nome de cada blob tem um prefixo da seguinte forma: 

{Id do espaço de trabalho}-{serviço da Web Id}-{Id de ponto de extremidade} / {registrar tipo}  

Onde o tipo de Log é um dos seguintes valores:  

- em lotes  
- pontuação/solicitações  
- pontuação/inicialização  