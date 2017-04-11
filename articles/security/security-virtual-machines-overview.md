<properties
   pageTitle="Visão geral sobre a segurança do Azure máquinas virtuais | Microsoft Azure"
   description=" Azure máquinas virtuais oferecem a flexibilidade da virtualização sem precisar comprar e manter o hardware físico que é executado na máquina virtual.  Este artigo fornece uma visão geral do núcleo recursos de segurança do Azure que podem ser usados com máquinas virtuais do Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Visão geral sobre segurança do Azure máquinas virtuais

Azure máquinas virtuais permite que você implantar uma ampla variedade de soluções de computação de forma ágil. Com suporte para Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e serviços do Azure BizTalk, você pode implantar qualquer carga de trabalho e qualquer idioma em praticamente qualquer sistema operacional.

Uma máquina virtual Azure fornece a flexibilidade da virtualização sem precisar comprar e manter o hardware físico que é executado na máquina virtual.  Você pode criar e implantar seus aplicativos com a garantia de que seus dados estão protegidos e seguros em nossos data centers altamente seguros.

Com o Azure, você pode criar soluções compatíveis com segurança avançada, que:

- Proteger suas máquinas virtuais contra vírus e malware
- Criptografar dados confidenciais
- Proteger o tráfego de rede
- Identificar e detectar ameaças
- Atender aos requisitos de conformidade

O objetivo deste artigo é fornecer uma visão geral do núcleo do recursos de segurança do Azure que podem ser usados com máquinas virtuais. Também fornecemos links para artigos que fornecem detalhes de cada recurso para saber mais.  

Os recursos de segurança do Azure Virtual Machine principais a serem abordados neste artigo:

- Antimalware
- Módulo de segurança de hardware
- Criptografia de disco de máquina virtual
- Backup de máquinas virtuais
- Recuperação de Site Azure
- Rede virtual
- Gerenciamento de política de segurança e relatórios
- Conformidade

## <a name="antimalware"></a>Antimalware

Com o Azure, você pode usar o software de antimalware de fornecedores de segurança como Microsoft, Symantec, antivírus, McAfee e Kaspersky proteger suas máquinas virtuais arquivos mal-intencionados, adwares e outras ameaças. Consulte a seção saber mais abaixo para localizar artigos sobre o parceiro soluções.

Antimalware da Microsoft para serviços de nuvem do Azure e máquinas virtuais é um recurso de proteção em tempo real que ajudam a identificar e remover vírus, spyware e outros softwares mal-intencionados.  Antimalware da Microsoft fornece configuráveis alertas quando conhecido software mal-intencionado ou indesejado tenta instalar ou executar nos sistemas Azure.

Antimalware da Microsoft é uma solução de único agente para aplicativos e ambientes de locatário, projetados para ser executado em segundo plano sem a intervenção. Você pode implantar proteção com base nas necessidades da sua cargas de trabalho do aplicativo, com qualquer básica seguro por padrão ou configuração personalizada, incluindo monitoramento de antimalware avançada.

Quando você implantar e ativar Antimalware da Microsoft, os seguintes recursos principais estão disponíveis:

- Proteção em tempo real - atividade de monitores em serviços de nuvem e nas máquinas virtuais para detectar e impedir a execução de malware.
- Verificação agendada - periodicamente efetua varredura direcionados para detectar malware, incluindo ativamente programas em execução.
- Correção de malware - automaticamente age em malware detectado, como excluir ou colocar em quarentena arquivos mal-intencionados e limpando entradas de registro mal-intencionados.
- Atualizações de assinatura - automaticamente instala as últimas assinaturas de proteção (definições de vírus) para garantir a proteção está atualizado em uma frequência predeterminada.
- Atualizações de mecanismo antimalware – automaticamente atualiza o mecanismo Antimalware da Microsoft.
- Atualizações de plataforma de antimalware – automaticamente atualiza a plataforma de Antimalware da Microsoft.
- Proteção ativa - relatórios aos metadados de telemetria Azure sobre ameaças detectadas e recursos suspeitos para garantir resposta rápida e permite o fornecimento de assinatura síncrono em tempo real através do Microsoft Active proteção (mapas do sistema).
- Exemplos de relatório - fornece relatórios e exemplos para o serviço de Antimalware da Microsoft para ajudar a refinar o serviço e habilite a solução de problemas.
- Exclusões – permite que o aplicativo e administradores de serviços para configurar certos arquivos, processos e unidades para excluí-los de proteção e varredura de desempenho e outros motivos.
- Coleta de eventos de antimalware - registra a integridade do serviço de antimalware, atividades suspeitas e ações de correção tomadas no log de eventos do sistema operacional e reúne-las em conta de armazenamento do Azure do cliente.

Saiba mais: para saber mais sobre o software de antimalware para proteger suas máquinas virtuais, consulte:

- [Antimalware da Microsoft para serviços de nuvem Azure e máquinas virtuais](../security/azure-security-antimalware.md)
- [Implantar soluções de Antimalware no Azure máquinas virtuais](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Como instalar e configurar tendência Micro profunda Security como um serviço em uma máquina virtual Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Como instalar e configurar a proteção de ponto de extremidade do Symantec em uma máquina virtual Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Novas opções de Antimalware para proteção de máquinas virtuais Azure – proteção de ponto de extremidade do McAfee](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Soluções de segurança do Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Módulo de segurança de hardware

Criptografia e autenticação não melhorar a segurança, a menos que as próprias chaves protegidas. Você pode simplificar o gerenciamento e a segurança de seus segredos críticos e chaves armazenando-os em Cofre de chave do Azure. Chave cofre fornece a opção para armazenar suas chaves em módulos de segurança de hardware (HSMs) certificados para FIPS 140-2 nível 2 padrões. Suas chaves de criptografia do SQL Server para backup ou [criptografia transparente de dados](https://msdn.microsoft.com/library/bb934049.aspx) podem ser todas armazenadas no cofre de chave com quaisquer teclas ou segredos dos seus aplicativos. Permissões e acesso a esses itens protegidos são gerenciadas por meio do [Active Directory do Azure](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba Mais:

- [O que é Azure chave cofre?](../key-vault/key-vault-whatis.md)
- [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md)
- [Blog de chave cofre Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Criptografia de disco de máquina virtual

Criptografia de disco Azure é um novo recurso que permite criptografar discos do Windows e Linux Azure Virtual Machine. Criptografia de disco Azure usa o recurso [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso de [dm crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume do sistema operacional e os discos de dados.

A solução é integrada ao Azure chave cofre para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e segredos em sua assinatura de chave cofre, garantindo que todos os dados na máquina virtual discos são criptografados inativos no seu armazenamento do Azure.

Saiba Mais:

- [Criptografia de disco Azure para Windows e Linux IaaS VMs](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Criptografia de disco Azure para Linux e máquinas virtuais do Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Criptografar uma máquina virtual](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Backup de máquinas virtuais

Backup Azure é uma solução flexível que protege seus dados de aplicativo com zero investimento de capital e custos operacionais mínimo. Erros do aplicativo podem danificar seus dados e erros humanos podem introduzir bugs em seus aplicativos. Com o Backup do Azure, suas máquinas virtuais executando o Windows e Linux são protegidas.

Saiba Mais:

- [O que é Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
- [Caminho de aprendizagem de Backup Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Serviço de Backup Azure - perguntas Frequentes](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Recuperação de Site Azure

Uma parte importante da estratégia BCDR da sua organização é descobrir como manter-se em aplicativos e cargas de trabalho corporativas e execução quando ocorrem interrupções planejadas e. Recuperação de Site Azure ajuda coordenar replicação, failover e recuperação de cargas de trabalho e aplicativos para que elas fiquem disponíveis de um local secundário se seu local principal falhar.

Recuperação de site:

- **Simplifica sua estratégia BCDR** — recuperação de Site facilita a lidar com replicação, failover e recuperação de várias cargas de trabalho de negócios e aplicativos de um único local. Recuperação de site organiza replicação e failover, mas não interceptar seus dados de aplicativo ou tiver qualquer informação sobre ele.
- **Replicação flexível fornece** — usando a recuperação do Site, você pode replicar cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos Windows/Linux.
- **Recuperação e oferece suporte a failover** — recuperação de Site fornece failovers de teste para oferecer suporte a produtos de recuperação de desastres sem afetar os ambientes de produção. Você também pode executar failovers planejadas com uma perda de dados nula para interrupções esperadas ou failovers planejadas com perda de dados mínima (dependendo da frequência de replicação) para desastres inesperados. Após o failover, você pode failback aos seus sites principais. Recuperação de site oferece planos de recuperação que podem incluir scripts e pastas de trabalho do Azure automação, para que você possa personalizar failover e recuperação de aplicativos de vários níveis.
- **Centro de dados secundário elimina** — você pode replicar para um site local secundário ou Azure. Usando o Azure como um destino para recuperação elimina o custo e a complexidade de manutenção de um site secundário. Dados duplicados serão armazenados em armazenamento do Azure.
- **Integra-se com as tecnologias BCDR existentes** — parceiros de recuperação de Site com outros recursos BCDR do aplicativo. Por exemplo, você pode usar a recuperação de Site para proteger o back-end do SQL Server de cargas de trabalho corporativos. Isso inclui suporte nativo para o SQL Server AlwaysOn gerenciar o failover dos grupos de disponibilidade.

Saiba Mais:

- [O que é recuperação de Site do Azure?](../site-recovery/site-recovery-overview.md)
- [Como funciona a recuperação do Site Azure?](../site-recovery/site-recovery-components.md)
- [O que cargas de trabalho são protegidas por recuperação de Site do Azure?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Rede virtual

Máquinas virtuais precisam de conectividade de rede. Para oferecer suporte a esse requisito, o Azure requer máquinas virtuais estar conectado a uma rede Virtual do Azure. Uma rede Virtual do Azure é uma construção lógica criada na parte superior da estrutura de rede Azure física. Cada rede Virtual do Azure lógico está isolada de todas as outras redes Virtual do Azure. Esse isolamento ajuda a garantir que o tráfego de rede nas implantações não é acessível para outros clientes do Microsoft Azure.

Saiba Mais:

- [Visão geral de segurança de rede Azure](security-network-overview.md)
- [Visão geral de rede virtual](../virtual-network/virtual-networks-overview.md)
- [Recursos de rede e parceiros para cenários empresariais](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gerenciamento de política de segurança e relatórios

O Centro de segurança do Azure ajuda você a evitar, detectar e responder a ameaças e fornece que maior visibilidade e controle sobre, a segurança dos seus recursos Azure. Ele fornece o monitoramento e a política de gerenciamento de segurança integrada em suas assinaturas Azure, ajuda a detectar ameaças que poderiam ir percebidas e funciona com uma enorme variedade de soluções de segurança.

O Centro de segurança do Azure ajuda você a otimizar e monitorar a segurança de máquina virtual por:

- Fornecendo de máquina virtual [recomendações de segurança](../security-center/security-center-recommendations.md) como aplicar atualizações do sistema, configurar pontos de extremidade de ACLs, habilitar antimalware, habilitar grupos de segurança de rede e aplicar criptografia de disco.
- Monitorando o estado de suas máquinas virtuais

Saiba Mais:

- [Introdução à Central de segurança do Azure](../security-center/security-center-intro.md)
- [Perguntas frequentes sobre o Centro de segurança do Azure](../security-center/security-center-faq.md)
- [Planejamento do Centro de segurança do Azure e operações](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade

Azure máquinas virtuais é certificado para FISMA, FedRAMP, HIPAA, PCI DSS nível 1 e outros programas de conformidade fundamental. Essa certificação torna mais fácil para seus próprios aplicativos do Azure atender aos requisitos de conformidade e para sua empresa a uma ampla variedade de requisitos regulamentares locais e internacionais de endereço.

Saiba Mais:

- [Central de confiabilidade do Microsoft: conformidade](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Nuvem confiável: Microsoft Azure segurança, privacidade e conformidade](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
