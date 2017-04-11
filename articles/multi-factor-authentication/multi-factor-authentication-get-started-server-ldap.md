<properties 
    pageTitle="Servidor de autenticação multifator autenticação LDAP e Azure"
    description="Esta é a página de autenticação multifator do Azure que ajudarão na implantação autenticação LDAP e servidor de autenticação multifator do Azure."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticação multifator autenticação LDAP e Azure


Por padrão, o servidor de autenticação multifator Azure está configurado para importar ou sincronizar usuários do Active Directory. No entanto, ele pode ser configurado para vincular a diretórios LDAP diferentes, como um diretório ADAM ou controlador de domínio específico do Active Directory. Quando configurado para se conectar a um diretório via LDAP, o servidor de autenticação multifator Azure pode ser configurado para agir como um proxy LDAP para realizar autenticação. Ele também permite o uso de ligação LDAP como um destino RADIUS, pré-autenticação de usuários usando a autenticação do IIS, ou para autenticação primária no Portal de usuário do Azure multifator autenticação.

Ao usar autenticação multifator do Azure como um proxy LDAP, o servidor de autenticação multifator Azure é inserido entre o cliente LDAP (por exemplo, o aparelho de VPN, o aplicativo) e o servidor de diretório LDAP para adicionar autenticação multifator. Para a autenticação multifator de Azure a função, o servidor de autenticação multifator Azure deverá ser configurado para se comunicar com os servidores de cliente e o diretório LDAP. Nessa configuração, o servidor de autenticação multifator Azure aceita solicitações LDAP do cliente servidores e aplicativos e encaminha para o servidor de diretório LDAP de destino para validar as credenciais principais. Se a resposta do diretório LDAP mostra que primária credenciais são válidos, a autenticação multifator Azure executa a autenticação de fator de segundo e envia uma resposta de volta para o cliente LDAP. A autenticação inteira funcionará somente se a autenticação com o servidor LDAP e a autenticação multifator êxito.





## <a name="ldap-authentication-configuration"></a>Configuração de autenticação de LDAP


Para configurar a autenticação LDAP, instale o servidor de autenticação multifator Azure em um servidor Windows. Use o procedimento a seguir:

1. Dentro do servidor de autenticação multifator Azure clique no ícone de autenticação LDAP no menu à esquerda.
2. Marque a caixa de seleção Habilitar a autenticação do LDAP.![Autenticação LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Na guia clientes altere as portas TCP e SSL se o serviço LDAP de autenticação multifator Azure deve ligar para portas não padrão para escutar solicitações LDAP dos clientes que serão configurados.
4. Se você planeja usar LDAPS do cliente para o servidor de autenticação multifator Azure, um certificado SSL deve ser instalado no servidor que o servidor está executando. Clique em Procurar … botão próximo à caixa de certificado SSL e selecione o certificado instalado que será usado para a conexão segura.
5. Clique em Adicionar... botão.
6. Na caixa de diálogo Adicionar cliente LDAP, digite o endereço IP do dispositivo, servidor ou aplicativo que irá autenticar para o servidor e o nome de um aplicativo (opcional). O nome do aplicativo aparece em relatórios de autenticação multifator de Azure e pode ser exibido em mensagens de autenticação de SMS ou aplicativo móvel.
7. Marque a caixa de correspondência de usuário de exigir autenticação do Azure multifator se todos os usuários foram ou serão importados para o servidor e sujeitos a autenticação mutli fatores. Se um número significativo de usuários ainda não foram importado para o servidor e/ou estarão isento de autenticação de fator mutli, deixe a caixa desmarcada. Consulte o arquivo de ajuda para obter informações adicionais sobre este recurso.
8. Você pode repetir as etapas 5 a 7 para adicionar clientes LDAP adicionais.
9. Quando a autenticação multifator Azure está configurada para receber autenticação LDAP, ele deve proxy esses autenticação com o diretório LDAP. Portanto, a guia de destino somente exibe uma única, acinzentado opção para usar um destino LDAP. Para configurar a conexão de diretório LDAP, clique no ícone de integração de diretório.
10. Na guia Configurações, selecione o botão de rádio de configuração do específico LDAP do uso.
11. Clique no botão Editar... botão.
12. Na caixa de diálogo Editar configuração de LDAP, preencha os campos com as informações necessárias para se conectar com o diretório LDAP. Descrições dos campos são incluídas na tabela abaixo. Observação: Essas informações também estão incluídas no arquivo de Ajuda do servidor de autenticação multifator do Azure.![Integração de diretórios](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Teste a conexão LDAP clicando no botão de teste.
14. Se o teste de conexão LDAP foi bem-sucedido, clique no botão Okey.
15. Clique na guia filtros. O servidor é pré-configurada para carregar contêineres, usuários e grupos de segurança do Active Directory. Se associação a um diretório LDAP diferente, provavelmente você precisará editar os filtros exibidos. Clique no link de ajuda para obter mais informações sobre filtros.
16. Clique em guia atributos. O servidor é pré-configurada para mapear atributos do Active Directory.
17. Se vinculando a um diretório LDAP diferente ou alterar os mapeamentos de atributo pré-configurado, clique no botão Editar... botão.
18. Na caixa de diálogo Editar atributos, modifique os mapeamentos de atributo LDAP para seu diretório. Nomes de atributo podem ser digitados ou selecionados clicando na … botão ao lado de cada campo.
19. Clique no link de ajuda para obter mais informações sobre atributos.
20. Clique no botão Okey.
21. Clique no ícone configurações da empresa e selecione a guia de resolução de nome de usuário.
22. se conectar ao Active Directory de um servidor de domínio, você poderá deixar os identificadores de segurança (SIDs) usar o Windows para obter a correspondência de botão de opção de nomes de usuário selecionado. Caso contrário, selecione o atributo de identificador exclusivo LDAP de uso para o botão de rádio de nomes de usuário correspondente. Quando selecionada, o servidor de autenticação multifator Azure tentará resolver cada nome de usuário para um identificador exclusivo no diretório LDAP. Uma pesquisa LDAP será executada no nome de usuário atributos definidos na integração de diretório -> guia atributos. Quando um usuário autenticado, o nome de usuário será resolvido para o identificador exclusivo no diretório LDAP e o identificador exclusivo será usado para obter a correspondência de usuário no arquivo de dados do Azure multi-Factor Authentication. Isso permite comparações de maiusculas e minúsculas, como nome de usuário bem como abreviada e por extenso formatos isso completa a configuração de servidor de autenticação multifator do Azure. O servidor está agora listening nas portas configuradas para solicitações de acesso LDAP dos clientes do configurado e defina para proxy essas solicitações ao diretório LDAP para autenticação.


## <a name="ldap-client-configuration"></a>Configuração de cliente LDAP

Para configurar o cliente do LDAP, use as diretrizes:

- Configure seu dispositivo, o servidor ou o aplicativo para autenticar via LDAP no servidor de autenticação multifator Azure como se fosse seu diretório LDAP. Você deve usar as mesmas configurações que normalmente você usaria para conectar-se diretamente ao diretório LDAP, exceto para o nome do servidor ou o endereço IP que será que o servidor de autenticação multifator Azure.
- Configure o tempo limite de LDAP para 30 a 60 segundos para que há tempo para validar as credenciais do usuário com o diretório LDAP, executar a autenticação de fator de segundo, receberá sua resposta e responder a solicitação de acesso LDAP.
- Se usando LDAPS, do dispositivo ou do servidor tornando as consultas LDAP deve confiar o certificado SSL instalado no servidor de autenticação multifator do Azure.
