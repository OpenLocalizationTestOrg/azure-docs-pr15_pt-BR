<properties
    pageTitle="Perguntas Frequentes de serviços de nuvem | Microsoft Azure"
    description="Perguntas frequentes sobre os serviços de nuvem."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>Perguntas Frequentes de serviços de nuvem
Este artigo responde algumas perguntas frequentes sobre os serviços de nuvem do Microsoft Azure. Você também pode visitar o [Perguntas Frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais de suporte e preços Azure. Você também pode consultar a [página de tamanho de máquina virtual de serviços de nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

## <a name="certificates"></a>Certificados

### <a name="where-should-i-install-my-certificate"></a>Onde posso instalar o meu certificado?

- **Meu**  
Certificado de aplicativo com chave privada (\*. pfx, \*P12).

- **AUTORIDADE DE CERTIFICAÇÃO**  
Todos os certificados intermediários vá nesse armazenamento (política e Sub autoridades de certificação).

- **RAIZ**  
Autoridade de certificação raiz armazenar, para que seu certificado de autoridade de certificação raiz principal deve ir aqui.

### <a name="i-cant-remove-expired-certificate"></a>Não é possível remover o certificado expirado

Azure impede que você remover um certificado enquanto ele está em uso. Você precisa exclua a implantação que usa o certificado ou atualizar a implantação com um certificado diferente ou renovado.

### <a name="delete-an-expired-certificate"></a>Excluir um certificado expirado

Como o certificado não estiver em uso, você pode usar o cmdlet do PowerShell [AzureCertificate remover](https://msdn.microsoft.com/library/azure/mt589145.aspx) para remover um certificado.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Posso ter expirado nomeados gerenciamento de serviço do Windows Azure para extensões de certificados

Esses certificados são criados sempre que uma extensão é adicionada ao serviço de nuvem como a extensão de área de trabalho remota. Esses certificados são usados apenas para criptografar e descriptografar a configuração particular da extensão. Não importa se esses certificados expirarem. A data de expiração não está marcada.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Certificados para excluiu continuam reaparecendo

Eles continuam reaparecendo provavelmente devido a uma ferramenta que você está usando, como Visual Studio. Sempre que você se reconectar com uma ferramenta que está usando um certificado, ele será novamente carregado Azure.

### <a name="my-certificates-keep-disappearing"></a>Meus certificados mantém desaparecer

Quando a instância de máquina virtual reciclagem, todas as alterações locais são perdidas. Use uma [tarefa de inicialização](cloud-services-startup-tasks.md) para instalar certificados na máquina virtual sempre que a função for iniciado.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Não consigo encontrar meus certificados de gerenciamento no portal

[Certificados gerenciamento](..\azure-api-management-certs.md) só estão disponíveis no Portal de clássico do Azure. O portal do Azure atual não usa certificados de gerenciamento. 

### <a name="how-can-i-disable-a-management-certificate"></a>Como posso desabilitar um certificado de gerenciamento?

[Certificados gerenciamento](..\azure-api-management-certs.md) não pode ser desabilitado. Excluí-las por meio do Portal de clássico do Azure quando não quiser que elas mais ser usada.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Como posso criar um certificado SSL para um endereço IP específico?

Siga as instruções em [criar um tutorial de certificado](cloud-services-certs-create.md). Use o endereço IP como o nome de DNS.

## <a name="security"></a>Segurança

### <a name="disable-ssl-30"></a>Desative o SSL 3.0

Para desativar o SSL 3.0 e usar a segurança de TLS, crie uma tarefa de inicialização que é documentada nesta postagem de blog: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Escala de um serviço de nuvem

### <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar além X instâncias

Sua assinatura do Azure tem um limite no número de cores que você pode usar. Dimensionamento não funcionará se você usou todas as cores disponíveis. Por exemplo, se você tiver um limite de 100 cores, isso significa que você pode ter 100 instâncias de máquina virtual A1 dimensionados para o seu serviço de nuvem ou A2 50 dimensionados instâncias de máquina virtual.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não é possível reservar um IP em um serviço de nuvem multi-VIP

Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP está ativada. Em segundo lugar, certifique-se de que você está usando IPs reservada para ter o trabalho as implantações de estágios e de produção. **Não** altere as configurações enquanto a implantação está atualizando.

