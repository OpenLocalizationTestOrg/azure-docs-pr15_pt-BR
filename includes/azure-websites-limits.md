Recurso|Livre|Compartilhado (prévia)|Básico|Padrão|Premium (prévia)</th>
---|---|---|---|---|---
[Web, celular ou aplicativos de API](https://azure.microsoft.com/services/app-service/) por [plano de serviço de aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>
[Aplicativos de lógica](https://azure.microsoft.com/services/app-service/logic/) por [plano de serviço de aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 por núcleo|20 por núcleo
[Plano de serviço de aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 por região|10 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos
Calcular o tipo de instância|Compartilhado|Compartilhado|Dedicado<sup>3</sup>|Dedicado<sup>3</sup>|Dedicado<sup>3</sup></p>
[Escala-Out](../articles/app-service-web/web-sites-scale.md) (instâncias máx)|1 compartilhado|1 compartilhado|dedicada e 3 de<sup>3</sup>|10 dedicado<sup>3</sup>|20 dedicado (50 no ASE)<sup>3,4</sup>
Armazenamento<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
(Curta) de tempo de CPU<sup>6</sup>|3 minutos|3 minutos|Ilimitado, remuneração padrão [taxas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pagamento em taxas padrão|Ilimitado, pagamento em taxas padrão
CPU tempo (dia)<sup>6</sup>|60 minutos|240 minutos|Ilimitado, remuneração padrão [taxas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pagamento em taxas padrão|Ilimitado, pagamento em taxas padrão
Memória (1 hora)|1024 MB por plano de serviço de aplicativo|1024 MB por aplicativo|N/D|N/D|N/D
Largura de banda|165 MB|Ilimitado, aplicar [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)|Ilimitado, tarifas de transferência de dados|Ilimitado, tarifas de transferência de dados|Ilimitado, tarifas de transferência de dados
Arquitetura do aplicativo|32 bits|32 bits|32 bits/64 bits|32 bits/64 bits|32 bits/64 bits
Web Sockets por instância<sup>7</sup>|5|35|350|Ilimitado|Ilimitado
[Conexões de depurador](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) simultâneas por aplicativo|1|1|1|5|5
[subdomínio azurewebsites.NET com FTP/S e SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
Suporte de [domínio personalizado](../articles/app-service-web/web-sites-custom-domain-name.md)||X|X|X|X
[Suporte para SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md) de domínio personalizado|||Ilimitado|Ilimitado, 5 SNI SSL e 1 conexões de IP SSL incluído|Ilimitado, 5 SNI SSL e 1 conexões de IP SSL incluído
Balanceador de carga integrado||X|X|X|X
[Sempre ativado](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Backups agendados](../articles/app-service-web/web-sites-backup.md)||||Uma vez por dia|Uma vez cada 5 minutos<sup>8</sup>
[Dimensionamento automático](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md) <sup>9</sup>|X|X|X|X|X
Suporte do [Agendador do Azure](https://azure.microsoft.com/services/scheduler/)||X|X|X|X
[Monitoramento de ponto de extremidade](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Preparação Slots (prévia)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Domínios personalizados por aplicativo</a>||500|500|500|500
SLA||<p>|99,9%|99,95%<sup>10</sup>|99,95%<sup>10</sup>

<sup>1</sup> Aplicativos e cotas de armazenamento são por plano de serviço de aplicativo, a menos que seja observado o contrário.  
<sup>2</sup> O número real de aplicativos que você pode hospedar nessas máquinas depende da atividade de aplicativos, o tamanho das instâncias de máquina e a utilização de recursos correspondente.  
<sup>3</sup> Instâncias dedicadas podem ser de tamanhos diferentes. Para obter mais detalhes, consulte [Preços de serviço de aplicativo](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) .  
<sup>4</sup> Nível de Premium permite até 50 calcula instâncias (sujeitos a disponibilidade) e 500 GB de espaço em disco ao usar o aplicativo de serviço ambientes e 20 computação instâncias e armazenamento de 250 GB caso contrário.  
<sup>5</sup> O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos no mesmo plano de serviço de aplicativo. Mais opções de armazenamento estão disponíveis no [Ambiente de serviço de aplicativo](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup> Esses recursos são restringidos pelos recursos físicos em instâncias dedicados (o tamanho da instância e o número de instâncias).  
<sup>7</sup> Se você dimensionar um aplicativo no nível básico para duas instâncias, você tem 350 conexões simultâneas para cada uma das duas instâncias.  
<sup>8</sup> Nível de Premium permite intervalos de backup para baixo até a cada 5 minutos ao usar o aplicativo de serviço ambientes e 50 vezes por dia caso contrário.  
<sup>9</sup> Execute executáveis personalizados e/ou scripts sob demanda, em um cronograma ou continuamente como uma tarefa de plano de fundo em sua instância de serviço de aplicativo. Sempre é necessária para execução de WebJobs contínua. Azure Agendador livre ou padrão é exigido para WebJobs agendada. Não há nenhum limite predefinido no número de WebJobs que podem ser executados em uma instância de serviço de aplicativo, mas há limites práticos que dependem o que o código do aplicativo está tentando fazer.   
<sup>10</sup> SLA de 99,95% fornecido para implantações que usam várias instâncias com o Gerenciador de tráfego do Azure configurado para failover.  
