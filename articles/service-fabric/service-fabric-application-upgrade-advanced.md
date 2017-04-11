<properties
   pageTitle="Atualização do aplicativo: tópicos avançados | Microsoft Azure"
   description="Este artigo aborda alguns tópicos avançados relacionados à atualização de um aplicativo de serviço tecidos."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização do aplicativo de serviço tecidos: tópicos avançados

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Adicionando ou removendo serviços durante uma atualização do aplicativo

Se um novo serviço for adicionado a um aplicativo que já está implantado e publicado como uma atualização, o novo serviço é adicionado ao aplicativo implantado.  Essa atualização não afeta qualquer um dos serviços que já foram parte do aplicativo. No entanto, uma instância do serviço que foi adicionada deve ser iniciada para o novo serviço ativas (usando o `New-ServiceFabricService` cmdlet).

Serviços também podem ser removidos de um aplicativo como parte de uma atualização. Entretanto, todas as instâncias do serviço a ser excluído devem ser interrompidas antes de prosseguir com a atualização (usando o `Remove-ServiceFabricService` cmdlet). 

## <a name="manual-upgrade-mode"></a>Modo de atualização manual

> [AZURE.NOTE]  O modo manual sem monitoramento deve ser considerado somente para uma atualização falhou ou está suspensa. O modo monitorar é o modo de atualização recomendado para aplicativos de serviço tecidos.

Azure serviço tecidos fornece vários modos de atualização para oferecer suporte a clusters de desenvolvimento e produção. Opções de implantação escolhidas podem ser diferentes para diferentes ambientes.

A atualização de aplicativo sem interrupção monitorar é a atualização mais comum para usar em produção. Quando a política de atualização é especificada, serviço tecidos garante que o aplicativo é eficaz, antes que a atualização prossiga.

 O administrador do aplicativo pode usar o modo de atualização de aplicativo sem interrupção manual para tem total controle sobre o progresso da atualização por meio de vários domínios atualização. Esse modo é útil quando uma política de avaliação de integridade personalizados ou complexa é necessária ou uma atualização não convencional está acontecendo (por exemplo, o aplicativo já está em perda de dados).

Por fim, a atualização de aplicativo sem interrupção automatizada é útil para desenvolvimento ou ambientes de testes para fornecer um ciclo de iteração fast durante o desenvolvimento de serviço.

## <a name="change-to-manual-upgrade-mode"></a>Alterar para modo de atualização manual
**Manual**– pare a atualização do aplicativo na UD atual e alterar o modo de atualização manual sem monitoramento. O administrador precisa chamar manualmente **MoveNextApplicationUpgradeDomainAsync** para continuar com a atualização ou acionar uma reversão iniciando uma nova atualização. Após a atualização insere o modo Manual, ela permanecerá no modo Manual até que uma nova atualização é iniciada. O comando **GetApplicationUpgradeProgressAsync** retorna TECIDOS\_aplicativo\_atualizar\_estado\_sem interrupção\_encaminhar\_pendente.

## <a name="upgrade-with-a-diff-package"></a>Atualizar um pacote de comparação

Um aplicativo de serviço tecidos pode ser atualizado por provisionamento com um pacote de aplicativo completo e independente. Também é possível atualizar um aplicativo usando um pacote de comparação que contém apenas os arquivos de aplicativo atualizado, o manifesto de aplicativo atualizado e os arquivos de manifesto de serviço.

Um pacote de aplicativo completo contém todos os arquivos necessários para iniciar e executar um aplicativo de serviço tecidos. Um pacote de comparação contém apenas os arquivos que foram alterados entre o último provisionar e a atualização atual, além de arquivos de manifesto manifesto do aplicativo completo e o serviço. Qualquer referência no manifesto do aplicativo ou manifesto de serviço que não pode ser encontrado no layout de compilação é procurada na loja de imagem.

Pacotes de aplicativo completo são necessários para a primeira instalação de um aplicativo ao cluster. Atualizações subsequentes podem ser um pacote de aplicativo completo ou um pacote de comparação.

Ocasiões quando usando um pacote de comparação seria uma boa opção:

* Um pacote de comparação é preferencial quando você tiver um pacote de aplicativo grande que faça referência a vários arquivos de manifesto de serviço e/ou vários pacotes de código, config pacotes ou pacotes de dados.

* Um pacote de comparação é preferencial quando você tiver um sistema de implantação que gera o layout de compilação diretamente do seu processo de criação do aplicativo. Nesse caso, mesmo que o código não foi alterado, montagens recém-criado obtém uma soma de verificação diferentes. Usando um pacote de aplicativo completo exigem que você atualize a versão em todos os pacotes de código. Usando um pacote de comparação, você fornece somente os arquivos que foram alterados e os arquivos de manifesto onde a versão foi alterada.

Quando um aplicativo é atualizado usando o Visual Studio, o pacote de comparação é publicado automaticamente. Para criar um pacote de comparação manualmente, o manifesto do aplicativo e os manifestos de serviço devem ser atualizado, mas apenas os pacotes alterados devem ser incluídos no pacote do aplicativo final. 

Por exemplo, vamos começar com o seguinte aplicativo (números de versão fornecidos para facilitar a compreensão):

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Agora, vamos supor que você quisesse pacote de atualização somente o código de service1 usando um pacote de comparação usando o PowerShell. Agora, seu aplicativo atualizado tem a seguinte estrutura de pastas:

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Nesse caso, você atualizar o manifesto do aplicativo 2.0.0 e o manifesto de serviço para service1 para refletir a atualização de pacote de código. A pasta para o pacote de aplicativo teria a estrutura a seguir:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Próximas etapas

[Atualizar o seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) conduz você por uma atualização do aplicativo usando o Visual Studio.

[Atualizando seu aplicativo usando o Powershell](service-fabric-application-upgrade-tutorial-powershell.md) conduz você por uma atualização do aplicativo usando o PowerShell.

Controle como o seu aplicativo atualizações usando [Parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne suas atualizações de aplicativo compatíveis aprender como usar a [Serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Corrigi problemas comuns em atualizações de aplicativo consultando as etapas na [Solução de problemas de atualizações de aplicativo](service-fabric-application-upgrade-troubleshooting.md).
 
