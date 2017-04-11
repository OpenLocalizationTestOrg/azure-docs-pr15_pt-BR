<properties
    pageTitle="Notas de versão para Serviços BizTalk Azure | Serviços de BizTalk do Microsoft Azure"
    description="Lista os problemas conhecidos para serviços de BizTalk do Azure" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Notas de versão para Serviços BizTalk Azure

As notas de versão para os serviços do Microsoft Azure BizTalk contêm os problemas conhecidos nesta versão.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Novidades na atualização de novembro dos serviços do BizTalk
* Criptografia no restante pode ser habilitada no Portal de Serviços BizTalk. Consulte [Ativar criptografia inativos no Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Histórico de atualização

### <a name="october-update"></a>Atualização de outubro

* Contas organizacionais são suportadas:  
 * **Cenário**: você registrou uma implantação de BizTalk Service usando uma conta da Microsoft (como o user@live.com). Nesse cenário, somente usuários do Microsoft Account podem gerenciar o BizTalk Service usando o portal de Serviços BizTalk. Uma conta organizacional não pode ser usada.  
 * **Cenário**: você registrou uma implantação de BizTalk Service usando uma conta organizacional no Azure Active Directory (como user@fabrikam.com ou user@contoso.com). Nesse cenário, somente usuários do Azure Active Directory na mesma organização podem gerenciar o BizTalk Service usando o portal de Serviços BizTalk. Uma conta da Microsoft não pode ser usada.  
* Quando você cria um BizTalk Service no portal de clássico do Azure, você é automaticamente registrados no Portal de Serviços BizTalk.
 * **Cenário**: você entra no portal do Azure clássico, criar um BizTalk Service e, em seguida, selecione **Gerenciar** pela primeira vez. Ao abre o portal de serviços do BizTalk, o BizTalk Service registra automaticamente e está pronto para as implantações.  
 Consulte [Registrando e atualizando uma implantação de serviço BizTalk no Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Atualização de 14 de agosto
* Contrato e ponte separação – negociação contratos de parceiros e pontes agora são desassociados no Portal de Serviços BizTalk. Agora você criar contratos e pontes separadamente e em tempo de execução pontes resolvem um contrato com base nos valores na mensagem EDI. Consulte [Criar contratos nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689908.aspx), [Crie uma ponte EDI usando o Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [criar uma ponte de AS2 usando o Portal de serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx), e [como pontes resolver a contratos em tempo de execução?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* A opção para criar modelos para contratos será interrompida.  
* Para o contrato de envio lado, agora você pode especificar conjuntos de delimitador diferentes para cada esquema. Esta configuração é especificada em configurações de protocolo para contrato de lado de envio. Para obter mais informações, consulte [Create X12 contrato nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx) e [criar um contrato EDIFACT nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/dn606267.aspx). Duas novas entidades também são adicionadas à API OM TPM para a mesma finalidade. Consulte [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) e [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Padrão construções XSD, incluindo tipos derivados, agora têm suporte. Consulte [Usar padrão XSD construções em seus mapas](https://msdn.microsoft.com/library/azure/dn793987.aspx) e [Tipos de derivadas de uso em cenários de mapeamento e exemplos](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 é compatível com novos algoritmos de Microfone para assinar mensagem e novos algoritmos de criptografia. Consulte [criar um contrato AS2 nos serviços do BizTalk Azure](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Conheça os problemas

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemas de conectividade após a atualização do Portal de serviços do BizTalk

  Se você tiver o Portal de Serviços BizTalk abrir enquanto os serviços do BizTalk é atualizado para reverter as mudanças no serviço, você poderá enfrentar problemas de conectividade com o Portal de serviços do BizTalk.  
  Como alternativa, você pode reiniciar o navegador, excluir o cache do navegador ou iniciar o portal no modo particular.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE não conseguir localizar o artefato se você clicar em um erro ou aviso em um projeto de Serviços BizTalk
Instale o Visual Studio 2012 atualização 3 RC 1 para corrigir o problema.  

### <a name="custom-binding-project-reference"></a>Referência de projeto de ligação personalizada
Considere as seguintes situações com um projeto de Serviços BizTalk em uma solução Visual Studio:  
* Na mesma solução Visual Studio, há um projeto de Serviços BizTalk e um projeto de ligação personalizada. O projeto BizTalk Service tem uma referência a esse arquivo de projeto de ligação personalizada.
* O projeto BizTalk Service tem uma referência a uma associação/comportamento personalizado DLL.

Você 'Build' a solução no Visual Studio com êxito. Em seguida, você 'Reconstruir' ou 'Limpa' a solução. Depois disso, quando você recriar ou limpe novamente, ocorre o seguinte erro:  
  Não é possível copiar o arquivo <Path to DLL> para "bin\Debug\FileName.dll". O processo não pode acessar o arquivo 'bin\Debug\FileName.dll' porque ele está sendo usado por outro processo.  

#### <a name="workaround"></a>Solução alternativa
* Se o [Visual Studio 2012 atualização 3](https://www.microsoft.com/download/details.aspx?id=39305) estiver instalado, você tem duas opções a seguir:

  * Reinicie o Visual Studio, ou

  * Reinicie a solução. Em seguida, execute somente uma compilação na solução.  

* Se o [Visual Studio 2012 atualização 3](https://www.microsoft.com/download/details.aspx?id=39305) não estiver instalado, abra o Gerenciador de tarefas, clique na guia processos, clique no processo de MSBuild.exe e clique no botão Finalizar processo.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Roteamento para pontos de extremidade de BasicHttpRelay não é suportado de pontes e o Portal de serviços do BizTalk se caracteres não imprimíveis são promovidos como cabeçalhos HTTP

Se você usar caracteres não imprimíveis como parte das propriedades promovidas para mensagens, as mensagens não podem ser roteadas para destinos de retransmissão que usam a vinculação de BasicHttpRelay. Além disso, as propriedades promovidas que estão disponíveis como parte do controle não codificada para destinos e codificada como URL para blobs.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN é enviada assíncrona mesmo se a opção de MDN assíncrona enviar está desmarcada  
Considere este cenário – se você selecionar a caixa de seleção **Enviar MDN assíncrona** e, especifique uma URL para enviar o assíncrono MDN e desmarque a caixa de seleção **Enviar MDN assíncrona** novamente, o MDN ainda é enviada para a URL especificada mesmo que a opção Enviar assíncrono MDNs não está selecionada.  
Como alternativa, você deve desmarcar a URL especificada antes desmarcando a caixa de seleção **Enviar MDN assíncrona** e implantar o contrato AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Caracteres de espaço em branco além uma troca válida causam uma mensagem vazia sejam enviadas para o ponto de extremidade de suspensão  
Se houver espaços em branco, além de um segmento IEA, o Desassemblador trata como final da troca atual e analisa o próximo conjunto de espaços em branco como uma mensagem próximo. Como isso não é válida troca, você pode observar que uma mensagem de êxito é enviada para o destino de rota e uma mensagem vazia é enviada o ponto de extremidade de suspender.  
### <a name="tracking-in-biztalk-services-portal"></a>Controle no Portal de serviços do BizTalk  
Eventos de controle são capturados até o processamento da mensagem EDI e qualquer correlação. Se uma mensagem falhar fora o estágio de protocolo, acompanhamento mostrará como bem-sucedida. Nessa situação, consulte a seção LOG coluna **detalhes** na **rastreamento** para obter detalhes do erro.
O X12 receber e enviar configurações ([Create X12 contrato nos serviços do Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx)) fornecem informações sobre o estágio de protocolo.  

### <a name="update-agreement"></a>Contrato de atualização  
Portal de serviços do BizTalk permite que você modifique o qualificador de uma identidade quando um contrato é configurado. Isso pode resultar em Propriedades de inconsistence. Por exemplo, há um contrato usando ZZ:1234567 e ZZ:7654321 o qualificador. Nas configurações de perfil do Portal de serviços do BizTalk, você alterar ZZ:1234567 para ser 01:ChangedValue. Você abre o contrato e 01:ChangedValue é exibida em vez de ZZ:1234567.
Para modificar o qualificador de uma identidade, excluir o contrato, atualizar **identidades** no perfil do parceiro e, em seguida, recrie o contrato.  
> AZURE. Aviso esse comportamento afeta X12 e AS2.  

### <a name="as2-attachments"></a>AS2 anexos  
Anexos de AS2 mensagens não são suportadas em enviar ou recebem. Especificamente, anexos silenciosamente são ignorados e o corpo da mensagem é processado como uma mensagem de AS2 normal.  
### <a name="resources-remembering-path"></a>Recursos: Caminho de lembrar  
Ao adicionar **recursos**, a janela de diálogo pode não se lembre do caminho anteriormente usado para adicionar um recurso. Para se lembrar o caminho usado anteriormente, tente adicionar o site de Portal de serviços do BizTalk aos **Sites confiáveis** no Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Se você renomear a entidade de uma ponte e fecha o projeto sem salvar as alterações, abrindo a entidade novamente resulta em erro
Considere um cenário na seguinte ordem:  
* Adicionar uma ponte (por exemplo, uma ponte de One-Way de XML) a um projeto de BizTalk Service  

* Renomeie a ponte especificando um valor para a propriedade de nome de entidade. Isso renomeia o arquivo de .bridgeconfig associado com o nome que você especificou.  

* Feche o arquivo de .bcs (fechando a guia no Visual Studio) sem salvar as alterações.  

* Abra o arquivo de .bcs novamente de Solution Explorer.  
Você observará que enquanto o arquivo de .bridgeconfig associado tem o novo nome que você especificou, o nome de entidade na superfície de design ainda é o nome antigo. Se você tentar abrir a configuração de ponte clicando duas vezes o componente de ponte, você obterá o seguinte erro:  
  '<old name>'Entidade associada do arquivo'<old name>.bridgeconfig' não existe  
Para evitar a execução para esse cenário, certifique-se de que salvar as alterações após renomear entidades em um projeto BizTalk Service.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Projeto BizTalk Service criado com êxito mesmo se um artefato foi excluído de um projeto do Visual Studio
Considere um cenário onde você adiciona um artefato (por exemplo, um arquivo XSD) a um projeto de BizTalk Service, incluir nesse artefato a configuração da ponte (por exemplo, especificando-lo como um tipo de mensagem de solicitação) e, em seguida, exclua-o do projeto do Visual Studio. Nesse caso, compilar o projeto não terá qualquer erro desde o artefato excluído está disponível no disco no mesmo local do qual ele foi incluído no projeto do Visual Studio.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>O projeto BizTalk Service não verifica a disponibilidade de esquema ao configurar as pontes
Em um projeto do BizTalk Service, se um esquema que é adicionado ao projeto importa outro esquema, o projeto BizTalk Service não verifica se o esquema importado é adicionado ao projeto. Se você tentar criar esse projeto, você não obtiver os erros de compilação.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>A mensagem de resposta para uma ponte de solicitação-resposta XML é sempre do conjunto de caracteres UTF-8
Nesta versão, o conjunto de caracteres da mensagem de resposta de uma ponte de solicitação-resposta XML é sempre definido como UTF-8.
### <a name="user-defined-datatypes"></a>Tipos de dados definidos pelo usuário
Os adaptadores BizTalk adaptador Pack dentro o recurso de serviço de adaptador BizTalk podem utilizar tipos de dados definidos pelo usuário para operações de adaptador.
Ao usar tipos de dados definidos pelo usuário, copie os arquivos (. dll) para unidade: \Program Files\Microsoft BizTalk adaptador Service\BAServiceRuntime\bin\ ou para o Global Assembly Cache (GAC) no servidor que hospeda o serviço BizTalk adaptador. Caso contrário, o seguinte erro pode ocorrer no cliente:  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] É recomendável usar GACUtil.exe para instalar um arquivo para o Cache de Assembly Global. GACUtil.exe documentos como usar essa ferramenta e as opções de linha de comando do Visual Studio.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Reiniciar o BizTalk adaptador serviço Web Site
Instalando o **Tempo de execução de serviço de adaptador BizTalk** * cria o * *Serviço de adaptador BizTalk* * site no IIS que contém o * *BAService* * aplicativo.* *BAService** aplicativo internamente usa associação de retransmissão para ampliar o alcance do ponto de extremidade do serviço no local para a nuvem. Para um serviço hospedado no local, o ponto de extremidade de retransmissão correspondente será registrado no serviço barramento somente quando o serviço de local é iniciado.  

Se você parar e iniciar um aplicativo, a configuração para iniciar automaticamente um aplicativo não será aplicada. Portanto quando **BAService** é interrompido, você deve sempre reiniciar o site do **Serviço de adaptador BizTalk** em vez disso. Não iniciar ou interromper o aplicativo **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Caracteres especiais não devem ser usados para nomes de endereço e entidade de componentes LOB
Você não deve usar caracteres especiais para nomes de endereço e entidade de componentes LOB. Se você fizer isso, você obterá um erro ao implantar o projeto BizTalk Service. Para determinados caracteres como '%', o site de serviço de adaptador BizTalk pode entrar em um estado parou e você terá que iniciá-lo manualmente.
### <a name="test-map-with-get-context-property"></a>Mapa de teste com propriedade de contexto de Get
Se uma transformação contiver uma operação de mapa de **Obter a propriedade de contexto** , **Mapa de teste** falhará. Como solução temporária, substitua a operação de mapa de **Propriedade de contexto de obter** uma cadeia de caracteres concatenar mapa operação que contêm dados fictícios. Isso irá preencher o esquema de destino e permitem que você testar outras funcionalidades de transformação.
### <a name="test-map-property-does-not-display"></a>Propriedade de mapa de teste não exibidos
Não exibir as propriedades de **Mapa de teste** no Visual Studio. Isso pode ocorrer se a janela **Propriedades** e janela **Solution Explorer** não estiverem encaixadas simultaneamente. Para resolver isso, encaixe janelas **Solution Explorer** e as **Propriedades** .  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>DateTime reformatar suspensa estiver esmaecida
Quando uma operação de mapa DateTime reformatar é adicionada para a superfície de design e configurada, a lista suspensa formato pode estar desabilitada. Isso pode acontecer se o vídeo do computador está definido como **Médio – 125%** ou **maior – 150%**. Para resolver, defina o vídeo com **menor – 100% (padrão)** usando as etapas abaixo:  
1. Abra o **Painel de controle** e clique em **aparência e personalização**.
2. Clique em **Exibir**.
3. Clique em **menor – 100% (padrão)** e clique em **Aplicar**.

Lista suspensa **formato** agora deve funcionar conforme o esperado.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Contratos duplicados no Portal de serviços do BizTalk
Considere o seguinte cenário:
1. Crie um contrato usando a API de OM de gerenciamento de parceiro negociação.
2. Abra o contrato no Portal de Serviços BizTalk em duas guias diferentes.
3. Implante o contrato de ambas as guias.
4. Como resultado, os dois os contratos são implantados resulta em entradas duplicadas no Portal de serviços do BizTalk

**Solução alternativa**. Abra qualquer um dos contratos duplicados no Portal de Serviços BizTalk e Cancelar.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Pontes não usam certificado atualizado mesmo após um certificado foi atualizado no repositório do artefato
Considere os seguintes cenários:  

**Cenário 1: Usando certificados baseados em impressão digital para proteger a transferência de mensagens de uma ponte para um ponto de extremidade do serviço**  
Considere um cenário em que você usa certificados baseados em impressão digital em seu projeto BizTalk Service. Você atualizar o certificado no Portal de Serviços BizTalk com o mesmo nome, mas uma impressão digital diferente, mas não atualizar o projeto BizTalk Service adequadamente. Cenário, a ponte pode continuar a processar as mensagens porque os dados de certificado mais antigos ainda podem estar no cache de canal. Depois disso, o processamento de mensagem falhar.  

**Solução alternativa**: atualizar o certificado no projeto BizTalk Service e reimplantar o projeto.  

**Cenário 2: Usando comportamentos baseados em nome para identificar certificados para proteger a transferência de mensagens de uma ponte para um ponto de extremidade do serviço**

Considere um cenário onde você use comportamentos baseados em nome para identificar certificados em seu projeto BizTalk Service. Você atualizar o certificado no Portal de serviços do BizTalk, mas não atualizar o projeto BizTalk Service adequadamente. Cenário, a ponte pode continuar a processar as mensagens porque os dados de certificado mais antigos ainda podem estar no cache de canal. Depois disso, o processamento de mensagem falhar.  

**Solução alternativa**: atualizar o certificado no projeto BizTalk Service e reimplantar o projeto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Pontes continuam processar mensagens, mesmo quando o banco de dados do SQL está offline
As pontes de Serviços BizTalk continuará processar mensagens por um tempo, mesmo se o Microsoft Azure SQL Database (que armazena as informações em execução como artefatos implantados e canais), está offline. Isso ocorre porque os Serviços BizTalk usa a configuração de ponte e artefatos armazenados em cache.
Se você não quiser as pontes para processar quaisquer mensagens quando o banco de dados do SQL está offline, você pode usar os cmdlets do PowerShell de serviços do BizTalk para interromper ou suspender o BizTalk Service. Consulte [Exemplo de gerenciamento de serviço do Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329019) para cmdlets do Windows PowerShell para gerenciar operações.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Ler a mensagem XML no componente de código personalizado de uma ponte inclui um caractere BOM extra
Considere um cenário onde você deseja ler uma mensagem XML dentro de código personalizado de uma ponte. Se você usar o .NET API System.Text.Encoding.UTF8.GetString(bytes) um caractere BOM extra é incluído na saída no início da mensagem. Portanto, se não desejar a saída para incluir o caractere BOM extra, você deve usar ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Enviar mensagens para uma ponte usando WCF não dimensionar
As mensagens enviadas para uma ponte usando WCF não dimensionar. Você deve usar HttpWebRequest se quiser que um cliente scalable.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>ATUALIZAÇÃO: Erro de provedor de Token após a atualização do BizTalk serviços visualização para disponibilidade geral (GA)
Há um EDI ou AS2 contrato com lotes de ativos. Quando o BizTalk Service é atualizado em Visualizar para GA, pode ocorrer o seguinte:
* Erro: O provedor de token pôde fornecer um token de segurança. Provedor de token retornado mensagem: O nome remoto não pôde ser resolvido.

* Tarefas em lote são canceladas.

**Solução alternativa**: após o serviço BizTalk é atualizado para disponibilidade geral (GA), reimplantar o contrato.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>ATUALIZAÇÃO: Ferramentas mostra os ícones de ponte antiga depois de atualizar o SDK dos serviços do BizTalk
Depois de atualizar uma versão anterior do SDK de serviços do BizTalk, que tinha antigos ícones que representam as pontes, a caixa de ferramentas continua mostrar os ícones antigos para as pontes. No entanto, se você adicionar uma ponte para superfície de designer de projeto do BizTalk Service, a superfície mostra o novo ícone.  

**Solução alternativa**. Você pode contornar esse problema excluindo os arquivos .tbd em <system drive>: \Users\<usuário > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>ATUALIZAÇÃO: Atualização de BizTalk Portal desde a visualização para GA pode mostrar um erro indicando que o recurso EDI não está disponível
Se você estiver conectado ao Portal de serviços do BizTalk enquanto os serviços do BizTalk é atualizado em Visualizar para GA, você pode receber o seguinte erro no portal:  

Esse recurso não está disponível como parte nesta edição do Microsoft Azure BizTalk Services. Para usar esses recursos alternar para uma edição apropriada.  

**Resolução**: Log-out a partir do portal, fechar e abrir o navegador e, em seguida, log no portal.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>ATUALIZAÇÃO: Novo controle de dados não aparece depois de Serviços BizTalk é atualizado para GA
Suponha que um cenário em que você tenha uma ponte XML implantada em assinatura de visualização de Serviços BizTalk. Enviar mensagens à ponte e o controle de dados correspondente está disponível no Portal de serviços do BizTalk. Agora, se os bits de tempo de execução do Portal de serviços do BizTalk e os serviços do BizTalk são atualizados para GA e enviar uma mensagem para o mesmo ponto de extremidade de ponte implantado anteriormente, os dados de controle não aparecer para mensagens enviadas após a atualização.  

### <a name="pipelines-vs-bridges"></a>Canais v/s pontes
Neste documento, o termo 'canais' e 'pontes' são usados alternadamente. Ambos essencialmente o mesmo significam, que é, uma unidade de processamento de mensagem implantada nos serviços do BizTalk.  

### <a name="concepts"></a>Conceitos  

[Serviços do BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
