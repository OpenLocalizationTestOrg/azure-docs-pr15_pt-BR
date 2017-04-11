<properties
    pageTitle="Sincronização do Azure AD Connect: referência de funções | Microsoft Azure"
    description="Referência de expressões de provisionamento declarativas em sincronização do Azure AD Connect."
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
    ms.date="08/23/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização do Azure AD Connect: referência de funções

No Azure AD Connect, funções são usadas para manipular um valor de atributo durante a sincronização.  
A sintaxe das funções é expressa usando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função está sobrecarregada e aceita várias sintaxes, todas as sintaxes válidas são listadas.  
As funções são altamente digitadas e verificam que o tipo passado de correspondências do tipo documentado.  
Se o tipo não corresponder, será gerado um erro.

Os tipos são expressas com a seguinte sintaxe:

- **compartimento** – binário
- **bool** – booliano
- **mesa** – UTC Data/hora
- **enumeração** – enumeração das constantes conhecidos
- **exp** – expressão, que é esperada a ser avaliada como um booliano
- **mvbin** – Multi-Valued binário
- **mvstr** – Multi-Valued cadeia de caracteres
- **mvref** – referência de Multi-Valued
- **num** – numérico
- **ref** – referência
- **str** – cadeia de caracteres
- **var** – uma variante (quase) qualquer outro tipo
- **Anular** – não retorna um valor

As funções com tipos **mvbin**, **mvstr**e **mvref** só podem trabalhar em atributos de valores múltiplos. Funções com **compartimento**, **str**e **ref** trabalhar em atributos de valor único e valores múltiplos.

## <a name="functions-reference"></a>Referência de funções

Lista de funções | | | | |  
--------- | --------- | --------- | --------- | --------- | ---------
**Conversão** |  
[CBool](#cbool) | [CDate](#cdate) | [CGuid](#cguid) | [ConvertFromBase64](#convertfrombase64)
[ConvertToBase64](#converttobase64) | [ConvertFromUTF8Hex](#convertfromutf8hex) | [ConvertToUTF8Hex](#converttoutf8hex) | [CNum](#cnum)
[CRef](#cref) | [CStr](#cstr) | [StringFromGuid](#StringFromGuid) | [StringFromSid](#stringfromsid)
**Data / hora** |  
[DateAdd](#dateadd) | [DateFromNum](#datefromnum) | [FormatDateTime](#formatdatetime) | [Agora](#now)
[NumFromDate](#numfromdate) |  
**Diretório** |  
[DNComponent](#dncomponent) | [DNComponentRev](#dncomponentrev) | [EscapeDNComponent](#escapedncomponent)
**Avaliação** |  
[IsBitSet](#isbitset) | [IsDate](#isdate) | [IsEmpty](#isempty) | [IsGuid](#isguid)
[IsNull](#isnull) | [IsNullOrEmpty](#isnullorempty) | [IsNumeric](#isnumeric) | [IsPresent](#ispresent) |
[IsString](#isstring) |  
**Matemática** |  
[BitAnd](#bitand) | [BitOr](#bitor) | [RandomNum](#randomnum)
**Valores múltiplos** |  
[Contém](#contains) | [Contagem](#count) | [Item](#item) | [ItemOrNull](#itemornull)
[Junção](#join) | [RemoveDuplicates](#removeduplicates) | [Dividir](#split) |
**Fluxo de programa** |  
[Erro](#error) | [IIF](#iif)  | [Alternar](#switch)
**Texto** |  
[GUID](#guid) | [InStr](#instr) | [InStrRev](#instrrev) | [LCase](#lcase)
[À esquerda](#left) | [CARACT](#len) | [Funções LTrim](#ltrim) | [Mid](#mid)
[PadLeft](#padleft) | [PadRight](#padright) | [PCase](#pcase) | [Substituir](#replace)
[ReplaceChars](#replacechars) | [Certo](#right) | [RTrim](#rtrim) | [Cortar](#trim)
[UCase](#ucase) | [Word](#word)

----------
### <a name="bitand"></a>BitAnd

**Descrição:**  
Função BitAnd define bits especificados em um valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

- valor1, valor2: valores numéricos que devem ser agrupadas juntas

**Comentários:**  
Esta função converte a representação binária ambos os parâmetros e define um pouco:

- 0 - se um ou ambos os bits correspondente da *máscara* e *sinalizador* forem 0
- 1 - se ambos dos bits correspondentes forem 1.

Em outras palavras, ela retornará 0 em todos os casos, exceto quando bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Retorna 7 porque hexadecimal "F" e "F7" avaliar como esse valor.

----------
### <a name="bitor"></a>BitOr

**Descrição:**  
Função BitOr define bits especificados em um valor.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

- valor1, valor2: valores numéricos que devem ser tornarão or juntas

**Comentários:**  
Esta função converte a representação binária ambos os parâmetros e define um pouco para 1 se um ou ambos os bits correspondente da máscara e sinalizador forem 1 e 0 se ambos dos bits correspondentes forem 0. Em outras palavras, ela retorna 1 em todos os casos, exceto onde os bits correspondentes de ambos os parâmetros são 0.

----------
### <a name="cbool"></a>CBool

**Descrição:**  
A função CBool retorna um booliano com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Comentários:**  
Se a expressão for avaliada como um valor diferente de zero, CBool retornará verdadeiro, caso contrário retornará falso.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Retorna True se ambos os atributos têm o mesmo valor.

----------
### <a name="cdate"></a>CDate

**Descrição:**  
A função CDate retorna um DateTime UTC de uma cadeia de caracteres. DateTime não é um tipo de atributo nativo em sincronia, mas é usado por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

- Valor: Uma cadeia de caracteres com uma data, hora e, opcionalmente, fuso horário

**Comentários:**  
A cadeia de caracteres retornada sempre é em UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
Hora de início de retorna que um DateTime com base do funcionário

`CDate("2013-01-10 4:00 PM -8")`  
Retorna um DateTime que representa "2013-01-11 12:00 AM"

----------
### <a name="cguid"></a>CGuid

**Descrição:**  
A função CGuid converte a representação de cadeia de caracteres de um GUID para sua representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

- Uma cadeia de caracteres formatada nesse padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

----------
### <a name="contains"></a>Contém

**Descrição:**  
A função Contains localiza uma cadeia de caracteres dentro de um atributo de valores múltiplos

**Sintaxe:**  
`num Contains (mvstring attribute, str search)`-diferencia maiusculas de minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`-diferencia maiusculas de minúsculas

- atributo: o atributo de valores múltiplos para pesquisar.
- pesquisa: cadeia de caracteres para encontrar no atributo.
- Casetype: CaseInsensitive ou CaseSensitive.

Retorna o índice no atributo de valores múltiplos onde a cadeia de caracteres foi encontrada. 0 é retornado se a cadeia de caracteres não for encontrada.

**Comentários:**  
Atributos de cadeia de caracteres de valores múltiplos, a pesquisa encontra substrings os valores.  
Atributos de referência, a cadeia de caracteres pesquisada deve corresponder exatamente o valor para ser considerado uma correspondência.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Se o atributo proxyAddresses tem um endereço de email principal (indicado pelo maiusculas "SMTP:"), em seguida, retorne o atributo proxyAddress, else retornará um erro.

----------
### <a name="convertfrombase64"></a>ConvertFromBase64

**Descrição:**  
A função ConvertFromBase64 converte o valor especificado na Base 64 codificado cadeia regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)`-supõe Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

- fonte: cadeia de caracteres de codificado na Base 64  
- Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Os dois exemplos retornam "*Olá!*"

----------
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex

**Descrição:**  
A função ConvertFromUTF8Hex converte o valor de codificados como Hex UTF8 especificado em uma cadeia de caracteres.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

- fonte: sequência codificada de 2 bytes UTF8

**Comentários:**  
A diferença entre essa função e ConvertFromBase64([],UTF8) em que o resultado é amigável para o atributo DN.  
Este formato é usado pelo Azure Active Directory como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Retorna "*Olá!*"

----------
### <a name="converttobase64"></a>ConvertToBase64

**Descrição:**  
A função ConvertToBase64 converte uma cadeia de caracteres para Unicode na Base 64.  
Converte o valor de uma matriz de inteiros em sua representação equivalente da cadeia de caracteres codificada com base 64 dígitos.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
Retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

----------
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex

**Descrição:**  
A função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor Hex UTF8 codificado.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Comentários:**  
O formato de saída dessa função é usado pelo Azure Active Directory como formato do atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
Retorna 48656C6C6F20776F726C6421

----------
### <a name="count"></a>Contagem

**Descrição:**  
A função Contar Retorna o número de elementos em um atributo de valores múltiplos

**Sintaxe:**  
`num Count(mvstr attribute)`

----------
### <a name="cnum"></a>CNum

**Descrição:**  
A função CNum leva uma cadeia de caracteres e retorna um tipo de dados numéricos.

**Sintaxe:**  
`num CNum(str value)`

----------
### <a name="cref"></a>CRef

**Descrição:**  
Converte uma cadeia de caracteres em um atributo de referência

**Sintaxe:**  
`ref CRef(str value)`

**Exemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

----------
### <a name="cstr"></a>CStr

**Descrição:**  
A função CStr converte em um tipo de dados de cadeia de caracteres.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

- valor: pode ser um valor numérico, atributo de referência ou booliano.

**Exemplo:**  
`CStr([dn])`  
Poderia retornar "cn = José, dc = contoso, dc = com"

----------
### <a name="dateadd"></a>DateAdd

**Descrição:**  
Retorna uma data que contém uma data à qual um intervalo de tempo especificado foi adicionado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

- intervalo: expressão em sequência que é o intervalo de tempo que você deseja adicionar. A cadeia de caracteres deve ter um dos seguintes valores:
 - aaaa ano
 - p trimestre
 - m mês
 - y dia do ano
 - d dia
 - w Weekday
 - SS semana
 - h hora
 - Minuto n
 - s segundo
- valor: O número de unidades que você deseja adicionar. Pode ser positivo (para obter datas no futuro) ou negativo (para obter datas no passado).
- Data: DateTime representando a data à qual o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Adiciona 3 meses e retorna um DateTime representando "2001-04-01".

----------
### <a name="datefromnum"></a>DateFromNum

**Descrição:**  
O converte de função DateFromNum um valor de data do AD formatar a um tipo de DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Retorna um DateTime representando 2012-01-01 23:00:00

----------
### <a name="dncomponent"></a>DNComponent

**Descrição:**  
A função DNComponent retornará o valor de um componente de DN especificado indo da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

- DN: o atributo de referência para interpretar
- ComponentNumber: O componente no DN para retornar

**Exemplo:**  
`DNComponent([dn],1)`  
Se for dn "cn = José, ou =...," retornará José

----------
### <a name="dncomponentrev"></a>DNComponentRev

**Descrição:**  
A função DNComponentRev retornará o valor de um componente de DN especificado indo da direita (a extremidade).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- DN: o atributo de referência para interpretar
- ComponentNumber - o componente no DN para retornar
- Opções: DC – ignorar todos os componentes com "dc ="

**Exemplo:**  
Se for dn "cn = José, ou = Atlanta, ou = GA, ou = US, dc = contoso, dc = com" depois  
`DNComponentRev([dn],3)`  
`DNComponentRev([dn],1,"DC")`  
Os dois retornam conosco.

----------
### <a name="error"></a>Erro

**Descrição:**  
A função de erro é usada para retornar um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Se não houver o atributo accountName, lança um erro no objeto.

----------
### <a name="escapedncomponent"></a>EscapeDNComponent

**Descrição:**  
A função EscapeDNComponent leva um componente de um DN e escapes-lo para que ele pode ser representado no LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Garante que o objeto pode ser criado em um diretório LDAP, mesmo se o atributo displayName tem caracteres que devem haver escape no LDAP.

----------
### <a name="formatdatetime"></a>FormatDateTime

**Descrição:**  
A função FormatDateTime é usada para formatar um DateTime em uma cadeia de caracteres com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

- valor: um valor no formato DateTime
- formato: uma cadeia de caracteres que representa o formato a ser convertido em.

**Comentários:**  
Os valores possíveis para o formato podem ser encontrados aqui: [Definida pelo usuário (função Format) de formatos de data/hora](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**Exemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resulta em "25-12-2007".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Pode resultar em "20140905081453.0Z"

----------
### <a name="guid"></a>GUID

**Descrição:**  
A função GUID gera um novo GUID aleatório

**Sintaxe:**  
`str GUID()`

----------
### <a name="iif"></a>IIF

**Descrição:**  
A função IIF retorna uma de um conjunto de valores possíveis com base em uma condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condição: qualquer valor ou expressão que pode ser avaliada como verdadeiro ou falso.
- ValorSeVerdadeiro: se a condição for avaliada como true, o valor retornado.
- ValorSeFalso: se a condição for falso, o valor retornado.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Se o usuário for um estagiários, retorna o alias de um usuário com "t-" adicionado ao início dele, mais retorna o alias do usuário como ela está.

----------
### <a name="instr"></a>InStr

**Descrição:**  
A função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck: cadeia de caracteres a ser pesquisado
- stringmatch: cadeia de caracteres a ser localizado
- Iniciar: Iniciando posição para localizar a subcadeia de caracteres
- Comparar: vbTextCompare ou vbBinaryCompare

**Comentários:**  
Retorna a posição onde a subcadeia de caracteres foi encontrada ou 0 se não foi encontrado.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
Evalues para 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Avalia para 7

----------
### <a name="instrrev"></a>InStrRev

**Descrição:**  
A função InStrRev localiza a última ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck: cadeia de caracteres a ser pesquisado
- stringmatch: cadeia de caracteres a ser localizado
- Iniciar: Iniciando posição para localizar a subcadeia de caracteres
- Comparar: vbTextCompare ou vbBinaryCompare

**Comentários:**  
Retorna a posição onde a subcadeia de caracteres foi encontrada ou 0 se não foi encontrado.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
Retorna 7

----------
### <a name="isbitset"></a>IsBitSet

**Descrição:**  
A função IsBitSet testa se um pouco está definida ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

- valor: um valor numérico que é evaluated.flag: um valor numérico que tem o bit a ser avaliada

**Exemplo:**  
`IsBitSet(&HF,4)`  
Retorna verdadeiro porque bit "4" é definido no valor hexadecimal "F"

----------
### <a name="isdate"></a>IsDate

**Descrição:**  
Se a expressão pode ser avaliada como um tipo de DateTime, e em seguida, a função IsDate é avaliada como verdadeira.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Comentários:**  
Usado para determinar se CDate () pode ser bem-sucedido.

----------
### <a name="isempty"></a>IsEmpty

**Descrição:**  
Se o atributo está presente na CS ou MV, mas será avaliado como uma cadeia de caracteres vazia, a função IsEmpty será avaliado como True.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

----------
### <a name="isguid"></a>IsGuid

**Descrição:**  
Se a cadeia de caracteres pode ser convertida em um GUID, a função IsGuid avaliado como true.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Comentários:**  
Um GUID é definido como uma cadeia de caracteres seguindo um desses padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Usado para determinar se CGuid() pode ser bem-sucedido.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Se a StrAttribute tiver um formato GUID, retorne uma representação binária, caso contrário retornar um valor nulo.

----------
### <a name="isnull"></a>IsNull

**Descrição:**  
Se a expressão for avaliada como Null, a função IsNull retorna true.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Comentários:**  
Para um atributo, um valor nulo é expressa pelo ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
Retorna VERDADEIRO se o atributo não estiver presente na CS ou MV.

----------
### <a name="isnullorempty"></a>IsNullOrEmpty

**Descrição:**  
Se a expressão for nulo ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retorna true.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Comentários:**  
Para um atributo, isso poderia avaliar como True se o atributo está ausente ou está presente mas uma cadeia de caracteres vazia.  
O inverso desta função é chamado IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
Retorna VERDADEIRO se o atributo não está presente ou é uma cadeia de caracteres vazia na CS ou MV.

----------
### <a name="isnumeric"></a>IsNumeric

**Descrição:**  
A função IsNumeric retorna um valor booliano que indica se uma expressão pode ser avaliada como um tipo de número.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Comentários:**  
Usado para determinar se CNum() pode ser bem-sucedida para analisar a expressão.

----------
### <a name="isstring"></a>IsString

**Descrição:**  
Se a expressão pode ser avaliada como um tipo de cadeia de caracteres, a função IsString será avaliado como True.

**Sintaxe:**  
`bool IsString(var expression)`

**Comentários:**  
Usado para determinar se CStr() pode ser bem-sucedida para analisar a expressão.

----------
### <a name="ispresent"></a>IsPresent

**Descrição:**  
Se a expressão for avaliada como uma cadeia de caracteres que não é nulo e não está vazio, a função IsPresent retorna true.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Comentários:**  
O inverso desta função é chamado IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

----------
### <a name="item"></a>Item

**Descrição:**  
A função de Item Retorna um único item de um cadeia de caracteres/atributo de valores múltiplos.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

- atributo: atributo de valores múltiplos
- índice: índice para um item na cadeia de valores múltiplos.

**Comentários:**  
A função de Item é útil junto com a função Contains desde a última função retorna o índice para um item no atributo de valores múltiplos.

Gera um erro se o índice estiver fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddress],Contains([proxyAddress], "SMTP:")),6)`  
Retorna o endereço de email principal.

----------
### <a name="itemornull"></a>ItemOrNull

**Descrição:**  
A função ItemOrNull retorna um único item de um cadeia de caracteres/atributo de valores múltiplos.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

- atributo: atributo de valores múltiplos
- índice: índice para um item na cadeia de valores múltiplos.

**Comentários:**  
A função ItemOrNull é útil junto com a função Contains desde a última função retorna o índice para um item no atributo de valores múltiplos.

Se o índice estiver fora dos limites, em seguida, retorna um valor nulo.

----------
### <a name="join"></a>Junção

**Descrição:**  
A função de associação usa uma cadeia de caracteres de valores múltiplos e retorna uma cadeia de caracteres de valor único com separador especificado inserido entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

- atributo: atributo de valores múltiplos que contêm cadeias de caracteres a serem agrupados.
- delimitador: qualquer string, usada para separar as substrings na string retornada. Se omitido, o caractere de espaço ("") é usado. Se delimitador é uma cadeia de caracteres de comprimento zero ("") ou nada, todos os itens na lista são concatenados sem delimitadores.

**Comentários**  
Há paridade entre as funções de junção e divisão. A função de associação leva uma matriz de cadeias de caracteres e une usando uma cadeia de caracteres de delimitador, para retornar uma única cadeia de caracteres. A função Split leva uma cadeia de caracteres e separa no delimitador, para retornar uma matriz de cadeias de caracteres. No entanto, uma diferença chave é que ingressar pode concatenar cadeias de caracteres com qualquer cadeia de caracteres de delimitador, divisão somente pode separar cadeias de caracteres usando um único caractere delimitador.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Poderia retornar:"SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

----------
### <a name="lcase"></a>LCase

**Descrição:**  
A função LCase converte todos os caracteres em uma cadeia de caracteres em minúsculas.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
Retorna "teste".

----------
### <a name="left"></a>À esquerda

**Descrição:**  
A função ESQUERDA retorna um número especificado de caracteres do lado esquerdo de uma cadeia de caracteres.

**Sintaxe:**  
`str Left(str string, num NumChars)`

- cadeia de caracteres: a cadeia de caracteres a partir de retorno
- NumChars: um número que identifica o número de caracteres para retornar desde o começo (à esquerda) da cadeia de caracteres

**Comentários:**  
Uma cadeia de caracteres que contém os primeiros caracteres numChars na cadeia de caracteres:

- Se numChars = 0, retorne a cadeia de caracteres vazia.
- Se numChars < 0, retorne a cadeia de caracteres de entrada.
- Se a cadeia de caracteres é nula, retorne cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos caracteres que o numChars especificado em número, uma cadeia de caracteres idêntica a cadeia de caracteres (isto é, que contém todos os caracteres no parâmetro 1) será retornada.

**Exemplo:**  
`Left("John Doe", 3)`  
Retorna "Joh".

----------
### <a name="len"></a>CARACT

**Descrição:**  
A função Len retorna o número de caracteres em uma cadeia.

**Sintaxe:**  
`num Len(str value)`

**Exemplo:**  
`Len("John Doe")`  
Retorna 8

----------
### <a name="ltrim"></a>Funções LTrim

**Descrição:**  
A função LTrim remove espaços em branco à esquerda de uma cadeia.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
Retorna "teste"

----------
### <a name="mid"></a>Mid

**Descrição:**  
A função médio retorna um número especificado de caracteres de uma posição especificada em uma cadeia.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

- cadeia de caracteres: a cadeia de caracteres a partir de retorno
- Iniciar: um número que identifica o início posicione na cadeia de caracteres para caracteres de retorno
- NumChars: um número que identifica o número de caracteres para retornar da posição na cadeia de caracteres

**Comentários:**  
Retorne a partir do início de posição na cadeia de caracteres de numChars.  
Uma cadeia de caracteres que contém caracteres de numChars do início de posição na cadeia de caracteres:

- Se numChars = 0, retorne a cadeia de caracteres vazia.
- Se numChars < 0, retorne a cadeia de caracteres de entrada.
- Se Iniciar > o comprimento da cadeia de caracteres, retorne a cadeia de caracteres de entrada.
- Se iniciar < = 0, retorne a cadeia de caracteres de entrada.
- Se a cadeia de caracteres é nula, retorne cadeia de caracteres vazia.

Se não houver numChar caracteres restantes na cadeia de caracteres do início de posição, quantos caracteres possível são retornados.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
Retorna "hn faça".

`Mid("John Doe", 6, 999)`  
Retorna "Silva"

----------
### <a name="now"></a>Agora

**Descrição:**  
A função agora retorna um DateTime Especifica a data atual e o tempo, de acordo com a data do sistema e a hora de seu computador.

**Sintaxe:**  
`dt Now()`

----------
### <a name="numfromdate"></a>NumFromDate

**Descrição:**  
A função NumFromDate retorna uma data no formato de data do AD.

**Sintaxe:**  
`num NumFromDate(dt value)`

**Exemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Retorna 129699324000000000

----------
### <a name="padleft"></a>PadLeft

**Descrição:**  
PadLeft função esquerda-painéis uma cadeia de caracteres para um período especificado usando um caractere de preenchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

- cadeia de caracteres: a cadeia de caracteres para preencher.
- comprimento: um inteiro que representa o comprimento da cadeia de caracteres desejado.
- padCharacter: uma cadeia de caracteres que consiste em um único caractere para usar como o caractere de teclado

**Comentários:**

- Se o comprimento da cadeia de caracteres for menor que o comprimento, padCharacter repetidamente é acrescentado ao início (à esquerda) da cadeia de caracteres até que ele tenha um comprimento igual ao comprimento.
- PadCharacter pode ser um caractere de espaço, mas ele não pode ser um valor nulo.
- Se o comprimento da cadeia de caracteres é igual a ou maior que o comprimento, a cadeia de caracteres é retornada inalterada.
- Se a cadeia de caracteres tiver um comprimento maior que ou igual ao comprimento, uma cadeia de caracteres idêntica a cadeia de caracteres é retornada.
- Se o comprimento da cadeia de caracteres for menor que o comprimento, uma nova cadeia de caracteres do tamanho desejado é retornada cadeia de caracteres contendo preenchida com um padCharacter.
- Se a cadeia de caracteres é nula, a função retorna uma cadeia de caracteres vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
Retorna "000000User".

----------
### <a name="padright"></a>PadRight

**Descrição:**  
PadRight função direita-painéis uma cadeia de caracteres para um período especificado usando um caractere de preenchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

- cadeia de caracteres: a cadeia de caracteres para preencher.
- comprimento: um inteiro que representa o comprimento da cadeia de caracteres desejado.
- padCharacter: uma cadeia de caracteres que consiste em um único caractere para usar como o caractere de teclado

**Comentários:**

- Se o comprimento da cadeia de caracteres for menor que o comprimento, em seguida, padCharacter é repetidamente acrescentado ao final (à direita) da cadeia de caracteres até que ele tenha um comprimento igual ao comprimento.
- padCharacter pode ser um caractere de espaço, mas ele não pode ser um valor nulo.
- Se o comprimento da cadeia de caracteres é igual a ou maior que o comprimento, a cadeia de caracteres é retornada inalterada.
- Se a cadeia de caracteres tiver um comprimento maior que ou igual ao comprimento, uma cadeia de caracteres idêntica a cadeia de caracteres é retornada.
- Se o comprimento da cadeia de caracteres for menor que o comprimento, uma nova cadeia de caracteres do tamanho desejado é retornada cadeia de caracteres contendo preenchida com um padCharacter.
- Se a cadeia de caracteres é nula, a função retorna uma cadeia de caracteres vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
Retorna "User000000".

----------
### <a name="pcase"></a>PCase

**Descrição:**  
A função PCase converte o primeiro caractere de cada palavra delimitada por espaço em uma cadeia de caracteres para maiusculas e todos os outros caracteres são convertidos em minúsculas.

**Sintaxe:**  
`String PCase(string)`

**Comentários:**

- Esta função não oferece atualmente capitalização apropriada para converter uma palavra que é totalmente maiuscula, como um acrônimo.

**Exemplo:**  
`PCase("TEsT")`  
Retorna "Teste".

`PCase(LCase("TEST"))`  
Retorna "teste"

----------
### <a name="randomnum"></a>RandomNum

**Descrição:**  
A função RandomNum retorna um número aleatório entre um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

- Iniciar: um número que identifica o limite inferior do valor aleatório para gerar
- final: um número que identifica o limite superior do valor aleatório para gerar

**Exemplo:**  
`Random(100,999)`  
Pode retornar 734.

----------
### <a name="removeduplicates"></a>RemoveDuplicates

**Descrição:**  
A função RemoveDuplicates leva uma cadeia de caracteres de valores múltiplos e verifique se cada valor seja exclusivo.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
Retorna um atributo proxyAddress corrigido onde todos os valores duplicados foram removidos.

----------
### <a name="replace"></a>Substituir

**Descrição:**  
A função Replace substitui todas as ocorrências de uma cadeia de caracteres para outra cadeia de caracteres.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

- cadeia de caracteres: substituir valores em uma cadeia de caracteres.
- OldValue: A cadeia de caracteres para pesquisar e substituir.
- Novo_valor: A cadeia de caracteres para substituir a.

**Comentários:**  
A função reconhece os seguintes identificadores especiais:

- \n – nova linha
- \r – retorno de carro
- \t – guia

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
Substitui CRLF com uma vírgula e um espaço e pode levar a "One Microsoft maneira, Redmond, WA, EUA"

----------
### <a name="replacechars"></a>ReplaceChars

**Descrição:**  
A função ReplaceChars substitui todas as ocorrências dos caracteres encontrados na cadeia de caracteres ReplacePattern.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

- cadeia de caracteres: substituir caracteres em uma cadeia de caracteres.
- ReplacePattern: uma cadeia de caracteres que contém um dicionário com caracteres para substituir.

O formato é {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN} onde fonte é o caractere para localizar e substituir com a cadeia de caracteres de destino.

**Comentários:**

- A função leva cada ocorrência de fontes definidos e substitui-los com os destinos.
- A origem deve ser exatamente um caractere (unicode).
- A fonte não pode ser vazio ou mais de um caractere (erro de análise).
- O destino pode ter vários caracteres, por exemplo, ö:oe, β:ss.
- O destino pode ser vazio indicando que o caractere deve ser removido.
- A fonte diferencia maiusculas de minúsculas e deve ser uma correspondência exata.
- (Vírgula) e: (dois-pontos) são caracteres reservados e não pode ser substituído usar esta função.
- Espaços e outros caracteres em brancos na cadeia ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Retorna Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Retorna "ONeil", a única escala é definido para ser removido.

----------
### <a name="right"></a>Certo

**Descrição:**  
A função Right retorna um número especificado de caracteres da direita (fim) de uma cadeia de caracteres.

**Sintaxe:**  
`str Right(str string, num NumChars)`

- cadeia de caracteres: a cadeia de caracteres a partir de retorno
- NumChars: um número que identifica o número de caracteres para retornar do final (à direita) da cadeia de caracteres

**Comentários:**  
NumChars caracteres são retornados da última posição da cadeia de caracteres.

Uma cadeia de caracteres que contém os últimos caracteres numChars na cadeia de caracteres:

- Se numChars = 0, retorne a cadeia de caracteres vazia.
- Se numChars < 0, retorne a cadeia de caracteres de entrada.
- Se a cadeia de caracteres é nula, retorne cadeia de caracteres vazia.

Se string contiver menos caracteres que o NumChars especificado em número, uma cadeia de caracteres idêntica a cadeia de caracteres é retornada.

**Exemplo:**  
`Right("John Doe", 3)`  
Retorna "Silva".

----------
### <a name="rtrim"></a>RTrim

**Descrição:**  
A função RTrim remove espaços em branco à direita de uma cadeia.

**Sintaxe:**  
`str RTrim(str value)`

**Exemplo:**  
`RTrim(" Test ")`  
Retorna "Teste".

----------
### <a name="split"></a>Dividir

**Descrição:**  
A função Split leva uma cadeia de caracteres separada por um delimitador e facilita uma cadeia de caracteres de valores múltiplos.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

- valor: a cadeia de caracteres com um caractere delimitador para separar.
- delimitador: um único caractere a ser usado como o delimitador.
- limite: número máximo de valores que pode retornar.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Retorna uma cadeia de caracteres de valores múltiplos com 2 elementos útil para o atributo proxyAddress.

----------
### <a name="stringfromguid"></a>StringFromGuid

**Descrição:**  
A função StringFromGuid leva um GUID binário e o converte em uma cadeia de caracteres

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

----------
### <a name="stringfromsid"></a>StringFromSid

**Descrição:**  
A função StringFromSid converte uma matriz de bytes que contém um identificador de segurança para uma cadeia de caracteres.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

----------
### <a name="switch"></a>Alternar

**Descrição:**  
A função Switch é usada para retornar um único valor com base em condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr: variante expressão que você deseja avaliar.
- valor: valor a ser retornado se a expressão correspondente for verdadeiro.

**Comentários:**  
A lista de argumentos de função Switch consiste em pares de expressões e valores. As expressões são avaliadas da esquerda para a direita e o valor associado à primeira expressão avaliada como True será retornado. Se as partes não tiverem pares adequados, ocorrerá um erro de tempo de execução.

Por exemplo, se expr1 for verdadeiro, alternar retornará valor1. Se expr-1 for falso, mas expr-2 for verdadeiro, Switch retorna o valor-2 e assim por diante.

Opção retornará um nada se:

- Nenhum das expressões for verdadeiro.
- A primeira expressão True tem um valor correspondente que é nulo.

Alternar avalia todas as expressões, mesmo que ela retorne apenas uma delas. Por esse motivo, você deve ficar atento à lado indesejada. Por exemplo, se a avaliação de qualquer expressão resulta em uma divisão por zero, ocorrerá um erro.

Valor também pode ser a função de erro, que retorna uma cadeia de caracteres personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Retorna o idioma falado em algumas cidades principais, caso contrário, retorna um erro.

----------
### <a name="trim"></a>Cortar

**Descrição:**  
A função ARRUMAR remove à esquerda e espaços em branco de uma cadeia.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
Retorna "Teste".

`Trim([proxyAddresses])`  
Remove à esquerda e espaços para cada valor do atributo proxyAddress à direita.

----------
### <a name="ucase"></a>UCase

**Descrição:**  
A função UCase converte todos os caracteres em uma cadeia de caracteres para maiusculas.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
Retorna "Teste".

----------
### <a name="word"></a>Word

**Descrição:**  
A função do Word retorna uma palavra contida em uma cadeia de caracteres, com base nos parâmetros descrevendo os delimitadores para uso e o número de word para retornar.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

- cadeia de caracteres: a cadeia de caracteres para retornar uma palavra.
- WordNumber: deve retornar um número que identifica qual número do word.
- delimitadores: uma cadeia de caracteres que representa o delimiter(s) que deve ser usado para identificar palavras

**Comentários:**  
Cada cadeia de caracteres na cadeia de caracteres separados por um dos caracteres na delimitadores são identificados como palavras:

- Se número < 1, retorna cadeia de caracteres vazia.
- Se a cadeia de caracteres é nula, retorna a cadeia de caracteres vazia.

Se a cadeia de caracteres contém menos do que o número de palavras ou cadeia de caracteres não contenha quaisquer palavras identificadas por delimitadores, uma cadeia de caracteres vazia será retornada.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
Retorna "marrom"

`Word("This,string!has&many separators",3,",!&#")`  
Retorna "tem"

## <a name="additional-resources"></a>Recursos adicionais

* [Noções básicas sobre expressões de provisionamento declarativas](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD conectar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração suas identidades do local com o Active Directory do Azure](active-directory-aadconnect.md)
