<properties
    pageTitle="Aprovado distribuições do Linux | Microsoft Azure"
    description="Saiba mais sobre Linux em distribuições aprovado Azure, incluindo diretrizes para Ubuntu, OpenLogic, Oracle e SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux em distribuições aprovado do Azure

> [AZURE.NOTE] Se você tiver alguns momentos, ajude-na melhorar a documentação de máquina virtual do Azure Linux fazendo esta [pesquisa rápida](https://aka.ms/linuxdocsurvey) de suas experiências. Cada resposta nos ajudam a ajuda você executar seu trabalho.

As imagens de Linux na Galeria de Azure ou Marketplace são fornecidas por um número de parceiros e estamos trabalhando com vários comunidades de Linux para adicionar tipos ainda mais a lista de distribuição aprovado. Enquanto isso, para as distribuições não está disponíveis na Galeria poderá sempre trazer-Your-proprietário-Linux seguindo as diretrizes [nesta](virtual-machines-linux-classic-create-upload-vhd.md)página.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>Distribuições suportadas & versões ##

A tabela a seguir lista as versões que têm suporte no Azure e distribuições Linux. Consulte também [oferece suporte para imagens de Linux no Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) para obter informações mais detalhadas.

Os drivers de serviços de integração de Linux (LIS) para Hyper-V e Azure são módulos de núcleo que Microsoft contribui diretamente para o núcleo Linux upstream.  Os drivers de lista com ou são criados em núcleo da distribuição por padrão ou para antigos RHEL/CentOS-based distribuições estão disponíveis como um download separado [aqui](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Consulte [Este artigo](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os drivers de lista com.

O agente de Linux Azure já vem pré-instalado nas imagens da Galeria do Azure e normalmente está disponível no repositório de pacote da distribuição.  Código-fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).

Distribuição|Versão|Drivers|Agente
---|---|---|---
CentOS por OpenLogic | CentOS 6.3 +, 7.0 + | CentOS 6.3: [lista com baixar](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: No núcleo | Pacote: Em [OpenLogic repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | No núcleo | Código-fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7,9 +, 8.2 + | No núcleo | Pacote: no repo em "waagent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4 +, 7.0 + | No núcleo | Pacote: no repo em "WALinuxAgent" <br/>Código-fonte: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux | RHEL 6.7 +, 7.1 + | No núcleo|Pacote: no repo em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Enterprise SUSE Linux | SLES 11 SP4, SLES 12 SP1 + e <p> SLES para o SAP 11 SP3 + | No núcleo | Pacote: Na [Nuvem: ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repo em "python azure-agente" <br/>Código-fonte: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | No núcleo | Pacote: Na [Nuvem: ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repo em "python azure-agente" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16.10 | No núcleo | Pacote: no repo em "walinuxagent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Parceiros

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic é um provedor líder de soluções de código-fonte aberto corporativo para a nuvem e do data center. OpenLogic ajuda centenas de empresas líderes em uma ampla gama de setores com segurança adquirir, suporte e controlar o software de fonte aberta. OpenLogic oferece suporte técnico de nível comercial e indenizações para 600 pacotes de código-fonte aberto feito pela OpenLogic especialista comunidade, incluindo o suporte de nível empresarial para CentOS bem como sendo o parceiro de inicialização para fornecer imagens baseadas em CentOS no Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/Running-coreos/Cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

No site CoreOS:

*CoreOS destina-se a segurança, consistência e confiabilidade. Em vez de instalar pacotes via yum ou chance, CoreOS usa Linux contêineres para gerenciar seus serviços de nível superior de abstração. Código do serviço um único e todas as dependências são empacotadas em um contêiner que pode ser executado em um ou vários máquinas CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/Debian-images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ é uma empresa de serviços, especializado em desenvolvimento e implementação de soluções profissionais por meio do uso software gratuito e de consultoria independente. Como principais especialistas em Abrir origem, Credative tem reconhecimento internacional com vários departamentos de TI usando seu suporte. Em conjunto com a Microsoft, Credativ está atualmente Preparando imagens Debian correspondentes Debian 8 (Jessie) e Debian antes de 7 (Wheezy), que são especialmente projetado para execução no Azure e pode ser gerenciado facilmente por meio da plataforma. Credativ suportam também a manutenção de longo prazo e atualizando das imagens Debian Azure por meio de seus centros de suporte de origem de abrir.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/Cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Estratégia da Oracle é oferecem um portfólio de soluções para nuvens pública e privada, dando clientes opção e flexibilidade em como implantar o software Oracle em nuvens Oracle, bem como outras nuvens.  Parceria da Oracle com a Microsoft permite aos clientes implantar software Oracle no nuvens pública e privada de Microsoft com a confiança de certificação e suporte da Oracle.  Compromisso e investimento em soluções de nuvem pública e privada Oracle da Oracle é alterada.

### <a name="red-hat"></a>É o seu vermelho
[http://www.redhat.com/en/Partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

O provedor do mundo líderes de soluções de código-fonte aberto, Red Hat ajuda a mais de 90% das empresas da Fortune 500 resolver desafios de negócios, alinhar sua estrutura de TI e estratégias de negócios e se preparar para o futuro da tecnologia. Red Hat faz isso fornecendo soluções seguras por meio de um modelo de negócios aberto e um modelo de assinatura acessível, previsíveis.

### <a name="suse"></a>SUSE
[http://www.SuSE.com/SuSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server no Azure é uma plataforma comprovada que fornece confiabilidade superior e segurança para computação em nuvem. Plataforma de Linux versátil do SUSE integra-se perfeitamente com serviços de nuvem Azure para fornecer um ambiente de nuvem facilmente gerenciável. E com mais de 9,200 aplicativos de certificada mais 1.800 fornecedores de software independentes para SUSE Linux Enterprise Server, SUSE garante que cargas de trabalho com suporte no Centro de dados podem ser implantadas com confiança no Azure.

### <a name="canonical"></a>Canônico
[http://www.Ubuntu.com/Cloud/Azure](http://www.ubuntu.com/cloud/azure)

Engenharia canônica e gestão de comunidade aberta unidade sucesso do Ubuntu no cliente, servidor e computação em nuvem, incluindo serviços de nuvem pessoal para consumidores. Visão da Canonical de uma plataforma unificada gratuita no Ubuntu, do telefone para a nuvem, com uma família de interfaces coerentes para telefone, tablet, TV e área de trabalho, torna Ubuntu a primeira opção para instituições diversas provedores de nuvem pública fabricantes do eletrônicos e um favorito entre especialistas em tecnologia individuais.

Com os desenvolvedores e centros de engenharia do mundo, Canonical está exclusivamente posicionado para parceiros com tomadores de hardware, provedores de conteúdo e desenvolvedores de software para trazer Ubuntu soluções no mercado, de PCs para servidores e dispositivos portáteis.

