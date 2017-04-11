<properties 
    pageTitle="Como você redirecionar dispositivos USB no Azure RemoteApp? | Microsoft Azure" 
    description="Saiba como usar o redirecionamento para dispositivos USB no Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Como você redirecionar dispositivos USB no Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Redirecionamento de dispositivo permite aos usuários usar dispositivos USB conectados ao seu computador ou tablet com os aplicativos do Azure RemoteApp. Por exemplo, se você compartilhou Skype por meio do Azure RemoteApp, seus usuários precisam ser capaz de usar suas câmeras de dispositivo.

Antes de ir adiante, certifique-se de que você leia as informações de redirecionamento de USB na [usando redirecionamento no Azure RemoteApp](remoteapp-redirection.md). No entanto, o recomendável nusbdevicestoredirect:s: * não funcionar para câmeras da web USB e pode não funcionar para alguns dispositivos de múltiplas funções de USB ou impressoras USB. Por design e por razões de segurança, o administrador de RemoteApp Azure tem que habilitar o redirecionamento por GUID de classe do dispositivo ou ID de instância de dispositivo antes de seus usuários podem usar esses dispositivos.

Embora este artigo fala sobre redirecionamento de câmera da web, você pode usar uma abordagem semelhante para redirecionar impressoras USB e outros dispositivos de múltiplas funções USB que não serão redirecionados pelo **nusbdevicestoredirect:s:*** comando.

## <a name="redirection-options-for-usb-devices"></a>Opções de redirecionamento de dispositivos USB
RemoteApp Azure usa mecanismos muito semelhantes para redirecionar dispositivos USB como aquelas disponíveis para os serviços de área de trabalho remota. A tecnologia subjacente permite que você escolha o método de redirecionamento correto para um determinado dispositivo, para obter o melhor dos dois alto nível e RemoteFX USB redirecionamento usando o **usbdevicestoredirect:s:** comando. Há quatro elementos a este comando:

| Ordem de processamento | Parâmetro           | Descrição                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Seleciona todos os dispositivos que não estão selecionados pelo redirecionamento de alto nível. Observação: por design, * não funciona para câmeras da web USB.  |
|                  | {GUID de classe de dispositivo} | Seleciona todos os dispositivos que correspondam a classe de instalação do dispositivo especificado.                                                           |
|                  | USB\InstanceID      | Seleciona um dispositivo USB especificado para ID da instância de determinado.                                                                  |
| 2                | -ID USB\Instance    | Remove as configurações de redirecionamento para o dispositivo especificado.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Redirecionar um dispositivo USB, usando a classe de dispositivo GUID
Há duas maneiras de localizar a GUID que pode ser usado para redirecionamento de classe do dispositivo. 

A primeira opção é usar o [System-Defined dispositivo configuração Classes disponível para fornecedores](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Escolha a classe que mais se aproxime do dispositivo conectado ao computador local. Para câmeras digitais, isso pode ser uma classe de dispositivo de imagens ou dispositivo de captura de vídeo. Você precisará fazer algumas experimento com as classes de dispositivo para localizar a classe correta GUID que funciona com o dispositivo USB conectado localmente (no nosso caso a câmera web).

Uma maneira melhor ou a segunda opção, é siga estas etapas para localizar o GUID de classe do dispositivo específico:

1. Abra o Gerenciador de dispositivos, localize o dispositivo que será redirecionado e clique sobre ela e, em seguida, abra as propriedades.
![Abra o Gerenciador de dispositivos](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Na guia **detalhes** , escolha a propriedade **Guid de classe**. O valor que aparece é o GUID de classe para esse tipo de dispositivo.
![Propriedades da câmera](./media/remoteapp-usbredir/ra-classguid.png)
3. Use o valor de Guid de classe de redirecionar dispositivos que correspondem a ele.

Por exemplo:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Você pode combinar vários redirecionamentos de dispositivo no mesmo cmdlet. Por exemplo: para redirecionar o armazenamento local e uma webcam USB, cmdlet tem esta aparência:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Quando você configurar o redirecionamento de dispositivo por classe GUID todos os dispositivos que correspondam a classe GUID na coleção especificada serão redirecionados. Por exemplo, se houver vários computadores na rede local que tenham as mesmas câmeras da web USB, você pode executar um único cmdlet para redirecionar todas as câmeras da web.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Redirecionar um dispositivo USB usando a identificação de instância de dispositivo

Se você deseja ter mais controle refinado e deseja controlar o redirecionamento por dispositivo, você pode usar o parâmetro de redirecionamento de **USB\InstanceID** .

A parte mais difícil desse método está localizando a identificação de instância de dispositivo USB. Você precisará ter acesso ao computador e o dispositivo USB específico. Em seguida, siga estas etapas:

1. Habilitar o redirecionamento de dispositivo em sessão de área de trabalho remota, conforme descrito em [como posso usar meus dispositivos e recursos em uma sessão de área de trabalho remota?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Abra uma Conexão de área de trabalho remota e clique em **Mostrar opções**.
3. Clique em **Salvar como** para salvar as configurações de conexão atuais em um arquivo RDP.  
    ![Salvar as configurações como um arquivo RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Escolha um nome de arquivo e um local, por exemplo, "MyConnection.rdp" e "Este PC\Documents" e salvar o arquivo.
5. Abra o arquivo de MyConnection.rdp usando um editor de texto e localize o ID de instância do dispositivo que você deseja redirecionar.

Agora, use o ID de instância no seguinte cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Ajude-na ajudá-lo 
Você sabia que, além de classificação neste artigo e fazer comentários para baixo abaixo, você pode fazer alterações para o artigo próprio? Algo ausente? Algo errado? Eu tenha escrito algo que é apenas confuso? Rolar para cima e clique em **Editar no GitHub** para fazer alterações - aqueles se torne para revisão e, em seguida, assim que podemos entrar neles, você verá suas alterações e melhorias aqui.