<properties
    pageTitle="Suportam a clientes de nível inferior do banco de dados SQL e ponto de extremidade IP muda para auditoria | Microsoft Azure"
    description="Saiba mais sobre o suporte de clientes de nível inferior do banco de dados SQL e IP alterações de ponto de extremidade para auditoria."
    services="sql-database"
    documentationCenter=""
    authors="ronitr"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/10/2016"
    ms.author="ronitr"/>

# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>Banco de dados SQL - suporte de clientes de nível inferior e alterações de ponto de extremidade IP para auditoria


[Auditoria](sql-database-auditing-get-started.md) funciona automaticamente com clientes SQL que oferecem suporte a redirecionamento de TDS.


##<a id="subheading-1"></a>Suporte de clientes de nível inferior

Qualquer cliente que implementa TDS 7.4 também deve oferecer suporte a redirecionamento. Exceções incluem JDBC 4.0 no qual o recurso de redirecionamento não é totalmente suportado e tediosas para Node nos quais o redirecionamento não foi implementada.

Para clientes de nível inferior"", ou seja, qual suporte TDS versão 7.3 e abaixo - o FQDN do servidor na conexão cadeia de caracteres deve ser modificada:

FQDN do servidor original na cadeia de conexão: <*nome do servidor*>. database.windows.net

FQDN do servidor modificado na cadeia de conexão: <*nome do servidor*> .database. **segura**. no windows.net

Uma lista parcial de "Clientes de nível inferior" inclui:

- .NET 4.0 e abaixo,
- ODBC 10.0 e abaixo.
- JDBC (JDBC dá suporte ao TDS 7.4, o recurso de redirecionamento TDS não é totalmente suportado)
- Entediante (para node)

**Comentário:** O servidor acima modificação FDQN pode ser útil também para aplicar uma política de auditoria do SQL Server nível sem a necessidade de uma etapa de configuração em cada banco de dados (atenuação temporário).

##<a id="subheading-2"></a>Alterações de ponto de extremidade IP ao habilitar auditoria

Observe que, quando você habilita a auditoria, o ponto de extremidade IP do banco de dados será alterado. Se você tiver as configurações de firewall estrito, atualize as configurações de firewall adequadamente.

O ponto de extremidade IP de banco de dados novo dependerá da região de banco de dados:

| Região de banco de dados | Possíveis pontos de extremidade IP |
|----------|---------------|
| América do Norte China  | 139.217.29.176, 139.217.28.254 |
| China Oriental  | 42.159.245.65, 42.159.246.245 |
| Austrália Oriental  | 104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Austrália Sudeste | 191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brasil Sul  | 104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centro dos EUA  | 104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Da Ásia Oriental   | 23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Leste dos EUA 2 | 104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Leste EUA   | 23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| Índia central  | 104.211.98.219, 104.211.103.71 |
| Índia Sul   | 104.211.227.102, 104.211.225.157 |
| Índia Oeste  | 104.211.161.152, 104.211.162.21 |
| Japão Leste   | 104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japão Oeste    | 104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Centro Norte dos EUA  | 191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Norte da Europa  | 104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Centro Sul dos EUA  | 191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sudeste Asiático  | 104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa Ocidental  | 104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Oeste EUA  | 191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canadá Central  | 13.88.248.106 |
| Canadá Leste  |  40.86.227.82 |
