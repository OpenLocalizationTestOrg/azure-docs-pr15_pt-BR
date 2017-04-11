<properties 
   pageTitle="Limites do sistema de StorSimple | Microsoft Azure"
   description="Descreve os limites de sistema e tamanhos recomendados para componentes de série 8000 StorSimple e conexões."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="storsimple-system-limits"></a>Limites do sistema de StorSimple

## <a name="overview"></a>Visão geral

StorSimple fornece armazenamento escalável e flexível para seu data center. No entanto, há alguns limites que você deve ter em mente enquanto você planeja, implantar e opera sua solução de StorSimple. A tabela a seguir descreve esses limites e fornece algumas recomendações para que você possa aproveitar ao máximo sua solução de StorSimple.

| Identificador de limite | Limite | Comentários |
|----------------- | ------|--------- |
| Número máximo de credenciais de conta de armazenamento | 64 | |
| Número máximo de contêineres de volume | 64 | |
| Número máximo de volumes | 255 | |
| Número máximo de volumes localmente fixados | 32 | |
| Número máximo de cronogramas por modelo de largura de banda | 168 | Uma agenda para cada hora, todos os dias da semana (24 * 7). |
| Tamanho máximo de um volume hierárquico em dispositivos físicos | 64 TB para 8100 e 8600 | 8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume hierárquico em dispositivos virtuais no Azure | 30 TB para 8010 <br></br> 64 TB para 8020 | 8010 e 8020 são dispositivos virtuais no Azure que usam armazenamento padrão e Premium, respectivamente. |
| Tamanho máximo de um volume localmente fixado em dispositivos físicos | 8,5 TB para 8100 <br></br> 22,5 TB para 8600 | 8100 e 8600 são dispositivos físicos. |
| Número máximo de conexões iSCSI | 512 | |
| Número máximo de conexões de iSCSI de iniciadores | 512 | |
| Número máximo de registros de controle de acesso por dispositivo | 64 | |
| Número máximo de volumes por política de backup | 24 | |
| Número máximo de backups mantidos por programação (em uma política de backup) | 64 | |
| Número máximo de cronogramas por política de backup | 10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser retidos por volume | 256 | Este número inclui instantâneos locais e na nuvem instantâneos. |
| Número máximo de instantâneos que podem estar presentes em qualquer dispositivo | 10.000 | |
| Número máximo de volumes que podem ser processados em paralelo para backup, restaurar ou clonar | 16 |<ul><li>Se houver mais de 16 volumes, eles são processados sequencialmente conforme disponibilizados slots de processamento.</li><li>Novos backups de um clonado ou um volume hierárquico restaurado não pode ocorrer até que a operação seja concluída. No entanto, para um volume local, backups são permitidos após o volume está online.</li></ul>|
| Restaurar e clonar recuperar tempo para volumes hierárquicos | < 2 minutos | <ul><li>O volume é disponibilizado em 2 minutos de operação de restauração ou clonar, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente pode ser mais lento que o normal, pois a maioria dos dados e metadados ainda está localizado na nuvem. Desempenho pode aumentar como fluxos de dados da nuvem no dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho de volume alocado. Metadados é automaticamente colocado no dispositivo no plano de fundo em uma taxa de 5 minutos por TB de dados de volume alocado. Essa taxa pode ser afetada por largura de banda de Internet para a nuvem.</li><li>A operação de restauração ou clonar é concluída quando todos os metadados estão no dispositivo.</li><li>Operações de backup não podem ser executadas até a restauração ou operação de clonagem é totalmente concluída.|
| Restaurar recuperar tempo para volumes localmente fixados | < 2 minutos | <ul><li>O volume é disponibilizado em 2 minutos da operação de restauração, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente pode ser mais lento que o normal, pois a maioria dos dados e metadados ainda está localizado na nuvem. Desempenho pode aumentar como fluxos de dados da nuvem no dispositivo StorSimple.</li><li>O tempo total para baixar metadados depende do tamanho de volume alocado. Metadados será automaticamente colocado no dispositivo no plano de fundo em uma taxa de 5 minutos por TB de dados de volume alocado. Essa taxa pode ser afetada por largura de banda de Internet para a nuvem.</li><li>Ao contrário dos volumes hierárquicos, para volumes localmente fixados, os dados do volume também são baixados localmente no dispositivo. A operação de restauração é concluída quando todos os dados de volume foi colocado no dispositivo.</li><li>As operações de restauração podem ser longas. O tempo total para completar a restauração dependerá do tamanho do volume local provisionado, sua largura de banda de Internet e os dados existentes no dispositivo. Operações de backup no volume localmente fixada são permitidas enquanto a operação de restauração estiver em andamento.|
|Taxa de processamento de instantâneos de nuvem| 15 minutos/TB | <ul><li>Tempo mínimo para tornar a nuvem instantâneo pronto para carregar, por TB de dados de volume alocado em backup. </li><li> Tempo de instantâneo de nuvem total é calculado adicionando desta vez para o tempo de carregamento de instantâneo, que é afetado pela largura de banda de Internet para a nuvem.
| Transferência de leitura/gravação do cliente máximo (quando served da camada de SSD) * | 920/720 MB/s com uma única interface de rede GbE 10 | Até 2x com MPIO e duas interfaces de rede. |
| Transferência de leitura/gravação do cliente máximo (quando served da camada de disco rígido) * | 120/250 MB/s |
| Cliente máximo leitura/gravação produtividade (quando served da camada de nuvem)* para atualização 3 e posterior** | 40/60 MB/s para hierárquico volumes<br><br>60/80 MB/s para hierárquico volumes com a opção de arquivamento selecionada durante a criação de volume | Taxa de transferência de leitura depende de clientes gerar e manter suficiente profundidade de fila e/s. <br><br>Velocidade obtida depende da velocidade da conta de armazenamento subjacentes usada. | 

& 42; Taxa de transferência máxima por tipo de e/s foi medida com 100 por cento leitura e gravação de 100 por cento cenários. Taxa de transferência real pode ser menor e depende de e/s misturar e condições de rede.

& 42; & 42; Números de desempenho antes da atualização 3 podem ser menores.

## <a name="next-steps"></a>Próximas etapas

Examine os [requisitos de sistema do StorSimple](storsimple-system-requirements.md). 
