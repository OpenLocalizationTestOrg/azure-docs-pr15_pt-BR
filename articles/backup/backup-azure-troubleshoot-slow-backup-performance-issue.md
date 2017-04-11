<properties
   pageTitle="Solucionar problemas de lenta backup de arquivos e pastas no Azure Backup | Microsoft Azure"
   description="Fornece orientação para solução de problemas para ajudá-lo a diagnosticar a causa de problemas de desempenho de Backup do Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Solucionar problemas de lenta backup de arquivos e pastas em Backup do Azure

Este artigo fornece orientação para solução de problemas para ajudá-lo a diagnosticar a causa do desempenho lento de backup para arquivos e pastas quando você estiver usando o Backup do Azure. Quando você usa o agente de Backup do Azure para fazer backup de arquivos, o processo de backup pode levar mais tempo do que o esperado. Esse atraso pode ser causado por um ou mais destes procedimentos:

-   [Existem afunilamentos de desempenho no computador que está sendo feito backup.](#cause1)
-   [Outro processo ou software antivírus está interferindo no processo de Backup do Azure.](#cause2)
-   [O agente de Backup é executado em uma máquina virtual Azure (máquina virtual).](#cause3)  
-   [Você está fazendo backup de um grande número (milhões) de arquivos.](#cause4)

Antes de começar a solução de problemas, recomendamos que você baixe e instale o [agente de Backup do Azure mais recente](http://aka.ms/azurebackup_agent). Podemos fazer atualizações frequentes o agente de Backup para corrigir vários problemas, adicionar recursos e melhorar o desempenho.

Também é altamente recomendável que você examine o [Perguntas frequentes sobre o serviço de Backup do Azure](backup-azure-backup-faq.md) para certificar-se de que você não estiver enfrentando problemas de configuração comuns.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Gargalos de desempenho no computador

Gargalos no computador que está sendo feito backup podem causar atrasos. Por exemplo, a capacidade do computador para ler ou gravar em disco ou largura de banda disponível para enviar dados pela rede, pode causar gargalos.

Windows fornece uma ferramenta interna que chamou o [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) para detectar esses gargalos.

Aqui estão alguns contadores de desempenho e os intervalos que podem ser úteis no diagnóstico gargalos backups ideais.

| Contador  | Status  |
|---|---|
|Disco lógico (disco físico) – % ocioso   | • 100% idle 50% ocioso = Íntegro</br>• 49% idle a 20% ocioso = aviso ou Monitor</br>• 19% idle como 0% ocioso = crítico ou ausência especificação|
|  Disco lógico (disco físico) – AVG %. Disco s leitura ou gravação |  • 0,001 ms para 0,015 ms = Íntegro</br>• 0,015 ms para ms 0.025 = aviso ou Monitor</br>• ms 0.026 ou mais = crítico ou ausência especificação|
|  Disco lógico (disco físico) – comprimento de fila de disco atual (para todas as instâncias) | 80 solicitações por mais de 6 minutos |
| Memória não paginável Bytes|• Menos de 60% do pool consumida = Íntegro<br>• 61 a 80% do pool consumida = aviso ou Monitor</br>• Maior pool de 80% consumida = crítico ou ausência especificação|
| Memória Bytes de Pool paginável |• Menos de 60% do pool consumida = Íntegro</br>• 61 a 80% do pool consumida = aviso ou Monitor</br>• Maior pool de 80% consumida = crítico ou ausência especificação|
| Memória – Megabytes disponíveis| • 50% de memória livre disponível ou mais = íntegro</br>• 25% de memória livre disponível = Monitor</br>• 10% de memória livre disponível = aviso</br>• Menos de 100 MB ou 5% de memória livre disponível = crítico ou ausência especificação|
|Processador –\%tempo de processador (todas as instâncias)|• Menos de 60% consumidas = Íntegro</br>• 61 a 90% consumida = Monitor ou cuidado</br>• 91 a 100% consumida = crítico|


> [AZURE.NOTE] Se você determinar que a infraestrutura é o culpado, recomendamos que você desfragmenta o disco regularmente para obter melhor desempenho.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Outro processo ou software antivírus interferindo com o Backup do Azure

Observamos várias instâncias onde outros processos no sistema do Windows tem prejudicado desempenho do processo de agente de Backup do Azure. Por exemplo, se você usar o agente de Backup do Azure e outro programa para fazer backup de dados, ou se o software antivírus está em execução e tem um bloqueio em arquivos de backup, o múltiplo bloqueia em arquivos podem causar disputa. Nessa situação, o backup pode falhar ou o trabalho pode levar mais tempo do que o esperado.

A melhor recomendação neste cenário é desativar o outro programa de backup para ver se o tempo de backup para o agente de Backup do Azure alterada. Geralmente, lembrando-se de que vários trabalhos de backup não estiverem sendo executado ao mesmo tempo é suficiente para impedir que afetem uns aos outros.

Programas de antivírus, recomendamos que você exclua os seguintes arquivos e locais:

- C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\cbengine.exe como um processo
- C:\Program Files\Microsoft Agent\ de serviços de recuperação de Azure pastas
- Scratch local (se você não estiver usando o local padrão)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de Backup executando em um computador virtual Azure

Se você estiver executando o agente de Backup em uma máquina virtual, o desempenho será mais lento que quando você executá-lo em uma máquina física. Isso é esperado devido a limitações de IOPS.  No entanto, você pode otimizar o desempenho alternando as unidades de dados que estão sendo feitas backup para o armazenamento do Azure Premium. Estamos trabalhando para corrigir esse problema e a correção estará disponível em uma versão futura.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Fazer backup de um grande número (milhões) de arquivos

Mover um grande volume de dados levará mais de movendo um volume menor de dados. Em alguns casos, o tempo de backup está relacionado ao não apenas o tamanho de dados, mas também o número de arquivos ou pastas. Isso é especialmente verdadeiro quando milhões de arquivos pequenos (alguns bytes para alguns quilobytes) estão sendo feitos backup.

Isso ocorre porque enquanto você estiver fazendo backup dos dados e movê-lo para Azure, Azure simultaneamente é catálogos seus arquivos. Em alguns cenários raros, a operação de catálogo pode levar mais tempo do que o esperado.

Os indicadores a seguir podem ajudá-lo a compreender o gargalo e adequadamente trabalhar sobre as próximas etapas:

- **Interface do usuário está mostrando o andamento da transferência de dados**. Os dados ainda está sendo transferidos. A largura de banda de rede ou o tamanho dos dados pode estar provocando atrasos.

- **Interface do usuário não está mostrando o andamento da transferência de dados**. Abrir os logs localizados em C:\Microsoft Agent\Temp de serviços de recuperação de Azure e marque para a entrada de FileProvider::EndData nos logs. Essa entrada significa que a transferência de dados concluída e a operação de catálogo está acontecendo. Não cancele os trabalhos de backup. Em vez disso, aguarde um pouco mais para a operação de catálogo concluir. Se o problema persistir, contate o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).
