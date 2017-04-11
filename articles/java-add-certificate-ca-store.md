<properties 
    pageTitle="Adicionar um certificado ao armazenamento de autoridade de certificação Java | Microsoft Azure" 
    description="Saiba como adicionar um certificado de autoridade de certificação de certificado ao armazenamento de certificados (cacerts) Java CA de serviço de Twilio ou barramento de serviço do Azure." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Adicionando um certificado para o repositório de certificados de autoridade de certificação Java
As etapas a seguir mostram como adicionar um certificado de autoridade de certificação de certificado ao armazenamento de certificados (cacerts) Java CA. O exemplo usado é para o certificado de autoridade de certificação necessário pelo serviço Twilio. Informações fornecidas posteriormente no tópico descrevem como instalar o certificado para o barramento de serviço do Azure. 

Você pode usar keytool para adicionar o certificado de autoridade de certificação antes compactados seu JDK e adicioná-lo à pasta de **approot** do seu projeto Azure ou você pode executar uma tarefa de inicialização Azure que usa keytool para adicionar o certificado. Este exemplo pressupõe que você irá adicionar um certificado antes do JDK sendo zipado. Além disso, o certificado específico a autoridade de certificação será usado no exemplo, mas as etapas de obtenção de um certificado diferente e importá-lo na loja cacerts seria similares.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Para adicionar um certificado no repositório de cacerts

1. No prompt de comando é definido como pasta de **jdk\jre\lib\security** do seu JDK, execute o seguinte para ver quais certificados estão instalados:

    `keytool -list -keystore cacerts`

    Você será solicitado a senha de armazenamento. A senha padrão é **changeit**. (Se você quiser alterar a senha, consulte a documentação de keytool em <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Este exemplo pressupõe que o certificado com MD5 impressão digital 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 não estiver listado, e que você deseja importá-lo (esse certificado específico é necessário pelo serviço Twilio API).
2. Obtenha o certificado da lista de certificados listados em [Certificados raiz de GeoTrust](http://www.geotrust.com/resources/root-certificates/). Clique com botão direito no link para o certificado com o número de série 35:DE:F4:CF e salve-o para a pasta **jdk\jre\lib\security** . Para fins deste exemplo, ele foi salvo em um arquivo denominado **Equifax\_seguro\_certificado\_Authority.cer**.
3. Importe o certificado por meio do comando a seguir:

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Quando solicitado a confiar este certificado, se o certificado tiver 67:CB:9 de impressão digital MD5 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responder digitando **y**.
4. Execute o seguinte comando para garantir que o certificado foi importado com êxito:

    `keytool -list -keystore cacerts`

5. ZIP o JDK e adicioná-lo à pasta de **approot** do seu projeto Azure.

Para obter informações sobre keytool, consulte <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificados raiz Azure

Os aplicativos que usam serviços Azure (como barramento de serviço do Azure) precisam confiar no certificado de Baltimore CyberTrust Root. (A partir de 15 de abril de 2013, Azure começou migrando da raiz GTE CyberTrust Global para Baltimore CyberTrust raiz. Essa migração levou vários meses para ser concluída.)

O Baltimore certificado pode já estar instalado no seu armazenamento de cacerts, portanto se lembrar de executar o **keytool-lista** comando primeiro para ver se ele já existe.

Se você precisar adicionar Baltimore CyberTrust raiz, ele tem 02:00:00:b9 de número de série e SHA1 impressão digital d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Ele pode ser baixado de <https://cacert.omniroot.com/bc2025.crt>, salva um arquivo local com extensão **. cer**e importados usando **keytool** conforme mostrado acima.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os certificados raiz usado pelo Azure, consulte [Migração de certificado raiz do Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Para obter mais informações sobre Java, consulte o [Java Developer Center](/develop/java/).
