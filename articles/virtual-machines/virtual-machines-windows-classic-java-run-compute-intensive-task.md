<properties
    pageTitle="Aplicativo de Java pesados em uma máquina virtual | Microsoft Azure"
    description="Saiba como criar uma máquina virtual Azure que executa um aplicativo de Java pesados que pode ser monitorado por outro aplicativo Java."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Como executar uma tarefa pesados em Java em uma máquina virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Com o Azure, você pode usar uma máquina virtual para lidar com tarefas de pesados. Por exemplo, uma máquina virtual pode lidar com tarefas e fornecer resultados para computadores cliente ou aplicativos móveis. Após ler este artigo, você terá uma compreensão de como criar uma máquina virtual que executa um aplicativo de Java pesados que possível monitorar por outro aplicativo Java.

Este tutorial supõe que você sabe como criar aplicativos de console de Java, pode importar bibliotecas ao seu aplicativo Java e pode gerar um arquivo de Java (JAR). Nenhum conhecimento do Microsoft Azure será considerado.

Você aprenderá:

* Como criar uma máquina virtual com um Java Development Kit (JDK) já instalado.
* Como fazer logon remotamente sua máquina virtual.
* Como criar um namespace de barramento de serviço.
* Como criar um aplicativo Java que executa uma tarefa pesados.
* Como criar um aplicativo Java que monitora o andamento da tarefa pesados.
* Como executar os aplicativos Java.
* Como parar os aplicativos Java.

Este tutorial usará o problema do Caixeiro da tarefa pesados. A seguir é um exemplo do aplicativo Java executando a tarefa pesados.

![Solver Caixeiro problema][solver_output]

A seguir é um exemplo do aplicativo Java monitoramento a tarefa pesados.

![Cliente Caixeiro problema][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual

1. Faça logon no [portal de clássico Azure](https://manage.windowsazure.com).
2. Clique em **novo**, clique em **Calcular**, clique em **Máquina Virtual**e, em seguida, clique em **Galeria de**.
3. Na caixa de diálogo **Selecionar imagem da máquina Virtual** , selecione **JDK 7 Windows Server 2012**.
Observe que **JDK 6 Windows Server 2012** está disponível caso você tenha aplicativos legados que ainda não estiver prontos para ser executado em JDK 7.
4. Clique em **Avançar**.
4. Na caixa de diálogo **configuração de máquina Virtual** :
    1. Especifique um nome para a máquina virtual.
    2. Especifique o tamanho para usar para a máquina virtual.
    3. Insira um nome para o administrador no campo **Nome de usuário** . Lembre-se esse nome e a senha que você digitará Avançar, você irá usá-los ao remotamente fazer logon máquina virtual.
    4. Digite uma senha no campo **nova senha** e digite-a novamente no campo **Confirmar** . Esta é a senha da conta de administrador.
    5. Clique em **Avançar**.
5. Na caixa de diálogo **configuração de máquina Virtual** seguinte:
    1. **Serviço de nuvem**, use o padrão de **criar um novo serviço de nuvem**.
    2. O valor de **nome DNS de serviço de nuvem** deve ser exclusivo em cloudapp.net. Se necessário, modificar esse valor para que esse Azure indica que ele seja exclusivo.
    2. Especifique uma região, grupo de afinidade ou rede virtual. Para fins deste tutorial, especifique uma região como **Oeste EUA**.
    2. **Conta de armazenamento**, selecione **usar uma conta de armazenamento gerado automaticamente**.
    3. Para **Definir de disponibilidade**, selecione **(nenhum)**.
    4. Clique em **Avançar**.
5. Em final caixa de diálogo de **configuração de máquina Virtual** :
    1. Aceite as entradas de ponto de extremidade padrão.
    2. Clique em **Concluir**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Faça logon em sua máquina virtual remotamente

1. Faça logon no [portal de clássico Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual que você deseja fazer logon no.
4. Clique em **Conectar**.
5. Responda às solicitações conforme necessário para se conectar à máquina virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando você criou a máquina virtual.

Observe que a funcionalidade de barramento de serviço do Azure requer o certificado de Baltimore CyberTrust Root será instalado como parte do repositório de **cacerts** do seu JRE. Este certificado é automaticamente incluído no Java Runtime ambiente (JRE) usados por este tutorial. Se você não tiver este certificado no seu armazenamento de **cacerts** JRE, consulte [Adicionar um certificado para o armazenamento de certificado de autoridade de certificação Java] [ add_ca_cert] para obter informações sobre como adicionar-(bem como informações sobre como visualizar os certificados em seu armazenamento de cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Como criar um namespace de barramento de serviço

Para começar a usar filas de barramento de serviço no Azure, você deve primeiro criar um namespace de serviço. Um namespace de serviço fornece um contêiner de escopo para endereçar recursos de barramento de serviço dentro de seu aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [portal de clássico Azure](https://manage.windowsazure.com).
2.  No painel inferior esquerdo do Azure portal clássico, clique em **barramento de serviço, controle de acesso e cache**.
3.  No painel superior esquerdo do Azure portal clássico, clique no nó **Barramento de serviço** e, em seguida, clique no botão **novo** .  
    ![Captura de tela de nó de barramento de serviço][svc_bus_node]
4.  Na caixa de diálogo **criar um novo Namespace de serviço** , insira um **Namespace**e para garantir que ele seja exclusivo, clique no botão **Verificar disponibilidade** .  
    ![Criar uma captura de tela do novo Namespace][create_namespace]
5.  Depois de garantir que o nome do namespace estiver disponível, escolha o país ou região no qual seu namespace deve estar hospedada e clique no botão **Criar Namespace** .  

    Namespace que você criou aparecerão no portal de clássico do Azure e leva alguns instantes para ativar. Aguarde até que o status for **ativo** antes de continuar com a próxima etapa.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obtenha as credenciais de gerenciamento padrão do namespace

Para realizar operações de gerenciamento, como criar uma fila, em novo namespace, você precisa obter as credenciais de gerenciamento do namespace.

1.  No painel de navegação esquerdo, clique no nó **Barramento de serviço** para exibir a lista de namespaces disponíveis.
    ![Captura de tela de Namespaces disponível][avail_namespaces]
2.  Selecione o namespace que você acabou de criar na lista mostrada.
    ![Captura de tela da lista de Namespace][namespace_list]
3.  Painel de **Propriedades** direito lista as propriedades para o novo namespace.
    ![Captura de tela de painel de propriedades][properties_pane]
4.  A **Chave padrão** está oculta. Clique no botão do **modo de exibição** para exibir as credenciais de segurança.
    ![Captura de tela de chave padrão][default_key]
5.  Anote o **Emissor padrão** e a **Chave padrão** conforme você usará essas informações abaixo para executar operações com o namespace.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Como criar um aplicativo Java que executa uma tarefa pesados

1. Em sua máquina de desenvolvimento (que não precisa ser a máquina virtual que você criou), baixe o [SDK do Azure para Java](https://azure.microsoft.com/develop/java/).
2. Crie um aplicativo de console Java usando o código de exemplo no final desta seção. Neste tutorial, usaremos **TSPSolver.java** como o nome de arquivo Java. Modificar a **sua\_service\_barramento\_namespace**, **seu\_serviço\_barramento\_proprietário**, e **seu\_serviço\_barramento\_chave** espaços reservados para usar seu serviço barramento **namespace**, **Emissor padrão** e valores de **Chave padrão** , respectivamente.
3. Depois de codificação, exportar o aplicativo para um arquivo executável do Java (JAR) e empacotar as bibliotecas necessárias em JAR gerado. Neste tutorial, usaremos **TSPSolver.jar** como o nome JAR gerado.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Como criar um aplicativo Java que monitora o andamento da tarefa pesados

1. Em sua máquina de desenvolvimento, crie um aplicativo de console Java usando o código de exemplo no final desta seção. Neste tutorial, usaremos **TSPClient.java** como o nome de arquivo Java. Como mostrado anteriormente, modificar o **seu\_service\_barramento\_namespace**, **seu\_serviço\_barramento\_proprietário**, e **seu\_serviço\_barramento\_chave** espaços reservados para usar seu serviço barramento **namespace**, **Emissor padrão** e valores de **Chave padrão** , respectivamente.
2. Exportar o aplicativo para um executável JAR e empacotar as bibliotecas necessárias em JAR gerado. Neste tutorial, usaremos **TSPClient.jar** como o nome JAR gerado.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Como executar os aplicativos Java
Execute o aplicativo de pesados, primeiro para criar a fila, em seguida, para resolver o problema de Saleseman viajando, que adicionará a melhor rota atual para a fila de barramento de serviço. Enquanto o aplicativo pesados está em execução (ou posteriormente), execute o cliente para exibir os resultados da fila de barramento de serviço.

### <a name="to-run-the-compute-intensive-application"></a>Para executar o aplicativo pesados

1. Faça logon sua máquina virtual.
2. Crie uma pasta onde você irá executar o aplicativo. Por exemplo, **c:\TSP**.
3. Copie **TSPSolver.jar** para **c:\TSP**,
4. Crie um arquivo denominado **c:\TSP\cities.txt** com o seguinte conteúdo.

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. No prompt de comando, altere diretórios para c:\TSP.
6. Certifique-se de que pasta de compartimento do JRE está na variável de ambiente PATH.
7. Você precisará criar fila de barramento de serviço antes de executar as permutações de solver TSP. Execute o seguinte comando para criar a fila de barramento de serviço.

        java -jar TSPSolver.jar createqueue

8. Agora que a fila é criada, você pode executar as permutações de solver TSP. Por exemplo, execute o seguinte comando para executar o solver para 8 cidades.

        java -jar TSPSolver.jar 8

 Se você não especificar um número, ele será executado para 10 cidades. Como o solver localiza rotas mais curta atuais, ele será adicioná-los à fila.

> [AZURE.NOTE]
> Quanto maior o número que você especificar, mais tempo o solver será executada. Por exemplo, sendo executado 14 cidades podem levar alguns minutos e executado por 15 cidades pode levar horas. Aumentar para cidades 16 ou mais pode resultar em dias do tempo de execução (eventualmente semanas, meses e anos). Isso é devido ao rápido aumento no número de permutações avaliada pelo solver como o número de cidades aumenta.

### <a name="how-to-run-the-monitoring-client-application"></a>Como executar o aplicativo cliente monitoramento
1. Faça logon no seu computador onde você será executado o aplicativo cliente. Isso não precisa ser a mesma máquina executando o aplicativo de **TSPSolver** , embora ele possa ser.
2. Crie uma pasta onde você irá executar o aplicativo. Por exemplo, **c:\TSP**.
3. Copie **TSPClient.jar** para **c:\TSP**,
4. Certifique-se de que pasta de compartimento do JRE está na variável de ambiente PATH.
5. No prompt de comando, altere diretórios para c:\TSP.
6. Execute o seguinte comando.

        java -jar TSPClient.jar

    Opcionalmente, especifique o número de minutos em suspensão entre os verificação fila, passando um argumento de linha de comando. O período de suspensão padrão para verificação fila é 3 minutos, que é usado se nenhum argumento de linha de comando é passado para **TSPClient**. Se você quiser usar um valor diferente para o intervalo de suspensão, por exemplo, um minuto, execute o seguinte comando.

        java -jar TSPClient.jar 1

    O cliente serão executadas até que ele veja uma mensagem de fila de "Concluído". Observe que, se você executar várias ocorrências de solver sem executar o cliente, você precisará executar o cliente várias vezes para esvaziar completamente fila. Como alternativa, você pode excluir a fila e criá-lo novamente. Para excluir a fila, execute o seguinte comando **TSPSolver** (não **TSPClient**).

        java -jar TSPSolver.jar deletequeue

    O solver serão executadas até que ele termine examinando todas as rotas.

## <a name="how-to-stop-the-java-applications"></a>Como parar os aplicativos Java
Para o solver e aplicativos cliente, você pode pressionar **Ctrl + C** para sair se você quiser encerrar antes da conclusão normal.


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
