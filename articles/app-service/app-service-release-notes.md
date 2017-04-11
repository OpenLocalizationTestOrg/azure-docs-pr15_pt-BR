<properties 
   pageTitle="Notas de versão do SDK do Azure para .NET 2.5.1" 
   description="Notas de versão do SDK do Azure para .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Notas de versão do SDK do Azure para .NET 2.5.1

Este documento contém as notas de versão para o SDK do Azure para .NET 2.5.1 de lançamento. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Notas de versão do SDK do Azure para .NET 2.5.1

A seguir estão novos recursos e atualizações no SDK do Azure para .NET 2.5.1.

- Novo features\scenarios relacionados às **Extensões de ferramentas da Web**. 

    - Sites Azure foi renomeado para o serviço de aplicativo do Azure. Para mais informações, consulte [o serviço de aplicativo do Azure e os serviços do Azure existente](app-service-changes-existing-services.md).
    - Suporte de aplicativos de API (visualização) Azure foi adicionado para que clientes possam publicar projetos ASP.NET como Apps API e, em seguida, use o Adicionar > gesto de cliente de aplicativo de API do Azure em c# projetos para gerar código baseado na estrutura do App API implantado. 
    - O nó de sites em Server Explorer foi substituída substitui o nó de serviço de aplicativo do Azure, que contém suporte para o recurso de agrupamento de aplicativos de API do Azure, aplicativos Mobile e aplicativos Web baseados em grupo.
    - Suporte do Azure aplicativos Mobile (visualização) foi adicionado para que os clientes podem criar novos projetos de aplicativos móveis, adicionar controladores de aplicativos Mobile, publicar os projetos e remotamente depurar aplicativos.
    - Adicionar > gesto de cliente de aplicativo de API do Azure agora dá suporte a arquivos JSON Swagger locais, para que os desenvolvedores de Web API podem usar NuGets de terceiros como Swashbuckle para gerar Swagger ou criá-lo manualmente. Dessa maneira, os desenvolvedores do cliente podem usar os recursos de geração de código para consumir qualquer ponto de extremidade de Swagger em projetos c#. 
    - Web App e caixas de diálogo publicação API App foram aprimoradas para o conceito de Portal Azure do recurso de agrupamento de suporte e seleção/criação de grupos de recursos do Azure e planos de serviço de aplicativo são representados na caixa nova Web App e API App provisionamento de diálogo. 
    - Nós de API App Server Explorer Azure fornecem links para o link de profunda de aplicativos de API no Portal do Azure, bem como outros recursos como Streaming de Log e depuração remota.

    Para problemas conhecidos e limitações atuais Azure SDK .NET 2.5.1 [nesta](app-service-release-notes.md#known_issues_2_5_1) seção abaixo.


- Novo features\scenarios relacionados ao **HDInsight ferramentas** no Visual Studio estão habilitados nesta versão. 
    - Validação de scripts de seção. Clique no botão de script validar na barra de ferramentas para ver se há erros no script. 
    - Aprimorado depuração de trabalhos de seção. Agora você pode depurar trabalhos de seção acessando logs de fio colorido no Visual Studio. Se seu aplicativo tiver problemas de desempenho, investigar logs de fio COLORIDO fornecerá informações úteis..
    - (Público Preview) Suporte a palavra-chave AutoCompletar e IntelliSense para seção. Para ajudar a criar scripts de seção, HDInsight ferramentas para Visual Studio adicionado suporte AutoCompletar de palavra-chave e IntelliSense para seção.
    - Suporte de tempestade. Agora você pode usar ferramentas de HDInsight para Visual Studio para desenvolver tempestade topologias/Spouts/parafusos em c#. Você pode enviar a topologia desenvolvida para um cluster de tempestade e ver o status de raio/topologia/spout. Você pode usar logs de sistema e atendimento ao cliente para solucionar seu tempestade parafusos/topologias/Spouts. Você também pode usar ativos JAVA existentes em tempestade em HDInsight.
    
    Para obter mais informações, consulte [Introdução ao uso de ferramentas de Hadoop HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>SDK do Azure para .NET 2.5.1 problemas conhecidos e limitações

- Aplicativos do Azure API fica visível como um destino de implantação para aplicativos Mobile. Aplicativos Web deve ser o destino apenas para aplicativos móvel até uma versão subsequente. 
- Provisionamento de aplicativo de API Azure pode resultar em sucesso mas intermitentemente falhar ao atualizar o progresso na janela de atividade de serviço de aplicativo do Azure. Solução alternativa é verificar o status do novo aplicativo de API do Azure no Portal do Azure. 
- Arquivo > Novo projeto > API App > experiência de F5 resulta em um erro HTTP porque não há nenhum default/index.html. Solução alternativa é manualmente, navegue até a URL de /api/values. 
- Intermitentemente, os ícones de Server Explorer aparecem plana. Reiniciar VS resolve esse problema. 
- Se uma exceção é lançada durante provisionar o aplicativo Web ou API App (como erros de cota excedida ou nome do gateway de aplicativo de API do Azure duplicado), os erros mostram algum texto JSON bruto. 
- Problemas de criação de projeto intermitente quando ideias de aplicativo está marcada no momento da criação de projeto.
- Ocasionalmente, o código do cliente de aplicativo do Azure API gerado está faltando namespaces, eles precisam ser incluídos manualmente (ou automaticamente importados via indicações do Visual Studio) para o código de compilação. 
- Projetos de aplicativo móvel devem ser publicados em aplicativos web, mas você deve escolher um site que você criou como um aplicativo móvel no Portal do Azure como projetos de aplicativo móvel exigem um banco de dados. 
- A página inicial para aplicativos móvel usa o termo "serviço móvel" em vez de "aplicativos móveis" 
- Criação de projeto de aplicativo móvel pode levar a um minuto para criar. 
- Durante a API App um erro de provisionamento (em alguns casos) provenientes novamente a API do Azure refletindo que as permissões não podem ser definidas corretamente, enquanto o App API foi configurado corretamente e está pronto para uso. Você pode definir manualmente as permissões usando o Portal do Azure.
- Obtenção de informações de aplicativo não é suportado em modelos de API App e modelos de aplicativo Mobile.
- API App projetos não podem ser usados em conjunto com projetos de serviço de nuvem.
- Modelos de projeto de API App só estão disponíveis em c#.
- Consumo de API App por meio do menu de contexto "Adicionar Azure API aplicativo cliente" só é suportado em c#.

 
