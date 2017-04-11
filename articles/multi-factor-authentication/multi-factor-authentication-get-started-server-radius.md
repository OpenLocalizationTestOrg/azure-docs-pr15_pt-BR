<properties 
    pageTitle="Servidor de autenticação multifator autenticação RADIUS e Azure"
    description="Esta é a página de autenticação multifator do Azure que ajudarão na implantação autenticação RADIUS e servidor de autenticação multifator do Azure."
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



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticação multifator autenticação RADIUS e Azure

Na seção autenticação RADIUS permite habilitar e configurar a autenticação RADIUS para o servidor de autenticação multifator Azure. RADIUS é um protocolo padrão para aceitar solicitações de autenticação e processar essas solicitações. O servidor de autenticação multifator Azure atua como um servidor RADIUS e é inserido entre o cliente RADIUS (por exemplo, o dispositivo de VPN) e seu destino de autenticação, o que poderia ser Active Directory (AD), um diretório LDAP ou outro servidor RADIUS, para adicionar autenticação multifator do Azure. Para a autenticação multifator de Azure a função, você deve configurar o servidor de autenticação multifator Azure para que ele possa se comunicar com os servidores de cliente e o destino de autenticação. O servidor de autenticação multifator Azure aceita solicitações de um cliente RADIUS, valida credenciais em relação ao destino de autenticação, adiciona autenticação multifator do Azure e envia uma resposta de volta para o cliente RADIUS. A autenticação inteira funcionará somente se o a autenticação primária e a autenticação multifator Azure êxito.

>[AZURE.NOTE]
>O servidor de MFA suporta apenas PAP (protocolo de autenticação de senha) e MSCHAPv2 (protocolo de autenticação de Handshake de desafio da Microsoft) RADIUS protocolos quando atuando como um servidor RADIUS.  Outros protocolos como EAP (protocolo de autenticação extensível) podem ser usados quando o servidor MFA atua como um proxy RADIUS para outro servidor RADIUS que suporta esse protocolo como Microsoft NPS.
></br>
>Ao usar outros protocolos nessa configuração, tokens SMS e JURAMENTO unidirecionais não funcionará como o servidor de MFA não é possível iniciar uma resposta de desafio RADIUS bem-sucedida usando esse protocolo.


![Autenticação RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Configuração de autenticação de RADIUS

Para configurar a autenticação RADIUS, instale o servidor de autenticação multifator Azure em um servidor Windows. Se você tiver um ambiente Active Directory, o servidor deve ser associado ao domínio dentro da rede. Use o procedimento a seguir para configurar o servidor de autenticação multifator Azure:

1. Dentro do servidor de autenticação multifator Azure clique no ícone de autenticação RADIUS no menu à esquerda.
2. Marque a caixa de seleção Habilitar RADIUS autenticação.
3. Na guia clientes altere a porta de autenticação e contabilidade portas se o serviço RADIUS de autenticação multifator Azure deve ligar para portas não padrão para ouvir solicitações RADIUS dos clientes que serão configurados.
4. Clique em Adicionar... botão.
5. Na caixa de diálogo Adicionar cliente RADIUS, digite o endereço IP do dispositivo/servidor que irá autenticar o servidor de autenticação multifator Azure, o nome de um aplicativo (opcional) e um segredo compartilhado. O segredo compartilhado precisa ser o mesmo para o servidor de autenticação multifator do Azure e o aparelho/servidor. O nome do aplicativo aparece em relatórios de autenticação multifator de Azure e pode ser exibido em mensagens de autenticação de SMS ou aplicativo móvel.
6. Marque a caixa de correspondência de usuário de exigir autenticação de vários fatores se todos os usuários foram ou serão importados para o servidor e sujeitos a autenticação multifator. Se um número significativo de usuários ainda não foram importado para o servidor e/ou será isentar de autenticação multifator, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre este recurso.
7. Marque a caixa habilitar a fallback token de JURAMENTO se os usuários utilizarão a autenticação de aplicativo móvel do Azure multi-Factor Authentication e você quiser usar senhas de JURAMENTO como uma autenticação de fallback para a notificação de chamada, o SMS ou o envio de telefone de fora da faixa.
8. Clique no botão Okey.
9. Você pode repetir as etapas 4 a 8 para adicionar clientes RADIUS adicionais.
10. Clique na guia de destino.
11. Se o servidor de autenticação multifator Azure estiver instalado em um servidor de domínio em um ambiente do Active Directory, selecione o domínio do Windows.
12. Se os usuários devem ser autenticados em relação a um diretório LDAP, selecione ligação LDAP. Ao usar ligação LDAP, você deve clique no ícone de integração de diretório e editar a configuração de LDAP na guia Configurações, para que o servidor pode vincular ao seu diretório. Instruções para configurar LDAP podem ser encontradas na guia de configuração de Proxy de LDAP.
13. Se os usuários devem ser autenticados em relação a outro servidor RADIUS, selecione servidores RADIUS.
14. Configurar o servidor que o servidor irá proxy as solicitações RADIUS clicando em Adicionar... botão.
15. Na caixa de diálogo Adicionar servidor RADIUS insira o endereço IP do servidor RADIUS e um segredo compartilhado. O segredo compartilhado precisa ser o mesmo no servidor do servidor de autenticação multifator do Azure e RADIUS. Altere a porta de autenticação e a porta de contabilização se portas diferentes são usadas pelo servidor RADIUS.
16. Clique no botão Okey.
17. Você deve adicionar o servidor de autenticação multifator Azure como um cliente RADIUS no outro servidor RADIUS para que ele processará solicitações de acesso enviadas do servidor de autenticação multifator Azure. Você deve usar o mesmo segredo compartilhado configurado no servidor de autenticação multifator do Azure.
18. Você pode repetir essa etapa para adicionar servidores RADIUS adicionais e configurar a ordem em que o servidor deve ligá-los com os botões Mover para cima e mover para baixo. Isso conclui a configuração do servidor de autenticação multifator Azure. O servidor está agora listening nas portas configuradas para solicitações de acesso RADIUS dos clientes do configurado.   


## <a name="radius-client-configuration"></a>Configuração do cliente RADIUS

Para configurar o cliente RADIUS, use as diretrizes:

- Configure seu dispositivo/server para autenticar por meio de RADIUS para endereço IP do servidor do Azure multifator autenticação, que atua como o servidor RADIUS.
- Use o mesmo segredo compartilhado que foi configurado acima.
- Configure o tempo limite RADIUS para 30 a 60 segundos para que há tempo para validar as credenciais do usuário, execute a autenticação multifator, receberá sua resposta e responder a solicitação de acesso RADIUS.
