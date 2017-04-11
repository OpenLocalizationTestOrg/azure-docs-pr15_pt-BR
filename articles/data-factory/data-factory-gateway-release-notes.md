<properties 
    pageTitle="Notas de versão do Gateway de gerenciamento de dados | Fábrica de dados do Azure" 
    description="Notas de versão do tório de Gateway de gerenciamento de dados" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Notas de versão do Gateway de gerenciamento de dados
Um dos desafios integração de dados modernos é mover perfeita de dados e local para a nuvem. Dados fábrica torna essa integração perfeita com o Gateway de gerenciamento de dados, que é um agente que você pode instalar local para permitir a movimentação de dados híbridos.

Consulte os artigos a seguir para obter informações detalhadas sobre o Gateway de gerenciamento de dados e como usá-lo: 

- [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md)
- [Mover dados entre locais e na nuvem usando fábrica de dados do Azure](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Versão atual (2.2.6072.1)

- Suporta a configuração de proxy HTTP para o gateway usando o Gerenciador de configuração do Gateway. Se configurado, Azure Blob, tabela do Azure, Lucerne de dados do Azure e BD de documento são acessados por meio de proxy HTTP.
- Cabeçalho de suporta tratamento de TextFormat ao copiar dados de/para o Azure Blob, armazenamento de Lucerne de dados do Azure, sistema de arquivos local e HDFS local.
- Suporta copiando dados de Blob acrescentar e Blob de página juntamente com o Blob de bloco já com suporte.
- Apresenta um novo status de gateway **Online (limitado)**, que indica que a principal funcionalidade do gateway funciona, exceto o suporte de operação interativa para o Assistente de cópia.
- Melhora a eficiência do registro de gateway usando chave do registro.

## <a name="earlier-versions"></a>Versões anteriores

## <a name="2160401"></a>2.1.6040.1

- Driver de DB2 agora está incluído no pacote de instalação do gateway. Você não precisa instalá-lo separadamente. 
- Driver de DB2 agora suporta z/OS e DB2 para eu (AS / 400) juntamente com as plataformas suportadas já (Linux, Unix e Windows). 
- Suporta usando DocumentDB como origem ou de destino para armazenamentos de dados locais
- Suporta copiando dados de/para resfriado/quente blob storage junto com a conta de armazenamento geral já compatíveis. 
- Permite que você se conecte ao local SQL Server por meio de gateway com privilégios de logon remoto.  

## <a name="2060131"></a>2.0.6013.1

- Você pode selecionar a idioma/cultura a ser usado por um gateway durante a instalação manual.
- Quando o gateway não funciona como esperado, você pode optar por enviar logs de gateway dos últimos sete dias à Microsoft para facilitar a solução de problemas do problema. Se o gateway não esteja conectado ao serviço de nuvem, você pode optar por salvar e arquive logs de gateway.  
- Aprimoramentos da interface do usuário do Gerenciador de configuração do gateway:
    - Verifique o status do gateway mais visíveis na guia página inicial.
    - Controles reorganizados e simplificados.
- Você pode copiar dados de um armazenamento usando a [ferramenta de visualização de cópia de livre de código](data-factory-copy-data-wizard-tutorial.md). Consulte [Copiar preparado](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre esse recurso em geral. 
- Você pode usar o Gateway de gerenciamento de dados para dados de ingresso diretamente de um banco de dados do SQL Server no local em aprendizado de máquina do Azure.
- Melhorias de desempenho
    - Melhore o desempenho em exibindo esquema/visualização no SQL Server em ferramenta de visualização de cópia sem código.



## <a name="11259531"></a>1.12.5953.1
- Correções de bugs

## <a name="11159181"></a>1.11.5918.1

- Tamanho máximo do log de eventos do gateway aumentou de 1 MB para 40 MB.
- Uma caixa de diálogo de aviso é exibida no caso de uma reinicialização é necessária durante a atualização automática do gateway. Você pode optar por reiniciar à direita, em seguida, ou posterior. 
- Em caso de falha de atualização automática, o instalador do gateway tentativas de atualização automática de três vezes no máximo.
- Melhorias de desempenho
    - Melhore o desempenho para carregamento de tabelas grandes de servidor local no cenário de cópia sem código.
- Correções de bugs

## <a name="11058921"></a>1.10.5892.1

- Melhorias de desempenho
- Correções de bugs

## <a name="1958652"></a>1.9.5865.2

- Recurso de atualização de automática zero touch
- Novo ícone na bandeja com indicadores de status do gateway
- Capacidade de "Atualizar agora" do cliente
- Capacidade de definir o tempo de programação de atualização
- Script do PowerShell para alternar/desativar a atualização automática
- Suporte para o formato JSON  
- Melhorias de desempenho
- Correções de bugs

## <a name="1858221"></a>1.8.5822.1

- Melhorar a experiência de solução de problemas
- Melhorias de desempenho
- Correções de bugs

### <a name="1757951"></a>1.7.5795.1

- Melhorias de desempenho
- Correções de bugs

### <a name="1757641"></a>1.7.5764.1

- Melhorias de desempenho
- Correções de bugs

### <a name="1657351"></a>1.6.5735.1

- Suporte HDFS origem/receptor local
- Melhorias de desempenho
- Correções de bugs

### <a name="1656961"></a>1.6.5696.1

- Melhorias de desempenho
- Correções de bugs

### <a name="1656761"></a>1.6.5676.1

- Ferramentas de diagnóstico de suporte no Gerenciador de configuração
- Colunas de tabela de suporte para fontes de dados tabulares para fábrica de dados do Azure
- Suporte a DW do SQL Azure dados fábrica
- Suporte Reclusive no BlobSource e fonte de arquivo para dados Azure fábrica
- Suporte CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy em BlobSink e FileSink com cópia binária para fábrica de dados do Azure
- Suporte a atividade de cópia relatar o andamento de fábrica de dados do Azure
- Validação de conectividade de fonte de dados de suporte para dados Azure fábrica
- Correções de bugs


### <a name="1656721"></a>1.6.5672.1

- Nome de tabela de suporte para a fonte de dados ODBC para fábrica de dados do Azure
- Melhorias de desempenho
- Correções de bugs

### <a name="1656581"></a>1.6.5658.1

- Arquivo de suporte PIA para fábrica de dados do Azure
- Suporte preservando hierarquia em binária cópia para fábrica de dados do Azure
- Suporte cópia atividade idempotência do Azure dados fábrica
- Correções de bugs

### <a name="1656401"></a>1.6.5640.1

- Suporte 3 mais fontes de dados do Azure fábrica de dados (ODBC, OData, HDFS)
- Suporte caractere de aspas no analisador de csv para fábrica de dados do Azure
- Suporte de compactação (BZip2)
- Correções de bugs

### <a name="1556121"></a>1.5.5612.1

- Suporte a cinco bancos de dados relacionais do Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
- Suporte de compactação (Gzip e Deflate)
- Melhorias de desempenho
- Correções de bugs


### <a name="1455491"></a>1.4.5549.1

- Adicionar suporte de fonte de dados Oracle para fábrica de dados do Azure
- Melhorias de desempenho
- Correções de bugs

### <a name="1454921"></a>1.4.5492.1

- Binário unificado que ofereça suporte a serviços de fábrica de dados do Microsoft Azure e Power BI para Office 365
- Refinar o processo de configuração da interface do usuário e registro
- Azure fábrica de dados – suporte Azure ingresso e egresso para fonte de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

-   Corrigi o problema de tempo limite para oferecer suporte a mais conexões de fontes de dados demorada. 
    
### <a name="1155268"></a>1.1.5526.8

- Requer o .NET Framework 4.5.1 como pré-requisito durante a instalação.

### <a name="1051442"></a>1.0.5144.2

- Nenhuma alteração que afetam os cenários de fábrica de dados do Azure. 
