<properties 
    pageTitle="Servidor de autenticação multifator de autenticação do Windows e do Azure"
    description="Esta é a página de autenticação multifator do Azure que ajudarão na implantação de autenticação do Windows e o servidor de autenticação multifator do Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticação multifator de autenticação do Windows e do Azure

A seção de autenticação do Windows permite ao administrador habilitar e configurar a autenticação do Windows para um ou mais aplicativos.  A seguir está uma lista de coisas a ter em mente antes de configurar a autenticação do Windows.

-  reinicialização é necessária antes da autenticação multifator de Azure para serviços de Terminal entrará em vigor.
-  Se 'Correspondência de usuário exigir autenticação do Azure multifator' está marcada e não estiver na lista de usuários, não será capaz de fazer logon na máquina após a reinicialização.
-  IPs confiáveis é dependente se o aplicativo pode fornecer o IP do cliente com a autenticação. No momento, somente os serviços de Terminal é suportado.  







>[AZURE.NOTE]Não há suporte para este recurso seguro dos serviços de Terminal no Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para proteger um aplicativo com autenticação do Windows, use o procedimento a seguir.

1. No servidor de autenticação multifator do Azure, clique no ícone de autenticação do Windows.
![Autenticação do Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Marque a caixa de seleção de autenticação do Windows habilitar. Por padrão, essa caixa está desmarcada.
3. Na guia aplicativos permite ao administrador configurar um ou mais aplicativos para autenticação do Windows.
4. Selecione um servidor ou aplicativo – especifique se o aplicativo do servidor/estiver habilitado. Clique em Okey.
5. Clique em Adicionar... botão.
6. Na guia IPs confiáveis permite ignorar sessões Azure autenticação multifator para Windows provenientes de IPs específicos. Por exemplo, se os funcionários usam o aplicativo do escritório e da página inicial, você pode decidir que não deseja seus telefones tocarem para autenticação multifator de Azure enquanto estiver no escritório. Para isso, especifique a sub-rede do office como entrada de IPs confiáveis.
7. Clique em Adicionar... botão.
8. Selecione IP único se você gostaria de ignorar um único endereço IP.
9. Selecione o intervalo de IP se você gostaria de ignorar um intervalo IP inteiro. 10.63.193.1-10.63.193.100 de exemplo.
10. Selecione sub-rede se você quiser especificar um intervalo de endereços IP usando a notação de sub-rede. Insira IP inicial da sub-rede e escolha a máscara de rede apropriada na lista suspensa.
11. Clique no botão Okey.
