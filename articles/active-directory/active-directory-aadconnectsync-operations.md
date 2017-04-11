<properties
   pageTitle="Sincronização do Azure AD Connect: tarefas operacionais e considerações | Microsoft Azure"
   description="Este tópico descreve tarefas operacionais para sincronização do Azure AD Connect e como se preparar para este componente de operação."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronização do Azure AD Connect: tarefas operacionais e consideração
O objetivo deste tópico é descrever tarefas operacionais para sincronização do Azure AD Connect.

## <a name="staging-mode"></a>Modo de teste
Modo de teste pode ser usado para vários cenários, incluindo:

-   Alta disponibilidade.
-   Testar e implantar novas alterações de configuração.
-   Apresente um novo servidor e encerrar o antigo.

Com um servidor no modo de teste, você pode fazer alterações na configuração e visualizar as alterações antes de fazer o servidor ativo. Ele também permite executar importação completa e sincronização completa para verificar que todas as alterações são esperadas antes de fazer essas alterações em seu ambiente de produção.

Durante a instalação, você pode selecionar o servidor para estar no **modo de teste**. Esta ação faz com que o servidor active para importação e sincronização, mas não executa qualquer exportações. Um servidor no modo de teste não está executando a sincronização de senha ou write-back de senha, mesmo se você selecionou esses recursos durante a instalação. Quando você desativar o modo de teste, o servidor inicia a exportação, permite a sincronização de senha e permite write-back de senha.

Você ainda pode forçar uma exportação usando o Gerenciador de serviço de sincronização.

Um servidor no modo de preparação continua a receber alterações do Active Directory e Azure AD. Ele sempre tem uma cópia das alterações mais recentes e podem levar muito rápido sobre as responsabilidades do outro servidor. Se você fizer alterações de configuração para seu servidor principal, ele é sua responsabilidade para fazer as mesmas alterações para o servidor no modo de teste.

Para aqueles com conhecimento das tecnologias de sincronização mais antigos, o modo de preparação é diferente, pois o servidor tem seu próprio banco de dados do SQL. Essa arquitetura permite que o servidor intermediário de modo a ser localizado em um data center diferente.

### <a name="verify-the-configuration-of-a-server"></a>Verificar a configuração de um servidor
Para aplicar esse método, siga estas etapas:

1. [Preparar](#prepare)
2. [Importar e sincronizar](#import-and-synchronize)
3. [Verifique se](#verify)
4. [Servidor ativo de mudança](#switch-active-server)

#### <a name="prepare"></a>Preparar

1. Instalar o Azure AD Connect, selecione o **modo de teste**e desmarcar **Iniciar sincronização** na última página do Assistente de instalação. Esse modo permite que você executar o mecanismo de sincronização manualmente.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Sinal desativa/entrar e do menu Iniciar, selecione **Serviço de sincronização**.

#### <a name="import-and-synchronize"></a>Importar e sincronizar

1. Selecione **conectores**e, em seguida, selecione o primeiro conector com o tipo de **Serviços de domínio Active Directory**. Clique em **Executar**, selecione **importação completa**e **Okey**. Siga estas etapas para todos os conectores desse tipo.
2. Selecione o conector com tipo **Azure Active Directory (Microsoft)**. Clique em **Executar**, selecione **importação completa**e **Okey**.
3. Verifique se que a guia conectores ainda está selecionada. Para cada conector com tipo de **Serviços de domínio Active Directory**, clique em **Executar**, selecione **Delta Synchronization**e **Okey**.
4. Selecione o conector com tipo **Azure Active Directory (Microsoft)**. Clique em **Executar**, selecione **Delta Synchronization**e **Okey**.

Agora que você tenha testado exportação é alterado para Azure AD e local AD (se você estiver usando implantação híbrida do Exchange). As próximas etapas permitem que você inspecione o que está prestes a alterar antes de realmente iniciar a exportação para os diretórios.

#### <a name="verify"></a>Verifique se

1. Inicie um prompt cmd e vá para`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Executar:`csexport "Name of Connector" %temp%\export.xml /f:x`  
O nome do conector pode ser encontrado no serviço de sincronização. Ela tem um nome semelhante a "contoso.com – AAD" do Azure AD.
3. Executar:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Você tem um arquivo em % temp % denominada export.csv que podem ser examinados no Microsoft Excel. Este arquivo contém todas as alterações que estão prestes a ser exportado.
5. Faça as alterações necessárias para os dados ou a configuração e execute estas etapas novamente (importar e sincronizar e verificar) até que as alterações que estão prestes a ser exportado são esperadas.

**Noções básicas sobre o arquivo de export.csv**

A maioria do arquivo é auto-explicativo. Alguns abreviações a entender o conteúdo:

- OMODT – tipo de modificação do objeto. Indica se a operação em um nível de objeto é um adicionar, atualizar ou excluir.
- AMODT – tipo de atributo de modificação. Indica se a operação em um nível de atributo é um adicionar, atualizar ou excluir.

Se o valor do atributo for múltiplos, não cada alteração é exibida. Somente o número de valores adicionados e removidos fica visível.

#### <a name="switch-active-server"></a>Servidor ativo de mudança

1. No servidor atualmente ativo, desative o servidor DirSync/FIM/Azure AD Sync () para que ele não está exportando ao Azure AD ou configurá-la em preparação modo (Azure AD Connect).
2. Execute o Assistente de instalação no servidor em **modo de teste** e desativar o **modo de teste**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação de dados
Parte do design implementação é planejar para o que fazer se houver um desastre onde você perde o servidor de sincronização. Há modelos diferentes para uso e qual deles usar depende de vários fatores, incluindo:

-   Qual é sua tolerância por não ser capaz de fazer alterações em objetos no Azure AD durante o tempo de inatividade?
-   Se você usar a sincronização de senha, os usuários aceitar que eles tenham que usar a senha antiga no Azure AD caso eles alterá-la no local?
-   Você tem uma dependência em operações em tempo real, como senha write-back?

Dependendo das respostas para essas perguntas e a política da sua organização, uma das seguintes estratégias pode ser implementada:

-   Recrie quando necessário.
-   Ter um servidor de espera reserva, conhecido como **modo de teste**.
-   Use máquinas virtuais.

Se você não usa o banco de dados interno do SQL Express, você também deve examinar o seção [SQL alta disponibilidade](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Recriar quando necessário
Uma estratégia viável é planejar para a recriação do servidor quando necessário. Geralmente, instalando o mecanismo de sincronização e que a importação inicial e a sincronização podem ser concluídos dentro de algumas horas. Se não houver um servidor reserva disponível, é possível usar temporariamente um controlador de domínio para hospedar o mecanismo de sincronização.

O servidor do mecanismo de sincronização não armazenar qualquer estado sobre os objetos para que o banco de dados pode ser recriado dos dados no Active Directory e Azure AD. O atributo **sourceAnchor** é usado para ingressar os objetos de locais e na nuvem. Se você recriar o servidor com existente objetos locais e na nuvem, em seguida, o mecanismo de sincronização corresponde a esses objetos juntos novamente na reinstalação. O coisas que você precisa documentar e salvar é as alterações feitas no servidor, como as regras de filtragem e sincronização. Estas configurações personalizadas devem ser aplicada novamente antes de iniciar a sincronização.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ter um servidor de espera reserva - modo de teste
Se você tiver um ambiente mais complexo, recomenda-se o ter um ou mais servidores em espera. Durante a instalação, você pode habilitar um servidor para estar no **modo de teste**.

Para obter mais detalhes, consulte [temporário modo](#staging-mode).

### <a name="use-virtual-machines"></a>Usar máquinas virtuais
Um método comum e compatível é executar o mecanismo de sincronização em uma máquina virtual. Caso o host tiver um problema, a imagem com o servidor do mecanismo de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>SQL alta disponibilidade
Se você não estiver usando o SQL Server Express que vem com o Azure AD Connect, alta disponibilidade para o SQL Server também deve ser considerada. A solução de alta somente disponibilidade suportada é SQL cluster. Soluções sem suporte incluem espelhamento e sempre no.

## <a name="next-steps"></a>Próximas etapas

**Tópicos de visão geral**  

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)  
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)  
