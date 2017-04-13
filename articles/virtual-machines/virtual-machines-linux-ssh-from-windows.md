<properties 
    pageTitle="Usar teclas de SSH com o Windows para Linux VMs | Microsoft Azure" 
    description="Aprenda a gerar e usar teclas SSH em um computador Windows para se conectar ao computador virtual Linux no Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como usar SSH as chaves com o Windows no Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

Quando você se conectar ao Linux VMs (máquinas virtuais) no Azure, você deve usar a [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography) para fornecer uma maneira mais segura para efetuar login no seu VM Linux. Este processo envolve uma troca de chaves pública e privada usando o comando SSH (secure shell) para autenticar a mesmo em vez de um nome de usuário e senha. As senhas são vulneráveis a ataques, especialmente em VMs voltado para a Internet como servidores web de força bruta. Este artigo fornece uma visão geral de chaves SSH e como gerar as teclas apropriadas em um computador Windows.


## <a name="overview-of-ssh-and-keys"></a>Visão geral de SSH e chaves

Você pode com segurança faça logon em sua VM Linux usando chaves pública e privada:

- A **chave pública** é colocada na sua VM Linux ou qualquer outro serviço que você deseja usar com criptografia de chave pública.
- A **chave privada** é o que você apresenta para sua VM Linux quando fizer logon no, para verificar sua identidade. Proteger essa chave particular. Não compartilhá-lo.

Essas chaves públicas e privadas podem ser usadas em várias VMs e serviços. Não é necessário um par de chaves para cada máquina virtual ou serviço que você deseja acessar. Para obter uma visão mais detalhada, consulte [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography).

SSH é um protocolo de conexão criptografada que permite logon seguro em conexões desprotegidas. É o protocolo de conexão padrão para VMs Linux hospedado no Azure. Embora SSH próprio forneça uma conexão criptografada, usar senhas com conexões SSH ainda vulnerável a máquina virtual a força bruta ataques ou descoberta de senhas. Um método mais seguro e preferido, de conexão para uma máquina virtual usando SSH é usando essas chaves públicas e privadas, também conhecido como teclas de SSH.

Se não desejar usar as teclas SSH, você pode ainda fazer logon seu VMs Linux usando uma senha. Se sua máquina virtual não é exposto à Internet, o uso de senhas pode ser suficiente. No entanto, você ainda precisa gerenciar suas senhas para cada VM Linux e manter políticas de senha íntegra e práticas, como comprimento mínimo da senha e atualizando regularmente-los. O uso de chaves SSH reduz a complexidade do gerenciamento de credenciais individuais em várias VMs.


## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes do SSH

Você conectar e gerenciar VMs Linux no Azure usando um cliente **ssh** . Computadores com Windows geralmente não têm um cliente **ssh** instalado. Comuns Windows SSH clientes que você pode instalar estão incluídos nos seguintes pacotes:

- [Gito para Windows](https://git-for-windows.github.io/)
- [Acabamento](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] A atualização mais recente do Windows 10 aniversário inclui Bash para Windows. Esse recurso permite que você execute subsistema do Windows para utilitários Linux e acesso como um cliente SSH. Bash para Windows é ainda em desenvolvimento e é considerada uma versão beta. Para obter mais informações sobre Bash para Windows, consulte [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>Quais arquivos chaves que você precisa criar?

**Ssh-rsa** Azure exige pelo menos 2048 bits, formate chaves pública e privada. Se você estiver gerenciando recursos Azure usando o modelo de implantação do clássico, você também precisa gerar um PEM (`.pem` arquivo).

Aqui estão os cenários de implantação e os tipos de arquivos que você usa em cada:

1. chaves **SSH rsa** são necessárias para qualquer implantação usando o [portal do Azure](https://portal.azure.com)e implantações de gerente de recursos usando a [CLI do Azure](../xplat-cli-install.md).
    - Estas teclas são geralmente que precisam de todos os maioria das pessoas.
2. `.pem`é necessário criar VMs usando o [portal de clássico](https://manage.windowsazure.com)de arquivo. Também há suporte para estas teclas em implantações de clássico que usam a [CLI do Azure](../xplat-cli-install.md).
    - Você só precisa criar estas teclas adicionais e certificados se você estiver gerenciando recursos criados usando o modelo de implantação do clássico.


## <a name="install-git-for-windows"></a>Instalar gito para Windows

A seção anterior listados vários pacotes que incluem o `openssl` ferramenta para Windows. Esta ferramenta é necessária para criar chaves pública e privada. Os exemplos a seguir detalham sobre como instalar e usar **Gito para Windows**, embora você pode escolher qualquer pacote preferir. **Gito para Windows** dá acesso a algumas ferramentas de software de código-fonte aberto adicional ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) e utilitários que podem ser úteis ao trabalhar com VMs Linux.

1. Baixe e instale **Gito para Windows** do seguinte local: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Aceite as opções padrão durante o processo de instalação, a menos que você precise especificamente alterá-los.

3. Executar **Gito Bash** do **Menu Iniciar** > **gito** > **Gito Bash**. O console é semelhante ao seguinte exemplo:

    ![Gito para Windows Bash shell](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Criar uma chave privada

1. Na janela **Gito Bash** , use `openssl.exe` para criar uma chave privada. O exemplo a seguir cria uma chave chamada `myPrivateKey` e certificado denominado `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    A saída é semelhante ao seguinte exemplo:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Responda as solicitações para o nome do país, local, nome da organização, etc.

3. Sua nova chave privada e certificado são criados no seu diretório de trabalho atual. Práticas recomendadas de segurança, você deve definir as permissões em sua chave privada para que só você pode acessá-lo:

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Se você também precisa gerenciar recursos do clássico, converta o `myCert.pem` para `myCert.cer` (X509 com codificação DER certificado). Execute esta etapa opcional somente se você precisar especificamente gerenciar recursos de clássico mais antigos. 

    Converta o certificado usando o seguinte comando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Criar uma chave privada para acabamento

Acabamento é um cliente SSH comuns para Windows. Você está livre para usar qualquer cliente SSH que desejar. Para usar acabamento, você precisa criar um tipo de chave - uma chave privada de acabamento (PPK) adicional. Se você não quiser usar acabamento, pule esta seção.

O exemplo a seguir cria esta chave privada adicional especificamente para acabamento usar:

1. Use **Gito Bash** para converter sua chave privada em uma chave privada RSA que PuTTYgen pode entender. O exemplo a seguir cria uma chave chamada `myPrivateKey_rsa` da chave existente denominada `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Práticas recomendadas de segurança, você deve definir as permissões em sua chave privada para que só você pode acessá-lo:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Baixar e executar PuTTYgen do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Clique no menu: **arquivo** > **carregar uma chave privada**

4. Localize sua chave privada (`myPrivateKey_rsa` no exemplo anterior). O diretório padrão quando você iniciar **Gito Bash** é `C:\Users\%username%`. Alterar o filtro de arquivo para mostrar **todos os arquivos (\*.\*)**:

    ![Carregar a chave privada existente em PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Clique em **Abrir**. Um aviso indica que a chave foi importada com êxito:

    ![Tecla importada com êxito para PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Clique em **Okey** para fechar o prompt.

7. A chave pública é exibida na parte superior da janela de **PuTTYgen** . Copie e cole esta chave pública no portal do Azure ou modelo do Gerenciador de recursos do Azure, quando você cria uma VM Linux. Você também pode clicar em **Salvar chave pública** para salvar uma cópia no seu computador:

    ![Salve o arquivo de chave pública acabamento](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    O exemplo a seguir mostra como você faria copiar e colar esta chave pública no portal do Azure, quando você cria uma VM Linux. A chave pública geralmente é então armazenada em `~/.ssh/authorized_keys` em sua máquina virtual novo.

    ![Use a chave pública quando você cria uma máquina virtual no portal do Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. Volta **PuTTYgen**, clique em **Salvar chave privada**:

    ![Salve o arquivo de chave privada acabamento](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] Um prompt perguntando se você deseja continuar sem inserir uma senha para a sua chave. Uma senha é como uma senha anexada a sua chave privada. Mesmo que alguém obter sua chave privada, eles ainda não poderá autenticar usando apenas a chave. Eles também precisaria a senha. Sem uma senha, se alguém obtiver sua chave privada, eles podem efetuar logon em qualquer máquina virtual ou serviço que usa a chave. Recomendamos que você crie uma senha. No entanto, se você esquecer a senha, não há nenhuma maneira de recuperá-lo.

    Se desejar inserir uma senha, clique em **não**, insira uma senha na janela principal do PuTTYgen e clique em **Salvar chave privada** novamente. Caso contrário, clique em **Sim** para continuar sem fornecer a senha opcional.

8. Insira um nome e local para salvar o arquivo PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Use acabamento SSH para um computador Linux

Novamente, Acabamento é um cliente SSH comuns para Windows. Você está livre para usar qualquer cliente SSH que desejar. As etapas a seguir detalham sobre como usar sua chave privada para autenticar com sua máquina virtual de Azure usando SSH. As etapas são semelhantes em outros clientes chaves SSH em termos de precisar carregar sua chave privada para autenticar a conexão SSH.

1. Baixar e executar acabamento do seguinte local: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Preencha o nome do host ou o endereço IP da sua máquina virtual do portal do Azure:

    ![Abrir nova conexão de acabamento](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Antes de selecionar **Abrir**, clique em **Conexão** > **SSH** > guia**Auth** . Procure e selecione sua chave privada:

    ![Selecione sua chave privada acabamento para autenticação](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Clique em **Abrir** para se conectar à sua máquina virtual
 

## <a name="next-steps"></a>Próximas etapas
Você também pode gerar chaves pública e privada [usando o OS X e Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Para obter mais informações sobre Bash para Windows e os benefícios de ter ferramentas OSS prontamente disponíveis no seu computador com Windows, consulte [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).

Se você tiver problemas para usar SSH para se conectar à sua VMs Linux, consulte [Solucionar problemas de SSH conexões para uma máquina virtual Linux do Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).