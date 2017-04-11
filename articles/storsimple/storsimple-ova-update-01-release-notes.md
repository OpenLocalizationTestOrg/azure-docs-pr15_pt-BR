<properties 
   pageTitle="Notas de versão do StorSimple Virtual matriz atualizações | Microsoft Azure"
   description="Descreve críticas questões abertas e resoluções para a matriz Virtual StorSimple executando atualização 0,2 e 0,1."
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de versão de atualização de matriz Virtual 0,2 e 0,1 StorSimple

## <a name="overview"></a>Visão geral

As seguintes notas identificam as questões abertas críticos e os problemas resolvidos Array Virtual do Microsoft Azure StorSimple atualizações. (Array Virtual do Microsoft Azure StorSimple também é conhecido como o dispositivo virtual do local de StorSimple ou o dispositivo virtual StorSimple.) 

As notas são atualizadas continuamente e como exigindo a solução de problemas críticos são descobertos, eles são adicionados. Antes de implantar seu dispositivo virtual StorSimple, leia com atenção as informações contidas nas notas de versão.

Atualização 0,2 corresponde ao a versão de software **10.0.10280.0**; Atualização de 0,1 é versão **10.0.10279.0**. As seções a seguir listam as alterações para cada atualização. 

> [AZURE.NOTE] As atualizações são interrupções e reinicie o seu dispositivo. Se e/s estão em andamento, o dispositivo provocará tempo de inatividade.

## <a name="issues-fixed-in-the-update-02"></a>Problemas corrigidos na atualização 0,2
Atualização 0,2 inclui todas as alterações de atualização 0,1 além a correção descrita na tabela a seguir:

Recurso                              | Problema                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Atualizações                                 | Na última versão, atualizações não foram detectadas automaticamente no portal do Azure clássico, para que você tinha que usar a interface do usuário do local da Web para instalar atualizações. Esse problema é corrigido nesta versão. Depois de instalar a atualização 0,2, você pode instalar atualizações futuras usando o portal de clássico Azure.                       

## <a name="whats-new-in-the-update-01"></a>Novidades na atualização de 0,1

Atualização 0,1 contém os seguintes aperfeiçoamentos e correções de bugs. 

- **Resiliência aprimorado para interrupções de nuvem**: esta versão tem várias correções de bugs em torno de recuperação de dados, fazer backup, restaurar e hierarquia em caso de uma interrupção de conectividade de nuvem. 

- **Melhor desempenho de restauração**: nesta versão possui correções de bugs que têm reduzir significativamente o tempo de conclusão os trabalhos de restauração.

- **Otimização de recuperação de espaço de automatizada**: quando dados são excluídos em volumes de provisionamento thin, os blocos de armazenamento não utilizada precisam ser recuperadas. Esta versão aumentou o processo de recuperação de espaço da nuvem resulta no espaço não utilizado se tornar disponível com mais rapidez em comparação com as versões anteriores.

- **Novas imagens de disco virtual**: novo VHD, VHDX e VMDK agora estão disponíveis através do portal de clássico Azure. Você pode baixar essas imagens para provisionar novos dispositivos de atualização 0,1.

- **Melhorar a precisão do status de trabalhos no portal**: na versão anterior do software, relatórios no portal de status de trabalho não foi granular. Este problema é resolvido nesta versão.

- **Experiência de junção de domínio**: correções de bugs relacionados a renomeação do dispositivo e participar de domínio.


## <a name="issues-fixed-in-the-update-01"></a>Problemas corrigidos na atualização de 0,1

A tabela a seguir fornece um resumo dos problemas corrigidos nesta versão.

| Não.  | Recurso                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | Em algumas versões de VMware, o disco de sistema operacional foi visto como esparso causando alertas e interromper operações normais. Isso foi corrigido nesta versão.                                                                                                                                                                                    |
| 2    | servidor iSCSI                         | Na última versão, o usuário foi necessárias para especificar um gateway para cada interface de rede habilitado do dispositivo StorSimple virtual. Esse comportamento é alterado nesta versão para que o usuário deve configurar pelo menos um gateway para todas as interfaces de rede habilitado.                                                                              |
| 3    | Pacote de suporte                      | Na versão anterior do software, conjunto de pacote de suporte falha quando os tamanhos de pacote foram mais de 1 GB. Esse problema é corrigido nesta versão.                                                                                                                                                                               |
| 4    | Acesso à nuvem                         |  Na última versão, se a matriz Virtual StorSimple não tinha conectividade de rede e foi reiniciada, a interface do usuário local teria problemas de conectividade. Esse problema é corrigido nesta versão.                                                                                                                            |
| 5    | Gráficos de monitoramento                    | Na versão anterior, seguindo um failover de dispositivo, os gráficos de utilização de capacidade de nuvem exibidos valores incorretos no portal de clássico do Azure. Isso é fixo na versão atual.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Problemas conhecidos na atualização de 0,1

A tabela a seguir fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas que seja observado lançamento relação às versões anteriores. **A versão de problemas indicada nesta versão são marcados com um asterisco. Quase todos os problemas desta lista tiveram transportados da versão GA Array Virtual StorSimple.**


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
| **14.** | Arquivo servidor *  | Se um arquivo em uma pasta tiver um fluxo de dados alternativo (anúncios) associada a ele, os anúncios não é feito o backup ou restaurados por meio de recuperação, clonagem e recuperação de nível de Item.| |


## <a name="next-step"></a>Próxima etapa

[Instalar atualizações](storsimple-ova-install-update-01.md) no Array Virtual StorSimple.
