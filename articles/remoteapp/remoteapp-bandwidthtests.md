<properties 
    pageTitle="Azure RemoteApp - teste seu uso de largura de banda de rede com alguns cenários comuns | Microsoft Azure"
    description="Saiba quanto cenários de uso comuns que podem ajudar a calcular suas necessidades de largura de banda de rede de RemoteApp do Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - teste seu uso de largura de banda de rede com alguns cenários comuns

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Como discutimos no [uso de largura de banda de rede de estimativa Azure RemoteApp](remoteapp-bandwidth.md), a melhor maneira de descobrir qual o impacto do Azure RemoteApp à sua rede executar algumas uso testes. Executar esses testes para um período de tempo definido e medir a largura de banda necessária para cada cenário. Se você tiver o recurso, você também pode medir a variação rede de rede e perda de pacote para entender os padrões de rede que serão criados em seu ambiente específico.

    
Ao avaliar o uso de largura de banda, lembre-se de que uso varia entre diferentes usuários dentro da sua empresa. Por exemplo, autores e leitores de texto geralmente consumam menos largura de banda que os usuários que trabalham com o vídeo. Para obter melhores resultados, estudar suas necessidades de usuário e criar uma combinação dos seguintes cenários que representa melhor os usuários da sua empresa. Lembre-se de [Revisar os fatores que uso de largura de banda de impacto e experiência de usuário](remoteapp-bandwidthexperience.md) - que ajudará você a identificar os testes ideais.

Primeiro leia sobre os testes, escolha sua combinação e depois executá-las. Você pode usar a tabela a seguir para ajudar a controlar o desempenho.

>[AZURE.NOTE] Se você não pode fazer seus próprios testes de rede ou se você não tem tempo para fazer isso, confira nossas [estimativas/recomendações de largura de banda de rede básica](remoteapp-bandwidthguidelines.md). Seu quilometragem pode variar, no entanto, portanto se você *pode* executar seus próprios testes, você deve.


## <a name="the-usage-tests"></a>Os testes de uso
Cada um desses testes executar para diferentes valores de tempo e testar diferentes funções/recursos que consumir largura de banda de rede. Lembre-se de escolher a combinação de teste que melhor corresponde a seus usuários individuais da empresa.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>PowerPoint executivo/complexo - executado por 900-1000 segundos

Um usuário apresenta entre os slides de alta fidelidade de 45 a 50 usando o Microsoft Office PowerPoint no modo de tela inteira. Os slides devem conter imagens, transições (com animações) e planos de fundo com gradiente de cor que são comuns para a sua empresa. O usuário deve passam pelo menos 20 segundos em cada slide.
    
Este cenário cria tráfego intermitente, quando um slide passa para o próximo slide na apresentação.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>PowerPoint Simple - executado por ~ 620 segundos

Um usuário apresenta um arquivo de PowerPoint simples com aproximadamente 30 slides usando o Microsoft Office PowerPoint no modo de tela inteira. Os slides são mais texto exigem o cenário de PowerPoint executivo/complexo e têm mais simples planos de fundo e imagens (diagramas pretos). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - executado por aproximadamente 250 segundos

Um usuário navega na web usando o Internet Explorer. O usuário navega e percorre uma mistura de texto, imagens naturais e alguns diagramas esquemáticos. As páginas da web armazenados na unidade de disco local do servidor Host de sessão de área de trabalho remota (Host de sessão de RD) como um. Arquivo MHT. O usuário rola usando Page Up, Page Down, para cima e para baixo de chaves, com intervalos de variáveis para cada chave/tipo de rolagem:
    
    - Para baixo - 250 pressionamentos de teclas muito 500 ms
    - Page Up - 36 pressionamentos de teclas cada ms 1000
    - Para baixo - 75 pressionamentos de teclas cada 100 ms
    - Page Down - 20 pressionamentos de teclas cada 500 ms
    - Para cima - 120 pressionamentos de tecla cada 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>Documento PDF - simple - executar por ~ 610 segundos
Um usuário lê e procura um documento PDF de várias maneiras usando o Adobe Acrobat Reader. O documento deve consistir em tabelas, gráficos simples e várias fontes de texto. O documento tem páginas 35-40. O usuário pode rolar em duas taxas diferentes, com versões anteriores e encaminha, em quatro tamanhos diferentes de zoom (Ajustar à página, ajustá-la à largura, 100% e outra de sua preferência). O zoom garante que o texto (fonte) renderiza em tamanhos diferentes. Rolagem é pressionada usando o Page Up, Page Down, para cima e para baixo de chaves, com intervalos de variáveis para cada rolagem.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>Executar - misto - por ~ 320 segundos de documentos do PDF
Um usuário lê e procura um documento PDF de várias maneiras usando o Adobe Acrobat Reader. O documento consiste em várias fontes de texto, tabelas, gráficos simples e imagens de alta qualidade (incluindo fotografias). O usuário pode rolar em duas taxas diferentes, com versões anteriores e encaminha, em quatro tamanhos diferentes de zoom (Ajustar à página, ajustá-la à largura, 100% e outra de sua preferência). O zoom garante que o texto (fonte) renderiza em tamanhos diferentes. Rolagem é pressionada usando o Page Up, Page Down, para cima e para baixo de chaves, com intervalos de variáveis para cada rolagem.

### <a name="flash-video-playback---run-for-180-seconds"></a>Reprodução de vídeo Flash - executado por ~ 180 segundos
Um usuário exibe um vídeo codificado Adobe Flash incorporado em uma página da web. A página da web é armazenada no disco rígido local do servidor Host de sessão de RD. O vídeo será reproduzido dentro do Internet Explorer por um player incorporado plug-in.

Este cenário emula usuários exibindo conteúdo rich páginas da web que contém multimídia. A maioria dos dados deve reto por meio de VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word remoto digitando - executar por ~ 1800 segundos
Um usuário digita um documento em uma sessão RDP. Pressionamentos de teclas são enviados do lado do cliente através da sessão RDP a um documento no Microsoft Word em execução na sessão remota. A taxa de digitação é um caractere cada 250 ms (totais 7050 caracteres). 

Este é um dos cenários mais comuns para um trabalhador de Conhecimento. Este cenário testa a capacidade de resposta de um usuário digitar em um processador de trabalho moderno. Este cenário é sensível pequenas alterações no uso de largura de banda.

## <a name="tracking-the-test-results"></a>Controle os resultados de teste

Você pode usar a tabela a seguir para avaliar os cenários em seu ambiente. Os dados fornecidos abaixo são apenas para ilustração - pode ser muito diferente daqueles que você observar. 

Para simplificar, vamos supor que todos os cenários são testados usando a mesma resolução de tela de 1920x1080 pixels e transportes TCP em uma rede com latência (atraso) abaixo 200 ms e rede variação na marca 120 ms + de aproximadamente 1%.

Sobre a tabela:
- **Experiência de média** contém a largura de banda de rede onde a produtividade do usuário não é afetada significativamente mas não excluir eventuais problemas de áudio ou vídeo. O sistema é capaz de recuperar rapidamente aproveitando a lógica dinâmica. A tentativa de estimativas de largura de banda de rede para garantir a qualidade da experiência do usuário.
 - **Problemas de Noticeable (ponto de interrupção)** contém a largura de banda de rede onde os usuários podem notar problemas significativos em sua experiência e sua produtividade é afetada para períodos mensuráveis. Neste ponto os algoritmos RDP enfrentam e não garantem de qualidade da experiência do usuário devido a largura de banda de rede insuficiente.
 - **Recomendado** contém a largura de banda de rede recomendada para experiência boa ou excelente. É geralmente uma etapa de maior que o valor na coluna **média experiência** correspondente.
 - **Notas** incluem observações e comentários.
 
| Teste                  | Experiência média | Problemas perceptíveis (ponto de interrupção) | Largura de banda de rede recomendada | Anotações                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| Executivo/complexo PPT | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferencial    | A 1 MB/s muitas animações são perdidas                                   |
| PPT Simple            | 5 MB/s              | 256 KB/s                         | 10 MB/s                        | Em 256 KB/s os slides carregar com atraso perceptível                   |
| Internet Explorer     | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferencial    | A 1 MB/s web vídeos estão desfocados e instável, rápido rolagem tem problemas |
| PDF Simple            | 1 MB/s              | 256 KB/s                         | 5 MB/s                         | Em 256 KB/s leva algum tempo para carregar a página                       |
| PDF misto             | 1 MB/s             | 256 KB/s                         | 5 MB/s                         | Em 256 KB/s página leva uma quantidade considerável de tempo para carregar    |
| Reprodução de vídeo Flash  | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferencial    | A 1 MB/s o vídeo está granulado e alguns quadros são descartados           |
| Digitar remoto do Word    | 256 KB/s            | 128 KB/s                         | 1 MB/s                         | Em 256 KB/s usuário pode perceber o tempo entre pressionamentos de teclas             |

Para avaliar a largura de banda de rede por usuário, crie uma combinação dos cenários acima e a proporção correspondente de largura de banda necessária. Escolha o número mais alto necessário para seus cenários. Como os usuários quase nunca usam o sistema sozinho, considere alguns reserva para usuários que trabalhar simultaneamente na mesma rede.
     
## <a name="learn-more"></a>Saiba Mais
- [Estimar Azure RemoteApp uso de largura de banda de rede](remoteapp-bandwidth.md)

- [RemoteApp Azure - como largura de banda de rede e qualidade da experiência trabalhar juntos?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp largura de banda - diretrizes gerais (se você não pode testar seu próprio)](remoteapp-bandwidthguidelines.md)