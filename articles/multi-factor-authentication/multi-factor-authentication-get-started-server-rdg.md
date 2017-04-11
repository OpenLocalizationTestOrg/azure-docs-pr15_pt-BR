<properties 
    pageTitle="O Gateway de área de trabalho e servidor de autenticação multifator Azure usando RADIUS remoto"
    description="Esta é a página de autenticação multifator do Azure que ajudarão na implantação do Gateway de área de trabalho remota (RD) e servidor de autenticação multifator Azure usando o RADIUS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>O Gateway de área de trabalho e servidor de autenticação multifator Azure usando RADIUS remoto

Em muitos casos, o Gateway de área de trabalho remota usa o NPS local para autenticar usuários. Este documento descreve como rotear solicitação RADIUS check-out do Gateway da área de trabalho remota (por meio do NPS local) ao servidor de autenticação multifator.

O servidor de autenticação multifator deve ser instalado em um servidor separado, que irá proxy a solicitação RADIUS voltar para o NPS no servidor do Gateway de área de trabalho remota. Quando o NPS valida o nome de usuário e a senha, ela retornará uma resposta para o servidor de autenticação multifator que executa o segundo fator de autenticação antes de retornar um resultado para o gateway.





## <a name="configure-the-rd-gateway"></a>Configurar o Gateway de RD

O Gateway RD deverá ser configurado para enviar autenticação RADIUS para um servidor de autenticação multifator Azure. Após a instalação do Gateway RD, configurado e está trabalhando, vá até as propriedades de Gateway RD. Vá para a guia de repositório de PONTA RD e altere-o para usar um servidor Central que executa o NPS em vez de servidor Local NPS. Adicione um ou mais servidores de autenticação multifator de Azure como servidores RADIUS e especifique um segredo compartilhado para cada servidor.





## <a name="configure-nps"></a>Configurar o NPS

O Gateway de RD usa NPS para enviar a solicitação RADIUS para a autenticação multifator Azure. Um tempo limite deve ser alterado para impedir que o Gateway de RD de tempo limite antes de autenticação multifator foi concluída. Use o procedimento a seguir configurá-lo.

1. No NPS, expanda o menu de servidor e clientes RADIUS na coluna à esquerda e clique em grupos de servidores RADIUS remotos. Vá até as propriedades do grupo de servidor de GATEWAY TS. Editar os servidores RADIUS exibido e vá para a guia de balanceamento de carga. Alterar o "número de segundos sem resposta antes de solicitação é considerada descartados" e o "número de segundos entre solicitações quando o servidor é identificado como indisponível" ao 30-60 segundos. Clique na guia/conta de autenticação e garantir que as portas RADIUS especificadas correspondam as portas que o servidor de autenticação multifator vai ser listening em.
2. NPS também deve ser configurado para receber autenticação RADIUS volta do servidor de autenticação multifator Azure. Clique em clientes RADIUS no menu à esquerda. Adicione o servidor de autenticação multifator Azure como um cliente RADIUS. Escolha um nome amigável e especifique um segredo compartilhado.
3. Expanda a seção de políticas no painel esquerdo e clique em diretivas de solicitação de Conexão. Ele deve conter uma diretiva de solicitação de Conexão chamado diretiva de autorização de GATEWAY TS que foi criada quando o Gateway RD foi configurado. Essa política encaminha solicitações RADIUS para o servidor de autenticação multifator.
4. Copie esta política para criar um novo. Na nova política, adicione uma condição que corresponde ao nome amigável do cliente com o nome amigável definida na etapa 2 acima para o cliente RADIUS do Azure multi-Factor Authentication Server. Altere o provedor de autenticação para o computador Local. Essa política garante que, quando uma solicitação RADIUS é recebida do servidor de autenticação Azure multifator, a autenticação ocorre localmente em vez de enviar uma solicitação RADIUS volta para o servidor de autenticação multifator Azure que resultaria em uma condição de loop. Para impedir que a condição de loop, essa nova diretiva deve ser solicitada acima a política original que encaminha para o servidor de autenticação multifator.

## <a name="configure-azure-multi-factor-authentication"></a>Configurar a autenticação multifator Azure


--------------------------------------------------------------------------------



O servidor de autenticação multifator Azure é configurado como um proxy RADIUS entre Gateway RD e NPS.  Ele deve ser instalado em um servidor do domínio que está separado do servidor de Gateway RD. Use o procedimento a seguir para configurar o servidor de autenticação multifator Azure.

1. Abra o servidor de autenticação multifator do Azure e clique no ícone de autenticação RADIUS. Marque a caixa de seleção Habilitar RADIUS autenticação.
2. Na guia clientes, certifique-se de que as portas correspondem ao que está configurado no NPS e clique em Adicionar... botão. Adicione o endereço IP do servidor de Gateway RD, nome do aplicativo (opcional) e um segredo compartilhado. O segredo compartilhado precisa ser o mesmo no servidor de autenticação multifator do Azure e Gateway RD.
3. Clique na guia destino e escolha o botão de opção de servidor (es) RADIUS.
4. Clique em Adicionar … botão. Insira o endereço IP, segredo compartilhado e portas do servidor NPS. A menos que usando um NPS central, o cliente RADIUS e destino RADIUS serão iguais. O segredo compartilhado deve corresponder a uma configuração na seção de cliente RADIUS do servidor NPS.

![Autenticação RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
