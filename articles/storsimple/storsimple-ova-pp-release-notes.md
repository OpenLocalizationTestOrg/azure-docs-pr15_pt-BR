<properties 
   pageTitle="Notas de versão do Array Virtual StorSimple | Microsoft Azure"
   description="Descreve problemas críticos de abrir e resoluções para a matriz Virtual StorSimple."
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
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>Notas de versão do Array Virtual StorSimple

## <a name="overview"></a>Visão geral

As seguintes notas identificam as questões de abrir críticas a versão de disponibilidade geral (GA) de 2016 de março do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo virtual do local de StorSimple ou o dispositivo virtual StorSimple). Esta versão corresponde à versão do software 10.0.10271.0.

As notas são atualizadas continuamente e como exigindo a solução de problemas críticos são descobertos, eles são adicionados. Antes de implantar seu dispositivo virtual StorSimple, leia com atenção as informações contidas nas notas de versão. 

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.


| Não. | Recurso | Problema | Solução alternativa/comentários |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Atualizações | Os dispositivos virtuais criados na versão de visualização não podem ser atualizados para uma versão suportada da disponibilidade geral. | Esses dispositivos virtuais devem ser falha sobre a versão de disponibilidade geral usando um fluxo de trabalho de recuperação (DR) de desastres. |
| **2.** | Dados provisionado disco | Depois que você tiver provisionado um disco de dados de um determinado tamanho especificado e criado o dispositivo de virtual StorSimple correspondente, você deve não expandir ou reduzir o disco de dados. Tentar fazer isso resultará em perda de todos os dados nos níveis locais do dispositivo. |   |
| **3.** | Política de grupo | Quando um dispositivo é o domínio, aplicar uma política de grupo pode afetar a operação de dispositivo. | Certifique-se de que seu array virtual está em sua própria unidade organizacional (OU) para o Active Directory e sem objetos de política de grupo (GPO) são aplicados a ele.|
| **4.** | Local da web a interface do usuário | Se os recursos de segurança aprimorada estiverem habilitados no Internet Explorer (IE ESC), algumas páginas de interface do usuário web local como solução de problemas ou manutenção podem não funcionar corretamente. Botões nessas páginas também podem não funcionar. | Desative recursos de segurança aprimorada no Internet Explorer.|
| **5.** | Local da web a interface do usuário | Em uma máquina virtual Hyper-V, as interfaces de rede na web UI são exibidos como 10 Gbps interfaces. | Esse comportamento é um reflexo do Hyper-V. Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** | Hierárquicos volumes ou compartilhamentos | Intervalo de bytes bloqueio para aplicativos que funcionam com o StorSimple volumes hierárquicos não é suportada. Se o bloqueio de intervalo de bytes é ativado, StorSimple hierárquico não funcionará. | Medidas recomendadas incluem: <br></br>Desative o bloqueio em lógica do seu aplicativo de intervalo de bytes.<br></br>Escolha colocar dados deste aplicativo da localmente fixados volumes em vez de volumes hierárquicos.<br></br>*Limitação*: se usando localmente fixos volumes e bloqueio de intervalo de bytes estiver ativado, lembre-se de que o volume localmente fixado pode ser online mesmo antes que a restauração for concluída. Nesses casos, se uma restauração estiver em andamento, em seguida, você deve esperar a restauração seja concluída. |
| **7.** | Compartilhamentos hierárquicos | Trabalhar com arquivos grandes pode resultar em camada lenta check-out. | Ao trabalhar com arquivos grandes, recomendamos que o arquivo maior é menor do que 3% do tamanho do compartilhamento. |
| **8.** | Capacidade de compartilhamentos usada | Você poderá ver compartilhar consumo na ausência de quaisquer dados no compartilhamento. Isso ocorre porque a capacidade utilizada para compartilhamentos inclui metadados. |   |
| **9.** | Recuperação de dados | Você somente pode executar a recuperação de dados de um servidor de arquivo para o mesmo domínio do dispositivo de origem. Não há suporte para a recuperação de dados para um dispositivo de destino em outro domínio nesta versão. | Isso será implementado em uma versão posterior. |
| **10.** | PowerShell Azure | Os dispositivos virtuais StorSimple não podem ser gerenciados através do PowerShell do Azure nesta versão. | Todo o gerenciamento dos dispositivos virtuais deve ser feito por meio de portal clássico do Azure e da web local da interface do usuário. |
| **11.** | Alteração de senha | Console de dispositivo de array virtual aceita somente entrada no formato de teclado pt-br. |   |
| **12.** | CHAP | Credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se você modificar as credenciais de CHAP, você precisará colocar os volumes offline e coloque-os online para que a alteração seja efetivada. | Esses serão corrigidos em uma versão posterior. |
| **13.** | servidor iSCSI  | O 'usada armazenamento' exibidos para um volume iSCSI pode ser diferente no serviço de Gerenciador de StorSimple e o host iSCSI. | O host iSCSI tem o modo de exibição do sistema de arquivos.<br></br>O dispositivo vê os blocos alocados quando o volume estava no tamanho máximo.|
