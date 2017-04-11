<properties 
   pageTitle="Notas de versão do StorSimple Virtual matriz atualizações | Microsoft Azure"
   description="Descreve críticas questões abertas e resoluções para a matriz Virtual StorSimple executando atualização 0,3."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notas da versão 0,3 StorSimple Virtual matriz atualização

## <a name="overview"></a>Visão geral

As seguintes notas identificam as questões abertas críticos e os problemas resolvidos Array Virtual do Microsoft Azure StorSimple atualizações.

As notas são atualizadas continuamente e como exigindo a solução de problemas críticos são descobertos, eles são adicionados. Antes de implantar sua matriz Virtual StorSimple, leia com atenção as informações contidas nas notas de versão.

Atualização 0,3 corresponde à versão de software **10.0.10288.0**.

> [AZURE.NOTE] As atualizações são interrupções e reinicie seu dispositivo. Se e/s estão em andamento, o dispositivo gera tempo de inatividade.


## <a name="whats-new-in-the-update-03"></a>Novidades na atualização 0,3

Atualização 0,3 é principalmente uma compilação de correção de bug. Nesta versão, foram resolvidos vários bugs, resultando em falhas de backup na versão anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas corrigidos na atualização 0,3

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não.  | Recurso                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Backups                                |Um problema foi visto na versão anterior onde os backups falhará para concluir para um compartilhamento de arquivo. Se este problema ocorreu, o trabalho de backup falhará e um alerta crítico foi gerado o serviço de Gerenciador de StorSimple para notificar o usuário. Esse problema não afeta os dados na compartilhamentos ou acesso aos dados. A causa identificada e corrigida nesta versão. <br></br> A correção não aplica retroativamente compartilhamentos que já estão vendo esse problema. Os clientes que estão vendo este problema primeiro devem aplicar atualização 0,3 e contate o Microsoft Support para executar um backup completo do sistema para corrigir o problema. Em vez de contatar Microsoft Support, os clientes podem também restaurar um novo compartilhamento de um backup eficaz, para os compartilhamentos afetados.                                                                                                                                                                                 |
| 2    | iSCSI                         | Um problema foi visto na versão anterior onde os volumes desaparecerá ao copiar dados para um volume da matriz Virtual StorSimple. Esse problema foi corrigido nesta versão. <br></br> As correções entrará em vigor somente em recém-criado volumes. As correções não serão aplicadas retroativamente volumes que já estão vendo esse problema. Clientes aconselhável trazer os volumes afetados online através do portal de clássico Azure, executar um backup para esses volumes e, em seguida, restaurar esses volumes para novos volumes.                                                               |


## <a name="known-issues-in-the-update-03"></a>Problemas conhecidos na atualização 0,3

A tabela a seguir fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas que seja observado lançamento relação às versões anteriores. 


| Não. | Recurso | Problema | Solução alternativa/comentários |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Atualizações | Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão suportada da disponibilidade geral. | Esses dispositivos virtuais devem ser falha sobre a versão de disponibilidade geral usando um fluxo de trabalho de recuperação (DR) de desastres. |
| **2.** | Dados provisionado disco | Depois que você tiver provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo de virtual StorSimple correspondente, você deve não expandir ou reduzir o disco de dados. Tentar fazer resulta em perda de todos os dados nos níveis locais do dispositivo. |   |
| **3.** | Política de grupo | Quando um dispositivo é o domínio, aplicar uma política de grupo pode afetar a operação de dispositivo. | Certifique-se de que seu array virtual está em sua própria unidade organizacional (OU) para o Active Directory e sem objetos de política de grupo (GPO) são aplicados a ele.|
| **4.** | Local da web a interface do usuário | Se os recursos de segurança aprimorada estiverem habilitados no Internet Explorer (IE ESC), algumas páginas de interface do usuário web local como solução de problemas ou manutenção podem não funcionar corretamente. Botões nessas páginas também podem não funcionar. | Desative recursos de segurança aprimorada no Internet Explorer.|
| **5.** | Local da web a interface do usuário | Em uma máquina virtual Hyper-V, as interfaces de rede na web UI são exibidos como 10 Gbps interfaces. | Esse comportamento é um reflexo do Hyper-V. Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** | Hierárquicos volumes ou compartilhamentos | Intervalo de bytes bloqueio para aplicativos que funcionam com o StorSimple volumes hierárquicos não é suportada. Se o bloqueio de faixa de bytes estiver habilitado, StorSimple hierárquico não funciona. | Medidas recomendadas incluem: <br></br>Desative o bloqueio em lógica do seu aplicativo de intervalo de bytes.<br></br>Escolha colocar dados deste aplicativo da localmente fixados volumes em vez de volumes hierárquicos.<br></br>*Limitação*: quando usando localmente fixos volumes e bloqueio de intervalo de bytes estiver ativado, o volume localmente fixado pode ser online mesmo antes que a restauração for concluída. Nesses casos, se uma restauração estiver em andamento, em seguida, você deve esperar a restauração seja concluída. |
| **7.** | Compartilhamentos hierárquicos | Trabalhar com arquivos grandes pode resultar em camada lenta check-out. | Ao trabalhar com arquivos grandes, recomendamos que o arquivo maior é menor do que 3% do tamanho do compartilhamento. |
| **8.** | Capacidade de compartilhamentos usada | Você poderá ver compartilhar consumo quando não há nenhum dado no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. |   |
| **9.** | Recuperação de dados | Você somente pode executar a recuperação de dados de um servidor de arquivo para o mesmo domínio do dispositivo de origem. Não há suporte para a recuperação de dados para um dispositivo de destino em outro domínio nesta versão. | Isso é implementado em uma versão posterior. |
| **10.** | PowerShell Azure | Os dispositivos virtuais StorSimple não podem ser gerenciados através do PowerShell do Azure nesta versão. | Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio de portal clássico do Azure e da web local da interface do usuário. |
| **11.** | Alteração de senha | Console de dispositivo de array virtual aceita somente entrada no formato de teclado pt-br. |   |
| **12.** | CHAP | Credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se você modificar as credenciais de CHAP, você precisa colocar os volumes offline e coloque-os online para que a alteração seja efetivada. | Este problema é corrigido em uma versão posterior. |
| **13.** | servidor iSCSI  | O 'usada armazenamento' exibidos para um volume iSCSI pode ser diferente no serviço de Gerenciador de StorSimple e o host iSCSI. | O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo vê os blocos alocados quando o volume estava no tamanho máximo.|
| **14.** | Servidor de arquivos  | Se um arquivo em uma pasta tiver um fluxo de dados alternativo (anúncios) associada a ele, os anúncios não é feito o backup ou restaurados por meio de recuperação, clonagem e recuperação de nível de Item.| |


## <a name="next-step"></a>Próxima etapa

[Instalar atualização 0,3](storsimple-ova-install-update-01.md) no Array Virtual StorSimple.

## <a name="references"></a>Referências

Você está procurando uma anotação de versão mais antiga? Ir para: 

- [Notas de versão de atualização de Array Virtual StorSimple 0,1 e 0.2](storsimple-ova-update-01-release-notes.md)

- [Notas da versão de disponibilidade geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)
 

