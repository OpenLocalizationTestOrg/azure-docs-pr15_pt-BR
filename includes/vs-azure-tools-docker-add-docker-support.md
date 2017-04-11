1. No Visual Studio **Solution Explorer**, clique com botão direito do projeto e selecione **Adicionar > suporte Docker** no menu de contexto.

    ![Adicionar um menu de contexto de suporte de Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Projeto web adicionando Docker suporte a uma Core ASP.NET resulta na adição de vários arquivos relacionados Docker sendo adicionado ao projeto, incluindo arquivos compor Docker, scripts do Windows PowerShell de implantação e arquivos de propriedade de Docker. 

    ![Arquivos de docker adicionados ao projeto](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Se usando o [Docker para Windows Beta](https://beta.docker.com), abra Properties\Docker.props, remova o valor padrão e reinicie o Visual Studio para o valor entrem em vigor.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
