<properties
    pageTitle="Serviços de Federação do Active Directory no Azure | Microsoft Azure"
    description="Neste documento, você aprenderá como implantar o AD FS no Azure para alta disponibilidade."
    keywords="implantar o AD FS no azure, implantar adfs azure, adfs azure, do azure ad fs, implantar adfs, implantar o ad fs, adfs no azure, implantar adfs no azure, implantar o AD FS no azure, adfs azure, introdução para o AD FS, o Azure, o AD FS no Azure, iaas, ADFS, mova adfs no azure"
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
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Implantação do AD FS no Azure 

AD FS fornece a federação de identidade simplificado, segura e recursos de logon único (SSO) da Web. A federação com o Azure AD ou Office 365 permite que os usuários autenticar usando credenciais locais e acessar todos os recursos na nuvem. Como resultado, é importante ter uma infraestrutura do AD FS altamente disponível para garantir que o acesso aos recursos de ambos os locais e na nuvem. Implantar o AD FS no Azure pode ajudar a alcançar a alta disponibilidade exigida com esforços mínimo.
Há várias vantagens de implantação do AD FS no Azure, algumas delas estão listadas abaixo:

* **Alta disponibilidade** - com o poder dos conjuntos de disponibilidade do Azure, você garantir uma infraestrutura altamente disponível.
* **Fácil de escala** – precisa de mais desempenho? Migrar facilmente para máquinas mais eficazes por apenas alguns cliques no Azure
* **Redundância de cruz geográfica** – com o Azure geográfica redundância você pode ter certeza de que sua infraestrutura é altamente disponível em todo o mundo
* **Fácil gerenciar** – com opções de gerenciamento altamente simplificado no portal do Azure, gerenciar sua infraestrutura é muito fácil e praticidade 

## <a name="design-principles"></a>Princípios de design

![Design de implantação](./media/active-directory-aadconnect-azure-adfs/deployment.png)

O diagrama acima mostra a topologia de básica recomendada para iniciar a implantação sua infraestrutura do AD FS no Azure. Os princípios em que os vários componentes da topologia estão listados abaixo:

* **DC / servidores ADFS**: se você tiver menos de 1.000 usuários você pode simplesmente instalar função AD FS nos controladores de domínio. Se você não deseja algum impacto no desempenho nos controladores de domínio ou se você tiver mais de 1.000 usuários, implante o AD FS em servidores separados.
* **Servidor de WAP** – é necessário implantar servidores Proxy de aplicativo Web, para que os usuários podem acessar o AD FS quando não estiverem na rede da empresa também.
* **DMZ**: servidores o Proxy de aplicativo Web serão colocados na DMZ e acesso somente TCP/443 é permitido entre DMZ e a sub-rede interna.
* **Balanceadores de carga**: para garantir alta disponibilidade dos servidores do AD FS e Proxy de aplicativo Web, recomendamos o uso de um balanceador de carga interna para servidores do AD FS e balanceador de carga do Azure para servidores Proxy de aplicativo Web.
* **Conjuntos de disponibilidade**: para fornecer redundância para sua implantação do AD FS, é recomendável que você agrupa dois ou mais máquinas virtuais em um conjunto de disponibilidade para cargas de trabalho semelhantes. Essa configuração garante que durante seja um evento de manutenção planejadas ou, pelo menos uma máquina virtual estará disponível
* **Contas de armazenamento**: é recomendável ter duas contas de armazenamento. Ter uma conta de armazenamento único pode levar a criação de um único ponto de falha e pode causar a implantação indisponível em um cenário provável onde a conta de armazenamento diminui. Duas contas de armazenamento ajudará a associar uma conta de armazenamento para cada linha de falhas.
* **Diferenciação de rede**: servidores Proxy de aplicativo Web devem ser implantados em uma rede DMZ separada. Você pode dividir uma rede virtual em duas sub-redes e implantar o servidor (es) Proxy de aplicativo Web em uma sub-rede isolada. Você pode simplesmente definir as configurações de grupo de segurança de rede para cada sub-rede e permitir apenas a comunicação necessária entre as duas sub-redes. Mais detalhes recebem por cenário de implantação abaixo

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Etapas para implantar o AD FS no Azure

As etapas mencionadas nesta seção descrevem o guia para implantar o abaixo descreveu infraestrutura do AD FS no Azure.

### <a name="1-deploying-the-network"></a>1. Implantando da rede

Como descrito acima, você pode tanto criar duas sub-redes em uma única rede virtual senão criar duas redes virtuais completamente diferentes (VNet). Este artigo irá enfocar Implantando uma única rede virtual e dividi-la em duas sub-redes. Atualmente, isso é uma abordagem mais fácil como duas VNets separadas exijam uma VNet VNet gateway para comunicações.

**1.1 Criar rede virtual**

![Criar rede virtual](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
No portal do Azure, selecione rede virtual e você pode implantar a rede virtual e uma sub-rede imediatamente com apenas um clique. Sub-rede INT também é definido e está pronto para VMs a ser adicionado.
A próxima etapa é adicionar outra sub-rede à rede, ou seja, sub-rede DMZ. Criar sub-rede DMZ, simplesmente

* Selecione a rede recém-criado
* Nas propriedades selecionar sub-rede
* Na sub-rede painel clique no botão Adicionar
* Forneça as sub-rede nome e endereço espaço informações para criar a sub-rede

![Sub-rede](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Sub-rede DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. criando a rede de grupos de segurança**

Um grupo de segurança de rede (NSG) contém uma lista de regras de lista de controle de acesso (ACL) que permitir ou negar o tráfego de rede para suas instâncias de máquina virtual em uma rede Virtual. NSGs podem ser associadas a sub-redes ou instâncias individuais de máquina virtual dentro daquela sub-rede. Quando um NSG está associado uma sub-rede, as regras ACL se aplicam a todas as instâncias de máquina virtual nessa sub-rede.
Para este guia, vamos criar dois NSGs: um para uma rede interna e DMZ. Eles serão rotulados NSG_INT e NSG_DMZ respectivamente.

![Criar NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Após a NSG é criado, haverá 0 regras de saída de entrada e 0. Depois que as funções nos respectivos servidores estão instaladas e funcional, em seguida, as regras de entrada e saídas podem ser feitas de acordo com o nível de segurança desejado.

![Inicializar NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Após as NSGs são criados, associar NSG_INT sub-rede INT e NSG_DMZ com sub-rede DMZ. Uma captura de tela de exemplo é fornecida a seguir:

![Configurar NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Clique em sub-redes para abrir o painel para sub-redes
* Selecione a sub-rede para associar o NSG 

Após a configuração, o painel para sub-redes deve aparência abaixo:

![Sub-redes após NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Crie a Conexão para o local**

Teremos uma conexão para o local para implantar o controlador de domínio (DC) no azure. Azure oferece várias opções de conectividade para se conectar a sua infraestrutura de local à sua infraestrutura Azure.

* Ponto-a-site
* To-site da rede virtual
* Rota expressa

É recomendável usar rota expressa. Rota expressa permite que você crie conexões privadas entre dos data centers do Azure e infraestrutura que está em seu local ou em um ambiente de localização conjunta. Rota expressa conexões não vá na Internet pública. Eles oferecem mais confiabilidade, rapidez, latências menores e maior segurança que conexões típicas pela Internet.
Embora seja recomendável usar rota expressa, você pode escolher qualquer método de conexão são mais adequado para sua organização. Para saber mais sobre rota expressa e as diversas opções de conectividade usando rota expressa, leia [Visão geral técnica de rota expressa](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Crie contas de armazenamento

Para manter a alta disponibilidade e evitar depender de uma conta de armazenamento única, você pode criar duas contas de armazenamento. Dividir as máquinas em cada conjunto de disponibilidade em dois grupos e atribua cada grupo de uma conta de armazenamento separada.

![Criar contas de armazenamento](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. criar conjuntos de disponibilidade

Para cada função (DC/AD FS e WAP), crie conjuntos de disponibilidade que conterão 2 máquinas cada no mínimo. Isso ajudará a obter maior disponibilidade para cada função. Embora criando a disponibilidade conjuntos, é essencial para decidir sobre o seguinte:
* **Domínios de falha**: máquinas virtuais no mesmo domínio falhas compartilham a mesma fonte de energia e mudança de rede física. Um mínimo de 2 domínios de falha são recomendados. O valor padrão é 3 e você pode deixá-la como está para essa implantação
* **Atualizar domínios**: máquinas pertencentes ao mesmo domínio atualização são reiniciadas juntas durante uma atualização. Você deseja ter mínimo de 2 domínios de atualização. O valor padrão é 5 e você pode deixá-la como se destina a essa implantação

![Conjuntos de disponibilidade](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Criar os seguintes conjuntos de disponibilidade

| Conjunto de disponibilidade | Função | Domínios de falha | Atualizar domínios |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | DC/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. implantar máquinas virtuais
A próxima etapa é implantar máquinas virtuais que hospedam as funções diferentes em sua infraestrutura. Um mínimo de duas máquinas são recomendados em cada conjunto de disponibilidade. Crie seis máquinas virtuais para a implantação básica.

| Máquina | Função | Sub-rede | Conjunto de disponibilidade | Conta de armazenamento | Endereço IP |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|DC/ADFS|INT|contosodcset|contososac1|Estático|
|contosodc2|DC/ADFS|INT|contosodcset|contososac2|Estático|
|contosowap1|WAP|DMZ|contosowapset|contososac1|Estático|
|contosowap2|WAP|DMZ|contosowapset|contososac2|Estático|

Como você deve ter notado, nenhum NSG foi especificado. Isso ocorre porque o azure permite que você use NSG no nível de sub-rede. Em seguida, você pode controlar o tráfego de rede de máquina usando o NSG individual associado tanto a sub-rede senão o objeto NIC. Leia sobre mais [o que é um grupo de segurança de rede (NSG)](https://aka.ms/Azure/NSG).
Endereço IP estático é recomendável se você estiver gerenciando o DNS. Você pode usar o DNS do Azure e em vez nos registros de DNS do seu domínio, consulte as novas máquinas por seus FQDNs Azure.
Seu painel de máquina virtual deve aparência abaixo, depois que a implantação for concluída:

![Máquinas virtuais implantadas](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. configurar o controlador de domínio / servidores do AD FS
 Para autenticar qualquer solicitação de entrada, será necessário contatar o controlador de domínio do AD FS. Para salvar a viagem cara do Azure DC local para autenticação, é recomendável para implantar uma réplica do controlador de domínio no Azure. Para obter alta disponibilidade, é recomendável para criar um conjunto de disponibilidade pelo menos 2 controladores de domínio.

|Controlador de domínio|Função|Conta de armazenamento|
|:-----:|:-----:|:-----:|
|contosodc1|Réplica|contososac1|
|contosodc2|Réplica|contososac2|

* Promover os dois servidores como controladores de domínio de réplica com DNS
* Configure os servidores do AD FS instalando a função do AD FS usando o Gerenciador de servidor.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. Implantando balanceador de carga interno (ILB)

**6.1. Crie o ILB**

Para implantar um ILB, selecione balanceadores de carga no portal do Azure e clique em Adicionar (+).
>[AZURE.NOTE] Se você não vir **Balanceadores de carga** no menu, clique em **Procurar** no canto inferior esquerdo do portal e role até ver **Balanceadores de carga**.  Em seguida, clique na estrela amarela para adicioná-lo ao menu. Agora, selecione o novo ícone de Balanceador de carga para abrir o painel para começar a configuração de Balanceador de carga.

![Procurar balanceador de carga](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nome**: nomeá adequado ao balanceador de carga
* **Esquema**: como este balanceador de carga será colocado na frente de servidores do AD FS e destina-se a para conexões de rede interna apenas, selecione "Interno"
* **Rede virtual**: escolha a rede virtual onde você está implantando o AD FS
* **Sub-rede**: escolha a sub-rede interna aqui
* **Atribuição de endereço IP**: dinâmico

![Balanceador de carga interno](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Depois de clicar em criar e o ILB for implantado, deve aparecer na lista de balanceadores de carga:

![Balanceadores de carga após ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Próxima etapa é configurar o pool de back-end e o teste de back-end.

**6.2. Configurar pool de back-end do ILB**

Selecione o ILB recém-criado no painel balanceadores de carga. Ele será aberto o painel de configurações. 
1.  Selecione pools de back-end no painel de configurações
2.  No painel Adicionar back-end pool, clique na máquina virtual de adicionar
3.  Você receberá um painel onde você pode escolher o conjunto de disponibilidade
4.  Escolha o conjunto de disponibilidade do AD FS

![Configurar pool de back-end do ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. Configurando teste**

No painel de configurações ILB, selecione testes.
1.  Clique em Adicionar
2.  Forneça detalhes de teste uma. **Nome**: teste b de nome. **Protocolo**: TCP c. **Porta**: 443 (HTTPS) d. **Intervalo**: 5 (valor padrão) – Este é o intervalo no qual ILB serão teste as máquinas do pool de back-end e. **Limite não íntegra**: 2 (padrão val ue) – Este é o limite de falhas de teste consecutivas após o qual ILB irá declarar uma máquina do pool de back-end não responda e interrompa enviando o tráfego para ele.

![Configurar o teste ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. Crie regras de balanceamento de carga**

Para conferir efetivamente o tráfego, o ILB deve ser configurado com regras de balanceamento de carga. Para criar uma regra, balanceamento de carga 
1.  Selecione a regra a partir do painel de configurações da ILB balanceamento de carga
2.  Clique em Adicionar no painel de regra de balanceamento de carga
3.  No painel de regra de balanceamento de carga de adicionar um. **Nome**: forneça um nome para a regra b. **Protocolo**: selecione TCP c. **Porta**: 443 d. **Porta de back-end**: 443 e. **Pool de back-end**: selecione o pool criado para o AD FS cluster f anterior. **Teste**: selecione o teste criado anteriormente para servidores do AD FS

![Configurar regras de balanceamento de ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. atualizar DNS com ILB**

Vá para seu servidor DNS e criar um CNAME para o ILB. O CNAME deve ser do serviço de federação com o endereço IP apontando para o endereço IP da ILB. Por exemplo, se o endereço de ILB DIP for 10.3.0.8 e o serviço de Federação instalado for fs.contoso.com, em seguida, crie um CNAME para fs.contoso.com apontando para 10.3.0.8.
Isso garante que todas as comunicações com relação ao final de fs.contoso.com cima na ILB e são roteadas adequadamente.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. Configurando o servidor de Proxy de aplicativo Web

**7.1. Configurando os servidores Proxy de aplicativo Web para acessar servidores do AD FS**

Para garantir que os servidores Proxy de aplicativo Web sejam capazes de atingir os servidores do AD FS atrás a ILB, crie um registro no %systemroot%\system32\drivers\etc\hosts para o ILB. Observe que o nome diferenciado (DN) deve ser o nome de serviço de federação, por exemplo fs.contoso.com. E a entrada IP deve ser do endereço IP do ILB (10.3.0.8 como no exemplo).

**7.2. instalando a função de Proxy de aplicativo Web**

Depois de você garantir que os servidores Proxy de aplicativo Web sejam capazes de atingir os servidores do AD FS atrás ILB, você pode, em seguida, instale os servidores Proxy de aplicativo Web. Servidores Proxy de aplicativo da Web não ser associados ao domínio. Instale as funções de Proxy de aplicativo Web nos dois servidores Proxy de aplicativo Web, selecionando a função de acesso remoto. O Gerenciador de servidor o guiará para concluir a instalação de WAP.
Para obter mais informações sobre como implantar WAP, leia [instalar e configurar o servidor de Proxy de aplicativo da Web](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. Implantando Internet opostas balanceador de carga (público)

**8.1. Crie Internet opostas balanceador de carga (público)**
 
No portal do Azure, selecione balanceadores de carga e clique em Adicionar. No painel de Balanceador de carga de criar, insira as seguintes informações
1. **Nome**: nome para o balanceador de carga
2. **Esquema**: público – essa opção informa Azure que este balanceador de carga precisará de um endereço público.
3. **Endereço IP**: criar um novo endereço IP (dinâmico)

![Balanceador de carga oposta da Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Após a implantação, o balanceador de carga aparecerão na lista de balanceadores de carga.

![Lista de Balanceador de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. atribuir um rótulo DNS para o IP público**

Clique na entrada do balanceador de carga recém-criado no painel balanceadores de carga para exibir o painel de configuração. Execute as etapas a seguir para configurar o rótulo DNS para o IP público:
1.  Clique no endereço IP público. Isso abrirá o painel para o IP público e suas configurações
2.  Clique em configuração
3.  Fornece um rótulo DNS. Isso tornará o rótulo DNS público que você pode acessar de qualquer lugar, por exemplo contosofs.westus.cloudapp.azure.com. Você pode adicionar uma entrada no DNS externo para o serviço de Federação (como fs.contoso.com) que resolve para o rótulo DNS de Balanceador de carga externos (contosofs.westus.cloudapp.azure.com).

![Configurar a internet opostas balanceador de carga](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurar a internet opostas balanceador de carga (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. configurar o pool de back-end para balanceador de carga de Internet opostas (público)** 

Siga as mesmas etapas criando balanceador de carga interno, para configurar o pool de back-end para Internet opostas balanceador de carga (pública) como a disponibilidade definida para os servidores WAP. Por exemplo, contosowapset.

![Configurar o pool de back-end de Balanceador de carga opostas da Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8,4. Configurar teste**

Siga as mesmas etapas Configurando o balanceador de carga interno para configurar o teste para o pool de back-end de servidores WAP.

![Configurar o teste de Balanceador de carga opostas da Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5. Crie regras de balanceamento de carga**

Siga as mesmas etapas ILB para configurar a balanceamento de carga regra para TCP 443.

![Configurar regras de balanceamento de Balanceador de carga opostas da Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. segurança da rede

**9.1. protegendo a sub-rede interna**

Em geral, você precisa as seguintes regras para proteger com eficiência sua sub-rede interna (na ordem de conforme listados abaixo)

|Regra|Descrição|Fluxo|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Permitir a comunicação HTTPS de DMZ | Entrada |
|DenyInternetOutbound| Sem acesso à internet | Saída |

![Regras de acesso INT (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[comentário]: <> (![as regras de acesso INT (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [comentário]: <> (![regras de acesso INT (saídas)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. protegendo sub-rede DMZ**

|Regra|Descrição|Fluxo|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Permitir HTTPS da internet o DMZ | Entrada|
|DenyInternetOutbound|  Nada, exceto HTTPS para internet é bloqueado | Saída |

![Regras de acesso EXT (de entrada)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[comentário]: <> (![as regras de acesso EXT (entrada)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [comentário]: <> (![regras de acesso EXT (saídas)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Se a autenticação de certificado de usuário do cliente (autenticação de clientTLS usando X509 certificados de usuário) for necessário, o AD FS requer TCP porta 49443 ser habilitada para acesso de entrada.

###<a name="10--test-the-ad-fs-sign-in"></a>10. testar a entrada do AD FS

A maneira mais fácil é testar o AD FS, usando a página de IdpInitiatedSignon.aspx. Para poder fazer o que, é necessário para habilitar o IdpInitiatedSignOn nas propriedades do AD FS. Siga as etapas abaixo para verificar a instalação do AD FS
1.  Executar o abaixo cmdlet no servidor do AD FS, usando o PowerShell, defini-lo para ativado.
    Set-AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  De qualquer máquina externos acesso https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3.  Você verá a página do AD FS, como abaixo:

![Página de login de teste](./media/active-directory-aadconnect-azure-adfs/test1.png)

Ao entrar com êxito, ele oferecerá com uma mensagem de sucesso conforme mostrado abaixo:

![Testar o sucesso](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modelo de implantação do AD FS no Azure

O modelo implanta uma configuração de 6 máquina, 2 controladores de domínio, o AD FS e WAP.

[AD FS no modelo de implantação do Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Você pode usar uma rede virtual existente ou criar um novo VNET ao implantar este modelo. Os diversos parâmetros disponíveis para personalizar a implantação estão listados abaixo com a descrição do uso do parâmetro no processo de implantação. 

| Parâmetro | Descrição |
|:--------|:-----|
|Local| A região para implantar os recursos para, por exemplo, Leste conosco. |
|StorageAccountType| O tipo da conta de armazenamento criou|
|VirtualNetworkUsage| Indica se uma nova rede virtual será criado ou use um existente|
|VirtualNetworkName| O nome da rede Virtual para criar, obrigatório em uso de rede virtual novo ou existente|
|VirtualNetworkResourceGroupName| Especifica o nome do grupo de recursos onde reside a rede virtual existente. Ao usar uma rede virtual existente, isso se torna um parâmetro obrigatório para a implantação possa localizar a ID da rede virtual existente|
|VirtualNetworkAddressRange| O intervalo de endereço do novo VNET, obrigatório se criando uma nova rede virtual|
|InternalSubnetName| O nome da sub-rede interno, obrigatório em ambas as opções de uso de rede virtual (novas ou existentes)|
|InternalSubnetAddressRange| O intervalo de endereços da sub-rede interna, que contém os servidores controladores de domínio e ADFS, obrigatórios se criando uma nova rede virtual.|
|DMZSubnetAddressRange| O intervalo de endereços da sub-rede dmz, que contém os Windows aplicativo servidores proxy, obrigatórios se criando uma nova rede virtual.|
|DMZSubnetName| O nome da sub-rede interno, obrigatório em ambas as opções de uso de rede virtual (novas ou existentes). |
|ADDC01NICIPAddress| O endereço IP interno do primeiro controlador de domínio, esse endereço IP estática obtiverem ao DC e deve ser um endereço ip válido dentro da sub-rede interno|
|ADDC02NICIPAddress| O endereço IP interno do segundo controlador de domínio, esse endereço IP estática obtiverem ao DC e deve ser um endereço ip válido dentro da sub-rede interno|
|ADFS01NICIPAddress| O endereço IP interno do primeiro servidor ADFS, esse endereço IP será atribuído estática para o servidor ADFS e deve ser um endereço ip válido dentro da sub-rede interno|
|ADFS02NICIPAddress| O endereço IP interno do segundo servidor ADFS, esse endereço IP será atribuído estática para o servidor ADFS e deve ser um endereço ip válido dentro da sub-rede interno|
|WAP01NICIPAddress| O endereço IP interno do primeiro servidor WAP, esse endereço IP estática obtiverem no servidor WAP e deve ser um endereço ip válido dentro da sub-rede DMZ|
|WAP02NICIPAddress| O endereço IP interno do segundo servidor WAP, esse endereço IP estática obtiverem no servidor WAP e deve ser um endereço ip válido dentro da sub-rede DMZ|
|ADFSLoadBalancerPrivateIPAddress| O endereço IP interno do balanceador de carga ADFS, esse endereço IP estática obtiverem ao balanceador de carga e deve ser um endereço ip válido dentro da sub-rede interno|
|ADDCVMNamePrefix| Prefixo de nome de máquina virtual para controladores de domínio|
|ADFSVMNamePrefix| Prefixo de nome de máquina virtual para servidores ADFS|
|WAPVMNamePrefix| Prefixo de nome de máquina virtual para servidores WAP|
|ADDCVMSize| O tamanho de máquina virtual dos controladores de domínio|
|ADFSVMSize| O tamanho de máquina virtual dos servidores ADFS|
|WAPVMSize| O tamanho de máquina virtual dos servidores WAP|
|AdminUserName| O nome do administrador local das máquinas virtuais|
|AdminPassword| A senha da conta administrador local das máquinas virtuais|

## <a name="additional-resources"></a>Recursos adicionais
* [Conjuntos de disponibilidade](https://aka.ms/Azure/Availability ) 
* [Balanceador de carga Azure](https://aka.ms/Azure/ILB)
* [Balanceador de carga interno](https://aka.ms/Azure/ILB/Internal)
* [Balanceador de carga de voltado para a Internet](https://aka.ms/Azure/ILB/Internet)
* [Contas de armazenamento](https://aka.ms/Azure/Storage )
* [Redes virtuais Azure](https://aka.ms/Azure/VNet)
* [AD FS e Links de Proxy de aplicativo Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Próximas etapas

* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
* [Configurar e gerenciar o AD FS usando Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Implantação do alta disponibilidade entre geográfica AD FS no Azure com o Gerenciador de tráfego do Azure](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




