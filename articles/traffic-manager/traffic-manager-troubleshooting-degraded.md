<properties
    pageTitle="Solução de problemas degradado status no Gerenciador de tráfego do Azure"
    description="Como solucionar problemas de perfis do Gerenciador de tráfego quando ele mostra como degradado status."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Solução de problemas degradado estado no Gerenciador de tráfego do Azure

Este artigo descreve como solucionar problemas de um perfil do Gerenciador de tráfego do Azure que está mostrando um status degradado. Para esse cenário, considere a possibilidade de que você tenha configurado um perfil do Gerenciador de tráfego apontando para alguns dos seus serviços de cloudapp.net hospedado. Quando você verifica a integridade do seu gerente de tráfego, você verá que o Status está degradado.

![estado degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Se você for para a guia de pontos de extremidade do perfil, você verá um ou mais dos pontos de extremidade com um status Offline:

![offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Gerenciador de tráfego de Noções básicas sobre testes

- Gerenciador de tráfego considera um ponto de extremidade para ser ONLINE apenas quando o teste recebe uma resposta HTTP 200 volta do caminho do teste. Qualquer outra resposta não-200 é uma falha.
- Um redirecionamento de 30 x falha, mesmo se o URL redirecionado retorna um 200.
- Para testes de HTTPs, erros de certificado são ignorados.
- Não importa o conteúdo real do caminho do teste, desde que um 200 é retornado. Sondagem uma URL para algum conteúdo estático como "/ favicon.ico" é uma técnica comum. Conteúdo dinâmico, como as páginas ASP, não pode sempre retornar 200, mesmo quando o aplicativo está íntegro.
- Uma prática recomendada é definir o caminho de teste para algo que tem bastante lógica para determinar se o site está para cima ou para baixo. No exemplo anterior, configurando o caminho como "/ favicon.ico", você só está testes que w3wp.exe está respondendo. Este teste não pode indicar que o aplicativo da web está íntegro. A melhor opção seria definir um caminho para um algo como "/ Probe.aspx" que tem lógica para determinar a integridade do site. Por exemplo, você poderia usar contadores de desempenho a utilização da CPU ou medir o número de solicitações com falha. Ou você pode tentar acessar recursos de banco de dados ou estado de sessão para garantir que o aplicativo da web está funcionando.
- Se todos os pontos de extremidade de um perfil estão degradados, em seguida, Gerenciador de tráfego trata todos os pontos de extremidade como eficaz e o tráfego de rotas para todos os pontos de extremidade. Esse comportamento garante que os problemas com o mecanismo de sondagem não resultar em uma interrupção completa do seu serviço.

## <a name="troubleshooting"></a>Solução de problemas

Para solucionar uma falha de teste, você precisa de uma ferramenta que mostra o código de status HTTP retorno da URL de teste. Há muitas ferramentas disponíveis que mostram a resposta HTTP bruta.

* [Fiddler](http://www.telerik.com/fiddler)
* [Ondulação](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, você pode usar a guia rede das ferramentas de depuração de F12 no Internet Explorer para exibir as respostas HTTP.

Para este exemplo queremos ver a resposta do nossa URL de teste: http://watestsdp2008r2.cloudapp.net:80/teste. O PowerShell de exemplo a seguir ilustra o problema.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemplo de saída:

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Observe que podemos recebeu uma resposta de redirecionamento. Conforme mencionado anteriormente, qualquer StatusCode além de 200 é considerado uma falha. Gerenciador de tráfego altera o status de ponto de extremidade para Offline. Para resolver o problema, verifique a configuração de site para garantir que o StatusCode adequada pode ser retornado do caminho de teste. Reconfigure o teste de Gerenciador de tráfego para apontar para um caminho que retorna um 200.

Se o teste estiver usando o protocolo HTTPS, você talvez precise desabilitar o certificado de verificação para evitar erros SSL/TLS durante o teste. As seguintes instruções do PowerShell desativar a validação de certificado para a sessão atual do PowerShell:

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Próximas etapas

[Sobre métodos de roteamento de tráfego do Gerenciador de tráfego](traffic-manager-routing-methods.md)

[O que é o Gerenciador de tráfego](traffic-manager-overview.md)

[Serviços de nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Aplicativos Web do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações em Gerenciador de tráfego (referência de API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de tráfego Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
