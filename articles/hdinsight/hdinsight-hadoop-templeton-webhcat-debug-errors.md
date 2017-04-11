<properties
 pageTitle="Entender e resolver erros de WebHCat em HDInsight"
 description="Saiba como a sobre erros comuns retornados por WebHCat HDInsight e como resolvê-los."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Compreender e resolver erros recebidos do WebHCat (Templeton), em HDInsight

Ao usar WebHCat (conhecida anteriormente como Templeton,) para trabalhar com HDInsight, você pode receber erros. Este documento fornece orientação sobre erros comuns – por que eles ocorrem e o que você pode fazer para resolvê-los.

##<a name="what-is-webhcat"></a>O que é WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma API REST [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma tabela e armazenamento camada de gerenciamento do Hadoop. WebHCat é ativada por padrão em HDInsight clusters e é usado por várias ferramentas para enviar trabalhos, obtenha o status do trabalho, etc. sem efetuar login no cluster.

##<a name="modifying-configuration"></a>Modificar configuração

> [AZURE.IMPORTANT] Vários dos erros listados neste documento ocorrerem porque um máximo configurado foi excedido. Quando a etapa de resolução menciona que você pode alterar um valor, você deve usar um destes procedimentos para realizar a alteração:

* Para clusters do **Windows** : usar uma ação de script para configurar o valor durante a criação de cluster. Para obter mais informações, consulte [ações de script de desenvolver](hdinsight-hadoop-script-actions.md).

* Para clusters **Linux** : Use Ambari (web ou API REST) para modificar o valor. Para obter mais informações, consulte [Gerenciar HDInsight usando Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configuração padrão

Estes são os valores de configuração padrão que podem afetar o desempenho de WebHCat ou causar erros se esses valores são excedidos:

| Configuração | O que ela faz | Valor padrão |
| ------- | ------------ | ------------- |
| [aplicativos yarn.Scheduler.Capacity.Maximum][maximum-applications] | O número máximo de trabalhos que podem estar ativas simultaneamente (em execução ou pendente) | 10.000 |
| [templeton.EXEC.max-BD][max-procs] | O número máximo de solicitações que podem ser atendidos simultaneamente | 20 |
| [MapReduce.jobhistory.max-idade-ms][max-age-ms] | O número de dias que o histórico de trabalho será mantido | 7 dias |

##<a name="too-many-requests"></a>Muitas solicitações

**Código de HTTP Status**: 429

| Causa | Resolução |
| ----- | ---------- |
| Você excedeu as máximos solicitações simultâneas servidas por WebHCat por minuto (padrão 20) | Reduza a carga de trabalho para garantir que você não enviar mais do que o número máximo de solicitações simultâneas ou aumentar o limite de solicitação simultânea modificando `templeton.exec.max-procs`. Consulte [configuração de modificando](#modifying-configuration) para obter mais informações |

##<a name="server-unavailable"></a>Servidor indisponível

**Código de HTTP Status**: 503

| Causa | Resolução |
| ---------------- | ------------------- |
| Normalmente, isso ocorre durante failover entre a HeadNode primário e secundário para o cluster | Aguarde dois minutos, em seguida, repita a operação |

##<a name="bad-request-content-could-not-find-job"></a>Solicitação inválida conteúdo: não foi possível localizar o trabalho

**Código de HTTP Status**: 400

| Causa | Resolução |
| ---------------- | ------------------- |
| Detalhes do trabalho tem sido limpos pelo histórico de trabalho limpeza | O período de retenção padrão para histórico de trabalho é 7 dias. Isso pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Consulte [configuração de modificando](#modifying-configuration) para obter mais informações |
| Trabalho foi encerrado devido a um failover | Repetir o envio do trabalho para até dois minutos |
| Uma id de trabalho inválida foi usada | Verifique se a id de trabalho está correta |

##<a name="bad-gateway"></a>Gateway incorreto

**Código de HTTP Status**: 502

| Causa | Resolução |
| ---------------- | ------------------- |
| Coleta de lixo interno está ocorrendo dentro do processo de WebHCat | Aguarde a coleta de lixo ser concluída ou reinicie o serviço de WebHCat |
| Tempo limite aguardando uma resposta do serviço ResourceManager. Isso pode ocorrer quando o número de aplicativos ativos vai o máximo configurado (padrão 10.000) | Aguarde em execução trabalhos para concluir ou aumentar o limite de trabalho simultâneas modificando `yarn.scheduler.capacity.maximum-applications`. Consulte [configuração de modificando](#modifying-configuration) para obter mais informações  |
| Ao tentar recuperar todos os trabalhos por meio da chamada [obter /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) enquanto `Fields` está definido como`*` | Não recuperar *todos os* detalhes de trabalho, em vez disso, use `jobid` para recuperar detalhes para trabalhos maiores somente a determinadas id de trabalho. Não use`Fields` |
| O serviço de WebHCat estiver inativo durante HeadNode failover | Aguarde dois minutos e repita a operação |
| Há mais de 500 trabalhos pendentes enviados por meio de WebHCat | Aguarde até que trabalhos pendentes atualmente concluiu antes de enviar mais trabalhos |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
