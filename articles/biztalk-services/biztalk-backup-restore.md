<properties 
    pageTitle="Criar e restaurar um backup nos serviços do BizTalk | Microsoft Azure" 
    description="Serviços BizTalk inclui Backup e restauração. Saiba como criar e restaurar um backup e determinar o que foi feito backup. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-backup-and-restore"></a>BizTalk serviços: Backup e restauração

Os serviços do Azure BizTalk inclui recursos de Backup e restauração. Este tópico descreve como fazer backup e restaurar serviços BizTalk usando o portal de clássico Azure.

Você também pode fazer backup Serviços BizTalk usando a [API do BizTalk serviços REST](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Híbrido conexões sem backup, independentemente da edição. Você deve recriar suas conexões híbrido.

## <a name="before-you-begin"></a>Antes de começar

- Backup e restauração podem não estar disponível para todas as edições. Consulte [Serviços BizTalk: gráfico das edições](biztalk-editions-feature-chart.md).

- Usando o portal de clássico Azure, você pode criar um backup sob demanda ou criar um backup agendado. 

- Conteúdo de backup pode ser restaurado para o mesmo BizTalk Service ou para um novo BizTalk Service. Para restaurar o BizTalk Service usando o mesmo nome, o BizTalk Service existente deve ser excluída e o nome deve estar disponível. Depois de excluir um BizTalk Service, pode levar mais tempo do que desejar para o mesmo nome esteja disponível. Se você não consegue esperar para o mesmo nome esteja disponível, em seguida, restaure um novo BizTalk Service.

- Serviços do BizTalk podem ser restaurados para a mesma edição ou uma edição superior. Restaurando Serviços BizTalk para uma edição inferior, de quando o backup foi feito, não é suportada.

    Por exemplo, um backup usando a edição básica pode ser restaurado a edição Premium. Não é possível restaurar um backup usando a edição Premium Standard Edition.

- Os números de controle de EDI são copiados para manter a continuidade dos números de controle. Se as mensagens são processadas depois do último backup, restaurar esse conteúdo backup pode causar os números de controle duplicados.

- Se um lote tiver mensagens ativas, processe o lote **antes de** executar um backup. Ao criar um backup (como necessários ou agendadas), mensagens em lotes nunca são armazenadas. 

    **Se um backup é feito com mensagens ativas em um lote, essas mensagens não são copiadas e, portanto, são perdidas.**

- Opcional: No Portal de serviços do BizTalk, pare qualquer operações de gerenciamento.


## <a name="create-a-backup"></a>Criar um backup

Um backup possa ser executado a qualquer momento e é completamente controlado por você. Esta seção lista as etapas para criar backups usando o portal de clássico do Azure, incluindo:

[Em backup demanda](#backupnow)

[Agendar um backup](#backupschedule)

#### <a name="backupnow"></a>Em backup demanda
1. No portal do Azure clássico, selecionar **Serviços BizTalk**e selecione o BizTalk Service que você deseja fazer backup.
2. Na guia do **painel** , selecione o **backup** na parte inferior da página.
3. Insira um nome de backup. Por exemplo, insira *myBizTalkService*BU*Data*.
4. Escolha uma conta de armazenamento de blob e selecione a marca de seleção para iniciar o backup.

Após o backup for concluído, um contêiner com o nome de backup é criado na conta de armazenamento. Este contêiner contém a configuração de backup BizTalk Service.

#### <a name="backupschedule"></a>Agendar um backup

1. No portal do Azure clássico, selecionar **Serviços BizTalk**, selecione o nome de BizTalk Service que você deseja agendar o backup e selecione a guia **Configurar** .
2. Defina o **Status do Backup** como **automático**. 
3. Selecione a **Conta de armazenamento** para armazenar o backup, insira a **frequência** para criar os backups e por quanto tempo manter os backups (**Dias de retenção**):

    ![][AutomaticBU]

    **Anotações**   
    - Em **Dias de retenção**, o período de retenção deve ser maior do que a frequência de backup.
    - Selecione **sempre manter pelo menos um backup**, mesmo se ele tiver passado o período de retenção.
    

4. Selecione **Salvar**.


Quando um trabalho de backup agendado é executado, ele cria um contêiner (para armazenar dados de backup) na conta de armazenamento que você inseriu. O nome do contêiner é chamado de *serviço BizTalk nome-data-hora*. 

Se o painel de BizTalk Service mostra um status de **Falha** :

![Status do último agendado backup][BackupStatus] 

O link abre os Logs de operações de serviços de gerenciamento para ajudar a solucionar problemas. Consulte [Serviços BizTalk: solucionar problemas usando os logs de operação](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restaurar

Você pode restaurar backups do portal do clássico Azure ou de [Restaurar API REST de serviço de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582). Esta seção apresenta as etapas para restaurar usando o portal de clássico.

#### <a name="before-restoring-a-backup"></a>Antes de restaurar um backup

- Novo controle, arquivamento e monitoramento lojas podem ser inseridos ao restaurar um BizTalk Service.

- Os mesmos dados de tempo de execução de EDI são restaurados. O backup de tempo de execução de EDI armazena os números de controle. Os números de controle restaurados são em sequência do momento do backup. Se as mensagens são processadas depois do último backup, restaurar esse conteúdo backup pode causar os números de controle duplicados.

#### <a name="restore-a-backup"></a>Restaurar um backup

1. No portal do Azure clássico, selecione **novo** > **Os serviços de aplicativo** > **Serviço BizTalk** > **Restaurar**:

    ![Restaurar um backup][Restore]

2. Na **URL de Backup**, selecione o ícone de pasta e expanda a conta de armazenamento do Azure que armazena o backup de configuração do BizTalk Service. Expanda o contêiner e no painel direito, selecione o arquivo. txt de backup correspondente. 
<br/><br/>
Selecione **Abrir**.

3. Na página **Serviço de BizTalk restauração** , insira um **Nome de serviço BizTalk** e verifique se a **URL de domínio**, **edição**e **região** para o BizTalk Service restaurado. **Criar uma nova instância de banco de dados SQL** do banco de dados de rastreamento:

    ![][RestoreBizTalkService]

    Selecione a seta próxima.

4.  Verifique se o nome do banco de dados SQL, insira o servidor físico onde o banco de dados do SQL será criado e uma nome de usuário/senha para o servidor.


    Se você deseja configurar a edição de banco de dados do SQL, tamanho e outras propriedades, selecione **Configurar definições avançadas do banco de dados**. 

    Selecione a seta próxima.

5. Criar uma nova conta de armazenamento ou insira uma conta de armazenamento existente para o BizTalk Service.

7. Selecione a marca de seleção para iniciar a restauração.

Quando a restauração for concluída com êxito, um novo BizTalk Service está listado em um estado suspenso na página Serviços BizTalk no portal de clássico do Azure.



### <a name="postrestore"></a>Após a restauração de um backup

O BizTalk Service sempre é restaurado em um estado **suspenso** . Nesse estado, você pode fazer alterações de configuração antes do novo ambiente é funcional, incluindo:

- Se você criou aplicativos de BizTalk Service usando o SDK dos serviços do Azure BizTalk, convém atualizar as credenciais de controle de acesso (ACS) nesses aplicativos para funcionar com o ambiente restaurado.

- Restaurar um BizTalk Service para replicar um ambiente existente do BizTalk Service. Nessa situação, se houver contratos configurados no portal de serviços do BizTalk original que usam uma pasta FTP de origem, você talvez precise atualize os contratos no ambiente recentemente restaurado para usar uma pasta FTP de origem diferentes. Caso contrário, pode haver dois acordos diferentes tentando retirar a mesma mensagem.

- Se você tiver restaurado para ter vários ambientes de BizTalk Service, verifique se que direcionar o ambiente correto os aplicativos do Visual Studio, cmdlets do PowerShell, APIs REST ou negociação APIs OM de gerenciamento de parceiro.

- É recomendável configurar backups automáticos no ambiente de BizTalk Service recentemente restaurado.

Para iniciar o BizTalk Service no portal de clássico do Azure, selecione o BizTalk Service restaurados e selecione **currículo** na barra de tarefas. 



## <a name="what-gets-backed-up"></a>O que foi feito backup

Quando um backup é criado, os itens a seguir são copiados:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Backup de itens</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de serviços do BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Configuração e o tempo de execução</td> 
<td>
<ul>
<li>Detalhes do parceiro e no perfil</li>
<li>Contratos de parceiros</li>
<li>Módulos personalizados implantados</li>
<li>Pontes implantado</li>
<li>Certificados</li>
<li>Transformações implantadas</li>
<li>Canais</li>
<li>Modelos criada e salva no Portal de serviços do BizTalk</li>
<li>X12 mapeamentos ST01 e GS01</li>
<li>Números de controle (EDI)</li>
<li>Valores de AS2 mensagem Microfone</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Serviço de BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Dados de certificado SSL</li>
<li>Senha do certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Configurações de serviço BizTalk</td> 
<td>
<ul>
<li>Contagem de unidade de escala</li>
<li>Edition</li>
<li>Versão do produto.</li>
<li>Região/Data Center</li>
<li>Chave e espaços de trabalho do access controle ACS (serviço)</li>
<li>Cadeia de conexão de banco de dados de acompanhamento</li>
<li>Cadeia de conexão de conta de armazenamento de arquivamento</li>
<li>Monitoramento de cadeia de conexão de conta de armazenamento</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Itens adicionais</strong></td>
</tr> 
<tr>
<td>Banco de dados de acompanhamento</td> 
<td>Quando o BizTalk Service é criado, os detalhes de controle de banco de dados são inseridos, incluindo o servidor de banco de dados do SQL Azure e o nome do banco de dados de rastreamento. O banco de dados do controle não são automaticamente backup.
<br/><br/>
<strong>Importante</strong><br/>
Se o banco de dados de rastreamento é excluído e as necessidades de banco de dados recuperados, deve existir um backup anterior. Se um backup não existir, o banco de dados de rastreamento e seus dados não são recuperáveis. Nessa situação, crie um novo controle de banco de dados com o mesmo nome de banco de dados. É recomendável replicação geográfica.</td>
</tr> 
</table>

## <a name="next"></a>Próximo

Para criar serviços do Azure BizTalk no portal de clássico do Azure, vá para [Serviços BizTalk: provisionamento usando o portal do Azure clássico](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para iniciar a criação de aplicativos, vá para [Serviços de BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
- [Serviço BizTalk backup](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar o serviço BizTalk de Backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [BizTalk serviços: Desenvolvedor, Basic, Standard e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Serviços do BizTalk: Portal de clássico do Azure usando de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Serviços do BizTalk: Gráfico de Status de provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Serviços BizTalk: Guias de painel, o Monitor e a escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Serviços do BizTalk: otimização](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Como faço para começar a usar o SDK dos serviços do BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
