
<properties
    pageTitle="O que há de novo no Azure RemoteApp? | Microsoft Azure"
    description="Saiba mais sobre alterações e aprimoramentos feitos RemoteApp do Azure"
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



# <a name="whats-new-in-azure-remoteapp"></a>O que há de novo no Azure RemoteApp?

> [AZURE.IMPORTANT]
> RemoteApp Azure está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Uma das vantagens do Azure RemoteApp é que estamos sempre trabalhando para melhorar a ele. Sempre que fazemos, podemos será anunciar essas alterações aqui.

## <a name="future-updates"></a>Atualizações futuras
Olá - você sabia que a equipe do Azure RemoteApp envia atualizações mensais no blog RDS? Você pode encontrar não apenas o que é a alteração no Azure RemoteApp, mas também outras informações sobre como usar o RDS. Confira seu blog, [Blog de serviços de área de trabalho remota](https://blogs.msdn.microsoft.com/rds/), para obter informações. Por exemplo, algumas semanas, eles lançada uma entrada sobre [remoção e deslocando cargas de trabalho com RemoteApp do Azure e Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>Setembro de 2015
- Infopath adicionado à imagem de modelo e Galeria do Microsoft Office 365. Se você quiser compartilhar Infopath, verifique se atualizar seus conjuntos com a imagem mais recente.
- Atualizações de cliente:
    - Cliente Windows atualizado para que seja possível para os usuários para compartilhar comentários, especialmente em torno de problemas de conexão.
    - cliente do iOS atualizado para corrigir o erro de mensagens e corrigir um problema onde suas credenciais expirado em versões anteriores do que o esperado.
- Estamos trabalhando para Obtendo suporte do Office 2016 testado. Quando tiver concluído, procure imagens atualizadas.
- Publicado um novo artigo sobre as [diferenças entre conjuntos de nuvem e híbridos](remoteapp-collections.md) - Isso ajudará você escolha o tipo de coleção que funciona melhor para seus aplicativos - somente na nuvem, nuvem + VNET ou híbrido.
- Deseja compartilhar QuickBooks usando Azure RemoteApp, mas não tem certeza das etapas? Confira o [artigo novo de Gustavo](remoteapp-quickbooks.md) informando exatamente o que fazer.

## <a name="august-2015"></a>Agosto de 2015
Grandes alterações aconteceram em agosto - Eis os destaques:

- Agora você pode usar um VNET Azure com uma coleção de nuvem! Confira as [instruções de criação de nuvem](remoteapp-create-cloud-deployment.md) para as novas etapas.
- Tornou possível adicionar aplicativos ao menu **Iniciar **para o cliente de RemoteApp do Windows. Aplicativos aparecerão na lista de aplicativos, e você poderá fixá-los ao menu **Iniciar **do Windows.
- Adicionar uma nova imagem na Galeria de máquina virtual do Azure - Windows Server Desktop Host da sessão remota com o Microsoft Office 365 ProPlus.
- Fixo do cliente do Mac para que aplicativos com o windows modais interromperá congelando.
- Documentadas como você pode usar sua [assinatura do Office 365 ProPlus](remoteapp-officesubscription.md) com RemoteApp do Azure.
- Detalhadas como você pode [proteger os aplicativos e dados](remoteapp-secure.md) no seu conjunto de RemoteApp do Azure.

## <a name="july-2015"></a>Julho de 2015

Julho definir o estágio alterações chegando em agosto, portanto, não há muito a falar sobre agora, principalmente atualizações do documento. Aqui estão as alterações mais recentes:

- Adicionou uma guia de **suporte** para o portal para poder acessar mais facilmente os recursos de suporte, como os fóruns.
- Reformulados as informações de solução de problemas para a criação de um conjunto de híbrido. Como o check-out [os melhores e mais recentes](remoteapp-hybridtrouble.md) para dicas de solução de problemas, como identificar as portas corretas para configurar para o seu VNET.
- Documentadas como os [dados do usuário](remoteapp-upd.md) é criada e salva no Azure RemoteApp.
- Documentadas como [bloqueio de aplicativos](remoteapp-secure.md).
- Publicado os [cmdlets do Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).
- E finalmente, podemos iniciou uma conversa com alguns usuários Azure RemoteApp sobre terminologia. Procure alterações na forma como podemos consulte as opções de outro conjunto.

## <a name="june-2015"></a>Junho de 2015

Alterações de tantas! A equipe foi muito ocupada em junho:

- Reprojetado o Azure RemoteApp [página inicial](https://www.remoteapp.windowsazure.com/) - check-out!
- Atualizado o software em todas as imagens disponíveis como parte da sua assinatura.
- Feitos aprimoramentos para coleções de híbrido, incluindo forçada túnel suporte e verificando o tamanho de sub-rede IP antes de tentar criar o conjunto.
- Descobriu que a * curinga não funciona para webcams. Em vez disso, você precisa especificar a identificação da instância ou GUID. Estamos vai ser Atualizando as informações de redirecionamento para refletir que.
- Tornou mais para poder adicionar software antivírus personalizado à sua imagem quando você cria uma imagem de modelo da Galeria Azure.

Nós temos mais alterações implantar em julho, portanto estamos estará novamente com outra atualização em breve.

## <a name="may-2015"></a>Maio de 2015

Houve um número de adições (e meses) desde podemos criado pela primeira vez neste tópico, para que esta lista impede um pouco e é desde o começo de março por meio de maio. Confira esses novos recursos:

- Automatizar tudo - Azure RemoteApp agora tem [cmdlets no módulo do PowerShell do Azure](remoteapp-tutorial-arawithpowershell.md).
- [Criar uma imagem de RemoteApp Azure de uma máquina virtual Azure](remoteapp-image-on-azurevm.md). Torna Carregando sua imagem personalizada Azure muito mais rápido.
- Use um VNET do Azure em vez de um VNET RemoteApp para conectar os recursos da sua rede corporativa no Azure. Podemos atualizou as [instruções de conjunto de híbrido](remoteapp-create-hybrid-deployment.md) para orientá-lo a criar um VNET Azure (é etapa 1).
- Falando de VNETs, confira [as novas diretrizes](remoteapp-vnetsizing.md) ao redor limitações e limites de tamanho VNET.
- E falando de limites - apenas quais são os [limites de serviço e padrões](../azure-subscription-service-limits.md)?

Deseja saber mais sobre o Azure RemoteApp? A equipe de RemoteApp estava em vigor em Ignite algumas semanas. Confira o vídeo de Gustavo, [o conceitos básicos do Microsoft Azure RemoteApp gerenciamento e administração](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

Precisa ver RemoteApp Azure no mundo real? Confira o tutorial de [executar qualquer aplicativo em qualquer dispositivo em qualquer lugar](remoteapp-anyapp.md) - -mostra como compartilhar o acesso com os usuários, inclusive compartilhar os arquivos de banco de dados. Também temos um tutorial sobre como [fazer o Office 365](remoteapp-tutorial-o365anywhere.md) executar o mesmo em qualquer dispositivo.

Agradecemos aderindo conosco - próximo mês com mais atualizações de volta.


### <a name="help-us-help-you"></a>Ajude-na ajudá-lo
Você sabia que, além de classificação neste artigo e fazer comentários para baixo abaixo, você pode fazer alterações para o artigo próprio? Algo ausente? Algo errado? Eu tenha escrito algo que é apenas confuso? Rolar para cima e clique em **Editar no GitHub** para fazer alterações - aqueles se torne para revisão e, em seguida, assim que podemos entrar neles, você verá suas alterações e melhorias aqui.
