<properties 
   pageTitle="Configurar o CHAP para seu dispositivo de StorSimple | Microsoft Azure"
   description="Descreve como configurar o protocolo de autenticação de Handshake de desafio (CHAP) em um dispositivo de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Configurar o CHAP para seu dispositivo de StorSimple

Este tutorial explica como configurar CHAP para seu dispositivo de StorSimple. O procedimento detalhado neste artigo se aplica a série 8000 StorSimple, bem como dispositivos de StorSimple 1200.

CHAP significa protocolo de autenticação de desafio. É um esquema de autenticação usado por servidores para validar a identidade de clientes remotos. A verificação baseia-se em uma senha compartilhado ou segredo. CHAP pode ser unidirecional (unidirecional) ou mútuo (bidirecional). CHAP unidirecional é quando o destino autentica um iniciador. CHAP comum ou inversa, por outro lado, requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. Autenticação do iniciador pode ser implementada sem autenticação de destino. No entanto, autenticação de destino pode ser implementada somente se a autenticação do iniciador também é implementada. 

Como prática recomendada, recomendamos que você use CHAP para aprimorar a segurança iSCSI.

>[AZURE.NOTE] Tenha em mente que IPSEC não é suportado atualmente em dispositivos de StorSimple.

As configurações de CHAP no dispositivo StorSimple podem ser configuradas das seguintes maneiras:

- Autenticação unidirecional ou unidirecional

- Bidirecional ou autenticação comum ou inversa

Em cada um desses casos, o portal para o dispositivo e o software do iniciador do servidor iSCSI precisa ser configurado. As etapas detalhadas para esta configuração são descritas no tutorial a seguir.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidirecional ou unidirecional

Na autenticação unidirecional, o destino autentica o iniciador. Essa autenticação requer que você configure as configurações do iniciador CHAP no dispositivo de StorSimple e o software do iniciador do host iSCSI. Os procedimentos detalhados para seu dispositivo de StorSimple e o host do Windows são descritos a seguir.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar seu dispositivo para autenticação unidirecional

1. No portal do clássico Azure, na página de **dispositivos** , clique na guia **Configurar** .

    ![Iniciador CHAP](./media/storsimple-configure-chap/IC740943.png)

2. Rolar para baixo nesta página e na seção **CHAP iniciador** :
                                                    
    1. Forneça um nome de usuário para o iniciador CHAP.

    2. Fornece uma senha para o iniciador CHAP.

         > [AZURE.IMPORTANT] O nome de usuário CHAP deve conter menos de 233 caracteres. A senha CHAP deve estar entre 12 e 16 caracteres. Um nome de usuário ou senha mais resultará em uma falha de autenticação do host do Windows.
    
    3. Confirme a senha.

4. Clique em **Salvar**. Uma mensagem de confirmação será exibida. Clique em **Okey** para salvar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação unidirecional no servidor de host do Windows

1. No servidor de host do Windows, inicie o iniciador iSCSI.

2. Na janela **Propriedades do iniciador iSCSI** , execute as seguintes etapas:
                                                    
    1. Clique na guia **Discovery** .

        ![Propriedades do iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)

    2. Clique em **Descubra o Portal**.

3. Na caixa de diálogo **Descubra o Portal de destino** :
                                                    
    1. Especifique o endereço IP do seu dispositivo.

    3. Clique em **Avançado**.

        ![Descubra o portal de destino](./media/storsimple-configure-chap/IC740945.png)

4. Na caixa de diálogo **Configurações avançadas** :
                                                    
    1. Marque a caixa de seleção **Habilitar CHAP logon** .

    2. No campo **nome** , forneça o nome de usuário que você especificou para o iniciador CHAP no portal do clássico.

    3. No campo **segredo de destino** , fornece a senha que você especificou para o iniciador CHAP no portal do clássico.

    4. Clique em **Okey**.

        ![Configurações avançadas gerais](./media/storsimple-configure-chap/IC740946.png)

5. Na guia **destinos** da janela de **Propriedades do iniciador iSCSI** , o status do dispositivo deve aparecer como **conectado**. Se você estiver usando um dispositivo de StorSimple 1200, em seguida, cada volume será montado como um destino iSCSI conforme mostrado abaixo. Portanto, as etapas 3-4 precisa ser repetido para cada volume.

    ![Volumes montados como destinos separados](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Se você alterar esse nome, o novo nome será usado para novas sessões de iSCSI. Novas configurações não são usadas para existente sessões até que você faz logoff e faça logon novamente.

Para obter mais informações sobre como configurar CHAP no servidor de host do Windows, vá para [Considerações adicionais](#additional-considerations).


## <a name="bidirectional-or-mutual-authentication"></a>Bidirecional ou autenticação mútua

Na autenticação bidirecional, o destino autentica o iniciador e, em seguida, o iniciador autentica o destino. Isso requer que o usuário configure as configurações do iniciador CHAP, bem como as configurações de CHAP inversa no software do iniciador iSCSI e dispositivo do host. Os procedimentos a seguir descrevem as etapas para configurar a autenticação comum no dispositivo e no host Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar seu dispositivo para autenticação comum

1. No portal do clássico Azure, na página de **dispositivos** , clique na guia **Configurar** .

    ![Destino CHAP](./media/storsimple-configure-chap/IC740948.png)

2. Rolar para baixo nesta página e na seção de **Destino CHAP** :
                                                    
    1. Forneça um **nome de usuário CHAP reverter** para o seu dispositivo.

    2. Fornece uma **senha reverter CHAP** para seu dispositivo.

    3. Confirme a senha.

3. Na seção **CHAP iniciador** :
                                                
    1. Forneça um **nome de usuário** para o seu dispositivo.

    1. Fornece uma **senha** para o seu dispositivo.

    3. Confirme a senha.

4. Clique em **Salvar**. Uma mensagem de confirmação será exibida. Clique em **Okey** para salvar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação de bidirecional no servidor de host do Windows

1. No servidor de host do Windows, inicie o iniciador iSCSI.

2. Na janela **Propriedades do iniciador iSCSI** , clique na guia de **configuração** .

3. Clique em **CHAP**.

4. Na caixa de diálogo **iSCSI segredo de CHAP de iniciador comum** :
                                                    
    1. Digite a **Senha de CHAP reverter** que você configurou no portal de clássico do Azure.

    2. Clique em **Okey**.

        ![iSCSI iniciador segredo](./media/storsimple-configure-chap/IC740949.png)

5. Clique na guia **destinos** .

6. Clique no botão **Conectar** . 

7. Na caixa de diálogo **Conectar ao destino** , clique em **Avançado**.

8. Na caixa de diálogo **Propriedades avançadas** :
                                                    
    1. Marque a caixa de seleção **Habilitar CHAP logon** .

    2. No campo **nome** , forneça o nome de usuário que você especificou para o iniciador CHAP no portal do clássico.

    3. No campo **segredo de destino** , fornece a senha que você especificou para o iniciador CHAP no portal do clássico.

    4. Marque a caixa de seleção **Executar comum autenticação** .

        ![Autenticação comum de configurações avançadas](./media/storsimple-configure-chap/IC740950.png)

    5. Clique em **Okey** para concluir a configuração de CHAP
     
Para obter mais informações sobre como configurar CHAP no servidor de host do Windows, vá para [Considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais

O recurso **Conexão rápida** não oferece suporte para conexões que têm CHAP habilitado. Quando o CHAP está habilitado, certifique-se de que você use o botão **Connect** que está disponível na guia **destinos** para se conectar a um destino.

![Conectar ao destino](./media/storsimple-configure-chap/IC740947.png)

Na caixa de diálogo **conectar ao destino** apresentados, marque a caixa de seleção **Adicionar esta conexão à lista de destinos favoritos** . Isso garante que sempre que o computador reiniciar, uma tentativa para restaurar a conexão com os destinos de iSCSI favorito.

## <a name="errors-during-configuration"></a>Erros durante a configuração

Se sua configuração de CHAP está incorreta, em seguida, você é provável ver uma mensagem de erro de **Falha de autenticação** .

## <a name="verification-of-chap-configuration"></a>Verificação de configuração de CHAP

Você pode confirmar que CHAP está sendo usado realizando as etapas a seguir.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar sua configuração de CHAP

1. Clique em **destinos favoritos**.

2. Selecione o destino para a qual habilitou autenticação.

3. Clique em **detalhes**.

    ![destinos do favorito de propriedades do iniciador iSCSI](./media/storsimple-configure-chap/IC740951.png)

4. Na caixa de diálogo **Detalhes de destino favorito** , observe a entrada no campo **autenticação** . Se a configuração foi bem-sucedida, ela deverá dizer **CHAP**.

    ![Detalhes de destino favorito](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [segurança de StorSimple](storsimple-security.md).

- Saiba mais sobre como [usar o serviço de Gerenciador de StorSimple para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).
