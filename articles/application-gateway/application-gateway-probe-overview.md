

<properties
   pageTitle="Integridade monitoramento visão geral do Gateway de aplicativo do Azure | Microsoft Azure"
   description="Saiba mais sobre os recursos de monitoramento do Gateway de aplicativo do Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>Visão geral do monitoramento de integridade de Gateway do aplicativo

Azure Application Gateway por padrão monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer considerados não íntegro do pool de recursos. Gateway do aplicativo continua a monitorar as instâncias não íntegras e adiciona-los novamente para o pool de back-end eficaz, depois que eles se tornam disponíveis e respondem a testes de integridade. Gateway de aplicativo envia que a integridade testes com a mesma porta que é definida nas configurações de HTTP de back-end. Isso garante que o teste está testando a mesma porta que os clientes usarão para se conectar ao back-end.

![exemplo de teste de gateway do aplicativo][1]

Além de usar o monitoramento de teste de integridade padrão, você também pode personalizar o teste de integridade de acordo com os requisitos do aplicativo. Neste artigo, estão cobertos padrão e testes de integridade personalizado.

## <a name="default-health-probe"></a>Teste de integridade de padrão

Um gateway aplicativo configura automaticamente um teste de integridade padrão quando você não definir qualquer configuração de teste personalizado. O comportamento de monitoramento funciona fazendo uma solicitação HTTP para os endereços IP configurados para o pool de back-end. Para testes de padrão se as configurações de http de back-end estão configuradas para HTTPS, o teste usará https também testar a integridade da back-ends.

Por exemplo: configurar o gateway de aplicativo para usar os servidores de back-end A, B e C para receber o tráfego de rede HTTP na porta 80. O monitoramento de integridade de padrão testa os três servidores por uma resposta HTTP eficaz, a cada 30 segundos. Uma resposta HTTP eficaz, tem um [código de status](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de teste padrão falhar para o servidor, o gateway de aplicativo removerá do seu pool de back-end e tráfego de rede interrompe fluindo para este servidor. O teste de padrão ainda continua verificar se há servidor a cada 30 segundos. Quando o servidor responde com êxito a uma solicitação de um teste de integridade padrão, ele é adicionado novamente como eficaz, ao pool de back-end e tráfego começa fluindo no servidor novamente.

### <a name="default-health-probe-settings"></a>Configurações de teste de integridade do padrão

|Propriedade de teste | Valor | Descrição|
|---|---|---|
| URL de teste| http://127.0.0.1:\<porta\>/ | Caminho de URL |
| Intervalo | 30 | Intervalo de sondagem em segundos |
| Tempo limite  | 30 | Tempo limite de teste em segundos |
| Limite não íntegra | 3 | Teste a contagem de repetição. O servidor back-end está marcado para baixo após a contagem de falha de teste consecutivas atinge o limite não íntegro. |

> [AZURE.NOTE] A porta será sempre a mesma porta que as configurações de HTTP de back-end.

O teste de padrão examina somente http://127.0.0.1:\<porta\> para determinar o status de integridade. Se você precisa configurar o teste de integridade para ir para uma URL personalizada ou modificar quaisquer outras configurações, você deve usar testes personalizados conforme descrito nas etapas a seguir.

## <a name="custom-health-probe"></a>Teste de integridade personalizado

Testes personalizados permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar testes personalizados, você pode configurar o intervalo de sondagem, a URL e caminho para testar e quantas respostas com falha para aceitar antes de marcar a instância de pool de back-end como não íntegra.

### <a name="custom-health-probe-settings"></a>Configurações de teste de integridade personalizado

A tabela a seguir fornece definições para as propriedades de um teste de integridade personalizado.

|Propriedade de teste| Descrição|
|---|---|
| Nome | Nome do teste. Esse nome é usado para se referir ao teste nas configurações de HTTP de back-end. |
| Protocolo | Protocolo usado para enviar o teste. O teste usará o protocolo definido nas configurações de HTTP de back-end |
| Host |  Nome de host para enviar o teste. Aplicável somente quando vários locais está configurado no aplicativo Gateway, caso contrário use '127.0.0.1'. Isso é diferente do nome do host de máquina virtual. |
| Caminho | Caminho relativo do teste. O caminho válido começa da '/'. |
| Intervalo | Teste o intervalo em segundos. Este é o intervalo de tempo entre dois testes consecutivas.|
| Tempo limite | Teste o tempo limite em segundos. O teste é marcado como falha se uma resposta válida não for recebida dentro desse período de tempo limite. |
| Limite não íntegra | Teste a contagem de repetição. O servidor back-end está marcado para baixo após a contagem de falha de teste consecutivas atinge o limite não íntegro. |

> [AZURE.IMPORTANT] Se o Gateway de aplicativo é configurado para um único local, por padrão o Host nome deve ser especificado como '127.0.0.1', a menos que o contrário configurada no teste personalizado.
Para referência um teste personalizado é enviado à \<protocolo\>://\<host\>:\<porta\>\<caminho\>. A porta usada será a mesma porta, conforme definido nas configurações de HTTP de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer o monitoramento de integridade do Gateway de aplicativo, você pode configurar um [teste de integridade personalizados](application-gateway-create-probe-portal.md) no portal do Azure ou um [teste de integridade personalizados](application-gateway-create-probe-ps.md) usando o PowerShell e o modelo de implantação do Gerenciador de recursos do Azure.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png