<properties
    pageTitle="Azure AD Connect integridade perguntas Frequentes"
    description="Estas perguntas Frequentes respondem dúvidas sobre integridade de conectar-se do Azure AD. Essas perguntas Frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect integridade perguntas frequentes (FAQ)

Estas perguntas Frequentes respondem dúvidas sobre integridade de conectar-se do Azure AD. Essas perguntas Frequentes abordam dúvidas sobre como usar o serviço, incluindo o modelo de cobrança, recursos, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais



**P: posso gerenciar vários diretórios do Azure AD. Como alternar para aquele com o Azure Active Directory Premium?**

Você pode alternar entre diferentes locatários do Azure AD selecionando atualmente conectado em nome de usuário no canto superior direito e escolhendo a conta apropriada. Se a conta não estiver listada aqui, selecione Sair e use as credenciais de administrador global do diretório que tem o Azure Active Directory Premium habilitado para entrar.

## <a name="installation-questions"></a>Perguntas sobre a instalação



**P: qual é o impacto da instalação do agente de integridade de conectar-se de AD Azure em servidores individuais?**

O impacto de instalar o Microsoft Azure AD conectar integridade agentes ADFS, servidores Proxy de aplicativo Web, servidores do Azure AD Connect (sycn), controladores de domínio é mínimo relacionadas com CPU, consumo de memória largura de banda e armazenamento.

Os números abaixo são uma aproximação.

- Consumo de CPU: ~ 1% de aumento
- Consumo de memória: até 10% da total de memória do sistema

>[AZURE.NOTE] Em caso do agente sendo capaz de se comunicar com o Azure, o agente armazena os dados localmente, até o limite máximo definido. O agente sobrescreverá os dados "em cache" em uma base "menos recentemente atendido".

- Armazenamento de buffer local para Azure AD conectar agentes de integridade: ~ 20 MB
- Para servidores do AD FS, é recomendável que você provisionar um espaço de disco de 1024 MB (1 GB) para o canal de auditoria do AD FS para agentes de integridade do Azure AD conectar processar todos os dados de auditoria antes que ele será substituído.

**P: posso terá que reinicializar meus servidores durante a instalação do Azure AD conectar agentes de integridade do?**

Não. A instalação dos agentes não exige a reinicialização do servidor. No entanto, a instalação de algumas das etapas de pré-requisito pode exigir a reinicialização do servidor.

Por exemplo, no Windows Server 2008 R2 a instalação do .net Framework 4,5 requer a reinicialização do servidor.


**P: faz Azure AD conectar integridade Services funcionam através de um proxy http passagem?**

Sim.  Para em indo operações, você pode configurar o agente de integridade para encaminhar solicitações http saída usando um HTTP Proxy. Para obter mais informações, consulte [Configurar Azure AD conectar agentes de integridade usar HTTP Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Se você precisar configurar um proxy durante o registro do agente, você talvez precise modificar as configurações de Proxy do Internet Explorer com antecedência.
1. Abrir o Internet Explorer -> Configurações -> Internet conexões -> Opções -> configurações da LAN.
2. Selecione usar um servidor Proxy para sua LAN.
3. Se você tiver portas de proxy diferentes para HTTP e HTTPS/seguro, selecione Avançado.

**P: serviços de saúde do Azure AD conectar suporta autenticação básica ao conectar com Proxies Http?**

Não. Um mecanismo para especificar o nome de usuário/senha aleatório para autenticação básica não é suportado atualmente.


**P: qual versão do AD DS são compatíveis com o Azure AD conectar integridade para o AD DS?**

Monitoramento do AD DS é suportado enquanto instalado nas seguintes versões de sistema operacional:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Perguntas de operações



**P: eu preciso para habilitar a auditoria em meus servidores de Proxy do AD FS aplicativo ou Meus servidores de Proxy de aplicativo da Web?**

Não, auditoria não precisa ser habilitada nos servidores Proxy de aplicativo da Web (WAP). Ativá-lo nos servidores do AD FS.


**P: como Azure AD conectar alertas de integridade obter resolvidas?**

Alertas de integridade conectar Azure AD obter resolvidas em uma condição de sucesso. Azure AD conectar agentes de integridade detectar e relatar as condições de sucesso serviço periodicamente. Para alguns alertas, a supressão é baseado em tempo. Em outras palavras, se a mesma condição de erro não é observada dentro de 72 horas de geração de alerta, o alerta automaticamente é resolvido.




**P: quais portas do firewall precisa abrir para o Azure AD conectar integridade agente funcione?**

Você precisa ter as portas TCP/UDP 443 e 5671 aberto para que o agente Azure AD de integridade conectar sejam capazes de se comunicar com os pontos de extremidade do serviço de integridade do Azure AD.


**P: por que vejo dois servidores com o mesmo nome no Azure AD conectar integridade do Portal do?**

Quando você remove um agente de um servidor, o servidor não é removido automaticamente a partir do Portal do Azure AD conectar.  Se você manualmente removido um agente de um servidor ou removido o próprio servidor, você precisa excluir manualmente a entrada do servidor do portal do Azure AD conectar integridade. Para obter mais informações, vá para [Excluir uma instância do servidor ou serviço.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Se você recriar a imagem de um servidor ou criou um novo servidor com os detalhes da mesmos (como o nome do computador) e não removeu o servidor já registrado do portal do Azure AD conectar integridade, instalado o agente no novo servidor, você pode ver duas entradas com o mesmo nome.  
Nesse caso, você deve excluir a entrada pertencentes ao servidor mais antigo manualmente. Os dados para este servidor devem ser desatualizados.

## <a name="related-links"></a>Links relacionados

* [Azure AD Connect integridade](active-directory-aadconnect-health.md)
* [Instalação do agente de integridade do Azure AD Connect](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect operações de integridade](active-directory-aadconnect-health-operations.md)
* [Usando o Azure AD conectar integridade com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Usando o Azure AD conectar integridade para sincronização](active-directory-aadconnect-health-sync.md)
* [Usando o Azure AD conectar integridade com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versões de integridade do Azure AD Connect](active-directory-aadconnect-health-version-history.md)
