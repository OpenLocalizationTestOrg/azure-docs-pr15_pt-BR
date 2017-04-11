<properties
   pageTitle="Implantar sua oferta para o Azure Marketplace | Microsoft Azure"
   description="Saiba mais sobre e percorrer as instruções para implantar sua oferta – imagem da máquina virtual, serviço de desenvolvedor, serviço de dados, etc. – do Azure Marketplace."
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
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Implantar sua oferta para o Azure Marketplace
Quando estiver satisfeito com sua oferta (ou seja, que tenha testado cenários de cliente, marketing conteúdo, etc.) e você está pronto para iniciar, solicitar o **envio por Push para produção** na guia **Publicar** .  

1. As quatro etapas em explicação página na publicação portal deve ser concluído e verde. Para ofertas de máquina Virtual, certifique-se de que as seguintes diretrizes sejam seguidas.

    ![desenho][img-pubportal-walkthru-checked]

2. Selecione a guia **Publicar** na lista no lado esquerdo.

    ![desenho][img-pubportal-menu-publish]

3. Clique no botão **Solicitar aprovação por push para produção**. Depois que a solicitação é feita, a equipe de aprovação executa uma revisão final e, em seguida, sua oferta estarão disponível do Azure Marketplace.

    ![desenho][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] No caso de máquinas virtuais, quando você clica no botão aprovação de solicitação por push para produção, as seguintes etapas são executadas por trás da cena. Você poderá ver o andamento de cada etapa na guia Publicar na publicação portal. Você precisa verificar esta página em intervalo regular (até que o status mostra "Listado") qualquer informação de falha que precisam ser corrigidos do seu final.

> - A primeira sua solicitação de produção vai para a equipe de certificação que validar o vhd. No entanto, se você estiver atualizando sua oferta já listada e a solicitação tem somente a alteração de marketing, a etapa de certificação será ignorada.
> - Na próxima etapa, a solicitação vêm para a equipe de validação de conteúdo que verificar o conteúdo de marketing da oferta.
> - Se as etapas acima forem bem-sucedidas, a oferta é aprovada em produção. No momento, o status ficam "listado" no portal de publicação. No entanto, esse status "Listado" não significa que o processo for concluído. As etapas a seguir precisam ser concluída antes que a oferta está disponível em do Azure Marketplace.
> - Depois que a oferta é aprovada em produção na etapa acima, comece a replicação da oferta em todo os Azure dos data centers. Geralmente ele leva 24-48hours para a conclusão da replicação, mas pode levar uma semana dependendo do tamanho do vhd. No entanto, se você estiver atualizando sua oferta já listada e tem somente a alteração de marketing, a replicação é mais rápida.
> - Quando a replicação for concluída, então a oferta estarão disponível do Azure Marketplace.

> Você sempre pode excluir a oferta enquanto ele está em um status de **rascunho** (ou seja, nunca **Push para preparação** ou **Push para produção**). Na guia **histórico** , clique no botão **Descartar rascunho** na parte inferior da página para excluir um rascunho.


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista de verificação de produção para todas as ofertas de máquina Virtual

- Certifique-se de que você é um parceiro certificado do Microsoft Azure
- Na guia SKUs, a opção "Ocultar este SKU do Marketplace porque ele sempre deve ser comprado por meio de um modelo de solução" deve ser marcada como Sim somente se a SKU faz parte de um modelo de solução. Em todos os outros casos, esta opção sempre deve ser marcada como não.
- Lembre-se: Você não deve alterar a configuração de visibilidade SKU depois que o SKU estiver listado. Não oferecemos suporte essa funcionalidade.
- Certifique-se de que os logotipos sigam as diretrizes de logotipo do Azure Marketplace fornecidas abaixo.
- Descrição da oferta e SKU não deve ser igual.
- Do SKU título e oferecer longo resumo não devem ser igual.
- Título de SKU e oferecer resumo não devem ser iguais.
- Títulos de SKU não devem ser idênticos para uma oferta com várias SKUs.

**Diretrizes de logotipo do Azure Marketplace**

- O design do Azure tem uma paleta de cores simples. Manter baixo o número principal e secundário cores em seu logotipo.
- As cores do tema do portal do Azure estão brancas e pretos. Portanto, evite usar essas cores como a cor de plano de fundo do seu logotipos. Use cor que faria seu logotipos perceptíveis no portal do Azure. Recomendamos simples cores primárias. Se você estiver usando o plano de fundo transparente, então certifique-se de que o logotipo/texto não é branco ou preto.
- Não use um plano de fundo gradiente no logotipo.
- Evite colocar texto, até mesmo sua empresa ou o nome de marca, no logotipo.
- A aparência de seu logotipo deve ser 'simples' e evite gradientes.
- O logotipo não deve ser alongado.

**Diretrizes adicionais para o logotipo do banner:**

- O logotipo do banner é opcional. O publisher pode optar por não carregar um logotipo do banner. **No entanto uma vez carregado o ícone herói não pode ser excluído da publicação portal. Nesse momento, o parceiro deve seguir as diretrizes do Azure Marketplace para ícones de banner mais que a oferta não será aprovada para produção.**
- O nome de exibição do Publisher, título SKU e a oferta longo resumo são exibidos em cor de fonte branca. Portanto, você deve evitar guardar qualquer cor clara de plano de fundo do ícone do banner. Preto, plano de fundo branco e transparente não é permitido para ícones do banner.
- O publisher exibir nome, título SKU, a oferta longo resumo e no botão Criar são incorporados programaticamente dentro do logotipo do banner após a oferta vai listada. Então você não deve inserir qualquer texto enquanto estiver criando o logotipo do banner. Basta deixar um espaço vazio à direita, porque o texto (ou seja, nome para exibição do Publisher, título SKU, a oferta longo resumo) serão incluídas programaticamente por conosco ali. O espaço vazio para o texto deve ser 415 x 100 à direita (e ela é deslocada por 370px à esquerda).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Lista de verificação de produção adicionais para já listados Máquina Virtual oferece

- Verifique se já existe uma oferta com o mesmo nome de oferta de sua empresa. Se escolher Sim, você deve adicionar uma nova versão da SKU na oferta de existente em vez de criar uma nova oferta duplicada.
- Disco de dados não deve alterar entre duas versões da mesma SKU.
- Do Azure Marketplace não dá suporte a alteração de preços das SKUS listadas, pois afeta a cobrança dos clientes existentes. Certifique-se de que você não altere o preço das SKUs listadas nas regiões onde o SKU está disponível. No entanto, você pode adicionar novos SKUs ou adicionar novas regiões a um SKU existente.


## <a name="next-steps"></a>Próximas etapas
Após a oferta fica ativa, teste os cenários de cliente para validar que todos os contratos e funcionalidades funcionem corretamente no ambiente de produção como testadas e validadas no ambiente de teste.

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta do Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
