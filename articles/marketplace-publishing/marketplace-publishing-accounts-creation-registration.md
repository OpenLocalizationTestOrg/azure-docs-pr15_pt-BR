<properties
   pageTitle="Criando e registrando a conta do publisher | Microsoft Azure"
   description="Instruções para criar uma conta da Microsoft Developer, portanto, após a aprovação, você pode vender vários oferecem tipos no Azure Marketplace."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="hascipio"/>

# <a name="create-a-microsoft-developer-account"></a>Criar uma conta do Microsoft Developer
Este artigo o orienta a criação de conta necessário e o processo de registro para se tornar um Microsoft Developer aprovados do Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Crie uma conta da Microsoft
Para iniciar o processo de publicação, você precisará criar uma conta da Microsoft. Essa conta será usada para registrar no **Microsoft Developer Center** tanto o **Portal de publicação do Azure**. Você deve ter apenas uma conta da Microsoft para suas ofertas do Azure Marketplace. Ela não deve ser específica aos serviços ou ofertas.

O endereço de forma o nome de usuário deve estar em seu domínio e controlado pela sua equipe de TI. Todas as atividades relacionadas publicação devem ser feitas através desta conta.

  >[AZURE.WARNING] Palavras como **"Azure"** e **"Microsoft"** não há suporte para o registro de conta da Microsoft. Evite usar estas palavras para concluir a criação da conta e o processo de registro.

### <a name="instructions"></a>Instruções

1. Crie uma lista de distribuição (DL) ou o grupo de segurança (SG) dentro de domínio da sua empresa. Usar uma DL permite que várias pessoas recebam notificações de email que são importantes para comunicação de informações de pagamento. Ele também garante que a propriedade da conta da Microsoft pode ser transferida e não está vinculada a um único indivíduo.
Siga as instruções fornecidas abaixo.

    1. Adicione sua equipe de integração à lista de distribuição.
    2. Garantir que o DL/SG é um endereço de email ativo e é capaz de receber emails, porque o pagamento, informações sobre impostos e relatórios serão ser roteados através desta conta.
    3. Recomendamos o uso algo parecido com marketplace@partnercompany.com como o endereço de email para o DL/SG.

2. Abra uma nova navegação anônima do Chrome ou Internet Explorer InPrivate sessão de navegação para garantir que você não estiver conectado a uma conta existente.
3. Registre DL criado na etapa 1 como uma conta da Microsoft usando o link [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Siga as instruções abaixo.

    1. Durante a registrar sua conta como uma conta da Microsoft, você precisa fornecer um número de telefone válido para o sistema para enviar a você um código de verificação de conta como uma mensagem de texto ou em uma chamada automatizada.
    2. Durante a registrar sua conta como uma conta da Microsoft, você precisa fornecer uma identificação de email válido para receber um email automatizado para verificação de conta.

4. Verifique se o endereço de email enviado para a lista de distribuição.
5. Agora você está pronto para usar a nova conta da Microsoft no Microsoft Developer Center.

## <a name="2-create-your-microsoft-developer-center-account"></a>2. Crie sua conta do Microsoft Developer Center
O Microsoft Developer Center é usado para registrar as informações da empresa uma vez. O inscrito deve ser um representante válido da empresa e deve fornecer suas informações pessoais, como uma forma para validar sua identidade. A pessoa registrando deve usar uma conta da Microsoft que está compartilhada da empresa, **e a mesma conta deve ser usada no Portal de publicação do Azure.** Você deve verificar para certificar-se de que sua empresa ainda não tiver uma conta do Microsoft Developer Center antes de tentar criar uma. Durante o processo, podemos coletará informações de endereço da empresa, informações de conta bancária e informações de imposto. Estas são normalmente obtidas no contatos Finanças ou comercial.

> [AZURE.IMPORTANT] Você deve concluir os seguintes componentes de perfil do desenvolvedor para percorrer as diversas fases da implantação e criação de oferta.


| Perfil de desenvolvedor | Para iniciar o rascunho | Preparação | Publicar gratuitamente e modelo de solução | Publicar comercial |
|----|----|----|----|----|
|Registro da empresa | Deve ter | Deve ter | Deve ter | Deve ter |
|ID de perfil de imposto | Opcional | Opcional | Opcional | Deve ter |
|Conta bancária | Opcional | Opcional | Opcional | Deve ter |


> [AZURE.NOTE] Trazer seu próprio licença (BYOL) é suportado apenas para máquinas virtuais e é considerada uma oferta **gratuita** .


### <a name="register-your-company-account"></a>Registrar sua conta da empresa
1. Abra uma nova InPrivate do Internet Explorer ou anônima do Chrome sessão de navegação para garantir que você não estiver conectado a uma conta pessoal.

2. Vá para [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) para registrar-se como um vendedor no Centro de desenvolvimento. Leia a observação importante a seguir antes de continuar.

    >[AZURE.IMPORTANT] Verifique se o email id ou lista de distribuição (uma lista de distribuição é recomendada remover dependência de indivíduos) que você usará para registrar no Centro de desenvolvimento do está no primeiro registrado como uma conta da Microsoft. Caso contrário, em seguida, registre usando esse [link](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Além disso, **qualquer id sob o domínio de empresa Microsoft de email ou seja @microsoft.com não podem ser usados** para o registro do Centro de desenvolvimento.

    ![desenho][img-signin]

3. Conclua o Assistente "Ajudar a proteger sua conta", que irá verificar sua identidade via telefone número ou endereço de email.

    ![desenho][img-verify]

4. Na seção "Informações de registro conta", selecione seu **país/região de conta** no menu suspenso.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

    > [AZURE.WARNING] **"Vender-de" países:** Para vender seus serviços no Azure Marketplace, sua entidade registrada precisa estar em um dos países aprovado "vender-de" acima. Essa restrição é por motivos de pagamento e tributação. Estamos ativamente procurando para expandir esta lista de países no futuro próximo, portanto, fique atento. Para obter mais informações, consulte as [políticas de participação de mercado](http://go.microsoft.com/fwlink/?LinkID=526833).

5. Selecione o tipo de conta"" como **empresa** e, em seguida, clique no botão **Avançar** .

    > [AZURE.IMPORTANT] Para entender melhor os tipos de conta e o que é melhor para sua escolha, veja página [taxas, locais e tipos de conta](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)

6. Insira o **nome de exibição do Publisher**, geralmente o nome da sua empresa.

    > [AZURE.TIP] O nome de exibição do publisher inserido no Centro de desenvolvimento não é exibido no mercado Azure após sua oferta vai listada. Mas isso deve ser preenchido para concluir o processo de registro.

7. Insira as **informações de contato** para a verificação de conta.

    > [AZURE.IMPORTANT] Você deve fornecer informações de contato precisas porque ela será usada no nosso processo de verificação para a sua empresa ser aprovados no centro do desenvolvedor.

8. Insira as informações de contato para o **Aprovador de empresa**. Aprovador da empresa é a pessoa que pode confirmar que você está autorizado a criar uma conta no Centro de desenvolvimento em nome de sua organização. Clique em **Avançar** para ir até a **"Seção de pagamento"** quando terminar.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)

9. Insira suas informações de pagamento para pagar pela sua conta. Se você tiver um código promocional que cubra o custo de registro, você pode inserir que aqui. Caso contrário, forneça suas informações de cartão de crédito (ou PayPal em mercados compatíveis). Quando tiver terminado, clique em **Avançar** para prosseguir para a **"Tela revisão"**.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)

10. Examine suas informações de conta e confirme que tudo está correto. Em seguida, leia e aceite os termos e condições do [Contrato de fornecedor do Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Marque a caixa para indicar que você leu e aceitou esses termos.

11. Clique em **Concluir** para confirmar seu registro. Enviaremos uma mensagem de confirmação para seu endereço de email.

12. Se você estiver planejando publicar apenas as ofertas grátis, clique em **Ir para o Portal de publicação do Azure Marketplace** e você pode pular para a seção 3 deste documento, [registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal).

Se você estiver planejando publicar comercial oferece (por exemplo, ofertas de máquina Virtual com o modelo de cobrança por hora), clique em **atualizar suas informações de conta** onde você deve preencher o imposto e as informações bancárias em sua conta do Developer Center.

Se você preferir atualizar suas informações de impostos e bancária posteriormente, você pode mover para a próxima seção ou seja seção 3 deste documento, [registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal)e vêm novamente mais tarde usando links no Portal de publicação do Azure.

> [AZURE.IMPORTANT] No caso de ofertas comerciais, não será capaz de envio por push as ofertas de produção sem concluir as informações de conta bancária e de imposto.

Se você preferir atualizar suas informações de impostos e bancária mais tarde, você pode ir para a seção 3, [registrar sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal)e vêm novamente mais tarde usando links no Portal de publicação do Azure.

### <a name="add-tax-and-banking-information"></a>Adicionar impostos e informações bancárias
 Se você quiser publicar ofertas comerciais para compra, você também precisa adicionar pagamento e informações de imposto e enviá-lo para validação no centro do desenvolvedor. Se você publica ofertas só gratuitas (ou BYOL oferece), não é necessário adicionar essas informações. Você pode adicioná-lo mais tarde, mas demora algum tempo para validar as informações de imposto. Se você souber que você oferecerá ofertas comerciais para compra, recomendamos que você adicioná-la assim que possível.

**Informações bancárias**

1. Entrar para o [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft.

2. Clique em **conta de pagamento** no menu à esquerda, em **Escolher método de pagamento** , clique em **conta bancária** ou **PayPal**.

    > [AZURE.IMPORTANT] Se você tiver ofertas comerciais que os clientes adquirir no mercado, esta é a conta onde você receberá pagamento para essas compras.

3. Insira as informações de pagamento e clique em **Salvar** quando estiver satisfeito.

    > [AZURE.IMPORTANT] Se você precisar atualizar ou alterar sua conta de pagamento, siga as mesmas etapas acima, substituindo as informações atuais com as novas informações. Alterar sua conta de pagamento pode atrasar os pagamentos por até um ciclo de pagamento. Esse atraso ocorre porque precisamos verificar a alteração da conta, como fizemos ao primeiro configurar a conta de pagamento. Você vai ainda obter pagos pelo valor total após sua conta tiver sido verificada; quaisquer pagamentos de conclusão para o pagamento atual ciclo será adicionado para a próxima.

4. Clique em **Avançar**.

**Informações de imposto**

1. Entrar para o [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) com sua conta da Microsoft (se necessário).

2. Clique em **perfil de imposto** no menu à esquerda.

3. Na página **Configurar o formulário de imposto** , selecione o país ou região em que você tiver residência permanente e, em seguida, selecione o país ou região onde você mantenha ações sociais principal. Clique em **Avançar**.

4. Insira os detalhes de imposto e clique em **Avançar**.

> [AZURE.WARNING] Você não poderá enviar ao seu comercial oferece sem concluir as informações de imposto e conta bancária em sua conta do Microsoft Developer Center de produção.

Se você tiver problemas com o registro de Developer Center, efetue um tíquete como abaixo

1. Vá para o link de suporte [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. **Fale** na seção, clique no botão **Enviar um incidente** (como mostrado na captura de tela abaixo)

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)

3. Escolha "Ajuda com o Centro de desenvolvimento" como **tipo de problema** e "Publicar e gerenciar aplicativos" como **categoria**. Depois clique no botão "Iniciar email".

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)

4. Você será fornecido com uma página de login. Use qualquer conta de entrada da Microsoft. Se você não tiver uma conta da Microsoft, em seguida, crie um usando esse [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Preencha os detalhes do problema e subit o tíquete clicando no botão **Enviar** .

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. registrar sua conta no portal de publicação
O [portal de publicação](http://publish.windowsazure.com) é usado para publicar e gerenciar suas ofertas.

1. Abra uma nova navegação anônima do Chrome ou Internet Explorer InPrivate sessão de navegação para garantir que você não estiver conectado a uma conta pessoal.

2. Vá para [http://publish.windowsazure.com](http://publish.windowsazure.com).

3. Se você for um novo usuário e entrar em publicação portal pela primeira vez, em seguida, você deve entrar com a mesma id de email com o qual sua conta do Centro de desenvolvimento está registrada. Dessa maneira sua conta do Centro de desenvolvimento e conta do portal de publicação serão vinculado uns com os outros. Você pode adicionar mais tarde dos outros membros da empresa, que estão trabalhando no aplicativo, como um administrador de co na publicação portal seguindo as etapas abaixo.

Se você é adicionado como um administrador de co na publicação portal, em seguida, você pode entrar com sua conta de administrador do co.

  > [AZURE.TIP] As políticas de participação são descritas no [site Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. etapas para adicionar um co-administrador na publicação portal
**Presumindo que você for o administrador,** fornecidas abaixo são as etapas para adicionar um co-administrador.

>[AZURE.NOTE] **Para os novos usuários,** antes de adicionar um administrador de co na publicação portal, certifique-se de que você tenha criado pelo menos um aplicativo na publicação portal. Isso é necessário como a guia **fornecedores** aparecem somente depois de criar pelo menos um aplicativo na publicação portal.

1. Certifique-se de que a id de email co-admin é uma account(MSA) da Microsoft. Caso contrário, registrado como uma MSA usando esse [link](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Verifique se há pelo menos um aplicativo sob a conta de administrador antes de tentar adicionar um co-administrador.
3. Depois de concluir as etapas acima, login publicação portal com a id de email do co-administração e log-out.
4. Agora login publicação portal com a id de email do administrador.
5. Navegue até fornecedores -> selecione sua conta -> administradores -> Adicionar co-administrador (captura de tela fornecida abaixo)

  ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. etapas para excluir um administrador de co na publicação portal
**Presumindo que você for o administrador,** fornecidas abaixo são as etapas para excluir um co-administrador.

1. Fazer login no publicação portal com a id de email do administrador.
2. Navegue até **fornecedores** -> selecione sua conta -> **administradores** -> **Co-administradores**.
3. Clique no botão **X** lado co-administrador que desejar excluir tot (captura de tela abaixo).

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Próximas etapas
Agora que sua conta é criada e registrada, certifique-se de que você atender ou atender a todos os pré-requisitos abrangem publicar sua oferta revisando [abrangem pré-requisitos](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
