<properties
    pageTitle="Comparando imagens personalizadas e fórmulas nos exercícios DevTest | Microsoft Azure"
    description="Saiba mais sobre as diferenças entre imagens personalizadas e fórmulas como bases de máquina virtual para que você possa decidir qual deles mais adequado para seu ambiente."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparando imagens personalizadas e fórmulas nos DevTest exercícios

## <a name="overview"></a>Visão geral
[Imagens personalizadas](./devtest-lab-create-template.md) e [as fórmulas](./devtest-lab-manage-formulas.md) podem ser usados como bases para [criada novas VMs](./devtest-lab-add-vm-with-artifacts.md). No entanto, a chave distinção entre imagens personalizadas e fórmulas é que uma imagem personalizada é simplesmente uma imagem com base em um VHD, enquanto uma fórmula é uma imagem com base nas configurações de *inclusão no* pré-configurado VHD - como o tamanho da máquina virtual, uma rede virtual e sub-rede, artefatos e assim por diante. Estas configurações predefinidas estão configuradas com valores padrão que podem ser substituídos no momento da criação de máquina virtual. Este artigo explica algumas das vantagens (profissionais) e desvantagens (contras) para usar imagens personalizadas versus usando fórmulas.
 
## <a name="custom-image-pros-and-cons"></a>Contras e profissionais de imagem personalizada
Imagens personalizadas fornecem um estático, imutável criem VMs de um ambiente desejado. 

**Profissionais**
- Provisionamento de máquina virtual a partir de uma imagem personalizada é rápida como nada muda após a máquina virtual é girada para cima da imagem. Em outras palavras, não há nenhuma configuração Aplicar conforme a imagem personalizada é simplesmente uma imagem sem configurações. 
- VMs criadas a partir de uma única imagem personalizada são idênticas.

**Contras**
- Se você precisa atualizar algum aspecto da imagem personalizada, a imagem deve ser recriada.  

## <a name="formula-pros-and-cons"></a>Fórmulas prós e contras
Fórmulas oferecem uma maneira dinâmica para criar VMs de configuração/configurações desejadas.

**Profissionais**
- Alterações no ambiente podem ser capturadas dinamicamente via artefatos. Por exemplo, se quiser uma máquina virtual instalada com os bits mais recentes do seu pipeline de lançamento ou inscrever-se o código mais recente do seu repositório, você pode simplesmente especificar um artefato que implanta os bits mais recentes ou inscreve o código mais recente na fórmula juntamente com uma imagem de base de destino. Sempre que esta fórmula é usada para criar VMs, o mais recente bits/código são implantadas/inscrito para a máquina virtual. 
- Fórmulas podem definir configurações padrão que não podem fornecer imagens personalizadas - como tamanhos de máquina virtual e configurações de rede virtual. 
- As configurações salvas em uma fórmula são mostradas como valores padrão, mas podem ser modificadas quando a máquina virtual é criada. 

**Contras**
- Criando uma máquina virtual a partir de uma fórmula pode levar mais tempo do que criando uma máquina virtual a partir de uma imagem personalizada.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens no blog relacionados

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

