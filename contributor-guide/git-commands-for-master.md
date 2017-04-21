<properties pageTitle="Comandos de gito para criar um novo artigo ou atualizar um artigo existente" description="Etapas para trabalhar com o Azure técnico GitHub repositórios de conteúdo." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>Comandos de gito para criar um novo artigo ou atualizar um artigo existente


## <a name="standard-process-working-from-master"></a>Processo padrão (trabalhando do mestre)
Siga as etapas neste artigo para criar uma ramificação local de trabalho em seu computador para que você possa criar um novo artigo para a seção de documentação técnica do azure.microsoft.com ou atualizar um artigo existente.

1. Inicie gito Bash (ou a ferramenta de linha de comando usada para gito).

 **Observação:** Se você estiver trabalhando no repositório público, altere azure-conteúdo-pr ao azure-conteúdo em todos os comandos.

2. Alterar para azure-conteúdo-pr:

        cd azure-content-pr
3. Confira a ramificação mestre:

        git checkout master

4. Crie uma nova ramificação de trabalho local derivada da filial mestre:

        git pull upstream master:<working branch>


5. Mover para a nova ramificação de trabalho:

        git checkout <working branch>

6. Informe seu bifurcação que criou a ramificação de trabalho local:

        git push origin <working branch>

7. Criar seu novo artigo ou fazer alterações em um artigo existente. Use o Windows Explorer para abrir e criar novos arquivos de redução e usar Atom (http://atom.io) como editor redução. Depois que você criou ou modificou seu artigo e imagens, vá para a próxima etapa.

8. Adicionar e confirmar as alterações feitas:

        git add .
        git commit –m "<comment>"
        
   Ou adicionar somente a específica arquivos que você modificou:

        git add <file path>
        git commit –m "<comment>"

   Se você excluir arquivos, é preciso usar isto:
   
        git add --all
        git commit -m "<comment>"

9. Atualize sua ramificação local de trabalho com as alterações de upstream:

        git pull upstream master

10. Enviar as alterações para sua bifurcação no GitHub:

        git push origin <working branch>

12. Quando você estiver pronto para enviar seu conteúdo para a ramificação mestre upstream para preparar, validação, e/ou de publicação, na UI GitHub, cria uma solicitação de recepção de seu bifurcação para a ramificação mestre.

13. Se você for um funcionário trabalhando no repositório particular, as alterações que você enviar são automaticamente transferidas e um link de preparação escrito à solicitação de recepção. Revise o conteúdo em estágios e entrar os comentários de solicitação de recepção adicionando o comentário **#sign-off** .  Isso indica que as alterações estão prontas para ser enviado ao vivo.  Se não quiser que a solicitação de recepção seja aceita - se você está armazenando apenas temporariamente as alterações - adicione anotação **#hold-desativar** à solicitação de recepção.

14. O acceptor de solicitação de recepção analisa sua solicitação de recepção, fornece comentários e/ou aceita a solicitação de recepção. 

15. Opcionalmente, verifique se seu artigo publicado ou alterações no

 http://Azure.microsoft.com/documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Publicação

- Artigos são publicados em aproximadamente 10:00 AM e 3:00 PM hora do Pacífico, de segunda a sexta. Pode levar até 30 minutos para artigos para aparecer online após a publicação. Lembre-se de que sua solicitação de recepção tem a serem mescladas por um revisor de solicitação de recepção antes que as alterações podem ser incluídas na próxima publicação agendada executar. Você precisa trabalhar com seu Revisor de solicitação de recepção antecedência para garantir que uma solicitação de recepção mesclada para uma publicação específica executar. Caso contrário, PRs são analisadas na ordem em que eles foram enviados.

- Se você for um funcionário trabalhando no repositório particular, todas as solicitações de recepção estão sujeitos a regras de validação que precisam ser resolvidos antes que a solicitação de recepção pode ser mesclada. 

## <a name="working-with-release-branches"></a>Trabalhando com ramificações de lançamento

Quando você estiver trabalhando com uma ramificação de lançamento, a melhor maneira de criar uma ramificação local de trabalho a partir da ramificação de lançamento é use esta sintaxe de comando:

    git checkout upstream/<upstream branch name> -b <local working branch name>

Isso cria a ramificação local diretamente a partir da ramificação upstream, evitando qualquer mesclando local.

