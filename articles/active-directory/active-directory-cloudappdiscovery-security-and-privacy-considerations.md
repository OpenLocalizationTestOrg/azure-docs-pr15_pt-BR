<properties
    pageTitle="Considerações de privacidade e segurança de descoberta de aplicativo em nuvem | Microsoft Azure"
    description="Este tópico descreve as considerações de segurança e privacidade relacionadas à descoberta de aplicativo de nuvem."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Considerações de privacidade e segurança de descoberta de aplicativo de nuvem

A Microsoft está comprometida em proteger sua privacidade e proteger seus dados, ao entregar software e serviços que ajudam a gerenciar a segurança de sua organização. <br>
Sabemos que quando você confiar seus dados para outras pessoas, que confiança requer investimentos de engenharia de segurança rigorosas e especialização fazê-lo.
Microsoft adere às diretrizes de segurança e conformidade de práticas de ciclo de vida de desenvolvimento de software seguro à operação de um serviço. <br>
Protegendo e proteção de dados é uma prioridade da Microsoft.

Este tópico explica como dados coletados, processados e seguros dentro do Azure Active Directory nuvem aplicativo Discovery




##<a name="overview"></a>Visão geral

Descoberta de aplicativo de nuvem é um recurso do Azure AD e é hospedada no Microsoft Azure. <br>
O agente de ponto de extremidade de descoberta de aplicativo de nuvem é usado para coletar dados de descoberta de aplicativo de máquinas gerenciado por TI. <br>
Os dados coletados são enviados com segurança por um canal criptografado para o serviço de nuvem do Azure AD App descoberta. <br>
Os dados de detecção de aplicativo de nuvem para uma organização, forem visíveis no portal do Azure. <br>


<center>![Como funciona a descoberta de aplicativo de nuvem](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


As seções a seguir siga o fluxo de informações e descrever como ele é protegido enquanto move da sua organização para o serviço de nuvem aplicativo descoberta e, por fim para o portal de descoberta de aplicativo de nuvem.



## <a name="collecting-data-from-your-organization"></a>Coleta de dados da sua organização

Para poder usar o recurso de descoberta de aplicativo de nuvem do Azure Active Directory para obter ideias para os aplicativos usados por funcionários em sua organização, você precisa primeiro implantar o agente de ponto de extremidade de descoberta de aplicativo do Azure AD nuvem em máquinas em sua organização.

Os administradores de locatário Azure Active Directory (ou seu representante) podem baixar o pacote de instalação do agente do portal do Azure. O agente pode ser manualmente instalado ou instalado em várias máquinas da organização usando SCCM ou política de grupo.

Para obter mais informações sobre as opções de implantação, consulte o [Guia de implantação de política de grupo do nuvem aplicativo descoberta](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).
<br>

### <a name="data-collected-by-the-agent"></a>Dados coletados pelo agente

As informações descritas na lista a seguir são coletadas pelo agente quando uma conexão é feita em um aplicativo Web. As informações são coletadas somente para os aplicativos que o administrador configurou para descoberta. <br>
Você pode editar a lista de aplicativos de nuvem que o agente monitora por meio de lâmina descoberta de aplicativo de nuvem no Microsoft [Azure portal](https://portal.azure.com/), em **configurações**->**Coleta de dados**->**lista de conjunto de aplicativos**. Para obter mais detalhes, consulte [Obtendo iniciado com nuvem aplicativo descoberta](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Categoria de informações**: informações do usuário <br>
**Descrição**: <br>
O nome de usuário do Windows do processo que fez uma solicitação para o aplicativo da Web de destino (por exemplo: domínio \ nomedeusuário) bem como o Windows segurança SID (identificador) do usuário.


**Categoria de informações**: informações de processo <br>
**Descrição**: <br>
O nome do processo que fez a solicitação para o aplicativo da Web de destino (por exemplo: "iexplore.exe")

**Categoria de informações**: informações de máquina <br>
**Descrição**: <br>
O nome de NetBIOS de computador em que o agente está instalado.

**Categoria de informações**: as informações de tráfego de aplicativo <br>
**Descrição**: <br>

As seguintes informações de conexão:

- A fonte (computador local) e endereços IP de destino e números de porta

- O endereço IP público da organização por meio da qual a solicitação vai check-out.

- O tempo da solicitação

- O volume de tráfego enviadas e recebidas

- A versão IP (4 ou 6)

- TLS somente para conexões: O nome do host de destino da extensão de indicação de nome do servidor ou o certificado de servidor.

As seguintes informações de HTTP:

- Método (GET, POST, etc.)

- Protocolo (HTTP/1.1, etc.)

- Cadeia de caracteres de agente de usuário

- Nome do host

- URI (excluindo com a cadeia de caracteres de consulta) de destino

- Informações de tipo de conteúdo

- Informações de URL de referência (excluindo a cadeia de caracteres de consulta)



> [AZURE.NOTE] As informações de HTTP acima são coletadas para todas as conexões não criptografado.
Para conexões de TLS, essas informações só são capturadas quando a configuração de 'Inspeção profunda' está ativada no portal. A configuração é 'Ativada' por padrão.
Para mais detalhes, veja abaixo e [Obtendo iniciado com nuvem aplicativo descoberta](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Além dos dados que o agente coleta sobre a atividade de rede, ele também coletará informações anônimas sobre o software e configuração de hardware, relatórios de erros e informações sobre como o agente está sendo usado.

<br><br>
### <a name="how-the-agent-works"></a>Como funciona o agente

A instalação do agente inclui dois componentes:

- Um componente de modo de usuário

- Um componente de driver de modo de núcleo (driver de plataforma de filtragem do Windows)



Quando o agente é instalado armazena um certificado confiável específicas da máquina na máquina que ele usa para estabelecer uma conexão segura com o serviço de descoberta de aplicativo de nuvem. <br>
O agente periodicamente recupera configuração de política de serviço de nuvem aplicativo descoberta sobre esta conexão segura. <br>
A política inclui informações sobre quais aplicativos de nuvem para monitorar e se atualização automática deve ser habilitado, entre outras coisas.

Como o tráfego da Web é enviado e recebido na máquina do Internet Explorer e Chrome, o agente de descoberta de aplicativo de nuvem analisa o tráfego e extrai os metadados relevantes (consulte a seção **dados coletados pelo agente** acima). <br>
Cada minuto, o agente carrega os metadados coletados para o serviço de nuvem aplicativo descoberta por um canal criptografado.

O componente de driver intercepta o tráfego criptografado e se insere no fluxo criptografado. Mais detalhes na seção **Intercepting dados de conexões criptografadas (inspeção profunda)** abaixo.


### <a name="respecting-user-privacy"></a>Respeito aos privacidade do usuário

Nosso objetivo é fornecer administradores as ferramentas para definir o equilíbrio entre óptica detalhada em privacidade de usuário e o uso do aplicativo conforme apropriado para sua organização. Para esse fim, oferecemos os seguintes botões na página de configurações no Portal:

- **Coleta de dados**: os administradores podem escolher para especificar quais aplicativos ou categorias de aplicativo que desejam obter dados de descoberta em.

- **Inspeção profunda**: administradores pode escolher especificar se o agente coleta tráfego HTTP para conexões SSL/TLS (também conhecidos como **' inspeção profunda '**). Saiba mais sobre isso na próxima seção.

- **Opções de consentimento**: os administradores podem usar o portal de descoberta de aplicativo de nuvem escolha se deseja notificar os usuários do conjunto de dados pelo agente, e se deseja exigir usuário concorda antes do início do agente coleta de dados de usuário.

O agente de ponto de extremidade de descoberta de aplicativo de nuvem coleta apenas as informações descritas na seção **dados coletados pelo agente** acima.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Interceptar os dados de conexões criptografadas (inspeção profunda)
Conforme mencionado anteriormente, os administradores podem configurar o agente para monitorar dados de conexões criptografadas ('inspeção profunda'). TLS ([Segurança da camada de transporte](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) é um dos protocolos mais comuns em uso na Internet hoje. Criptografando comunicação com TLS, um cliente pode estabelecer um canal de comunicação segura e privada com um servidor web; TLS oferece proteção essencial para passar credenciais de autenticação e evitar a divulgação de informações confidenciais.

Enquanto ponta a ponta criptografado canal seguro fornecido por TLS permite proteção de privacidade e segurança importante, o protocolo é geralmente abusos para fins mal-intencionados ou mal-intencionado. Muito então, na verdade, que TLS é conhecida como "bypass de firewall universal protocolo de". A raiz do problema é que a maioria dos firewalls não conseguem inspecionar comunicação TLS porque os dados de camada de aplicativo são criptografados com SSL. Sabendo disso, ataques frequentemente aproveitam TLS para entregar cargas mal-intencionado para um usuário certo de que mesmo os firewalls de camada de aplicativo mais inteligentes cegas completamente a TLS e simplesmente devem retransmissão comunicação TLS entre hosts. Usuários finais frequentemente aproveitar TLS para ignorar controles de acesso impostos pelo seus firewalls corporativos e servidores proxy, usá-lo para conectar proxies públicos e para protocolos não-TLS através do firewall que caso contrário, podem ser bloqueados pelo política de túnel.

Inspeção profunda permite que o agente de descoberta de aplicativo de nuvem agir como um confiável-em-a-interceptação. Quando uma solicitação de cliente é feita para acessar um recurso HTTPS protegido, o driver de agente de ponto de extremidade intercepta a conexão e estabelece que uma nova conexão ao servidor de destino para recupera seu certificado SSL em nome do cliente. O agente verifica que o certificado pode ser confiável (Verificando se ele não foi revogado e realizar outras verificações de certificado) e, se essas passagem, em seguida, o agente de ponto de extremidade copia as informações de certificado do servidor e cria seu próprio certificado de servidor – conhecido como um certificado de interceptação – usando essas informações. O certificado de interceptação é assinado em interrupções pelo agente de ponto de extremidade com um certificado raiz, que é instalado no repositório de certificado confiável do Windows. Este certificado auto-assinado está marcado não exportável e é ACL tinha para administradores. Ele se destina a nunca deixe o computador no qual ele foi criado. Quando o aplicativo de cliente do usuário final recebe o certificado de interceptação, ele será confiar porque ele pode validar com êxito a cadeia de certificados totalmente para o certificado raiz. Esse processo é mais transparente do ponto de vista do usuário final com algumas limitações conforme descrito abaixo.

Habilitando inspeção profunda, o agente de ponto de extremidade de descoberta de aplicativo de nuvem pode descriptografar e inspecionar comunicações de TLS criptografada, permitindo que o serviço para reduzir ruído e forneça a obtenção de informações sobre o uso dos aplicativos nuvem criptografados.

#### <a name="a-word-of-caution"></a>Uma palavra de precaução
Antes de ligar inspeção profunda, é altamente recomendável que você se comunicar sua intenção aos seus legal e departamentos de RH e obter consentimento. Inspecionar comunicações de criptografadas particular do usuário final pode ser um assunto confidencial, por motivos óbvios. Antes de um produção rolo-out de inspeção profunda, certifique-se que segurança da sua empresa e políticas de uso aceitável tenham sido atualizadas para indicar que comunicação criptografada será inspecionar. Notificação de usuário e isenção de sites considerados confidenciais (por exemplo, médicos e bancários sites) também podem ser necessários se você configurar a descoberta de aplicativo de nuvem para monitorar a elas. Conforme mencionado acima, os administradores podem usar o portal de descoberta de aplicativo de nuvem para escolher se deseja notificar os usuários do conjunto de dados pelo agente e solicitar consentimento do usuário antes do agente inicia a coleta de dados de usuário.

### <a name="known-issues-and-drawbacks"></a>Problemas conhecidos e desvantagens
Há alguns casos onde interceptação TLS pode afetar a experiência do usuário final:

- Os certificados de validação (EV) estendida renderizados barra de endereços do navegador da web verde para agir como uma dica visual que você está visitando um site confiável. Inspeção de TLS não é possível duplicar EV no certificado emite para o cliente, para que sites que usam certificados EV funcionarão normalmente, mas a barra de endereços não exibirá verde.  

- Fixação chave pública (também conhecido como certificado fixação) foram projetados para ajudar a proteger os usuários contra ataques no meio e maliciosas as autoridades de certificação. Quando o certificado raiz para um site fixado não coincidir com um da autoridade de certificação boa conhecidas, navegador rejeita a conexão com um erro. Como interceptação TLS é, na verdade, uma-em-a-interceptação, essas conexões falhará.

- Se os usuários clicam no ícone de cadeado no navegador de barra de endereços do navegador para inspecionar as informações do site, eles não verão uma cadeia que termina em autoridade de certificação usada para assinar o certificado de site, mas em vez disso, uma cadeia de certificados que terminam com o Windows confiáveis repositório de certificados.

Para reduzir as ocorrências desses problemas, podemos controlar de serviços de nuvem e aplicativos cliente conhecidos usar validação estendida ou fixação de chave pública e instruir o agente de ponto de extremidade para evitar interceptação conexões afetados. Mesmo nesses casos, no entanto, você ainda receberá relatórios com o uso desses aplicativos de nuvem e o volume de dados transferidos, mas como não são profunda inspecionadas, sem detalhes sobre como os aplicativos foram usados estarão disponíveis.


## <a name="sending-data-to-cloud-app-discovery"></a>Enviar dados para descoberta de aplicativo de nuvem

Depois que os metadados foram coletados pelo agente, ele está em cache na máquina por até um minuto ou até que os dados armazenados em cache atingem um tamanho de 5MB. Em seguida, é compactado e enviado por uma conexão segura para o serviço de descoberta de aplicativo de nuvem.

Se o agente conseguir se comunicar com o serviço de descoberta de aplicativo de nuvem por algum motivo, os metadados coletados são armazenados em um cache de arquivo local que só pode ser acessado por usuários privilegiados na máquina (como o grupo de administradores). <br>
O agente tenta automaticamente reenviar os metadados em cache até que ele com êxito foi recebido pelo serviço Descoberta de aplicativo de nuvem.



## <a name="receiving-the-data-at-the-service-end"></a>Recebendo os dados no final do serviço

Os agentes autenticam a descoberta de aplicativo de nuvem serviço usando o certificado de autenticação de cliente específico de máquina mencionado acima e encaminha dados por um canal criptografado. <br>
Pipeline de análise do serviço Descoberta de aplicativo de nuvem processa os metadados para cada cliente separadamente logicamente partição-la por todos os estágios do pipeline de análise.
Os metadados analisados unidades os diversos relatórios no portal.

Os metadados não processados e os metadados analisados são armazenados em até 180 dias. Além disso, os clientes podem escolher capturar os metadados analisado em uma conta de armazenamento de blob do Microsoft Azure de sua escolha.
Isso é útil para análise offline de metadados, bem como maior retenção de dados.

## <a name="accessing-the-data-using-the-azure-portal"></a>Acessar os dados usando o portal do Azure

Em um esforço para manter os metadados coletados seguro, por padrão somente os administradores globais do locatário têm acesso ao recurso de descoberta de aplicativo de nuvem no portal do Azure. <br>
No entanto, os administradores podem optar por esse acesso a outros usuários ou grupos de representante.



> [AZURE.NOTE] Para obter mais detalhes, consulte [Obtendo iniciado com nuvem aplicativo descoberta](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Qualquer usuário acessar os dados no portal do, deve ser licenciado com uma licença do Azure AD Premium.



##<a name="additional-resources"></a>Recursos adicionais


* [Como pode descobrir aplicativos de nuvem unsanctioned que são usados em minha organização](active-directory-cloudappdiscovery-whatis.md)
* [Índice de artigo de gerenciamento de aplicativos no Active Directory do Azure](active-directory-apps-index.md)
