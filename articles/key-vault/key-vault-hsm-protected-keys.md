<properties
    pageTitle="Como gerar e transferir protegidos por HSM chaves do Azure chave cofre | Microsoft Azure"
    description="Utilize este artigo para ajudá-lo a planejar, gerar e transferi-suas próprias teclas protegidos por HSM para usar com o Azure chave cofre."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Como gerar e transferir protegidos por HSM chaves para Cofre de chave do Azure

##<a name="introduction"></a>Introdução

Para garantia extra, quando você usa o Azure chave cofre, você pode importar ou gerar chaves em módulos de segurança de hardware (HSMs) que nunca deixe o limite HSM. Este cenário é conhecido como *Trazer sua própria chave*ou BYOK. Os HSMs são FIPS 140-2 nível 2 validados. Azure Cofre de chave usa família de nShield Thales de HSMs para proteger suas chaves.

Use as informações neste tópico para ajudá-lo a planejar, gerar e transferi-suas próprias teclas protegidos por HSM para usar com o Azure chave cofre.

Esta funcionalidade não está disponível para China do Azure. 

>[AZURE.NOTE] Para obter mais informações sobre Cofre de chave do Azure, consulte [o que é Azure chave cofre?](key-vault-whatis.md)  
>
>Para um tutorial de Introdução ao obter, que inclui a criação de um cofre chave para chaves protegidas por HSM, consulte [Introdução ao Azure chave cofre](key-vault-get-started.md).

Mais informações sobre gerando e transferindo uma tecla protegidos por HSM pela Internet:

- Gerar a chave de uma estação de trabalho offline, o que reduz a superfície de ataque.

- A chave é criptografada com uma chave Exchange chave KEK (), que fica criptografada até que ele é transferido para o HSMs do Azure chave cofre. Somente a versão criptografada da chave deixa a estação de trabalho original.

- O conjunto de ferramentas define propriedades na sua chave de locatário que vincula sua chave para o mundo de segurança do Azure chave cofre. Portanto após o HSMs do Azure chave cofre receber e descriptografar sua chave, apenas essas HSMs podem usá-lo. A chave não pode ser exportada. Essa ligação é imposta pelos HSMs Thales.

- A chave do Exchange chave KEK que é usado para criptografar a chave é gerado dentro os HSMs de Cofre de chave do Azure e não é exportável. Os HSMs impõem que não pode haver nenhuma versão limpar do KEK fora os HSMs. Além disso, o conjunto de ferramentas inclui certificação de Thales que o KEK não é exportável e foi gerado dentro de um HSM original que foi fabricado por Thales.

- O conjunto de ferramentas inclui certificação de Thales que o mundo de segurança do Azure chave cofre também foi gerado em um HSM genuine fabricado por Thales. Essa certificação prova a você que a Microsoft está usando hardware original.

- A Microsoft usa KEKs separadas e separar mundo de segurança em cada área geográfica. Essa separação garante que a chave pode ser usada somente em data centers na região na qual você criptografados. Por exemplo, uma chave de um cliente Europeu não pode ser usada em centros de dados na América do Norte ou na Ásia.

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Obter mais informações sobre os serviços Thales HSMs e Microsoft

Segurança Thales eletrônica é um fornecedor global líder de criptografia de dados e soluções de segurança virtuais para os serviços financeiros, alta tecnologia, fabricação, governo e tecnologia setores. Com um ano de 40 histórico de proteger corporativo e informações do governo, soluções de Thales são usadas pelo quatro ou cinco maiores energia e aeroespacial empresas. Suas soluções também são usadas pelo 22 países NATO e seguro mais de 80 por cento das transações de pagamento em todo o mundo.

Microsoft tem vindo a colaborar com Thales para aprimorar o estado da arte para HSMs. Esses aprimoramentos permitem que você obtém os benefícios típicos de serviços hospedados sem abandonar o controle sobre suas chaves. Especificamente, esses aprimoramentos permitem Microsoft gerenciar os HSMs para que você não precisa. Como um serviço de nuvem, Azure chave cofre escalas sem aviso para atender aos picos de uso da sua organização. Ao mesmo tempo, sua chave estiver protegida dentro HSMs da Microsoft: você mantenha o controle sobre o ciclo de vida chave porque você gerar a chave e transferi-la para HSMs da Microsoft.

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementando trazer sua própria chave (BYOK) para Cofre de chave do Azure

Use as seguintes informações e procedimentos se você gerará sua própria chave protegidos por HSM e transferi-la ao Azure chave cofre — os trazer seu cenário de chave (BYOK).


##<a name="prerequisites-for-byok"></a>Pré-requisitos para BYOK

Consulte a tabela a seguir para obter uma lista de pré-requisitos para trazer sua própria chave (BYOK) para Azure chave cofre.

|Requisito|Mais informações|
|---|---|
|Uma assinatura do Azure|Para criar um cofre de chave do Azure, você precisa de uma assinatura do Azure: [inscrição para avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)|
|O nível de serviço do Azure chave cofre Premium para dar suporte a chaves protegidas por HSM|Para obter mais informações sobre os níveis de serviço e recursos para Cofre de chave do Azure, veja o site de [Preços do Azure chave cofre](https://azure.microsoft.com/pricing/details/key-vault/) .|
|Thales HSM, cartões inteligentes e software de suporte|Você deve ter acesso a um módulo de segurança de Hardware Thales e conhecimento operacional básico de Thales HSMs. Consulte [Thales módulo de segurança de Hardware](https://www.thales-esecurity.com/msrms/buy) para a lista de modelos compatíveis, ou para comprar um HSM se não tiver um.|
|Os seguintes hardware e software:<ol><li>Um x64 offline estação de trabalho com um sistema de operação mínima do Windows do Windows 7 e Thales software de nShield que tenha pelo menos versão 11.50.<br/><br/>Se esta estação de trabalho é executado o Windows 7, você deve [instalar o Microsoft .NET Framework 4,5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está conectada à Internet e tem um sistema de operação mínimo do Windows do Windows 7.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil com pelo menos 16 MB de espaço livre.</li></ol>|Por razões de segurança, recomendamos que a primeira estação não está conectada a uma rede. No entanto, essa recomendação não é imposta por programação.<br/><br/>Observe que nas instruções a seguir, esta estação de trabalho é considerada como a estação de trabalho desconectada.</p></blockquote><br/>Além disso, se sua chave de locatário destina-se uma rede de produção, recomendamos que você use uma segunda estação de trabalho separada para baixar o conjunto de ferramentas e carregue a chave de locatário. Mas, para fins de teste, você pode usar a mesma estação de trabalho que o primeiro.<br/><br/>Observe que nas instruções a seguir, essa segunda estação de trabalho é considerada como a estação de trabalho conectado à Internet.</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gerar e transferir sua chave para Azure chave cofre HSM

Você usará as cinco etapas seguintes para gerar e transferir sua chave para um HSM de cofre do Azure chave:

- [Etapa 1: Preparar sua estação de trabalho conectado à Internet](#step-1-prepare-your-internet-connected-workstation)
- [Etapa 2: Preparar sua estação de trabalho desconectada](#step-2-prepare-your-disconnected-workstation)
- [Etapa 3: Gerar sua chave](#step-3-generate-your-key)
- [Etapa 4: Preparar sua chave de transferência](#step-4-prepare-your-key-for-transfer)
- [Etapa 5: Transferir sua chave Cofre de chave do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Etapa 1: Preparar sua estação de trabalho conectado à Internet
Para esta primeira etapa, siga os procedimentos a seguir na estação de trabalho que está conectado à Internet.


###<a name="step-11-install-azure-powershell"></a>Etapa 1.1: Instalar o PowerShell Azure

A estação de trabalho conectado à Internet, baixe e instale o módulo do PowerShell do Azure que inclui os cmdlets para gerenciar Cofre de chave do Azure. Isso requer uma versão mínima do 0.8.13.

Para obter instruções de instalação, veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

###<a name="step-12-get-your-azure-subscription-id"></a>Etapa 1.2: Obter sua ID do Azure assinatura

Iniciar uma sessão do PowerShell do Azure e entrar em sua conta do Azure usando o seguinte comando:

        Add-AzureAccount
Na janela do navegador pop-up, insira seu nome de usuário da conta do Azure e senha. Em seguida, use o comando [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
A partir da saída, localize a ID da assinatura que você usará para Azure chave cofre. Posteriormente, você precisará este ID da assinatura.

Não feche a janela do PowerShell do Azure.

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Etapa 1.3: Baixar o conjunto de ferramentas BYOK para Cofre de chave do Azure

Vá para o Microsoft Download Center e [baixar o conjunto de ferramentas do Azure chave cofre BYOK](http://www.microsoft.com/download/details.aspx?id=45345) para sua região geográfica ou instância do Azure. Use as seguintes informações para identificar o nome do pacote para download e seu hash de pacote SHA-256 correspondente:

---

**América do Norte:**

States.zip KeyVault-BYOK-ferramentas-United

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**Europa:**

KeyVault-BYOK-ferramentas-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**Ásia:**

KeyVault-BYOK-ferramentas-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**América Latina:**

KeyVault-BYOK-ferramentas-LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**Japão:**

KeyVault-BYOK-ferramentas-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**Austrália:**

KeyVault-BYOK-ferramentas-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Governo Azure:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-ferramentas-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**Canadá:**

KeyVault-BYOK-ferramentas-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**Alemanha:**

KeyVault-BYOK-ferramentas-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**Índia:**

KeyVault-BYOK-ferramentas-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

Para validar a integridade do seu baixado BYOK conjunto de ferramentas, da sua sessão do PowerShell do Azure, use o cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

O conjunto de ferramentas inclui o seguinte:

- Um pacote de chave Exchange chave KEK () que tem um nome começando com **BYOK-KEK - pkg-.**
- Um pacote de segurança mundo que possui um nome começando com **BYOK-SecurityWorld - pkg-.**
- Um script de python chamado v**erifykeypackage.py.**
- Um linha de comando executável arquivo nomeado **KeyTransferRemote.exe** e DLLs associadas.
- Um pacote Visual C++ redistribuível, chamado **vcredist_x64.exe.**

Copie o pacote para uma unidade USB ou outro armazenamento portátil.

##<a name="step-2-prepare-your-disconnected-workstation"></a>Etapa 2: Preparar sua estação de trabalho desconectada

Para essa segunda etapa, siga os procedimentos a seguir na estação de trabalho que não está conectado a uma rede (Internet ou sua rede interna).


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Etapa 2.1: Preparar a estação de trabalho desconectada com Thales HSM

Instalar o software de suporte donCipher (Thales) em um computador com Windows e anexar um HSM Thales nesse computador.

Certifique-se de que as ferramentas de Thales estão no seu caminho (**%nfast_home%\bin** e **%nfast_home%\python\bin**). Por exemplo, digite o seguinte:

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Para obter mais informações, consulte o guia do usuário incluído com o HSM Thales.

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Etapa 2.2: Instalar o conjunto de ferramentas BYOK na estação de trabalho desconectada

Copie o pacote de conjunto de ferramentas BYOK da unidade USB ou outro armazenamento portátil e, em seguida, faça o seguinte:

1. Extrai os arquivos do pacote baixado para qualquer pasta.
2. A partir dessa pasta, execute vcredist_x64.exe.
3. Siga as instruções para instalar os componentes de tempo de execução do Visual C++ para Visual Studio 2013.

##<a name="step-3-generate-your-key"></a>Etapa 3: Gerar sua chave

Para a terceira etapa, siga os procedimentos a seguir na estação de trabalho desconectado.

###<a name="step-31-create-a-security-world"></a>Etapa 3.1: Criar um mundo de segurança

Inicie um prompt de comando e executar o programa de novo mundo Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa cria um arquivo de **Segurança mundial** em % NFAST_KMDATA%\local\world, que corresponde à pasta Data\local de gerenciamento de C:\ProgramData\nCipher\Key. Você pode usar valores diferentes para o quorum mas em nosso exemplo, você será solicitado a inserir três cartões em branco e pins para cada um deles. Em seguida, qualquer duas placas dar acesso completo para o mundo de segurança. Esses cartões se tornar o **Administrador cartão definido** para o novo mundo de segurança.

Em seguida, faça o seguinte:

- Fazer backup do arquivo do mundo. Proteger e proteger o arquivo do mundo, cartões de administrador e seus pins e certifique-se de que uma única pessoa não tem acesso a mais de um cartão.

###<a name="step-32-validate-the-downloaded-package"></a>Etapa 3,2: Validar o pacote baixado

Esta etapa é opcional, mas recomendados para que você pode validar o seguinte:

- A chave de troca de chave que está incluído no conjunto de ferramentas foi gerado de um HSM Thales original.
- O hash do mundo da segurança que está incluído no conjunto de ferramentas foi gerado em um HSM Thales original.
- A chave de troca de chave é não exportável.

>[AZURE.NOTE]Para validar o pacote baixado, o HSM deve estar conectado ligado e deve ter um mundo de segurança nele (como aquele que você acabou de criar).

Para validar o pacote baixado:

1.  Execute o script verifykeypackage.py ligando um destes procedimentos, dependendo de sua região geográfica ou instância do Azure:
    - América do Norte:

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - Para Europa:

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - Da Ásia:

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - Para a América Latina:

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - Para Japão:

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - Para a Austrália:

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - Para [Governo do Azure](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - Para o Canadá:

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - Para Alemanha:

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - Para a Índia:

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]O software de Thales inclui python em %NFAST_HOME%\python\bin

2.  Confirme que você veja a seguir, que indica a validação bem-sucedida: **resultado: sucesso**

Esse script valida a cadeia de assinante até a chave de raiz de Thales. O hash dessa chave raiz é incorporado no script e seu valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Você também pode confirmar esse valor separadamente visitando o [site Thales](http://www.thalesesec.com/).

Agora você está pronto para criar uma nova chave.

###<a name="step-33-create-a-new-key"></a>Etapa 3.3: Criar uma nova chave

Gere uma chave usando o programa de **generatekey** Thales.

Execute o seguinte comando para gerar a chave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando você executa este comando, use estas instruções:

- O parâmetro *proteger* deve ser definido como o valor **módulo**, conforme mostrado. Isso cria uma chave protegidos por módulo. O conjunto de ferramentas BYOK não oferece suporte para teclas protegidos por OCS.

- Substitua o valor de *contosokey* para a **identificação** e **plainname** com qualquer valor de cadeia de caracteres. Para minimizar despesas administrativas e reduzir o risco de erros, recomendamos que você use o mesmo valor para ambos. O valor de **identificação** deve conter apenas números, traços, letras maiusculas e minúsculas.

- O pubexp é deixado em branco (padrão) neste exemplo, mas você pode especificar valores específicos. Para obter mais informações, consulte a documentação de Thales.

Este comando cria um arquivo de chave indexado em sua pasta de %NFAST_KMDATA%\local com um nome começando com **key_simple_**, seguido de **identificação** que foi especificada no comando. Por exemplo: **key_simple_contosokey**. Este arquivo contém uma chave criptografada.

Fazer backup deste arquivo de chave indexado em um local seguro.

>[AZURE.IMPORTANT] Quando você transfere posteriormente sua chave Cofre de chave do Azure, o Microsoft não pode exportar esta tecla para você para que ele se torne muito importante que você faça backup de seu mundo de segurança e de chave com segurança. Contato Thales para obter orientações e práticas recomendadas para fazer backup de sua chave.

Agora você está pronto para transferir sua chave ao Azure chave cofre.

##<a name="step-4-prepare-your-key-for-transfer"></a>Etapa 4: Preparar sua chave de transferência

Para este quarta etapa, siga os procedimentos a seguir na estação de trabalho desconectado.

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Etapa 4.1: Criar uma cópia da sua chave com permissões reduzidas

Para reduzir as permissões em sua chave, em um prompt de comando, execute um destes procedimentos, dependendo de sua região geográfica ou instância do Azure:

- América do Norte:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Para Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Da Ásia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- Para a América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Para Japão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Para a Austrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Para [Governo do Azure](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- Para o Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- Para Alemanha:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- Para a Índia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


Quando você executar este comando, substitua *contosokey* com o mesmo valor que você especificou no **3.3 etapa: criar uma nova chave** da etapa [gerar sua chave](#step-3-generate-your-key) .

Você será solicitado a plug-in seus cartões de administração do mundo de segurança.

Quando o comando for concluída, você verá **resultado: sucesso** e a cópia da sua chave com permissões reduzidas estão no arquivo chamado key_xferacId_<contosokey>.

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>Etapa 4.2: Inspecionar a nova cópia da chave

Opcionalmente, execute o Thales utilitários para confirmar as permissões mínimas a nova chave:

- aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Quando você executa esses comandos, substitua contosokey com o mesmo valor que você especificou no **3.3 etapa: criar uma nova chave** da etapa [gerar sua chave](#step-3-generate-your-key) .

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Etapa 4.3: Criptografar sua chave usando a chave de troca de chave da Microsoft

Execute um dos seguintes comandos, dependendo de sua região geográfica ou instância do Azure:

- América do Norte:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Da Ásia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Japão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Austrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para [Governo do Azure](https://azure.microsoft.com/features/gov/), que usa a instância do governo dos EUA do Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para o Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Alemanha:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Índia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Quando você executa este comando, use estas instruções:

- Substituir *contosokey* com o identificador que você usou para gerar a chave no **3.3 etapa: criar uma nova chave** da etapa [gerar sua chave](#step-3-generate-your-key) .

- Substitua *SubscriptionID* com a ID da assinatura do Azure que contém seu cofre chave. Você recuperou esse valor anteriormente, no **1.2 etapa: obter sua ID do Azure assinatura** da etapa [preparar sua estação de trabalho conectado à Internet](#step-1-prepare-your-internet-connected-workstation) .

- Substitua *ContosoFirstHSMKey* por um rótulo que é usado para o nome do arquivo de saída.

Quando isso estiver concluído com êxito, ele exibe **resultado: sucesso** e há um novo arquivo na pasta atual que tem o seguinte nome: TransferPackage -*ContosoFirstHSMkey*.byok

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Etapa 4.4: Copiar seu pacote de transferência chave para a estação de trabalho conectado à Internet

Use uma unidade USB ou outro armazenamento portátil para copiar o arquivo de saída da etapa anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) para sua estação de trabalho conectado à Internet.

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>Etapa 5: Transferir sua chave Cofre de chave do Azure

Para essa etapa final, a estação de trabalho conectado à Internet, use o cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) para carregar o pacote de transferência chave que você copiou do desconectado estação de trabalho para o HSM de Cofre de chave do Azure:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se o carregamento for bem-sucedido, você verá exibidas as propriedades da chave que você acabou de adicionar.


##<a name="next-steps"></a>Próximas etapas

Agora você pode usar esta tecla protegidos por HSM em sua chave cofre. Para obter mais informações, consulte a seção **se você deseja usar um módulo de segurança de hardware (HSM)** do tutorial de [Introdução ao Azure chave cofre](key-vault-get-started.md) .
