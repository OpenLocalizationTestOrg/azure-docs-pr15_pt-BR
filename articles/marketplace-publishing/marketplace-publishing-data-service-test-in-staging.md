<properties
   pageTitle="Teste sua oferta de serviço de dados para o Marketplace | Microsoft Azure"
   description="Compreenda como testar sua oferta de serviço de dados do Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="testing-your-data-service-offer-in-staging"></a>Testar sua oferta de serviço de dados em teste

>[AZURE.IMPORTANT] **Neste momento, não são mais integração qualquer novas fornecedores de serviço de dados. Novo dataservices não irá obter aprovado para listagem.** Se você tiver um aplicativo de negócios SaaS que você gostaria de publicar no AppSource você pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se você tiver um aplicativos IaaS ou serviço de desenvolvedor que você gostaria de publicar no Azure Marketplace, você pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Depois de concluir as duas primeiras etapas de [criação de sua conta do Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) e [criar sua oferta de serviço de dados no Portal de publicação](marketplace-publishing-data-service-creation.md) , você está pronto para disponibilizar sua oferta do Azure Marketplace. Este tópico vai orientar você da primeira, intermediária, etapa denominada "Teste"

Preparação significa Implantando sua oferta em uma "área restrita" onde você pode testar e verificar sua funcionalidade antes de enviar-produção privada. A oferta serão exibidas em teste como faria para um cliente que implantou-lo.

## <a name="step-1-pushing-your-offer-to-staging"></a>Etapa 1. Insistem sua oferta de teste
Insistem sua oferta para preparação permite que você teste a oferta antes que ele fique disponível para os assinantes futuros.  Você pode ver como sua oferta aparecerão e função para aqueles assinar aos seus dados.  

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Logon para o [Portal de publicação](https://publish.windowsazure.com)
2.  Selecione **Serviços de dados** na janela de navegação à esquerda
3.  Selecione sua oferta que você deseja enviar para teste. Você verá a tela acima.
4.  Clique em botão de **Envio por Push para teste** .  
5.  Se houver problemas com a oferta que precisam ser concluídas antes de envio para teste, você verá uma lista exibida.  Corrigi esses itens clicando em cada item na lista. Quando todas as correções feitas, clique em botão de **envio por Push para preparação** novamente.

Se não existem problemas com sua oferta você verá a janela pop-up abaixo.  

Se você estiver planejando não/não aprovado para expor sua oferta no Portal do Azure (atualmente tem capacidade limitada), em seguida, basta fechar a janela pop-up.

Para testar seu serviço de dados no Portal do Azure (além do portal de DataMarket), você precisará de um ID da assinatura do Azure para teste.  Este ID da assinatura será identificar a conta que será permitida para testar sua oferta.  

Recortar e colar sua ID de assinatura e clique na marca de seleção para continuar.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Essas assinaturas Azure IDs só estão necessários para o teste e no [Portal de gerenciamento do Azure](https://manage.windowsazure.com). Eles não são necessários para testar no Azure Marketplace.

A próxima tela exibida mostra que a publicação está ocorrendo exibindo o ícone "em andamento" realçada amarelo abaixo. Estender a preparação leva entre 10 a 15 minutos.  Se ele levar mais tempo, primeiro atualize seu navegador (pressione F5 no IE).  Nos casos raros onde sua oferta ainda está fazendo para preparação depois de uma hora, clique no contato conosco vincular Fale conosco que não há um problema.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Quando o envio para preparação conclui o ícone "em andamento" serão parada movendo e o status serão atualizados para "Em estágios".  Agora você está pronto para testar sua oferta.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Etapa 2. Testar sua oferta em estágios no DataMarket

Clique no link após o texto **"Vejam seu serviço oferecem at...."** Para exibir a tela que o assinante verá quando sua oferta vai para produção e aparecerão em DataMarket.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Teste ou verifique se cada um dos 12 itens marcados acima para garantir que todos os logotipos, preços/transações, texto, imagens, documentação e links estão corretos e funcionando corretamente.  Este é um bom momento para garantir que os valores de teste que você inseriu ao criar sua oferta foram substituídos por valores reais.

1. Logotipo de oferta
2. Nome da oferta
3. Fornecedor/link do nome do site da sua empresa
4. Categorias de pesquisa para sua oferta
5. Link de suporte da sua oferta para ajudar os assinantes
6. Descrição contextual para sua oferta
7. Plano de oferta representando detalhes de cobrança
8. Link para o código de implementação
9. Usam imagens de exemplo que ilustram dos dados de oferta
10. Entrada/saída metadados para cada serviço dentro da oferta
11. Termos de uso da oferta
12. Visualização dos dados da oferta


Finalmente, verifique o serviço funcionará por meio do Datamarket clicando no link "Explorar este conjunto de dados".  Uma nova janela será aberta na ferramenta chamamos "Explorador de serviço" para que você pode visualizar os resultados de uma consulta em relação a seu serviço.  Nessa janela, você pode inserir os parâmetros necessários e ver os resultados exibidos a partir de uma consulta em relação a seu serviço.   Além disso, exibida é a URL para a sua consulta.  

> [AZURE.NOTE] Certifique-se de revisar o texto de descrição do serviço exibido na parte superior.  E se sua oferta consiste em mais de um serviço de chamadas, clique nas guias na parte inferior para alternar para o próximo serviço para revisar e testar.



## <a name="next-step"></a>Próxima etapa
Se você está enfrentando problemas e precisa de ajuda para solucioná-los entre em contato com [O suporte do Azure Publisher]( http://go.microsoft.com/fwlink/?LinkId=272975).

Se você estiver satisfeito e pronto para publicar sua oferta, leia a documentação de [Aprovação de solicitação de envio por Push para produção](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Consulte também
- [Introdução: Como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)
