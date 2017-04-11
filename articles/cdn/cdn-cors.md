<properties
    pageTitle="Usando o Azure CDN com CORS | Microsoft Azure"
    description="Saiba como usar o Azure conteúdo entrega rede (CDN) para com o compartilhamento de recursos com a entre origens (CORS)."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="casoper"/>
    
# <a name="using-azure-cdn-with-cors"></a>Usando o Azure CDN com CORS     

## <a name="what-is-cors"></a>O que é CORS?

CORS (Cross Origin compartilhamento de recursos) é um recurso HTTP que permite que um aplicativo da web em execução em um domínio acessar recursos em outro domínio. Para reduzir a possibilidade de ataques de script entre sites, todos os navegadores modernos implementam uma restrição de segurança conhecida como [diretiva de mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Isso impede que uma página da web de chamada APIs em um domínio diferente.  CORS fornece uma maneira segura para permitir que um domínio (o domínio de origem) para chamar APIs em outro domínio.
 
## <a name="how-it-works"></a>Como ele funciona
1.  O navegador envia a solicitação de opções com um cabeçalho de **origem** HTTP. O valor desse cabeçalho é o domínio que serviram a página pai. Quando uma página da https://www.contoso.com tenta acessar dados de um usuário do domínio fabrikam.com, o seguinte cabeçalho de solicitação seria enviado para fabrikam.com: 
    
    `Origin: https://www.contoso.com`
 
2.  O servidor pode responder com o seguinte:
    - Um cabeçalho de **Acesso controle-permitir-origem** em sua resposta indicando quais sites de origem são permitidas. Por exemplo:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Uma página de erro se o servidor não permitir a solicitação entre origens
    - Um cabeçalho de **Acesso controle-permitir-origem** com um caractere curinga que permite que todos os domínios:
        
        `Access-Control-Allow-Origin: *`
 
Para solicitações HTTP complexas, há uma solicitação de "comprovação" concluída primeiro para determinar se ela tem permissão antes de enviar a solicitação de inteira.
 
## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de origem única ou curinga

CORS no Azure CDN funcionará automaticamente com nenhuma configuração adicional quando o cabeçalho de **Acesso controle-permitir-origem** está definido como curinga (*) ou uma única origem.  A CDN irá armazenar em cache a primeira resposta e as solicitações subsequentes usarão o mesmo cabeçalho.
 
Se as solicitações já foram feitas a CDN antes CORS sendo definidos na sua origem, você precisará limpar conteúdo em seu conteúdo de ponto de extremidade para recarregar o conteúdo com o cabeçalho de **Acesso controle-permitir-origem** .
 
## <a name="multiple-origin-scenarios"></a>Vários cenários de origem

Se você precisar permitir uma lista específica de origens de ser permitida para CORS, as coisas são um pouco mais complicadas. O problema ocorre quando o CDN armazena o cabeçalho de **Acesso controle-permitir-origem** para a origem CORS primeiro.  Quando uma origem CORS diferente faz uma solicitação subsequente, a CDN será served o cabeçalho de **Acesso controle-permitir-origem** em cache, que não corresponde.  Há várias maneiras para corrigir isso.
 
### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium da Verizon

A melhor maneira de habilitar isso é usar o **Azure CDN Premium da Verizon**, que expõe algumas funcionalidades avançadas. 
 
Você precisará [criar uma regra](cdn-rules-engine.md) verificar o cabeçalho de **origem** na solicitação.  Se for uma origem válida, sua regra definirá o cabeçalho de **Acesso controle-permitir-origem** com a origem fornecida na solicitação.  Se a origem especificada no cabeçalho de **origem** não é permitida, sua regra deve omitir o cabeçalho de **Acesso controle-permitir-origem** que fará com que o navegador para rejeitar a solicitação. 
 
Há duas maneiras de fazer isso com o mecanismo de regras.  Em ambos os casos, o cabeçalho de **Acesso controle-permitir-origem** do servidor de origem do arquivo é totalmente ignorado, mecanismo de regras do CDN completamente gerencia as origens CORS permitidas.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
 
Nesse caso, você irá criar uma expressão regular que inclui todas as origens que você deseja permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **Azure CDN da Verizon** usa [Expressões regulares compatíveis com Perl](http://pcre.org/) como seu mecanismo de expressões regulares.  Você pode usar uma ferramenta como [101 de expressões regulares](https://regex101.com/) para validar sua expressão regular.  Observe que o caractere "/" é válido em expressões regulares e não precise ser escape, no entanto, escape esse caractere é considerada uma prática recomendada e esperado pela alguns validadores regex.

Se a expressão regular corresponde, sua regra substituirá o cabeçalho de **Acesso controle-permitir-origem** (se houver) na origem com a origem que enviou a solicitação.  Você também pode adicionar cabeçalhos CORS adicionais, como o **Access--permitir-métodos de controle**.

![Exemplo de regras com expressões regulares](./media/cdn-cors/cdn-cors-regex.png)
 
#### <a name="request-header-rule-for-each-origin"></a>Regra de cabeçalho de solicitação para cada origem.

Em vez de expressões regulares, em vez disso, você pode criar uma regra separada para cada origem que deseja permitir usando a **Solicitação cabeçalho curinga** [correspondem à condição](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Como com o método de expressões regulares, o mecanismo de regras sozinho define os cabeçalhos CORS. 
  
![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] No exemplo acima, o uso do caractere curinga * informa ao mecanismo de regras para corresponder HTTP e HTTPS.
 
### <a name="azure-cdn-standard"></a>Azure CDN padrão

Em perfis Azure CDN padrão, o mecanismo somente para permitir várias origens sem o uso da origem curinga é usar o [cache de cadeia de caracteres de consulta](cdn-query-string.md).  Você precisa habilitar configuração de cadeia de caracteres de consulta para o ponto de extremidade CDN e use uma cadeia de caracteres de consulta exclusiva para solicitações de cada domínio permitido. Isso resultará na CDN cache um objeto separado para cada cadeia de caracteres de consulta exclusivo. Essa abordagem não é ideal, no entanto, pois resultará em várias cópias do mesmo arquivo em cache a CDN.  

