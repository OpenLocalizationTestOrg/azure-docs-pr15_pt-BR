<properties
    pageTitle="Implantar o Azure pilha VDC | Microsoft Azure"
    description="Saiba como preparar o Azure pilha VDC e execute o script do PowerShell para implantar Azure pilha VDC."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Implantar o Azure pilha VDC
Para implantar o Azure pilha VDC, primeiro é necessário para [preparar o computador de implantação](#prepare-the-deployment-machine) e, em seguida, [execute o script de implantação do PowerShell](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Baixe e extraia Microsoft Azure pilha VDC TP2

Antes de começar, verifique se você pelo menos 85 GB de espaço.

1. O download do Azure pilha VDC TP2 é composto de um arquivo zip que contém os seguintes 12 arquivos, totalizando ~ 20 GB:
    - 1 MicrosoftAzureStackPOC.EXE
2. Examine as informações do Assistente de extração automática e a tela de contrato de licença e clique em **Avançar**.
3. Examine as informações do Assistente de extração automática e a tela de declaração de privacidade e clique em **Avançar**.
4. Selecione o destino para os arquivos extraídos, clique em **Avançar**.
    - O padrão é: <drive letter>:\<pasta atual > \Microsoft Azure pilha VDC
5. Examine as informações do Assistente de extração automática e tela do local de destino e clique em **extrair** para extrair os arquivos de ThirdPartyLicenses.rtf e CloudBuilder.vhdx (~44.5 GB).

> [AZURE.NOTE] Após extrair os arquivos, você pode excluir o arquivo zip para recuperar espaço na máquina. Ou, você pode mover o arquivo zip para outro local para que se precisar reimplantar você não precisa baixar os arquivos zip novamente.

## <a name="prepare-the-deployment-machine"></a>Preparar o computador de implantação

1. Certifique-se de que você pode física conectar à máquina implantação ou ter acesso de console físico (como KVM). Você precisará desse acesso após a reinicialização da máquina de implantação na etapa 9.

2. Verifique se que a máquina de implantação atende aos [requisitos mínimos](azure-stack-deploy.md). Você pode usar o [Verificador de implantação do Azure pilha Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar suas necessidades.

3. Faça logon como Administrador Local à sua máquina VDC.

4. Copie o arquivo CloudBuilder.vhdx C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Se você optar por não usar o script recomendado para preparar o computador de host VDC (etapas 5 – etapa 7), não insira qualquer tecla de licença na página de ativação. Uma versão de avaliação do Windows Server 2016 imagem está incluída e inserir uma chave de licença causa mensagens de aviso de expiração.

5. Na máquina VDC, execute o seguinte script do PowerShell para baixar os arquivos de suporte do Azure pilha TP2:

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Esse script baixa os arquivos de suporte do Azure pilha TP2 para a pasta especificada pelo parâmetro $LocalPath.
    
6. Abrir um console do PowerShell elevado e altere o diretório para onde você copiou os arquivos.
    - 11 MicrosoftAzureStackPOC-N.BIN (onde N é 1-11)
7. Clique com botão direito no MicrosoftAzureStackPOC.EXE > Executar como administrador.

8. Execute o script PrepareBootFromVHD.ps1. Esse script e os arquivos de autônomo estão disponíveis com os outros scripts de suporte fornecidos juntamente com esta compilação.
    Há cinco parâmetros para este script do PowerShell:
    - CloudBuilderDiskPath (obrigatório) – o caminho para o CloudBuilder.vhdx do host.
    - DriverPath (opcional) – permite que você adicione drivers adicionais para o host a HD virtual.
    - ApplyUnattend (opcional) – Especifique esse parâmetro de alternar para automatizar a configuração do sistema operacional. Se especificado, o usuário deve fornecer AdminPassword para configurar o sistema operacional na inicialização (requer desde que acompanha unattend_NoKVM.xml de arquivo).
    Se você não usar esse parâmetro, o arquivo Unattend genérico é usado sem personalização adicional. Você precisará KVM personalização completa após a reinicialização do-lo.
    - (Opcional) – AdminPassword usado somente quando o parâmetro ApplyUnattend estiver definido, exige um mínimo de seis caracteres.
    - VHDLanguage (opcional) – Especifica o idioma VHD, padronizado "en-US".
    O script está documentado e contém exemplo de uso, embora o uso mais comum é:
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Se você executar este comando exato, você deve digitar AdminPassword no prompt.

9. Quando o script for concluído, você deverá confirmar a reinicialização. Se houver outros usuários conectados, esse comando falhará. Se o comando falhar, execute o seguinte comando:`Restart-Computer -force` 

10. A reinicialização no SO do CloudBuilder.vhdx, onde a implantação continua.

> [AZURE.IMPORTANT] Pilha Azure requer acesso à Internet, diretamente ou através de um proxy transparente. Implantação TP2 VDC suporta exatamente um NIC para uma rede. Se você tiver várias placas de rede, verifique se somente um está habilitado (e todos os outros estão desabilitados) antes de executar o script de implantação na próxima seção.

## <a name="run-the-powershell-deployment-script"></a>Execute o script de implantação do PowerShell

1. Faça logon como Administrador Local à sua máquina VDC. Use as credenciais especificadas nas etapas anteriores.

2. Abra um console elevado do PowerShell.

3. No PowerShell, execute este comando:`cd C:\CloudDeployment\Configuration`

4. Execute o comando implantar:`.\InstallAzureStackPOC.ps1`

5. No prompt de **Digite a senha** , digite uma senha e confirme-lo. Esta é a senha para todas as máquinas virtuais. Certifique-se de gravá-lo.

6. Digite as credenciais da sua conta do Active Directory do Azure. Esse usuário deve ser o Administrador Global no locatário de diretório.

7. O processo de implantação pode levar algumas horas, durante o qual o sistema automaticamente reinicializar uma vez.

    > [AZURE.IMPORTANT] Se você quiser monitorar o progresso de implantação, entre no como azurestack\AzureStackAdmin. Se você entrar como um administrador local depois que o computador está associado ao domínio, você não verá o progresso de implantação. Não execute novamente a implantação, em vez disso, entrar como azurestack\AzureStackAdmin para validar que ele seja executado.

    Quando a implantação estiver concluído, console do PowerShell exibe: **concluída: ação 'Implantação'**.

    Se a implantação falhar, você pode tentar [executá-la novamente](azure-stack-rerun-deploy.md). Ou você pode [reimplantá](azure-stack-redeploy.md) -lo do zero.

### <a name="deployment-script-examples"></a>Exemplos de script de implantação

Se sua identidade AAD só está associado a um diretório de AAD:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Se sua identidade AAD está associada a maior do que um AAD diretório:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Se seu ambiente não tiverem DHCP ativado, você deve incluir os seguintes parâmetros adicionais para uma das opções acima (uso de exemplo fornecidos):

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>Parâmetros opcionais InstallAzureStackPOC.ps1

| Parâmetro | Obrigatório/opcional | Descrição |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Opcional | Define o nome de usuário do Active Directory do Azure e a senha. Essas credenciais Azure podem ser um ID da organização ou uma Account da Microsoft. Para usar credenciais de Account da Microsoft, omita esse parâmetro no cmdlet. Omitir esse parâmetro avisos pop-up do Azure autenticação durante a implantação. Isso cria os tokens de autenticação e atualização usados durante a implantação. |
| AADDirectoryTenantName | Necessário | Define o diretório de locatário. Use este parâmetro para especificar um diretório específico onde a conta AAD tem permissões para gerenciar vários diretórios. Nome de um locatário de diretório AAD no formato de completo <directoryName>. onmicrosoft.com. |
| AdminPassword | Necessário | Define a conta de administrador local e todas as outras contas de usuário em todas as máquinas virtuais criadas como parte da implantação de VDC. Essa senha deve corresponder a senha de administrador local atual do host. |
| AzureEnvironment | Opcional | Selecione o ambiente do Azure com o qual você deseja registrar desta implantação do Azure pilha. Opções incluem *Azure público*, *Azure - China*, *Azure - governo dos EUA*. |
| EnvironmentDNS | Opcional | Um servidor DNS é criado como parte da implantação do Azure pilha. Para permitir que os computadores dentro da solução para resolver nomes fora do carimbo, forneça seu servidor DNS de infraestrutura existente. O servidor DNS no carimbo encaminha solicitações de resolução de nome desconhecido para este servidor. |
| NatIPv4Address | Necessário para o suporte de DHCP NAT | Define um endereço IP estático para MAS-BGPNAT01. Use somente este parâmetro se o DHCP não pode atribuir um endereço IP válido para acessar a Internet. |
| NatIPv4DefaultGateway | Necessário para o suporte de DHCP NAT | Define o gateway padrão usado com o endereço IP estático para MAS-BGPNAT01. Use somente este parâmetro se o DHCP não pode atribuir um endereço IP válido para acessar a Internet.  |
| NatIPv4Subnet | Necessário para o suporte de DHCP NAT | Prefixo de sub-rede IP usado para DHCP sobre suporte NAT. Use somente este parâmetro se o DHCP não pode atribuir um endereço IP válido para acessar a Internet.  |
| PublicVLan | Opcional | Define o ID de VLAN. Use somente este parâmetro se o host e BGPNAT01 MAS devem configurar ID de VLAN para acessar a rede física (e Internet). Por exemplo,`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Executar novamente | Opcional | Use este sinalizador reexecutar implantação.  Todas as entradas anteriores é usada. Inserir novamente dados fornecidos anteriormente não são suportados porque vários valores exclusivos são gerados e usados para implantação. |
| TimeServer | Opcional | Use esse parâmetro se for necessário especificar um servidor de tempo específico. |

## <a name="next-steps"></a>Próximas etapas

[Conectar-se a pilha Azure](azure-stack-connect-azure-stack.md)
