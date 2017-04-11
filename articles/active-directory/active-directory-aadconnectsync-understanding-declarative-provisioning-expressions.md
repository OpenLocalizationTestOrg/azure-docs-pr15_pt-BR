<properties
    pageTitle="Sincronização do Azure AD Connect: Noções básicas sobre expressões de provisionamento declarativa | Microsoft Azure"
    description="Explica as expressões de provisionamento declarativas."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronização do Azure AD Connect: Noções básicas sobre expressões de provisionamento declarativa
Sincronização do Azure AD Connect baseia provisionamento declarativa introduzidas no Gerenciador de identidades Forefront 2010. Ele permite que você implementar a lógica de negócios de integração de identidade completa sem a necessidade de escrever código compilado.

Uma parte essencial de provisionamento declarativa é a linguagem de expressão usada em fluxos de atributo. O idioma usado é um subconjunto do Microsoft® Visual Basic® for Applications (VBA). Esse idioma é usado no Microsoft Office e os usuários com experiência do VBScript também reconhecerá. A linguagem de expressão de provisionamento declarativa somente está usando funções e não é uma linguagem estruturada. Existem métodos ou instruções. Funções em vez disso são aninhadas para o fluxo de programa express.

Para obter mais detalhes, consulte [Bem-vindo ao Visual Basic para referência de linguagem de aplicativos do Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos são altamente digitados. Uma função aceita somente atributos do tipo correto. Também é diferencia maiusculas de minúsculas. Nomes de função e nomes de atributo devem ter capitalização apropriada ou um erro é lançado.

## <a name="language-definitions-and-identifiers"></a>Definições de idioma e identificadores

- Funções têm um nome seguido por argumentos em colchetes: nomedafunção (argumento 1, o argumento N).
- Atributos são identificados por colchetes: [attributeName]
- Parâmetros são identificados por sinais de porcentagem: nome do parâmetro %
- As constantes de cadeia estão entre aspas: por exemplo, "Contoso" (Observação: deve usar aspas normais "" e não aspas inglesas "")
- Valores numéricos são expresso sem aspas e deve ser decimal. Valores hexadecimais são precedidos com & H. Por exemplo, 98052 & HFF
- Valores booleanos são expressas com constantes: verdadeiro, FALSO.
- Literais e constantes internas são expressas com apenas o nome: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funções
Provisionamento declarativa usa muitas funções para habilitar a possibilidade de transformar valores de atributo. Essas funções podem ser aninhadas para que o resultado de uma função é passado para outra função.

`Function1(Function2(Function3()))`

A lista completa das funções pode ser encontrada na [referência de função](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parâmetros
Um parâmetro é definido por um conector ou por um administrador usando o PowerShell. Parâmetros geralmente contêm valores que são diferentes de um sistema, por exemplo, o nome do domínio do usuário está localizado no. Esses parâmetros podem ser usados nos fluxos de atributo.

O Active Directory Connector fornecidos os seguintes parâmetros de sincronização para regras de entrada:

| Nome do parâmetro | Comentário |
| --- | --- |
| Domain.Netbios | Formato de NetBIOS do domínio sendo importado no momento, por exemplo FABRIKAMSALES |
| Domain.FQDN | Formato FQDN do domínio sendo importado no momento, por exemplo sales.fabrikam.com |
| Domain.LDAP | Formato LDAP do domínio sendo importado no momento, por exemplo DC = vendas, DC = fabrikam, DC = com |
| Forest.Netbios | Formato de NetBIOS do nome da floresta sendo importado no momento, por exemplo FABRIKAMCORP |
| Forest.FQDN | Formato FQDN do nome da floresta sendo importado no momento, por exemplo fabrikam.com |
| Forest.LDAP | Formato LDAP do nome da floresta sendo importado no momento, por exemplo DC = fabrikam, DC = com |

O sistema oferece o parâmetro a seguir, que é usado para obter o identificador do conector em execução:  
`Connector.ID`

Aqui está um exemplo que preenche o domínio de atributo metaverso com o nome netbios do domínio onde se encontra o usuário:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
Os seguintes operadores podem ser usados:

- **Comparação**: <, < =, <>, =, >, > =
- **Matemática**: +, -, \*, -
- **Cadeia de caracteres**: & (concatenar)
- **Lógica**: & & (e), | | (ou)
- **Ordem de avaliação**:)

Operadores são avaliados da esquerda para a direita e têm a mesma prioridade de avaliação. Isto é, o \* (multiplicador) não é avaliada antes - (subtração). 2\*(5 + 3) não é a mesma 2\*5 + 3. Os colchetes () são usadas para alterar a ordem de avaliação quando esquerda para a ordem de avaliação direita não for apropriada.

## <a name="multi-valued-attributes"></a>Atributos de valores múltiplos
As funções podem operar em atributos de valor único e valores múltiplos. Atributos de valores múltiplos, a função opera sobre cada valor e se aplica a mesma função para cada valor.

Por exemplo:  
`Trim([proxyAddresses])`Faça um corte de cada valor do atributo proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Para cada valor com uma @-sign, substituir o domínio com @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Procure o endereço SIP e removê-lo dos valores.

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o modelo de configuração em [Noções básicas sobre declarativa provisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Consulte como declarativa provisionamento é usado de prontos em [Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md).
- Veja como fazer uma mudança prática usando provisionamento declarativa em [como fazer uma alteração para a configuração padrão](active-directory-aadconnectsync-change-the-configuration.md).

**Tópicos de visão geral**

- [Sincronização do Azure AD Connect: entender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)

**Tópicos de referência**

- [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)
