<properties
   pageTitle="Cenários e exemplos para gestão de assinatura | Microsoft Azure"
   description="Fornece exemplos de como implementar gestão de assinatura Azure para cenários comuns."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exemplos de implementação scaffold enterprise Azure

Este tópico fornece exemplos de como uma empresa pode implementar as recomendações para um [scaffold enterprise Azure](resource-manager-subscription-governance.md). Ele usa uma empresa fictícia chamada Contoso para ilustrar as práticas recomendadas para cenários comuns. 

## <a name="background"></a>Plano de fundo

Contoso é uma empresa em todo o mundo que fornece soluções de cadeia de fornecimento de clientes em tudo a partir de um modelo de "Software como um serviço" a um modelo de pacote implantado local.  Eles desenvolvem o software em todo o mundo com centros de desenvolvimento significativo na Índia, Estados Unidos e Canadá. 

A parte de ISV da empresa é dividida em várias unidades de negócios independentes que gerenciar produtos em uma empresa significativa. Cada unidade de negócios tem seu próprio desenvolvedores, gerentes de produto e arquitetos. 

A unidade de negócios de serviços de tecnologia da empresa (ETS) fornece recursos de TI centralizado e gerencia vários centros de dados onde as unidades de negócios hospedam seus aplicativos. Juntamente com Gerenciando os centros de dados, a organização ETS fornece e gerencia centralizada colaboração (como email e sites) e serviços de rede/telefonia. Eles também gerenciam cargas de trabalho de atendimento de unidades de negócios menores que não têm equipe operacional. 

As seguintes personas são usados neste tópico:

- Dave é o administrador do Azure ETS.
- Alice é diretor de desenvolvimento da Contoso na unidade de negócios de cadeia de suprimentos. 

Contoso precisa para criar um aplicativo de linha de negócios e um aplicativo de atendimento. Ele decidiu executar aplicativos no Azure. Dave lê o tópico de [Gestão de assinatura dirigidas](resource-manager-subscription-governance.md) e está pronto para implementar as recomendações. 

## <a name="scenario-1-line-of-business-application"></a>Cenário 1: aplicativo de linha de negócios

Contoso está criando um sistema de gerenciamento de código fonte (BitBucket) para ser usado por desenvolvedores em todo o mundo.  O aplicativo usa infraestrutura como um serviço (IaaS) para hospedar e consiste em servidores web e um servidor de banco de dados. Os desenvolvedores acessar servidores em seus ambientes de desenvolvimento, mas eles não precisam acessar os servidores no Azure. Contoso ETS deseja permitir que o proprietário do aplicativo e equipe gerenciar o aplicativo. O aplicativo só está disponível na rede corporativa da Contoso. Dave precisa configurar a assinatura para este aplicativo. A inscrição também hospedar outros softwares relacionados ao desenvolvedor no futuro.  

### <a name="naming-standards--resource-groups"></a>Padrões de nomenclatura & grupos de recursos

Dave cria uma assinatura para oferecer suporte a ferramentas de desenvolvedor que são comuns em todas as unidades de negócios. Ele precisa criar nomes significativos para os grupos de recursos e de assinatura (para o aplicativo e as redes). Ele cria os seguintes grupos de recursos e de assinatura:

| Item | Nome | Descrição |
| ---- | ---- | ----------- |
| Assinatura | Contoso ETS DeveloperTools produção | Suporta ferramentas de desenvolvedor comuns |
| Grupo de recursos | rgBitBucket | Contém o aplicativo servidor web e o servidor de banco de dados |
| Grupo de recursos | rgCoreNetworks | Contém as redes virtuais e conexão de gateway to-site |


### <a name="role-based-access-control"></a>Controle de acesso baseado em função

Depois de criar sua assinatura, Dave quer garantir que as equipes apropriadas e proprietários de aplicativo possam acessar seus recursos. Dave reconhece que cada equipe tem requisitos diferentes. Ele utiliza os grupos que foram sincronizados do Active Directory (AD) local da Contoso ao Azure Active Directory e fornece o nível de acesso para as equipes certo. 

Dave atribui funções a seguir para a assinatura: 

| Função | Atribuído a | Descrição |
| ---- | ----------- | ----------- |
| [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) | Gerenciado ID a partir da Contoso AD | Essa ID é controlada com Just no access Time (JIT) por meio da ferramenta de gerenciamento de identidades da Contoso e garante que o acesso de proprietário de assinatura é totalmente auditado. |
| [Gerenciador de segurança](./active-directory/role-based-access-built-in-roles.md#security-manager) | Departamento de gerenciamento de risco e segurança | Esta função permite aos usuários examinar a Central de segurança do Azure e o status dos recursos. |
| [Colaborador de rede](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Equipe de rede | Esta função permite que a equipe de rede da Contoso gerenciar a VPN de Site para Site e as redes virtuais. |
| *Função personalizada* | Proprietário do aplicativo | Dave cria uma função que concede a capacidade de modificar recursos no grupo de recursos. Para obter mais informações, consulte [Funções de personalizado no Azure RBAC](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Políticas

Dave tem os seguintes requisitos para gerenciar recursos na assinatura:

- Como as ferramentas de desenvolvimento dá suporte a desenvolvedores em todo o mundo, ele não deseja impedir que os usuários criando recursos em qualquer região. No entanto, ele precisa saber onde os recursos são criados. 
- Está preocupado com custos. Portanto, ele deseja impedir que os proprietários de aplicativo crie desnecessariamente caras máquinas virtuais.  
- Como este aplicativo serve desenvolvedores em várias unidades de negócios, ele deseja marcar cada recurso com o proprietário de unidade e aplicativo de negócios. Usando essas marcas, ETS pode bill as equipes apropriadas.

Ele cria as seguintes [políticas do Gerenciador de recursos](resource-manager-policy.md): 

| Campo | Efeito | Descrição |
| ----- | ------ | ----------- |
| local | auditoria | Auditar a criação dos recursos em qualquer região |
| tipo | Negar | Negar a criação de máquinas virtuais de série G |
| marcas | Negar | Exigir marca de proprietário de aplicativo |
| marcas | Negar | Exigir marca do Centro de custo |
| marcas | Acrescentar | Acrescentar o nome da marca **BusinessUnit** e marca valor **ETS** a todos os recursos |


### <a name="resource-tags"></a>Marcas de recurso

Dave entende que ele precisa ter informações específicas sobre a fatura para identificar o Centro de custos para a implementação de BitBucket. Além disso, Dave quer saber todos os recursos que possui ETS. 

Ele adiciona as seguintes [marcas](resource-group-using-tags.md) para os grupos de recursos e recursos. 
 
| Nome da marca | Valor de marca |
| -------- | --------- |
| ApplicationOwner | O nome da pessoa que gerencia este aplicativo. |
| CostCenter | O Centro de custos do grupo que está pagando para o consumo Azure. |
| BusinessUnit | **ETS** (a unidade de negócios associada a assinatura) |

### <a name="core-network"></a>Rede principal

Equipe de gerenciamento de Contoso ETS informações de segurança e risco revisa proposta planeja do Dave mover o aplicativo no Azure. Eles querem garantir que o aplicativo não é exposto à internet.  Dave também tem aplicativos de desenvolvedor que no futuro serão movidos para o Azure. Esses aplicativos exigem interfaces públicas.  Para atender a esses requisitos, ele fornece redes virtuais internas e externas e um grupo de segurança de rede para restringir o acesso.

Ele cria os seguintes recursos:

| Tipo de recurso | Nome | Descrição |
| ------------- | ---- | ----------- |
| Rede virtual | vnInternal | Usado com o aplicativo de BitBucket e está conectado por meio de rota expressa à rede corporativa da Contoso.  Uma sub-rede (sbBitBucket) fornece o aplicativo com um espaço de endereço IP específico. |
| Rede virtual | vnExternal | Disponível para futuros aplicativos que exigem pontos de extremidade do público. |
| Grupo de segurança de rede | nsgBitBucket | Garante que a superfície de ataque dessa carga de trabalho é minimizada permitindo conexões somente na porta 443 para a sub-rede onde o aplicativo reside (sbBitBucket). |

### <a name="resource-locks"></a>Bloqueios de recursos 

Dave reconhece que a conectividade de rede corporativa da Contoso à rede virtual interna deve ser protegida contra qualquer script wayward ou exclusões acidentais. 

Ele cria o seguinte [bloqueio de recurso](resource-group-lock-resources.md):

| Tipo de bloqueio | Recurso | Descrição |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Impede os usuários de excluir a rede virtual ou sub-redes, mas não impede que a adição de novas sub-redes. |

### <a name="azure-automation"></a>Automação Azure 

Dave tem nada para automatizar para este aplicativo. Embora ele criou uma conta de automação do Azure, ele não inicialmente usá-lo. 

### <a name="azure-security-center"></a>O Centro de segurança do Azure 

Gerenciamento de serviço de TI da Contoso precisa identificar e tratar ameaças rapidamente. Eles também queiram entender quais problemas podem existir.  

Para atender a esses requisitos, Dave habilita o [Centro de segurança do Azure](./security-center/security-center-intro.md)e fornece acesso à função Gerenciador de segurança. 

## <a name="scenario-2-customer-facing-app"></a>Cenário 2: voltados para o cliente do aplicativo

A liderança de negócios na unidade de negócios de cadeia de suprimentos identificou diversas oportunidades para aumentar o envolvimento com clientes da Contoso usando um cartão de fidelidade. Equipe de Alice deve criar este aplicativo e decide que Azure aumenta a capacidade de alcançar a necessidade de negócios. Alice funciona com Dave do ETS configurar duas assinaturas para desenvolver e operacional este aplicativo.

### <a name="azure-subscriptions"></a>Assinaturas do Azure 

Dave registra no portal do Enterprise do Azure e vê que o departamento de cadeia de suprimentos já existe.  No entanto, como este projeto é o primeiro projeto de desenvolvimento para a equipe de cadeia de fornecimento no Azure, Dave reconhece a necessidade de uma nova conta para a equipe de desenvolvimento de Alice.  Ele cria a conta "R & D" para sua equipe e atribui acesso para Alice. Alice logs na através do portal de conta e cria duas assinaturas: uma para reter os servidores de desenvolvimento e outra para manter os servidores de produção.  Ela segue os padrões de nomenclatura estabelecidos anteriormente ao criar as seguintes assinaturas: 

| Uso de assinatura | Nome |
| ---------------- | ---- |
| Desenvolvimento | SupplyChain ResearchDevelopment LoyaltyCard desenvolvimento |
| Produção | SupplyChain operações LoyaltyCard produção |

### <a name="policies"></a>Políticas

Dave e Alice discutam o aplicativo e identificam que este aplicativo serve apenas clientes na região da América do Norte.  Alice e sua equipe planejam usar o ambiente de serviço de aplicativo do Azure e SQL Azure para criar o aplicativo. Eles talvez precise criar máquinas virtuais durante o desenvolvimento.  Alice quer garantir que seu desenvolvedores têm os recursos necessários para explorar e examinar problemas sem retirando no ETS. 

Para a **assinatura de desenvolvimento**, eles criam a diretiva a seguir:

| Campo | Efeito | Descrição |
| ----- | ------ | ----------- |
| local | auditoria | Auditar a criação dos recursos em qualquer região. |

Eles não limitar o tipo de um usuário pode criar no desenvolvimento de sku e eles não requerem marcas para qualquer recursos ou grupos de recursos.

Para a **assinatura de produção**, eles criam as seguintes políticas:

| Campo | Efeito | Descrição |
| ----- | ------ | ----------- |
| local | Negar | Nega a criação dos recursos fora dos EUA data centers. |
| marcas | Negar | Exigir marca de proprietário de aplicativo |
| marcas | Negar | Exigir marca do departamento. | 
| marcas | Acrescentar | Acrescente marca a cada grupo de recursos que indica o ambiente de produção. |

Eles não limitar o tipo de sku que um usuário pode criar em produção.

### <a name="resource-tags"></a>Marcas de recurso 

Dave entende que ele precisa ter informações específicas para identificar os grupos de negócios correta de cobrança e de propriedade. Ele define marcas de recursos para grupos de recursos e recursos. 
 
Nome da marca | Valor de marca |
| -------- | --------- |
| ApplicationOwner | O nome da pessoa que gerencia este aplicativo. |
| Departamento | O Centro de custos do grupo que está pagando para o consumo Azure. |
| EnvironmentType | **Produção** (Mesmo que a assinatura inclui **produção** no nome, incluindo essa marca permite fácil identificação ao olhar no portal ou na lista de recursos.) |

### <a name="core-networks"></a>Redes centrais

Equipe de gerenciamento de Contoso ETS informações de segurança e risco revisa proposta planeja do Dave mover o aplicativo no Azure. Eles querem garantir que o aplicativo de cartão-fidelidade é isolado e protegido em uma rede DMZ corretamente.  Para atender esse requisito, Dave e Alice criar uma rede virtual externa e um grupo de segurança de rede para isolar o aplicativo de cartão-fidelidade da rede corporativa Contoso.  

Para a **assinatura de desenvolvimento**, eles criarem:

| Tipo de recurso | Nome | Descrição |
| ------------- | ---- | ----------- |
| Rede virtual | vnInternal | Serve o ambiente de desenvolvimento do cartão de fidelidade da Contoso e está conectada por meio de rota expressa à rede corporativa da Contoso. |

Para a **assinatura de produção**, eles criarem:

| Tipo de recurso | Nome | Descrição |
| ------------- | ---- | ----------- |
| Rede virtual | vnExternal | Hospeda o aplicativo de cartão-fidelidade e não está conectado diretamente a rota expressa da Contoso. Código é enviado por meio de seu sistema de código-fonte diretamente para os serviços de PaaS. |
| Grupo de segurança de rede | nsgBitBucket | Garante que a superfície de ataque dessa carga de trabalho é minimizada permitindo apenas no ligadas a comunicação em TCP 443.  Contoso é investigar também usando um Firewall de aplicativo da Web para proteção adicional. |  

### <a name="resource-locks"></a>Bloqueios de recursos 

Dave e Alice conferência e decidir adicionar bloqueios de recursos em alguns dos principais recursos no ambiente para impedir exclusões acidentais durante um envio de código errôneo. 

Eles criarem o bloqueio a seguir:

| Tipo de bloqueio | Recurso | Descrição |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | Para impedir que as pessoas excluindo a rede virtual ou sub-redes. O bloqueio não impede a adição de novas sub-redes. |

### <a name="azure-automation"></a>Automação Azure 

Alice e sua equipe de desenvolvimento têm runbooks considerável para gerenciar o ambiente para este aplicativo. Os runbooks permitem a adição/exclusão de nós para o aplicativo e outras tarefas de DevOps. 

Para usar esses runbooks, eles permitem [a automação](./automation/automation-intro.md).

### <a name="azure-security-center"></a>O Centro de segurança do Azure 

Gerenciamento de serviço de TI da Contoso precisa identificar e tratar ameaças rapidamente. Eles também queiram entender quais problemas podem existir.  

Para atender a esses requisitos, Dave habilita o Centro de segurança do Azure. Ele garante que o Centro de segurança do Azure está monitorando os recursos e fornece acesso às equipes de segurança e DevOps. 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de recursos, consulte [práticas recomendadas para a criação de modelos do Gerenciador de recursos do Azure](resource-manager-template-best-practices.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribuiu neste tópico.*
