<properties
    pageTitle="Sincronização do Azure AD Connect: conceitos técnicos | Microsoft Azure"
    description="Explica os conceitos técnicos de sincronização do Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: conceitos técnicos
Este artigo é um resumo do tópico [Entendendo a arquitetura](active-directory-aadconnectsync-technical-concepts.md).

Sincronização do Azure AD Connect constituída de acordo com uma plataforma de sincronização metadiretório sólida.
As seções a seguir apresentam os conceitos de sincronização metadiretório.
Baseando MIIS, ILM e FIM, os serviços de sincronização do Azure Active Directory fornece a plataforma próxima para se conectar a fontes de dados, sincronizando dados entre fontes de dados, bem como o provisionamento e desprovisionamento de identidades.

![Conceitos técnicos](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

As seções a seguir fornecem mais detalhes sobre os seguintes aspectos do serviço de sincronização de FIM:

- Conector
- Fluxo de atributo
- Espaço do conector
- Metaverso
- Provisionamento

## <a name="connector"></a>Conector

Os módulos de código que são usados para se comunicar com um diretório conectado são chamados de conectores (conhecidas anteriormente como agentes de gerenciamento (MAs)).

Eles são instalados no computador executando o Azure AD Connect sincronização.
Os conectores fornecem a capacidade de agentes para conversar usando protocolos de sistema remoto em vez de depender de implantação de agentes especializados. Isso significa que a redução de riscos e tempos de implantação, especialmente quando lidando com sistemas e aplicativos críticos.

Na imagem acima, o conector é sinônimo de espaço do conector mas abrange toda a comunicação com o sistema externo.

O conector é responsável por todos os importar e exportar funcionalidade para o sistema e libera os desenvolvedores da necessidade de compreender como se conectar a cada sistema nativamente ao usar o provisionamento declarativa personalizar transformações de dados.

Importa e exporta só ocorre quando agendada, permitindo isolamento outro alterações que ocorrem dentro do sistema, desde que as alterações não propagar automaticamente à fonte de dados conectada. Além disso, os desenvolvedores também podem criar seus próprios conectores para conectar-se a praticamente qualquer fonte de dados.

## <a name="attribute-flow"></a>Fluxo de atributo

O metaverso é o modo de exibição consolidado de todas as identidades unidas das vizinhas espaços de conector. Na figura acima, o fluxo de atributo é representado por linhas com setas para o fluxo de entrada e de saída. Fluxo de atributo é o processo de copiando ou transformando dados de um sistema para outra e todos atributo fluxos (entrados ou saídos).

Fluxo de atributo ocorre entre espaço do conector e o metaverso bidirecional quando operações de sincronização (completo ou delta) estão agendadas para ser executado.

Fluxo de atributo somente ocorre quando essas sincronizações são executadas. Fluxos de atributo são definidos nas regras de sincronização. Eles podem ser entrada (ISR na imagem acima) ou saída (OSR na imagem acima).

## <a name="connected-system"></a>Sistema conectado

Sistema conectado (também conhecidos como diretório conectado) é se referir ao sistema remoto Azure AD Connect sincronização se conectou a leitura e gravação e dados de identidade de e para.

## <a name="connector-space"></a>Espaço do conector

Cada fonte de dados conectada é representado por um subconjunto filtrado os objetos e atributos no espaço do conector.
Isso permite que o serviço de sincronização operar localmente sem a necessidade de entrar em contato com o sistema remoto ao sincronizar os objetos e restringe interação para importa e exporta apenas.

Quando a fonte de dados e o conector tem a capacidade de fornecer uma lista das alterações (uma importação delta), em seguida, a eficiência operacional aumenta como apenas alterações desde o último ciclo de sondagem são trocadas. Espaço do conector isola a fonte de dados conectada alterações propagar automaticamente exigindo que o cronograma de conector importa e exporta. Este seguros adicionado concede tranquilidade durante o teste, visualização ou confirmando a próxima atualização.

## <a name="metaverse"></a>Metaverso

O metaverso é o modo de exibição consolidado de todas as identidades unidas das vizinhas espaços de conector.

Como identidades vinculadas e autoridade está atribuída para vários atributos por meio de mapeamentos de fluxo de importação, o objeto do metaverso central começa a agregar informações de vários sistemas. Deste fluxo de atributo de objeto, mapeamentos transportam informações para sistemas de saída.

Objetos são criados quando um sistema autoritativo projetos-los no metaverso. Assim que todas as conexões são removidas, o objeto do metaverso é excluído.

Objetos no metaverso não podem ser editados diretamente. Todos os dados no objeto devem ser contribuiu com por meio de fluxo de atributo. O metaverso mantém persistentes conectores com cada espaço de conector. Esses conectores não exigem reavaliação para cada sincronização executar. Isso significa que sincronização do Azure AD Connect não tem que localizar o objeto remoto correspondente a cada vez. Isso evita a necessidade de agentes dispendiosos impedir alterações nos atributos que normalmente seria responsáveis por correlação os objetos.

Ao descobrir novas fontes de dados que podem ter objetos preexistentes que precisam ser gerenciados, sincronização do Azure AD Connect usa um processo chamado uma regra de junção para avaliar candidatos em potencial com o qual deseja estabelecer um vínculo.
Depois que o link for estabelecido, essa avaliação não ocorrer novamente e fluxo de atributo normal pode ocorrer entre a fonte de dados conectada remota e o metaverso.

## <a name="provisioning"></a>Provisionamento

Quando um projetos de origem autoritativas um novo objeto no metaverso um novo objeto de espaço do conector pode ser criado no outro conector que representa uma fonte de dados conectada downstream.

Isso naturalmente estabelece um link e fluxo de atributo poderá continuar bidirecional.

Sempre que uma regra determina que um novo objeto de espaço do conector precisa ser criado, ele é chamado de provisionamento. No entanto, como essa operação somente ocorre dentro do espaço de conector, ele não transferidos para a fonte de dados conectados até que seja executada uma exportação.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure AD conectar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
