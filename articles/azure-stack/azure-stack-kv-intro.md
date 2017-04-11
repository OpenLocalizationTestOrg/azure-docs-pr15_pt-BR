<properties
    pageTitle="Introdução de pilha chave cofre Azure | Microsoft Azure"
    description="Saiba como o Azure pilha chave cofre gerencia chaves e senhas"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Introdução à chave cofre na pilha Azure #

## <a name="before-you-start"></a>Antes de começar

Este artigo pressupõe o seguinte:

- O leitor tem acesso a uma assinatura com o Azure chave cofre habilitado.
- O SDK do PowerShell Azure está configurado e disponível.
- Para a versão de TP2, todas as operações de locatário voltados podem ser executadas somente a partir do PowerShell.

## <a name="key-vault-basics"></a>Noções básicas do Cofre de chave

Cofre de chave na pilha do Azure ajuda a proteger as chaves de criptografia e usam senhas que aplicativos e serviços de nuvem. Usando a tecla cofre, você pode criptografar chaves e senhas (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos. pfx e senhas).

Chave cofre simplifica o processo de gerenciamento de chaves e permite que você mantenha o controle de chaves que acessar e criptografar seus dados. Os desenvolvedores podem criar chaves para desenvolvimento e teste em minutos e depois migrá-los diretamente às chaves de produção. Os administradores de segurança podem conceder (e revogar) permissão para chaves, conforme necessário.

Qualquer pessoa com uma assinatura do Azure pilha pode criar e usar compartimentos chaves. Embora chave cofre benefícios desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado por um administrador que gerencia outros serviços de pilha do Azure para uma organização. Por exemplo, esse administrador pode entrar com uma assinatura do Azure pilha, crie um cofre para a organização no qual deseja armazenar chaves e, em seguida, ser responsável por essas tarefas operacionais:

- Criar ou importar uma chave ou segredo
- Revogar ou excluir uma chave ou segredo
- Autorizar usuários ou aplicativos para acessar o Cofre de chave, para que eles possam gerenciar ou usar suas chaves e segredos
- Configurar o uso da chave (por exemplo, assinar ou criptografar)

Esse administrador pode fornecer aos desenvolvedores URIs para chamar a partir de seus aplicativos e fornecer um administrador de segurança com as informações de log de uso da chave.

Os desenvolvedores também podem gerenciar as teclas diretamente, usando APIs. Para obter mais informações, consulte Guia do desenvolvedor chave cofre.

## <a name="scenarios"></a>Cenários

A tabela a seguir descreve alguns dos cenários onde chave cofre podem ajudar a atender às necessidades dos desenvolvedores e administradores de segurança:


### <a name="developer-for-an-azure-stack-application"></a>Desenvolvedor para um aplicativo do Azure pilha

**Problema**: quero escrever um aplicativo para pilha Azure que usa chaves para criptografia e assinatura, mas eu quero que esses arquivos para ser externo de meu aplicativo para que a solução é adequada para um aplicativo que for distribuído geograficamente.

**Instrução**: chaves são armazenadas em um cofre e invocadas pelo URI quando necessário.


### <a name="developer-for-software-as-a-service-saas"></a>Desenvolvedor do software como um serviço (SaaS)

**Problema:** Não quero que a responsabilidade ou responsabilidade potencial para teclas de locatário dos meus clientes e senhas.

**Instrução:** Os clientes podem importar suas próprias chaves para pilha do Azure e gerenciá-los. Desejo clientes proprietário e gerenciar suas chaves para que eu possa se concentrar em fazendo o que posso fazer melhor, que fornece os principais recursos de software.


### <a name="chief-security-officer-cso"></a>Diretor de segurança (CSO)

**Problema:** Eu quero certificar-se de que minha organização está no controle sobre o ciclo de vida de chave e pode monitorar o uso da chave.

**Instrução** Chave cofre foi projetado para que a Microsoft não ver ou extrair suas chaves.  Quando um aplicativo precisa executar operações de criptografia usando as teclas de clientes, chave cofre faz isso em nome do aplicativo. O aplicativo não verá chaves dos clientes.  Embora podemos usar vários serviços de pilha do Azure e recursos, eu quero gerenciar as chaves de um único local na pilha do Azure. O cofre fornece uma única interface, independentemente de compartimentos quantos você tem na pilha do Azure, quais regiões eles suporte e quais aplicativos usá-los.

## <a name="next-steps"></a>Próximas etapas

[Introdução ao Cofre chave](azure-stack-kv-getting-started.md)
