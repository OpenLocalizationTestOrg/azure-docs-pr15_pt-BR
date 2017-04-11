<properties
   pageTitle="Azure AD Connect: Atualização de uma versão anterior | Microsoft Azure"
   description="Explica os diferentes métodos para atualizar para a última versão do Azure Active Directory conectar, incluindo atualização in-loco e migração de giro."
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
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Atualização de uma versão anterior para o mais recente
Este tópico descreve os diferentes métodos que você pode usar para atualizar sua instalação do Azure AD Connect para a versão mais recente. Recomendamos que você mantenha-se atualizado com as versões do Azure AD Connect. As etapas descritas em [gire migração](#swing-migration) também são usadas quando você faz uma configuração substancial alterar.

Se desejar atualizar de DirSync, consulte [atualização da ferramenta de sincronização do Azure AD (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Há algumas diferentes estratégias de atualização Azure AD Connect.

Método | Descrição
--- | ---
[Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) | Para clientes com uma instalação expressa, este é o método mais fácil.
[Atualização in-loco](#in-place-upgrade) | Se você tiver um único servidor, atualize o instalação no local no mesmo servidor.
[Gire a migração](#swing-migration) | Com dois servidores, você pode preparar um dos servidores com a nova versão ou configuração e alterar o active server quando estiver pronto.

Para permissões necessárias, consulte [permissões necessárias para atualização](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Atualização in-loco
Uma atualização in-loco funciona para mover do Azure AD Sync ou Azure AD Connect. Ele não funcione para DirSync ou para uma solução com FIM + Azure AD conector.

Este método é preferencial quando você tem um único servidor e menor que cerca de 100.000 objetos. Se houver qualquer alteração às regras prontos de sincronização, uma importação completa e sincronização completa ocorrerem após a atualização. Isso garante que a nova configuração é aplicada a todos os objetos existentes no sistema. Isso pode levar algumas horas, dependendo do número de objetos no escopo do mecanismo de sincronização. O Agendador de sincronização delta normal, por padrão a cada 30 minutos, está suspenso mas continua de sincronização de senha. Você pode considerar fazer a atualização in-loco durante um final de semana. Se não houver nenhuma alteração na configuração de prontos com a nova versão do Azure AD Connect, uma importação/sincronização delta normal será iniciado em vez disso.  
![Atualização in-loco](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se você tiver feito alterações para regras de sincronização de prontos, esses serão definidos para configuração padrão na atualização. Para certificar-se de que sua configuração é mantida entre atualizações, verifique se que as alterações são feitas conforme descrito em [práticas recomendadas para a alteração da configuração padrão](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Gire a migração
Se você tiver uma implantação complexa ou muito muitos objetos, talvez seja prático fazer uma atualização in-loco no sistema live. Isso pode para alguns clientes levar vários dias e durante esse tempo nenhuma alteração delta serão processadas. Este método também é usado quando você planeja fazer alterações substanciais sua configuração e você quiser experimentá-las antes de elas são enviadas para a nuvem.

O método recomendado para esses cenários é usar uma migração de giro. Você precisa (pelo menos) dois servidores, um ativo e um servidor de teste. O active server (linhas azuis sólidas na imagem abaixo) é responsável pela carga de produção ativa. O servidor de teste (roxos tracejadas na imagem abaixo) está preparado com a nova versão ou configuração e quando totalmente estiver pronto, este servidor é feito ativo. O servidor active anterior, agora com a versão antiga ou configuração instalado, é feito o servidor de teste e atualizado.

Os dois servidores podem usar diferentes versões. Por exemplo, o servidor ativo que você planeja encerrar pode usar a sincronização do Azure AD e o novo servidor de preparação pode usar Azure AD Connect. Se você usar a migração de giro para desenvolver uma nova configuração é uma boa ideia ter as mesmas versões nos dois servidores.  
![Servidor de teste](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Observação: Ele foi observado que alguns clientes preferem ter três ou quatro servidores para esse cenário. Quando o servidor de teste é atualizado, você não tem um servidor de backup no caso de uma [recuperação de dados](active-directory-aadconnectsync-operations.md#disaster-recovery). Com três ou quatro servidores, um conjunto de servidores de principal/reserva com a nova versão pode estar preparado, garantindo que sempre há um servidor de teste pronto para assumir.

Estas etapas também funciona para mover de sincronização do Azure AD ou uma solução com FIM + Azure AD conector. Essas etapas não funcionam para DirSync, mas o mesmo método de migração (também chamado de implantação paralela) de giro com etapas para DirSync pode ser encontrado na [Atualização do Azure Active Directory sincronizar (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Gire as etapas de migração

1. Se você usa o Azure AD Connect em ambos os servidores e planeja fazer apenas uma alteração de configuração, verifique se o seu servidor active e servidor intermediário são ambos usando a mesma versão. Que facilita a comparar as diferenças mais tarde. Se você estiver atualizando do Azure AD Sync, esses servidores têm versões diferentes. Se você estiver atualizando de uma versão mais antiga do Azure AD Connect, é uma boa ideia começar com os dois servidores usando a mesma versão, mas não é necessário.
2. Se você fez algumas configuração personalizada e ele não está no seu servidor intermediário, siga as etapas em [Mover configuração personalizada de ativo para servidor de teste](#move-custom-configuration-from-active-to-staging-server).
3. Se você estiver atualizando de uma versão anterior do Azure AD Connect, atualize o servidor de teste para a versão mais recente. Se você estiver movendo do Azure AD Sync, em seguida, instale o Azure AD Connect no seu servidor intermediário.
4. Permitir que o mecanismo de sincronização executar importação completa e sincronização completa no seu servidor intermediário.
5. Verifique se que a nova configuração não causou inesperadas alterações usando as etapas em **Verificar** em [Verificar se a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se algo não é sincronização e como esperada, correta, executar importação e verificar até que os dados com uma boas aparência. Estas etapas podem ser encontradas no tópico vinculado.
6. Alternar o servidor de teste para ser o servidor ativo. Esta é a etapa final **Alternar active server** em [Verificar a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se você estiver atualizando Azure AD Connect, atualize o servidor agora no modo para a versão mais recente de teste. Siga as mesmas etapas antes de obter os dados e a configuração atualizada. Se você atualizou do Azure AD Sync, agora você pode desativar e encerrar o servidor antigo.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Mover configuração personalizada de ativos para servidor de teste
Se você tiver feito alterações de configuração para o servidor ativo, você precisa certificar-se de que as mesmas alterações são aplicadas para o servidor de teste.

As regras de sincronização personalizadas que você criou podem ser movidas com o PowerShell. Outras alterações devem ser aplicadas da mesma maneira em ambos os sistemas e não podem ser migradas.

Coisa que você deve garantir é configurada da mesma maneira em ambos os servidores:

- Conexão florestas mesmo.
- Qualquer domínio e OU filtragem.
- Os mesmos recursos opcionais, como sincronização de senha e senha write-back.

**Mover regras de sincronização**  
Para mover uma regra personalizada de sincronização, faça o seguinte:

1. Abra o **Editor de regras de sincronização** no servidor active.
2. Selecione sua regra personalizada. Clique em **Exportar**. Isso abre uma janela do bloco de notas. Salve o arquivo temporário com uma extensão de PS1. Isso torna um script do PowerShell. Copie o arquivo ps1 para o servidor de teste.  
![Exportação de regra de sincronização](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. O GUID de conector é diferente no servidor de preparação e deve ser alterado. Para obter o GUID, inicie o **Editor de regras de sincronização**, selecione uma das regras de prontos que representa o mesmo sistema conectado e clique em **Exportar**. Substitua o GUID no seu arquivo PS1 com o GUID do servidor intermediário.
4. Em um prompt do PowerShell, execute o arquivo PS1. Isso criará a regra personalizado de sincronização no servidor intermediário.
5. Se você tiver várias regras personalizadas, repita para todas as regras personalizadas.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [integrar suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md).
