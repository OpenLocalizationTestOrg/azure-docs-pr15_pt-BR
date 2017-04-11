<properties
   pageTitle="Azure AD Connect: Histórico de versão de versão | Microsoft Azure"
   description="Este tópico lista todas as versões do Azure AD Connect e sincronização do Azure AD"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Histórico de versão de versão

A equipe do Active Directory do Azure atualiza regularmente Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi projetado para ajudá-lo a controlar as versões que foram lançadas e compreender se você precisa atualizar para a versão mais recente ou não.

Esta é a lista de tópicos relacionados:

Tópico |  
--------- | --------- |
Etapas para atualizar a partir do Azure AD Connect | Diferentes métodos para [atualização de uma versão anterior para o mais recente](active-directory-aadconnect-upgrade-previous-version.md) do Azure AD Connect lançamento.
Permissões necessárias | Para permissões necessárias para aplicar uma atualização, consulte [contas e permissões](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Baixar| [Conectar-se de download Azure AD](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Lançado: agosto de 2016

**Problemas corrigidos:**

- Alterações para sincronizar intervalo não ocorrem até após a conclusão do próximo ciclo de sincronização.
- Assistente do Azure AD Connect não aceitar a conta do Azure AD cujo nome de usuário começa com um sublinhado (\_).
- Assistente do Azure AD Connect não conseguir autenticar Azure AD conta fornecida se a senha da conta contém muitos caracteres especial. Mensagem de erro "não é possível validar credenciais. Um erro inesperado ocorreu." será retornado.
- A desinstalação do servidor intermediário desabilita a sincronização de senha no locatário do Azure AD e faz a sincronização de senha falha com o active server.
- Sincronização de senha falha em casos incomuns quando não há nenhum hash de senha armazenado no usuário.
- Quando o servidor do Azure AD Connect estiver habilitado para o modo de teste, senha write-back não está desativado temporariamente.
- Assistente do Azure AD Connect não mostram a sincronização de senha real e configuração de write-back de senha quando o servidor está em modo de teste. Ele sempre mostra-los como desativado.
- Alterações de configuração para sincronização de senha e senha write-back não são mantidas pelo Assistente do Azure AD Connect quando o servidor está no modo de teste.

**Melhorias:**

- Cmdlet do início-ADSyncSyncCycle atualizado para indicar se ele é capaz de iniciar um novo ciclo de sincronização com êxito ou não.
- Adicionado ADSyncSyncCycle parar o cmdlet para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
- Cmdlet parada ADSyncScheduler atualizado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
- Ao configurar [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md) no Assistente do Azure AD Connect, o atributo do AD do tipo "Teletex string" agora pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançadas: 2016 junho

**Fixo de problemas e aprimoramentos:**

- Azure AD Connect agora pode ser instalado em um servidor compatível com FIPS.
    - Para sincronização de senha, consulte [sincronização de senha e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Corrigido um problema onde um nome NetBIOS não pôde ser resolvido para o FQDN no conector do Active Directory.

## <a name="111800"></a>1.1.180.0
Lançamento: 2016 maio

**Novos recursos:**

- Avisa e ajuda a verificar os domínios se não-lo antes de executar o Azure AD Connect.
- Adicionado suporte para [Alemanha de nuvem da Microsoft](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Adicionado suporte para a infraestrutura de [nuvem da Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) mais recente com novos requisitos de URL.

**Fixo de problemas e aprimoramentos:**

- Adicionado ao Editor de regra de sincronização para tornar mais fácil de localizar regras de sincronização de filtragem.
- Desempenho aprimorado ao excluir um espaço de conector.
- Corrigido um problemas quando o mesmo objeto foi excluído e adicionado na mesma executar (chamados delete/Adicionar).
- Uma regra de sincronização desabilitada não serão mais reabilitar incluído objetos e atributos no esquema de atualização ou diretório de atualização.

## <a name="111300"></a>1.1.130.0
Lançamento: 2016 abril

**Novos recursos:**

- Adicionado suporte para atributos de valores múltiplos extensões de [Diretório](active-directory-aadconnectsync-feature-directory-extensions.md).
- Adicionado suporte para mais variações de configuração para [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) ser considerado qualificado para atualização.
- Adicionou alguns cmdlets do [scheduler personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançamento: 2016 março

**Problemas corrigidos:**

- Feitas-se de instalar Express não pode ser usado no Windows Server 2008 (pré-R2) desde a sincronização de senha não é suportado neste sistema operacional.
- Atualização de DirSync com uma configuração de filtro personalizado não funcionam como esperado.
- Ao atualizar para uma versão mais recente e nenhuma alteração na configuração, uma importação/sincronização completa não deve ser agendada.

## <a name="111100"></a>1.1.110.0
Lançamento: fevereiro de 2016

**Problemas corrigidos:**

- Atualização de versões anteriores não funciona se instalação não estiver na pasta padrão **C:\Program Files** .
- Se você instalar e cancelar a seleção de **Iniciar o processo de sincronização.** no final do Assistente de instalação, executar novamente o Assistente de instalação não irá habilitar o Agendador.
- O Agendador não funcionará como esperado em servidores onde o formato de data/hora não é en-US. Ele também bloqueará `Get-ADSyncScheduler` para retornar horas corretas.
- Se você instalou uma versão anterior do Azure AD Connect com ADFS como a opção de entrada e a atualização, você não pode executar o Assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Lançamento: fevereiro de 2016

**Novos recursos:**

- Recurso de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) para clientes de configurações do Express.
- Suporte para o administrador global usando MFA e Gerenciador no Assistente de instalação.
    - É preciso permitir que o proxy de também permitir o tráfego para https://secure.aadcdn.microsoftonline-p.com se você usar MFA.
    - Você precisa adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites confiáveis para MFA funcionar corretamente.
- Permitir alteração de método de entrada do usuário após a instalação inicial.
- Permitir [filtragem de domínio e OU](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no Assistente de instalação. Isso também permite conectando a florestas onde não todos os domínios estão disponíveis.
- [Agendador](active-directory-aadconnectsync-feature-scheduler.md) é integrado ao mecanismo de sincronização.

**Recursos promovidos da visualização para GA:**

- [Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md).
- [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).

**Novos recursos de visualização:**

- O novo padrão sincronizar ciclo intervalo é 30 minutos. Costumava ser 3 horas para todas as versões anteriores. Adiciona o suporte para alterar o comportamento de [Agendador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas corrigidos:**

- A página de domínios do DNS verificar sempre não reconhece os domínios.
- Solicita credenciais de administrador de domínio ao configurar ADFS.
- O local contas do AD não são reconhecidas pelo Assistente de instalação se localizado em um domínio com uma árvore diferente do DNS do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Lançado: dezembro de 2015

**Problemas corrigidos:**

- Sincronização de senha pode não funcionar quando você altera senhas no AD DS, mas funciona quando você definir senha.
- Quando você tiver um servidor proxy, autenticação Azure AD pode falhar durante a instalação ou un atualização na página configuração.
- Atualizando de uma versão anterior do Azure AD Connect completo do SQL Server falhará se você não for SA no SQL.
- Atualizando de uma versão anterior do Azure AD Connect com um controle remoto do SQL Server mostrará o erro "Não é possível acessar o banco de dados do SQL ADSync".

## <a name="1091250"></a>1.0.9125.0
Lançado: novembro de 2015

**Novos recursos:**

- Pode reconfigurar o ADFS para confiança Azure AD.
- Pode atualizar o esquema do Active Directory e gerar regras de sincronização.
- Pode desativar uma regra de sincronização.
- Pode definir "AuthoritativeNull" como um novo literal em uma regra de sincronização.

**Novos recursos de visualização:**

- [Azure AD conectar integridade para sincronização](active-directory-aadconnect-health-sync.md).
- Suporte para sincronização de senha de [Serviços de domínio do Azure AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Novo cenário com suporte:**

- Suporta vários organizações do Exchange local. Consulte [implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx) para obter mais informações.

**Problemas corrigidos:**

- Problemas de sincronização de senha:
    - Um objeto movido de fora do escopo para no escopo não terá sua senha sincronizada. Este incudes atributo filtragem e unidade Organizacional.
    - Selecionar uma nova OU para incluir em sincronia não exige uma sincronização de senha completo.
    - Quando um usuário desabilitado está habilitado a senha não sincronizar.
    - Fila de repetição de senha é infinita e o limite anterior de 5.000 objetos sejam descartados foi removido.
    - A [melhor solução de problemas](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Não é possível se conectar ao Active Directory com nível funcional de floresta do Windows Server 2016.
- Não é possível alterar o grupo usado para filtragem de grupo após a instalação inicial.
- Não é mais criará um novo perfil de usuário no servidor do Azure AD Connect para cada usuário fazendo uma alteração de senha com write-back de senha habilitada.
- Não é possível usar inteiro longo valores em sincronia escopos de regras.
- A caixa de seleção "dispositivo write-back" permanece desativada se houver controladores de domínio inacessível.

## <a name="1086670"></a>1.0.8667.0
Lançado: agosto de 2015

**Novos recursos:**

- O Assistente de instalação do Azure AD Connect agora está localizado a todos os idiomas do Windows Server.
- Suporte adicionado para conta desbloquear ao usar o gerenciamento de senha do Azure AD.

**Problemas corrigidos:**

- Assistente de instalação do Azure AD Connect falha se outro usuário continuará a instalação em vez da pessoa que iniciou pela primeira vez a instalação.
- Se uma desinstalação anterior falhar Azure AD Connect desinstalar sincronização do Azure AD Connect corretamente, não é possível reinstalar.
- Não é possível instalar o Azure AD Connect usando Express instalação se o usuário não está no domínio raiz da floresta ou se uma versão não inglesa do Active Directory é usada.
- Se o FQDN da conta de usuário do Active Directory não pode ser resolvido, uma mensagem de erro "Falha ao confirmar o esquema" falsos é mostrada.
- Se a conta usada no conector do Active Directory forem alterada fora do assistente, o assistente falhará em execuções subsequentes.
- Às vezes, o Azure AD Connect não instalar em um controlador de domínio.
- Não é possível ativar e desativar "Modo de preparação" se atributos de extensão foram adicionados.
- Senha write-back falha em algumas configurações devido a uma senha incorreta no conector do Active Directory.
- DirSync não pode ser atualizado se dn é usado na filtragem de atributo.
- Uso excessivo de CPU ao usar redefinição de senha.

**Recursos de visualização removidos:**

- O recurso de visualização de [que write-back usuário](active-directory-aadconnect-feature-preview.md#user-writeback) temporariamente foi removido com base nos comentários de nossos clientes de visualização. Ele será novamente adicionado mais tarde quando tiver retratado os comentários fornecidos.

## <a name="1086410"></a>1.0.8641.0
Lançado: junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de sincronização do Azure AD para Azure AD Connect.

**Novos recursos:**

- Instalação [expressa configurações](./connect/active-directory-aadconnect-get-started-express.md)
- Pode [Configurar ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Pode [Atualizar do DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introduzido [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode)

**Novos recursos de visualização:**

- [Write-back do usuário](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Write-back do dispositivo](active-directory-aadconnect-feature-device-writeback.md)
- [Extensões de diretório](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
Lançado: maio de 2015

**Novo requisito:**

- Sincronização do Azure AD agora requer o .net framework versão 4.5.1 esteja instalado.

**Problemas corrigidos:**

- Senha write-back do Azure AD está falhando com um erro de conectividade servicebus.

## <a name="104910413"></a>1.0.491.0413
Lançado: abril de 2015

**Fixo de problemas e aprimoramentos:**

- O Active Directory Connector não processa exclui corretamente se a Lixeira está habilitada e há vários domínios na floresta.
- O desempenho das operações de importação foi aprimorado para o Azure Active Directory Connector.
- Quando um grupo excedeu o limite de associação (por padrão, o limite é definido para 50 mil objetos), o grupo foi excluído no Active Directory do Azure. O novo comportamento é que o grupo permanecerá, um erro é lançado e não há novas alterações de associação serão exportadas.
- Um novo objeto não pode ser configurado se uma exclusão em estágios com o mesmo DN já está presente no espaço do conector.
- Alguns objetos são marcados para sendo sincronizados durante uma sincronização delta embora há nenhuma alteração testada no objeto.
- Forçar uma sincronização de senha também remove lista DC preferencial.
- CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novos recursos:**

- Uma junção agora pode conectar ao tipo de objeto "ANY" no MV.

## <a name="104850222"></a>1.0.485.0222
Lançamento: fevereiro de 2015

**Melhorias:**

- Desempenho aprimorado de importação.

**Problemas corrigidos:**

- Sincronização de senha honra o atributo de cloudFiltered usado por filtragem de atributo. Objetos filtrados não será mais no escopo para sincronização de senha.
- Em raras situações em que a topologia tinha muito muitos controladores de domínio, a sincronização de senha não funciona.
- "Interrompido-servidor" ao importar do Azure AD conector depois de gerenciamento de dispositivo foi habilitado no Azure AD/Intune.
- Ingressando em objetos de segurança externa (FSPs) de vários domínios na mesma floresta causa um erro de junção ambígua.

## <a name="104751202"></a>1.0.475.1202
Lançado: dezembro de 2014

**Novos recursos:**

- Agora há suporte para fazer a sincronização de senha com a filtragem de atributo com base. Para obter mais detalhes, consulte [a sincronização de senha com filtragem](active-directory-aadconnectsync-configure-filtering.md).
- O atributo msDS-ExternalDirectoryObjectID é gravada de volta para o AD. Isso adiciona o suporte para aplicativos do Office 365 usando OAuth2 para acessar ambos, Online e caixas de correio em uma implantação híbrida do Exchange local.

**Problemas de atualização de fixos:**

- Uma versão mais recente do assistente entrada está disponível no servidor.
- Um caminho de instalação personalizada foi usado para instalar o Azure AD Sync.
- A atualização de blocos de um critério de junção personalizado inválido.

**Outras correções:**

- Corrigido os modelos para o Office Pro Plus.
- Problemas de instalação fixo causados por nomes de usuário que começam com um traço.
- Fixo perder a configuração de sourceAnchor ao executar o Assistente de instalação uma segunda vez.
- Rastreamento ETW fixo para sincronização de senha

## <a name="104701023"></a>1.0.470.1023
Lançado: outubro de 2014

**Novos recursos:**

- Sincronização de senha de vários locais AD ao Azure AD.
- Instalação localizado UI a todos os idiomas do Windows Server.

**Atualização do AADSync 1.0 GA**

Se você já tiver instalado do Azure AD Sync, há uma etapa adicional que você deve tomar caso você alterou qualquer uma das regras prontos de sincronização. Depois que você atualizou para o 1.0.470.1023 solte, a sincronização regras que você modificou são duplicadas. Para cada regra de sincronização modificada, faça o seguinte:

- Localize a regra de sincronização você modificou e anote as alterações.
- Exclua a regra de sincronização.
- Localize a nova regra de sincronização criada pela sincronização do Azure AD e aplique novamente as alterações.

**Permissões para a conta do AD**

A conta AD deverá ter permissões adicionais possam ler o hash de senha do AD. As permissões para conceder são nomeadas "Replicar alterações de diretório" e "Replicar alterações de diretório tudo". Ambas as permissões são necessárias para poderá ler o hash de senha

## <a name="104190911"></a>1.0.419.0911
Lançado: setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
