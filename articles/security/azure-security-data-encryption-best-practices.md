<properties
   pageTitle="Práticas recomendadas de segurança de dados e criptografia | Microsoft Azure"
   description="Este artigo fornece um conjunto de práticas recomendadas para segurança de dados e criptografia usando criados em funcionalidades do Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Práticas recomendadas de segurança de dados do Azure e criptografia

Uma das teclas de proteção de dados na nuvem é contabilidade para os estados possíveis em que seus dados podem ocorrer e quais controles estão disponíveis para esse estado. Para fins de dados Azure as recomendações de práticas recomendadas de segurança e criptografia será em torno de estados dos dados a seguir:

- Inativos: Isso inclui todas as informações de objetos de armazenamento, contêineres e tipos que existem estática em mídia física, seja ele magnético ou disco óptico.

- Em trânsito: Quando dados estão sendo transferidos entre componentes, locais ou programas, como através da rede, através de um barramento de serviço (do local na nuvem e vice-versa, incluindo as conexões híbrido como rota expressa), ou durante um processo de entrada/saída, ele é considerado como sendo em movimento.

Neste artigo, abordaremos uma coleção de práticas recomendadas de segurança e criptografia de dados do Azure. Estas práticas recomendadas são derivadas das nossa experiência com segurança de dados do Azure e criptografia e experiências de clientes como você.

Cada prática recomendada, explicaremos:

- Qual é a prática recomendada
- Por que você deseja habilitar essa prática recomendada
- O que pode ser o resultado se você não conseguir habilitar a prática recomendada
- Possíveis alternativas à prática recomendada
- Como você pode aprender habilitar a prática recomendada

Este artigo de segurança de dados do Azure e práticas recomendadas de criptografia baseia-se em uma opinião de consenso e plataforma Windows Azure conjuntos de recursos, como elas existem no momento que este artigo foi escrito. Tecnologias e opiniões alterar ao longo do tempo e este artigo será atualizado com regularidade para refletir essas alterações.

Dados Azure segurança e criptografia práticas recomendadas abordadas neste artigo incluem:

- Impor a autenticação multifator
- Controle de acesso (RBAC) baseado em função de uso
- Criptografar Azure máquinas virtuais
- Usar modelos de segurança de hardware
- Gerenciar com estações de trabalho seguras
- Habilitar a criptografia de dados do SQL
- Proteger os dados em trânsito
- Aplicar a criptografia de dados de nível de arquivo


## <a name="enforce-multi-factor-authentication"></a>Impor a autenticação multifator

A primeira etapa no acesso a dados e controle no Microsoft Azure é autenticar o usuário. [Autenticação multifator da Azure (MFA)](../multi-factor-authentication/multi-factor-authentication.md) é um método de verificação da identidade do usuário usando outro método que apenas um nome de usuário e senha. Este autenticação método ajuda a proteger informações acesso aos dados e aplicativos atendendo demanda do usuário para um processo de entrada simples.

Ativando o Azure MFA para seus usuários, você está adicionando uma segunda camada de segurança ao usuário entrada e transações. Nesse caso, uma transação pode estar acessando um documento localizado em um servidor de arquivos ou no SharePoint Online. MFA Azure também ajuda a TI para reduzir a probabilidade de que uma credencial comprometida terá acesso aos dados da organização.

Por exemplo: se você impor MFA do Azure para seus usuários e configure-o para usar um telefonema ou mensagem de texto como verificação, se a credencial do usuário for comprometida, o invasor não será possível acessar qualquer recurso, desde que ele não terão acesso ao telefone do usuário. As organizações que não adicionam essa camada extra de proteção de identidade estão mais sujeitas para ataque de roubo de credencial, que pode levar a compromisso de dados.

Uma alternativa para as organizações que deseja manter o autenticação controle local é usar o [Servidor de autenticação multifator Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), também chamado MFA local. Usando esse método ainda será capaz de aplicar a autenticação multifator, mantendo o MFA server local.

Para obter mais informações sobre MFA do Azure, leia o artigo [Introdução ao Azure multi-Factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Controle de acesso (RBAC) baseado em função de uso
Restringir o acesso com base nos princípios de segurança [precisa saber](https://en.wikipedia.org/wiki/Need_to_know) e [privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) . Isso é fundamental para as organizações que deseja aplicar políticas de segurança para acesso a dados. Azure controle de acesso baseado em função (RBAC) pode ser usado para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso.

Você pode aproveitar [funções RBAC internas](../active-directory/role-based-access-built-in-roles.md) no Azure atribuir privilégios aos usuários. Considere o uso de *Colaborador de conta de armazenamento* para operadores de nuvem que precisam gerenciar contas de armazenamento e a função de *Colaborador clássico de conta de armazenamento* para gerenciar contas de armazenamento clássico. Para operadores de nuvem que precisa gerenciar VMs e conta de armazenamento, considere adicioná-los à função *Colaborador de máquina Virtual* .

As organizações que não impor o controle de acesso de dados, aproveitando recursos como RBAC podem ser dando mais privilégios do que for necessário para seus usuários. Isso pode levar a dados compromisso fazendo com que alguns usuários tenham acesso aos dados que eles não deveriam ter em primeiro lugar.

Você pode saber mais sobre Azure RBAC lendo o artigo [Azure Role-Based controle de acesso](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Criptografar Azure máquinas virtuais
Para muitas organizações, a [criptografia de dados no restante](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória na direção de privacidade de dados, conformidade e Soberania de dados. Criptografia de disco Azure permite que os administradores de TI criptografar discos Windows e Linux IaaS Máquina Virtual (VM). Criptografia de disco Azure utiliza o recurso BitLocker padrão do setor do Windows e o recurso de DM Crypt do Linux para fornecer criptografia de volume do sistema operacional e os discos de dados.

Você pode aproveitar criptografia de disco do Azure para ajudar a proteger e proteger seus dados para atender a requisitos de conformidade e segurança organizacional. Organizações considere também usando criptografia para ajudar a reduzir o acesso a dados relacionados a não autorizado riscos. Também é recomendável que você criptografe unidades antes de gravar dados confidenciais a elas.

Certifique-se de criptografar volumes de dados do sua máquina virtual e o volume de inicialização para proteger os dados no restante em sua conta de armazenamento do Azure. Proteja as chaves de criptografia e segredos aproveitando [Azure chave cofre](../key-vault/key-vault-whatis.md).

Para servidores Windows local, considere a criptografia seguinte práticas recomendadas:

- Usar o [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para criptografia de dados
- Armazenar informações de recuperação no AD DS.
- Se houver qualquer problema que foram comprometidas BitLocker chaves, recomendamos que você formatar a unidade para remover todas as instâncias dos metadados BitLocker da unidade ou que descriptografar e criptografar toda a unidade novamente.

As organizações que não aplicar a criptografia de dados serão mais prováveis expostos a problemas de integridade de dados, como usuários maliciosos ou não autorizados roubar dados e comprometido contas obtenha acesso não autorizado aos dados no formato de limpar. Além desses riscos, empresas que estejam em conformidade com as normas do setor, devem comprovar que eles estão atento e estão usando os controles de segurança corretas para melhorar a segurança de dados.

Você pode saber mais sobre criptografia de disco do Azure lendo o artigo [Azure disco criptografia para Windows e Linux IaaS VMs](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Usar módulos de segurança de Hardware

Soluções de criptografia do setor usam teclas secretas para criptografar dados. Portanto, é importante que essas chaves são armazenadas com segurança. Gerenciamento de chaves torna-se parte integrante da proteção de dados, pois ele será aproveitado para armazenar chaves secretas que são usadas para criptografar dados.

Criptografia de disco Azure usa [Cofre de chave do Azure](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e gerenciar chaves de criptografia de disco e senhas na sua assinatura de chave cofre, garantindo que todos os dados na máquina virtual discos são criptografados inativos no seu armazenamento do Azure. Você deve usar Cofre de chave do Azure para chaves e o uso de política de auditoria.

Existem muitos riscos inerentes relacionados a não ter controles de segurança apropriadas para proteger as chaves secretas que foram usadas para criptografar seus dados. Se ataques tem acesso às chaves secretas, eles poderão descriptografar os dados e potencialmente tenha acesso a informações confidenciais.

Você pode saber mais sobre recomendações gerais para gerenciamento de certificados no Azure lendo o artigo [gerenciamento de certificados no Azure: regras](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Para obter mais informações sobre o Azure chave cofre, leia [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gerenciar com estações de trabalho seguras

Como a maioria dos ataques de destino do usuário final, o ponto de extremidade torna-se um dos principais pontos de ataque. Se um invasor compromete o ponto de extremidade, ele pode aproveitar as credenciais do usuário para obter acesso aos dados da organização. A maioria dos ataques de ponto de extremidade são capazes de tirar proveito do fato de que os usuários finais são administradores em suas estações de trabalho locais.

Você pode reduzir esses riscos usando uma estação de trabalho de gerenciamento seguro. Recomendamos que você use um [Estações de trabalho com privilégios de acesso (PATA)](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque em estações de trabalho. Estas estações de trabalho de gerenciamento seguro podem ajudá-lo a reduzir alguns desses ataques ajudam a garantir que seus dados são mais seguros. Verifique se usar PATA para proteger e bloquear sua estação de trabalho. Essa é uma etapa importante para fornecer garantias de alto nível de segurança para contas confidenciais, tarefas e proteção de dados.

Falta de proteção de ponto de extremidade pode colocar seus dados em risco, verifique se a aplicar políticas de segurança em todos os dispositivos que são usados para consumir dados, independentemente do local de dados (nuvem ou local).

Você pode aprender que mais sobre com privilégios de acesso estação de trabalho lendo o artigo [Proteger acesso privilegiado](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Habilitar a criptografia de dados do SQL

[Criptografia de dados transparente de banco de dados do SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) ajuda a proteger contra as ameaças de atividade mal-intencionado realizando em tempo real criptografia e descriptografia do banco de dados, backups associados e arquivos de log de transação inativos sem a necessidade de mudanças no aplicativo.  TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados.

Mesmo quando todo o armazenamento está criptografado, é muito importante para também criptografar seu próprio banco de dados. Esta é uma implementação do defesa profunda para proteção de dados. Se você estiver usando o [Banco de dados do SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) e deseja proteger dados confidenciais como cartão de crédito ou números de seguridade social, você pode criptografar bancos de dados com a criptografia AES de 256 bits de 140-2 validados de FIPS que atenda os requisitos de muitos padrões do setor (por exemplo, HIPAA, PCI).

É importante entender o que os arquivos relacionados a [extensão do pool de buffer](https://msdn.microsoft.com/library/dn133176.aspx) (as BPE) não são criptografados quando um banco de dados está criptografado usando TDE. Você deve usar ferramentas de criptografia no nível de sistema de arquivo como BitLocker ou o [Sistema de arquivos criptografados](https://technet.microsoft.com/library/cc700811.aspx) (EFS) para as BPE arquivos relacionados.

Desde um usuário autorizado como um administrador de segurança ou um administrador de banco de dados pode acessar os dados, mesmo se o banco de dados é criptografado com TDE, você também deve seguir as recomendações abaixo:

- Autenticação do SQL no nível do banco de dados
- Autenticação de AD Azure usando funções RBAC
- Usuários e aplicativos devem usar contas separadas para autenticar. Dessa maneira, você pode limitar as permissões concedidas a usuários e aplicativos e reduzir os riscos de atividades maliciosas
- Implementar segurança em nível de banco de dados usando funções de banco de dados fixa (como db_datareader ou db_datawriter), ou você pode criar funções personalizadas para seu aplicativo para conceder permissões explícitas para objetos de banco de dados selecionado

As organizações que não estão usando criptografia no nível do banco de dados podem ser mais sujeitas para ataques que possam comprometer dados localizados em bancos de dados SQL.

Você pode saber mais sobre criptografia de SQL TDE lendo o artigo [Criptografia transparente de dados com o Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Proteger os dados em trânsito

Proteção de dados em trânsito deve ser parte essencial de sua estratégia de proteção de dados. Como dados mudará trocadas de vários locais, a recomendação geral é que você sempre use protocolos SSL/TLS a troca de dados em diferentes locais. Em algumas circunstâncias, talvez você queira isolar o canal de comunicação inteira entre seu local e a nuvem infraestrutura usando uma rede virtual privada (VPN).

Para mover entre sua infraestrutura de local e o Azure de dados, você deve considerar apropriadas garantias como HTTPS ou VPN.

Para organizações que precisam proteger o acesso de várias estações de trabalho localizado local no Azure, use [Azure - to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Para organizações que precisam de acesso seguro de uma estação de trabalho localizado local ao Azure, use [ponto-a-Site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Link de dados maiores conjuntos podem ser movidos em uma WAN de alta velocidade dedicada como [rota expressa](https://azure.microsoft.com/services/expressroute/). Se você optar por usar rota expressa, você também pode criptografar os dados no nível do aplicativo usando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para adicionado proteção.

Se você interage com o armazenamento do Azure por meio do Portal do Azure, todas as transações ocorrerem via HTTPS. [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd179355.aspx) por HTTPS também podem ser usados para interagir com [O armazenamento do Azure](https://azure.microsoft.com/services/storage/) e [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

As organizações que falharem ao proteger os dados em trânsito estão mais sujeitas para [ataques no meio](https://technet.microsoft.com/library/gg195821.aspx), [espionagem](https://technet.microsoft.com/library/gg195641.aspx) e sequestro de sessão. Esses ataques podem ser a primeira etapa no acesso a dados confidenciais.

Você pode saber mais sobre opção Azure VPN lendo o artigo [de planejamento e design para Gateway VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Aplicar a criptografia de dados de nível de arquivo

Outra camada de proteção que pode aumentar o nível de segurança para os seus dados é criptografar o arquivo em si, independentemente do local do arquivo.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa políticas de criptografia, identidade e autorização para ajudar a proteger seus arquivos e email. Azure RMS funciona em vários dispositivos — telefones, tablets e PCs protegendo dentro de sua organização e fora da sua organização. Esse recurso é possível porque Azure RMS adiciona um nível de proteção que permanece com os dados, mesmo quando ele sai dos limites da sua organização.

Quando você usa o Azure RMS para proteger seus arquivos, você está usando criptografia padrão do setor com suporte total de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Quando você aproveitar Azure RMS para proteção de dados, você tem a garantia de que a proteção permanecerá com o arquivo, mesmo que ele é copiado para armazenamento que não está sob o controle de IT, como um serviço de armazenamento em nuvem. O mesmo ocorre para arquivos compartilhados por email, o arquivo está protegido como um anexo a uma mensagem de email, com instruções de como abrir o anexo protegido.

Ao planejar a adoção do Azure RMS recomendamos o seguinte:

- Instale o [aplicativo de compartilhamento do RMS](https://technet.microsoft.com/library/dn339006.aspx). Este aplicativo integra-se com o Office aplicativos instalando um Office suplemento para que os usuários podem facilmente proteger arquivos diretamente.
- Configurar aplicativos e serviços para oferecer suporte a Azure RMS
- Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que refletem suas necessidades de negócios. Por exemplo: um modelo de dados de secreta principais que devem ser aplicados em todos os principais segredo relacionados emails.

As organizações que são fracas na proteção de arquivo e [classificação de dados](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) podem ser mais sujeitas a perda de dados. Sem proteção de arquivo adequado, organizações não conseguirá obter percepções comerciais, monitorar abuse e impedir o acesso mal-intencionado a arquivos.

Você pode saber mais sobre Azure RMS lendo o artigo [Introdução ao gerenciamento de direitos do Azure](https://technet.microsoft.com/library/jj585016.aspx).
