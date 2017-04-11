<properties
    pageTitle="Trabalhando com domínios personalizados no Proxy de aplicativo do Azure AD | Microsoft Azure"
    description="Capas como trabalhar com domínios personalizados no Proxy de aplicativo do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Trabalhando com domínios personalizados no Proxy de aplicativo do Azure AD

Usar um domínio padrão permite que você defina a mesma URL como o URL interno e externo para acessar o aplicativo para que os usuários tenham apenas uma URL de lembrar acessar o aplicativo, independentemente de onde elas estão acessando de. Isso também permite criar um atalho de único no painel de acesso para o aplicativo. Se você usar o domínio padrão fornecido pelo Proxy de aplicativo do Azure AD, não há nenhuma configuração adicional que você precisa habilitar o seu domínio. Se você usa um domínio personalizado, há algumas coisas que você precisa fazer para certificar-se de que o Proxy de aplicativo reconhece seu domínio e valida seus certificados.

## <a name="selecting-your-custom-domain"></a>Selecionando seu domínio personalizado

1. Publica seu aplicativo de acordo com as instruções em [publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md).
2. Quando o aplicativo for exibido na lista de aplicativos, selecione-o e clique em **Configurar**.
3. Em **URL externo**, insira seu domínio personalizado.
4. Se a URL externa for https, você será solicitado para carregar um certificado para que esse Azure possa validar a URL do aplicativo. Você também pode carregar um certificado curinga que corresponde a URL externa do aplicativo. Esse domínio deve estar dentro da lista dos seus [Azure verificado domínios](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure deve ter um certificado para a URL de domínio do aplicativo ou um certificado curinga que corresponda à URL externo para o aplicativo.
5. Certifique-se adicionar um registro DNS que direcione a URL interna para o aplicativo que permite que você tenha a mesma URL para o acesso interno e externo e um único atalho para o aplicativo na lista de aplicativos do usuário.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Perguntas frequentes sobre como trabalhar com domínios personalizados

P: é possível selecionar um certificado já carregou sem carregá-la novamente?  
R: anteriormente certificados carregados são vinculados automaticamente a um aplicativo e há exatamente um certificado correspondência o nome do aplicativo host.  

P: como adicionar um certificado e qual formato devem o certificado exportado ser carregado no?  
R: o certificado deve ser carregado na página de configuração do aplicativo. O certificado deve ser um arquivo PFX.  

P: os certificados ECC podem ser usados?  
R: não há nenhuma limitação explícita em métodos de assinatura.  

P: os certificados de SAN podem ser usados?  
R: Sim.  

P: os certificados curinga podem ser usados?  
R: Sim.  

P: um certificado diferente ser usado em cada aplicativo?  
R: Sim, a menos que os dois aplicativos compartilham o mesmo host externo.  

P: se eu registrar um novo domínio, posso usar esse domínio?  
R: Sim, a lista de domínios é tio verificado lista do locatário de domínios.  

P: o que acontece quando um certificado expira?  
R: você receberá um aviso na seção certificado na página de configuração do aplicativo. Quando um usuário tenta acessar o aplicativo, um aviso de segurança será exibida.  

P: o que devo fazer se eu quiser substituir um certificado para um determinado aplicativo?  
R: carregar um novo certificado da página de configuração do aplicativo.  

P: posso excluir um certificado e substituí-lo?  
R: quando você carrega um novo certificado, se o certificado antigo não estiver em uso por outro aplicativo, ele serão automaticamente excluído.  

P: o que acontece quando um certificado foi revogado?  
R: verificações de revogação de não são executadas para certificados. Quando um usuário tenta acessar o aplicativo, dependendo do navegador, um aviso de segurança pode aparecer.  

P: posso usar um certificado auto-assinado?  
R: Sim, certificados autoassinados são permitidos. Observe que, se você estiver usando uma autoridade de certificação privada, o CDP (ponto de distribuição do ponto de revogação de certificado) para o certificado deve ser público.  

P: existe um lugar para ver todos os certificados de meu locatário?  
R: isso não há suporte na versão atual.  


## <a name="see-also"></a>Consulte também

- [Publicar aplicativos com o Proxy de aplicativo](active-directory-application-proxy-publish.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Adicionar seu nome de domínio personalizado ao Azure AD](active-directory-add-domain.md)

Para as últimas notícias e atualizações, confira o [blog do Proxy de aplicativo](http://blogs.technet.com/b/applicationproxyblog/)
