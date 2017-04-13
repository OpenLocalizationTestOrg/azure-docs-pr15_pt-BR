<properties
    pageTitle="Infraestrutura Diretrizes de nomenclatura | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de design e implementação chaves para nomear nos serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Diretrizes de nomenclatura de infraestrutura

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Este artigo aborda Noções básicas sobre como abordar convenções de nomenclatura para todos os diversos recursos de Azure criar um conjunto de recursos lógico e facilmente identificável em seu ambiente.

## <a name="implementation-guidelines-for-naming-conventions"></a>Diretrizes de implementação para convenções de nomenclatura

Decisões:

- Quais são suas convenções de nomenclatura para recursos Azure?

Tarefas:

- Defina os afixos usar em seus recursos para manter a consistência.
- Defina nomes de conta de armazenamento atribuídos a exigência para que eles possam ser exclusivo.
- A convenção de nomenclatura para ser usado e distribua para todas as partes envolvidas garantir a consistência em implantações do documento.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Você deve ter uma boa convenção de nomenclatura no lugar antes de criar qualquer coisa no Azure. Uma convenção de nomenclatura garante que todos os recursos tenham um nome previsível, que ajuda a reduzir a carga administrativa associada ao gerenciamento desses recursos.

Você pode escolher acompanhar um conjunto específico de convenções de nomenclatura definidas para sua organização inteira ou para uma conta ou inscrição Azure específica. Embora seja fácil para as pessoas em organizações estabelecer regras implícitas ao trabalhar com recursos Azure, quando uma equipe precisa trabalhar em um projeto no Azure, esse modelo não se adapta bem.

Concorda em um conjunto de convenções de nomenclatura antecipadamente. Há algumas considerações sobre convenções de nomenclatura que recortar em conjuntos de regras.

## <a name="affixes"></a>Afixos

Como você buscam para definir uma convenção de nomenclatura, uma decisão vem como para se o afixo está em:

- O início do nome (prefixo)
- O final do nome (sufixo)

Por exemplo, aqui estão dois nomes possíveis para um grupo de recursos usando o `rg` fixar:

- RG-Web App (prefixo)
- Web App-Rg (sufixo)

Afixos podem se referir a diferentes aspectos que descrevem os recursos específicos. A tabela a seguir mostra alguns exemplos normalmente usados.

| Aspecto                               | Exemplos                                                               | Anotações                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Ambiente                          | produto de desenvolvimento, stg,                                                         | Dependendo do nome de cada ambiente e finalidade.                                                     |
| Local                             | usw (Oeste EUA), use (Leste dos EUA 2)                                         | Dependendo da região do data center ou a região da organização.                               |
| Componente do Azure, serviço ou produto | RG para o grupo de recursos, VNet para rede virtual                        | Dependendo do produto para o qual o recurso oferece suporte.                                          |
| Função                                 | SQL, ora, sp, iis                                                      | Dependendo da função da máquina virtual.                                                              |
| Instância                             | 01, 02, 03, etc.                                                       | Para os recursos que têm mais de uma instância. Por exemplo, balanceamento de carga servidores web em um serviço na nuvem. |


Ao estabelecer convenções de nomenclatura, certifique-se de que eles descreva claramente quais afixos usar para cada tipo de recurso e em qual posição (sufixo de vs prefixo).

## <a name="dates"></a>Datas

Muitas vezes é importante determinar a data de criação do nome de um recurso. Recomendamos o formato de data AAAAMMDD. Esse formato garante que não apenas a data completa é registrada, mas também que dois recursos cujos nomes diferem somente na data é classificada em ordem alfabética e em ordem cronológica ao mesmo tempo.

## <a name="naming-resources"></a>Recursos de nomenclatura

Definir cada tipo de recurso na convenção de nomenclatura, que devem ter regras que definem como atribuir nomes a cada recurso que é criado. Essas regras deverão se aplicar a todos os tipos de recursos, por exemplo:

- Assinaturas
- Contas
- Contas de armazenamento
- Redes virtuais
- Sub-redes
- Conjuntos de disponibilidade
- Grupos de recursos
- Máquinas virtuais
- Pontos de extremidade
- Grupos de segurança de rede
- Funções

Para garantir que o nome fornece informações suficientes para determinar qual recurso refere-se, você deve usar nomes descritivos.

## <a name="computer-names"></a>Nomes de computador

Quando você cria uma máquina virtual (VM), Microsoft Azure requer um máquina virtual nome de até 15 caracteres que é usado para o nome do recurso. Azure usa o mesmo nome para o sistema operacional instalado na VM. No entanto, esses nomes podem não ser sempre a mesma.

No caso de uma máquina virtual é criada a partir de um arquivo de imagem. vhd que já contém um sistema operacional, o nome da máquina virtual no Azure pode diferir sistema operacional da máquina virtual nome do computador. Essa situação pode adicionar um nível de dificuldade para gerenciamento de máquina virtual, que, portanto, não recomendamos. Atribua o recurso de máquina virtual do Azure o mesmo nome que o nome do computador que você atribui ao sistema operacional da máquina que virtual.

Recomendamos que o nome de máquina virtual do Azure é a mesma que o nome do computador de sistema operacional subjacente.

## <a name="storage-account-names"></a>Nomes de conta de armazenamento

Contas de armazenamento têm regras especiais que regem seus nomes. Você só pode usar letras minúsculas e números. Consulte [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para obter mais informações. Além disso, o nome da conta de armazenamento, juntamente com core.windows.net, deve ser um nome DNS globalmente válido e exclusivo. Por exemplo, se a conta de armazenamento é chamada mystorageaccount, os seguintes nomes DNS resultantes devem ser exclusivos:

- mystorageaccount.blob.Core.Windows.NET
- mystorageaccount.Table.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Próximas etapas
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 