<properties
   pageTitle="Infraestrutura de atualização é o seu vermelho (RHUI) | Microsoft Azure"
   description="Saiba mais sobre vermelho é o seu atualização infraestrutura (RHUI) para instâncias de Red Hat Enterprise Linux sob demanda no Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização (RHUI) é o seu vermelho para sob demanda Red Hat Enterprise Linux VMs no Azure

Máquinas virtuais criadas das sob demanda Red Hat Enterprise Linux (RHEL) imagens disponíveis no Azure Marketplace são registradas para acessar o vermelho é o seu atualização infraestrutura (RHUI) implantado no Azure.  As instâncias RHEL sob demanda tem acesso a um repositório de yum regionais e pode receber atualizações incrementais.

A lista de repositório yum, que é gerenciada por RHUI, está configurada na sua instância RHEL durante a configuração. Você não precisa fazer qualquer configuração adicional - executar `yum update` depois que sua instância RHEL estiver pronta para obter as atualizações mais recentes.

> [AZURE.NOTE] Infraestrutura RHUI Azure foi atualizada recentemente (setembro de 2016) e requer alterações na configuração do seu instâncias RHEL existentes para acesso contínuo aos RHUI Azure. Consulte a seção RHUI Azure Infrastructure Update para obter detalhes.


## <a name="rhui-azure-infrastructure-update"></a>Atualização de infraestrutura Azure RHUI
A partir de setembro de 2016, o Azure tem um novo conjunto de servidores de infraestrutura de atualização de é o seu de vermelho (RHUI). Esses servidores são implantados com o [Gerenciador de tráfego do Azure]( https://azure.microsoft.com/services/traffic-manager/) para que um único ponto de extremidade (rhui-1.micrsoft.com) pode ser usado por qualquer máquina virtual, independentemente de região. Eles também usar um certificado SSL que esteja encadeado para uma autoridade de certificação conhecido (raiz de Baltimore). Tornar essa atualização automática seria perigoso para alguns clientes que têm ACLs ou tabelas de roteamento personalizadas para os servidores de atualização RHUI, portanto, essa atualização é "opt-in." Etapas manuais para integração para esses novos servidores estão disponíveis nesta página, e um script completo para integração de forma automática (durante a verificação das etapas individuais). As novas imagens RHEL PAYG no Azure Marketplace (versões setembro de 2016 ou posteriores) automaticamente irá apontar para os novos servidores do Azure RHUI e não exigem nenhuma ação adicional.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>O novo cronograma de integração de infraestrutura RHUI do Azure

| Data | Observação |
| --- | --- |
|22 de setembro de 2016 | Os servidores RHUI e instruções de instalação disponível para uso. VMs implantadas usando o novo (setembro de 2016 com data) imagens do marketplace RHEL PAYG usará automaticamente os novos servidores RHUI, mas VMs existentes são "opt-in"
|1 de novembro de 2016 | Imagens de máquina virtual do RHEL PAYG herdadas (que usam os servidores antigos do Azure RHUI) serão removidas da Galeria Azure Marketplace
|16 de janeiro de 2017 | Os servidores antigos do Azure RHUI serão descomissionados. Atualizar todos os seus VMs de RHEL PAYG afetados desta vez para manter o acesso aos RHUI do Azure

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Os IPs para os novos servidores de distribuição de conteúdo de RHUI são

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedimento de atualização manual para usar os novos servidores do Azure RHUI

Baixar (via ondulação) a assinatura de chave pública

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verifique se a chave baixada

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Verifique a saída, verifique se `keyid` e `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Instalar a chave pública

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Baixar, verificar e instalar o cliente RPM

Download: Para RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verifique se:

```
rpm -Kv azureclient.rpm
```

Verificar a assinatura do pacote de saída é Okey

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instalar o RPM

```
sudo rpm -U azureclient.rpm
```

Após a conclusão, verifique se que você pode acessar o formulário de Azure RHUI a máquina virtual

### <a name="all-in-one-script-for-automating-the-above-task"></a>Todos-em-um script para automatizar as tarefas acima
Use o script a seguir conforme necessário para automatizar a tarefa de atualização VMs afetadas aos servidores do Azure RHUI novo.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Visão geral RHUI
[Infraestrutura de atualização é o seu vermelho](https://access.redhat.com/products/red-hat-update-infrastructure) oferece uma solução altamente escalável para gerenciar o conteúdo do repositório de yum por instâncias de nuvem Red Hat Enterprise Linux que são hospedados por provedores de nuvem certificação Red Hat. RHUI com base no projeto de pasta superior, permite que os provedores de nuvem espelhar repositório hospedados em vermelho é o seu conteúdo, criar repositórios personalizados com seu próprio conteúdo e disponibilizar esses repositórios para um grande grupo de usuários finais por meio de um sistema de balanceamento de carga de entrega conteúdo localmente.

## <a name="regions-where-rhui-is-available"></a>Regiões onde RHUI está disponível
RHUI está disponível em todas as regiões onde as imagens de sob demanda RHEL estão disponíveis. Atualmente, ele inclui todos os públicas regiões listadas na página de [Painel de status do Azure](https://azure.microsoft.com/status/) e regiões do governo do Azure conosco. Acesso RHUI para VMs provisionado de imagens do RHEL sob demanda será incluído no seu preço. Disponibilidade de nuvem nacional/regional adicional será atualizada à medida que possamos expandir a disponibilidade de sob demanda RHEL no futuro.

> [AZURE.NOTE] Acesso a RHUI hospedados Azure está limitado aos VMs nos [intervalos de IP do Microsoft Azure data center](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Obter as atualizações de outro repositório de atualização

Se você precisar obter as atualizações de um repositório de atualização diferente (em vez de RHUI hospedados Azure) você precisará cancelar o registro de suas instâncias de RHUI e registrá-las novamente com a infraestrutura de atualização desejada (como vermelho é o seu satélite ou vermelho é o seu cliente Portal CDN). Você precisará assinaturas de Red Hat apropriadas para esses serviços e registro para [Vermelho é o seu acesso à nuvem no Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para cancelar o registro RHUI e registrar novamente para o acompanhamento de infraestrutura de atualização as etapas abaixo.

1.  Editar /etc/yum.repos.d/rh-cloud.repo e alterar todos `enabled=1` para `enabled=0`. Por exemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Edite /etc/yum/pluginconf.d/rhnplugin.conf e altere `enabled=0` para `enabled=1`.
3.  Em seguida, registre a infraestrutura desejada, como o Portal do vermelho é o seu cliente. Siga o guia de solução de Red Hat em [como registrar e assinar um sistema para o Portal do vermelho é o seu cliente](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Acesse o RHUI hospedados Azure será incluída no preço de imagem RHEL pré-pago (PAYG). Cancelando o registro de uma máquina virtual RHEL de PAYG do RHUI hospedados Azure não converter a máquina virtual em trazer seu-proprietário-licença (BYOL) tipo máquina virtual e, portanto, você pode ser cobrado duplo se registrar a máquina virtual mesma com outra fonte de atualizações. 
> 
> Se você precisar usar uma infraestrutura de atualização diferente consistentemente hospedados Azure RHUI considere criar e implantar suas próprias imagens (tipo BYOL), conforme descrito no artigo [criar e baseado em carregar Red Hat máquina virtual do Azure](virtual-machines-linux-redhat-create-upload-vhd.md) .

## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual Linux do Red Hat Enterprise de aproveitar e imagem de pré-pago do Azure Marketplace RHUI hospedados Azure acesse [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Você poderá usar `yum update` na sua instância RHEL sem nenhuma instalação adicional.