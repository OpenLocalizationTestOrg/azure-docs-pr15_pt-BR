<properties
    pageTitle="Como solucionar problemas de armazenamento de arquivo do Azure | Microsoft Azure"
    description="Solucionando problemas de armazenamento de arquivo do Azure"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Solução de problemas de armazenamento de arquivo do Azure

Este artigo lista os problemas comuns que estão relacionados ao armazenamento de arquivos do Microsoft Azure quando você conectar de clientes Windows e Linux. Ele também fornece as possíveis causas e resoluções para esses problemas.

**Problemas gerais (ocorrem em clientes do Windows e Linux)**

- [Erro de cota ao tentar abrir um arquivo](#quotaerror)

- [Desempenho lento ao acessar o armazenamento de arquivos do Azure do Windows ou do Linux](#slowboth)

**Problemas de cliente do Windows**

- [Desempenho lento quando você acessa o armazenamento de arquivos do Azure do Windows 8.1 ou Windows Server 2012 R2](#windowsslow)

- [Erro 53 tentando montar um compartilhamento de arquivo do Azure](#error53)

- [Usar líquido foi bem-sucedida, mas não vejo o arquivo Azure compartilhar montado no Windows Explorer](#netuse)

- [Minha conta de armazenamento contém "/" e a rede usar o comando falhar](#slashfails)

- [Meu aplicativo/serviço não consegue acessar montada unidade de arquivos do Azure.](#accessfiledrive)

- [Recomendações adicionais para otimizar o desempenho](#additional)

**Problemas de cliente Linux**

- [Erro "Você estiver copiando um arquivo para um destino que não dá suporte à criptografia" ao carregar/copiar arquivos para arquivos do Azure](#encryption)

- [Compartilha o erro "Host está inoperante" no arquivo existente, ou o shell trava quando fazendo a lista de comandos no ponto de montagem](#errorhold)

- [Erro de montagem 115 durante a tentativa de arquivos de montagem Azure na VM Linux](#error15)

- [Linux VM enfrentando atrasos aleatórios na comandos como "ls"](#delayproblem)

## <a name="general-problems"></a>Problemas gerais
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Erro de cota ao tentar abrir um arquivo

No Windows, você recebe mensagens de erro semelhante ao seguinte:

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**Não há cota suficiente está disponível para processar este comando**

**Valor de identificador inválido GetLastError: 53**

No Linux, você recebe mensagens de erro semelhante ao seguinte:

**<filename>[permissão negado]**

**Cota de disco excedida**

#### <a name="cause"></a>Causa

O problema ocorre porque você atingiu o limite superior de identificadores abertos simultâneos permitidos para um arquivo.

#### <a name="solution"></a>Solução

Reduzir o número de identificadores abertos simultâneos fechando algumas alças e tente novamente. Para obter mais informações, consulte a [lista de verificação de escalabilidade e desempenho de armazenamento do Microsoft Azure](storage-performance-checklist.md).

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Desempenho lento ao acessar o armazenamento de arquivos do Windows ou Linux

- Se você não tiver um requisito de tamanho mínimo e/s específico, é recomendável que você use 1 MB como o tamanho para um desempenho ideal.

- Se você souber o tamanho final de um arquivo que você estiver estendendo com gravações e seu software não tem problemas de compatibilidade quando cauda ainda não foram gravados no arquivo que contém zeros à esquerda, em seguida, defina o tamanho de arquivo com antecedência em vez de cada gravação sendo uma gravação de extensão.

## <a name="windows-client-problems"></a>Problemas de cliente do Windows
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Desempenho lento quando estiver acessando o armazenamento de arquivos do Windows 8.1 ou Windows Server 2012 R2

Para clientes que executam o Windows 8.1 ou Windows Server 2012 R2, certifique-se de que a correção [KB3114025](https://support.microsoft.com/kb/3114025) é instalada. Esse hotfix melhora a criar e feche a alça de desempenho.

Você pode executar o seguinte script para verificar se o hotfix foi instalado em:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se a correção for instalada, a seguinte saída é exibida:

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1**

> [AZURE.NOTE]  Imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalados pelo início padrão em dezembro de 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Recomendações adicionais para otimizar o desempenho

Nunca crie ou abra um arquivo para cache i/o que está solicitando acesso de gravação, mas não o acesso de leitura. Isto é, quando você chamar **CreateFile ()**, nunca especificar somente **GRAVAÇÃO_GENÉRICA**, mas sempre especificar **GENERIC_READ | GRAVAÇÃO_GENÉRICA**. Uma alça de somente leitura não pode armazenar em cache pequenas gravações localmente, mesmo quando estiver o identificador aberto somente para o arquivo. Isso impõe uma perda de desempenho graves pequenas gravações. Observe que o "a" modo para CRT **fopen()** abre uma alça de somente leitura.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Erro 53" quando você tenta montar ou desmontar um compartilhamento de arquivo do Azure

Esse problema pode ser causado por condições a seguir:

#### <a name="cause-1"></a>Causa 1

"Erro de sistema 53 ocorreu. Acesso negado." Por razões de segurança, conexões para compartilhamentos de arquivos do Azure são bloqueados se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo data center em compartilhamentos de arquivos do Azure residem. Criptografia de canal de comunicação não é fornecida se o cliente do usuário sistema operacional não dá suporte a criptografia SMB. Isso é indicado por um "Erro de sistema 53 ocorreu. Acesso negado"mensagem de erro quando um usuário tenta montar um compartilhamento de arquivo do local ou de uma central de dados diferentes. Windows 8, Windows Server 2012 e versões posteriores de cada solicitação de negociação que inclui 3.0 de SMB, que oferece suporte à criptografia.

#### <a name="solution-for-cause-1"></a>Solução para causa 1

Conectar-se de um cliente que atende aos requisitos do Windows 8, Windows Server 2012 ou versões posteriores, ou que se conecte de uma máquina virtual que está no mesmo data center como a conta de armazenamento do Azure que é usado para o compartilhamento de arquivo do Azure.

#### <a name="cause-2"></a>Causa 2

"Erro de sistema 53" quando você monta um compartilhamento de arquivo Azure pode ocorrer se porta 445 comunicação de saída para arquivos do Azure data center é bloqueada. Clique [aqui](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) para ver o resumo de ISPs que permitir ou não permitir acesso de porta 445.

Comcast e algumas organizações de TI bloqueiam essa porta. Para entender se esse é o motivo a mensagem "Erro de sistema 53", você pode usar Portqry para o ponto de extremidade de TCP:445 de consulta. Se o ponto de extremidade de TCP:445 é exibido conforme filtradas, a porta TCP está bloqueada. Aqui está uma consulta de exemplo:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se o TCP 445 sendo bloqueadas por uma regra do caminho de rede, você verá a seguinte saída:

**Porta TCP 445 (serviço microsoft-ds): FILTRADO**

Para obter mais informações sobre como usar Portqry, consulte [Descrição do utilitário de linha de comando Portqry.exe](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Solução para a causa 2

Trabalhar com sua organização de TI para abrir a porta 445 saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Causa 3

"Erro de sistema 53" também pode ser recebido se NTLMv1 comunicação está habilitada no cliente. Ter NTLMv1 habilitado cria um cliente menos seguro. Portanto, a comunicação será bloqueada para arquivos do Azure. Para verificar se essa é a causa do erro, verifique se as seguintes subchaves do registro está definida como um valor de 3:

HKLM\System\CurrentControlSet\Control\LSA. > LmCompatibilityLevel.

Para obter mais informações, consulte o tópico de [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) no TechNet.

#### <a name="solution-for-cause-3"></a>Solução para a causa 3

Para resolver esse problema, reverta o valor LmCompatibilityLevel na chave do registro HKLM\System\CurrentControlSet\Control\Lsa. para o valor padrão de 3.

Arquivos do Azure suporta somente a autenticação NTLMv2. Certifique-se de que a política de grupo é aplicada aos clientes. Isso impedirá que esse erro ocorrendo. Isso também é considerado como uma prática recomendada de segurança. Para obter mais informações, consulte [como configurar clientes para usar NTLMv2 usando política de grupo](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

A configuração de política recomendada é **Enviar somente resposta NTLMv2**. Isso corresponde a um valor de registro de 3. Os clientes usam somente a autenticação NTLMv2 e usarem a segurança de sessão NTLMv2 se o servidor suportar. Controladores de domínio aceitam autenticação LM, NTLM e NTLMv2.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Usar líquido foi bem-sucedida, mas não vir o arquivo Azure compartilhar montado no Windows Explorer

#### <a name="cause"></a>Causa

Por padrão, o Windows Explorer não executar como administrador. Se você executar o **uso líquido** de um prompt de comando de administrador, você mapear a unidade de rede "como"administrador. Como as unidades mapeadas são direcionadas ao usuário, a conta de usuário que está conectada não exibe as unidades se eles são montados em outra conta de usuário.

#### <a name="solution"></a>Solução

Monte o compartilhamento de uma linha de comando não-administrador. Como alternativa, você pode seguir [Este tópico TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor de registo **EnableLinkedConnections** .

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Minha conta de armazenamento contém "/" e a rede usar o comando falhar

#### <a name="cause"></a>Causa

Quando o comando **use líquido** é executado no Prompt de comando (cmd.exe), ela é analisada adicionando "/" como uma opção de linha de comando. Isso faz com que o mapeamento de unidade falha.

#### <a name="solution"></a>Solução

Você pode usar qualquer uma das etapas a seguir para contornar o problema:

• Use o seguinte comando do PowerShell:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

De um arquivo em lote, isso pode ser feito como

`Echo new-smbMapping ... | powershell -command –`

• Colocar aspas duplas ao redor a tecla para contornar esse problema — a menos que "/" é o primeiro caractere. Se for, use o modo interativo e digite sua senha separadamente ou gerar suas chaves para obter uma chave que não começa com o caractere de barra (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Meu aplicativo/serviço não consegue acessar montada unidade de arquivos do Azure

#### <a name="cause"></a>Causa

Unidades são montadas por usuário. Se o aplicativo ou serviço está em execução em outra conta de usuário, os usuários não verão a unidade.

#### <a name="solution"></a>Solução

Monte a unidade da mesma conta de usuário em qual é o aplicativo. Isso pode ser feito usando ferramentas como psexec.

Como alternativa, você pode criar um novo usuário que tenha os mesmos privilégios que a conta de serviço ou sistema de rede e execute **cmdkey** e **uso líquido** nessa conta. O nome de usuário deve ser o nome da conta de armazenamento e senha deve ser a chave da conta de armazenamento. Outra opção para **uso líquido** é passar o nome da conta de armazenamento e a chave nos parâmetros de nome e senha de usuário do comando **usar líquido** .

Depois de seguir essas instruções, você pode receber a seguinte mensagem de erro: "1312 erro do sistema. Uma sessão de logon especificada não existe. Talvez ele já tenha sido finalizado"quando você executa **líquido usar** para a conta de serviço de rede do sistema. Se isso acontecer, verifique se o nome de usuário que é passado para **uso líquido** inclui informações de domínio (por exemplo: "[nome de conta de armazenamento]. file.core.windows .net").

## <a name="linux-client-problems"></a>Problemas de cliente Linux

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Você estiver copiando um arquivo para um destino que não dá suporte à criptografia"

#### <a name="cause"></a>Causa

Arquivos criptografados pelo BitLocker podem ser copiados para arquivos do Azure. No entanto, o armazenamento de arquivo não suporta NTFS EFS. Portanto, você provavelmente está usando EFS nesse caso. Se você tiver arquivos que são criptografados por meio de EFS, uma operação de cópia para o armazenamento de arquivo pode falhar, a menos que o comando Copiar é descriptografar um arquivo copiado.

#### <a name="workaround"></a>Solução alternativa

Para copiar um arquivo para o armazenamento de arquivos, você deverá primeiro descriptografá-lo. Você pode fazer isso usando um dos seguintes métodos:

• Use **/d de cópia**.

• Definir a seguinte chave do registro:

- Caminho = HKLM\Software\Policies\Microsoft\Windows\System
- Tipo de valor = DWORD
- Nome = CopyFileAllowDecryptedRemoteDestination
- Valor = 1

Entretanto, observe que a chave do registro a configuração afeta todas as operações de cópia para compartilhamentos de rede.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Compartilha o erro "Host está inoperante" no arquivo existente, ou o shell trava quando você executar comandos de lista no ponto de montagem

#### <a name="cause"></a>Causa

Esse erro ocorre no cliente Linux quando o cliente ficou inativo por um longo período de tempo. Quando este erro ocorre, o cliente se desconecta e a conexão de cliente atinge o tempo limite.

#### <a name="solution"></a>Solução

Agora, esse problema é corrigido no núcleo Linux como parte do [conjunto de alterações](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), backport pendente em distribuição Linux.

Works alternativa para esse problema, sustentar a conexão e evite entrar em um estado ocioso, manter um arquivo no compartilhamento de arquivo do Azure que você escreve para periodicamente. Isso deve ser uma operação de gravação, como reconfiguração a data da modificação/criado no arquivo. Caso contrário, você poderá obter resultados em cache e a operação não pode disparar a conexão.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>"Montar erro 115" quando você tenta montar arquivos Azure na VM Linux

#### <a name="cause"></a>Causa

Distribuições Linux ainda não suporta o recurso de criptografia no SMB 3.0. Em algumas distribuições, o usuário pode receber uma mensagem de erro "115" se eles tentarem arquivos de montagem Azure usando SMB 3.0 devido a um recurso ausente.

#### <a name="solution"></a>Solução

Se o cliente de Linux SMB usado não dá suporte à criptografia, montagem Azure arquivos usando 2.1 SMB de uma VM Linux no mesmo data center como a conta de armazenamento de arquivos.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>Linux VM enfrentando atrasos aleatórios na comandos como "ls"

#### <a name="cause"></a>Causa

Isso pode ocorrer quando o comando de montagem não inclui a opção de **serverino** . Sem **serverino**, o comando ls executa um **stat** em cada arquivo.

#### <a name="solution"></a>Solução

Verificar a **serverino** em sua entrada "/etc/fstab":

azureuser.File.Core.Windows.NET/WMS/comer no/home/sampledir tipo cifs (rw, nodev, relatime, vers = 2.1, sec = ntlmssp, cache = username strict, = xxx, domínio = X, file_mode = 0755, dir_mode = 0755, serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Se a opção **serverino** não estiver presente, desmontar e montar arquivos do Azure novamente fazendo com que a opção **serverino** selecionada.

## <a name="learn-more"></a>Saiba Mais

- [Introdução ao armazenamento de arquivos do Azure no Windows](storage-dotnet-how-to-use-files.md)

- [Introdução ao armazenamento de arquivos do Azure no Linux](storage-how-to-use-files-linux.md)
