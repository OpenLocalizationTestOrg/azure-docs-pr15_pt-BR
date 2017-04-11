<properties
    pageTitle="O que é Azure chave cofre? | Microsoft Azure"
    description="Azure Cofre de chave ajuda a proteger chaves de criptografia e senhas usadas por aplicativos de nuvem e serviços. Usando o Azure chave cofre, os clientes podem criptografar chaves e senhas (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados. Arquivos PFX e senhas) usando teclas protegidos por módulos de segurança de hardware (HSMs)."
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
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>O que é Azure chave cofre?

Azure Cofre de chave está disponível na maioria das regiões. Para obter mais informações, consulte a [chave do cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução

Azure Cofre de chave ajuda a proteger chaves de criptografia e senhas usadas por aplicativos de nuvem e serviços. Usando a tecla cofre, você pode criptografar chaves e senhas (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados. Arquivos PFX e senhas) usando teclas protegidos por módulos de segurança de hardware (HSMs). Para assurance adicionado, você pode importar ou gerar chaves em HSMs. Se você optar por fazer isso, processos da Microsoft suas chaves no FIPS 140-2 nível 2 HSMs validados (hardware e firmware).  

Chave cofre simplifica o processo de gerenciamento de chaves e permite que você mantenha o controle de chaves que acessar e criptografar seus dados. Os desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-los diretamente às chaves de produção. Os administradores de segurança podem conceder (e revogar) permissão para chaves, conforme necessário.

Use a tabela a seguir para melhor entender como chave cofre pode ajudar a atender às necessidades dos desenvolvedores e administradores de segurança.





| Função        | Descrição do problema           | Resolvido usando Azure cofre chave  |
| ------------- |-------------|-----|
| Desenvolvedor para um aplicativo do Azure      | "Quero escrever um aplicativo do Azure que usa chaves para criptografia e assinatura, mas eu quero estas teclas sejam externo de meu aplicativo para que a solução é adequada para um aplicativo que for distribuído geograficamente. <br/><br/>Também quero essas chaves e segredos devem ser protegidos, sem precisar escrever o código sozinho. Também quero essas chaves e segredos seja fácil para mim usar meus aplicativos, com um desempenho ideal." | Chaves √ são armazenadas em um cofre e invocadas pelo URI quando necessário.<br/><br/> √ Teclas são protegidas por Azure, usando módulos de segurança de hardware (HSMs), comprimentos de chave e algoritmos padrão da indústria.<br/><br/> Teclas de √ são processadas em HSMs que residem nos mesma dos data centers Azure como os aplicativos. Isso fornece maior confiabilidade e latência reduzida que se as teclas residem em um local separado, como o local.|
| Desenvolvedor do Software como um serviço (SaaS)      |"Não quero que a responsabilidade ou responsabilidade potencial para teclas de locatário dos meus clientes e senhas. <br/><br/>Desejo os clientes para o proprietário e gerenciar suas chaves para que eu possa se concentrar em fazendo o que posso fazer melhor, que fornece os principais recursos de software." | Clientes √ podem importar suas próprias chaves para Azure e gerenciá-los. Quando um aplicativo de SaaS precisa executar operações de criptografia usando as teclas de seus clientes, chave cofre faz essas operações em nome do aplicativo. O aplicativo não verá chaves dos clientes.|
| Officer chefe de segurança (CSO) | "Eu quiser saber que nossos aplicativos cumpram FIPS 140-2 nível 2 HSMs para gerenciamento de chave seguro. <br/><br/>Eu quero certificar-se de que minha organização está no controle sobre o ciclo de vida de chave e pode monitorar o uso da chave. <br/><br/>E embora podemos usar vários serviços Azure e recursos, quero gerenciar as chaves de um único local no Azure."     |√ HSMs são FIPS 140-2 nível 2 validados.<br/><br/>√ Chave cofre foi projetado para que a Microsoft não ver ou extrair suas chaves.<br/><br/>√ Quase em tempo real log de uso da chave.<br/><br/>√ cofre fornece uma única interface, independentemente de compartimentos de quantos você tem no Azure, quais regiões eles suporte e quais aplicativos usá-los. |


Qualquer pessoa com uma assinatura do Azure pode criar e usar compartimentos chaves. Embora chave cofre benefícios desenvolvedores e administradores de segurança, ele poderia ser implementado e gerenciado pelo administrador de uma organização que gerencia outros serviços do Azure para uma organização. Por exemplo, esse administrador seria entrar com uma assinatura do Azure, crie um cofre para a organização no qual deseja armazenar chaves e ser responsável por tarefas operacionais, como:

+ Criar ou importar uma chave ou segredo
+ Revogar ou excluir uma chave ou segredo
+ Autorizar usuários ou aplicativos para acessar o Cofre de chave, para que eles possam gerenciar ou usar suas chaves e segredos
+ Configurar o uso da chave (por exemplo, assinar ou criptografar)
+ Monitore o uso de chave

Este administrador, em seguida, seria fornecer aos desenvolvedores URIs para chamar a partir de seus aplicativos e forneça seu administrador de segurança com as informações de log de uso da chave. 

   ![Visão geral do Azure cofre chave][1]

Os desenvolvedores também podem gerenciar as teclas diretamente, usando APIs. Para obter mais informações, consulte [Guia do desenvolvedor chave cofre](key-vault-developers-guide.md).

## <a name="next-steps"></a>Próximas etapas

Para um tutorial de Introdução obtendo um administrador, consulte [Introdução ao Azure chave cofre](key-vault-get-started.md).

Para obter mais informações sobre o uso do registro em log para Cofre de chave, consulte [O log de Cofre de chave do Azure](key-vault-logging.md).

Para obter mais informações sobre o uso de chaves e senhas com o Azure chave cofre, consulte [sobre chaves, segredos e certificados](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
