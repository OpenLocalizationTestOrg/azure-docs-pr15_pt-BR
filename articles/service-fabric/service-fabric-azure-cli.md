<properties
   pageTitle="Interagindo com clusters de serviço tecidos usando CLI | Microsoft Azure"
   description="Como usar o Azure CLI para interagir com um cluster de estrutura de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Usando a CLI do Azure para interagir com um Cluster de estrutura de serviço

Você pode interagir com cluster de estrutura de serviço de máquinas Linux utilizando a CLI Azure no Linux.

A primeira etapa é obter a versão mais recente da CLI do representante gito e configurá-lo no seu caminho usando os seguintes comandos:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Para cada comando, ele dá suporte, você pode digitar o nome do comando para obter a Ajuda do comando. Preenchimento automático tem suporte para os comandos. Por exemplo, o comando a seguir lhe ajuda para todos os comandos do aplicativo. 

```sh
 azure servicefabric application 
```

Você pode filtrar ainda mais a Ajuda para um comando específico, como mostra o exemplo a seguir:

```sh
 azure servicefabric application  create
```

Para ativar o preenchimento automático na CLI, execute os seguintes comandos:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Os seguintes comandos conectem ao cluster e mostram os nós no cluster:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Para usar parâmetros nomeados e encontre o que são, você pode digitar – ajuda após um comando. Por exemplo:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Ao conectar a um cluster de vários máquina da máquina **ou seja não faz parte do cluster**, use o seguinte comando:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Substitua a marca PublicIPorFQDN o IP real ou FQDN conforme apropriado. Ao conectar a um cluster de vários máquina da máquina **ou seja parte do cluster**, use o seguinte comando:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Você pode usar o PowerShell ou CLI para interagir com seu Cluster de estrutura de serviço Linux criado por meio do portal Azure. 

**Cuidado:** Esses clusters não seguros, portanto, você pode ser abrindo a caixa de um adicionando o endereço IP público no manifesto cluster.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Usando a CLI do Azure para se conectar a um Cluster de estrutura de serviço

Os seguintes comandos do Azure descrevem como se conectar a um cluster de seguro. Os detalhes do certificado devem corresponder um certificado em nós do cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Se seu certificado tiver (autoridades de certificação), você precisa adicionar o parâmetro de caminho de certificado de autoridade de certificação, como o exemplo a seguir: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se você tiver várias autoridades de certificação, use uma vírgula como o delimitador.
 
Se seu nome comum no certificado não coincidir com o ponto de extremidade de conexão, você pode usar o parâmetro `--strict-ssl` para ignorar a verificação, conforme mostrado no seguinte comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Se você gostaria de ignorar a verificação de autoridade de certificação, você pode adicionar-parâmetro não autorizado rejeitar conforme mostrado no seguinte comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Depois de conectar, você deve ser capaz de executar outros comandos para interagir com o cluster. 

## <a name="deploying-your-service-fabric-application"></a>Implantando seu aplicativo de serviço tecidos

Execute os seguintes comandos para copiar, registrar e iniciar o aplicativo de estrutura de serviço:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Atualizar seu aplicativo

O processo é semelhante ao [processo no Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Criar, copiar, registre e criar seu aplicativo do diretório raiz do projeto. Se sua instância de aplicativo é denominada tecidos: / MySFApp e o tipo for MySFApp, os comandos seria assim:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Faça as alterações ao seu aplicativo e recriar o serviço modificado.  Atualize o arquivo de manifesto do serviço modificado (ServiceManifest.xml) com as versões atualizadas para o serviço (e código ou Config ou dados conforme apropriado). Também modificar manifesto do aplicativo (ApplicationManifest.xml) com o número de versão atualizada para o aplicativo e o serviço modificado.  Agora, copie e registrar seu aplicativo atualizado usando os seguintes comandos:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Agora, você pode iniciar a atualização do aplicativo com o seguinte comando:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Agora você pode monitorar a atualização do aplicativo usando SFX. Em poucos minutos, o aplicativo seria foram atualizado.  Você também pode experimentar um aplicativo atualizado com um erro e verificar a funcionalidade de reversão automática em estrutura de serviço.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="copying-of-the-application-package-does-not-succeed"></a>Copiando do pacote de aplicativo não bem-sucedida

Verifique se `openssh` está instalado. Por padrão, área de trabalho do Ubuntu não tem instalada. Instalá-lo usando o seguinte comando:

```
 sudo apt-get install openssh-server openssh-client**
```

Se o problema persistir, tente desativar PAM para ssh alterando o arquivo de **sshd_config** usando os seguintes comandos:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Se o problema persistir, tente aumentar o número de ssh sessões executando os seguintes comandos:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
Usando as teclas para ssh autenticação (em vez de senhas) não ainda tem suporte (desde que a plataforma usa ssh para copiar pacotes), portanto use autenticação de senha em vez disso.


## <a name="next-steps"></a>Próximas etapas

Configurar o ambiente de desenvolvimento e implantar um aplicativo de serviço tecidos para um cluster de Linux.
