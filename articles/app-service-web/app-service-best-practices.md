<properties
    pageTitle="Práticas recomendadas para o serviço de aplicativo do Azure"
    description="Aprenda as práticas recomendadas e solução de problemas para o serviço de aplicativo do Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Práticas recomendadas para o serviço de aplicativo do Azure

Este artigo resume práticas recomendadas para usar o [Serviço de aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Colocação
Quando recursos Azure redigindo uma solução como um aplicativo web e um banco de dados estiverem localizados em diferentes regiões os efeitos podem incluir o seguinte:

*  Maior latência na comunicação entre recursos
*  Encargos monetários para dados de saída transferir entre-região como indicado na [página de preços Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Colocação na mesma região é melhor para recursos Azure redigindo uma solução como um aplicativo web e uma conta de armazenamento ou banco de dados usado para armazenar conteúdo ou dados. Ao criar recursos que deverá garantir que eles estão na mesma região Azure, a menos que você tem negócios específicas ou motivo para que eles não sejam de design. Você pode mover um aplicativo de serviço de aplicativo para a mesma região como seu banco de dados utilizando o [recurso de clonagem de serviço de aplicativo](app-service-web-app-cloning-portal.md) atualmente disponíveis para aplicativos do plano de serviço de aplicativo Premium.   

## <a name="memoryresources"></a>Quando aplicativos consumam mais memória do que o esperado
Quando você perceber um aplicativo consome mais memória do que o esperado, conforme indicado pelo monitoramento ou recomendações de serviço considerar o [recurso de reparo automático de serviço de aplicativo](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para o recurso de reparo automático está demorando ações personalizadas com base em um limite de memória. Ações abrangem a variedade de notificações por email para investigação via despejo de memória para atenuação no local por meio da reciclagem do processo de trabalho. Autoreparo pode ser configurado via Web. config e por meio de uma interface de usuário amigável conforme descrito nesta postagem de blog para a [Extensão do Site de suporte de serviço de aplicativo](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando aplicativos consumam mais CPU que o esperado
Quando você perceber que um aplicativo consome mais CPU que o esperado ou experiências repetidos picos de CPU conforme indicado pelo monitoramento ou recomendações de serviço consideram dimensionamento para cima ou dimensionamento o plano de serviço de aplicativo. Se seu aplicativo for estado, dimensionamento é a única opção, enquanto se seu aplicativo é sem estado, dimensionamento out você terá mais flexibilidade e maior potencial de escala. 

Para obter mais informações sobre os aplicativos do "estado" versus "sem estado", você pode assistir a este vídeo: [planejamento de um aplicativo de várias camadas Scalable ponta a ponta no Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre as opções de escala e autoscaling do serviço de aplicativo leia: [escala de um aplicativo Web no serviço de aplicativo do Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Quando os recursos de soquete são esgotados
Um motivo comum usar todos TCP para conexões de saída é o uso de bibliotecas de cliente que não são implementadas reutilizar conexões TCP ou no caso de um protocolo de nível superior como HTTP - manutenção de funcionamento não está sendo utilizada. Revise a documentação para cada uma das bibliotecas referenciadas por aplicativos em seu plano de serviço de aplicativo para garantir que eles são configurados ou acessados em seu código para reutilização eficiente de conexões de saída. Além disso, siga as orientações de documentação biblioteca para criação correta e lançamento ou limpeza para evitar perdendo conexões. Enquanto tais investigações de bibliotecas de cliente estão em impacto de andamento pode ser minimizado dimensionando para várias instâncias.  

## <a name="appbackup"></a>Quando o seu aplicativo de backup inicia falhando
As duas razões mais comuns, por que a falha de backup de aplicativo está: configurações de armazenamento inválido e configuração do banco de dados inválidos. Normalmente, essas falhas acontecem quando há alterações aos recursos de armazenamento ou banco de dados, ou as alterações de como acessar esses recursos (por exemplo, credenciais atualizadas para o banco de dados selecionado nas configurações de backup). Backups normalmente executados em um cronograma e exigem acesso aos bancos de dados e de armazenamento (de saída o backup dos arquivos) (para copiar e ler o conteúdo a ser incluído no backup). O resultado da falha ao acessar qualquer um desses recursos seria falha de backup consistente. 

Quando ocorrem falhas de backup, examine os resultados mais recentes para compreender qual tipo de falha está acontecendo. No caso de falhas de acesso de armazenamento, revise e atualizar as configurações de armazenamento usadas na configuração do backup. No caso de falhas de acesso do banco de dados, revise e atualizar suas cadeias de caracteres de conexões como parte das configurações de aplicativo; em seguida, prossiga para atualizar a configuração de backup para incluir corretamente os bancos de dados necessários. Para obter mais informações sobre o backup do aplicativo, consulte a documentação de [fazer backup de um aplicativo web no serviço de aplicativo do Azure](web-sites-backup.md) .

## <a name="nodejs"></a>Quando novos aplicativos Node são implantados ao serviço de aplicativo do Azure
Azure configuração padrão de serviço de aplicativo do Node aplicativos destina-se para melhor atender às necessidades dos aplicativos mais comuns. Se a configuração para seu aplicativo Node seria beneficiar ajustes personalizadas para melhorar o desempenho ou otimizar o uso de recursos para os recursos de CPU/memória/rede, você pode revisar nossas práticas recomendadas e as etapas de solução de problemas. Este artigo documentação descreve as configurações de iisnode você talvez precise configurar para o aplicativo Node, descreve os diversos cenários ou problemas que seu aplicativo pode enfrentar e mostra como resolver esses problemas: [guia de solução de problemas para aplicativos de nós, serviço de aplicativo do Azure e práticas recomendadas](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   


