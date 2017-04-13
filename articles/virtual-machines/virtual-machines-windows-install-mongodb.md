<properties
    pageTitle="Instalar o MongoDB em um máquina virtual do Windows | Microsoft Azure"
    description="Saiba como instalar MongoDB em uma VM Azure executando o Windows Server 2012 R2 criados com o modelo de implantação do Gerenciador de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar e configurar o MongoDB em uma máquina de virtual do Windows no Azure
[MongoDB](http://www.mongodb.org) é um popular código-fonte aberto alto desempenho NoSQL banco de dados. Este artigo o orientará na instalando e configurando MongoDB em uma máquina virtual do Windows Server 2012 R2 (máquina virtual) no Azure. Você também pode [instalar o MongoDB em uma VM Linux no Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e configurar MongoDB, você precisa criar uma máquina virtual e, adicionar ideal seria um disco de dados a ele. Consulte os artigos a seguir para criar uma máquina virtual e adicionar um disco de dados:

- [Criar uma máquina de virtual do Windows Server usando o portal de Azure](virtual-machines-windows-hero-tutorial.md) ou [criar uma máquina de virtual do Windows Server usando o PowerShell do Azure](virtual-machines-windows-ps-create.md)
- [Anexar um disco de dados para um servidor de Windows máquina virtual usando o portal do Azure](virtual-machines-windows-attach-disk-portal.md) ou [anexar um disco de dados para uma máquina virtual Server do Windows Azure PowerShell](https://msdn.microsoft.com/library/mt603673.aspx)
    
Para começar a MongoDB instalando e configurando, [Faça logon no seu servidor de Windows máquina virtual](virtual-machines-windows-connect-logon.md) usando a área de trabalho remota.


## <a name="install-mongodb"></a>Instalar o MongoDB

> [AZURE.IMPORTANT] Recursos de segurança do MongoDB, como autenticação e associação de endereço IP, não estão ativados por padrão. Recursos de segurança devem ser habilitados antes de implantar MongoDB em um ambiente de produção. Para obter mais informações, consulte [MongoDB segurança e autenticação](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Depois que você conectou à sua máquina virtual usando a área de trabalho remota, abra o Internet Explorer no menu **Iniciar** a máquina virtual.

2. Selecione **recomendável usar configurações de segurança, privacidade e compatibilidade** quando abre o Internet Explorer pela primeira vez e clique **Okey**.

3. Configuração de segurança aprimorada do Internet Explorer é ativada por padrão. Adicione o site MongoDB à lista de sites permitidos:

    - Selecione o ícone de **Ferramentas** no canto superior direito.
    - Nas **Opções da Internet**, selecione a guia **segurança** e selecione o ícone **Sites confiáveis** .
    - Clique no botão **Sites** . Adicionar _https://\*. mongodb.org_ à lista de sites confiáveis e feche a caixa de diálogo.

    ![Definir configurações de segurança do Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Navegue até a página de [Downloads do MongoDB -](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. Por padrão, ele deve selecionar a edição de **Servidor de comunidade** e a última versão estável atual para o Windows Server 2008 R2 64 bits e posterior. Para baixar o instalador, clique em **DOWNLOAD (msi)**.

    ![Baixar o instalador do MongoDB](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Execute o instalador após o download for concluído.

6. Leia e aceite o contrato de licença. Quando solicitado, selecione Instalar de **concluída** .

7. Na tela final, clique em **instalar**.


## <a name="configure-the-vm-and-mongodb"></a>Configurar a máquina virtual e MongoDB

1. As variáveis de caminho não são atualizadas pelo instalador MongoDB. Sem o MongoDB `bin` local na sua variável de caminho, você precisará especificar o caminho completo cada vez que você usar um executável MongoDB. Para adicionar o local para a variável path:

    - Clique com botão direito no menu **Iniciar** e, em seguida, selecione **sistema**.
    - Clique em **Configurações avançadas do sistema**e clique em **Variáveis de ambiente**.
    - Em **variáveis do sistema**, selecione o **caminho**e clique em **Editar**.

    ![Configurar variáveis de caminho](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Adicionar o caminho para seu MongoDB `bin` pasta. MongoDB normalmente é instalado em `C:\Program Files\MongoDB`. Verifique se o caminho de instalação em sua máquina virtual. O exemplo a seguir adiciona o padrão MongoDB instalar local para o `PATH` variável:

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Certifique-se de adicionar o líder ponto e vírgula (`;`) para indicar que você está adicionando um local para seus `PATH` variável.

2. Crie diretórios de dados e log do MongoDB em seu disco de dados. No menu **Iniciar** , selecione **Prompt de comando**. Os exemplos a seguir criam os diretórios na unidade de disco f:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Iniciar uma instância de MongoDB com o seguinte comando, ajuste o caminho para seus dados e diretórios de log adequadamente:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Ele pode levar alguns minutos para MongoDB alocar os arquivos de diário e começar a ouvir para conexões. Todas as mensagens de log estão sendo direcionadas para o arquivo de *F:\MongoLogs\mongolog.log* como `mongod.exe` server inicia e aloca arquivos de diário.

    > [AZURE.NOTE] Prompt de comando permanecerá com foco nessa tarefa enquanto sua instância MongoDB está em execução. Deixe a janela do prompt de comando aberto para continuar executando MongoDB. Ou, instale MongoDB como serviço, conforme detalhado na próxima etapa.

4. Para uma experiência MongoDB mais robusta, instale o `mongod.exe` como um serviço. Criando um serviço significa que você não precisa deixar um prompt de comando executando cada vez que você deseja usar MongoDB. Crie o serviço da seguinte maneira, ajustando o caminho para os diretórios de dados e log adequadamente:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    O comando anterior cria um serviço chamado MongoDB, com uma descrição de "Mongo DB". Os seguintes parâmetros também são especificados:

    - O `--dbpath` opção especifica o local do diretório de dados.
    - O `--logpath` opção deve ser usada para especificar um arquivo de log, porque a execução do serviço não tem uma janela de comando para exibir a saída.
    - O `--logappend` opção especifica que uma reinicialização do serviço faz com que a saída acrescentar ao arquivo de log existente.

  Para iniciar o serviço de MongoDB, execute o seguinte comando:

    ```
    net start MongoDB
    ```

    Para obter mais informações sobre como criar o serviço MongoDB, consulte [Configurar um serviço do Windows para MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testar a instância MongoDB

Com MongoDB executando como uma única instância ou instalado como um serviço, agora você pode começar criando e usando seus bancos de dados. Para iniciar o shell administrativo MongoDB, abrir outra janela do prompt de comando do menu **Iniciar** e digite o seguinte comando:

```
mongo  
```

Você pode listar os bancos de dados com a `db` comando. Inserir alguns dados da seguinte maneira:

```
db.foo.insert( { a : 1 } )
```

Pesquise dados da seguinte maneira:

```
db.foo.find()
```

A saída é semelhante ao seguinte exemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Sair do `mongo` console da seguinte maneira:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar regras de grupo de segurança de rede e firewall
Agora que MongoDB está instalado e em execução, abra uma porta no Firewall do Windows para que você possa se conectar remotamente ao MongoDB. Para criar uma nova regra de entrada para permitir que a porta TCP 27017, abra um prompt de PowerShell administrativo e digite o seguinte comando:

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

Você também pode criar a regra usando a ferramenta de gerenciamento gráfico do **Firewall do Windows com segurança avançada** . Crie uma nova regra de entrada para permitir que a porta TCP 27017.

Se necessário, crie uma regra de grupo de segurança de rede para permitir acesso ao MongoDB de fora da sub-rede Azure rede virtual existente. Você pode criar as regras de grupo de segurança de rede usando o [portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md) ou [Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md). Como com as regras de Firewall do Windows, permitir que a porta TCP 27017 para a interface de rede virtual da sua VM MongoDB.

> [AZURE.NOTE] Porta TCP 27017 é a porta padrão usada pelo MongoDB. Você pode alterar essa porta usando o `--port` parâmetro ao iniciar `mongod.exe` manualmente ou de um serviço. Se você alterar a porta, verifique se a atualizar as regras de Firewall do Windows e o grupo de segurança de rede nas etapas anteriores.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como instalar e configurar o MongoDB em sua máquina virtual Windows. Agora você pode acessar MongoDB em sua máquina de virtual do Windows, seguindo os tópicos avançados na [documentação do MongoDB](https://docs.mongodb.com/manual/).
