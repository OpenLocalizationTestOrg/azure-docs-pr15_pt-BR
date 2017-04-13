# <a name="pull-request-comment-automation"></a>Retirar automação de comentário de solicitação

Usamos automação de comentário em comentários de solicitação de recepção para permitir que os colaboradores e processo de revisão de autores atribuir rótulos que orientam a solicitação de recepção.

| Comentário | O que ela faz | Disponibilidade|
| -------- |-------------|-------------|
|#aprovação | Quando o autor de um artigo digita o comentário **#sign-off** no fluxo de comentário, o rótulo **pronto para mesclar** é atribuído. | Pública e privada|
|#aprovação | Se um colaborador que não seja o autor listados tentar entrar em uma solicitação de recepção público usando o comentário **#sign-off** , uma mensagem é gravada para a solicitação de recepção indicando que o rótulo pode ser atribuído somente pelo autor. | Público |
|#Mantenha-desativado | Se você digitar **#hold-off** em um comentário de solicitação de recepção, ele remove o rótulo **pronto para mesclar** - caso você mudar de ideia ou cometer um erro. O repo particular, isso atribui o rótulo **-não-mesclar** . | Pública e privada |
| #Faça-fechamento | Autores podem digitar o comentário **#please-fechamento** no fluxo de comentário de uma solicitação de recepção para fechá-lo se você decidir não tem as alterações que foram mescladas. | Público |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Saídas no público repo de solução de problemas

O público repo aprovação automação é permite somente o autor a aprovação. Alguns processamento de exceção manual poderão ser necessárias:

- **Autores de artigo**: para usar a automação de comentário do repositório público, sua conta GitHub real deve corresponder exatamente a conta GitHub listada nos metadados do artigo. O uso de maiusculas da sua conta é importante. Se você está bloqueados da assiná-lo devido a esse problema, envie email para o alias azdocprs.

- **Outros funcionários**: se você for um funcionário que está assinando em nome do autor e é bloqueados pela automação, contate azdocprs com o link de solicitação de recepção. Indica que você é – PMs na mesma equipe de produto, colegas na equipe de escrita e escrever gerentes de equipe são consideradas fontes confiáveis.



## <a name="related"></a>Relacionados

- [Etiqueta de solicitação de recepção e práticas recomendadas para colaboradores da Microsoft](contributor-guide-pull-request-etiquette.md)

- [Critérios de qualidade de solicitação de recepção revisar](contributor-guide-pr-criteria.md)
