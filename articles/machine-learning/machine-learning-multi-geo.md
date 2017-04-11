<properties
   pageTitle="Documentação da Ajuda de multi-geográfica | Microsoft Azure"
   description="Aprenda a criar um espaço de trabalho e publicar um serviço da web em uma região Azure diferente do Sul Central Estados Unidos (SCUS) região Azure."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Documentação da Ajuda de multi-geográfica

Este artigo descreve como você pode criar um espaço de trabalho e publicar um serviço da web em outras regiões Azure.

## <a name="create-a-workspace"></a>Criar um espaço de trabalho

1. Entre portal do Azure clássico.

2.  Clique em **+ novo** > **Serviços de dados** > **APRENDIZADO de máquina** > **criar rápida**.  Em **local** , selecione outra região, como **Sudeste Asiático**.
![Imagem de multi-geográfica ajuda 1][1]
3. Selecione o espaço de trabalho e clique em **entrar no Studio ML**.
![Imagem de multi-geográfica ajuda 2][2]

4. Agora você tem um espaço de trabalho em outra região que você pode usar como qualquer outro espaço de trabalho. Para alternar entre seus espaços de trabalho, procure o canto superior direito da tela. Clique na lista suspensa, selecione a região e selecione o espaço de trabalho. Tudo é local para a região de espaço de trabalho; Por exemplo, todos os seus serviços web criados a partir de um espaço de trabalho será na mesma região que o espaço de trabalho está localizado no.
![Imagem de multi-geográfica ajuda 3][3]

## <a name="open-an-experiment-from-gallery"></a>Abrir um experimento da Galeria

Se você abrir um experimento da galeria, você também pode selecionar quais região que você deseja copiar o experimento para.

![Imagem de multi-geográfica ajuda 4][4a]

## <a name="web-service-management"></a>Gerenciamento de serviço Web

Para programaticamente gerenciar serviços da web, como treinamento, use o endereço de específica de região:
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Preparar para uma observação

1.  Você só pode copiar experiências entre espaços de trabalho que pertencem a mesma região dessa forma. Se você precisa copiar experimento em espaços de trabalho em diferentes regiões, você pode usar o [PowerShell](http://aka.ms/amlps) commandlet [*Cópia AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) para realizar que. Outra alternativa é publicar o experimento na galeria no modo não listado, abra-a no espaço de trabalho da região outro.
2.  O seletor de região mostrará somente espaços de trabalho para uma região por vez. No futuro, você poderá ver uma lista completa de espaços de trabalho que você tem acesso em todas as regiões ao mesmo tempo.  
3.  Um espaço de trabalho gratuito ou espaço de trabalho (anônimo) acesso de convidado será criado e hospedado no Centro Sul dos EUA No futuro, você poderá criar espaços de trabalho do Access Free/convidado na região que você escolher.  
4.  Serviços Web implantados a partir de um espaço de trabalho do Sudeste Asiático também serão hospedados no sudeste asiático. No futuro, você poderá ter a flexibilidade de criação de experiências em uma região e implantando gerado pontos de extremidade de serviço web em regiões diferentes.  

## <a name="more-information"></a>Mais informações

Faça uma pergunta no [Fórum de aprendizado de máquina do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
