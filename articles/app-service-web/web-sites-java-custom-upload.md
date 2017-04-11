<properties 
    pageTitle="Carregar um aplicativo web personalizado do Java no Azure" 
    description="Este tutorial mostra como carregar um aplicativo web personalizado do Java para aplicativos do Azure aplicativo de serviço Web." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="upload-a-custom-java-web-app-to-azure"></a>Carregar um aplicativo web personalizado do Java no Azure

Este tópico explica como carregar um aplicativo de web Java personalizado para [O serviço de aplicativo do Azure] Web Apps. Informações que se aplicam a qualquer aplicativo web ou site de Java e também alguns exemplos para aplicativos específicos está incluído.

Observe que o Azure fornece um meio para criar aplicativos da web de Java usando a configuração do Portal do Azure UI e do Azure Marketplace, como documentadas ao [criar um aplicativo da web de Java no serviço de aplicativo do Azure](web-sites-java-get-started.md). Este tutorial é para cenários em que você não deseja usar a configuração de Portal Azure UI ou do Azure Marketplace.  

## <a name="configuration-guidelines"></a>Diretrizes de configuração

A seguir descreve as configurações esperadas para Java web apps personalizados no Azure.

- A porta HTTP usada pelo processo Java é atribuída dinamicamente.  O processo deve usar a porta a partir da variável de ambiente `HTTP_PLATFORM_PORT`.
- Todas as portas de ouvir diferente o ouvinte HTTP único devem ser desativadas.  Em Tomcat, que inclui o desligamento, HTTPS e AJP portas.
- O contêiner precisa ser configurada para tráfego de IPv4 somente.
- O comando de **inicialização** para o aplicativo precisa ser definido na configuração.
- Aplicativos que exigem diretórios com escrever permissão precisam ser localizados no diretório de conteúdo do aplicativo web Azure, que é **D:\home**.  A variável de ambiente `HOME` se refere a D:\home.  

Você pode definir variáveis de ambiente conforme necessário no arquivo Web. config.

## <a name="webconfig-httpplatform-configuration"></a>configuração de httpPlatform Web. config

As informações a seguir descrevem o formato de **httpPlatform** em Web. config.
                                 
**argumentos** (Padrão = ""). Argumentos para o executável ou script especificado na configuração **processPath** .

Exemplos (mostrados com **processPath** incluídos):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** - caminho para o executável ou script que iniciará um processo escutar solicitações HTTP.

Exemplos:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (Padrão = 10.) Número de vezes que o processo especificado no **processPath** é permitido falhar por minuto. Se esse limite for excedido, **HttpPlatformHandler** irá parar de iniciar o processo para o restante do minuto.
                                    
**requestTimeout** (Padrão = "00: 02:00".) Duração para a qual **HttpPlatformHandler** esperará por uma resposta do processo de ouvir `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (Padrão = 10.) Número de vezes **que httpplatformhandler** tentará iniciar o processo especificado no **processPath**. Consulte **startupTimeLimit** para obter mais detalhes.

**startupTimeLimit** (Padrão = 10 segundos.) Duração pela qual **HttpPlatformHandler** irá aguardar para que o executável/script iniciar um processo escutar na porta.  Se esse limite de tempo for excedido, **HttpPlatformHandler** irá interromper o processo e tente iniciá-lo novamente **startupRetryCount** vezes.
                                                                                      
**stdoutLogEnabled** (Padrão = "true".) Se verdadeiro, **stdout** e **stderr** para o processo especificado na configuração de **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile** (consulte a seção **stdoutLogFile** ).
                                    
**stdoutLogFile** (Default="d:\home\LogFiles\httpPlatformStdout.log".) Caminho de arquivo absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** serão registrados.
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%`é um espaço reservado especial que precisa especificado como parte dos **argumentos** ou como parte da lista de **environmentVariables** **httpPlatform** . Isso será substituído por uma porta gerada internamente por **HttpPlatformHandler** para que o processo especificado pelo **processPath** pode ouvir nesta porta.

## <a name="deployment"></a>Implantação

Aplicativos web baseados em Java aplicativos web baseados em podem ser implantados facilmente a maioria da mesma maneira que é usada com os serviços de informações de Internet (IIS).  FTP, gito e Kudu há suporte para como mecanismos de implantação, assim como a capacidade SCM integrada para web apps. WebDeploy funciona como um protocolo, no entanto, como Java não é desenvolvido no Visual Studio, WebDeploy não se encaixa com casos de uso de implantação do Java web app.

## <a name="application-configuration-examples"></a>Exemplos de configuração de aplicativo

Para os seguintes aplicativos, um arquivo Web. config e o aplicativo de configuração é fornecida como exemplos para mostrar como habilitar o seu aplicativo Java no aplicativo de serviço Web Apps.

### <a name="tomcat"></a>Tomcat
Embora existam duas variações em porta de destino que são fornecidas com o aplicativo de serviço Web Apps, ainda é bastante possível carregar instâncias específicas do cliente. Abaixo está um exemplo de uma instalação do Tomcat com uma Java Virtual Machine (JVM diferente).

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
            arguments="">
          <environmentVariables>
            <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
            <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
            <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

No lado Tomcat, há algumas alterações de configuração que precisam ser feitas. XML precisa ser editado para definir:

-   Porta de encerramento = -1
-   Porta do conector HTTP = ${port.http}
-   Endereço do conector HTTP = "127.0.0.1"
-   Comentar conectores HTTPS e AJP
-   A configuração de IPv4 também pode ser definida no arquivo catalina.properties onde você pode adicionar`java.net.preferIPv4Stack=true`
    
Não há suporte para chamadas de Direct3D no aplicativo de serviço Web Apps. Para desabilitar os, adicione a seguinte opção Java deve seu aplicativo tornar essas chamadas:`-Dsun.java2d.d3d=false`

### <a name="jetty"></a>Jetty

Assim como para Tomcat, clientes podem carregar suas próprias instâncias de Jetty. No caso de executando a instalação completa do Jetty, a configuração teria esta aparência:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
             arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
            startupTimeLimit="20"
          startupRetryCount="10"
          stdoutLogEnabled="true">
        </httpPlatform>
      </system.webServer>
    </configuration>

A configuração de Jetty precisa ser mudada a start.ini definir `java.net.preferIPv4Stack=true`.

### <a name="springboot"></a>Springboot
Para obter uma Springboot aplicativo em execução, você precisa carregar seu arquivo JAR ou guerra e adicione o seguinte arquivo Web. config. O arquivo Web. config entra em pasta wwwroot. Na Web. config ajuste os argumentos para apontar para o seu arquivo JAR, no exemplo a seguir, que o arquivo JAR está localizado na pasta wwwroot.  

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
            arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\my-web-project.jar&quot;">
        </httpPlatform>
      </system.webServer>
    </configuration>


### <a name="hudson"></a>Hudson

Nosso teste usado guerra Hudson 3.1.2 e a instância do Tomcat 7.0.50 padrão, mas sem usar a interface do usuário para configurar as coisas.  Como Hudson é a ferramenta de compilação de um software, é aconselhável instalá-lo em dedicado instâncias onde é possível definir o sinalizador **sempre ativado** do aplicativo web.

1. No diretório raiz da seu aplicativo web, ou seja, **d:\home\site\wwwroot**, crie um diretório de **webapps** (se não estiver presente) e coloque Hudson.war em **d:\home\site\wwwroot\webapps**.
2. Baixe maven apache 3.0.5 (compatível com Hudson) e colocá-lo em **d:\home\site\wwwroot**.
3. Criar Web. config em **d:\home\site\wwwroot** e cole o seguinte conteúdo nele:
    
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <handlers>
              <add name="httppPlatformHandler" path="*" verb="*" 
        modules="httpPlatformHandler" resourceType="Unspecified" />
            </handlers>
            <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
        startupTimeLimit="20"
        startupRetryCount="10">
        <environmentVariables>
          <environmentVariable name="HUDSON_HOME" 
        value="%HOME%\site\wwwroot\hudson_home" />
          <environmentVariable name="JAVA_OPTS" 
        value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
        </environmentVariables>            
            </httpPlatform>
          </system.webServer>
        </configuration>

    Neste ponto, o aplicativo da web pode ser reiniciado para fazer as alterações.  Conecte a http://yourwebapp/hudson para iniciar Hudson.

4. Depois de se configura Hudson, você verá a tela a seguir:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Acessar a página de configuração de Hudson: clique em **Gerenciar Hudson**e clique em **Configurar sistema**.
6. Configure o JDK conforme mostrado abaixo:

    ![Configuração de Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configure Maven conforme mostrado abaixo:

    ![Configuração de Maven](./media/web-sites-java-custom-upload/maven.png)

8. Salve as configurações. Hudson agora deve ser configurado e pronto para uso.

Para obter informações adicionais sobre Hudson, consulte [http://hudson-ci.org](http://hudson-ci.org).

### <a name="liferay"></a>Liferay

Liferay é suportado no aplicativo de serviço Web Apps. Como Liferay pode exigir memória significativa, o aplicativo web precisa executar em um trabalho de dedicados médio ou grande, que pode fornecer memória suficiente. Liferay também leva vários minutos para iniciar. Por que motivo, é recomendável que você defina o web app para **Sempre**.  

Os seguintes arquivos usando Liferay 6.1.2 que comunidade Edition GA3 fornecido com o Tomcat, tenham sido editados após o download Liferay:

**XML**

- Altere a porta de encerramento para -1.
- Conector de alteração HTTP para      `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Comente o conector AJP.

Na pasta **liferay\tomcat-7.0.40\webapps\ROOT\WEB-INF\classes** , crie um arquivo denominado **ext.properties portal**. Este arquivo precisa conter uma linha, como mostrado aqui:

    liferay.home=%HOME%/site/wwwroot/liferay

No mesmo nível do diretório como a pasta tomcat-7.0.40, crie um arquivo denominado **Web. config** com o seguinte conteúdo:

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"
         modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
                      arguments="run" 
                      startupTimeLimit="10" 
                      requestTimeout="00:10:00" 
                      stdoutLogEnabled="true">
          <environmentVariables>
      <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
      <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
            <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Sob o bloco de **httpPlatform** , o **requestTimeout** está definido como "00:10:00".  Ele pode ser reduzido mas então você provavelmente ver alguns erros de tempo limite enquanto Liferay está carregando.  Se esse valor for alterado, o **connectionTimeout** em XML tomcat também deve ser modificadas.  

Vale a pena observar que os varariable de ambiente JRE_HOME especificado no Web. config acima para apontar para o JDK de 64 bits. O padrão é 32 bits, mas como Liferay podem exigir altos níveis de memória, é recomendável usar o JDK de 64 bits.

Após fazer essas alterações, reinicie o aplicativo web executando Liferay, em seguida, abra http://yourwebapp. O portal de Liferay está disponível na raiz do aplicativo da web. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Liferay, consulte [http://www.liferay.com](http://www.liferay.com).

Para obter mais informações sobre Java, consulte o [Java Developer Center](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[Serviço de aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
