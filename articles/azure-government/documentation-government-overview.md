<properties
    pageTitle="Documentação do governo Azure | Microsoft Azure"
    description="Isso fornece uma comparação de recursos e orientações sobre como desenvolver aplicativos para o governo do Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="08/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-documentation-overview"></a>Visão geral da documentação governo Azure

##  <a name="introduction-to-azure-government-documentation"></a>Introdução à documentação do governo Azure

Este site descreve os recursos dos serviços do [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e fornece orientações gerais aplicáveis a todos os clientes. Antes de incluir especificamente regulamentados dados em sua assinatura do governo do Azure, você deve se familiarizar com os recursos do governo do Azure e consulte a sua equipe de conta se você tiver dúvidas.

Você deve fazer referência à [Página de conformidade de Central de confiabilidade do Microsoft Azure](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx) para obter informações atuais sobre os serviços do governo do Azure coberto normas e referências específicas. Serviços Microsoft adicionais também podem estar disponíveis, mas não estão dentro do escopo dos serviços do governo do Azure cobertos e não são abordados por este documento. Serviços do Azure governamentais podem também permitir que você use uma variedade de recursos adicionais, aplicativos ou serviços que são fornecidos por terceiros — ou pela Microsoft sob termos separados de políticas de uso e privacidade — que não estão incluídos no escopo deste documento. Você é responsável por revisar os termos de todas essas ofertas de "complemento", como ofertas de mercado, para garantir que elas atendam às suas necessidades relacionados à conformidade.

Governo Azure está disponível para entidades que manipular os dados que está sujeito a determinadas normas governamentais e requisitos (como NIST 800.171 DIB, ITAR, Administração fiscal 1075, DoD L4 e CJIS) onde o uso do governo do Azure é necessário para cumprir normas. Clientes do governo Azure estão sujeitos a validação de qualificação.

Entidades com dúvidas sobre qualificação para o Azure governo devem consultar sua equipe de conta.

##  <a name="principles-for-securing-customer-data-in-azure-government"></a>Princípios para a proteção de dados do cliente no Azure governo

Governo Azure fornece uma variedade de recursos e serviços que você pode usar para criar soluções de nuvem para atender suas necessidades de dados regulamentadas/controlados. Uma solução de conformidade com cliente é nada mais do que a implementação eficaz de recursos de governo do Azure-de-prontos, juntamente com uma prática de segurança de dados sólida.
Quando você hospeda uma solução no Azure governo, Microsoft trata muitas desses requisitos do nível de infraestrutura de nuvem.

O diagrama a seguir mostra o modelo de defesa no Azure. Por exemplo, a Microsoft fornece infraestrutura de nuvem básicas DDOS, juntamente com recursos de cliente como dispositivos de segurança para o aplicativo específico do cliente que DDoS precisa.

![texto ALT](./media/azure-government-Defenseindepth.png)

Esta página descreve os princípios fundamentais para proteger seus serviços e aplicativos, fornecendo orientação e práticas recomendadas sobre como aplicar esses princípios; em outras palavras, como os clientes devem fazer uso inteligente do governo do Azure para cumprir as obrigações e responsabilidades necessários para uma solução que lida com informações de ITAR.

Os princípios abrangente para a proteção de dados do cliente são:
* Proteção de dados usando criptografia
* Gerenciando segredos
* Isolamento restringir o acesso a dados

##  <a name="protecting-customer-data-using-encryption"></a>Proteção de dados do cliente usando criptografia

Reduzindo riscos e regulamentares obrigações de reunião estão gerando o foco crescente e a importância da criptografia de dados. Usar uma implementação de criptografia eficaz para aprimorar atuais medidas de segurança de rede e aplicativos — e reduzir o risco geral do seu ambiente de nuvem.

### <a name="Overview"></a>Criptografia inativos
A criptografia de dados inativos aplica-se a proteção de conteúdo de cliente contido em armazenamento em disco. Existem várias maneiras que isso pode acontecer:

### <a name="Overview"></a>Criptografia de serviço de armazenamento

Azure criptografia de serviço de armazenamento está habilitada no nível da conta de armazenamento, resultando em bloco blobs e blobs de página sendo criptografados automaticamente quando gravados ao armazenamento do Azure. Quando você lê os dados do armazenamento do Azure, ele será descriptografado pelo serviço de armazenamento antes de ser retornado. Use isto para proteger seus dados sem precisar modificar ou adicionar código a todos os aplicativos.

### <a name="Overview"></a>Criptografia de disco Azure
Use a criptografia de disco do Azure para criptografar o sistema operacional discos e dados usados por uma máquina Virtual do Azure. Integração com o Azure chave cofre oferece controle e ajuda você a gerenciar chaves de criptografia de disco.

### <a name="Overview"></a>Criptografia do lado do cliente
Criptografia do lado do cliente é incorporada do Java e as bibliotecas de cliente de armazenamento do .NET, que podem utilizar Azure chave cofre APIs, como fazer isso simples de implementar. Use Cofre de chave do Azure para obter acesso às senhas no Azure chave cofre para indivíduos específicos usando o Active Directory do Azure.

### <a name="Overview"></a>Criptografia em trânsito

A criptografia básica disponível para conectividade com o Azure governo suporta o protocolo de segurança de nível de transporte (TLS) 1.2 e certificados x. 509. Federal Information Processing Standard (FIPS) 140-2 algoritmos de criptografia de nível 1 também são usados para conexões de rede de infraestrutura entre data centers do governo do Azure.  Windows 8-plus VMs e compartilhamentos de arquivos do Azure e Windows Server 2012 R2 pode usar SMB 3.0 para criptografia entre a máquina virtual e o compartilhamento de arquivos. Use a criptografia do lado do cliente para criptografar os dados antes que ele é transferido para o armazenamento em um aplicativo cliente e descriptografar os dados depois que ele é transferido sem armazenamento.

### <a name="Overview"></a>Práticas recomendadas para criptografia

* IaaS VMs: Use a criptografia de disco Azure. Ativar a criptografia de serviço de armazenamento para criptografar os arquivos VHD que são usados para fazer backup desses discos no armazenamento do Azure, mas isso só criptografa dados recém-criados. Isso significa que, se você criar uma máquina virtual e habilite a criptografia de serviço de armazenamento da conta de armazenamento que contém o arquivo VHD, somente as alterações serão criptografadas, não o arquivo VHD original.
* Criptografia do lado do cliente: Este é o método mais seguro para criptografar seus dados, porque ele criptografa antes de trânsito e criptografa os dados inativos. No entanto, ele requer que você adiciona código a aplicativos usando armazenamento, que talvez você não queira fazer. Nesses casos, você pode usar HTTPs para seus dados e em trânsito, criptografia de serviço de armazenamento para criptografar os dados inativos. Criptografia do lado do cliente também envolve mais carga no cliente — você precisa levar isso em conta seus planos de escalabilidade, especialmente se você estiver criptografando e transferindo muitos dados.

Para obter mais informações sobre as opções de criptografia no Azure, consulte o [Guia de segurança de armazenamento](/storage-security-guide).

##  <a name="protecting-customer-data-by-managing-secrets"></a>Proteção de dados do cliente ao gerenciar segredos

Gerenciamento de chave seguro é essencial para proteger os dados na nuvem. Clientes esforce-se simplificar o gerenciamento de chaves e manter o controle de teclas usada pelos serviços e aplicativos de nuvem para criptografar dados.

### <a name="Overview"></a>Práticas recomendadas para gerenciamento de informações secretas

* Use chave cofre para minimizar os riscos de segredos sejam expostos por meio de arquivos de configuração embutida, scripts, ou no código-fonte. Azure Cofre de chave criptografa chaves (como as chaves de criptografia para criptografia de disco do Azure) e senhas (como senhas), armazenando-os em FIPS 140-2 nível 2 validados módulos de segurança de hardware (HSMs). Para assurance adicionado, você pode importar ou gerar chaves nestas HSMs.
* Modelos e códigos de aplicativo devem conter apenas as referências de URI para as senhas (que significa que reais são eles não no código, configuração ou código-fonte repositórios). Isso impede ataques de phishing chave em repos internos ou externos, como coleta-robôs no GitHub.
* Utilize controles RBAC fortes dentro de compartimento de chave. Se um operador confiável deixa a empresa ou transferências para um novo grupo dentro da empresa, eles devem ser impedidos de poder acessar as senhas.  

Para obter informações adicionais, consulte [Cofre de chave do governo do Azure](/azure-government/azure-government-tech-keyvault)

##  <a name="isolation-to-restrict-data-access"></a>Isolamento restringir o acesso a dados

Isolamento trata usando limites, segmentação e contêineres para limitar o acesso a dados para somente os usuários autorizados, serviços e aplicativos. Por exemplo, a separação entre locatários é um mecanismo de segurança essenciais para plataformas de nuvem vários locatários como Microsoft Azure. Isolamento lógico ajuda a impedir que um locatário interferindo com as operações de qualquer outro locatário.

### <a name="Overview"></a>Isolamento de ambiente
O ambiente do governo do Azure é uma instância física separada do restante da rede da Microsoft. Isso é feito por meio de uma série de controles físicos e lógicos que incluem o seguinte: proteção de obstáculos físicos usando dispositivos biométricos e câmeras.  Uso de credenciais específicas e autenticação multifator pelo pessoal da Microsoft que exigem acesso lógico ao ambiente de produção.  Todos os infraestrutura de serviço para o Azure governo está localizada nos Estados Unidos.

#### <a name="Overview"></a>Isolamento por cliente
Controle de acesso de rede do Azure implementa e diferenciação por meio de isolamento de VLAN, ACLs, carregam balanceadores e filtros IP

Os clientes podem isolar ainda mais seus recursos em assinaturas, grupos de recursos, redes virtuais e sub-redes.

Para obter mais informações sobre o isolamento no Microsoft Azure consulte a [seção de isolamento do guia de segurança do Azure](/azure-security-getting-started/#isolation).

Para informações complementares e atualizações assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure governamentais.</a>
