<properties
    pageTitle="Implantação do alta disponibilidade entre geográficos AD FS no Azure com o Gerenciador de tráfego do Azure | Microsoft Azure"
    description="Neste documento, você aprenderá como implantar o AD FS no Azure para alta disponibilidade."
    keywords="O AD fs com o Gerenciador de tráfego Azure, adfs com Gerenciador de tráfego Azure, geográficos, data center multi, centros de dados geográficos, vários centros de dados geográficos, implantar o AD FS no azure, implantar adfs azure, adfs azure, do azure ad fs, implantar adfs, implantar o ad fs, adfs no azure, implantar adfs no azure, implantar o AD FS no azure, adfs azure, introdução para o AD FS, o Azure, o AD FS no Azure, iaas , ADFS, mover adfs no azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="anandy;billmath"/>
    
#<a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Implantação do alta disponibilidade entre fronteiras geográficas AD FS no Azure com o Gerenciador de tráfego do Azure

[A implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) fornece orientação passo a passo sobre como você pode implantar uma infraestrutura do AD FS simple para sua organização no Azure. Este artigo fornece as próximas etapas para criar uma implantação entre fronteiras geográficas do AD FS no Azure usando o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md). Gerenciador de tráfego Azure ajuda a criar uma visualização geograficamente alta disponibilidade e a infraestrutura do AD FS de alto desempenho para sua organização fazendo uso do intervalo de métodos de roteamento disponíveis para atender às necessidades diferentes em relação a infraestrutura.

Permite que uma infraestrutura do AD FS entre geográficos altamente disponível:

* **Eliminação de ponto único de falha:** Com recursos de failover do Gerenciador de tráfego do Azure, você pode obter uma infraestrutura do AD FS altamente disponível mesmo quando um dos data centers em uma parte do globo desce
* **Desempenho aprimorado:** Você pode usar a implantação sugerida neste artigo para fornecer uma infraestrutura do AD FS de alto desempenho que pode ajudar os usuários a autenticar com mais rapidez. 

##<a name="design-principles"></a>Princípios de design

![Design geral](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Os princípios básicos de design será o mesmos conforme listado no Princípios de Design no artigo implantação do AD FS no Azure. O diagrama acima mostra uma simples extensão da implantação básica para outra área geográfica. Abaixo estão alguns pontos a serem consideradas ao estender sua implantação para nova região geográfica

* **Rede Virtual:** Você deve criar uma nova rede virtual na região geográfica que deseja implantar a infraestrutura adicional do AD FS. No diagrama acima você vê Geo1 VNET e Geo2 VNET como as duas redes virtuais em cada área geográfica.

* **Controladores de domínio e servidores do AD FS no novo VNET geográfica:** É recomendável para implantar o domínio controladores na nova região geográfica para que os servidores do AD FS na nova região não têm contatar um controlador de domínio em outro distante de rede para completar uma autenticação e, portanto, melhorar o desempenho.

* **Contas de armazenamento:** Contas de armazenamento estão associadas uma região. Como você vai implantar máquinas em uma nova região geográfica, você terá que criar novas contas de armazenamento a ser usado na região.  

* **Grupos de segurança de rede:** Como contas de armazenamento, grupos de segurança de rede criados em uma região não podem ser usadas em outra região geográfica. Portanto, você precisará criar do nova rede grupos de segurança semelhantes aos perfis em primeira região geográfica para sub-rede INT e DMZ na nova região geográfica.

* **Etiquetas de DNS para endereços IP públicos:** Gerenciador de tráfego Azure pode se referir a pontos de extremidade somente por meio de rótulos DNS. Portanto, são necessárias para criar etiquetas DNS para os endereços IP públicos dos balanceadores de carga externos.

* **Gerenciador de tráfego azure:** Gerenciador de tráfego do Microsoft Azure permite controlar a distribuição de tráfego de usuário para seus pontos de extremidade do serviço em execução em diferentes dos data centers em todo o mundo. Gerenciador de tráfego Azure funciona no nível do DNS. Ele usa respostas DNS para direcionar o tráfego de usuário final para pontos de extremidade distribuídas globalmente. Clientes, em seguida, se conectar a esses pontos de extremidade diretamente. Com opções de roteamento diferentes de desempenho, ponderado e prioridade, você pode escolher facilmente a opção de roteamento são mais adequada para as necessidades da sua organização. 

* **V-líquido para V-líquido conectividade entre duas regiões:** Não é necessário ter conectividade entre as redes virtuais em si. Como cada rede virtual tem acesso a controladores de domínio e tem o servidor do AD FS e WAP em si, eles podem trabalhar sem qualquer conectividade entre as redes virtuais em diferentes regiões. 

##<a name="steps-to-integrate-azure-traffic-manager"></a>Etapas para integrar o Gerenciador de tráfego do Azure

###<a name="deploy-ad-fs-in-the-new-geographical-region"></a>Implantar o AD FS na nova região geográfica
Siga as etapas e diretrizes na [implantação do AD FS no Azure](active-directory-aadconnect-azure-adfs.md) para implantar a mesma topologia na nova região geográfica.

###<a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Etiquetas DNS para endereços IP públicos dos balanceadores de carga Internet opostas (público)
Conforme mencionado acima, o Gerenciador de tráfego Azure só pode se referir rótulos DNS como pontos de extremidade e, portanto, é importante criar etiquetas DNS para os endereços IP públicos dos balanceadores de carga externos. Abaixo de captura de tela mostra como você pode configurar seu rótulo DNS para o endereço IP público. 

![Rótulo DNS](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

###<a name="deploying-azure-traffic-manager"></a>Implantando o Gerenciador de tráfego Azure

Siga as etapas abaixo para criar um perfil do Gerenciador de tráfego. Para obter mais informações, você também pode consultar a [Gerenciar um perfil de Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Criar um perfil do Gerenciador de tráfego:** Dê um nome exclusivo para o perfil do Gerenciador de tráfego. Esse nome do perfil é parte do nome do DNS e atua como um prefixo para o rótulo de nome de domínio do Gerenciador de tráfego. O nome / prefixo é adicionado à. trafficmanager.net para criar um rótulo DNS para o tráfego de seu gerente. Captura de tela abaixo mostra o Gerenciador de tráfego prefixo DNS sendo definido como mysts e rótulo DNS resultante será mysts.trafficmanager.net. 

    ![Criação de perfil do Gerenciador de tráfego](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
 
2. **Método de roteamento de tráfego:** Há três opções de roteamento disponíveis no Gerenciador de tráfego:

    * Prioridade 
    * Desempenho
    * Ponderada
    
    **Desempenho** é a opção recomendada para atingir altamente ágil infraestrutura do AD FS. No entanto, você pode escolher qualquer método de roteamento são mais adequado para suas necessidades de implantação. A funcionalidade do AD FS não é afetada pela opção de roteamento selecionada. Consulte [métodos de roteamento de tráfego do Gerenciador de tráfego](../traffic-manager/traffic-manager-routing-methods.md) para obter mais informações. No exemplo de captura de tela acima que você pode ver o método de **desempenho** selecionado.
   
3.  **Configurar pontos de extremidade:** Na página do Gerenciador de tráfego, clique em pontos de extremidade e selecione Adicionar. Isso abrirá uma página de ponto de extremidade de adicionar semelhante a captura de tela abaixo
 
    ![Configurar pontos de extremidade](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
 
    Para as entradas diferentes, siga as diretrizes abaixo:

    **Tipo:** Selecione o ponto de extremidade Azure como podemos será estar apontando para um endereço IP público Azure.

    **Nome:** Crie um nome que você deseja associar o ponto de extremidade. Isso não é o nome DNS e não possui registros de DNS.

    **Tipo de recurso de destino:** Selecione o endereço IP público como o valor a essa propriedade. 

    **Recurso de destino:** Isso lhe dará uma opção para escolher os rótulos DNS diferentes que têm disponíveis em sua assinatura. Escolha o rótulo DNS para a.

    Adicione o ponto de extremidade para cada área geográfica que deseja que o Gerenciador de tráfego do Azure para rotear o tráfego para.
    Para obter mais informações e etapas detalhadas sobre como adicionar / configurar pontos de extremidade no Gerenciador de tráfego, consulte [Adicionar, desativar, ativar ou excluir pontos de extremidade](../traffic-manager/traffic-manager-endpoints.md)
    
4. **Configurar teste:** Na página do Gerenciador de tráfego, clique na configuração. Na página de configuração, você precisa alterar as configurações do monitor para teste na porta 80 HTTP e /adfs/probe de caminho relativo

    ![Configurar o teste](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 

    >[AZURE.NOTE] **Certifique-se de que o status dos pontos de extremidade for ONLINE após a configuração está concluída**. Se todos os pontos de extremidade estão em estado 'degradado', o Gerenciador de tráfego do Azure fará uma tentativa de melhor para rotear o tráfego presumindo que o diagnóstico estiver incorreto e todos os pontos de extremidade estão acessíveis.

5. **Registros de DNS modificando Gerenciador de tráfego de Azure:** Seu serviço de federação deve ser um CNAME para o nome do Azure tráfego Gerenciador DNS. Crie um CNAME nos registros de DNS públicos para que realmente quem está tentando alcançar o serviço de Federação atinge o Gerenciador de tráfego do Azure.

    Por exemplo, para apontar o fs.fabidentity.com de serviço de Federação ao Gerenciador de tráfego, que você precisa atualizar seu registro de recurso DNS para ser a seguinte:

    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

##<a name="test-the-routing-and-ad-fs-sign-in"></a>Testar o roteamento e entrar do AD FS   

###<a name="routing-test"></a>Roteamento de teste

Um teste básico para o roteamento seria tentar ping o nome DNS do serviço de federação com uma máquina em cada região geográfica. Dependendo do método de roteamento escolhido, o ponto de extremidade realmente executa ping será refletido na exibição ping. Por exemplo, se você selecionou o roteamento de desempenho, em seguida, o ponto de extremidade mais próxima a região do cliente seja atingido. Abaixo está o instantâneo do duas ping de duas máquinas de clientes de região diferente, um na região de EastAsia e um Oeste EUA. 

![Roteamento de teste](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

###<a name="ad-fs-sign-in-test"></a>AD FS entrar teste

A maneira mais fácil para testar o AD FS é usando a página de IdpInitiatedSignon.aspx. Para poder fazer o que, é necessário para habilitar o IdpInitiatedSignOn nas propriedades do AD FS. Siga as etapas abaixo para verificar a instalação do AD FS
 
1. Executar o abaixo cmdlet no servidor do AD FS, usando o PowerShell, defini-lo para ativado. Set-AdfsProperties - EnableIdPInitiatedSignonPage $true
2. De qualquer máquina externos acesso https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Você verá a página do AD FS, como abaixo:

    ![Teste do ADFS - desafio de autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)

    e ao entrar bem-sucedida, ele fornecerá uma mensagem de sucesso conforme mostrado abaixo:

    ![Teste do ADFS - sucesso de autenticação](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)
 
##<a name="related-links"></a>Links relacionados
* [Implantação básica do AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
* [Gerenciador de tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
* [Métodos de roteamento de tráfego do Gerenciador de tráfego](../traffic-manager/traffic-manager-routing-methods.md)

##<a name="next-steps"></a>Próximas etapas
* [Gerenciar um perfil de Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-manage-profiles.md)
* [Adicionar, desativar, ativar ou excluir pontos de extremidade](../traffic-manager/traffic-manager-endpoints.md) 

