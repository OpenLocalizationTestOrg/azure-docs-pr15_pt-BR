<properties
   pageTitle="Conector do PowerShell | Microsoft Azure"
   description="Este artigo descreve como configurar Windows PowerShell conector da Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="windows-powershell-connector-technical-reference"></a>Referência técnica do conector do Windows PowerShell
Este artigo descreve o conector do Windows PowerShell. O artigo aplica-se aos seguintes produtos:

- Gerenciador de identidades do Microsoft 2016 (MIM2016)
- Gerenciador de identidades Forefront 2010 R2 (FIM2010R2)
    -   Deve usar hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como um download a partir do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Visão geral do conector PowerShell
O conector do PowerShell permite integrar o serviço de sincronização com sistemas externos que oferecem que APIs baseadas no Windows PowerShell. O conector fornece uma ponte entre os recursos do agente de gerenciamento baseado em chamada do extensible connectivity 2 (ECMA2) framework e Windows PowerShell. Para obter mais informações sobre o framework ECMA, consulte a [Referência de agente de gerenciamento do Extensible conectividade 2.2](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Pré-requisitos
Antes de usar o conector, verifique se que você tem o seguinte no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior
- O Windows PowerShell 2.0, 3.0 ou 4.0

A política de execução no servidor do serviço de sincronização deverá ser configurada para permitir que o conector executar scripts do Windows PowerShell. A menos que os scripts que executa o conector assinadas digitalmente, configure a política de execução executando este comando:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector do Windows PowerShell no serviço de sincronização, você deve fornecer uma série de scripts do Windows PowerShell que execute as etapas solicitadas pelo serviço de sincronização. Dependendo da fonte de dados que você se conecta ao e a funcionalidade necessária, os scripts que você deve implementar varia. Esta seção descreve cada um dos scripts que pode ser implementado e quando eles são necessários.

O conector do Windows PowerShell foi projetado para armazenar cada um dos scripts dentro do banco de dados de serviço de sincronização. Embora seja possível executar scripts que estão armazenados no sistema de arquivos, é mais fácil inserir corpo de cada script diretamente à configuração do conector.

Para criar um conector do PowerShell, no **Serviço de sincronização** , selecione **Agente de gerenciamento** e **criar**. Selecione o conector do **PowerShell (Microsoft)** .

![Criar conector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Conectividade
Fornece parâmetros de configuração para conexão com um sistema remoto. Esses valores são armazenados pelo serviço de sincronização e disponibilizados para scripts do Windows PowerShell quando o conector é executado com segurança.

![Conectividade](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Você pode configurar os seguintes parâmetros de conectividade:

**Conectividade**

Parâmetro | Valor padrão | Finalidade
--- | --- | ---
Servidor | <Blank> | Nome do servidor que o conector deve se conectar.
Domínio | <Blank> | Domínio da credencial armazenar para uso quando o conector é executado.
Usuário | <Blank> | Nome de usuário da credencial armazenar para uso quando o conector é executado.
Senha | <Blank> | Senha da credencial armazenar para uso quando o conector é executado.
Representar conta de conector | FALSO | Quando for verdadeiro, o serviço de sincronização é executado os scripts do Windows PowerShell no contexto das credenciais fornecidas. Quando possível, é recomendável que o parâmetro **$Credentials** é passado para cada script é usado em vez de representação. Para obter mais informações sobre permissões adicionais que são necessárias para usar esta opção, consulte [Configuração adicional para a representação](#additional-configuration-for-impersonation).
Carregar o perfil de usuário de representação | FALSO | Instrui o Windows para carregar o perfil de usuário das credenciais do conector durante a representação. Se o usuário representado tem um perfil móvel, o conector não carrega o perfil móvel. Para obter mais informações sobre permissões adicionais que são necessárias para usar esse parâmetro, consulte [Configuração adicional para a representação](#additional-configuration-for-impersonation).
Tipo de logon de representação | Nenhum | Tipo de logon durante a representação. Para obter mais informações, consulte o [dwLogonType] [ dw] documentação.
Somente Scripts assinados | FALSO | Se verdadeiro, o conector do Windows PowerShell valida que cada script possui uma assinatura digital válida. Se for falso, certifique-se de que a política de execução do Windows PowerShell do servidor serviço de sincronização é RemoteSigned ou irrestrito.

**Módulo comuns**  
O conector permite armazenar um módulo Windows PowerShell compartilhado na configuração. Quando o conector executa um script, o módulo Windows PowerShell é extraído para o sistema de arquivos para que ele pode ser importado por cada script.

Para obter scripts importar, exportar e sincronização de senha, o módulo comum é extraído MAData pasta do conector. Para os scripts de descoberta de esquema, validação, hierarquia e partição, o módulo comum é extraído para a pasta % TEMP %. Em ambos os casos, o script de módulo comuns extraído é nomeado acordo com a configuração de nome de Script de módulo comum.

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 da pasta MAData, use a instrução a seguir:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 da pasta % TEMP %, use a instrução a seguir:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validação de parâmetro**  
O Script de validação é um script do Windows PowerShell opcional que pode ser usado para garantir que os parâmetros de configuração do conector fornecidos pelo administrador são válidos. Servidor de validação, credenciais de conexão e parâmetros de conectividade são usos comuns do script de validação. Script de validação é chamado após as seguintes guias e caixas de diálogo são modificadas:

- Conectividade
- Parâmetros globais
- Configuração de partição

Script de validação recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | A guia configuração ou caixa de diálogo que disparou a solicitação de validação.
ConfigParameters | [KeyedCollection] [ keyk] [cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.

Script de validação deve retornar um único objeto de ParameterValidationResult no pipeline.

**Descoberta de esquema**  
O script de descoberta de esquema é obrigatório. Esse script retorna os tipos de objeto, atributos e restrições de atributo que o serviço de sincronização usa ao configurar regras de fluxo de atributo. O script de descoberta de esquema é executado durante a criação do conector e preenche esquema do conector. Ele também é usado pela ação Atualizar esquema no Gerenciador de serviço de sincronização.

O script de descoberta de esquema recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.

O script deve retornar um único [esquema] [ schema] objeto para o pipeline. O objeto de esquema é composto [SchemaType] [ schemaT] objetos que representam os tipos de objeto (por exemplo: usuários e grupos). O objeto SchemaType contém uma coleção de [SchemaAttribute] [ schemaA] objetos que representam os atributos (por exemplo: nome, sobrenome e endereço postal) do tipo.

**Parâmetros adicionais**  
Além das configurações padrão, você pode definir outras configurações personalizadas que são específicas para a instância do conector. Esses parâmetros podem ser especificados no conector, partição, ou etapa execução níveis e acessados a partir do script do Windows PowerShell relevante. Configurações personalizadas podem ser armazenadas no banco de dados de serviço de sincronização no formato de texto sem formatação ou eles podem ser criptografados. O serviço de sincronização automaticamente criptografa e descriptografa seguro configurações quando necessário.

Para especificar configurações personalizadas, separe o nome de cada parâmetro com uma vírgula (,).

Para acessar as configurações personalizadas de um script, você deve sufixo o nome com um sublinhado ( \_ ) e o escopo do parâmetro (Global, partição ou RunStep). Por exemplo, para acessar o parâmetro FileName Global, use este trecho de código:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Recursos
Na guia recursos do gerenciamento de agente Designer define o comportamento e a funcionalidade do conector. As seleções feitas nesta guia não podem ser modificadas quando o conector foi criado. Esta tabela lista as configurações do recurso.

![Recursos](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Recurso | Descrição |
--- | --- |
[Estilo de nome diferenciado][dnstyle] | Indica se o conector suporta nomes distintos e então, qual estilo.
[Tipo de exportação][exportT] | Determina o tipo de objetos que são apresentados para o script de exportação. <li>AttributeReplace – inclui o conjunto completo de valores para um atributo de valores múltiplos quando o atributo for alterado.</li><li>AttributeUpdate – inclui apenas deltas para um atributo de valores múltiplos quando o atributo for alterado.</li><li>MultivaluedReferenceAttributeUpdate - inclui um conjunto completo de valores para atributos de valores múltiplos não-referência e apenas deltas para atributos de valores múltiplos de referência.</li><li>ObjectReplace – inclui todos os atributos de um objeto quando qualquer alterações de atributos</li>
[Normalização de dados][DataNorm] | Instrui o serviço de sincronização para normalizar atributos de âncora antes que eles são fornecidos para scripts.
[Confirmação de objeto][oconf] | Configura o comportamento de importação pendente no serviço de sincronização. <li>Normal – comportamento padrão que espera exportadas todas as alterações para ser confirmada por meio da importação</li><li>NoDeleteConfirmation – quando um objeto é excluído, não há nenhuma importação pendente gerada.</li><li>NoAddAndDeleteConfirmation – quando um objeto é criado ou excluído, não há nenhuma importação pendente gerada.</li>
Usar DN como âncora | Se o estilo de nome distinto é definido para LDAP, o atributo de âncora para o espaço de conector também é o nome distinto.
Operações simultâneas de vários conectores | Quando marcada, vários conectores do Windows PowerShell podem ser executados simultaneamente.
Partições | Quando marcada, o conector suporta vários partições e descoberta de partição.
Hierarquia | Quando marcada, o conector suporta uma estrutura hierárquica de estilo LDAP.
Habilitar a importação | Quando marcada, o conector importa dados por meio de scripts de importação.
Habilitar a importação de Delta | Quando marcada, o conector pode solicitar deltas dos scripts de importação.
Habilitar exportação | Quando marcada, o conector exporta dados por meio de scripts de exportação.
Habilitar exportação completa | Quando selecionada, os scripts de exportação oferecem suporte à exportação o espaço de conector inteiro. Para usar esta opção, a habilitar exportar também deve ser verificada.
Nenhum valor de referência no primeiro passo de exportação | Quando marcada, atributos de referência são exportados em uma segunda passagem de exportação.
Habilitar a renomeação de objeto | Quando marcada, nomes distintos podem ser modificados.
Excluir-adicionar como substituir | Quando marcada, excluir-Adicione operações são exportadas como uma única substituição.
Habilitar operações de senha | Quando marcada, scripts de sincronização de senha são suportados.
Habilitar a senha de exportação no primeiro passo | Quando marcada, definir durante a configuração de senhas são exportadas quando o objeto é criado.

### <a name="global-parameters"></a>Parâmetros globais
Na guia parâmetros globais no Designer de agente de gerenciamento permite que você configure os scripts do Windows PowerShell que são executados pelo conector. Você também pode configurar valores globais para as configurações personalizadas definidos na guia de conectividade.

**Descoberta de partição**  
Uma partição é um namespace separado dentro de um esquema compartilhado. Por exemplo, do Active Directory cada domínio é uma partição dentro de uma floresta. Uma partição é o agrupamento lógico para importação e exportação de operações. Importar e exportar têm partição como um contexto e todas as operações acontece neste contexto. Partições devem para representar uma hierarquia no LDAP. O nome distinto de uma partição é usado na importação para verificar se todos os objetos retornados estão dentro do escopo de uma partição. O nome distinto da partição também é usado durante a configuração do metaverso no espaço de conector para determinar a partição que deve ser associado a um objeto durante a exportação.

O script de descoberta de partição recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.

O script deve retornar um uma única [partição] [ part] objeto ou uma lista [T] dos objetos de partição no pipeline.

**Descoberta de hierarquia**  
O script de descoberta de hierarquia é usado apenas quando o recurso de estilo de nome distinto é LDAP. O script é usado para permitir que você navegue e selecione um conjunto de contêineres que é considerado ou reduzir o escopo para importar e exportar operações. O script deve fornecer somente uma lista de nós que sejam filhos diretos do nó raiz fornecido para o script.

O script de descoberta de hierarquia recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
ParentNode | [HierarchyNode][hn] | O nó raiz da hierarquia em qual o script deve retornar filhos diretos.

O script deve retornar um um objeto de HierarchyNode único filho ou uma lista [T] de objetos de HierarchyNode filho à tubulação.

#### <a name="import"></a>Importar
Conectores que suportam as operações de importação devem implementar três scripts.

**Iniciar a importação**  
O script de importação de começar é executado no início de uma etapa de importação executar. Durante esta etapa, você pode estabelecer uma conexão com o sistema de origem e fazer etapas preparatórias antes de importar dados de sistema conectado.

O script de importação de começar recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de importação executar (delta ou completo), partição, hierarquia, marca d'água e tamanho de página esperada.
Tipos de | [Esquema][schema] | Esquema para o espaço de conector importados.

O script deve retornar um único [OpenImportConnectionResults] [ oicres] o objeto para o pipeline de, por exemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importar dados**  
O script de importação de dados é chamado pelo conector até que o script indica que não há nenhum mais dados para importar. O conector do Windows PowerShell tem um tamanho de página de 9.999 objetos. Se seu script retornar mais de 9.999 objetos para importação, você deve suportar paginação. Expõe o conector uma propriedade de dados personalizados que você pode usar para uma loja uma marca d'água para que cada vez que o script de importação de dados é chamada, o script continua importar objetos onde parou.

O script de importação de dados recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contém a marca d'água (CustomData) que pode ser usada durante a paginação importações e delta importa.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de importação executar (delta ou completo), partição, hierarquia, marca d'água e tamanho de página esperada.
Tipos de | [Esquema][schema] | Esquema para o espaço de conector importados.

O script de importação de dados deve gravar uma lista [[CSEntryChange][csec]] objeto para o pipeline. Essa coleção é composta de atributos de CSEntryChange que representam cada objeto que está sendo importado. Durante uma execução de importação completa, esta coleção deve ter um conjunto completo de objetos de CSEntryChange que possuem todos os atributos de cada objeto. Durante uma importação de Delta, o objeto CSEntryChange deve conter os deltas de nível de atributo de cada objeto para importar ou uma representação completa dos objetos que foram alterados (modo de substituir).

**Importar de fim**  
No final da importação executar, o script final importar é executado. Esse script deve executar as tarefas de limpeza necessárias (por exemplo, fechar conexões com sistemas) e responder a falhas.

O script de importação de fim recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de importação executar (delta ou completo), partição, hierarquia, marca d'água e tamanho de página esperada.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informa o script sobre o motivo pelo qual que a importação foi encerrada.

O script deve retornar um único [CloseImportConnectionResults] [ cicres] o objeto para o pipeline de, por exemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportar
Idêntico à arquitetura de importação do conector, conectores que oferece suporte à exportação devem implementar três scripts.

**Começar a exportação**  
O script de exportação de começar é executado no início de uma etapa de execução de exportação. Durante esta etapa, você pode estabelecer uma conexão com o sistema de origem e conduzir qualquer etapas preparatórias antes de exportar dados para o sistema conectado.

O script de exportação comece recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de exportação executar (delta ou completo), partição, hierarquia e tamanho de página esperada.
Tipos de | [Esquema][schema] | Esquema para o espaço de conector que é exportado.

O script não deve retornar qualquer saída para o pipeline.

**Exportar dados**  
O serviço de sincronização chama o script de exportar dados quantas vezes é necessária para processar todas as exportações pendentes. Se o espaço de conector tem mais exportações pendentes que tamanho da página do conector, o script de exportação de dados pode ser chamado várias vezes e possivelmente várias vezes para o mesmo objeto.

O script de exportação de dados recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
CSEntries | IList[CSEntryChange][csec] | Lista de todos os espaços de conector objetos com pendentes exportações sejam processados durante esse passo.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de exportação executar (delta ou completo), partição, hierarquia e tamanho de página esperada.
Tipos de | [Esquema][schema] | Esquema para o espaço de conector que é exportado.

O script de exportação de dados deve retornar um [PutExportEntriesResults] [ peeres] objeto para o pipeline. Esse objeto não precisa incluir informações de resultado para cada conector exportado, a menos que ocorre um erro ou uma alteração para o atributo de âncora. Por exemplo, para retornar um objeto de PutExportEntriesResults ao pipeline:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Exportação de fim**  
Após a conclusão da exportação executar, o script de exportação de fim para executar. Esse script deve executar as tarefas de limpeza necessárias (por exemplo, fechar conexões com sistemas) e responder a falhas.

O script de exportação final recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de exportação executar (delta ou completo), partição, hierarquia e tamanho de página esperada.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informa o script sobre o motivo pelo qual que a exportação foi encerrada.

O script não deve retornar qualquer saída para o pipeline.

#### <a name="password-synchronization"></a>Sincronização de senha
Conectores de Windows PowerShell podem ser usados como um destino para alterações/redefinições de senha.

O script de senha recebe os seguintes parâmetros do conector:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia de caracteres, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém quaisquer credenciais inseridas pelo administrador da guia de conectividade.
Partição | [Partição][part] | Partição de diretório que o CSEntry está em.
CSEntry | [CSEntry][cse] | Entrada de espaço do conector para o objeto que está recebeu uma alteração de senha ou redefinir.
OperationType | Cadeia de caracteres | Indica se a operação é uma redefinição (**SetPassword**) ou uma alteração (**ChangePassword**).
PasswordOptions | [PasswordOptions][pwdopt] | Sinalizadores que especificam a senha pretendida redefinir comportamento. Este parâmetro só estará disponível se OperationType for **SetPassword**.
Senha_atual | Cadeia de caracteres | Preenchida com a senha antiga do objeto para alterações de senha. Este parâmetro só estará disponível se OperationType for **ChangePassword**.
Novasenha | Cadeia de caracteres | Preenchida com a senha do objeto novo que o script deve definir.

O script de senha não é esperado para retornar resultados ao pipeline do Windows PowerShell. Se ocorrer um erro no script de senha, o script deve acionar uma das seguintes exceções para informar o serviço de sincronização sobre o problema:

- [PasswordPolicyViolationException] [ pwdex1] – lançada se a senha não atender a política de senha no sistema conectado.
- [PasswordIllFormedException] [ pwdex2] – lançada se a senha não é aceitável para o sistema conectado.
- [PasswordExtension] [ pwdex3] – lançada para todos os outros erros no script de senha.

## <a name="sample-connectors"></a>Conectores de amostra
Para obter uma visão geral completa dos conectores de amostra disponíveis, consulte [Coleção de conector de amostra de conector do Windows PowerShell][samp].

## <a name="other-notes"></a>Outras anotações

### <a name="additional-configuration-for-impersonation"></a>Configuração adicional de representação
Conceda ao usuário que está representada as seguintes permissões no servidor do serviço de sincronização:

Acesso de leitura para as seguintes chaves do registro:

- HKEY_USERS\\\Software\Microsoft\PowerShell [SynchronizationServiceServiceAccountSID]
- HKEY_USERS\\\Environment [SynchronizationServiceServiceAccountSID]

Para determinar o identificador de segurança (SID) da conta de serviço do serviço de sincronização, execute os seguintes comandos do PowerShell:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Acesso de leitura para as seguintes pastas de sistema de arquivo:

- %ProgramFiles%\Microsoft forefront identidade Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront identidade Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront identidade Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Substitua o nome do conector do Windows PowerShell para o espaço reservado {ConnectorName}.

## <a name="troubleshooting"></a>Solução de problemas

-   Para obter informações sobre como habilitar o registro em log solucionar o conector, consulte [como habilitar o rastreamento ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
