
<properties 
    pageTitle="Como o Azure RemoteApp salvar configurações e dados de usuário? | Microsoft Azure"
    description="Saiba como o Azure RemoteApp salva dados de usuário usando o disco de perfil de usuário."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Como o Azure RemoteApp salvar configurações e dados de usuário?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp salva identidade de usuário e personalizações em dispositivos e sessões. Esses dados de usuário serão armazenados em um disco de por conjunto por usuário, conhecido como um disco de perfil de usuário (UPD). O disco segue o usuário e garante que o usuário tem uma experiência consistente, independentemente de onde eles entrar. salva 

Discos de perfil de usuário são totalmente transparentes ao usuário — os usuários salvar documentos em sua pasta de documentos (no que parece ser uma unidade local) e alterar suas configurações de aplicativo como de costume. Ao mesmo tempo, todas as configurações pessoais persistirem ao conectar com o Azure RemoteApp de qualquer dispositivo. Tudo o que o usuário vê é seus dados no mesmo local.

Cada UPD tem 50GB de armazenamento persistente e contém ambas as configurações de dados e aplicativos do usuário. 

Continue a ler para informações específicas sobre os dados de perfil de usuário.

>[AZURE.NOTE] Você precisa desativar a UPD? Você pode fazer que agora - check-out postagem de blog do Pavithra, [Desabilitar o usuário perfil discos (UPDs) no Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), para obter detalhes.


## <a name="how-can-an-admin-get-to-the-data"></a>Como um administrador pode obter os dados?

Se você precisar acessar os dados para um de seus usuários (para recuperação de dados ou se o usuário sair da empresa), contate o suporte do Azure e forneça as informações de assinatura para o conjunto e a identidade do usuário. A equipe do Azure RemoteApp fornecerá uma URL para o VHD. Baixe esse VHD e recuperar quaisquer documentos ou arquivos que você precisa. Observe que o VHD é 50GB, portanto vai demorar um pouco para baixá-lo.


## <a name="is-the-data-backed-up"></a>É feito backup dos dados?

Sim, vamos salvar um backup dos dados do usuário por localização geográfica. Os dados são somente leitura e podem ser acessados da mesma forma como os dados regulares seria (contato Azure RemoteApp para colocá-lo), se o Centro de dados principal for pressionada. Os dados são copiados em tempo real para o local de backup e não podemos guardar cópias de diferentes versões. Portanto, em corrupção de dados, podemos não será possível restaurá-lo para uma versão de boa anteriormente conhecida, mas se data center principal estiver desativado, você poderá obter dados do usuário do outro local.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>Como os usuários ver o UPD no lado do servidor?

Cada usuário terá seu próprio diretório no servidor que mapeia para seu UPD: c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>O que é a melhor maneira de usar o Outlook e UPD?

Azure RemoteApp salva o estado do Outlook (caixas de correio, PSTs) entre as sessões. Para permitir isso, precisamos PST para ser armazenados nos dados de perfil de usuário (c:\users\<nome de usuário >). Este é o local padrão para os dados, portanto contanto que você não altere o local, os dados serão mantidas entre as sessões.

Também é recomendável que você usa o modo de "em cache" no Outlook e use o modo "servidor/online" para pesquisa.

Consulte [Este artigo](remoteapp-outlook.md) para obter mais informações sobre como usar o Outlook e o Azure RemoteApp.

## <a name="what-about-redirection"></a>E o redirecionamento?
Você pode configurar o Azure RemoteApp para permitir que usuários acessar dispositivos locais definindo o [redirecionamento](remoteapp-redirection.md). Dispositivos locais poderão acessar os dados no UPD.

## <a name="can-i-use-my-upd-as-a-network-share"></a>Pode usar minha UPD como um compartilhamento de rede?
Não. UPDs não podem ser usados como um compartilhamento de rede. Um UPD só está disponível ao usuário quando o usuário esteja conectado ativamente ao Azure RemoteApp.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>Se eu excluir um usuário de uma coleção, seu UPD será excluído?

Não, quando você exclui um usuário, podemos não excluir automaticamente o UPD - em vez disso, podemos armazenar os dados até que você exclua o conjunto. 90 dias após excluir da coleção, podemos excluir todos os UPDs. 

Se você precisar excluir um UPD de uma coleção, entre em contato com o Azure RemoteApp - podemos excluir UPD do nosso lado.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>É possível acessar UPDs dos meus usuários (usuários atuais ou excluídos)?

Sim, se você contatar [Azure RemoteApp](mailto:remoteappforum@microsoft.com), podemos configurar você com uma URL para acessar os dados. Você terá cerca de 10 horas baixar os arquivos de dados ou do UPD antes que expire o acesso.

## <a name="are-upds-available-offline"></a>UPDs estão disponíveis offline?

Agora, podemos não fornecem acesso offline ao UPDs, além da janela de acesso de hora 10 descrita acima. Isso significa que não atualmente temos uma maneira de fornecer acesso por muito tempo suficiente para concluir tarefas mais complicadas, como a execução de software de antivírus nas UPDs ou acessar dados para uma auditoria.

## <a name="do-registry-key-settings-persist"></a>Configurações de chave do registro se mantiver?
Sim, nada gravados em HKEY_Current_User é parte do UPD.

## <a name="can-i-disable-upds-for-a-collection"></a>Pode desabilitar UPDs para um conjunto?

Sim, você pode pedir Azure RemoteApp para desabilitar UPDs para uma assinatura, mas você não pode fazer que você mesmo. Isso significa que UPDs serão desabilitados para todos os conjuntos na assinatura.

Talvez você queira desabilitar UPDs em qualquer uma das seguintes situações: 

- Você precisa concluir acesso e controle de dados do usuário (para auditoria e revisar fins como instituições financeiras).
- Você tem 3º terceiros usuário perfil gerenciamento soluções local e continuar a usá-los em sua implantação do Azure RemoteApp domínio. Isso requer o agente de perfil a ser carregado na imagem de ouro. 
- Não é necessário qualquer armazenamento de dados locais ou exibir todos os dados no compartilhamento de arquivo ou nuvem e gostaria de controle salvar dados localmente usando RemoteApp do Azure.

Consulte [Desabilitar usuário perfil discos (UPDs) no Azure RemoteApp para obter mais informações.](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>Pode restringir os usuários de salvar os dados na unidade de sistema?

Sim, mas você precisará configurar que a imagem de modelo antes de criar o conjunto. Use as seguintes etapas para bloquear o acesso a unidade do sistema:

1. Execute **gpedit. msc** na imagem do modelo.
2. Navegue até **configuração do usuário > modelos administrativos > componentes do Windows > Explorer**.
3. Selecione as seguintes opções:
    - **Oculta estas unidades especificadas em Meu computador**
    - **Impedir o acesso a unidades de Meu computador**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>Posso pode propagar UPDs? Eu quero colocar alguns dados no que está disponível na primeira vez que o usuário entra no UPD.

Sim, quando você cria a imagem de modelo, você pode adicionar informações para o perfil padrão. Essas informações são adicionadas para o UPD.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>Posso alterar o tamanho da UPD dependendo de quantos dados quero armazenar?

Não, todos os UPDs tem 50 GB de armazenamento. Se você quiser armazenar diferentes quantidades de dados, tente o seguinte:

1. Desabilite UPDs para o conjunto.
2. Configure um compartilhamento de arquivo para usuários acessem.
3. Carrega o compartilhamento de arquivos usando um script de inicialização. Veja abaixo para obter detalhes sobre os scripts de inicialização no Azure RemoteApp.
4. Direcionar os usuários para salvar todos os dados para o compartilhamento de arquivo.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Como executo um script de inicialização no Azure RemoteApp?

Se você quiser executar um script de inicialização, inicie criando uma tarefa agendada na imagem de modelo que você vai usar para o conjunto. (Faça este *antes de* você executar sysprep.) 

![Criar uma tarefa do sistema](./media/remoteapp-upd/upd1.png)

![Criar uma tarefa de sistema que é executada quando um usuário fizer logon](./media/remoteapp-upd/upd2.png)

Na guia **Geral** , certifique-se de alterar a **Conta de usuário** em segurança para "BUILTIN\Users".

![Alterar a conta de usuário a um grupo](./media/remoteapp-upd/upd4.png)

A tarefa agendada iniciará seu script de inicialização, usando as credenciais do usuário. Agendar a tarefa para executar cada uma vez que um usuário fizer logon.

![Definir o disparador para a tarefa "no logon"](./media/remoteapp-upd/upd3.png)

Você também pode usar [scripts de inicialização baseada em política de grupo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>E quanto colocando um script de inicialização no menu Iniciar? Que funcionariam?

Em outras palavras, posso criar um arquivo. bat que executa um script de janela config e salvá-lo para a pasta de Iniciar\Programas\Inicializar. c:\ProgramData\Microsoft\Windows\Start e então ter esse script executado sempre que um usuário inicia uma sessão de RemoteApp?

Não, que não é compatível com o Azure RemoteApp, que usa RDSH, que também não suporta scripts de inicialização no menu Iniciar.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>Pode usar mstsc.exe (o programa de área de trabalho remota) para configurar scripts de logon?

Não, não têm suporte no Azure RemoteApp.

## <a name="can-i-store-data-on-the-vm-locally"></a>Pode armazenar dados sobre a máquina virtual localmente?

Não, os dados armazenados em qualquer lugar na VM diferente no UPD não serão perdidos. Não há uma chance de alta o usuário não terá a mesma máquina de virtual na próxima vez que eles entrarem RemoteApp do Azure. Não podemos manter persistência de máquina virtual o usuário, para que o usuário não entrar mesma VM e os dados serão perdidos. Além disso, quando podemos atualizar o conjunto, as VMs existentes são substituídas com um novo conjunto de VMs - o que significa nenhum dado armazenado na máquina virtual em si é perdido. A recomendação é para armazenar dados em UPD, armazenamento compartilhado como arquivos do Azure, um servidor de arquivos dentro de uma VNET ou na nuvem usando um sistema de armazenamento de nuvem como o DropBox.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>Como posso montar um compartilhamento de arquivo do Azure em uma máquina virtual, usando o PowerShell?

Você pode usar o cmdlet líquido PSDrive para montar a unidade, da seguinte maneira:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


Você também pode salvar suas credenciais executando o seguinte:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Isso permite que você ignore o credencial parâmetro - o cmdlet New-PSDrive.
