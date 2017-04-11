<properties
    pageTitle="Perguntas Frequentes do Azure autenticação multifator"
    description="Fornece uma lista de perguntas frequentes e respostas relacionadas a autenticação multifator do Azure. Autenticação multifator é um método de verificação da identidade de um usuário que requer mais de um nome de usuário e senha. Ele fornece uma camada adicional de segurança para o acesso do usuário e transações."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Perguntas Frequentes do Azure autenticação multifator


Estas perguntas Frequentes respondem dúvidas comuns sobre autenticação multifator do Azure e usando o serviço de autenticação multifator, incluindo perguntas sobre o modelo de cobrança e usabilidade.

## <a name="general"></a>Geral

**P: como o servidor de autenticação multifator Azure lidar com dados de usuário?**

Com o servidor de autenticação multifator, dados de usuário são armazenados apenas em servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário realiza verificação em duas etapas, o servidor de autenticação multifator envia dados para o serviço de nuvem do Azure multi-Factor Authentication para autenticação. A comunicação entre o servidor de autenticação multifator e o serviço de nuvem de autenticação multifator utiliza Secure Sockets Layer (SSL) ou segurança de camada de transporte (TLS) pela porta 443 saída.

Quando as solicitações de autenticação são enviadas para o serviço de nuvem, dados são coletados para autenticação e o uso de relatórios. Campos de dados incluídos em logs de verificação em duas etapas são da seguinte maneira:

- **ID exclusiva** (o nome ou local multifator autenticação servidor ID de usuário)
- **Nome e sobrenome** (opcional)
- **Endereço de email** (opcional)
- **Número de telefone** (quando usar uma chamada de voz ou autenticação de SMS)
- **Símbolo de dispositivo** (quando estiver usando a autenticação do aplicativo móvel)
- **Modo de autenticação**
- **Resultado de autenticação**
- **Nome do servidor de autenticação multifator**
- **Servidor de autenticação multifator IP**
- **Cliente IP** (se disponível)

Os campos opcionais podem ser configurados no servidor de autenticação multifator.

O resultado de verificação (sucesso ou negação) e o motivo se ele foi negado, é armazenada com os dados de autenticação e está disponíveis nos relatórios de autenticação e uso.


## <a name="billing"></a>Cobrança

A maioria das perguntas de cobrança pode ser respondidas fazendo referência à [página de preços de autenticação multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**P: minha organização cobrada telefonemas ou mensagens de texto usada para autenticar meus usuários?**

Organizações não são cobradas texto ou individuais telefonemas colocadas mensagens enviadas aos usuários por meio de autenticação multifator do Azure. Proprietários de telefone poderão ser cobrados da telefonemas ou mensagens de texto que eles recebam, de acordo com o seu serviço de telefone pessoal.

**P: faz a carga de modelo de cobrança por usuário com base no número de usuários que são configuradas para usar a autenticação multifator ou o número de usuários que executar verificações?**

Cobrança baseia-se no número de usuários configurados para usar autenticação multifator.

**P: como funciona a cobrança de autenticação multifator?**

Quando você usa o "por usuário" ou "por autenticação" MFA modelo, Azure é um recurso baseado em consumo. Todos os encargos serão cobrados à assinatura do Azure da organização como máquinas virtuais, sites, etc.

Quando você usa o modelo de licença, licenças de autenticação multifator de Azure são adquiridas e atribuídas aos usuários, assim como para Office 365 e outros produtos de assinatura.

**P: existe uma versão gratuita do Azure multi-Factor Authentication para administradores?**

Em alguns casos, Sim. Autenticação multifator para administradores do Azure oferece um subconjunto dos recursos do Azure MFA sem nenhum custo. Esta oferta aplica-se aos membros do grupo de administradores globais do Azure em instâncias do Active Directory do Azure que não estão vinculadas a um provedor de autenticação multifator de Azure baseado em consumo. Usando um provedor de autenticação multifator atualiza todos os administradores e usuários no diretório que estão configurados para usar autenticação multifator para a versão completa do Azure multi-Factor Authentication.

**P: existe uma versão gratuita do Azure multi-Factor Authentication para usuários do Office 365?**

Em alguns casos, Sim. Autenticação multifator para o Office 365 oferece um subconjunto dos recursos do Azure MFA sem nenhum custo. Esta oferta aplica-se aos usuários que têm uma licença do Office 365 atribuída, quando um provedor de autenticação multifator de Azure baseado em consumo não foi vinculado a instância correspondente do Active Directory do Azure. Usando o provedor de autenticação multifator atualiza todos os administradores e usuários no diretório que estão configurados para usar autenticação multifator para a versão completa do Azure multi-Factor Authentication.

**P: minha organização alternar entre modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?**

Sua organização escolhe um modelo de cobrança quando cria um recurso. Você não pode alterar um modelo de cobrança após o recurso está provisionado. No entanto, você pode criar outro recurso de autenticação multifator para substituir o original. Configurações de usuário e opções de configuração não podem ser transferidas para o novo recurso.

**P: minha organização alternar entre a cobrança de consumo e o modelo de licença a qualquer momento?**

Quando licenças são adicionadas a uma pasta que já tenha um provedor de autenticação multifator de Azure por usuário, baseada em consumo de cobrança é diminuída pelo número de licenças de propriedade. Se todos os usuários configurados para usar autenticação multifator tiverem licenças atribuídas, o administrador pode excluir o provedor de autenticação multifator de Azure.

Você não pode misturar cobrança de consumo por autenticação com um modelo de licença. Quando um provedor de autenticação multifator por autenticação está vinculado a um diretório, a organização é cobrada por todas as solicitações de verificação de autenticação multifator, independentemente de quaisquer licenças de propriedade.

**P: minha organização tem usar e sincronizar identidades para usar a autenticação multifator Azure?**

Quando uma organização usa um modelo de cobrança baseado em consumo, Azure Active Directory não é necessário. A vinculação de um provedor de autenticação multifator para um diretório é opcional. Se sua organização não está vinculada a um diretório, ele pode implantar o servidor de autenticação multifator Azure ou o SDK de autenticação multifator Azure locais.

Active Directory do Azure é necessário para o modelo de licença porque licenças são adicionadas ao diretório quando você compra e atribuí-las a usuários no diretório.


## <a name="usability"></a>Usabilidade

**P: o que um usuário faz se eles não recebem uma resposta em seu telefone, ou se o telefone não está disponível para o usuário?**

Se o usuário tiver configurado um telefone de backup, eles devem tente novamente e selecione esse telefone quando solicitado na página de entrada. Se o usuário não tiver outro método configurado, o administrador da organização pode atualizar o número atribuído para o telefone do usuário principal.


**P: o que o administrador faz se um usuário entra em contato com o administrador sobre uma conta de usuário não pode mais acessar?**

O administrador pode redefinir a conta do usuário solicitando que percorrer o processo de registro novamente. Saiba mais sobre [gerenciamento de usuário e configurações do dispositivo com o Azure multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: o que um administrador faz se telefone de um usuário que está usando senhas de aplicativo é perdido ou roubado?**

O administrador pode excluir senhas de aplicativos de todos os do usuário para impedir o acesso não autorizado. Após o usuário tiver um dispositivo de substituição, o usuário pode recriar as senhas. Saiba mais sobre [gerenciamento de usuário e configurações do dispositivo com o Azure multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: o que acontece se o usuário não consegue entrar no não-localizador aplicativos?**

Um usuário que está configurado para usar autenticação multifator requer uma senha de aplicativo entrar para alguns aplicativos não-localizador. Um usuário precisa limpar informações de entrada (excluir), reinicie o aplicativo e entrar usando sua senha do usuário nome e o aplicativo.

Obter mais informações sobre como criar senhas de aplicativos e outros [Ajuda com senhas de aplicativos](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Autenticação moderna para clientes do Office 2013
>
> Clientes do Office 2013 (incluindo Outlook) suportam novos protocolos de autenticação. Você pode configurar o Office 2013 para oferecer suporte à autenticação multifator. Depois de configurar o Office 2013, senhas de aplicativos não são necessárias para clientes do Office 2013. Para obter mais informações, consulte o [lançamento do Office 2013 autenticação moderno demonstração pública](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: o que um usuário faz se o usuário não recebe uma mensagem de texto, ou se o usuário responde a uma mensagem de texto bidirecional, mas a verificação atinge o tempo limite?**

Entregar de mensagens de texto e não há garantia de recebimento de respostas no SMS bidirecional porque há incontroláveis fatores que podem afetar a confiabilidade do serviço. Esses fatores incluem o país de destino, a operadora de celular e o sinal.

Os usuários que tenha dificuldade confiavelmente receber mensagens de texto devem selecionar o método de aplicativo ou chamada telefônica móvel em vez disso. O aplicativo móvel pode receber notificações tanto em conexões de Wi-Fi e celulares. Além disso, o aplicativo móvel pode gerar códigos de verificação, mesmo quando o dispositivo não tem nenhum sinal de forma alguma. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se você deve usar mensagens de texto, é recomendável usar o SMS unidirecionais em vez de SMS bidirecional quando possível. SMS unidirecionais é mais confiável e impede que usuários cobrado global SMS de responder a uma mensagem de texto que foi enviada de outro país.


**P: é possível usar tokens de hardware com o servidor de autenticação multifator Azure?**

Se você estiver usando o servidor de autenticação multifator Azure, você pode importar tokens de senha única, baseada em tempo (TOTP) de autenticação aberta (JURAMENTO) de terceiros e usá-las para verificação em duas etapas.

Você pode usar os tokens de ActiveIdentity que são tokens JURAMENTO TOTP se você colocar o arquivo de chave secreto em um arquivo CSV e importar para o servidor de autenticação multifator do Azure. Você pode usar os tokens JURAMENTO com Active Directory Federation Services (ADFS), Remote Authentication Dial-In User Service (RADIUS) quando o sistema do cliente pode processar as respostas de desafio de acesso e autenticação baseada em formulários do servidor de informações da Internet (IIS).

Você pode importar tokens de TOTP JURAMENTO de terceiros com os seguintes formatos:  
- Portátil contêiner de chave simétrica (PSKC)  
- CSV se o arquivo contém um número de série, uma chave secreta no formato de 32 de Base e um intervalo de tempo  

**P: é possível usar o servidor de autenticação multifator Azure proteger serviços de Terminal?**

Sim, mas, se você estiver usando o Windows Server 2012 R2 ou mais tarde, somente por usando o Gateway de área de trabalho remota (RD Gateway).

Alterações de segurança no Windows Server 2012 R2 alterou a forma como o servidor de autenticação multifator Azure se conecta o pacote de segurança de autoridade (Segurança Local) no Windows Server 2012 e versões anteriores. Para as versões dos serviços de Terminal no Windows Server 2012 ou anterior, você pode [proteger um aplicativo com autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se você estiver usando o Windows Server 2012 R2, é necessário Gateway RD.

**P: por que faria um usuário receber uma chamada de autenticação multifator de um chamador anônimo após configurar a ID do chamador?**

Quando chamadas de autenticação multifator são colocadas através da rede telefônica pública, às vezes, eles serão roteados por meio de uma operadora que não dá suporte a ID de chamador. Por isso, ID do chamador não é garantia, mesmo que o sistema de autenticação multifator sempre envia a ele.


## <a name="errors"></a>Erros

**P: o que os usuários devem fazer se eles vir uma mensagem de erro "solicitação de autenticação não é para uma conta ativada" ao usar notificações do aplicativo móvel?**

Pedir que elas siga este procedimento para remover suas contas no aplicativo móvel e, em seguida, adicioná-lo novamente:

1. Vá para [seu perfil de portal Azure](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta organizacional.
2. Selecione **verificação de segurança adicional**.
4. Remova a conta existente no aplicativo móvel.
5. Clique em **Configurar**e siga as instruções para reconfigurar o aplicativo móvel.


**P: o que os usuários devem fazer se eles vir uma mensagem de erro de 0x800434D4L ao entrar um aplicativo não do navegador?**

Atualmente, um usuário pode usar a verificação de segurança adicional somente com aplicativos e serviços que o usuário pode acessar através de um navegador. Aplicativos de navegador não (também chamados de *aplicativos de cliente avançado*) que são instalados em um computador local, como o Windows PowerShell, não funciona com contas que exigem verificação de segurança adicional. Nesse caso, o usuário pode ver o aplicativo gera um erro de 0x800434D4L.

Uma solução alternativa para isso é ter usuário separada contas para relacionados a administração e operações de não administradores. Posteriormente, você pode vincular caixas de correio entre sua conta de administrador e a conta não administrativa para que você pode entrar no Outlook utilizando sua conta não administradores. Para obter mais detalhes sobre isso, saiba como [dar um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Próximas etapas

Se sua dúvida não seja resolvida aqui, deixe-nos comentários na parte inferior da página. Ou, aqui estão algumas opções adicionais para obter ajuda:


**P: como posso obter ajuda com a autenticação multifator Azure?**

- Pesquise a [Base de Conhecimento de suporte do Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) para soluções para problemas técnicos comuns.

- Procurar e navegue perguntas e respostas da comunidade técnicas ou faça sua própria pergunta nos [fóruns do Active Directory do Azure](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Se você for um cliente de PhoneFactor herdado e você tiver dúvidas ou precisa de ajuda para redefinir uma senha, use o link de [redefinição de senha](mailto:phonefactorsupport@microsoft.com) para abrir um caso de suporte.

- Contate um profissional de suporte através de [suporte do servidor de autenticação multifator do Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, é útil se você pode incluir o máximo de informações sobre seu problema possível. Você pode fornecer as informações incluem a página onde você viu o erro, o código de erro específico, a ID de sessão específica e a ID do usuário que viu o erro.
