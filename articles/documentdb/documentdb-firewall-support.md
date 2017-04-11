<properties
    pageTitle="Suporte a firewall DocumentDB | Microsoft Azure"
    description="Saiba como usar as diretivas de controle de acesso IP para o suporte de firewall contas de banco de dados do Azure DocumentDB."
    keywords="Controle de acesso IP, o suporte de firewall"
    services="documentdb"
    authors="shahankur11"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ankshah; kraman"/>

# <a name="documentdb-firewall-support"></a>Suporte a firewall DocumentDB

Para proteger os dados armazenados em uma conta de banco de dados do Azure DocumentDB, DocumentDB forneceu suporte para um segredo baseado em [modelo de autorização](https://msdn.microsoft.com/library/azure/dn783368.aspx) que utiliza um código de autenticação de mensagem baseado em Hash forte (HMAC). Agora, além do modelo de autorização baseados em secreta, DocumentDB dá suporte à política controlado pelo controles de acesso baseado em IP para o suporte de firewall de entrada. Este modelo é muito semelhante às regras de firewall de um sistema de banco de dados tradicional e fornece um nível adicional de segurança para a conta de banco de dados DocumentDB. Com esse modelo, agora você pode configurar uma conta de banco de dados de DocumentDB para ser acessível somente a partir de um conjunto aprovado de máquinas e/ou serviços em nuvem. Acesso aos recursos de DocumentDB por esses conjuntos aprovados de máquinas e serviços ainda requerem o chamador apresentar um token de autorização válido.

## <a name="ip-access-control-overview"></a>Visão geral do controle de acesso IP

Por padrão, uma conta de banco de dados de DocumentDB é acessível a partir do internet pública, desde que a solicitação é acompanhada por um token de autorização válido. Para configurar o controle de acesso baseado em política IP, o usuário deve fornecer o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR devem ser incluídos como a lista permitida de IPs de cliente para uma conta de banco de dados fornecido. Quando essa configuração for aplicada, todas as solicitações provenientes máquinas fora esta lista de permitidos serão bloqueadas pelo servidor.  O fluxo de processamento para o controle de acesso baseado em IP de conexão é descrito no diagrama a seguir.

![Diagrama mostrando o processo de conexão de controle de acesso baseado em IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Conexões de serviços de nuvem

No Azure, serviços de nuvem são uma maneira muito comum para hospedar lógica de serviço de camada intermediária usando DocumentDB. Para habilitar o acesso a uma conta de banco de dados DocumentDB de um serviço de nuvem, o endereço IP público do serviço de nuvem deve ser adicionado à lista de permitidos de endereços IP associados a uma conta de banco de dados de DocumentDB contatando o [suporte Azure](#configure-ip-policy).  Isso garante que todas as instâncias de função de serviços de nuvem tenham acesso à sua conta de banco de dados DocumentDB. Você pode recuperar endereços IP para os serviços de nuvem no portal do Azure, conforme mostrado na seguinte captura de tela. 

![Captura de tela mostrando o endereço IP público para um serviço de nuvem exibido no portal do Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Quando você dimensionar fora do seu serviço de nuvem, adicionando instâncias de função adicional, essas novas instâncias automaticamente terá acesso à conta do banco de dados DocumentDB pois fazem parte do serviço de nuvem do mesmo.

## <a name="connections-from-virtual-machines"></a>Conexões de máquinas virtuais

[Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) ou [escala de máquina virtual define](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) também pode ser usados para hospedar serviços de camada intermediária usando DocumentDB.  Para configurar o DocumentDB conta de banco de dados para permitir o acesso de máquinas virtuais, endereços IP públicos de máquina virtual e/ou conjunto de escala de máquina virtual deve ser configurada como um dos endereços IP permitidos para a sua conta de banco de dados DocumentDB contatando o [suporte Azure](#configure-ip-policy). Você pode recuperar endereços IP para máquinas virtuais no portal do Azure, conforme mostrado na seguinte captura de tela.

![Captura de tela mostrando um endereço IP público para uma máquina virtual exibido no portal do Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Quando você adiciona instâncias de máquinas virtuais adicionais para o grupo, eles são fornecidos automaticamente o acesso à sua conta de banco de dados DocumentDB.

## <a name="connections-from-the-internet"></a>Conexões da internet

Quando você acessa uma conta de banco de dados DocumentDB de um computador na internet, o endereço IP do cliente ou o intervalo de endereços IP da máquina deve ser adicionado à lista de permitidos de endereço IP para a conta de banco de dados DocumentDB. 

## <a id="configure-ip-policy"></a>Configurando a política de controle de acesso IP

Use o portal do Azure para registrar uma solicitação com [Suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para habilitar a política de controle de acesso IP em sua conta do banco de dados.

1. Na lâmina [Ajuda + suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , selecione **nova solicitação de suporte**.
2. Na lâmina **nova solicitação de suporte** , selecione **Noções básicas**.
3. Na lâmina **Noções básicas** , selecione o seguinte:
    - **Tipo de problema**: cota
    - **Assinatura**: A assinatura associada à conta na qual deseja adicionar a política de controle de acesso IP.
    - **Tipo de cota**: DocumentDB
    - **Plano de suporte**: suporte de cota - incluído.
4. Na lâmina **problema** , faça o seguinte:
    - **Gravidade**: selecione C - impacto mínimo
    - **Detalhes**: Copie o seguinte texto na caixa e incluir seu nome de conta/s e o endereço IP/es: "eu gostaria de habilitar o suporte de firewall para minha conta de banco de dados DocumentDB. Conta de banco de dados: *nome de conta de incluir/s*. Permitido/intervalos de endereços IP: *incluir/intervalo de endereços IP no formato CIDR, por exemplo 13.91.6.132, 13.91.6.1/24*. "
    - Clique em **Avançar**. 
5. Na lâmina **informações de contato** , preencha os detalhes do contato e clique em **criar**. 

Depois que sua solicitação for recebida, controle de acesso IP deve ser habilitado dentro de 24 horas. Você será notificado quando a solicitação for concluída.

![Captura de tela da Ajuda + oferecem suporte para lâminas](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![Captura de tela da lâmina problema](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>A política de controle de acesso IP de solução de problemas

### <a name="portal-operations"></a>Operações de portal

Habilitando uma diretiva de controle de acesso IP para sua conta de banco de dados de DocumentDB, todo o acesso à sua conta de banco de dados DocumentDB de máquinas fora configurado permitido a lista de intervalos de endereços IP bloqueados. Por esse modelo, a operação de plano de dados a partir do portal de navegação também será bloqueado para garantir a integridade de controle de acesso. 

### <a name="sdk--rest-api"></a>API SDK & restante

Por razões de segurança, o access via SDK ou API REST de máquinas não na lista de permitidos retornará genérico 404 não encontrado resposta com sem detalhes adicionais. Verifique se o IP permitido lista configurada para sua conta de banco de dados de DocumentDB para garantir que a configuração de diretiva correta está aplicada à sua conta de banco de dados DocumentDB.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre rede dicas de desempenho relacionados, consulte [dicas de desempenho](documentdb-performance-tips.md).