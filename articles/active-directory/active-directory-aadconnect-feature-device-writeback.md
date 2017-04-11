<properties
    pageTitle="Azure AD Connect: Ativando o dispositivo write-back | Microsoft Azure"
    description="Este documento detalha como habilitar o dispositivo write-back usando o Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Write-back de dispositivo de habilitando

>[AZURE.NOTE] Uma assinatura do Azure AD Premium é exigida para write-back do dispositivo.

A documentação a seguir fornece informações sobre como habilitar o recurso de write-back de dispositivo do Azure AD Connect. Dispositivo write-back é usado nas seguintes situações:

- Habilitar acesso condicional com base nos dispositivos para ADFS (2012 R2 ou superior) protegido por aplicativos (confiante com a relações de confiança de terceiros).

Isso fornece segurança adicional e garantia de que o acesso aos aplicativos é concedido somente a dispositivos confiáveis. Para obter mais informações sobre acesso condicional, consulte [Gerenciamento de riscos com acesso condicional](active-directory-conditional-access.md) e [Configurando o acesso de condicional local usando o registro de dispositivos do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Dispositivos devem estar localizados na mesma floresta que os usuários. Como dispositivos devem ser escritos de volta para uma única floresta, esse recurso não suportamos uma implantação com várias florestas de usuário.</li>
<li>Objeto de configuração do dispositivo apenas um registro pode ser adicionado a floresta do Active Directory local. Este recurso não é compatível com uma topologia onde o Active Directory local é sincronizado com vários diretórios do Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar Azure AD conectar
1. Instale o Azure AD Connect usando personalizada ou configurações expressas. Recomendamos a iniciar com todos os usuários e grupos sincronizada com êxito antes de habilitar write-back do dispositivo.

## <a name="part-2-prepare-active-directory"></a>Parte 2: Preparar o Active Directory
Use as seguintes etapas para se preparar para usando write-back do dispositivo.

1.  Na máquina onde o Azure AD Connect está instalado, inicie o PowerShell no modo elevado.

2.  Se o módulo do PowerShell do Active Directory não estiver instalado, instalá-lo usando o seguinte comando:

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Se o módulo Azure Active Directory PowerShell não estiver instalado, baixe e instale-o do [Azure Active Directory módulo para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Este componente tem uma dependência no Assistente de entrada, que é instalado com o Azure AD Connect.

4.  Com credenciais de administrador de empresa, execute os seguintes comandos e depois saia do PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Credenciais de administrador corporativo são necessárias, já que as alterações ao namespace configuração são necessários. Um administrador de domínio não terá permissões suficientes.

![PowerShell para habilitar write-back do dispositivo](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Descrição:

- Se não existir, cria e configura novos contêineres e objetos em CN = Configuração de registro do dispositivo, CN = Services, CN = configuração, [floresta-dn].
- Se não existir, cria e configura novos contêineres e objetos sob CN = RegisteredDevices, [domínio-dn]. Objetos de dispositivo serão criados neste contêiner.
- Define as permissões necessárias na conta do Azure AD conector, para gerenciar dispositivos do Active Directory.
- Só precisa executar em uma floresta, mesmo se Azure AD Connect está sendo instalado em várias florestas.

Parâmetros:

- Nome_do_domínio: Domínio do Active Directory onde objetos de dispositivo serão criados. Observação: Todos os dispositivos para uma determinada floresta do Active Directory serão criados em um único domínio.
- AdConnectorAccount: Contas do Active Directory que serão usada por Azure AD Connect para gerenciar objetos no diretório. Esta é a conta usada pela sincronização do Azure AD Connect para se conectar ao AD. Se você instalou usando configurações expressas, ele é a conta prefixada MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Parte 3: Ativar dispositivo write-back no Azure AD Connect
Use o procedimento a seguir para habilitar write-back de dispositivo no Azure AD Connect.

1.  Execute o Assistente de instalação novamente. Selecione **Personalizar as opções de sincronização** da página tarefas adicionais e clique em **Avançar**.
![Instalação personalizada personalizar as opções de sincronização](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  Na página recursos opcionais, Write-back dispositivo ficará não está mais esmaecido. Por favor, observe que se o Azure AD Connect etapas preparatório não são concluídos dispositivo write-back estará esmaecido check-out na página recursos opcionais. Marque a caixa do dispositivo write-back e clique em **Avançar**. Se a caixa de seleção ainda estiver desabilitada, consulte a [seção de solução de problemas](#the-writeback-checkbox-is-still-disabled).
![Recursos opcionais do dispositivo write-back de instalação personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  Na página de write-back, você verá o domínio fornecido como a floresta de write-back de dispositivo padrão.
![Personalizado floresta de destino write-back de instalação de dispositivo](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Conclua a instalação do assistente sem alterações de configuração adicionais. Se necessário, consulte [instalação personalizada do Azure AD Connect.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Habilitar acesso condicional
Instruções detalhadas para habilitar esse cenário estão disponíveis em [Configurar o acesso condicional local usando o registro de dispositivos do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verificar que dispositivos são sincronizados com o Active Directory
Dispositivo write-back agora deve estar funcionando adequadamente. Lembre-se de que ele pode levar até 3 horas para objetos de dispositivo sejam gravados-back para o AD.  Para verificar se seus dispositivos estão sendo sincronizados corretamente, faça o seguinte após concluir as regras de sincronização:

1.  Abra o Centro Administrativo do Active Directory.
2.  Expanda RegisteredDevices, dentro do domínio que está sendo federado.
![Central de administração do Active Directory registrado dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Dispositivos registrados atuais serão listados lá.
![Central de administração do Active Directory registrados lista dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Solução de problemas

### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de seleção de write-back ainda está desativada
Se a caixa de seleção para write-back do dispositivo não estiver habilitada, apesar de ter seguido as etapas acima, as etapas a seguir orientará você o que está verificando o Assistente de instalação antes da caixa está habilitada.

Primeiras coisas primeiro:

- Verifique se pelo menos uma floresta tem 2012R2 do Windows Server. O tipo de objeto do dispositivo deve estar presente.
- Se já estiver executando o Assistente de instalação, em seguida, as alterações não serão detectadas. Nesse caso, conclua o Assistente de instalação e executá-la novamente.
- Verifique se a conta que você fornecer no script de inicialização é realmente o usuário correto usado pelo conector do Active Directory. Para verificar isso, siga estas etapas:
    - No menu Iniciar, abra o **Serviço de sincronização**.
    - Abra a guia de **conectores** .
    - Localize o conector com tipo de serviços de domínio Active Directory e selecioná-lo.
    - Em **ações**, selecione **Propriedades**.
    - Vá para **se conectar à floresta do Active Directory**. Verifique se o nome de usuário e domínio especificado nessa correspondência de tela a conta fornecida para o script.
![Conta do conector no Gerenciador de serviço de sincronização](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Verificar a configuração do Active Directory:
- Verifique se o serviço de registro do dispositivo está localizado no local abaixo (CN = DeviceRegistrationService, CN = Serviços de registro do dispositivo, CN = Configuração de registro do dispositivo, CN = Services, CN = Configuration) em contexto de nomenclatura de configuração.

![Solucionar problemas, DeviceRegistrationService no namespace de configuração](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Verifique se há apenas um objeto de configuração pesquisando o namespace de configuração. Se houver mais de um, exclua a cópia.

![Solucionar problemas, procure os objetos duplicados](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- No objeto do serviço de registro do dispositivo, verifique se o atributo msDS-DeviceLocation estiver presente e tem um valor. Pesquisa neste local e verifique se ele está presente com o objectType msDS-DeviceContainer.

![Solucionar problemas, msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Solução de problemas, a classe de objeto do RegisteredDevices](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Verifique se a conta usada do Active Directory Connector tem permissões necessárias no contêiner dispositivos registrados encontrado pela etapa anterior. Este é as esperado permissões neste contêiner:

![Solucionar problemas, verifique se as permissões no contêiner](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Verifique se a conta do Active Directory tem permissões em CN = Configuração de registro do dispositivo, CN = Services, CN = objeto de configuração.

![Solucionar problemas, verifique se as permissões na configuração de registro do dispositivo](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informações adicionais
- [Gerenciamento de riscos com acesso condicional](active-directory-conditional-access.md)
- [Configuração de acesso condicional local usando o registro de dispositivos do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
