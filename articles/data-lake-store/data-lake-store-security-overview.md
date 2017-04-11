<properties
   pageTitle="Visão geral de segurança no repositório de Lucerne dados | Microsoft Azure"
   description="Entender como o armazenamento do Azure dados Lucerne é um armazenamento de dados grande mais seguro"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Segurança no repositório de Lucerne de dados do Azure

Muitas empresas estão aproveitando a análise de dados grande de obtenção de informações de negócios para ajudá-lo a tomar decisões inteligentes. Uma organização pode ter um ambiente complexo e regulamentado, com um número crescente de usuários diferentes. É fundamental para uma empresa garantir que os dados de negócios essenciais são armazenados mais segura, com o nível de acesso concedido a usuários individuais correto. Armazenamento de Lucerne de dados do Azure foi projetado para ajudar a atender a esses requisitos de segurança. Neste artigo, saiba mais sobre os recursos de segurança Lucerne de repositório de dados, incluindo:

* Autenticação
* Autorização
* Isolamento da rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Gerenciamento de autenticação e identidade

Autenticação é o processo pelo qual a identidade do usuário será verificada quando o usuário interage com dados Lucerne loja ou com qualquer serviço que se conecta aos dados Lucerne Store. Para gerenciamento de identidade e autenticação, armazenamento de dados de Lucerne usa [Azure Active Directory](../active-directory/active-directory-whatis.md), uma identidade abrangente e solução de nuvem de gerenciamento de acesso que simplifica o gerenciamento de usuários e grupos.

Cada assinatura Azure pode ser associada uma instância do Active Directory do Azure. Somente os usuários e identidades de serviço definidos no seu serviço do Active Directory do Azure podem acessar sua conta de armazenamento de Lucerne de dados, usando o portal do Azure, ferramentas de linha de comando, ou por meio de aplicativos cliente sua organização cria usando o SDK do Azure dados Lucerne Store. Principais vantagens de usar o Active Directory do Azure como um mecanismo de controle de acesso centralizado são:

* Gerenciamento do ciclo de vida de identidade simplificado. A identidade de um usuário ou um serviço (uma identidade principal do serviço) pode ser criada rapidamente e rapidamente revogada simplesmente excluindo ou desativando a conta no diretório.

* Autenticação multifator. [Autenticação multifator](../multi-factor-authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança para inscrições de usuário e transações.

* Autenticação de qualquer cliente através de um protocolo abrir padrão, como OAuth ou OpenID.

* Federação com serviços de diretório corporativo e provedores de identidade de nuvem.

## <a name="authorization-and-access-control"></a>Autorização e controle de acesso

Após o Azure Active Directory autentica um usuário para que o usuário pode acessar o repositório de Lucerne de dados do Azure, controles de autorização acessar permissões de repositório de Lucerne de dados. Armazenamento de dados de Lucerne separa autorização para atividades relacionados à conta e relacionada a dados da seguinte maneira:

* [Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) (RBAC) fornecida pelo Azure para gerenciamento de conta
* POSIX ACL para acessar dados da loja


### <a name="rbac-for-account-management"></a>RBAC para gerenciamento de conta

Quatro funções básicas são definidas para armazenamento de dados de Lucerne por padrão. As funções permitem operações diferentes em uma conta de armazenamento de Lucerne de dados via o portal do Azure, cmdlets do PowerShell e APIs REST. Funções de proprietário e colaborador podem executar uma variedade de funções de administração da conta. Você pode atribuir a função de leitor para os usuários que só interagem com dados.

![Funções RBAC] (./media/data-lake-store-security-overview/rbac-roles.png "Funções RBAC")

Observe que, embora as funções são atribuídas para gerenciamento de conta, algumas funções afetam acesso aos dados. Você precisa usar ACLs para controlar o acesso às operações que um usuário pode executar no sistema de arquivos. A tabela a seguir mostra um resumo dos direitos de gerenciamento e acesso de dados para as funções padrão.

| Funções                    | Direitos de gerenciamento               | Direitos de acesso de dados | Explicação |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Nenhuma função atribuída         | Nenhum                            | Controlados por ACL    | O usuário não pode usar o portal do Azure ou os cmdlets do PowerShell do Azure para procurar dados Lucerne loja. O usuário pode usar apenas as ferramentas de linha de comando.
| Proprietário  | Todos os  | Todos os  | A função de proprietário é um superusuário. Esta função pode gerenciar tudo e tem acesso completo aos dados.
| Leitor   | Somente leitura  | Controlados por ACL    | Função leitor pode exibir tudo sobre o gerenciamento de conta, como o que é atribuída à qual função. Função leitor não pode fazer qualquer alteração.   |
| Colaborador              | Tudo, exceto a adicionar e remover funções | Controlados por ACL    | A função Colaborador pode gerenciar alguns aspectos de uma conta, como implantações e criar e gerenciar alertas. A função Colaborador não pode adicionar ou remover funções.
| Administrador de acesso do usuário | Adicionar e remover funções            | Controlados por ACL    | A função de administrador de acesso do usuário pode gerenciar o acesso de usuário a contas. |

Para obter instruções, consulte [atribuir usuários ou grupos de segurança para contas de armazenamento de Lucerne de dados](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Usando ACLs para operações em sistemas de arquivo

Dados Lucerne Store é um sistema de arquivos hierárquico como o arquivo do Hadoop distribuído com o sistema (HDFS) e suporta [POSIX ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Ela controla a leitura (r), gravação (w) e executar (permissões aos recursos para a função de proprietário, para o grupo de proprietários e para outros usuários e grupos x). Na visualização de dados Lucerne repositório público (a versão atual), ACLs estão habilitadas na pasta raiz, subpastas e arquivos individuais. As ACLs que você aplicar à pasta raiz também se aplicam a todas as pastas filho e arquivos.

Recomendamos que você defina ACLs para vários usuários usando [grupos de segurança](../active-directory/active-directory-accessmanagement-manage-groups.md). Adicionar usuários a um grupo de segurança e atribuir as ACLs de um arquivo ou pasta a esse grupo de segurança. Isso é útil quando você deseja fornecer acesso personalizado, porque você está limitado à adição de um máximo de nove entradas para acesso personalizado. Para obter mais informações sobre como proteger melhor dados armazenados no repositório de Lucerne dados usando grupos de segurança do Azure Active Directory, consulte [atribuir usuários ou grupos de segurança como ACLs para o sistema de arquivos de armazenamento de Lucerne de dados do Azure](data-lake-store-secure-data.md#filepermissions).

![Acesso à lista padrão e personalizado] (./media/data-lake-store-security-overview/adl.acl.2.png "Acesso à lista padrão e personalizado")

## <a name="network-isolation"></a>Isolamento da rede

Armazenamento de Lucerne de dados de uso para ajudar a controlar o acesso ao seu armazenamento de dados no nível da rede. Você pode estabelecer firewalls e definir um intervalo de endereços IP para os clientes confiáveis. Com um intervalo de endereços IP, somente os clientes que têm um endereço IP dentro do intervalo definido podem se conectar ao armazenamento de Lucerne de dados.

![Configurações de firewall e acesso IP] (./media/data-lake-store-security-overview/firewall-ip-access.png "Endereços IP e as configurações de firewall")

## <a name="data-protection"></a>Proteção de dados

Organizações desejam garantir que seus dados essenciais estão seguros ao longo do ciclo de vida. Para dados em trânsito, armazenamento de dados de Lucerne usa o protocolo de segurança de camada de transporte (TLS) de padrão da indústria para proteger os dados que se move entre um cliente e armazenamento de Lucerne de dados.

Proteção de dados para dados inativos estarão disponível em versões futuras.

## <a name="auditing-and-diagnostic-logs"></a>Logs de diagnósticos e auditoria

Você pode usar logs de auditoria ou diagnósticos, dependendo se você estiver procurando por logs de atividades relacionadas ao gerenciamento ou atividades de dados relacionados.

*  Relacionados a gerenciamento de atividades usam APIs do Gerenciador de recursos do Azure e forem reproduzidas no portal do Azure por meio de logs de auditoria.
*  Atividades relacionadas a dados usam WebHDFS restante APIs e forem reproduzidas no portal do Azure via logs de diagnóstico.

### <a name="auditing-logs"></a>Logs de auditoria

Para cumprir normas, uma organização pode exigir trilhas de auditoria adequado se precisar aprofundar ocorrências específicas. Armazenamento de Lucerne de dados tem internos de monitoramento e auditoria, e ele registrará todas as atividades de gerenciamento de conta.

De trilhas de auditoria de gerenciamento de conta, exibir e escolha as colunas que você deseja fazer logon. Você também pode exportar os logs de auditoria ao armazenamento do Azure.

![Logs de auditoria] (./media/data-lake-store-security-overview/audit-logs.png "Logs de auditoria")

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Você pode definir trilhas de auditoria de acesso a dados no portal do Azure (nas configurações de diagnóstico) e crie uma conta de armazenamento de Blob do Azure onde os logs estão armazenados.

![Logs de diagnóstico] (./media/data-lake-store-security-overview/diagnostic-logs.png "Logs de diagnóstico")

Depois de configurar as definições de diagnóstico, você pode exibir os logs da guia de **Logs de diagnóstico** .

Para obter mais informações sobre como trabalhar com os logs de diagnóstico com armazenamento de Lucerne de dados do Azure, consulte [logs de diagnóstico de acesso para armazenamento de Lucerne de dados](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo

Clientes corporativos exigem uma plataforma de nuvem de análise de dados seguro e fácil de usar. Armazenamento de Lucerne de dados do Azure foi projetado para ajudar o endereço que esses requisitos por meio de gerenciamento de identidade e autenticação via integração com o Active Directory do Azure, autorização baseada em ACL, isolamento de rede, criptografia de dados em trânsito e em posiciona (no futuro) e auditoria.

Se você quiser ver os novos recursos do armazenamento de dados de Lucerne, envie-nos seus comentários no [Fórum de dados Lucerne repositório UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Consulte também

- [Visão geral do Azure Lucerne de repositório de dados](data-lake-store-overview.md)
- [Introdução ao armazenamento de Lucerne de dados](data-lake-store-get-started-portal.md)
- [Proteger os dados no repositório de Lucerne de dados](data-lake-store-secure-data.md)
