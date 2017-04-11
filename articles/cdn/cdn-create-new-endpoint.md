<properties
     pageTitle="Usando o Azure CDN | Microsoft Azure"
     description="Este tópico mostra como habilitar a rede de entrega de conteúdo (CDN) para o Azure. O tutorial percorre a criação de um novo perfil CDN e o ponto de extremidade."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>Usando o Azure CDN  

Este tópico descreve ativando o Azure CDN, criando um novo perfil CDN e ponto de extremidade.

>[AZURE.IMPORTANT] Para uma introdução ao CDN funcionamento, bem como uma lista de recursos, consulte [Visão geral de CDN](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN

Um perfil CDN é um conjunto de pontos de extremidade CDN.  Cada perfil contém um ou mais pontos de extremidade CDN.  Talvez você queira usar vários perfis para organizar seus pontos de extremidade CDN por domínio internet, aplicativo web ou algum outro critério.

> [AZURE.NOTE] Por padrão, uma única assinatura Azure está limitada aos perfis CDN oito. Cada perfil CDN está limitada aos pontos de extremidade de dez CDN.
>
> Preços de CDN é aplicada no nível de perfil de CDN. Se você quiser usar uma mistura de níveis de preços do Azure CDN, você precisará vários perfis CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto de extremidade CDN

**Para criar um novo ponto de extremidade CDN**

1. No [Portal do Azure](https://portal.azure.com), navegue até seu perfil CDN.  Você pode ter fixada ao painel na etapa anterior.  Se você não, você pode encontrá-lo clicando em **Procurar**, em seguida **perfis CDN**e, em seguida, clicando no perfil que você planeja adicionar seu ponto de extremidade para.

    A lâmina de perfil CDN é exibida.

    ![Perfil CDN][cdn-profile-settings]

2. Clique no botão **Adicionar ponto de extremidade** .

    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]

    A lâmina de **Adicionar um ponto de extremidade** é exibida.

    ![Adicionar blade de ponto de extremidade][cdn-add-endpoint]

3. Insira um **nome** para este ponto de extremidade CDN.  Esse nome será usado para acessar os recursos armazenados em cache no domínio `<endpointname>.azureedge.net`.

4. Na lista suspensa **tipo de origem** , selecione o tipo de origem.  Selecione o **armazenamento** para uma conta de armazenamento do Azure, **serviço de nuvem** para um serviço de nuvem do Azure, **Web App** para um Azure Web App ou **origin personalizada** para qualquer outra origem de servidor da web publicamente acessíveis (hospedada no Azure ou em outro lugar).

    ![Tipo de origem CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Na lista suspensa **nome do host de origem** , selecione ou digite o seu domínio de origem.  Na lista suspensa listará todas as origens disponíveis do tipo especificada na etapa 4.  Se você selecionou *origin personalizada* como sua **origem digite**, você digitará no domínio de sua origem personalizado.

6. Na caixa de texto **caminho de origem** , insira o caminho para os recursos que você deseja armazenar em cache ou deixe em branco para permitir que o cache qualquer recurso no domínio que você especificou na etapa 5.

7. No **cabeçalho do host de origem**, insira o cabeçalho do host que deseja que a CDN enviar com cada solicitação ou deixe o padrão.

    > [AZURE.WARNING] Alguns tipos de origens, como o armazenamento do Azure e Web Apps, requerem o cabeçalho do host para corresponder ao domínio da origem. A menos que tenha uma origem que requer um cabeçalho de host diferente do seu domínio, você deve deixar o valor padrão.

8. Para **protocolo** e **porta de origem**, especifica os protocolos e portas usadas para acessar os recursos na origem.  Pelo menos um protocolo (HTTP ou HTTPS) deve ser selecionado.
    
    > [AZURE.NOTE] A **porta de origem** afeta apenas o que porta os usos de ponto de extremidade para recuperar informações de origem.  O ponto de extremidade em si só estará disponível para clientes de fim no padrão HTTP e portas HTTPS (80 e 443), independentemente da **porta de origem**.  
    >
    > Pontos de extremidade do **Azure CDN do Akamai** não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidos, consulte [Azure CDN de portas de origem do Akamai permitidas](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > Acessando CDN conteúdo usando HTTPS tem as seguintes restrições:
    > 
    > - Você deve usar o certificado SSL fornecido pela CDN. Não há suporte para certificados de terceiros.
    > - Você deve usar o domínio fornecidos CDN (`<endpointname>.azureedge.net`) para acessar o conteúdo HTTPS. Suporte HTTPS não está disponível para nomes de domínio personalizado (CNAMEs) como a CDN não oferece suporte a certificados personalizados neste momento.

9. Clique no botão **Adicionar** para criar o novo ponto de extremidade.

10. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade para o perfil. O modo de exibição de lista mostra o URL usar para acessar o conteúdo em cache, bem como o domínio de origem.

    ![Ponto de extremidade CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] O ponto de extremidade não estará disponível imediatamente para uso, como demora para o registro a se propagar através do CDN.  Para os perfis do <b>Azure CDN do Akamai</b> , propagação geralmente concluirá dentro de um minuto.  Para perfis do <b>Azure CDN da Verizon</b> , propagação geralmente concluirá dentro de 90 minutos, mas em alguns casos pode levar mais tempo.
    >    
    > Os usuários que tentarem usar o nome de domínio CDN antes da configuração da empresa tem propagado para o aparece receberá códigos de resposta de HTTP 404.  Se ele tiver sido várias horas desde que você criou seu ponto de extremidade e ainda estiver recebendo 404 respostas, consulte [pontos de extremidade de solução de problemas CDN retornando 404 status](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Consulte também
- [Controlar o comportamento de cache de solicitações com cadeias de caracteres de consulta](cdn-query-string.md)
- [Como mapear CDN conteúdo para um domínio personalizado](cdn-map-content-to-custom-domain.md)
- [Pré-carregar ativos em um ponto de extremidade do Azure CDN](cdn-preload-endpoint.md)
- [Limpar um ponto de extremidade do Azure CDN](cdn-purge-endpoint.md)
- [Pontos de extremidade CDN retornando 404 status de solução de problemas](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
