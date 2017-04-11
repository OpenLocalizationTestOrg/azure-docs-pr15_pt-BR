<properties
   pageTitle="Usando o ouvinte HTTP e o conector nos aplicativos de lógica | Serviço de aplicativo do Microsoft Azure "
   description="Como criar e configurar o ouvinte HTTP e o aplicativo de conector ou API de ação de HTTP e usá-lo em um aplicativo de lógica no serviço de aplicativo do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Comece a usar o ouvinte HTTP e a ação de HTTP e adicioná-lo ao seu aplicativo de lógica

> [AZURE.NOTE]Nós estão terminando suporte para este conector porque sua funcionalidade é agora incluída por padrão como o **disparador manual** quando você cria novos aplicativos de lógica.  Recomendamos que você atualize todos os seus aplicativos de lógica que estão usando esse conector.  
> Esta versão do artigo se aplica à versão do esquema de 2014-12-01-visualização de aplicativos de lógica.

Conectar-se diretamente aos recursos HTTP para ouvir solicitações HTTP e configurar solicitações de web HTTP. Existem alguns cenários em que talvez você precise trabalhar com conexões diretas de HTTP, incluindo:

1.  Desenvolver uma lógica de aplicativo que ofereça suporte a uma web ou usuário móvel interativo front-end.
2.  Para obter e processar dados de um serviço web que não tem uma conector de caixa de ausência.
3.  Para executar ações que já estão expostos como um serviço web, mas não está disponível como um aplicativo de API.

Para esses cenários, há duas opções:

1. **Ouvinte HTTP**: esse conector atua como um disparador e ouve para solicitações HTTP em um ponto de extremidade configurado. Quando uma chamada é recebida no ponto de extremidade configurado, ele dispara uma nova instância do fluxo de e passa os dados recebidos na solicitação ao fluxo de processamento. Ele também pode ser configurado para responder automaticamente a solicitação de entrada quando o fluxo tenha iniciado ou permitem construir uma resposta com base na execução fluxo e enviar uma resposta para o chamador.
2. **Ação de HTTP**: Isso permite que você configure uma solicitação da web para qualquer ponto de extremidade disponível na internet, obtém uma resposta de volta e disponibiliza para ações adicionais no fluxo de consumir.

Aplicativos de lógica podem disparar com base em uma variedade de fontes de dados e conectores de oferta para obter e processar dados como parte do fluxo. Você pode adicionar o conector HTTP aos seus dados de fluxo de trabalho e o processo de negócios como parte deste fluxo de trabalho dentro de um aplicativo de lógica. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Criando um ouvinte HTTP para seu aplicativo de lógica
Um conector pode ser criado em um aplicativo de lógica ou ser criado diretamente a partir do Azure Marketplace. Para criar um conector do Marketplace:  

1. No startboard Azure, selecione **Marketplace**.
2. Procurar por "HTTP", selecione o ouvinte HTTP e selecione **criar**.
3.  Configure o ouvinte HTTP da seguinte maneira:  
![][1]

4.  Ao configurar as configurações de pacote, você verá a seguinte opção no se o ouvinte deve responder automaticamente ou exigir para enviar uma resposta explícita. Defina isso como **False** para enviar sua própria resposta:  
![][2]

5.  Clique em **Okey** criar.
6.  Depois que a instância de aplicativo API é criada, abra as configurações para configurar a segurança. O ouvinte HTTP atualmente dá suporte à autenticação básica. Você pode configurar isso usando a opção de segurança quando você abre o ouvinte HTTP:  
![][3]
  
    **Problema conhecido** Configurações de *a segurança mostram "Nenhum" como o valor padrão, mas isso é indefinido. Você deve alterar a configuração para Basic e voltar para nenhum antes de salvá-lo para garantir que o ouvinte HTTP está configurado corretamente.*  

7. Por fim, defina as configurações de segurança do API App ao público (anônimo) para permitir que os clientes externos acessem o ponto de extremidade. Esta configuração está disponível em "todas as configurações > configurações do aplicativo" do aplicativo HTTP ouvinte API:![][10]

Depois disso, agora você pode criar um aplicativo de lógica para usar o ouvinte HTTP.

## <a name="using-the-http-listener-in-your-logic-app"></a>Usando o ouvinte HTTP em seu aplicativo de lógica
Depois que seu aplicativo de API é criado, você agora pode usar o ouvinte HTTP como um disparador para o aplicativo de lógica. Para fazer isso, você precisa:

4.  Crie um novo aplicativo de lógica.
5.  Abra "Disparadores e ações" para abrir o Designer de aplicativos lógica e configurar seu fluxo. O ouvinte HTTP está listado na Galeria. Selecione-o.
6.  Agora você pode definir o método de HTTP e a URL relativa no qual você exige que o ouvinte disparar o fluxo:  
![][4]  
![][5]

7.  Para obter o URI completo, clique duas vezes o ouvinte HTTP para exibir suas configurações e copie a URL para o "Host" do aplicativo API:  
![][6]
8.  Agora você pode usar os dados recebidos na solicitação HTTP em outras ações no fluxo da seguinte maneira:  
![][7]  
![][8]
9.  Por fim, para enviar uma resposta, adicione outro ouvinte HTTP e selecione a ação de enviar resposta de HTTP. Defina a identificação de solicitação para a identificação da solicitação obtida o ouvinte HTTP e preencher o corpo de resposta e o status HTTP que você deseja retornar novamente:  
![][9]

## <a name="using-the-http-action"></a>Usando a ação de HTTP
A ação de HTTP nativamente é suportada por aplicativos de lógica e não exige o aplicativo API a ser criado primeiro para poder usá-lo. Você pode inserir uma ação de HTTP em qualquer ponto em seu aplicativo de lógica e escolha o URI, cabeçalhos e corpo de chamada.
A ação de HTTP oferece suporte a várias opções para segurança do lado cliente. Consulte as [Opções de segurança do lado cliente](../scheduler/scheduler-outbound-authentication.md).

A saída da ação HTTP é cabeçalhos e o corpo, que pode ser usado mais downstream no fluxo de semelhante a como saída de outras ações e conectores é consumida.

## <a name="do-more-with-your-connector"></a>Faça mais com o conector
Agora que o conector for criado, você pode adicioná-lo para um fluxo de trabalho de negócios usando um aplicativo de lógica. Consulte [quais são os aplicativos de lógica?](app-service-logic-what-are-logic-apps.md).

Exiba a referência de API de REST Swagger a [referência de aplicativos de API e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Você também pode analisar segurança de controle e as estatísticas de desempenho ao conector. Consulte [Gerenciar e monitorar seus aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Se você quiser começar a usar aplicativos do Azure lógica antes de se inscrever para uma conta do Azure, vá para [Experimentar o aplicativo de lógica](https://tryappservice.azure.com/?appservice=logic). Imediatamente, você pode criar um aplicativo de lógica de curta duração starter no aplicativo de serviço. Não há cartões de crédito obrigatório; Não há compromissos.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
