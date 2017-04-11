<properties
    pageTitle="Perguntas Frequentes: Gerenciamento de senha do Azure AD | Microsoft Azure"
    description="Perguntas frequentes (FAQ) sobre o gerenciamento de senha no Azure AD, incluindo redefinição de senha, registro, relatórios e Write-back para o Active Directory local."
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
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="password-management-frequently-asked-questions"></a>Perguntas frequentes sobre o gerenciamento de senha

> [AZURE.IMPORTANT] **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).

A seguir estão algumas perguntas frequentes para tudo relacionado ao gerenciamento de senha.

Se você encontrar-se com uma pergunta que você não souber a resposta, ou se estiver procurando ajuda com um problema específico que você está enfrentando, você pode ler em abaixo para ver se podemos abordados ele já.  Se nós ainda não começou, não se preocupe! Fique à vontade para fazer perguntas, você tem que não sejam abordadas aqui nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) e será voltamos a você assim que possível.

Essas perguntas Frequentes é dividido em seções a seguir:

- [**Perguntas sobre o registro de redefinição de senha**](#password-reset-registration)
- [**Perguntas sobre redefinição de senha**](#password-reset)
- [**Perguntas sobre relatórios de gerenciamento de senha**](#password-management-reports)
- [**Perguntas sobre write-back de senha**](#password-writeback)

## <a name="password-reset-registration"></a>Registro de redefinição de senha
 - **P: meus usuários registrar seus próprios dados de redefinição de senha?**

 > **A:** Sim, desde que redefinição de senha estiver ativada, e eles são licenciados, pode ir para o portal de registro de redefinição de senha em http://aka.ms/ssprsetup para registrar suas informações de autenticação a ser usado com redefinição de senha. Os usuários também podem registrar indo para o painel de acesso em http://myapps.microsoft.com, clicando na guia de perfil e, em seguida, clicando no registrador para opção de redefinição de senha. Saiba mais sobre como obter seus usuários configurados para redefinir lendo como obter os usuários configurados para redefinição de senha a senha.

 - **P: é possível definir dados de redefinição de senha em nome de meus usuários?**

 > **A:** Sim, você pode fazer isso com DirSync ou PowerShell, ou por meio do portal de administração do Office ou de [Portal de gerenciamento do Azure](https://manage.windowsazure.com) . Saiba mais sobre esse recurso da postagem de blog privacidade aprimorada para Azure AD MFA e números de telefone de redefinição de senha e lendo Saiba como os dados são usados por senha redefine.

 - **P: posso sincronizar dados para perguntas de segurança do local?**

 > **A:** Não, isso não é possível hoje, mas estamos considerando-lo.

 - **P: meus usuários registrar dados de forma que outros usuários não podem ver esses dados?**

 > **A:** Sim, quando os usuários registrar dados usando o Portal de registro de redefinição de senha ele é salvo em campos de autenticação particulares que são visíveis apenas por administradores globais e o usuário si. Saiba mais sobre esse recurso da postagem de blog privacidade aprimorada para Azure AD MFA e números de telefone de redefinição de senha e lendo Saiba como os dados são usados por senha redefine.

 - **P: que tenho que meus usuários sejam registrados para eles poderem usar redefinição de senha?**

 > **A:** Não, se você definir informações suficientes autenticação em seu nome, os usuários não terão que registrar. Redefinição de senha funcionará bem como dados armazenados nos campos apropriados no diretório tenha sido formatada corretamente. Saiba que mais sobre lendo Saiba como os dados são usados pela redefinição de senha.

 - **P: é possível sincronizar ou definir o telefone de autenticação, os campos de Email de autenticação ou telefone alternativo de autenticação em nome de meus usuários?**

 > **A:** Não atualmente, mas estamos considerando ativar esse recurso.

 - **P: como o portal de registro souber quais opções para Mostrar meus usuários?**

 > **A:** O portal de registro de redefinição de senha só mostra as opções que você ativou para seus usuários na seção política de redefinição de senha de usuário da guia de configurar seu diretório. Isso significa que se você não ativar, digamos, perguntas de segurança, em seguida, os usuários não poderão registrar para essa opção.

 - **P: quando um usuário é considerado registrado?**

 > **A:** Um usuário é considerado registrado quando ele ou ela tem pelo menos partes N de informações de autenticação definido, onde N é o número de autenticação métodos necessários que você definiu no [Portal de gerenciamento do Azure](https://manage.windowsazure.com). Para saber mais, consulte Personalizando política de redefinição de senha de usuário.


## <a name="password-reset"></a>Redefinição de senha

 - **P: quanto tempo deve esperar para receber um email, SMS ou chamada telefônica de redefinição de senha?**

 > **A:** Email, mensagens SMS e chamadas telefônicas deve chegar em menos de 1 minuto, com as maiusculas e minúsculas normal sendo 5-20 segundos. Se você não receber a notificação nesse período de tempo, verifique a pasta Lixo eletrônico, que o número / email sendo contatado é aquele que você espera, e que os dados de autenticação no diretório esteja formatados corretamente. Para saber mais sobre a formatação de números de telefone e email endereços para uso com redefinição de senha Consulte Saiba como os dados são usados pela redefinição de senha.

 - **P: quais idiomas são suportados pelo redefinição de senha?**

 > **A:** A redefinição de senhas UI, mensagens de SMS e chamadas de voz estão localizados no mesmos 40 idiomas com suporte no Office 365. Esses são: árabe, búlgaro, chinês simplificado, chinês tradicional, croata, tcheco, dinamarquês, holandês, inglês, estoniano, finlandês, francês, alemão, grego, hebraico, híndi, húngaro, indonésio, italiano, japonês, cazaque, coreano, letão, lituano, malaio (Malásia), norueguês (Bokmål), polonês, português (Brasil), português (Portugal), romeno, russo, Sérvio (latino), eslovaco, esloveno, espanhol, sueco, tailandês, turco, ucraniano e vietnamita.

 - **P: quais partes da experiência de redefinição de senha obtém marca quando eu defino organizacional de identidade visual no meu diretório do configurar guia?**

 > **A:** O portal de redefinição de senha mostrará o logotipo da sua organização e também permitirá que você configure o contato seu link de administrador apontar para um email personalizado ou uma URL. Qualquer email que obtém enviado por redefinição de senha incluirá o logotipo da sua organização, cores (neste caso vermelhos), nome no corpo do email e personalizados do nome. Veja um exemplo com todos os elementos marca abaixo. Para saber mais, leia a redefinição de senha personalizando aparência.

  ![][001]

 - **P: como posso informe meus usuários sobre onde ir para redefinição de senhas?**

 > **A:** Você pode enviar aos usuários para https://passwordreset.microsoftonline.com diretamente, ou pode instruir a clicar no não consegue acessar o link de conta encontrado em qualquer tela de entrada ID de trabalho ou na escola. Você pode fique à vontade para publicar esses links (ou criar redirecionamentos de URL para eles) em qualquer lugar que seja facilmente acessado aos usuários.

 - **P: posso usar esta página em um dispositivo móvel?**

 > **A:** Sim, esta página funciona em dispositivos móveis.

 - **P: você oferece suporte desbloqueando contas do active directory local quando os usuários redefinir suas senhas?**

 > **A:** Sim, quando um usuário redefine sua senha e Write-back senha foi implantado com todas as versões do Azure AD Connect ou versões do Azure AD Sync 1.0.0485.0222 ou posterior, conta do usuário será automaticamente desbloqueada quando esse usuário redefine sua senha.

 - **P: como integrar o senha redefinir diretamente na minha experiência do usuário da área de trabalho entrar?**

 > **A:** Isso não é possível hoje. No entanto, se absolutamente você precisa desse recurso e um cliente do Azure AD Premium, você pode instalar o Gerenciador de identidades do Microsoft sem nenhum custo adicional e implantar a solução de redefinição de senha locais encontrada para resolver esse requisito.

 - **P: posso definir perguntas de segurança diferente para localidades diferentes?**

 > **A:** Não, isso não é possível hoje, mas estamos considerando-lo.

 - **P: quantas perguntas nós pode configurar para a opção de autenticação de perguntas de segurança?**

 > **A:** Você pode configurar até 20 perguntas de segurança personalizada no [Portal de gerenciamento do Azure](https://manage.windowsazure.com).

 - **P: quanto tempo podem ser as perguntas de segurança?**

 > **A:** Perguntas sobre segurança pode estar entre 3 e 200 caracteres.

 - **P: quanto tempo podem ser as respostas para as perguntas de segurança?**

 > **A:** As respostas sejam 3 a 40 caracteres.

 - **P: são duplicadas respostas para perguntas de segurança rejeitadas?**

 > **A:** Sim, nós rejeitar duplicadas respostas para perguntas de segurança.

 - **P: maio um usuário registrar mais de um da mesma pergunta de segurança?**

 > **A:** Não, quando um usuário registra uma determinada pergunta, ele ou ela pode não Registre-se para essa pergunta uma segunda vez.

 - **P: é possível definir um limite mínimo de perguntas de segurança para o registro e redefinir?**

 > **A:** Sim, um limite pode ser definido para registro e outro para redefinir. perguntas de segurança de 3 a 5 podem ser necessárias para o registro e 3 a 5 podem ser necessárias para redefinir.

 - **P: se um usuário tiver registrado mais do que o número máximo de perguntas necessárias para redefinir, como perguntas de segurança são selecionadas durante redefinição?**

 > **A:** Perguntas de segurança N são selecionadas aleatoriamente fora do número total de perguntas que um usuário registrou, onde N é o número mínimo de perguntas necessários para redefinição de senha. Por exemplo, se um usuário tem 5 perguntas de segurança registradas, mas somente 3 são necessários para redefinir, 3 desses 5 serão selecionada aleatoriamente e apresentados ao usuário na ocasião da redefinição. Se o usuário obtém as respostas para as perguntas errado, o processo de seleção ocorre novamente impedir a pergunta hammering.

 - **P: você impedir que usuários tentar redefinir muitas vezes em um período de hora abreviada sua senha?**

 > **A:** Sim, há vários recursos de segurança integrados do redefinição de senha. Os usuários só podem tentar 5 tentativas de redefinição de senha dentro de uma hora antes sendo bloqueadas por 24 horas. Os usuários só podem tentar validar um número de telefone 5 vezes dentro de uma hora antes sendo bloqueadas por 24 horas. Os usuários só podem tentar um método de autenticação única 5 vezes dentro de uma hora antes sendo bloqueadas por 24 horas.

 - **P: por quanto tempo são o email e a senha de uso único de SMS válidos?**

 > **A:** A duração da sessão para redefinição de senha é 105 minutos. Isso significa que, desde o começo da senha, redefina a operação, o usuário tem 105 minutos para redefinir sua senha. O email e a senha de uso único de SMS são inválidos depois que esse período de tempo expira.


## <a name="password-management-reports"></a>Relatórios de gerenciamento de senha

 - **P: quanto tempo leva para dados em relatórios de gerenciamento de senha?**

 > **A:** Após 5 a 10 minutos, os dados deverão aparecer nos relatórios de gerenciamento de senha. Ele alguns casos pode levar até uma hora apareça.

 - **P: como para filtrar os relatórios de gerenciamento de senha?**

 > **A:** Você pode filtrar os relatórios de gerenciamento de senha clicando na Lupa pequena à extrema direita dos rótulos de coluna, na parte superior do relatório (consulte a captura de tela). Se você quiser fazer uma filtragem mais sofisticadas, você pode baixar o relatório do excel e criar uma tabela dinâmica.

  ![][002]

 - **P: o que é o número máximo de eventos são armazenados nos relatórios de gerenciamento de senha?**

 > **A:** Até 1.000 senha eventos de registro de redefinição de senha ou redefinir são armazenados nos relatórios de gerenciamento de senha.  Estamos trabalhando para expandir esse número para incluir mais eventos.

 - **P: quanto os relatórios de gerenciamento de senha ir?**

 > **A:** Os relatórios de gerenciamento de senha mostram operações ocorrendo nos últimos 30 dias. Estamos atualmente analisando como fazer isso em um período de tempo maior. Por agora, se você precisar arquivar dados, você pode baixar os relatórios periodicamente e salvá-los em um local separado.

 - **P: existe um número máximo de linhas que podem ser exibidas nos relatórios de gerenciamento de senha?**

 > **A:** Sim, um máximo de 1.000 linhas pode aparecer em qualquer um dos relatórios gerenciamento de senha, se elas estão sendo mostradas na interface do usuário ou sendo baixados. Estamos atualmente analisando como aumentar esse limite.

 - **P: existe uma API para acessar a redefinição de senha ou o registro de dados do relatório?**

 > **A:** Sim, consulte a documentação a seguir para saber como você pode acessar a fluxo de dados de relatório de redefinição de senha.  [Saiba como acessar senha redefinir relatar eventos por programação](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Write-back de senha
 - **P: como funciona a senha write-back nos bastidores?**

 > **A:** Veja [que como senha write-back funciona](active-directory-passwords-learn-more.md#how-password-writeback-works) para uma explicação detalhada do que acontece quando você habilita write-back de senha, bem como dados fluem através do sistema de volta para seu ambiente local. Consulte que o [modelo de segurança de senha write-back](active-directory-passwords-learn-more.md#password-writeback-security-model) em write-back como senha funciona para saber como podemos garantir a que senha write-back é um serviço altamente seguro.

 - **P: como tempo senha write-back leva para funcionar?  Há um atraso de sincronização como com a sincronização de hash de senha?**

 > **A:** Senha write-back é instantânea. Ele é um pipeline síncrono que funciona bem diferente da sincronização de hash de senha. Senha write-back permite aos usuários obter comentários em tempo real sobre o sucesso da sua redefinição de senha ou alterar a operação. O tempo médio para um write-back bem-sucedida de uma senha é em 500 ms.

 - **P: quais tipos de contas senha write-back funcionam para?**

 > **A:** Senha write-back funciona para federados e senha Hash-sincronizados usuários.

 - **P: o write-back senha impor políticas de senha do meu domínio?**

 > **A:** Sim, senha write-back impõe idade de senha, histórico, complexidade, filtros e qualquer outra restrição que você pode colocar no lugar de senhas em seu domínio local.

 - **P: é write-back senha segura?  Como posso ter certeza de que eu não obter hackers?**

 > **A:** Sim, senha write-back é altamente seguro. Para ler mais sobre as 4 camadas de segurança implementada pelo serviço write-back de senha, confira o [modelo de segurança de senha write-back](active-directory-passwords-learn-more.md#password-writeback-security-model) no works de write-back como senha.




## <a name="links-to-password-reset-documentation"></a>Links para senha redefinir documentação
A seguir são links para todas as páginas de documentação de redefinição de senha do Azure AD:

* **Você está aqui porque você está tendo problemas para entrar?** Em caso afirmativo, [Veja como você pode alterar e redefinir sua própria senha](active-directory-passwords-update-your-own-password.md).
* [**Como ele funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os diferentes seis componentes do serviço e o que cada
* [**Introdução**](active-directory-passwords-getting-started.md) - Aprenda a permitem aos usuários reiniciar e alterar suas senhas de nuvem ou local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar a aparência e a aparência e o comportamento do serviço às necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) – Saiba como implantar rapidamente e efetivamente gerenciar senhas em sua organização
* [**Obter ideias**](active-directory-passwords-get-insights.md) - Saiba mais sobre nossos recursos de geração de relatórios integrados
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente solucionar problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) - vá profunda os detalhes técnicos de como funciona o serviço


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"
