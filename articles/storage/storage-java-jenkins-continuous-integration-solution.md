<properties 
    pageTitle="Usando o armazenamento do Azure com uma solução de integração contínua Jenkins | Microsoft Azure" 
    description="Este tutorial mostra como usar o serviço do Azure blob como um repositório para construir artefatos criados por uma solução de integração contínua Jenkins." 
    services="storage" 
    documentationCenter="java" 
    authors="dineshmurthy" 
    manager="jahogg" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Usando o armazenamento do Azure com uma solução de integração contínua Jenkins

## <a name="overview"></a>Visão geral

As informações a seguir mostram como usar o armazenamento de Blob como um repositório de artefatos de compilação criado por uma solução Jenkins CI (integração contínua) ou como uma fonte de arquivos podem ser baixados para ser usado em um processo de compilação. Um dos cenários onde você descobrirá que isso útil é quando você está codificando em um ambiente de desenvolvimento ágil (usando Java ou outros idiomas), compilações são executados com base em integração contínua e você precisa de um repositório para seu artefatos de compilação, para que você poderia, por exemplo, compartilhá-los com outros membros da organização, os clientes, ou manter um arquivo morto. Outro cenário é quando seu trabalho de compilação próprio requer outros arquivos, por exemplo, dependências para baixar como parte da compilação de entrada.

Neste tutorial você utilizará o plug-in do Azure armazenamento for CI Jenkins disponibilizados pela Microsoft.

## <a name="overview-of-jenkins"></a>Visão geral de Jenkins ##

Jenkins permite integração contínua de um projeto de software, permitindo que desenvolvedores integrar facilmente suas alterações de código e tem compilações produzidas automaticamente e com frequência, portanto, aumentando a produtividade dos desenvolvedores. Compilações são versão e artefatos de compilação podem ser carregados no vários repositórios. Este tópico mostrará como usar o armazenamento de blob do Microsoft Azure como o repositório dos artefatos compilação. Ele também mostrará como baixar dependências do armazenamento de blob do Microsoft Azure.

Mais informações sobre Jenkins podem ser encontradas na [Jenkins atender](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Benefícios de usar o serviço de Blob ##

Benefícios de usar o serviço de Blob para hospedar seu artefatos de compilação de desenvolvimento ágil incluem:

- Alta disponibilidade do seu artefatos de compilação e/ou dependências para download.
- Desempenho quando sua solução CI Jenkins carrega seu artefatos de compilação.
- Desempenho quando seu artefatos de compilação de download de seus clientes e parceiros.
- Controle sobre políticas de acesso do usuário, com uma escolha entre acesso anônimo, acesso de assinatura de acesso compartilhado baseadas em expiração, particular acesso, etc.

## <a name="prerequisites"></a>Pré-requisitos ##

Você precisará procedimentos para usar o serviço de Blob com sua solução Jenkins CI:

- Uma solução de integração contínua Jenkins.

    Se você atualmente não tiver uma solução Jenkins CI, você pode executar uma solução de CI Jenkins usando a técnica a seguir:

    1. Em um computador habilitado para Java, baixe jenkins.war da <http://jenkins-ci.org>.
    2. Em um prompt de comando que é aberto para a pasta que contém jenkins.war, execute:

        `java -jar jenkins.war`

    3. No seu navegador, abra `http://localhost:8080/`. Isso abrirá no painel Jenkins, que você usará para instalar e configurar o plug-in de armazenamento do Azure.

        Embora uma solução CI Jenkins típica poderia ser configurada para executar como um serviço, executado guerra Jenkins na linha de comando será suficiente para este tutorial.

- Uma conta do Azure. Você pode se inscrever para uma conta do Azure em <http://www.azure.com>.

- Uma conta de armazenamento do Azure. Se você ainda não tiver uma conta de armazenamento, você pode criar um usando as etapas em [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

- Familiaridade com a solução Jenkins CI é recomendável, mas não necessário, como o seguinte conteúdo utilizará um exemplo básico para mostrar as etapas necessárias ao usar o serviço de Blob como um repositório para Jenkins CI construir artefatos.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Como usar o serviço de Blob com Jenkins CI ##

Para usar o serviço de Blob com Jenkins, você precisará instalar o plug-in de armazenamento do Azure, configurar o plug-in para usar sua conta de armazenamento e, em seguida, crie uma ação de pós-compilação que carrega os artefatos de compilação para sua conta de armazenamento. Essas etapas são descritas nas seções a seguir.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in de armazenamento do Azure ##

1. No painel de Jenkins, clique em **Gerenciar Jenkins**.
2. Na página **Gerenciar Jenkins** , clique em **Gerenciar plug-ins**.
3. Clique na guia **disponível** .
4. Na seção **Artefato Uploaders** , verifique o **plug-in de armazenamento do Microsoft Azure**.
5. Clique em **instalar sem reiniciar** ou **Baixar agora e instalar após a reinicialização**.
6. Reinicie Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in de armazenamento do Azure para usar sua conta de armazenamento ##

1. No painel de Jenkins, clique em **Gerenciar Jenkins**.
2. Na página **Gerenciar Jenkins** , clique em **Configurar sistema**.
3. Na seção **Configuração de conta de armazenamento do Microsoft Azure** :
    1. Insira seu nome de conta de armazenamento, que você pode obter a partir do [Portal do Azure](https://portal.azure.com).
    2. Insira sua chave de conta de armazenamento, também obtido a partir do [Portal do Azure](https://portal.azure.com).
    3. Use o valor padrão para o **URL de ponto de extremidade do serviço de Blob** se você estiver usando a nuvem Azure pública. Se você estiver usando uma nuvem Azure diferente, use o ponto de extremidade conforme especificado no [Portal do Azure](https://portal.azure.com) para sua conta de armazenamento. 
    4. Clique em **validar credenciais de armazenamento** para validar sua conta de armazenamento. 
    5. [Opcional] Se você tiver contas de armazenamento adicional que você deseja que sejam disponibilizados para sua CI Jenkins, clique em **Adicionar mais contas de armazenamento**.
    6. Clique em **Salvar** para salvar suas configurações.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de pós-compilação que carrega os artefatos de compilação para sua conta de armazenamento ##

Para fins de instrução, primeiro precisaremos criar um trabalho que criar vários arquivos e, em seguida, adicione na ação pós-compilação para carregar os arquivos em sua conta de armazenamento.

1. No painel de Jenkins, clique em **Novo Item**.
2. Nomear o trabalho **MyJob**, clique em **criar um projeto de software livre ao estilo**e clique em **Okey**.
3. Na seção **criar** da configuração do trabalho, clique em **Adicionar etapa de compilação** e escolha **executar Windows comando em lote**.
4. **Comando**, use os seguintes comandos:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Na seção **Ações de pós-compilação** da configuração do trabalho, clique em **Adicionar ação de pós-compilação** e escolha **carregar artefatos ao armazenamento de Blob do Azure**.
6. Para **nome da conta de armazenamento**, selecione a conta de armazenamento para usar.
7. Para **nome do contêiner**, especifique o nome do contêiner. (O recipiente será criado se ele ainda não existir quando os artefatos de compilação são carregados.) Você pode usar variáveis de ambiente, portanto para este exemplo, digite **${JOB_NAME}** como o nome do contêiner.

    **Dica**
    
    Abaixo do **comando** seção onde você inseriu um script para **executar Windows comando em lote** é um link para as variáveis de ambiente reconhecido pelo Jenkins. Clique nesse link para saber os nomes de variável de ambiente e descrições. Observe que as variáveis de ambiente que contêm caracteres especiais, como a variável de ambiente **BUILD_URL** , não são permitidas como um nome de contêiner ou o caminho virtual comum.

8. Clique em **Criar novo contêiner públicos por padrão** para este exemplo. (Se você quiser usar um contêiner particular, você precisará criar uma assinatura de acesso compartilhado para permitir o acesso. Que está além do escopo deste tópico. Você pode aprender mais sobre assinaturas de acesso compartilhado em [Assinaturas usando de acesso compartilhado (SAS)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcional] Clique em **contêiner limpa antes de carregar** se quiser que o contêiner ser limpos do conteúdo antes de artefatos de compilação são carregados (deixá-la desmarcada se você não quiser limpar o conteúdo do contêiner).
10. Para a **Lista de artefatos para carregar**, insira * *texto /*. txt**.
11. **Caminho virtual comum para artefatos carregados**, para fins deste tutorial, digite **${CONSTRUIR\_ID} / ${CONSTRUIR\_número}**.
12. Clique em **Salvar** para salvar suas configurações.
13. No painel Jenkins, clique em **Criar agora** para executar **MyJob**. Examine a saída do console de status. Mensagens de status para o armazenamento do Azure serão incluídas na saída do console quando a ação de compilação POST começa a carregar artefatos de compilação.
14. Após a conclusão bem-sucedida do trabalho, você pode examinar os artefatos de compilação abrindo o blob público.
    1. Faça logon no [Portal do Azure](https://portal.azure.com).
    2. Clique em **armazenamento**.
    3. Clique no nome da conta de armazenamento que você usou para Jenkins.
    4. Clique em **contêineres**.
    5. Clique no contêiner chamado **myjob**, que é a versão em letras minúsculas do nome do trabalho que você atribuiu quando você criou o trabalho Jenkins. Nomes de contêiner e blob estão em minúscula (e diferencia maiusculas de minúsculas) do armazenamento do Azure. Na lista de blobs para o contêiner chamado **myjob** , você deve ver **hello.txt** e **date.txt**. Copie a URL para um desses itens e abra-o no seu navegador. Você verá o arquivo de texto que foi carregado como um artefato de construção.

Somente uma ação de pós-compilação que carrega artefatos para armazenamento de blob do Microsoft Azure pode ser criada por trabalho. Observe que a ação pós-compilação carregar artefatos ao Azure blob storage pode especificar arquivos diferentes (incluindo caracteres curinga) e caminhos para arquivos em **Lista de artefatos carregar** usando ponto e vírgula como separador. Por exemplo, se sua compilação Jenkins produz arquivos JAR e TXT na pasta de **build** do seu espaço de trabalho e você deseja carregar ambos ao Azure blob storage, use o seguinte para o valor de **Lista de artefatos carregar** : **Construir /\*. jar; compilação /\*. txt**. Você também pode usar dois-pontos duplos sintaxe para especificar um caminho para usar dentro do nome de blob. Por exemplo, se você quiser os JARs para obter carregado usando **binários** no caminho blob e os arquivos TXT para obter carregado usando **avisos** no caminho blob, use o seguinte para o valor de **Lista de artefatos carregar** : **Construir /\*. jar::binaries; compilação /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar uma etapa de compilação que downloads do armazenamento de blob do Microsoft Azure ##

As etapas a seguir mostram como configurar uma etapa de compilação para fazer o download de itens do armazenamento de blob do Microsoft Azure. Isso seria útil se você quiser incluir itens em sua compilação, por exemplo, JARs que você mantém no Azure armazenamento de blob.

1. Na seção **criar** da configuração do trabalho, clique em **Adicionar build etapa** e escolha **baixar do armazenamento de Blob do Azure**.
2. Para **nome da conta de armazenamento**, selecione a conta de armazenamento para usar.
3. Para **nome do contêiner**, especifique o nome do recipiente que tenha os blobs que você deseja baixar. Você pode usar variáveis de ambiente.
4. Para **nome de Blob**, especifique o nome do blob. Você pode usar variáveis de ambiente. Além disso, você pode usar um asterisco, como um curinga depois que você especificar as letras iniciais do nome do blob. Por exemplo, **projeto\* ** especifique blobs todos os nomes que começam com o **project**.
5. [Opcional] Para **baixar o caminho**, especifique o caminho na máquina Jenkins onde você deseja baixar arquivos de armazenamento de blob do Microsoft Azure. Variáveis de ambiente também podem ser usadas. (Se você não fornecer um valor para **baixar o caminho**, os arquivos do armazenamento de blob do Microsoft Azure serão baixados para espaço de trabalho da tarefa.)

Se você tiver itens adicionais que você deseja baixar do armazenamento de blob do Microsoft Azure, você pode criar etapas de compilação adicionais.

Após executar uma compilação, você pode verificar a saída de console do histórico de compilação ou examine seu local de download, para ver se os blobs esperado foram baixados com êxito.  

## <a name="components-used-by-the-blob-service"></a>Componentes usados pelo serviço Blob ##

A seguir fornece uma visão geral dos componentes de serviço do Blob.

- **Conta de armazenamento**: todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Este é o nível mais alto do namespace para acessar blobs. Uma conta pode conter um número ilimitado de contêineres, como seu tamanho total está abaixo de 100TB.
- **Contêiner**: um contêiner fornece um agrupamento de um conjunto de blobs. Todos os blobs devem estar em um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.
- **Blob**: um arquivo de qualquer tipo e tamanho. Há dois tipos de bolhas que podem ser armazenados em armazenamento do Azure: blobs de página e de bloco. A maioria dos arquivos são blobs de bloco. Um blob de bloco único pode ter até 200GB de tamanho. Este tutorial usa blobs de bloco. Página blobs, outro tipo de blob, podem ser até 1TB em tamanho e são mais eficiente quando intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre blobs, consulte [Noções básicas sobre bloco Blobs, Blobs acrescentar e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Formato de URL**: Blobs são acessados usando o seguinte formato de URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (Se aplica o formato acima para a nuvem Azure pública. Se você estiver usando uma nuvem Azure diferente, use o ponto de extremidade dentro do [Portal do Azure](https://portal.azure.com) para determinar seu ponto de extremidade de URL.)

    No formato acima, `storageaccount` representa o nome da sua conta de armazenamento, `container_name` representa o nome do seu contêiner, e `blob_name` representa o nome do seu blob, respectivamente. Dentro do nome de contêiner, você pode ter vários caminhos, separados por uma barra invertida, **/**. O nome do contêiner de exemplo neste tutorial foi **MyJob**, e **${CONSTRUIR\_ID} / ${CONSTRUIR\_número}** foi usado para o caminho virtual comum, resultando em blob tendo uma URL do seguinte formato:

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Próximas etapas

- [Reunir Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
- [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
- [Referência SDK do cliente de armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Serviços de armazenamento do Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blog da equipe de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, consulte também o [Java Developer Center](https://azure.microsoft.com/develop/java/).