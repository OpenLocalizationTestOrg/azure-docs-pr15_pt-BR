<properties
   pageTitle="Gerenciar serviços de análise Azure | Microsoft Azure"
   description="Saiba como gerenciar um servidor do Analysis Services no Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="manage-analysis-services"></a>Gerenciar serviços de análise

Depois que você tiver criado um servidor do Analysis Services no Azure, pode haver algumas tarefas de administração e gerenciamento que você precisa executar imediatamente ou um dia no futuro. Por exemplo, executar processamento para os dados de atualização, controlar quem pode acessar os modelos no seu servidor ou monitorar a saúde do seu servidor. Algumas tarefas de gerenciamento só podem ser executadas no portal Azure, outras pessoas no SQL Server Management Studio (SSMS), e algumas tarefas podem ser executadas em ambos.

## <a name="azure-portal"></a>Portal do Azure
O [portal do Azure](http://portal.azure.com/) é onde você pode criar e excluir servidores, monitorar os recursos de servidor, alterar o tamanho e gerencie quem tem acesso aos servidores.  Se você estiver enfrentando alguns problemas, você também pode enviar uma solicitação de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Conectar-se ao seu servidor do Azure é exatamente como se conectar a uma instância do servidor na sua organização. Do SSMS, você pode realizar muitas das mesmas tarefas como dados de processo ou criar um script de processamento, gerenciar funções e usar o PowerShell.

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Uma das diferenças maiores é a autenticação que você usa para se conectar ao seu servidor. Para se conectar ao seu servidor do Azure Analysis Services, você precisa selecionar **A autenticação de senha do Active Directory**.

### <a name="to-connect-with-ssms"></a>Para se conectar com SSMS
1. Antes de conectar, você precisa obter o nome do servidor. No **portal Azure** > servidor > **Visão geral** > **o nome do servidor**, copie o nome do servidor.

    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

2. No SSMS > **Object Explorer**, clique em **Conectar** > **Analysis Services**.

3. Na caixa de diálogo **conectar ao servidor** , cole no nome do servidor, em seguida, na **autenticação**, escolha um destes procedimentos:

    **Autenticação integrada do active Directory** usar logon único com o Active Directory para a federação do Active Directory do Azure.

    **Autenticação de senha do active Directory** para usar uma conta organizacional. Por exemplo, quando conectando a partir de um domínio não-ingressou computador.

    Observação: Se você não vir a autenticação do Active Directory, você talvez seja necessário [Habilitar a autenticação do Active Directory do Azure](#enable-azure-active-directory-authentication) no SSMS.

    ![Conectar-se no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Como gerenciar seu servidor do Azure usando SSMS é muito semelhante a de gerenciamento de um servidor local, não vamos entrar em detalhes aqui. Toda a Ajuda que você precisa podem ser encontradas no [Gerenciamento de instâncias do Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) no MSDN.

## <a name="server-administrators"></a>Administradores de servidor
Você pode usar **Os administradores de serviços de análise** na lâmina controle para seu servidor do Azure portal ou SSMS para gerenciar administradores de servidor. Administradores de serviços de análise são administradores de servidor de banco de dados com direitos para tarefas comuns de administração do banco de dados, como adicionar e remover bancos de dados e gerenciamento de usuários. Por padrão, o usuário que cria o servidor no portal Azure é automaticamente adicionado como administrador Analysis Services.

Você também deve saber:

-   Windows Live ID não é um tipo de identidade que tem suporte para serviços de análise do Azure.  
-   Os administradores de serviços de análise deve ser usuários válidos do Active Directory do Azure.
-   Se criando um servidor do Azure Analysis Services por meio de modelos do Gerenciador de recursos do Azure, os administradores de serviços de análise leva uma matriz JSON dos usuários que deve ser adicionado como administradores.

Os administradores de serviços de análise podem ser diferentes dos administradores de recurso Azure, que podem gerenciar recursos para assinaturas do Azure. Esse procedimento mantém compatibilidade com existente XMLA e TSML gerenciar comportamentos no Analysis Services e permitir que você separar deveres entre o gerenciamento de recursos do Azure e análise de serviços de gerenciamento de banco de dados.

Para exibir todas as funções e acessar tipos para o recurso de serviços de análise do Azure, use o controle de acesso (IAM) na lâmina controle.

## <a name="database-users"></a>Usuários do banco de dados
Usuários de banco de dados do Azure Analysis Services modelo devem estar no seu Azure Active Directory. Nomes de usuário especificado para o banco de dados de modelo devem ser pelo endereço de email organizacional ou UPN. Isso é diferente de bancos de dados de modelo de locais que oferece suporte a usuários por nomes de domínio do Windows.

Você pode adicionar usuários usando [atribuições de função do Azure Active Directory](../active-directory/role-based-access-control-configure.md) ou usando a [Linguagem de script de modelo Tabular](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) no SQL Server Management Studio.

**Exemplo de script TMSL**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Habilitar a autenticação do Active Directory do Azure
Para habilitar o recurso de autenticação do Active Directory do Azure para SSMS no registro, crie um arquivo de texto chamado EnableAAD.reg, e em seguida, copie e cole o seguinte:


```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Salve e execute o arquivo.



## <a name="next-steps"></a>Próximas etapas
Se você ainda não já implantou um modelo tabular em seu novo servidor, agora é uma boa ocasião. Para saber mais, consulte [implantar ao Azure Analysis Services](analysis-services-deploy.md).

Se você já implantou um modelo ao seu servidor, você está pronto para se conectar ao usando um cliente ou um navegador. Para saber mais, consulte [obter dados do servidor do Azure Analysis Services](analysis-services-connect.md).
