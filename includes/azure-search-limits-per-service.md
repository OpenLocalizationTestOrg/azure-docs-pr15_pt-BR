Armazenamento é restringido por espaço em disco ou por um limite rígido no *número máximo* de índices ou documentos, o que vier primeiro. 

Recurso|Livre|Básico|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrato de nível de serviço (SLA)|Não <sup>1</sup> |Sim |Sim  |Sim |Sim |Sim
Espaço de armazenamento por partição|50 MB |2 GB|25 GB|100 GB|200 GB|200 GB
Partições por serviço|N/D|1|12|12|12|3
Tamanho da partição|N/D|2 GB|25 GB|100 GB|200 GB |200 GB
Réplicas|N/D|1|12|12|12|12
Índices máximos|3|5|50|200|200|1000 por partição ou 3000 por serviço
Documentos máximos|10.000|1 milhão|15 milhões por partição ou milhões de 180 por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 bilhões por serviço|1 milhão por índice ou 200 milhões por partição |
Consultas estimadas por segundo (QPS)|N/D|~ 3 por réplica|aproximadamente 15 por réplica|~ 60 por réplica|~ 60 por réplica|> 60 por réplica

<sup>1</sup> livre e SKUs de visualização não vêm com contratos de nível de serviço (SLAs). SLAs são aplicados quando uma SKU fique disponível.