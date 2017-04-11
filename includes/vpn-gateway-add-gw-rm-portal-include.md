1. No portal, vá para o **novo**. Digite "Gateway de rede Virtual" na pesquisa. Localize o **gateway de rede Virtual** na pesquisa do remetente e clique na entrada. Isso abre a lâmina de **gateway de rede virtual de criar** .
2. Clique em **criar** na parte inferior da lâmina **gateway de rede Virtual** . O **gateway de rede virtual criar** blade será aberta. Preencha os valores para o gateway de rede virtual.

    ![Criar campos de blade de gateway de rede virtual] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Criar campos de blade de gateway de rede virtual")

3. **Nome**: nome do seu gateway. Isso não é a mesma que nomear uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.

4. **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual **VPN**. 

5. **Tipo de VPN**: selecione o tipo VPN especificado para sua configuração. Maioria das configurações requer um tipo VPN baseada em rota.

6. **SKU**: selecione o gateway SKU na lista suspensa. As SKUs listadas na lista suspensa dependem do tipo VPN selecionado.

7. **Local**: ajustar o campo **local** para apontar para o local onde se encontra sua rede virtual.
 
8. Escolha a rede virtual à qual você deseja adicionar este gateway. Clique em **rede Virtual** para abrir a lâmina de **Escolher uma rede virtual** . Selecione o VNet. Se você não vir seu VNet, verifique se que o campo **local** está apontando para a região em que sua rede virtual está localizado.

9. Escolha um endereço IP público. Clique em **endereço IP público** para abrir a lâmina **Escolha endereço IP público** . Clique em **+ Criar novo** para abrir a **criar pública lâmina de endereço IP**. Insira um nome para seu endereço IP público. Este blade cria um objeto de endereço IP público para que um endereço IP público será atribuído dinamicamente.<br>Clique em **Okey** para salvar as alterações para este blade.

10. **Assinatura**: Verifique se a assinatura correta está selecionada.

11. **Grupo de recursos**: esta configuração é determinada pela rede Virtual que você selecionar. 

12. Não ajuste o **local** depois que você especificou as configurações anteriores.

13. Verifique as configurações. Você pode selecionar **Fixar no painel de controle** na parte inferior da lâmina se quiser que o seu gateway para aparecem no painel.

14. Clique em **criar** para começar a criar o gateway. As configurações serão validadas e você verá o gateway de rede"Implantando Virtual" lado a lado no painel. Criar um gateway pode levar até 45 minutos. Você talvez precise atualizar sua página do portal para ver o status concluído.

    ![Implantando Virtual gateway de rede] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Implantando Virtual gateway de rede")

11. Depois que o gateway for criado, você pode exibir o endereço IP que foi atribuído a ele examinando a rede virtual no portal. O gateway será exibido como um dispositivo conectado. Você pode clicar o dispositivo conectado (o gateway de rede virtual) para exibir mais informações.



