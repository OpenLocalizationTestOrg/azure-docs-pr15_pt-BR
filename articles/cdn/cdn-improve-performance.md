<properties
    pageTitle="Melhorar o desempenho compactando arquivos em Azure CDN | Microsoft Azure"
    description="Aprenda a melhorar a velocidade de transferência de arquivo e aumenta o desempenho de carga de página ao compactar seus arquivos no Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="improve-performance-by-compressing-files"></a>Melhorar o desempenho compactando arquivos

A compactação é um método simple e eficiente para melhorar a velocidade de transferência de arquivo e aumentar o desempenho de carga de página, reduzindo o tamanho do arquivo antes de serem enviado do servidor. Ele reduz os custos de largura de banda e fornece uma experiência mais responde para seus usuários.

Há duas maneiras de ativar a compactação:

- Você pode habilitar a compactação no seu servidor de origem, no qual caso a CDN passam os arquivos compactados e entregar arquivos compactados para os clientes que solicitem-los.
- Você pode habilitar a compactação diretamente nos servidores de borda CDN, no qual caso a CDN irá compactar os arquivos e servi-lo aos usuários finais, mesmo se eles não são compactados pelo servidor de origem.

> [AZURE.IMPORTANT] Alterações de configuração de CDN levar algum tempo para se propagarem através da rede.  Para os perfis do <b>Azure CDN do Akamai</b> , propagação geralmente conclui em um minuto.  Para os perfis do <b>Azure CDN da Verizon</b> , você geralmente verá as alterações aplicar dentro de 90 minutos.  Se esta for a primeira vez que você configurou compactação para seu ponto de extremidade CDN, você deve considerar aguardando 1 e 2 horas para ter certeza de que a compactação configurações tiveram propagado para o aparece antes de solução de problemas

## <a name="enabling-compression"></a>Ativar a compactação

> [AZURE.NOTE] Os níveis padrão e Premium CDN fornecem a mesma funcionalidade de compactação, mas difere de interface do usuário.  Para obter mais informações sobre as diferenças entre os níveis padrão e Premium CDN, consulte [Visão geral de CDN do Azure](cdn-overview.md).

### <a name="standard-tier"></a>Nível padrão

> [AZURE.NOTE] Esta seção aplica-se aos perfis de ** **Azure CDN da Verizon Azure CDN padrão** e de Akamai** .

1. Da lâmina CDN perfil, clique na extremidade CDN que você deseja gerenciar.

    ![Pontos de extremidade do CDN perfil blade](./media/cdn-file-compression/cdn-endpoints.png)

    A lâmina de ponto de extremidade CDN é aberta.

2. Clique no botão **Configurar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-file-compression/cdn-config-btn.png)

    A lâmina de configuração de CDN é aberta.

3. Ative a **compactação**.

    ![Opções de compactação de CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Use os tipos padrão ou modificar a lista, removendo ou adicionando tipos de arquivo.
    
    > [AZURE.TIP] Embora possível, não é recomendável aplicar a compactação a formatos compactados, como ZIP, MP3, MP4, JPG, etc.
    
5. Depois de fazer suas alterações, clique no botão **Salvar** .

### <a name="premium-tier"></a>Nível de Premium

> [AZURE.NOTE] Esta seção aplica-se aos perfis de **Azure CDN Premium da Verizon** .

1. Da lâmina CDN perfil, clique no botão **Gerenciar** .

    ![Botão de gerenciar blade de perfil CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    O portal de gerenciamento de CDN é aberta.

2. Passe o mouse sobre a guia **HTTP grandes** , em seguida, passe o mouse sobre o submenu de **Configurações de Cache** .  Clique em **compactação**.

    Opções de compactação são exibidas.

    ![Compactação de arquivo](./media/cdn-file-compression/cdn-compress-files.png)

3. Ative a compactação clicando no botão de rádio **Compactação ativada** .  Insira os tipos MIME que você deseja compactar como uma lista delimitada por vírgulas (sem espaços) na caixa de texto **Tipos de arquivo** .
        
    > [AZURE.TIP] Embora possível, não é recomendável aplicar a compactação a formatos compactados, como ZIP, MP3, MP4, JPG, etc. 

4. Depois de fazer suas alterações, clique no botão de **atualização** .


## <a name="compression-rules"></a>Regras de compactação

Estas tabelas descrevem o comportamento de compactação de CDN do Azure para cada cenário.

> [AZURE.IMPORTANT] Para **Azure CDN da Verizon** (padrão e Premium), somente os arquivos elegíveis são compactados.  Para ser qualificado para compactação, um arquivo deve:
>
> - Ser maior que 128 bytes.
> - Ser menor que 1 MB.
> 
> Para **Azure CDN do Akamai**, todos os arquivos são qualificados para compactação.
>
> Para todos os produtos do Azure CDN, um arquivo deve ser um tipo de MIME que foi [configurado para compactação](#enabling-compression).
>
> Perfis de **CDN do Azure da Verizon** (padrão e Premium) suportam **gzip**, **deflate**ou **bzip2** codificação.  Perfis de **Azure CDN do Akamai** só oferece suporte a codificação **gzip** .
>
> Pontos de extremidade do **Azure CDN do Akamai** sempre solicitam **gzip** codificada como arquivos de origem, independentemente da solicitação do cliente.

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compactação desabilitada ou arquivo não é elegível para compactação

|Formato solicitado do cliente (via cabeçalho de codificação de aceitação)|Formato de arquivo armazenados em cache|Resposta CDN ao cliente|Anotações|
|----------------|-----------|------------|-----|
|Compactado|Compactado|Compactado|   |
|Compactado|Descompactado|Descompactado|    | 
|Compactado|Não em cache|Compactado ou descompactado|Depende de resposta de origem|
|Descompactado|Compactado|Descompactado|    |
|Descompactado|Descompactado|Descompactado|    |   
|Descompactado|Não em cache|Descompactado|     |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Compactação ativada e o arquivo está qualificado para compactação

|Formato solicitado do cliente (via cabeçalho de codificação de aceitação)|Formato de arquivo armazenados em cache|Resposta CDN ao cliente|Anotações|
|----------------|-----------|------------|-----|
|Compactado|Compactado|Compactado|Transcodifica CDN entre formatos com suporte|
|Compactado|Descompactado|Compactado|CDN executa a compactação|
|Compactado|Não em cache|Compactado|CDN executa a compactação se origin retornará descompactado.  **Azure CDN da Verizon** passar o arquivo descompactado na primeira solicitação e, em seguida, compactar e o arquivo para solicitações subsequentes em cache.  Arquivos com `Cache-Control: no-cache` cabeçalho nunca será compactado. 
|Descompactado|Compactado|Descompactado|CDN executa descompactação|
|Descompactado|Descompactado|Descompactado|     |  
|Descompactado|Não em cache|Descompactado|     |

## <a name="media-services-cdn-compression"></a>Serviços de mídia CDN compactação

Para CDN de serviços de mídia habilitado pontos de extremidade de streaming, compactação é ativada por padrão para os seguintes tipos de conteúdo: aplicativo/vnd.ms-sstr + xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m + xml. Você não pode ativar/desativar a compactação para os tipos de mencionada usando o portal do Azure.  

## <a name="see-also"></a>Consulte também
- [Solução de problemas de compactação de arquivo CDN](cdn-troubleshoot-compression.md)    
