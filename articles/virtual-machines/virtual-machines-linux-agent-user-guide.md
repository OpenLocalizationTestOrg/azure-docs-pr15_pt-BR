<properties 
    pageTitle="Guia de usuário do agente de Linux | Microsoft Azure" 
    description="Saiba como instalar e configurar o agente de Linux (waagent) para gerenciar a interação da sua máquina virtual com controlador de tecidos do Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Guia de usuário do agente de Linux Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introdução

O Microsoft Azure Linux Agent (waagent) gerencia Linux e FreeBSD provisionamento e interação de máquina virtual com o controlador de tecidos do Azure. Ele fornece as seguintes funcionalidades para Linux e FreeBSD IaaS implantações:

> [AZURE.NOTE] Consulte o agente do Azure Linux [Leiame](https://github.com/Azure/WALinuxAgent/blob/master/README.md) para obter detalhes adicionais.

* **Provisionamento de imagem**
  - Criação de uma conta de usuário
  - Configurando tipos de autenticação de SSH
  - Implantação de chaves públicas SSH e pares de chaves
  - Definindo o nome do host
  - O nome do host de publicação para a plataforma do DNS
  - Relatório SSH host impressão digital da chave para a plataforma
  - Gerenciamento de recursos de disco
  - Formatação e monte o disco de recurso
  - Configurando o espaço de troca

* **Rede**
  - Gerencia rotas para melhorar a compatibilidade com os servidores DHCP de plataforma
  - Garante a estabilidade do nome da interface de rede

* **Núcleo**
  - Configura NUMA virtual (desabilitar para núcleo < 2.6.37)
  - Consome entropia Hyper-V para /dev/random
  - Configura tempos limite de SCSI para dispositivo raiz (que poderia ser remoto)

* **Diagnósticos**
  - Redirecionamento de console à porta serial

* **Implantações de SCVMM**
    - Detecta e inicializa o agente VMM para Linux durante a execução em um ambiente do System Center Virtual Machine Manager 2012 R2

* **Extensão de máquina virtual**
    - Inserir componente criado pela Microsoft e parceiros em Linux VM (IaaS) para ativar o software e automação de configuração
    - Implementação de referência de extensão de máquina virtual em [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Comunicação
O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um tempo de inicialização anexado DVD para implantações de IaaS. Este DVD inclui um arquivo de configuração compatível com OVF que inclui todas as informações de provisionamento diferente os pares de chaves SSH real.
* Um ponto de extremidade TCP expor uma API REST usada para obter a implantação e configuração de topologia.


## <a name="requirements"></a>Requisitos
Os seguintes sistemas foram testados e funcionam com o agente de Linux Azure:

> [AZURE.NOTE] Esta lista pode diferir na lista oficial de sistemas suportados na plataforma Microsoft Azure, conforme descrito aqui: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Outros sistemas suportados:

* FreeBSD 10 + (agente do Azure Linux v2.0.10 +)

O agente de Linux depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Utilitários de sistema de arquivos: sfdisk, fdisk, mkfs, divididas
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: rota de ip
* Suporte do núcleo para montagem sistemas de arquivos UDF.

## <a name="installation"></a>Instalação
Instalação usando um RPM ou um pacote de DEB do repositório de pacote da sua distribuição é o método preferencial de instalar e atualizar o agente do Azure Linux. Todos os [aprovado provedores de distribuição](virtual-machines-linux-endorsed-distros.md) integrar o pacote do agente do Azure Linux suas imagens e repositórios.

Consulte a documentação do [Azure Linux agente repo no Github](https://github.com/Azure/WALinuxAgent) para opções de instalação avançada, como instalação de origem ou para locais personalizados ou prefixos.


## <a name="command-line-options"></a>Opções de linha de comando

### <a name="flags"></a>Sinalizadores

- detalhado: Aumentar nível de detalhes do comando especificado
- forçar: Ignorar confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos

- Ajuda: lista os comandos com suporte e sinalizadores.

- deprovision: tentativa de limpar o sistema e torná-lo adequado para provisionamento novamente. Essa operação excluiu o seguinte:
 * Todas as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
 * Configuração do servidor de nomes no /etc/resolv.conf
 * Senha raiz de /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
 * Concessões de cliente DHCP em cache
 * Nome de host redefine para localdomain


> [AZURE.WARNING] Desprovisionamento não garante que a imagem está desmarcada de todas as informações confidenciais e adequado para redistribuição.


- deprovision + user: executa tudo em - deprovision (acima) e também exclui a última conta de usuário provisionado (obtida /var/lib/waagent) e dados associados. Este parâmetro é ao provisionar eliminação de uma imagem que foi provisionamento anteriormente no Azure para que ele pode ser capturado e reutilizado.

- versão: exibe a versão do waagent

- serialconsole: configura GRUB para marcar ttyS0 (primeira porta serial) como o console de inicialização. Isso garante que os logs de inicialização de núcleo são enviadas para a porta serial e disponibilizados para depuração.

- daemon: executar waagent como um daemon gerenciar a interação com a plataforma. Este argumento é especificado para waagent no script de inicialização de waagent.

- Iniciar: executar waagent como um processo de plano de fundo


## <a name="configuration"></a>Configuração

Um arquivo de configuração (/ etc/waagent.conf) controla as ações de waagent. Um arquivo de configuração de exemplo é mostrado abaixo:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

As diversas opções de configuração são descritas em detalhes abaixo. Opções de configuração são de três tipos; Booliano, cadeia de caracteres ou inteiro. As opções de configuração booliana podem ser especificadas como "y" ou "n". A palavra-chave especial "Nenhum" pode ser usado para entradas de configuração de tipo alguns cadeia como detalhado abaixo.

**Provisioning.Enabled:**  
Tipo: booliana  
Padrão: y

Isso permite ao usuário habilitar ou desabilitar a funcionalidade de provisionamento no agente. Valores válidos são "y" ou "n". Se provisionamento estiver desabilitado, chaves de host e usuário SSH na imagem são preservadas e qualquer configuração especificada o Azure API de provisionamento será ignorada.

> [AZURE.NOTE] O `Provisioning.Enabled` padrões de parâmetro para "n" em imagens de nuvem Ubuntu que usam inicialização de nuvem para provisionamento.

  
**Provisioning.DeleteRootPassword:**  
Tipo: booliana  
Padrão: n

Se o conjunto, a senha raiz no arquivo sombra é apagado durante o processo de provisionamento.


**Provisioning.RegenerateSshHostKeyPair:**  
Tipo: booliana  
Padrão: y

Se o conjunto, todas as SSH host pares de chaves (ecdsa, dsa e rsa) é excluído durante o processo de provisionamento de /etc/ssh /. E um único par de chaves clara é gerado.

O tipo de criptografia para o novo par de chaves é configurável pela entrada Provisioning.SshHostKeyPairType. Observe que algumas distribuições recriará pares de chave SSH para quaisquer tipos de criptografia ausentes quando o daemon SSH é reiniciado (por exemplo, após a reinicialização).


**Provisioning.SshHostKeyPairType:**  
Tipo: cadeia de caracteres  
Padrão: rsa

Isso pode ser definido como um tipo de algoritmo de criptografia que é suportado pelo daemon SSH na máquina virtual. Os valores normalmente compatíveis são "rsa", "dsa" e "ecdsa". Observe que "putty.exe" no Windows não suporta "ecdsa". Portanto, se você pretende usar putty.exe no Windows para se conectar a uma implantação do Linux, use "rsa" ou "dsa".


**Provisioning.MonitorHostName:**  
Tipo: booliana  
Padrão: y

Se definido, waagent será monitorar máquina virtual Linux para alterações de nome do host (conforme retornado pelo comando "hostname") e atualizar automaticamente a configuração de rede na imagem para refletir a alteração. Para enviar a alteração do nome para os servidores DNS, rede será reiniciada na máquina virtual. Isso resultará em resumo perda de conectividade com a Internet.


**Provisioning.DecodeCustomData**  
Tipo: booliana  
Padrão: n

Se definido, waagent será decodificar CustomData na Base 64.


**Provisioning.ExecuteCustomData**  
Tipo: booliana  
Padrão: n

Se definido, waagent executará CustomData após a configuração.


**Provisioning.PasswordCryptId**  
Tipo: String  
Padrão: 6

Algoritmo usado pelo crypt ao gerar hash de senha.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  


**Provisioning.PasswordCryptSaltLength**  
Tipo: String  
Padrão: 10

Comprimento do valor falso aleatório usado ao gerar hash de senha.


**ResourceDisk.Format:**  
Tipo: booliana  
Padrão: y

Se definido, o disco de recursos fornecido pela plataforma será formatado e montado pelo waagent se o tipo de sistema de arquivos solicitado pelo usuário em "ResourceDisk.Filesystem" for algo diferente de "ntfs". Uma única partição do tipo Linux (83) estarão disponível no disco. Observe que essa partição não seja formatada se ele pode ser montado com êxito.


**ResourceDisk.Filesystem:**  
Tipo: cadeia de caracteres  
Padrão: ext4

Especifica o tipo de sistema de arquivos para o disco de recurso. Valores suportados variam conforme a distribuição Linux. Se a cadeia de caracteres é X, em seguida, mkfs. X deve estar presente na imagem Linux. Imagens de 11 SLES normalmente devem usar 'ext3'. Imagens de FreeBSD devem usar 'ufs2' aqui.


**ResourceDisk.MountPoint:**  
Tipo: cadeia de caracteres  
Padrão: recurso/manutenção / 

Especifica o caminho em que o disco de recurso é montado. Observe que o disco de recurso é um disco *temporário* e pode ser esvaziado quando a máquina virtual será desprovisionada.


**ResourceDisk.MountOptions**  
Tipo: cadeia de caracteres  
Padrão: nenhum

Especifica opções de montagem do disco a serem transmitidos ao comando montagem -o. Esta é uma lista de separados por vírgulas de valores, ex. 'nodev, nosuid'. Consulte mount(8) para obter detalhes.


**ResourceDisk.EnableSwap:**  
Tipo: booliana  
Padrão: n

Se definido, um arquivo de troca (/ swapfile) é criado no disco recurso e adicionado para o espaço de troca de sistema.


**ResourceDisk.SwapSizeMB:**  
Tipo: inteiro  
Padrão: 0

O tamanho do arquivo trocar em megabytes.


**Logs.Verbose:**  
Tipo: booliana  
Padrão: n

Se o conjunto de detalhamento de log é aumentado. Waagent registra /var/log/waagent.log e utiliza a funcionalidade de logrotate de sistema para girar logs.


**SISTEMA OPERACIONAL. EnableRDMA**  
Tipo: booliana  
Padrão: n

Se definido, o agente tentará instalar e, em seguida, carregar um driver de núcleo RDMA que corresponde à versão do firmware do hardware subjacente.


**SISTEMA OPERACIONAL. RootDeviceScsiTimeout:**  
Tipo: inteiro  
Padrão: 300

Isso configura o tempo limite de SCSI em segundos as unidades de disco e dados do sistema operacional. Se não estiver definida, o sistema de padrões são usados.


**SISTEMA OPERACIONAL. OpensslPath:**  
Tipo: cadeia de caracteres  
Padrão: nenhum

Isso pode ser usado para especificar um caminho alternativo para o openssl binário a ser usado para operações de criptografia.


**HttpProxy.Host, HttpProxy.Port**  
Tipo: cadeia de caracteres  
Padrão: nenhum

Se definido, o agente usará este servidor proxy para acessar a internet. 


## <a name="ubuntu-cloud-images"></a>Imagens de nuvem Ubuntu

Observe que imagens de nuvem Ubuntu utilizar [inicialização de nuvem](https://launchpad.net/ubuntu/+source/cloud-init) para realizar várias tarefas de configuração que caso contrário poderiam ser gerenciadas pelo agente de Linux do Azure.  Observe as seguintes diferenças:


- **Provisioning.Enabled** padrão "n" em imagens de nuvem Ubuntu que usar inicialização de nuvem para executar tarefas de provisionamento.

- Os seguintes parâmetros de configuração não têm nenhum efeito sobre imagens de nuvem Ubuntu que use inicialização de nuvem para gerenciar o disco de recurso e troque espaço:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consulte os seguintes recursos para configurar o ponto de montagem do recurso disco e troque espaço em imagens de nuvem Ubuntu durante a configuração:

 - [Ubuntu Wiki: Configurar partições de troca](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Inserindo dados personalizadas em uma máquina Virtual Azure](virtual-machines-windows-classic-inject-custom-data.md)

