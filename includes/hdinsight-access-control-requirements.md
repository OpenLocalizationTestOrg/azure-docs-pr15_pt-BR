Se você usar uma assinatura do Azure onde você está não é o administrador/proprietário, como uma empresa pertencente a assinatura, você deve verificar o seguinte antes de usar as etapas neste documento:

* Seu login Azure deve ter pelo menos acesso de __Colaborador__ ao grupo de recursos Azure que você usar ao criar HDInsight (e outros recursos Azure).

* Alguém no mínimo acesso __Colaborador__ à assinatura Azure deve ter registrado anteriormente o provedor para o recurso que você está usando. Registro do provedor acontece quando um usuário com acesso de Colaborador à assinatura cria um recurso pela primeira vez com assinaturas. Ele também pode ser realizado sem criar um recurso registrando [um provedor usando o restante](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para obter mais informações sobre como trabalhar com gerenciamento de acesso, consulte os seguintes documentos:

* [Introdução ao gerenciamento de acesso no portal do Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Use atribuições de função para gerenciar o acesso aos recursos da sua assinatura do Azure](../articles/active-directory/role-based-access-control-configure.md)
