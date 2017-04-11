<properties 
   pageTitle="Substituir um controlador de StorSimple EBOD | Microsoft Azure"
   description="Explica como remover e substituir um ou ambos os controladores EBOD em um dispositivo StorSimple 8600."
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
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Substituir um controlador EBOD em seu dispositivo de StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como substituir um módulo de controlador EBOD com defeito em seu dispositivo de StorSimple do Microsoft Azure. Para substituir um módulo de controlador EBOD, você precisa:

- Remover o controlador EBOD com defeito
- Instalar um novo controlador EBOD

Antes de começar, considere as seguintes informações:

- Módulos EBOD em branco devem ser inseridos em todos os slots não utilizados. Compartimento não serão interessantes corretamente se um slot permanecerá aberto.

- O controlador EBOD é trocados e podem ser removidos ou substituídos. Não remova um módulo falhou até que você tenha uma substituição. Quando você inicia o processo de substituição, você deve conclui-lo em 10 minutos.

>[AZURE.IMPORTANT] Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que você examine as [convenções de ícone de segurança](storsimple-safety.md#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Remover um controlador EBOD

Antes de substituir o módulo de controlador EBOD falha no seu dispositivo StorSimple, certifique-se de que o outro módulo de controlador EBOD está ativo e em execução. O procedimento e a tabela a seguir explicam como remover o módulo do controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para remover um módulo EBOD

1. Abra o portal do clássico Azure.

2. Navegue até **dispositivos** > **manutenção** > **Status de Hardware**e verifique se o status do LED para o módulo de controlador EBOD ativo é verde e o LED do módulo de controlador EBOD falha está vermelho.

3. Localize o módulo de controlador EBOD Falha na parte posterior do dispositivo.

4. Remova os cabos que conectam o módulo do controlador EBOD para o controlador antes de fazer o módulo EBOD fora do sistema.

5. Anote a porta SAS exata do módulo controlador EBOD que estava conectado ao controlador. Você precisará restaurar o sistema para essa configuração depois de você substituir o módulo EBOD. 

    >[AZURE.NOTE] Normalmente, esta será A de porta, que está rotulada como **Host** no diagrama a seguir.

    ![Controlador de backplane de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figura 1** Verso módulo EBOD

  	|Rótulo|Descrição|
  	|:----|:----------|
  	|1|LED de falha|
  	|2|LED de energia|
  	|3|Conectores SAS|
  	|4|LED SAS|
  	|5|Portas seriais fábrica somente para uso|
  	|6|Porta um (Host)|
  	|7|Porta B (Host-out)|
  	|8|C de porta (apenas para uso de fábrica)|

## <a name="install-a-new-ebod-controller"></a>Instalar um novo controlador EBOD

O procedimento e a tabela a seguir explicam como instalar um módulo de controlador EBOD em seu dispositivo de StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar um controlador EBOD

1. Verifique o dispositivo EBOD para danificar, especialmente o conector de interface. Não instale o novo controlador EBOD se houver pinos tortos.

2. Com as presilhas na posição aberta, deslize o módulo no compartimento até que as travas.

    ![Instalando controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figura 2**  Instalando o módulo do controlador EBOD

3. Feche a trava. Como a trava é ativado, você deverá ouvir um clique.

    ![Liberar trava EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figura 3**  Fechando a trava de módulo EBOD

4. Reconecte os cabos. Use a configuração exata que estava presente antes de substituição. Consulte o diagrama e a tabela para obter detalhes sobre como conectar os cabos a seguir.

    ![Cabo seu dispositivo 4U para power](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figura 4**. Reconectando cabos

  	|Rótulo|Descrição|
  	|:----|:----------|
  	|1|Compartimento principal|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|Controlador de 0|
  	|5|Controlador de 1|
  	|6|Controlador EBOD 0|
  	|7|Controlador EBOD 1|
  	|8|Compartimento EBOD|
  	|9|Unidades de distribuição de energia|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [substituição de componentes de hardware StorSimple](storsimple-hardware-component-replacement.md).
