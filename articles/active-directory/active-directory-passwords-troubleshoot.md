<properties
    pageTitle="Solução de problemas: Gerenciamento de senha do Azure AD | Microsoft Azure"
    description="Etapas de solução de problemas comuns para gerenciamento de senha do Azure AD, incluindo redefinir, alterar, Write-back, registro, e quais informações para incluir quando procurando ajuda."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Como solucionar problemas de gerenciamento de senha

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

Se você estiver tendo problemas com o gerenciamento de senha, estamos aqui para ajudar. A maioria dos problemas que pode ocorrer pode ser resolvido com algumas etapas de solução de problemas simples que você pode ler sobre abaixo para solucionar sua implantação:

* [**Informações a serem incluídas quando precisar de ajuda**](#information-to-include-when-you-need-help)
* [**Problemas com a configuração de gerenciamento de senha no Portal de gerenciamento do Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemas com relatórios de gerenciamento de senha no Portal de gerenciamento do Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemas com a senha redefinir Portal de registro**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemas com a senha redefinir Portal**](#troubleshoot-the-password-reset-portal)
* [**Problemas com write-back de senha**](#troubleshoot-password-writeback)
  - [Códigos de erro de log de eventos de write-back de senha](#password-writeback-event-log-error-codes)
  - [Problemas de conectividade de write-back de senha](#troubleshoot-password-writeback-connectivity)

Se você já tentou fazer as etapas de solução de problemas a seguir e ainda enfrentando problemas, você pode postar uma pergunta nos [fóruns do Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou contate o suporte e vamos dar uma olhada no seu problema assim que possível.

## <a name="information-to-include-when-you-need-help"></a>Informações a serem incluídas quando precisar de ajuda

Se você não consegue resolver o seu problema com a orientação abaixo, você pode contatar nossos engenheiros de suporte. Quando você contatá-los, é recomendável incluir as seguintes informações:

 - **Descrição geral do erro** – qual mensagem de erro exata fez o usuário consulte?  Caso não exista nenhuma mensagem de erro, descreva o comportamento inesperado notado, em detalhes.
 - **Página** – qual página você estava em quando você viu o erro (inclua a URL)?
 - **Data / tempo / fuso horário** – qual foi a precisa data e hora que você viu o erro (incluem o fuso horário)?
 - **Código de suporte** – qual foi o código de suporte gerado quando o usuário viu o erro (para encontrar isso, reproduzir o erro, e em seguida, clique no link de suporte código na parte inferior da tela e enviar a engenharia de suporte a GUID que resulta).
   - Se você estiver em uma página sem um código de suporte na parte inferior, pressionar F12 e procure SID e CID e enviar esses dois resultados para a engenharia de suporte.

    ![][001]

 - **ID do usuário** – qual foi a ID do usuário que viu o erro (por exemplouser@contoso.com)?
 - **Informações sobre o usuário** – foi o usuário federado, a senha de hash sincronizou, somente nuvem?  O usuário tinha uma licença AAD Premium ou AAD básica atribuída?
 - **Log de eventos do aplicativo** – se você estiver usando senha write-back e o erro está em sua infraestrutura de locais, por favor, zip uma cópia do seu log de eventos do aplicativo do seu servidor do Azure AD Connect e enviar junto com sua solicitação.

Incluindo estas informações nos ajudarão a resolver o problema assim que possível.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Solucionar problemas de configuração de redefinição de senha no Portal de gerenciamento do Azure
Se você encontrar um erro quando a redefinição de senha de configuração, você poderá solucioná-lo seguindo as etapas de solução de problemas a seguir:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um usuário ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo a seção de <strong>Política de redefinição de senha do usuário </strong>na guia <strong>Configurar</strong> no portal de gerenciamento do Azure</p>
            </td>
            <td>
              <p>A seção de <strong>Política de redefinição de senha do usuário </strong>não estiver visível na guia <strong>Configurar</strong> no Portal de gerenciamento do Azure.</p>
            </td>
            <td>
              <p>Isso pode ocorrer se você não tiver uma licença do AAD Premium ou AAD básicas atribuída ao administrador executar essa operação. </p>
              <p>Para corrigir isso, atribuir uma licença AAD Premium ou AAD básico para a conta de administrador em questão, navegando até a guia <strong>licenças</strong> e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo qualquer uma das opções de configuração na seção <strong>Política de redefinição de senha de usuário</strong> que são descritas na documentação.</p>
            </td>
            <td>
              <p>A seção de <strong>Política de redefinição de senha do usuário </strong>estiver visível, mas o único sinalizador aparece sob é o sinalizador <strong>Os usuários habilitados para redefinição de senha</strong> .</p>
            </td>
            <td>
              <p>O resto da interface do usuário será exibido quando você alterna o sinalizador <strong>Os usuários habilitados para redefinição de senha</strong> para <strong>Sim.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo uma opção de configuração específica.</p>
            </td>
            <td>
              <p>Por exemplo, não vejo a opção <strong>número de dias antes que um usuário deve confirmar seus dados de contato</strong> quando eu percorra a seção de <strong>Política de redefinição de senha do usuário</strong> (ou outros exemplos do mesmo problema).</p>
            </td>
            <td>
              <p>Muitos elementos da interface do usuário estão ocultas até que eles são necessários. Tente ativar todas as opções na página se você quiser ver.</p>
              <p>Consulte o <a href="active-directory-passwords-customize.md#password-management-behavior">comportamento de gerenciamento de senha</a> para obter mais informações sobre todos os controles que estão disponíveis para você.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo a opção de configuração de <strong>Gravar novamente as senhas para o local</strong></p>
            </td>
            <td>
              <p>A opção de <strong>Gravar novamente as senhas para o local</strong> não estiver visível na guia <strong>Configurar</strong> no Portal de gerenciamento do Azure</p>
            </td>
            <td>
              <p>Essa opção só é visível se você baixou Azure AD Connect e configurou write-back de senha. Quando você tiver feito isso, essa opção é exibida e permite habilitar ou desabilitar write-back da nuvem.</p>
              <p>Consulte <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Habilitar senha write-back no Azure AD Connect</a> para obter mais informações sobre como fazer isso.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Solucionar problemas de relatórios de gerenciamento de senha no Portal de gerenciamento do Azure
Se você encontrar um erro ao usar os relatórios de gerenciamento de senha, você poderá solucioná-lo seguindo as etapas de solução de problemas a seguir:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um usuário ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não vejo os relatórios de gerenciamento de senha</p>
            </td>
            <td>
              <p>Os relatórios de <strong>atividade de redefinição de senha</strong> e a <strong>atividade de registro de redefinição de senha</strong> não são visíveis nos relatórios de <strong>Log de atividades</strong> na guia <strong>relatórios</strong> .</p>
            </td>
            <td>
              <p>Isso pode ocorrer se você não tiver uma licença do AAD Premium ou AAD básicas atribuída ao administrador executar essa operação. </p>
              <p>Para corrigir isso, atribuir uma licença AAD Premium ou AAD básico para a conta de administrador em questão, navegando até a guia <strong>licenças</strong> e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Registros de usuário mostram várias vezes</p>
            </td>
            <td>
              <p>Quando um usuário registra email alternativo, telefone celular e perguntas de segurança, cada um deles aparecer como linhas separadas em vez de uma única linha.</p>
            </td>
            <td>
              <p>Atualmente, quando um usuário registra, podemos não pode assumir que eles registrará tudo presentes na página de registro. Como resultado, podemos atualmente faça logon cada parte individual de dados registrado como um evento separado.</p>
              <p>Se você quiser agregar dados, você pode baixar o relatório e abra os dados como uma tabela dinâmica no excel para ter mais flexibilidade.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Solucionar o portal de registro de redefinição de senha
Se você encontrar um erro ao registrar um usuário para redefinição de senha, você poderá solucioná-lo seguindo as etapas de solução de problemas a seguir:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um usuário ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório não está habilitado para redefinição de senha</p>
            </td>
            <td>
              <p>O administrador não tiver ativado para usar esse recurso.</p>
            </td>
            <td>
              <p>Alternar o sinalizador <strong>Os usuários habilitados para redefinição de senha</strong> para <strong>Sim</strong> e clicar em <strong>Salvar</strong> no guia de configuração de diretório do Portal de gerenciamento do Azure. Você deve ter um Azure AD Premium ou básica licença atribuída para o administrador executar essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuário não tem uma licença de AAD Premium ou AAD básicas atribuída</p>
            </td>
            <td>
              <p>O administrador não tiver ativado para usar esse recurso.</p>
            </td>
            <td>
              <p>Atribua uma licença do Azure AD Premium ou básica do Azure AD para o usuário sob a guia <strong>licenças</strong> no Portal de gerenciamento do Azure. Você deve ter um Azure AD Premium ou básica licença atribuída para o administrador executar essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao processar solicitação</p>
            </td>
            <td>
              <p>Usuário vê um erro que diz:</p>
              <p>

              </p>
              <p>Erro ao processar solicitação </p>
              <p>Ao tentar redefinir uma senha.</p>
            </td>
            <td>
              <p>Isso pode ser causado por vários problemas, mas geralmente este erro é causado por qualquer um serviço falta ou configuração problema que não pode ser resolvido. </p>
              <p>Se você ver esse erro e ele está afetando sua empresa, contate o suporte e podemos auxiliarão OMBP. Consulte <a href="#information-to-include-when-you-need-help">informações a serem incluídas quando precisar de ajuda</a> para ver o que você deve fornecer para a engenharia de suporte para auxiliar em uma resolução rápida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Solucionar problemas do portal de redefinição de senha
Se você encontrar um erro quando redefinir uma senha de um usuário, você poderá solucioná-lo seguindo as etapas de solução de problemas a seguir:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um usuário ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório não está habilitado para redefinição de senha</p>
            </td>
            <td>
              <p>Sua conta não está habilitada para redefinição de senha</p>
              <p>Lamentamos, mas seu administrador não configurou a sua conta para usar com esse serviço. </p>
              <p>

              </p>
              <p>Se você quiser, podemos pode contatar um administrador em sua organização para redefinir sua senha para você.</p>
            </td>
            <td>
              <p>Alternar o sinalizador <strong>Os usuários habilitados para redefinição de senha</strong> para <strong>Sim</strong> e clicar em <strong>Salvar</strong> no guia de configuração de diretório do Portal de gerenciamento do Azure. Você deve ter um Azure AD Premium ou básica licença atribuída para o administrador executar essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuário não tem uma licença de AAD Premium ou AAD básicas atribuída</p>
            </td>
            <td>
              <p>Enquanto não é possível redefinir senhas de conta não administradores automaticamente, podemos contate o administrador da sua organização para fazer isso por você.</p>
            </td>
            <td>
              <p>Atribua uma licença do Azure AD Premium ou básica do Azure AD para o usuário sob a guia <strong>licenças</strong> no Portal de gerenciamento do Azure. Você deve ter um Azure AD Premium ou básica licença atribuída para o administrador executar essa operação.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório está habilitado para redefinição de senha, mas o usuário tenha informações de autenticação formadas incorretamente ou ausentes</p>
            </td>
            <td>
              <p>Sua conta não está habilitada para redefinição de senha</p>
              <p>Lamentamos, mas seu administrador não configurou a sua conta para usar com esse serviço. </p>
              <p>

              </p>
              <p>Se você quiser, podemos pode contatar um administrador em sua organização para redefinir sua senha para você.</p>
            </td>
            <td>
              <p>Certifique-se de que o usuário tem formada corretamente dados de contato no arquivo no diretório antes de continuar. Veja <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quais dados são usados pela redefinição de senha</a> para obter informações sobre como configurar informações de autenticação no diretório para que os usuários não vir esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório está habilitado para redefinição de senha, mas um usuário tem apenas uma parte dos dados de contato no arquivo quando a política é definida para exigir duas etapas de verificação</p>
            </td>
            <td>
              <p>Sua conta não está habilitada para redefinição de senha</p>
              <p>Lamentamos, mas seu administrador não configurou a sua conta para usar com esse serviço. </p>
              <p>

              </p>
              <p>Se você quiser, podemos pode contatar um administrador em sua organização para redefinir sua senha para você.</p>
            </td>
            <td>
              <p>Certifique-se de que o usuário tem pelo menos dois métodos de contato configurados adequadamente (por exemplo, telefone celular e Office Phone) antes de continuar. Veja <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quais dados são usados pela redefinição de senha</a> para obter informações sobre como configurar informações de autenticação no diretório para que os usuários não vir esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Diretório está habilitado para redefinição de senha e usuário está configurado corretamente, mas não é possível ser contatado usuário </p>
            </td>
            <td>
              <p>Algo deu errado.  Podemos encontrou um erro inesperado ao entrar em contato com você.</p>
            </td>
            <td>
              <p>Isso pode ser o resultado de um erro de serviço temporário ou configurado incorretamente dados de contato que nós não pôde detectar corretamente. Se o usuário aguarda 10 segundos, experimentar novamente e link "Contate o administrador" aparece. Clicar tente novamente enviará novamente a chamada, enquanto clicando em "Contate o administrador" enviará um email de formulário para o usuário, senha ou administradores globais (em que ordem de precedência) solicitando uma redefinição de senha para ser executado para essa conta de usuário.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuário nunca recebe a redefinição de senha SMS ou chamada telefônica</p>
            </td>
            <td>
              <p>Cliques do usuário "texto-me" ou "telefonar para mim" e recebe nunca nada.</p>
            </td>
            <td>
              <p>Isso pode ser o resultado de um número de telefone formadas incorretamente no diretório. Verifique se o número de telefone está no formato "+ ccc xxxyyyzzzzXeeee". Para saber mais sobre formatação telefone números para uso com redefinição de senha consulte <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quais dados são usados pela redefinição de senha</a>.</p>
              <p>Se você solicitar uma extensão sejam roteados para o usuário em questão, observe que redefinição de senha não suporta extensões, mesmo se você especificar um no diretório (eles são removidos antes que a chamada é enviada). Tente usar um número sem uma extensão, ou integrar a extensão do número de telefone no seu PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuário nunca recebe email de redefinição de senha</p>
            </td>
            <td>
              <p>Usuário clica em "enviar por email-me" e recebe nunca nada.</p>
            </td>
            <td>
              <p>A causa mais comum para esse problema é que a mensagem foi rejeitada por um filtro de spam. Verifique seu spam, a pasta de lixo eletrônico ou excluídos itens para o email.</p>
              <p>Também, certifique-se de que você está verificando o email correto para a mensagem … várias pessoas tenham endereços de email muito semelhante e acabar a caixa de entrada errada para a mensagem de verificação. Se nenhuma dessas opções funcionar, também é possível que o endereço de email no diretório é mal formado, verifique para certificar-se de que o endereço de email está correto e tente novamente. Para saber mais sobre formatação email endereços para uso com redefinição de senha consulte <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quais dados são usados pela redefinição de senha</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Posso ter definido uma política de redefinição de senha, mas quando uma conta de administrador usa redefinição de senha, essa política não é aplicada</p>
            </td>
            <td>
              <p>Contas de administrador redefinir suas senhas consulte as mesmas opções habilitadas para redefinição de senha, o email e o telefone celular, não importa qual política é definida na seção <strong>Política de redefinição de senha de usuário</strong> da guia <strong>Configurar</strong> .</p>
            </td>
            <td>
              <p>As opções configuradas sob a seção de <strong>Política de redefinição de senha de usuário</strong> da guia <strong>Configurar</strong> aplicam-se apenas aos usuários finais em sua organização.</p>
              <p>Microsoft gerencia e controles política de redefinição de senha de administrador para garantir o nível mais alto de segurança</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuário impedido de tentativa de redefinição muitas vezes em um dia de senha</p>
            </td>
            <td>
              <p>Usuário vê um erro informando:</p>
              <p>

              </p>
              <p>Use uma outra opção.</p>
              <p>Você já tentou verificar sua conta muitas vezes na última 1 hora. Por razões de segurança, você terá que aguardar 24 horas antes que você pode tentar novamente. </p>
              <p>Se você quiser, podemos pode contatar um administrador em sua organização para redefinir sua senha para você.</p>
            </td>
            <td>
              <p>Podemos implementar um mecanismo de otimização automático para bloquear usuários de tentar redefinir suas senhas muitas vezes em um curto período de tempo. Isso ocorre quando:</p>
              <ol class="ordered">
                <li>
Usuário tenta validar um número de telefone 5 vezes em uma hora.<br\><br\></li>
                <li>
Usuário tenta usar o gate de perguntas de segurança 5 vezes em uma hora.<br\><br\></li>
                <li>
Usuário tenta redefinir uma senha para a mesma conta de usuário 5 vezes em uma hora.<br\><br\></li>
              </ol>
              <p>Para corrigir isso, instrua o usuário aguardar 24 horas após a última tentativa e o usuário será capaz de redefinir sua senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Usuário vê um erro ao validar o seu número de telefone</p>
            </td>
            <td>
              <p>Ao tentar verificar um telefone para usar como um método de autenticação, o usuário vê um erro informando:</p>
              <p>

              </p>
              <p>Número de telefone incorreto especificado.</p>
            </td>
            <td>
              <p>Esse erro ocorre quando o número de telefone inserido não coincidir com o número de telefone no arquivo.</p>
              <p>Verifique se que o usuário estiver inserindo o número de telefone completo, incluindo o código de área e país, ao tentar usar um método baseado em telefone para redefinição de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao processar solicitação</p>
            </td>
            <td>
              <p>Usuário vê um erro que diz:</p>
              <p>

              </p>
              <p>Erro ao processar solicitação </p>
              <p>Ao tentar redefinir uma senha.</p>
            </td>
            <td>
              <p>Isso pode ser causado por vários problemas, mas geralmente este erro é causado por qualquer um serviço falta ou configuração problema que não pode ser resolvido. </p>
              <p>Se você ver esse erro e ele está afetando sua empresa, contate o suporte e podemos auxiliarão OMBP. Consulte <a href="#information-to-include-when-you-need-help">informações a serem incluídas quando precisar de ajuda</a> para ver o que você deve fornecer para a engenharia de suporte para auxiliar em uma resolução rápida.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Solucionar problemas de write-back de senha
Se você encontrar um erro ao ativar, desativar ou usando write-back de senha, você poderá solucioná-lo seguindo as etapas de solução de problemas a seguir:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Caso de erro</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erro de que um usuário ver?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solução</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falhas de inicialização e integração de geral</p>
            </td>
            <td>
              <p>Serviço de redefinição de senha não inicia no local com erro 6800 no log de eventos de aplicativo do Azure AD Connect da máquina.</p>
              <p>

              </p>
              <p>Depois de integração, federado ou hash de senha usuários sincronizados não podem redefinir suas senhas.</p>
            </td>
            <td>
              <p>Quando write-back senha está habilitado, o mecanismo de sincronização chamará a biblioteca de write-back para executar a configuração (integração) falar com o serviço de integração de nuvem. Todos os erros encontrados durante a integração ou ao iniciar o ponto de extremidade do WCF para write-back senha resultará em erros no log de eventos no log de eventos do seu computador Azure AD Connect.</p>
              <p>Durante a reinicialização do serviço de ADSync, se Write-back foi configurado, o ponto de extremidade do WCF será iniciado para cima. No entanto, se a inicialização do ponto de extremidade falhar, podemos simplesmente será registrar evento 6800 e deixe a inicialização do serviço de sincronização. Presença desse evento significa que a senha write-back ponto de extremidade não foi iniciado. Detalhes de log de eventos para este evento (6800) juntamente com entradas de log de eventos para gerar PasswordResetService componente indicará por que o ponto de extremidade não pôde ser iniciado para cima. Examine esses erros do log de eventos e tente iniciar novamente o Azure AD Connect se senha write-back não estiver funcionando. Se o problema persistir, tente desativar e reativar write-back de senha.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Quando um usuário tenta redefinir uma senha ou desbloquear uma conta com senha write-back habilitado, a operação falhará. Além disso, você verá um evento no log de eventos do Azure AD Connect contendo: "mecanismo de sincronização retornado um erro hr = 800700CE, mensagem = o nome do arquivo ou extensão é muito longa" após a operação de desbloquear.
                            </p>
            </td>
            <td>
              <p>Isso pode ocorrer se você tivesse atualizado de versões mais antigas do Azure AD Connect ou DirSync. Atualização para versões mais antigas do Azure AD Connect definir uma senha de 254 caractere para a conta do agente de gerenciamento do Azure AD (versões mais recentes definirá uma senha de comprimento de 127 caracteres). Tais senhas longas funcionam para operações de conector AD importação e exportação, mas eles não são suportados pela operação desbloquear.
                            </p>
            </td>
            <td>
              <p>[Para localizar a conta do Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) do Azure AD Connect e redefinir a senha para conter não mais do que 127 caracteres. Em seguida, abra o **Serviço de sincronização** do menu Iniciar. Navegue até **conectores** e localize o **Conector do Active Directory**. Selecione-a e clique em **Propriedades**. Navegue até a página de **credenciais** e insira a nova senha. Selecione **Okey** para fechar a página.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Erro ao configurar write-back durante a instalação do Azure AD Connect.</p>
            </td>
            <td>
              <p>Na última etapa do processo de instalação do Azure AD Connect, você verá um erro indicando que senha write-back não pôde ser configurada.</p>
              <p>

              </p>
              <p>O log de eventos do aplicativo do Azure AD conectar contém erro 32009 com o texto "Erro obtendo auth token".</p>
            </td>
            <td>
              <p>Esse erro ocorre nos dois casos a seguir:</p>
              <ul>
                <li class="unordered">
Você especificou uma senha incorreta para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Você tentou usar um usuário federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.<br\><br\></li>
              </ul>
              <p>Para corrigir esse erro, certifique-se de que você não está usando uma conta federada para o administrador global especificado no início do processo de instalação do Azure AD Connect, e se a senha especificada está correta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar write-back durante a instalação do Azure AD Connect.</p>
            </td>
            <td>
              <p>O log de eventos do Azure AD Connect máquina contém erro 32002 lançada pela PasswordResetService.</p>
              <p>

              </p>
              <p>Lê se o erro: "erro se conectando ao ServiceBus, o provedor de token pôde fornecer um token de segurança..."</p>
              <p>

              </p>
            </td>
            <td>
              <p>A causa desse erro é o serviço de redefinição de senha em execução no seu ambiente local não conseguir conectar o ponto de extremidade do barramento de serviço na nuvem. Este erro normalmente normalmente é causado por uma regra de firewall bloqueando uma conexão de saída para um endereço web ou porta específico.</p>
              <p>

              </p>
              <p>Verifique se que seu firewall permite conexões de saída para o seguinte:</p>
              <ul>
                <li class="unordered">
Todo o tráfego sobre TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Conexões de saída para <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Quando tiver atualizado essas regras, reinicie a máquina Azure AD Connect e senha write-back deve começar a trabalhar novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Senha write-back ponto de extremidade local não acessível</p>
            </td>
            <td>
              <p>Após trabalhar para algum tempo, federado ou hash de senha usuários sincronizados não podem redefinir suas senhas.</p>
            </td>
            <td>
              <p>Em alguns casos raros, o serviço de senha write-back pode falhar ao iniciar novamente quando Azure AD Connect iniciou a novamente. Nesses casos, primeiro, verifique se a senha write-back parece estar habilitado no prem. Isso pode ser feito usando o Assistente de Azure AD Connect ou powershell (consulte HowTos seção acima). Se o recurso parece estar habilitado, tente ativar ou desativar o recurso novamente por meio da interface do usuário ou o PowerShell. Consulte "etapa 2: habilitar write-back senha no seu computador de sincronização de diretórios &amp; configurar regras de firewall" em <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">como ativar/desativar write-back de senha</a> para obter mais informações sobre como fazer isso.</p>
              <p>

              </p>
              <p>Se isso não funcionar, tente completamente desinstalar e reinstalar o Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erros de permissões</p>
            </td>
            <td>
              <p>Federado ou senha hash-sincronizados os usuários que tentarem redefinir sua senhas Consulte erro após enviar a senha indicando que houve um problema de serviço.</p>
              <p>

              </p>
              <p>Além disso, durante operações de redefinição de senha, você poderá ver que um erro com relação ao agente de gerenciamento foi negado acesso em locais logs de eventos.</p>
            </td>
            <td>
              <p>Se você vir esses erros no registro de eventos, confirme se a conta do AD MA (que foi especificada no assistente no momento da configuração) tem as permissões necessárias para write-back de senha.</p>
              <p>

              </p>
              <p>Observe que após essa permissão é dada pode levar até 1 hora as permissões para repassadas através de tarefa de plano de fundo de sdprop no controlador de domínio. </p>
              <p>Para redefinir sua senha, para trabalhar, a permissão precisa estar marcada em descritor de segurança do objeto do usuário cuja senha está sendo redefinida. Até que essa permissão seja exibido no objeto do usuário, redefinição de senha continuarão a falhar com acesso negado.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erro ao configurar senha write-back do Assistente de configuração do Azure AD Connect </p>
            </td>
            <td>
              <p>Erro "Não é possível localizar MA" no assistente ao ativar/desativar write-back de senha</p>
            </td>
            <td>
              <p>Há um bug conhecido na versão de lançamento do Azure AD Connect que manifestos nas seguintes situações:</p>
              <ol class="ordered">
                <li>
Configurar Azure AD Connect para abc.com de locatário (domínio verificado) usando credenciais. Isso resulta em conector AAD com nome "abc.com – AAD" está sendo criada.<br\><br\></li>
                <li>
Você, em seguida, em seguida, altere as credenciais AAD para o conector (usando a sincronização antiga UI) para (Observe que é o mesmo locatário mas um nome de domínio diferente). <br\><br\></li>
                <li>
Agora você tenta ativar/desativar write-back de senha. O assistente será construir o nome do conector usando as credenciais, como "abc.onmicrosoft.com – AAD" e passar para o cmdlet Write-back de senha. Isso falhará porque não há nenhum conector criado com este nome.<br\><br\></li>
              </ol>
              <p>Isso foi corrigido em nossas compilações mais recentes. Se você tiver uma compilação mais antiga, a uma solução alternativa é usar o cmdlet do powershell para ativar/desativar o recurso. Consulte "etapa 2: habilitar write-back senha no seu computador de sincronização de diretórios &amp; configurar regras de firewall" em <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">como ativar/desativar write-back de senha</a> para obter mais informações sobre como fazer isso.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Não é possível redefinir a senha para os usuários em grupos especiais como Domain Admins / Enterprise administradores etc.</p>
            </td>
            <td>
              <p>Federado ou senha hash-sincronizados usuários que fazem parte de grupos protegidos e tentarem redefinir sua senhas Consulte erro após enviar a senha indicando que houve um problema de serviço.</p>
            </td>
            <td>
              <p>Usuários privilegiados no Active Directory são protegidos usando AdminSDHolder. Consulte <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> para obter mais detalhes. </p>
              <p>

              </p>
              <p>Isso significa descritores de segurança nesses objetos são verificados periodicamente para corresponder ao AdminSDHolder especificado em um e são redefinidos se eles forem diferentes. Portanto, as permissões adicionais que são necessários para senha write-back não fluir para esses usuários. Isso pode resultar em senha write-back não funcionando para esses usuários. Como resultado, não suportamos gerenciar senhas para os usuários desses grupos porque ele quebras o modelo de segurança do AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Redefinir operações falhar com objeto não pôde ser encontrado</p>
            </td>
            <td>
              <p>Federado ou senha hash-sincronizados os usuários que tentarem redefinir sua senhas Consulte erro após enviar a senha indicando que houve um problema de serviço.</p>
              <p>

              </p>
              <p>Além disso, durante operações de redefinição de senha, você poderá ver um erro em seus logs de eventos do serviço do Azure AD Connect indicando um erro de "Objeto não encontrado".</p>
            </td>
            <td>
              <p>Esse erro geralmente indica que o mecanismo de sincronização é possível localizar o objeto de usuário no espaço do conector AAD ou o MV vinculada ou objeto de espaço de conector do AD. </p>
              <p>

              </p>
              <p>Para solucionar esse problema, certifique-se de que o usuário realmente está sincronizado de local com AAD via a instância atual do Azure AD Connect e inspecionar o estado dos objetos nos espaços de conector e MV. Confirme se o objeto do AD CS é conector ao objeto MV via a regra "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Falha de operações de redefinir com várias correspondências encontradas eror</p>
            </td>
            <td>
              <p>Federado ou senha hash-sincronizados os usuários que tentarem redefinir sua senhas Consulte erro após enviar a senha indicando que houve um problema de serviço.</p>
              <p>

              </p>
              <p>Além disso, durante operações de redefinição de senha, você poderá ver um erro em seus logs de eventos do serviço do Azure AD Connect indicando um erro de "Vários maches encontradas".</p>
            </td>
            <td>
              <p>Isso indica que o mecanismo de sincronização detectado que o objeto de MV está conectado com mais de um objetos do AD CS via "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o usuário tenha uma conta habilitada em mais de uma floresta. </p>
              <p>

              </p>
              <p>No momento este cenário não há suporte para write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Operações de senha falharem com um erro de configuração.</p>
            </td>
            <td>
              <p>Operações de senha falharem com um erro de configuração. O log de eventos do aplicativo contém o erro de Azure AD Connect 6329 com texto: 0x8023061f (a operação falha porque a sincronização de senha não está ativada neste agente de gerenciamento).</p>
            </td>
            <td>
              <p>Isso ocorre se a configuração do Azure AD Connect é alterada para adicionar&nbsp;uma nova floresta AD (ou para remover e adicionar novamente uma floresta existente) <strong>após</strong> o recurso de write-back senha já foi ativado. Operações de senha para usuários em tais florestas recém-adicionado falhará. Para corrigir o problema, desabilitar e habilitar o recurso de write-back senha novamente após as alterações de configuração de floresta foram concluídas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Gravar de volta senhas que tenham sido redefinidas pelo works de usuários corretamente, mas gravar de volta senhas alterados por um usuário ou falha de redefinição de um usuário por um administrador.</p>
            </td>
            <td>
              <p>Ao tentar redefinir uma senha em nome de um usuário do Portal de gerenciamento do Azure, você vê uma mensagem informando: "o serviço de redefinição de senha em execução no seu ambiente local não dá suporte a administradores de redefinição de senhas de usuário. Atualize para a versão mais recente do Azure AD Connect para resolver o problema."</p>
            </td>
            <td>
              <p>Isso ocorre quando a versão do mecanismo de sincronização não oferece suporte a operação de write-back senha específica que foi usada. Versões do Azure AD Connect posteriores 1.0.0419.0911 oferece suporte a todas as operações de gerenciamento de senha, incluindo senha redefinir write-back, Write-back de alteração de senha e Write-back redefinição de senha iniciadas pelo administrador do Portal de gerenciamento do Azure.&nbsp; DirSync versões mais recentes que 1.0.6862 suportam senha redefinir write-back somente. Para resolver esse problema, é altamente recomendável que você instale a versão mais recente do Azure AD Connect ou a conexão do Azure Active Directory (para obter mais informações, consulte <a href="active-directory-aadconnect">Ferramentas de integração de diretório</a>) para resolver esse problema e obter o máximo proveito do Write-back de senha em sua organização.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de log de eventos de write-back de senha
Uma prática recomendada ao solucionar problemas com write-back de senha é inspecionar esse Log de eventos do aplicativo em sua máquina Azure AD Connect. O log de eventos conterá eventos de duas fontes de interesse para write-back de senha. A fonte de PasswordResetService descreverá problemas relacionados à operação de write-back de senha e operações. A fonte de ADSync descreverá operações e problemas relacionados à configuração senhas no seu ambiente do AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Código</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nome / mensagem</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Fonte</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descrição</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>ALÇA: MMS(4924) 0x80230619 – "uma restrição impede que a senha está sendo alterado ao atual especificado."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Esse evento ocorre quando o serviço de write-back senha tenta definir uma senha em seu diretório local que não atende a duração da senha, histórico, complexidade ou requisitos de filtragem do domínio.</p>
              <ul>
                <li class="unordered">
Se você tiver uma duração mínima da senha e alterou recentemente a senha nessa janela de tempo, você não poderá alterar a senha novamente até atingir a idade especificada em seu domínio. Para fins de teste, idade mínima deve ser definida como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se você tem requisitos de histórico de senha habilitados, então você deve selecionar uma senha que não foi usada nos últimos tempos N, onde N é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nos últimos tempos N, em seguida, você verá uma falha nesse caso. Para fins de teste, histórico deve ser definido como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se você tiver filtros de senha habilitados, e um usuário seleciona uma senha que não atendem aos critérios de filtragem, em seguida, redefinir ou alterar a operação falhará.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>Mecanismo de sincronização retornado um erro hr = 80230402, mensagem = tentar obter um objeto falha porque existem entradas duplicadas com a mesma âncora</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Esse evento ocorre quando a mesma id de usuário está habilitada em vários domínios.  Por exemplo, se você estiver sincronizando florestas conta/recurso e ter o mesmo id de usuário presente e habilitado em cada, esse erro pode ocorrer.  </p>
              <p>Esse erro também pode ocorrer se você estiver usando um atributo de âncora não exclusivo (como alias ou nome UPN) e dois usuários compartilharem esse mesmo atributo de âncora.</p>
              <p>Para resolver esse problema, certifique-se de que você não tem quaisquer usuários duplicados dentro de seus domínios e que você está usando um atributo de âncora exclusivo para cada usuário.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço de local detectados solicitação para um federados de redefinição de senha ou senha hash-sincronizados usuário provenientes na nuvem. Este evento é que o primeiro evento em cada senha redefinir a operação de write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que um usuário selecionado uma nova senha durante uma operação de redefinição de senha, podemos determinado que essa senha atende aos requisitos de senha corporativo e senha foi com êxito escrita volta para o ambiente do AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que um usuário selecionado uma senha e que a senha chegou com êxito para o ambiente local, mas quando podemos tentou definir a senha no ambiente AD local, ocorreu uma falha. Isso pode acontecer por vários motivos:</p>
              <ul>
                <li class="unordered">
A senha do usuário não atender a idade, histórico, complexidade ou filtrar requisitos para o domínio. Tente uma senha completamente nova para resolver esse problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de serviço de MA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta do usuário está em um grupo protegido, como administradores de domínio ou empresa, que não permite operações de conjunto de senha.<br\><br\></li>
              </ul>
              <p>Consulte <a href="#troubleshoot-password-writeback">Solucionar problemas de write-back de senha</a> para saber mais sobre quais outros situtions podem causar esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento ocorre se você habilitar write-back de senha com o Azure AD Connect e indica que estamos iniciado integração sua organização para o serviço da web de write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica o processo de integração foi bem-sucedido e que o recurso de write-back de senha está pronto para uso.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o serviço de local detectou uma solicitação de alteração de senha para uma federados ou senha hash-sincronizados usuário provenientes na nuvem. Este evento é o primeiro em todas as operações de write-back de alteração de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que um usuário selecionado uma nova senha durante uma operação de alteração de senha, podemos determinado que essa senha atende aos requisitos de senha corporativo e senha foi com êxito escrita volta para o ambiente do AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que um usuário selecionado uma senha e que a senha chegou com êxito para o ambiente local, mas quando podemos tentou definir a senha no ambiente AD local, ocorreu uma falha. Isso pode acontecer por vários motivos:</p>
              <ul>
                <li class="unordered">
A senha do usuário não atender a idade, histórico, complexidade ou filtrar requisitos para o domínio. Tente uma senha completamente nova para resolver esse problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de serviço de MA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta do usuário está em um grupo protegido, como administradores de domínio ou empresa, que não permite operações de conjunto de senha.<br\><br\></li>
              </ul>
              <p>Consulte <a href="#troubleshoot-password-writeback">Solucionar problemas de write-back de senha</a> para saber mais sobre outras situações que podem causar esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O serviço de local detectou solicitação para um federados de redefinição de senha ou senha hash-sincronizados usuário provenientes o administrador em nome de um usuário. Este evento é o primeiro em todas as operações de write-back de redefinição de senha iniciado pelo administrador.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O administrador selecionada uma nova senha durante uma operação de redefinição de senha iniciado pelo administrador, podemos determinado que essa senha atende aos requisitos de senha corporativo e senha foi com êxito escrita volta para o ambiente do AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>O administrador selecionado uma senha em nome de um usuário e essa senha chegou com êxito para o ambiente local, mas quando podemos tentou definir a senha no ambiente AD local, ocorreu uma falha. Isso pode acontecer por vários motivos:</p>
              <ul>
                <li class="unordered">
A senha do usuário não atender a idade, histórico, complexidade ou filtrar requisitos para o domínio. Tente uma senha completamente nova para resolver esse problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta de serviço de MA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
A conta do usuário está em um grupo protegido, como administradores de domínio ou empresa, que não permite operações de conjunto de senha.<br\><br\></li>
              </ul>
              <p>Consulte <a href="#troubleshoot-password-writeback">Solucionar problemas de write-back de senha</a> para saber mais sobre quais outros situtions podem causar esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento ocorre se você desabilitar senha write-back com o Azure AD Connect e indica que estamos iniciado offboarding sua organização para o serviço da web de write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica o processo de offboarding foi bem-sucedido e que o recurso de write-back senha foi desabilitado com êxito.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o processo de offboarding não foi bem-sucedido. Isso pode ser devido a um erro de permissões da conta de administrador de nuvem ou local especificado durante a configuração, ou porque você está tentando usar um administrador global de nuvem federados quando desabilitando write-back de senha. Para corrigir isso, verifique sua administrativo permissões e que você não estiver usando qualquer federado conta ao configurar o recurso de write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço de write-back de senha foi iniciado com êxito e está pronto para aceitar solicitações de gerenciamento de senha da nuvem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço de write-back senha parou e quaisquer solicitações de gerenciamento de senha da nuvem não será bem sucedidas.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que estamos recuperado com êxito um token de autorização para o administrador global especificado durante a instalação do Azure AD Connect para iniciar o processo de offboarding ou integração.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que criamos com êxito a chave de criptografia de senha que será usada para criptografar senhas da nuvem sejam enviadas para seu ambiente local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica um erro desconhecido durante uma operação de gerenciamento de senha. Examine o texto de exceção no evento para obter mais detalhes. Se você estiver tendo problemas, tente desativar e reativar write-back de senha. Se isso não ajudar, inclua uma cópia do seu log de eventos junto com a id de acompanhamento especificada internas para sua engenharia de suporte.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro de conexão com a senha de nuvem redefinir serviço e geralmente ocorre quando o serviço de local não foi possível se conectar ao serviço da web de redefinição de senha. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro de conexão com a instância de barramento de serviço do seu locatário. Isso pode acontecer porque você está bloqueando conexões de saída no seu ambiente local. Verifique seu firewall para garantir que você permite conexões pela TCP 443 e <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e tente novamente. Se você ainda estiver enfrentando problemas, tente desativar e reativar write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que a entrada transmitida à nossa API do serviço web era inválida. Tente a operação novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro ao descriptografar a senha que chegaram da nuvem. Isso pode ser devido a uma incompatibilidade de chave de descriptografia entre o serviço de nuvem e seu ambiente local. Para resolver isso, desative e reabilitar write-back senha no seu ambiente local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante a integração, podemos salvar informações de locatário específicos em um arquivo de configuração em seu ambiente local. Esse evento indica Ocorreu um erro ao salvar este arquivo ou que quando o serviço foi iniciado lá foi um erro de leitura do arquivo. Para corrigir esse problema, tente desativar e reativação senha write-back para forçar uma regravar deste arquivo de configuração. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETA – este evento não está presente no Azure AD Connect, somente muito cedo compilações do DirSync suporte write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante a integração, podemos enviar dados de nuvem para a senha local redefinir serviço. Que dados sejam gravados para um arquivo na memória antes de serem enviados para o serviço de sincronização para armazenar essas informações com segurança no disco. Esse evento indica um problema com escrevendo ou atualizando dados na memória. Para corrigir esse problema, tente desativar e reativação senha write-back para forçar uma regravar dessa configuração.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que recebemos uma resposta inválida do serviço da web de redefinição de senha. Para corrigir esse problema, tente desativar e reativação write-back de senha.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que podemos não pôde obter uma autorização token para a conta de administrador global especificada durante a instalação do Azure AD Connect. Este erro pode ser causado por um nome de usuário incorreto ou senha especificado para a conta de administrador global ou porque a conta de administrador global especificada federada. Para corrigir esse problema, execute a configuração com o nome de usuário correto e a senha novamente e certifique-se de que o administrador é gerenciada (somente na nuvem ou seria de sincronização de senha) conta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve um erro ao gerar a senha de chave de criptografia ou descriptografar uma senha recebido a partir do serviço de nuvem. Este erro provavelmente indica um problema com o seu ambiente. Examine os detalhes do seu log de eventos para saber mais e resolver esse problema. Você também pode tentar desabilitar e habilitar novamente o serviço de write-back de senha para resolver esse problema.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço de local não pôde corretamente se comunicar com o serviço de web de redefinição de senha para iniciar o processo de integração. Isso pode ser devido a uma regra de firewall ou um problema ao obter um token de autenticação do seu locatário. Para corrigir isso, verifique se você não está bloqueando conexões de saída sobre TCP 443 e TCP 9350 9354 ou <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e a conta de administrador AAD estiver usando para integrado não federado. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETA – este evento não está presente no Azure AD Connect, somente muito cedo compilações do DirSync suporte write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o serviço de local não pôde corretamente se comunicar com o serviço de web de redefinição de senha para iniciar o processo de offboarding. Isso pode ser devido a uma regra de firewall ou um problema ao obter um token de autorização do seu locatário. Para corrigir isso, certifique-se de que você não está bloqueando conexões de saída pela 443 ou <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e que a conta de administrador AAD estiver usando para offboard não federado. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que tivemos Repetir para conectar-se a instância do barramento de serviço do seu locatário. Em condições normais, isso deve não ser um problema, mas se você vir esse evento muitas vezes, considere a verificação de sua conexão de rede para barramento de serviço, especialmente se for uma conexão de baixa largura de banda ou alta latência.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Para monitorar a integridade do seu serviço de write-back de senha, podemos enviar dados de pulsação à nossa senha redefinição de serviço web cada 5 minutos. Esse evento indica que houve um erro ao enviar essas informações de saúde volta para o serviço da web de nuvem. Essas informações de integridade não inclui um OII ou IIP de dados e é puramente uma pulsação e estatísticas de serviço básico para que podemos fornecer informações de status do serviço na nuvem.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que houve um erro desconhecido retornado por AD, verifique o log de eventos de servidor do Azure AD Connect para eventos da origem ADSync para obter mais informações sobre esse erro.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que o usuário que está tentando redefinir ou alterar uma senha não foi encontrado no diretório local. Isso pode ocorrer quando o usuário foi excluído no local, mas não na nuvem, ou se houver um problema com a sincronização. Verifique os logs de sincronização, bem como a última sincronização alguns execução detalhes para obter mais informações.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Quando um redefinição de senha ou solicitação de alteração se originam de nuvem, usamos a âncora de nuvem especificada durante o processo de configuração do Azure AD Connect para determinar como vincular solicitação para um usuário no seu ambiente local. Esse evento indica que encontramos dois usuários no diretório local com o mesmo atributo de âncora de nuvem. Verifique os logs de sincronização, bem como a última sincronização alguns execução detalhes para obter mais informações.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que a conta de serviço do agente de gerenciamento não tem as permissões apropriadas na conta em questão para definir uma nova senha. Certifique-se de que a conta de MA floresta do usuário tem redefinir e alterar permissões de senha em todos os objetos na floresta.  Para obter mais informações sobre como fazer isso, veja <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">etapa 4: configurar as permissões apropriadas do Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que estamos tentou redefinir ou alterar uma senha de uma conta que foi desabilitada no local. Habilitar a conta e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Evento indica que estamos tentou redefinir ou alterar uma senha de uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um usuário tiver tentado uma alteração ou redefinir a operação de senha muitas vezes em um período curto. Desbloquear a conta e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Este evento indica que o usuário especificado uma senha atual incorreta quando a operação de alteração de realizar uma senha. Especifique a senha atual correta e tente novamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento ocorre quando o serviço de write-back senha tenta definir uma senha em seu diretório local que não atende a duração da senha, histórico, complexidade ou requisitos de filtragem do domínio.</p>
              <ul>
                <li class="unordered">
Se você tiver uma duração mínima da senha e alterou recentemente a senha nessa janela de tempo, você não poderá alterar a senha novamente até atingir a idade especificada em seu domínio. Para fins de teste, idade mínima deve ser definida como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se você tem requisitos de histórico de senha habilitados, então você deve selecionar uma senha que não foi usada nos últimos tempos N, onde N é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nos últimos tempos N, em seguida, você verá uma falha nesse caso. Para fins de teste, histórico deve ser definido como 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se você tiver filtros de senha habilitados, e um usuário seleciona uma senha que não atendem aos critérios de filtragem, em seguida, redefinir ou alterar a operação falhará.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Esse evento indica que houve uma gravação de problema uma senha de volta para seu diretório local devido a um problema de configuração com o Active Directory. Verifique o log de eventos do aplicativo da máquina Azure AD Connect para mensagens do serviço ADSync para obter mais informações sobre o erro. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETA – este evento não está presente no Azure AD Connect, somente muito cedo compilações do DirSync suporte write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETA – este evento não está presente no Azure AD Connect, somente muito cedo compilações do DirSync suporte write-back.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETA – este evento não está presente no Azure AD Connect, somente muito cedo compilações do DirSync suporte write-back.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Solucionar problemas de conectividade de write-back de senha

Se você estiver enfrentando interrupções de serviço com o componente de write-back de senha do Azure AD Connect, aqui estão algumas etapas rápidas, que você pode tomar para resolver esse problema:

 - [Reiniciar o Azure AD conectar o serviço de sincronização](#restart-the-azure-AD-Connect-sync-service)
 - [Desabilitar e habilitar novamente o recurso de write-back de senha](#disable-and-re-enable-the-password-writeback-feature)
 - [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Solucionar problemas de write-back de senha](#troubleshoot-password-writeback)

Em geral, recomendamos que você executa essas etapas na ordem acima para recuperar o seu serviço da maneira mais rápida.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o Azure AD conectar o serviço de sincronização
Reiniciar o serviço de sincronização de conectar-se de AD Azure pode ajudar a resolver problemas de conectividade ou outros problemas de temporárias com o serviço.

 1. Como administrador, clique em **Iniciar** no servidor que executa o **Azure AD Connect**.
 2. Digite **"Services. msc"** na caixa de pesquisa e pressione **Enter**.
 3. Procure a entrada do **Microsoft Azure AD Connect** .
 4. Clique com botão direito na entrada do serviço, clique em **Reiniciar**e aguarde a conclusão da operação.

    ![][002]

Essas etapas serão restabelecer sua conexão com o serviço de nuvem e resolver qualquer interrupções que você pode estar enfrentando.  Se a reinicialização do serviço de sincronização não resolver o problema, recomendamos que você tente desabilitar e habilitar novamente o recurso de write-back senha como a próxima etapa.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desabilitar e habilitar novamente o recurso de write-back de senha
Desabilitar e habilitar novamente o recurso de senha write-back podem ajudar a resolver problemas de conectividade.

 1. Como administrador, abra o **Assistente de configuração do Azure AD Connect**.
 2. Na caixa de diálogo **conectar ao Azure AD** , insira suas **credenciais de administrador global do Azure AD**
 3. Na caixa de diálogo **conectar ao AD DS** , insira suas **credenciais de administrador de serviços de domínio do AD**.
 4. Na caixa de diálogo **identifica exclusivamente seus usuários** , clique no botão **próxima** .
 5. Na caixa de diálogo **recursos opcionais** , desmarque a caixa de seleção de **senha write-back** .

    ![][003]

 6. Clique em **Avançar** pelas páginas do diálogo restantes sem alterar nada até chegar à página **pronto para configurar** .
 7. Certifique-se de que a página Configurar mostra a **opção de regravação de senha como desabilitada** e clique no botão de **Configurar** verde para confirmar as alterações.
 8. Na caixa de diálogo **concluído** , desmarque a opção **Sincronizar agora** e clique em **Concluir** para fechar o assistente.
 9. Abra novamente o **Assistente de configuração do Azure AD Connect**.
 10.    **Repita as etapas 2-8**, exceto garantir que você **marcar a opção de regravação de senha** os **recursos opcionais** tela para reativar o serviço.

    ![][004]

Essas etapas serão restabelecer sua conexão com o nosso serviço de nuvem e resolver qualquer interrupções que você pode estar enfrentando.

Se desabilitar e habilitar novamente o recurso de senha write-back não resolver o problema, recomendamos que você tente instalar novamente o Azure AD Connect como a próxima etapa.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente do Azure AD Connect
Reinstalar o pacote do Azure AD Connect resolverá problemas de configuração que podem estar afetando sua capacidade de seja se conectar a nossos serviços de nuvem ou gerenciar senhas no seu ambiente local do AD.
Recomendamos, executar esta etapa somente após tentar as duas primeiras etapas descritas acima.

 1. Baixar a versão mais recente do Azure AD Connect [aqui](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Desde que você já tiver instalado o Azure AD Connect, você precisará somente realizar uma atualização in-loco para atualizar sua instalação do Azure AD Connect para a versão mais recente.
 3. Executar o pacote baixado e siga na tela instruções para atualizar a máquina Azure AD Connect.  Não há etapas manuais adicionais são necessárias, a menos que você personalizou o limite de regras de sincronização de caixa, nesse caso, você deve **fazer o backup antes de continuar com a atualização e manualmente reimplantá-las após terminar de**.

Essas etapas serão restabelecer sua conexão com o nosso serviço de nuvem e resolver qualquer interrupções que você pode estar enfrentando.

Se instalar a versão mais recente do servidor Azure AD Connect não resolver o problema, recomendamos que você tente desativação e reativação write-back de senha como uma etapa final depois de instalar a última sincronização QFE.

Se isso não resolver o problema, é recomendável que você examine [Solucionar write-back de senha](#troubleshoot-password-writeback) e a [senha do Azure AD perguntas frequentes sobre o gerenciamento](active-directory-passwords-faq.md) para ver se o problema pode ser discutido ali.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Links para senha redefinir documentação
A seguir são links para todas as páginas de documentação de redefinição de senha do Azure AD:

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
* [**Como ele funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os diferentes seis componentes do serviço e o que cada
* [**Introdução**](active-directory-passwords-getting-started.md) - Aprenda a permitem aos usuários reiniciar e alterar suas senhas de nuvem ou local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar a aparência e a aparência e o comportamento do serviço às necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) – Saiba como implantar rapidamente e efetivamente gerenciar senhas em sua organização
* [**Obter ideias**](active-directory-passwords-get-insights.md) - Saiba mais sobre nossos recursos de geração de relatórios integrados
* [**Perguntas Frequentes**](active-directory-passwords-faq.md) - Obtenha respostas para perguntas frequentes
* [**Saiba mais**](active-directory-passwords-learn-more.md) - vá profunda os detalhes técnicos de como funciona o serviço



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
