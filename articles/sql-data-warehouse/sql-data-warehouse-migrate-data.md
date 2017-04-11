<properties
   pageTitle="Migrar seus dados para o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para migrar seus dados depósito de dados do SQL Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Migrar seus dados
Dados podem ser movidos de diferentes fontes para seu SQL Data Warehouse com ferramentas uma variedade.  Copiar AAD, SSIS e bcp podem ser usados para atingir essa meta. No entanto, como a quantidade de dados aumenta você deve pensar sobre dividindo o processo de migração de dados em etapas. Isso lhe dá a oportunidade de otimizar cada etapa para desempenho e resiliência garantir uma migração de dados suave.

Primeiro, este artigo aborda os cenários de migração simples de cópia AAD, SSIS e bcp. Ele examine aprofundar em como a migração pode ser otimizada.

## <a name="azure-data-factory-adf-copy"></a>Azure copiar dados fábrica (AAD)
[Copiar AAD][] faz parte de [Fábrica de dados do Azure][]. Você pode usar AAD cópia para exportar seus dados para arquivos simples que reside no armazenamento local, arquivos simples remoto mantidos em armazenamento de blob do Microsoft Azure ou diretamente no SQL Data Warehouse.

Se seus dados começam em arquivos simples, primeiro será necessário transferi-la para blob de armazenamento do Azure antes de iniciar uma carga no SQL Data Warehouse. Quando os dados são transferidos para o armazenamento de blob do Microsoft Azure você pode optar por usar [AAD copiar][] novamente para os dados por push para SQL Data Warehouse.

PolyBase também fornece uma opção de alto desempenho para carregar os dados. No entanto, isso significa usando duas ferramentas em vez de um. Se você precisa o melhor desempenho, use PolyBase. Se você desejar uma experiência única ferramenta (e os dados não forem grandes) AAD é sua resposta.

> [AZURE.NOTE] PolyBase requer os arquivos de dados sejam em UTF-8. Esse é padrão da cópia AAD codificação há nada para alterar. Este é apenas um lembrete para não alterar o comportamento padrão do AAD cópia.

Vá direto para o artigo a seguir para alguns ótimos [exemplos AAD][].

## <a name="integration-services"></a>Serviços de integração ##
Integration Services (SSIS) é uma ferramenta poderosa e flexível extrair transformação e carregamento (ETL) que ofereça suporte a fluxos de trabalho complexos, transformação de dados e várias opções de carregamento de dados. Use o SSIS para simplesmente transferir dados para o Azure ou como parte de uma migração mais ampla.

> [AZURE.NOTE] SSIS pode exportar UTF-8 sem a marca de ordem de byte no arquivo. Para configurar isso, primeiro você deve usar o componente de coluna derivada para converter os dados de caractere no fluxo de dados para usar a página de código 65001 UTF-8. Depois que as colunas foram convertidas, gravar os dados para o adaptador de destino do arquivo simples garantindo que 65001 também foi selecionada como a página de código para o arquivo.

SSIS se conecta ao SQL Data Warehouse exatamente como ele faria se conectar a uma implantação do SQL Server. No entanto, suas conexões precisará estar usando um Gerenciador de conexão de ADO.NET. Você também deve ter cuidado para configurar a "usar inserção em massa quando disponível" configuração maximizar a produtividade. Consulte o artigo [ADO.NET adaptador de destino][] para saber mais sobre esta propriedade

> [AZURE.NOTE] Conectando-se depósito de dados do SQL Azure usando OLEDB não é suportada.

Além disso, sempre há a possibilidade de que um pacote pode falhar devido a problemas de rede ou a otimização. Pacotes de design para que eles podem ser reiniciados no ponto de falha, sem refazendo trabalho concluída antes da falha.

Para obter mais informações, consulte a [documentação do SSIS][].

## <a name="bcp"></a>BCP
BCP é um utilitário de linha de comando que foi projetado para importação de dados de arquivo simples e exportação. Alguns transformação podem ocorrer durante a exportação de dados. Para executar transformações simples usam uma consulta para selecionar e transformar os dados. Depois de exportada, arquivos simples podem, em seguida, ser carregados diretamente para o destino do banco de dados SQL Data Warehouse.

> [AZURE.NOTE] Muitas vezes é uma boa ideia encapsular as transformações usadas durante a exportação de dados em um modo de exibição no sistema de origem. Isso garante que a lógica é mantida e o processo repetitivo.

Vantagens de bcp são:

- Simplicidade. comandos de BCP são simples para criar e executar
- Processo de carga reinicializável. Uma vez exportado a carga pode ser executada qualquer número de vezes

Limitações de bcp são:

- BCP funciona com tabulado arquivos simples apenas. Ele não funciona com arquivos como xml ou JSON
- BCP não dá suporte a exportação para UTF-8. Isso pode impedir que usando PolyBase em dados bcp exportado
- Recursos de transformação de dados limitam-se para o estágio de exportação somente e são simples de natureza
- BCP não foi adaptado para ser robusto ao carregar dados pela internet. Qualquer instabilidade da rede pode causar um erro de carregamento.
- BCP depende do esquema sendo presente do banco de dados de destino antes da carga

Para obter mais informações, consulte [usar bcp para carregar dados depósito de dados do SQL][].

## <a name="optimizing-data-migration"></a>Otimizando a migração de dados
Um processo de migração de dados SQLDW pode ser dividido efetivamente em três etapas distintas:

1. Exportação de dados de origem
2. Transferência de dados para o Azure
3. Carregar para o banco de dados SQLDW de destino

Cada etapa pode ser otimizada individualmente para criar um processo de migração robusta, reinicializável e flexíveis que maximiza o desempenho em cada etapa.

## <a name="optimizing-data-load"></a>Otimização de carga de dados
Examinando esses na ordem inversa por alguns instantes; a maneira mais rápida para carregar dados é via PolyBase. Otimizando para um processo de carga PolyBase colocará pré-requisitos nas etapas anteriores portanto, é melhor entender esse antecipada. Eles são:

1. Codificação de arquivos de dados
2. Formato de arquivos de dados
3. Local dos arquivos de dados

### <a name="encoding"></a>Codificação
PolyBase requer arquivos de dados a serem codificados UTF-8. Isso significa que quando você exporta dados ele deve em conformidade com esse requisito. Se seus dados contiverem apenas básicos caracteres ASCII (não estendidos ASCII), em seguida, esses mapa diretamente para o padrão de UTF-8 e você não precisa se preocupe muito com a codificação. No entanto, se seus dados contiverem quaisquer caracteres especiais, como caracteres especiais, acentos ou símbolos ou seus dados dá suporte a idiomas não latinos, em seguida, será necessário que garantir que seus arquivos de exportação adequadamente codificados UTF-8.

> [AZURE.NOTE] BCP não dá suporte a exportação de dados para UTF-8. Portanto, a melhor opção é usar o Integration Services ou AAD cópia para exportação de dados. Vale a pena destacar que a marca de ordem de byte UTF-8 (BOM) não é necessário no arquivo de dados.

Quaisquer arquivos codificados usando UTF-16 precisará ser novamente gravado ***anterior*** a transferência de dados.

### <a name="format-of-data-files"></a>Formato de arquivos de dados
PolyBase exige um terminador de linha fixa de \n ou nova linha. Seus arquivos de dados devem estar em conformidade com este padrão. Não existem quaisquer restrições sobre terminadores de cadeia de caracteres ou coluna.

Você precisará definir cada coluna no arquivo como parte de sua tabela externa no PolyBase. Certifique-se de que todas as colunas exportadas são necessárias e que tipos de em conformidade com os padrões de necessários.

Consulte novamente a [migrar seu esquema] artigo para obter detalhes sobre os tipos de dados com suporte.

### <a name="location-of-data-files"></a>Local dos arquivos de dados
SQL Data Warehouse usa PolyBase para carregar dados do armazenamento de Blob do Azure exclusivamente. Consequentemente, os dados devem foram primeiro transferidos para o armazenamento de blob.

## <a name="optimizing-data-transfer"></a>Otimizando transferência de dados
Uma das partes mais lentas de migração de dados é a transferência de dados no Azure. Não só pode ser de largura de banda de rede um problema, mas também confiabilidade da rede pode obstruir seriamente andamento. Por padrão a migração de dados para o Azure é pela internet para que as chances de erros de transferência que ocorrem são razoavelmente prováveis. No entanto, esses erros podem exigir dados a serem enviados novamente no todo ou em parte.

Felizmente, você tem várias opções para melhorar a velocidade e resiliência desse processo:

### <a name="expressroute"></a>[Rota expressa][]
Você talvez queira considerar usando [rota expressa][] para acelerar a transferência. [Rota expressa][] oferece uma conexão privada estabelecida no Azure para que a conexão não vai na Internet pública. Isso não é uma etapa obrigatória. No entanto, ela aumentará produtividade quando insistem dados no Azure de um local ou recurso de localização conjunta.

Os benefícios do uso de [rota expressa][] são:

1. Maior confiabilidade
2. Mais velocidade de rede
3. Menor latência de rede
4. maior segurança de rede

[Rota expressa][] é útil para um número de cenários; não apenas a migração.

Interessado? Para obter mais informações e preços, visite a [rota expressa documentação][].

### <a name="azure-import-and-export-service"></a>Serviço de exportação e importação Azure
O serviço de exportação e importação do Azure é um processo de transferência de dados criado para grandes (GB + +) para grandes (TB + +) transferências de dados no Azure. Ele envolve a gravar dados em discos e enviá-los a um centro de dados do Azure. O conteúdo do disco, em seguida, será carregado na Blobs de armazenamento do Azure em seu nome.

Uma visão geral do processo de exportação de importação é da seguinte maneira:

1. Configurar um contêiner de armazenamento de Blob do Azure para receber os dados
2. Exportar seus dados para o armazenamento local
2. Copie os dados para as unidades de disco rígido SATA II/III 3,5 polegadas usando a [ferramenta de importação/exportação do Azure]
3. Criar um trabalho de importação usando o Azure importar e exportar serviço fornecendo os arquivos de diário produzidos pela [ferramenta de importação/exportação do Azure]
4. Enviar os discos seu centro de dados Azure indicado
5. Seus dados são transferidos para o contêiner de armazenamento de Blob do Azure
6. Carregar os dados na SQLDW usando PolyBase

### <a name="azcopy-utility"></a>Utilitário de [AZCopy][]
O utilitário de [AZCopy][] é uma ótima ferramenta para obter seus dados transferidos para Blobs de armazenamento do Azure. Ele é projetado para pequenas (MB + +) para muito grandes transferências de dados (GB + +). [AZCopy] também foi projetado para fornecer boa produtividade e apresentam resiliência ao transferir dados do Azure e isso é uma excelente opção para a etapa de transferência de dados. Uma vez transferido, você pode carregar os dados usando PolyBase para SQL Data Warehouse. Você também pode incorporar AZCopy seus pacotes SSIS utilizando uma tarefa de "Executar processo".

Para usar AZCopy primeiro será necessário baixar e instalá-lo. Há uma [versão de produção][] e uma [versão de visualização][] disponível.

Para carregar um arquivo de seu sistema de arquivos, você precisará um comando como mostrado abaixo:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Um resumo de alto nível do processo pode ser:

1. Configurar um contêiner de blob do armazenamento do Azure para receber os dados
2. Exportar seus dados para o armazenamento local
3. AZCopy seus dados no contêiner de armazenamento de Blob do Azure
4. Carregar os dados em um depósito de dados do SQL usando PolyBase

Completo documentação disponível: [AZCopy][].

## <a name="optimizing-data-export"></a>Otimizando exportação de dados
Além de garantir que a exportação está de acordo com os requisitos dispostos por PolyBase você também pode buscar otimizar a exportação de dados para melhorar ainda mais o processo.

> [AZURE.NOTE] Como PolyBase requer os dados a serem no formato UTF-8, é provável que você usará bcp para executar a exportação de dados. BCP não dá suporte a exibição de arquivos de dados UTF-8. SSIS ou AAD cópia são muito mais adequado para executar esse tipo de exportação de dados.

### <a name="data-compression"></a>Compactação de dados
PolyBase pode ler dados gzip compactado. Se você é capaz de compactar seus dados e arquivos gzip minimizará a quantidade de dados sendo enviados pela rede.

### <a name="multiple-files"></a>Vários arquivos
Dividir tabelas grandes em diversos arquivos não apenas ajuda a melhorar a velocidade de exportação, como também ajuda com reinicialização de transferência e a capacidade de gerenciamento geral dos dados do armazenamento de blob do Microsoft Azure de uma vez. Um dos muitos recursos interessantes de PolyBase é que ele vai ler todos os arquivos dentro de uma pasta e tratado como uma tabela. Portanto, ele é uma boa ideia para isolar os arquivos para cada tabela para sua própria pasta.

PolyBase também oferece suporte a um recurso conhecido como "percurso de pasta recursivo". Você pode usar esse recurso para aprimorar a organização de seus dados exportados para melhorar o gerenciamento de dados.

Para saber mais sobre o carregamento de dados com PolyBase, consulte [Usar PolyBase para carregar dados depósito de dados do SQL][].


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre migração, consulte [migrar sua solução depósito de dados do SQL][].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[Copiar AAD]: ../data-factory/data-factory-data-movement-activities.md 
[Exemplos de AAD]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md
[Migrar sua solução depósito de dados do SQL]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp para carregar dados em um depósito de dados do SQL]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase para carregar dados em um depósito de dados do SQL]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Fábrica de dados do Azure]: http://azure.microsoft.com/services/data-factory/
[Rota expressa]: http://azure.microsoft.com/services/expressroute/
[Rota expressa documentação]: http://azure.microsoft.com/documentation/services/expressroute/

[versão de produção]: http://aka.ms/downloadazcopy/
[versão de visualização]: http://aka.ms/downloadazcopypr/
[ADO.NET adaptador de destino]: https://msdn.microsoft.com/library/bb934041.aspx
[Documentação do SSIS]: https://msdn.microsoft.com/library/ms141026.aspx
