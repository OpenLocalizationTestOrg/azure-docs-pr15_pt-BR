<properties 
   pageTitle="Instalar atualizações em uma matriz de Virtual StorSimple | Microsoft Azure"
   description="Descreve como usar a web de Array Virtual StorSimple UI para aplicar atualizações usando o método de portal e hotfix"
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
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Instalar atualizações em seu Array Virtual StorSimple

## <a name="overview"></a>Visão geral

Este artigo descreve as etapas necessárias para instalar atualizações em seu Array Virtual StorSimple através da interface do usuário da web local e o portal de clássico Azure. Você precisa aplicar atualizações de software ou hotfixes para manter sua matriz Virtual StorSimple atualizada. 

Tenha em mente que instalar uma atualização ou hotfix reinicia seu dispositivo. Considerando que a matriz Virtual StorSimple é um dispositivo de nó único, qualquer e/s em andamento for interrompida e tempo de inatividade de experiências de seu dispositivo. 

Antes de aplicar uma atualização, recomendamos que você execute os volumes ou compartilhamentos offline do host primeiro e, em seguida, o dispositivo. Isso minimiza qualquer possibilidade de corrupção de dados.

> [AZURE.IMPORTANT] Se você estiver executando atualização 0,1 ou versões de software GA, você deve use o método de hotfix via web local da interface do usuário para instalar a atualização 0,3. Se você estiver executando atualização 0,2, recomendamos que você instale as atualizações através do portal de clássico Azure.

## <a name="use-the-local-web-ui"></a>Usar o interface do usuário de web local 
 
Há duas etapas quando usando a web local da interface do usuário:

- Baixe a atualização ou o hotfix
- Instale a atualização ou o hotfix

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as seguintes etapas para baixar a atualização de software do catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para baixar a atualização ou o hotfix

1. Inicie o Internet Explorer e navegue até [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se esta for a primeira vez usando o catálogo do Microsoft Update neste computador, clique em **instalar** quando solicitado a instalar o complemento do catálogo do Microsoft Update.
  
3. Na caixa de pesquisa de catálogo do Microsoft Update, insira o número da Base de dados de Conhecimento (KB) do hotfix que você deseja baixar. Insira **3182061** para atualização 0,3 e clique em **Pesquisar**.

    A listagem de hotfix aparece, por exemplo, **StorSimple Virtual matriz atualização 0,3**.

    ![Catálogo de pesquisa](./media/storsimple-ova-install-update-01/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao carrinho.

5. Clique em **Exibir cesta**.

6. Clique em **Baixar**. Especifique ou **Procure** um local local onde você deseja que os downloads apareça. As atualizações são baixadas para o local especificado e colocadas em uma subpasta com o mesmo nome que a atualização. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.

7. Abra a pasta copiada, você deve ver um arquivo de pacote do Microsoft Update autônomo `WindowsTH-KB3011067-x64`. Esse arquivo é usado para instalar a atualização ou hotfix.


### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou hotfix, verifique se que você tem a atualização ou o hotfix baixados localmente em seu host ou acessível através de um compartilhamento de rede. 

Use esse método para instalar atualizações em um dispositivo executando GA ou atualizar 0,1 versões de software. Esse procedimento leva menos de 2 minutos para ser concluída. Execute as seguintes etapas para instalar a atualização ou hotfix.


#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou o hotfix

1. Na web local UI, vá para **manutenção** > **Atualização de Software**.

    ![atualizar dispositivo](./media/storsimple-ova-install-update-01/update1m.png)

2. Em **atualizar o caminho do arquivo**, digite o nome de arquivo para a atualização ou o hotfix. Você também pode navegar para o arquivo de instalação de atualização ou hotfix se colocado em um compartilhamento de rede. Clique em **Aplicar**.

    ![atualizar dispositivo](./media/storsimple-ova-install-update-01/update2m.png)

3.  Um aviso é exibido. Fornecido isto é um dispositivo de nó único, após a atualização é aplicada, reinicia o dispositivo e não há tempo de inatividade. Clique no ícone de seleção.

    ![atualizar dispositivo](./media/storsimple-ova-install-update-01/update3m.png)

4. A atualização é iniciado. Após o dispositivo com êxito for atualizado, ele será reiniciado. Interface do usuário do local não está acessível essa duração.

    ![atualizar dispositivo](./media/storsimple-ova-install-update-01/update5m.png)

5. Após a reinicialização estiver concluída, é exibida a página **entrar** . Para verificar se o software do dispositivo foi atualizado na web local UI, vá para **manutenção** > **Atualização de Software**. A versão do software exibida deve ser **10.0.0.0.0.10288.0** para atualização 0,3.

    > [AZURE.NOTE] Podemos reportar as versões de software no maneira um pouco diferente na web local da interface do usuário e o portal de clássico Azure. Por exemplo, da web local UI **10.0.0.0.0.10288** portal clássico do Azure relatórios e **10.0.10288.0** para a mesma versão. 

    ![atualizar dispositivo](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Usar o portal de clássico do Azure

Se executando atualização 0,2, recomendamos que você instale as atualizações por meio do portal de clássico Azure. O procedimento de portal requer o usuário digitalizar, baixe e instale as atualizações. Esse procedimento leva cerca de 7 minutos para ser concluída. Execute as seguintes etapas para instalar a atualização ou hotfix.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Após a instalação estiver concluída (conforme indicado pelo status do trabalho em 100%), vá para **dispositivos > manutenção > atualizações de Software**. A versão do software exibida deve ser 10.0.10288.0.

![atualizar dispositivo](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).
