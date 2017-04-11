<properties
    pageTitle="Aprender sobre recursos em edições de Serviços BizTalk | Microsoft Azure"
    description="Compare os recursos das edições Serviços BizTalk: livre, desenvolvedor, Basic, Standard e Premium. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>Serviços do BizTalk: Gráfico de edições

Os serviços do Azure BizTalk oferece várias edições. Use este artigo para determinar qual é a edição certa para suas necessidades de negócios e cenário.


## <a name="compare-the-editions"></a>Comparar as edições

**Liberar (prévia)**

Pode criar e gerenciar conexões de híbrido. Uma Conexão de híbrida é uma maneira fácil de se conectar a um site do Azure para um sistema local, como o SQL Server.

**Desenvolvedor**

Inclui conexões de híbrido, EAI e EDI processamento de mensagem com um portal de gerenciamento de parceiro negociação fáceis de usar e suporte para esquemas EDI comuns e o processamento de EDI rich sobre X12 e AS2. Pode criar cenários comuns de EAI conectar serviços na nuvem com qualquer protocolos HTTP/S, restante, FTP, WCF e SFTP para ler e escrever mensagens.  Utilize a conectividade com sistemas LOB local com adaptadores SAP, Oracle eBusiness, banco de dados Oracle, Siebel e SQL Server prontos para uso. Use um ambiente de centrados em desenvolvedor com o Visual Studio tools para implantação e desenvolvimento fácil. Limitado para fins de desenvolvimento e teste somente com nenhum nível contrato serviço (SLA).

**Básico**

Inclui a maioria dos recursos de desenvolvedor com aumentos em conexões de conexões híbridos, pontes EAI, contratos de EDI e BizTalk adaptador Pack. Também oferece alta disponibilidade e a opção Dimensionar com um contrato de nível de serviço (SLA).

**Padrão**

Inclui todos os recursos básicos com aumentos em conexões de conexões híbridos, pontes EAI, contratos de EDI e BizTalk adaptador Pack. Também oferece alta disponibilidade e a opção Dimensionar com um contrato de nível de serviço (SLA).

**Premium**

Inclui todos os recursos padrão com aumentos em conexões de conexões híbridos, pontes EAI, contratos de EDI e BizTalk adaptador Pack. Também inclui arquivamento, alta disponibilidade e a opção Dimensionar com um contrato de nível de serviço (SLA).


## <a name="editions-chart"></a>Gráfico de edições
A tabela a seguir lista as diferenças.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Liberar (prévia)</th>
        <th>Desenvolvedor</th>
        <th>Básico</th>
        <th>Padrão</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Preço inicial</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Serviços de preços do Azure BizTalk</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Calculadora de preços do Azure</a></td>
</tr>
<tr>
<td><strong>Configuração mínima padrão</strong></td>
<td>1 unidade livre</td>
<td>Unidade de 1 de desenvolvedor</td>
<td>1 unidade básica</td>
<td>1 unidade padrão</td>
<td>Unidade de 1 premium</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Nenhuma escala</td>
<td>Nenhuma escala</td>
<td>Sim, em incrementos de 1 unidade básica</td>
<td>Sim, em incrementos de 1 unidade padrão</td>
<td>Sim, em incrementos de 1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala out máxima permitida</strong></td>
<td>Nenhuma escala</td>
<td>Nenhuma escala</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
</tr>
<tr>
<td><strong>Pontes EAI por unidade</strong></td>
<td>Não incluído</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclui contratos de TPM</td>
<td>Não incluído</td>
<td>Incluído. 10 contratos por unidade.</td>
<td>Incluído. 50 contratos por unidade.</td>
<td>Incluído. 250 contratos por unidade.</td>
<td>Incluído. contratos de 1000 unitário.</td>
</tr>
<tr>
<td><strong>Conexões de híbrido unitário</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transferência de dados de Conexão híbrido (GB) por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Conexões de serviço de adaptador BizTalk para sistemas LOB de local</strong></td>
<td>Não incluído</td>
<td>1 conexão</td>
<td>conexões de 2</td>
<td>5 conexões</td>
<td>25 conexões</td>
</tr>
<tr>
<td align="left"><strong>Sistemas/protocolos suportados:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Barramento de serviço (SB)</li>
<li>Blob do Microsoft Azure</li>
<li>APIs REST</li>
</ul>
</td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Alta disponibilidade</strong>
<br/><br/>
Para o contrato de nível de serviço (SLA), consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Preços do Azure BizTalk Services</a>.
</td>
<td>Não incluído</td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Backup e restauração</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Acompanhamento</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Arquivamento</strong><br/><br/>
Inclui não repúdio de recebimento (NRR) e baixar mensagens controladas</td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Não incluído</td>
<td>Não incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Usar códigos personalizados</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Uso das transformações, incluindo XSLT personalizado</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
</table>

> [AZURE.NOTE] Para resiliência contra falhas de hardware, alta disponibilidade significa ter várias VMs dentro de uma única unidade de BizTalk.


## <a name="faqs"></a>Perguntas frequentes

#### <a name="what-is-a-biztalk-unit"></a>O que é uma unidade de BizTalk?
Uma unidade de"" é o nível atômico de uma implantação do Azure BizTalk Services. Cada edition vem com uma unidade que tem memória e capacidade de computação diferentes. Por exemplo, uma unidade básica tem mais computação de desenvolvedor, padrão tem mais computação que básicas e assim por diante. Quando você dimensionar um BizTalk Service, você dimensionar em termos de unidades.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Qual é a diferença entre os Serviços BizTalk e máquina virtual do Azure BizTalk?
Serviços do BizTalk fornece uma arquitetura de plataforma-como um serviço (PaaS) verdadeira para a criação de soluções de integração na nuvem. Com o modelo de PaaS, você pode focalizar completamente a lógica do aplicativo e deixar todo o gerenciamento de infraestrutura para a Microsoft, incluindo:

- Não é necessário para gerenciar ou patch máquinas virtuais.
- Microsoft garante a disponibilidade.
- Você controla escala sob demanda simplesmente solicitando mais ou menos capacidade por meio do portal Azure.

BizTalk Server em máquinas virtuais do Azure fornece uma arquitetura de infraestrutura-como um serviço (IaaS). Crie máquinas virtuais e configurá-los exatamente como o seu ambiente local, tornando mais fácil executar aplicativos existentes na nuvem sem alterações de código. Com IaaS, você ainda é responsáveis pela Configurando as máquinas virtuais, gerenciando as máquinas virtuais (por exemplo, instalar software e patches do SO) e arquitetura do aplicativo para alta disponibilidade.

Se você está procurando na criação de novas soluções de integração que minimizar o esforço de gerenciamento de infraestrutura, use os serviços do BizTalk. Se você estiver pretendendo para migrar rapidamente as soluções existentes do BizTalk ou procurando um ambiente sob demanda para desenvolver e testar aplicativos BizTalk Server, use BizTalk Server em uma máquina Virtual do Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Qual é a diferença entre o serviço de adaptador BizTalk e híbrido conexões?
O serviço de adaptador BizTalk é usado por um serviço de BizTalk Azure. O serviço de adaptador BizTalk usa o pacote de adaptador BizTalk para se conectar a um sistema de linha de negócios (LOB) local. Uma Conexão de híbrido oferece uma maneira fácil e conveniente para conectar-se a aplicativos do Azure, como o recurso de aplicativos Web do serviço de aplicativo do Azure e serviços de celular do Azure, para um recurso local.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>O que significa "Híbrido Conexão transferência de dados (GB) por unidade"? Trata-se por minuto/hora/dia/semana/mês? O que acontece quando o limite é alcançado?

O custo de Conexão híbrido unitário depende o BizTalk Services edition. Resumindo, dependem de custos em quanto aos dados de transferência. Por exemplo, transferir dados de 10 GB diariamente custa menos que transferir 100 GB diariamente. Use a [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/?scenario=full) para os serviços do BizTalk para determinar custos específicos. Normalmente, os limites são aplicados diariamente. Se você exceder o limite, qualquer excedente cobrada de acordo com a taxa de $1 por GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Ao criar um contrato nos serviços do BizTalk, por que o número de pontes subir por dois em vez de apenas um?

Cada contrato consiste de duas pontes diferentes: uma ponte de comunicação do lado de envio e uma ponte de comunicação do lado de recebimento.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>O que acontece quando eu tiver atingido o limite de cota no número de pontes ou contratos?

Não é possível implantar qualquer novas pontes ou criar qualquer novos contratos. Para implantar mais, é necessário expandir a mais unidades do serviço BizTalk ou atualizar para uma edição superior.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Como migrar de um nível de Serviços BizTalk para outro?

A edição livre não pode ser migradas ou 'aumentadas' para outro nível e não pode ser feito o backup e restaurados para outra camada. Se você precisar outra camada, crie um novo BizTalk Service usando o novo nível. Qualquer artefatos criados usando a edição livre, incluindo conexões híbridos, precisem ser recriadas no novo BizTalk Service. 

Para as edições restantes, use o backup e restauração para migrar seus artefatos de um nível para outro. Por exemplo, backup seu artefatos na camada padrão e, em seguida, restaurá-los para o nível Premium. [Serviços BizTalk: Backup e restauração](biztalk-backup-restore.md) descreve os caminhos de migração com suporte e listas que artefatos são copiados. Observe que híbrido conexões não são copiados. Depois de backup e restauração para um novo nível, você recrie as conexões híbrido.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>É o serviço de adaptador BizTalk incluído no serviço? Como posso receber o software?

Sim, o serviço de adaptador BizTalk com o pacote de adaptador BizTalk são fornecidos com o SDK dos serviços do Azure BizTalk [Baixar](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Próximas etapas

Para criar serviços do Azure BizTalk no portal do Azure, vá para [Serviços BizTalk: provisionamento usando o portal de Azure](biztalk-provision-services.md). Para iniciar a criação de aplicativos, vá para [Serviços de BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Recursos adicionais
- [Serviços do BizTalk: Usando o portal de Azure de provisionamento](biztalk-provision-services.md)<br/>
- [Serviços do BizTalk: Gráfico de Status de provisionamento](biztalk-service-state-chart.md)<br/>
- [Serviços BizTalk: Guias de painel, o Monitor e a escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk serviços: Backup e restauração](biztalk-backup-restore.md)<br/>
- [Serviços do BizTalk: otimização](biztalk-throttling-thresholds.md)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](biztalk-issuer-name-issuer-key.md)<br/>
- [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
