<properties
  pageTitle="Pacote do Azure IoT perguntas Frequentes | Microsoft Azure"
  description="Perguntas frequentes para IoT pacote"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas frequentes para IoT pacote

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em Excluir em uma solução pré-configurada em azureiotsuite.com?

- Se você excluir a solução pré-configurado no [azureiotsuite.com][lnk-azureiotsuite], excluir todos os recursos que foram provisionados quando você criou a solução pré-configurado; Se você adicionou recursos adicionais ao grupo de recursos, eles também são excluídos. 

- Se você excluir o grupo de recursos no [portal do Azure][lnk-azure-portal], você excluir somente os recursos desse grupo de recursos; Você também precisará excluir o aplicativo do Azure Active Directory associado com a solução pré-configurado no [portal clássico Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias de IoT Hub podem provisionar em uma assinatura? 

Dez. Você pode criar um [tíquete de suporte do Azure] [ link-azuresupportticket] aumentar esse limite, mas por padrão, você só poderá provisionar dez Hubs IoT por assinatura, conforme descrito em [limites de assinatura Azure][link-azuresublimits]. Como resultado, desde que cada solução pré-configurada provisiona um IoT Hub novo, você só poderá provisionar até dez soluções pré-configurado em uma determinada assinatura. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias de DocumentDB podem provisionar em uma assinatura?

50. Você pode criar um [tíquete de suporte do Azure] [ link-azuresupportticket] aumentar esse limite, mas por padrão, você só poderá provisionar 50 instâncias de DocumentDB por assinatura. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantos APIs de mapas do Bing livre podem provisionar em uma assinatura?

Dois. Você pode criar apenas dois interno transações nível 1 Bing Maps para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remota está provisionada por padrão com o plano de interno transações nível 1. Como resultado, você só poderá provisionar até dois remotas soluções de monitoramento em uma assinatura sem modificações.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tenho uma implantação de solução de monitoramento remoto com um mapa estático, como faço para adicionar um mapa do Bing interativo? 
1. Obter a API do Bing Maps para Enterprise QueryKey de [portal Azure][lnk-azure-portal]: 
 1. Navegue até o grupo de recursos onde seu API do Bing Maps para Enterprise está no [portal do Azure][lnk-azure-portal].
 2. Clique em todas as configurações, gerenciamento de chaves, em seguida. 
 3. Você notará duas chaves: MasterKey e QueryKey. Copie o valor para QueryKey.

     > [AZURE.NOTE] Não tem uma API do Bing Maps para a conta da empresa? Criar uma no [portal do Azure] [ lnk-azure-portal] por clicando em + novo, pesquisando API do Bing Maps para o Enterprise e siga os avisos para criar.

2. Suspenso o código mais recente do [Azure-IoT-monitoramento remoto][lnk-remote-monitoring-github].

3. Executar um local ou seguindo a orientação de implantação de linha de comando na pasta /docs/ no repositório de implantação de nuvem. 

4. Depois de ter executado local ou nuvem implantação, procure na pasta raiz para o *. config arquivo criado durante a implantação. Abra este arquivo em um editor de texto. 

5. Altere a seguinte linha para incluir o valor que você copiou do seu QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se tiver o Microsoft Azure para DreamSpark?
No momento, você não pode criar uma solução pré-configurada com um [Microsoft Azure para DreamSpark] [ lnk-dreamspark] conta. No entanto, você pode criar uma [conta de avaliação gratuita do Azure] [ lnk-30daytrial] em apenas alguns minutos que permitirá que você criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como excluo um locatário AAD?

Consulte [explicação passo a passo de exclusão de um locatário do Azure AD]de postagem no blog de Eric Golpe[lnk-delete-aad-tennant].

## <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos e recursos das soluções Suite IoT pré-configurado:

- [Visão geral da solução previsão manutenção pré-configurado][lnk-predictive-overview]
- [Segurança IoT desde o início][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
