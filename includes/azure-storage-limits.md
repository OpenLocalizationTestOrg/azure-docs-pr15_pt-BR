Recurso|Limite padrão
---|---
Número de contas de armazenamento por assinatura|200<sup>1</sup>
TB por conta de armazenamento|500 TB
Número máximo de contêineres de blob, blobs, compartilhamentos de arquivos, tabelas, filas, entidades ou mensagens por conta de armazenamento|Somente o limite é a capacidade de conta de armazenamento de 500 TB
Tamanho máximo de um contêiner de único blob, tabela ou fila|500 TB
Máx número de blocos em um blob de bloco ou acrescentar blob|50.000
Máx tamanho de um bloco em um blob de bloco ou acrescentar blob|4 MB
Máx tamanho de um blob de bloco ou acrescentar blob|50.000 x 4 MB (aproximadamente 195 GB) 
Tamanho máximo de um blob de página |1 TB
Tamanho máximo de uma entidade de tabela|1 MB
Número máximo de propriedades em uma entidade de tabela|252
Tamanho máximo de uma mensagem em uma fila|64 KB
Tamanho máximo de um compartilhamento de arquivo|5 TB
Tamanho máximo de um arquivo em um compartilhamento de arquivo|1 TB
Número máximo de arquivos em um compartilhamento de arquivo|Somente o limite é a capacidade de total de 5 TB do compartilhamento de arquivo
Max 8 KB IOPS por compartilhar|1000
Número máximo de arquivos em um compartilhamento de arquivo|Somente o limite é a capacidade de total de 5 TB do compartilhamento de arquivo
Número máximo de contêineres de blob, blobs, compartilhamentos de arquivos, tabelas, filas, entidades ou mensagens por conta de armazenamento|Somente o limite é a capacidade de conta de armazenamento de 500 TB
Número máximo de políticas de acesso armazenadas por contêiner, compartilhamento de arquivo, tabela ou fila|5
Solicitar taxa total (presumindo que o tamanho do objeto de 1KB) por conta de armazenamento|Até 20.000 IOPS, entidades por segundo ou mensagens por segundo
Taxa de transferência de destino para único blob|Até 60 MB por segunda, ou até 500 solicitações por segundo
Taxa de transferência de destino para fila única (mensagens de 1 KB)|Até 2000 mensagens por segundo
Taxa de transferência de destino para partição de tabela única (entidades 1 KB)|Até 2000 entidades por segundo
Taxa de transferência de destino para compartilhamento de arquivo único|Até 60 MB por segundo
Max ingresso<sup>2</sup> por conta de armazenamento (conosco regiões)|10 Gbps se GRS/ZRS<sup>3</sup> habilitada, 20 Gbps para LRS
Max egresso<sup>2</sup> por conta de armazenamento (conosco regiões)|20 Gbps se ar-GRS/GRS/ZRS<sup>3</sup> habilitada, 30 Gbps para LRS
Max ingresso<sup>2</sup> por conta de armazenamento (Europeia e regiões asiático)|5 Gbps se GRS/ZRS<sup>3</sup> habilitada, 10 Gbps para LRS
Max egresso<sup>2</sup> por conta de armazenamento (Europeia e regiões asiático)|10 Gbps se ar-GRS/GRS/ZRS<sup>3</sup> habilitada, 15 Gbps para LRS

<sup>1</sup> Isso inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento, fazer uma solicitação por meio de [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de armazenamento do Azure examinará seu caso de negócios e pode aprovar até 250 contas de armazenamento. 

<sup>2</sup> *Ingresso* refere-se a todos os dados (solicitações) está sendo enviados a uma conta de armazenamento. *Egresso* refere-se a todos os dados (respostas) sendo recebidos de uma conta de armazenamento.  

<sup>3</sup> Opções de replicação de armazenamento Azure incluem:

- **Ar-GRS**: armazenamento de localização geográfica redundantes de acesso de leitura. Se ar GRS estiver habilitada, os destinos de saída para o local secundário são idênticos de local principal.
- **GRS**: armazenamento redundante de localização geográfica. 
- **ZRS**: armazenamento redundante de zona. Disponível somente para blobs de bloco. 
- **LRS**: armazenamento redundante localmente. 

