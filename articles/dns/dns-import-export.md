<properties
   pageTitle="Importar e exportar um arquivo de zona de domínio DNS Azure usando CLI | Microsoft Azure"
   description="Saiba como importar e exportar um arquivo de zona DNS para DNS Azure usando CLI do Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um arquivo de zona DNS usando a CLI do Azure


Este artigo apresentará como importar e exportar arquivos de zona DNS para o DNS do Azure usando a CLI do Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração de zona DNS

Um arquivo de zona DNS é um arquivo de texto que contém detalhes de cada registro do sistema de nome de domínio (DNS) na zona. Ela segue um formato padrão, tornando mais adequado para transferir os registros DNS entre sistemas DNS. Usando um arquivo de zona é uma maneira de confiável e conveniente de rápido, para transferir uma zona DNS em ou sair do Azure DNS.

DNS Azure dá suporte à importação e exportação de arquivos de zona usando a interface de linha Azure (comando). O Azure é uma ferramenta de linha de comando entre plataformas usada para gerenciar os serviços do Azure. Ele está disponível para as plataformas Windows, Mac e Linux na [página downloads Azure](https://azure.microsoft.com/downloads/). Suporte entre plataformas é particularmente importante para importar e exportar arquivos de zona, porque o software de servidor de nome mais comuns, [vincular](https://www.isc.org/downloads/bind/), normalmente é executado no Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obter o seu arquivo de zona DNS existente

Antes de importar um arquivo de zona DNS no DNS do Azure, você precisará obter uma cópia do arquivo de zona. A fonte deste arquivo dependerá onde a zona DNS está hospedada no momento.

- Se sua zona DNS estiver hospedada em um serviço de parceiro (como um registrador de domínio, dedicado provedor de hospedagem de DNS ou provedor de nuvem alternativo), esse serviço deve fornecer a capacidade de baixar o arquivo de zona DNS.

- Se sua zona DNS estiver hospedada em DNS do Windows, a pasta padrão para os arquivos de zona é **%systemroot%\system32\dns**. O caminho completo para cada arquivo de zona também mostra na guia **Geral** do console de gerenciamento de serviço DNS.

- Se sua zona DNS estiver hospedada usando vincular, o local do arquivo de zona para cada zona especificado na de arquivo de configuração de vincular **named.conf**.

**Trabalhando com arquivos de zona da GoDaddy**<BR>
Arquivos de zona baixados do GoDaddy têm um formato ligeiramente diferente do padrão. Você precisa corrigir isso antes de importar esses arquivos de zona para Azure DNS. Nomes DNS a RData de cada registro DNS são especificados como nomes totalmente qualificados, mas não têm um terminando "." Isso significa que eles são interpretados por outros sistemas DNS como nomes relativos. Você precisar editar o arquivo de zona para acrescentar a encerrando "." para seus nomes antes de você importá-los para Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um arquivo de zona DNS para o DNS do Azure


Importar um arquivo de zona criará uma nova zona no Azure DNS se ainda não existir. Se a zona já existir, os conjuntos de registro no arquivo de zona devem ser mesclados com os conjuntos de registro existentes.

### <a name="merge-behavior"></a>Comportamento de mesclagem

- Por padrão, conjuntos de registros novos e existentes são mesclados. Registros idênticos dentro de um conjunto de registros mesclado são duplicação.

- Como alternativa, especificando a `--force` opção, o processo de importação substituirá existentes conjuntos de registros com novos conjuntos de registro. Conjuntos de registro existentes que não têm um registro correspondente definido no arquivo de zona importado não serão removidos.

- Quando os conjuntos de registros são mesclados, o tempo de vida útil (TTL) de conjuntos de registros preexistentes é usado. Quando `--force` é usado, o TTL do novo conjunto de registro é usado.

- Início de parâmetros de autoridade (SOA) (exceto `host`) são sempre tirado de arquivo de zona importada, independentemente se `--force` é usado. Da mesma forma, para o registro de servidor de nomes definidos a apex a zona, o TTL é sempre obtido o arquivo de zona importada.

- Um registro CNAME importado não substituirá um registro CNAME existente com o mesmo nome, a menos que o `--force` parâmetro for especificado.

- Quando um conflito surge entre um registro CNAME e outro registro do mesmo nome mas tipo diferente (independentemente de qual existentes ou novas), o registro existente é mantido. Isso é independente do uso de `--force`.

### <a name="additional-information-about-importing"></a>Informações adicionais sobre como importar

As observações a seguir fornecem detalhes técnicos adicionais sobre a zona de processo de importação.

- O `$TTL` diretiva é opcional, e ele é suportado. Quando não `$TTL` diretiva é fornecida, registros sem um TTL explícita será importado definido como um TTL padrão de 3600 segundos. Quando dois registros no conjunto de registros mesmo especificar TTLs diferentes, o menor valor é usado.

- O `$ORIGIN` diretiva é opcional, e ele é suportado. Quando não `$ORIGIN` estiver definido, o valor padrão usado é o nome de zona conforme especificado na linha de comando (plus o encerrando ".").

- O `$INCLUDE` e `$GENERATE` diretivas não têm suporte.

- Esses tipos de registro são suportados: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

- O registro SOA é criado automaticamente pelo DNS Azure quando uma zona é criada. Quando você importa um arquivo de zona, todos os parâmetros SOA são obtidos a zona arquivo *exceto* o `host` parâmetro. Esse parâmetro usa o valor fornecido pelo Azure DNS. Isso ocorre porque esse parâmetro deve se referir a primary name server fornecido pelo DNS do Azure.

- O registro de servidor de nomes definidos a apex a zona também é criado automaticamente pelo Azure DNS quando a zona é criada. Somente o TTL deste conjunto de registros é importado. Esses registros contêm os nomes de servidor de nome fornecidos pelo Azure DNS. Os dados de registro não são substituídos por valores contidos no arquivo de zona importados.

- Durante a visualização pública, Azure DNS suporta somente os registros TXT única cadeia de caracteres. Um registros TXT serão concatenados e truncados para 255 caracteres.

### <a name="cli-format-and-values"></a>Valores e formato CLI


O formato do comando CLI do Azure para importar uma zona DNS é:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>`é o nome do grupo de recursos para a zona de DNS do Azure.
- `<zone name>`é o nome da zona.
- `<zone file name>`é o caminho/nome do arquivo a ser importado zona.

Se uma zona com este nome não existir no grupo de recursos, ele será criado para você. Se a zona já existir, os conjuntos de registros importados serão mesclados com os conjuntos de registro existentes. Para substituir os conjuntos de registro existentes, use o `--force` opção.

Para verificar o formato de um arquivo de zona sem realmente importá-los, use o `--parse-only` opção.

### <a name="step-1-import-a-zone-file"></a>Etapa 1. Importar um arquivo de zona

Importar um arquivo de zona para a zona **contoso.com**.

1. Entrar para sua assinatura do Azure usando a CLI do Azure.

        azure login

2. Selecione a assinatura à qual você deseja criar sua nova zona DNS.

        azure account set <subscription name>

3. DNS Azure é um serviço de somente Gerenciador de recursos do Azure, portanto a CLI Azure deve ser mudada para o modo do Gerenciador de recursos.

        azure config mode arm

4. Antes de usar o serviço de DNS do Azure, você deve registrar sua assinatura para usar o provedor de recursos de Microsoft.Network. (Isso é uma operação única para cada assinatura).

        azure provider register Microsoft.Network

5. Se você não tenha um, você também precisa criar um grupo de recursos do Gerenciador de recursos.

        azure group create myresourcegroup westeurope

6. Para importar a zona **contoso.com** do arquivo **contoso.com.txt** para uma nova zona DNS no grupo de recursos **myresourcegroup**, execute o comando `azure network dns zone import`.<BR>Este comando carregar o arquivo de zona e analisá-lo. O comando executará uma série de comandos o serviço de DNS do Azure para criar a zona e todo o registro define na zona. O comando também irá relatar o progresso na janela do console, juntamente com erros ou avisos. Como os conjuntos de registro são criados em série, pode levar alguns minutos para importar um arquivo de zona grande.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Etapa 2. Verifique se a zona

Para verificar a zona DNS após importar o arquivo, você pode usar qualquer um dos seguintes métodos:

- Você pode listar os registros usando o seguinte comando CLI do Azure.

        azure network dns record-set list myresourcegroup contoso.com

- Você pode listar os registros usando o cmdlet do PowerShell `Get-AzureRmDnsRecordSet`.

- Você pode usar `nslookup` para verificar a resolução de nomes para os registros. Como a zona não é delegada ainda, você precisará especificar os servidores de nomes DNS Azure corretos explicitamente. O exemplo a seguir mostra como recuperar os nomes de servidor de nome atribuídos à zona. IT também mostra como consultar o registro de "www" usando `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Etapa 3. Delegação de DNS de atualização

Após ter verificado que a zona foi importada corretamente, você precisará atualizar a delegação de DNS para apontar para os servidores de nomes DNS do Azure. Para obter mais informações, consulte o artigo [atualizar a delegação de DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um arquivo de zona DNS do DNS do Azure

O formato do comando CLI do Azure para importar uma zona DNS é:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>`é o nome do grupo de recursos para a zona de DNS do Azure.
- `<zone name>`é o nome da zona.
- `<zone file name>`é o caminho/nome do arquivo a ser exportado zona.

Como com a importação de zona, você primeiro precisa entrar, escolha sua assinatura e configure o CLI do Azure para usar o modo do Gerenciador de recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um arquivo de zona


1. Entrar para sua assinatura do Azure usando a CLI do Azure.

        azure login

2. Selecione a assinatura à qual você deseja criar sua nova zona DNS.

        azure account set <subscription name>

3. Azure DNS é um serviço de somente Gerenciador de recursos do Azure. A CLI do Azure deve ser mudada para o modo do Gerenciador de recursos.

        azure config mode arm

4. Para exportar o existente de zona de DNS do Azure **contoso.com** no grupo de recursos **myresourcegroup** para o arquivo **contoso.com.txt** (na pasta atual), executar `azure network dns zone export`. Este comando chamará o serviço de DNS do Azure para enumerar conjuntos de registro na zona e exportar os resultados para um arquivo de zona de vincular compatível.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

