<properties 
   pageTitle="Criar certificados autoassinados para uma rede virtual ponto-a-Site entre conexões locais usando makecert | Microsoft Azure"
   description="Este artigo contém etapas para usar makecert criar certificados autoassinados Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Trabalhando com certificados autoassinados para conexões ponto-a-Site

Este artigo ajuda você a criar um certificado autoassinado usando **makecert**e, em seguida, gerar certificados de cliente dele. As etapas são gravadas para makecert no Windows 10. Makecert foi validada para criar certificados que são compatíveis com conexões de P2S. 

Para conexões de P2S, o método preferencial para certificados é usar sua solução de certificado de empresa, lembrando-se emitir certificados de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez de no formato 'Name\username de domínio NetBIOS'.

Se você não tiver uma solução corporativa, um certificado auto-assinado é necessário para permitir que clientes de P2S para se conectar a uma rede virtual. Estamos cientes que makecert foi substituída, mas ainda é um método válido para a criação de certificados autoassinados são compatíveis com conexões de P2S. Estamos trabalhando para outra solução para a criação de certificados autoassinados, mas desta vez, makecert é o método preferencial.

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Makecert é uma maneira de criar um certificado autoassinado. As etapas a seguir irão orientá-lo durante a criação de um certificado auto-assinado usando makecert. Essas etapas não são específicas do modelo de implantação. Eles são válidos para o Gerenciador de recursos e clássico.

### <a name="to-create-a-self-signed-certificate"></a>Para criar um certificado autoassinado

1. Em um computador executando o Windows 10, baixe e instale o [Kit de desenvolvimento de Software (SDK) do Windows para Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Após a instalação, você pode encontrar o utilitário de makecert.exe sob esse caminho: C:\Program Files (x86) \Windows Kits\10\bin\<arco >. 
        
    Exemplo:`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Em seguida, criar e instalar um certificado no repositório de certificados pessoais no seu computador. O exemplo a seguir cria um arquivo *. cer* correspondentes que você carregar no Azure durante a configuração P2S. Execute o comando a seguir, como administrador. Substitua *ARMP2SRootCert* e *ARMP2SRootCert.cer* com o nome que você deseja usar para o certificado.<br><br>O certificado estará localizado em certificados - certificados atual.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Para obter a chave pública

Como parte da configuração do Gateway VPN para conexões ponto-a-Site, a chave pública para o certificado raiz é carregada para o Azure.

1. Para obter um arquivo. cer do certificado, abra **certmgr. msc**. O certificado auto-assinado de atalho, clique em **todas as tarefas**e clique em **Exportar**. Isso abre o **Assistente de exportação de certificado**.

2. No assistente, clique em **Avançar**, selecione **não, não exportar a chave privada**e clique em **Avançar**.

3. Na página **Formato do arquivo de exportação** , selecione **Base 64 509 codificado (. CER).** Em seguida, clique em **Avançar**. 

4. No **arquivo de exportação**, **Navegue** até o local ao qual você deseja exportar o certificado. **Nome do arquivo**, nomeie o arquivo de certificado. Clique em **Avançar**.

5. Clique em **Concluir** para exportar o certificado.

 
### <a name="export-the-self-signed-certificate-optional"></a>Exporte o certificado auto-assinado (opcional)

Você talvez queira exportar o certificado auto-assinado e armazená-la com segurança. Se precisar ser, você pode posteriormente instalá-lo em outro computador e gerar mais certificados de cliente ou exportar outro arquivo. cer. Qualquer computador com um certificado de cliente instalado e que também está configurado com o cliente VPN PRI configurações podem se conectar à sua rede virtual via P2S. Por esse motivo, você quer certificar-se de que os certificados de cliente são gerados e instalados somente quando necessário e que o certificado auto-assinado é armazenado com segurança.

Para exportar o certificado auto-assinado como um. pfx, selecione o certificado raiz e use as mesmas etapas conforme descrito em [Exportar um certificado de cliente](#clientkey) para exportar.

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Você não instalar o certificado auto-assinado diretamente no computador cliente. Você precisa gerar um certificado de cliente do certificado auto-assinado. Depois de exportar e instalar o certificado de cliente para o computador cliente. As etapas a seguir não são específicas do modelo de implantação. Eles são válidos para o Gerenciador de recursos e clássico.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Parte 1 - gerar um certificado de cliente a partir de um certificado autoassinado

As etapas a seguir irão orientá-lo por meio de uma maneira de gerar um certificado de cliente a partir de um certificado auto-assinado. Você pode gerar vários certificados de cliente do mesmo certificado. Cada certificado de cliente pode ser exportado e instalado no computador cliente. 

1. No mesmo computador que você usou para criar o certificado auto-assinado, abra um prompt de comando como administrador.

2. Neste exemplo, "ARMP2SRootCert" refere-se para o certificado auto-assinado gerado. 
    - Altere *"ARMP2SRootCert"* para o nome da raiz auto-assinado que você está gerando o certificado de cliente do. 
    - Alterar *ClientCertificateName* para o nome que você deseja gerar um certificado de cliente para ser. 


    Modificar e executar o exemplo para gerar um certificado de cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado é um certificado de cliente denominado ClientCertificateName no seu armazenamento de certificado pessoal que foi gerado de certificado raiz ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Todos os certificados são armazenados em seu 'certificados - certificados atual' armazenar no seu computador. Você pode gerar como muitos certificados de cliente conforme necessário com base neste procedimento.

### <a name="clientkey"></a>Parte 2 - exportar um certificado de cliente

1. Para exportar um certificado de cliente, abra **certmgr. msc**. Clique com botão direito o certificado de cliente que você deseja exportar, clique em **todas as tarefas**e clique em **Exportar**. Isso abre o **Assistente de exportação de certificado**.

2. No assistente, clique em **Avançar**e em seguida, selecione **Sim, exportar a chave privada**e clique em **Avançar**.

3. Na página **Exportar formato de arquivo** , você pode deixar os padrões selecionados. Clique em **Avançar**. 
 
4. Na página de **segurança** , você deve proteger a chave privada. Se você selecionar usar uma senha, certifique-se lembrar da senha que você definiu para este certificado ou gravar. Clique em **Avançar**.

5. No **arquivo de exportação**, **Navegue** até o local ao qual você deseja exportar o certificado. **Nome do arquivo**, nomeie o arquivo de certificado. Clique em **Avançar**.

6. Clique em **Concluir** para exportar o certificado.  

### <a name="part-3---install-a-client-certificate"></a>Parte 3 - instalar um certificado de cliente

Cada cliente que você deseja se conectar à sua rede virtual usando uma conexão ponto-a-Site deve ter um certificado de cliente instalado. Este certificado é além do pacote de configuração de VPN necessário. As etapas a seguir orientam com instalando o certificado de cliente manualmente.

1. Localize e copie o arquivo *. pfx* para o computador cliente. No computador cliente, clique duas vezes o arquivo *. pfx* para instalar. Sair do **Local de armazenamento** como **Usuário atual**e, em seguida, clique em **Avançar**.

2. No **arquivo** para importar página, não faça as alterações. Clique em **Avançar**.

3. Na página de **proteção de chave particular** , inserir a senha para o certificado se você usou uma, ou verificar se a entidade de segurança que está instalando o certificado está correta, e clique em **Avançar**.

4. Na página **Repositório de certificados** , deixe o local padrão e clique em **Avançar**.

5. Clique em **Concluir**. O **Aviso de segurança** para a instalação do certificado, clique em **Sim**. O certificado é importado com êxito.

## <a name="next-steps"></a>Próximas etapas

Continue com a configuração de ponto-a-Site. 

- Para obter etapas de modelo de implantação do **Gerenciador de recursos** , consulte [Configurar uma conexão ponto-a-Site para um VNet usando o PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Para obter etapas **clássico** do modelo de implantação, consulte [Configurar um ponto-a-Site conexão de VPN para um VNet usando o portal de clássico](vpn-gateway-point-to-site-create.md).