<properties 
   pageTitle="Segurança de StorSimple | Microsoft Azure" 
   description="Descreve os recursos de segurança e privacidade que protegem seu serviço StorSimple, dispositivo e dados nos locais e na nuvem." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>StorSimple segurança e proteção de dados

## <a name="overview"></a>Visão geral

Segurança é um grande problema para qualquer pessoa que está adotando uma nova tecnologia, especialmente quando a tecnologia é usada com dados confidenciais ou de propriedade. Conforme você avaliar tecnologias diferentes, você deve considerar o aumento de riscos e os custos para proteção de dados. Microsoft Azure StorSimple fornece uma solução de privacidade e segurança para proteção de dados, ajudando a garantir a: 

- **Confidencialidade** – somente entidades autorizadas podem exibir seus dados. 
- **Integridade** – somente autorizados entidades pode modificar ou excluir seus dados.

A solução do Microsoft Azure StorSimple consiste em quatro componentes principais que interagem uns com os outros:

- **Serviço de Gerenciador de StorSimple hospedado no Microsoft Azure** – o serviço de gerenciamento que você usa para configurar e provisionar o dispositivo StorSimple.
- **Dispositivo de StorSimple** – um dispositivo físico instalado no seu data center. Todos os hosts e clientes que geram dados conectam ao dispositivo StorSimple, e o dispositivo gerencia os dados e move-a para a nuvem Azure conforme apropriado.
- **Clientes/hosts conectados ao dispositivo** – os clientes em sua infraestrutura que se conectam ao dispositivo StorSimple e gerar dados que precisam ser protegido.
- **Armazenamento em nuvem** – o local na nuvem Azure onde os dados são armazenados.

As seções a seguir descrevem os recursos de segurança de StorSimple que ajudam a proteger cada um desses componentes e os dados armazenados nelas. Ele também inclui uma lista de perguntas que você possa ter sobre segurança do Microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-manager-service-protection"></a>Proteção de serviço do Gerenciador de StorSimple

O serviço do Gerenciador de StorSimple é um serviço de gerenciamento hospedado no Microsoft Azure e usados para gerenciar todos os dispositivos de StorSimple que sua organização adquiriu. Você pode acessar o serviço do Gerenciador de StorSimple usando suas credenciais organizacionais fazer logon no portal do Azure clássico por meio de um navegador da web. 

Acesso ao serviço do Gerenciador de StorSimple requer que sua organização tenha uma assinatura do Azure que inclui StorSimple. Sua assinatura controla os recursos que você pode acessar no portal de clássico do Azure. Se sua organização não tem uma assinatura do Azure e você quiser saber mais sobre eles, consulte [Inscreva-se no Azure como uma organização](../active-directory/sign-up-organization.md). 

Porque o serviço Gerenciador de StorSimple está hospedado no Azure, ele é protegido pelos recursos de segurança do Azure. Para obter mais informações sobre os recursos de segurança fornecidos pelo Microsoft Azure, vá para a [Central de confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção de dispositivos StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento de híbrido local que contém unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), junto com recursos de failover automático e controladores redundantes. Controladores de gerenciam armazenamento hierárquico, colocar atualmente usados (ou quente) dados em armazenamento local (em StorSimple dispositivo ou local servidores), ao mover menos dados usados com frequência na nuvem.

Somente autorizados StorSimple dispositivos são permitidos para ingressar o serviço do Gerenciador de StorSimple que você criou na sua assinatura do Azure. Para autorizar um dispositivo, você deve registrá-lo com o serviço Gerenciador de StorSimple, fornecendo a chave do registro de serviço. A chave do registro de serviço é uma chave aleatória de 128 bits gerada no portal de clássico do Azure. 

![Chave do registro de serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para saber como obter uma chave de registro de serviço, vá para [etapa 2: obter a chave do registro de serviço](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

A chave de registro do serviço é uma chave longa que contém 100 + caracteres. Você pode copiar a tecla e salvá-lo em um arquivo de texto em um local seguro para que você possa usá-lo para autorizar dispositivos adicionais conforme necessário. Se a chave do registro de serviço for perdida depois que você registrar seu primeiro dispositivo, você pode gerar uma nova chave do serviço do Gerenciador de StorSimple. Isso não afetará a operação dos dispositivos existentes. 

Depois que um dispositivo é registrado, ele usa tokens para se comunicar com o Microsoft Azure. A chave de registro do serviço não é usada após o registro do dispositivo.

> [AZURE.NOTE] Recomendamos que você gerar novamente a chave do registro de serviço após cada uso.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteger sua solução StorSimple por meio de senhas

As senhas são um aspecto importante da segurança do computador e são usadas extensivamente na solução StorSimple para ajudar a garantir que seus dados estão acessíveis apenas a usuários autorizados. StorSimple permite que você configure as seguintes senhas:

- Senha de administrador do dispositivo StorSimple
- Desafio senhas de iniciador e o destino de Handshake Authentication Protocol (CHAP)
- Gerenciador de instantâneo StorSimple senha

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a senha de administrador do dispositivo StorSimple

Windows PowerShell para StorSimple é uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. Windows PowerShell para StorSimple possui recursos que permitem que você registrar seu dispositivo, configurar a interface de rede em seu dispositivo, instale certos tipos de atualizações, solucionar problemas de seu dispositivo acessando a sessão de suporte e alteram o estado do dispositivo. Você pode acessar o Windows PowerShell para StorSimple conectando-se ao console serial no dispositivo ou usando o Windows PowerShell remoto.

Conexão remota do PowerShell pode ser feita sobre HTTPS ou HTTP. Se gerenciamento remoto em HTTPS estiver ativado, você precisará baixar o certificado de gerenciamento remoto do dispositivo e instale-o no cliente remoto. Para saber mais sobre conexão remota do PowerShell, vá para [conectar-se remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md).

Depois que você usa o Windows PowerShell para StorSimple para conectar o dispositivo, você precisará fornecer a senha de administrador do dispositivo para fazer logon no dispositivo.

![Senha de administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Mantenha as seguintes práticas recomendadas em mente:

- Gerenciamento remoto está desativado por padrão. Você pode usar o serviço do Gerenciador de StorSimple para habilitá-la. Como prática recomendada de segurança, acesso remoto deve ser habilitado apenas durante o período de tempo que é realmente necessária.
- Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não tenham uma perda de conectividade inesperado.
- O serviço do Gerenciador de StorSimple não é possível recuperar senhas existentes: somente poderá redefini-las. Recomendamos que você armazene todas as senhas em um local seguro para que você não tem a redefinir uma senha, se for esquecida. Se você precisar redefinir uma senha, certifique-se de notificar todos os usuários antes de você redefini-la. 

Você pode acessar a interface do Windows PowerShell, usando uma conexão serial para o dispositivo. Você também pode acessá-lo remotamente usando HTTP ou HTTPS, que fornecem segurança adicional. HTTPS fornece um nível maior de segurança que seja uma série ou conexão de HTTP. No entanto, para usar HTTPS, primeiro você deve instalar um certificado no computador cliente que vai acessar o dispositivo. Você pode baixar o certificado de acesso remoto da página de configuração de dispositivo no serviço do Gerenciador de StorSimple. Se o certificado para acesso remoto for perdido, você deve baixar um novo certificado e propagá-lo para todos os clientes que estão autorizados a usar o gerenciamento remoto.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Desafio senhas de iniciador e o destino de Handshake Authentication Protocol (CHAP)

CHAP é um esquema de autenticação usado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação baseia-se em uma senha compartilhada. CHAP pode ser unidirecional (unidirecional) ou mútuo (bidirecional). Com o CHAP unidirecional, o destino (o dispositivo StorSimple) autentica um iniciador (host). Comum ou inversa CHAP requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. Seu StorSimple pode ser configurado para usar qualquer um dos métodos.

Lembre-se dos seguintes quando você configura o CHAP:

- O nome de usuário CHAP deve conter menos de 233 caracteres.
- A senha CHAP deve estar entre 12 e 16 caracteres. Tentativa de usar um nome de usuário mais longo ou senha resultará em uma falha de autenticação do host do Windows.
- Você não pode usar a mesma senha para o iniciador CHAP e o destino CHAP.
- Depois de definir a senha, ela pode ser alterada, mas não é possível recuperá-la. Se a senha é alterada, certifique-se de notificar todos os usuários de acesso remoto para que eles possam conectar com êxito ao dispositivo StorSimple.

Para obter mais informações sobre CHAP e como configurá-lo para a sua solução de StorSimple, vá para [Configurar o CHAP para seu dispositivo de StorSimple](storsimple-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Gerenciador de instantâneo StorSimple senha

Gerenciador de instantâneo StorSimple é um snap-in do Console de gerenciamento da Microsoft (MMC) que usa o serviço de cópia de sombra de Volume do Windows e grupos de volume para gerar backups consistentes com o aplicativo. Além disso, você pode usar o Gerenciador de instantâneo StorSimple para criar clonar e agendas de backup ou restaurar volumes.

Quando você configura um dispositivo para usar o Gerenciador de instantâneo StorSimple, você deverão fornecer a senha do Gerenciador de instantâneo StorSimple. Essa senha é definida pela primeira vez no Windows PowerShell para StorSimple durante o registro. A senha também pode ser definida e alterada do serviço do Gerenciador de StorSimple. Essa senha autentica o dispositivo com o Gerenciador de instantâneo StorSimple.

![Gerenciador de instantâneo StorSimple senha](./media/storsimple-security/SnapshotMgrPassword.png)

A senha do Gerenciador de instantâneo StorSimple deve ser 14 a 15 caracteres e deve conter 3 ou mais de uma combinação de caracteres em maiusculas, minúsculas, numérico e especial. Depois de definir a senha do Gerenciador de instantâneo StorSimple, ele pode ser alterado, mas não é possível recuperá-la. Se você alterar a senha, certifique-se de notificar todos os usuários remotos.

Para obter mais informações sobre o Gerenciador de instantâneo StorSimple, vá para [o que é o Gerenciador de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Práticas recomendadas de senha

Recomendamos que você use as seguintes diretrizes para ajudar a garantir que StorSimple senhas fortes e bem protegido:

- Altere suas senhas a cada três meses. Alterar as senhas é imposta anualmente.
- Use senhas fortes. Para obter mais informações, vá para [criar senhas mais seguras e protegê-las](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
- Sempre usar senhas diferentes para mecanismos de acesso diferente; cada um das senhas que você especificar deve ser exclusiva.
- Não compartilhe senhas com qualquer pessoa que não está autorizado a acessar o dispositivo StorSimple.
- Não falar sobre uma senha na frente de outras pessoas ou dica o formato de uma senha.
- Se você suspeita que uma conta ou senha foi comprometida, relate o incidente para seu departamento de segurança de informações.
- Trate todas as senhas como informações confidencial. 

## <a name="storsimple-data-protection"></a>Proteção de dados de StorSimple

Esta seção descreve os recursos de segurança de StorSimple que protegem dados em trânsito e dados armazenados.

Conforme descrito em outras seções, senhas são usadas para autorizar e autenticar usuários antes que eles podem acessar sua solução de StorSimple. Outra consideração de segurança está protegendo dados de usuários não autorizados, enquanto ele está sendo transferido entre sistemas de armazenamento e enquanto ele está armazenado. As seções a seguir descrevem os recursos de proteção de dados fornecidos com StorSimple.

> [AZURE.NOTE] Duplicação fornece proteção adicional para dados armazenados no dispositivo StorSimple e em armazenamento do Microsoft Azure. Quando dados é eliminação de duplicação, os objetos de dados são armazenados separadamente dos metadados usados para mapear e acessá-los: há nenhum contexto de nível de armazenamento disponível para reconstruir os dados com base em estrutura de volume, sistema de arquivos ou nome do arquivo.

## <a name="protect-data-flowing-through-the-service"></a>Proteger os dados fluindo através do serviço

A principal finalidade do serviço StorSimple Manager é gerenciar e configurar o dispositivo StorSimple. O serviço do Gerenciador de StorSimple executa no Microsoft Azure. Você usa o portal de clássico Azure para inserir dados de configuração de dispositivo e, em seguida, o Microsoft Azure usa o serviço de Gerenciador de StorSimple para enviar os dados para o dispositivo. StorSimple usa um sistema assimétricos dos pares de chaves para ajudar a garantir que um compromisso do serviço do Azure não resultará em um compromisso das informações armazenadas. 

![Criptografia de dados em trânsito](./media/storsimple-security/DataEncryption.png)

O sistema de chave assimétrico ajuda a proteger os dados que fluem através do serviço, da seguinte maneira:

1. Um certificado de criptografia de dados que usa uma assimétrica chave de pública e privada par é gerado no dispositivo e é usado para proteger os dados. As teclas são geradas quando o primeiro dispositivo é registrado. 
2. As chaves de certificado de criptografia de dados são exportadas para um arquivo de troca de informações pessoais (. pfx) que é protegido pela chave de criptografia de dados de serviço, que é uma chave de 128 bits forte que é gerada aleatoriamente pelo primeiro dispositivo durante o registro.
3. A chave pública do certificado com segurança é tornada disponível para o serviço do Gerenciador de StorSimple e a chave privada permanece com o dispositivo.
4. Inserindo o serviço de dados são criptografados com o público chave e descriptografado usando a chave particular armazenada no dispositivo, garantindo que o serviço do Azure não pode descriptografar os dados fluindo no dispositivo.

A chave de criptografia de dados de serviço é gerada no somente o primeiro dispositivo registrado com o serviço. Todos os dispositivos subsequentes que são registrados com o serviço devem usar a mesma chave de criptografia de dados de serviço. 

> [AZURE.IMPORTANT] 
> 
> É muito importante fazer uma cópia da chave de criptografia de dados de serviço e salvá-lo em um local seguro. Uma cópia da chave de criptografia de dados de serviço deve ser armazenada de forma que ele possa ser acessado por uma pessoa autorizada e possa ser comunicado facilmente ao administrador do dispositivo.
>
> Se a chave de criptografia de dados de serviço for perdida, uma pessoa de suporte da Microsoft pode ajudar você a recuperá-la, desde que você tenha pelo menos um dispositivo em um estado online. Recomendamos que você altere a chave de criptografia de dados de serviço após recuperá-la. Para obter instruções, vá para [alterar a chave de criptografia de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Você pode alterar a chave de criptografia de dados de serviço e o certificado de criptografia de dados correspondente, selecionando a opção **alterar a chave de criptografia de dados de serviço** no painel de serviço. Para garantir que a segurança de dados não seja comprometida, você deve usar um dispositivo StorSimple físico para alterar a chave de criptografia de dados de serviço. Alterar as chaves de criptografia requer que todos os dispositivos ser atualizado com a nova chave. Portanto, recomendamos que você altere a tecla quando todos os dispositivos estiverem online. Se dispositivos estiverem offline, suas chaves podem ser alteradas em um horário diferente. Os dispositivos com chaves desatualizadas ainda poderão executar backups, mas eles não poderão restaurar dados até que a chave é atualizada. Para obter mais informações, vá para [usar o painel de StorSimple Gerenciador de serviço](storsimple-service-dashboard.md).

A chave de criptografia de dados de serviço e o certificado de criptografia de dados não expiram. No entanto, recomendamos que você altere a chave de criptografia de dados de serviço anualmente para ajudar a evitar compromisso de chave.

## <a name="protect-data-at-rest"></a>Proteger os dados inativos

O dispositivo StorSimple gerencia dados armazenando-o em níveis localmente e na nuvem, dependendo da frequência de uso. Todas as máquinas host que estão conectadas ao dispositivo enviam dados para o dispositivo, que move os dados na nuvem, conforme apropriado. Dados são transferidos do dispositivo para a nuvem com segurança pela Internet. Cada dispositivo possui um destino iSCSI que revela todos os volumes compartilhados nesse dispositivo. Todos os dados são criptografados antes que sejam enviadas para armazenamento em nuvem. 

![Chave de criptografia de armazenamento de nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e a integridade dos dados movidos para a nuvem, o StorSimple permite que você defina chaves de criptografia de armazenamento de nuvem da seguinte maneira:

- A chave de criptografia de armazenamento de nuvem que você especifica quando você cria um contêiner de volume. A chave não pode ser modificada ou adicionada posteriormente. 
- Todos os volumes em um contêiner de volume compartilham a mesma chave de criptografia. Se você quiser um formulário diferente de criptografia para um volume específico, é recomendável que você crie um novo contêiner de volume para hospedar o volume.
- Quando você insere a chave de criptografia de armazenamento de nuvem no serviço do Gerenciador de StorSimple, a chave é criptografada usando a parte pública da chave de criptografia de dados de serviço e, em seguida, enviadas ao dispositivo.
- A chave de criptografia de armazenamento de nuvem não está armazenada em qualquer lugar no serviço e é conhecida somente para o dispositivo.
- Especificar uma chave de criptografia de armazenamento de nuvem é opcional. Você pode enviar dados que foram criptografados no host para o dispositivo.

### <a name="additional-security-best-practices"></a>Práticas recomendadas de segurança adicionais

- Dividir o tráfego: isolar sua SAN iSCSI de tráfego de usuário em uma LAN corporativa implantar uma rede totalmente separada e usando VLANs a onde isolamento físico não é uma opção. Uma rede dedicada de armazenamento iSCSI garantirá a segurança e o desempenho dos seus dados essenciais. Misturar tráfego de armazenamento e o usuário através de uma LAN corporativa não é recomendado e pode aumentar a latência e causar falhas de rede.

- Para segurança de rede do lado do host, use as interfaces de rede que oferecem suporte ao mecanismo de descarregar TCP/IP (TOE). TOE reduz a carga de CPU pelo processamento TCP no adaptador de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger os dados por meio de contas de armazenamento

Cada assinatura do Microsoft Azure pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um namespace exclusivo para trabalhar com dados armazenados na nuvem Azure). Acesso a uma conta de armazenamento é controlado pelas teclas de acesso e de assinatura associadas a essa conta de armazenamento. 

Quando você cria uma conta de armazenamento, Microsoft Azure gera duas teclas de acesso de armazenamento de 512 bits, um dos quais é usado para autenticação quando o dispositivo StorSimple acessa a conta de armazenamento. Observe que apenas um destas teclas está em uso. A outra chave é mantida na reserva, permitindo que você girar as chaves periodicamente. Para girar chaves, você tornar a chave secundária ativo e exclua a chave primária. Você pode criar uma nova chave para uso durante a rotação próxima. (Por razões de segurança, muitos data centers exigem a rotação de chaves). 

Recomendamos que você siga estas práticas recomendadas para a rotação de chaves:

- Você deve girar chaves de conta de armazenamento regularmente para ajudar a garantir que sua conta de armazenamento não é acessada por usuários não autorizados.
- Periodicamente, o administrador do Azure deve alterar ou gerar novamente a chave primária ou secundária usando a seção de armazenamento do portal de clássico do Azure para acessar diretamente a conta de armazenamento.


## <a name="protect-data-via-encryption"></a>Proteger os dados por meio de criptografia

StorSimple usa os seguintes algoritmos de criptografia para proteger os dados armazenados em ou viajando entre os componentes de sua solução de StorSimple.

| Algoritmo | Comprimento da chave | Aplicativos/protocolos/comentários |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | 1.5 RSA PKCS 1 é usado pelo portal de clássico do Azure para criptografar dados de configuração que são enviados para o dispositivo: por exemplo, o armazenamento de credenciais, configuração de dispositivo de StorSimple, de conta e chaves de criptografia de armazenamento em nuvem. |
| AES       | 256        | AES com CBC é usado para criptografar a parte pública da chave de criptografia de dados de serviço antes de ser enviado portal do Azure clássico do dispositivo StorSimple. Ele também é usado pelo dispositivo StorSimple para criptografar dados antes dos dados são enviados para a conta de armazenamento de nuvem. |


## <a name="storsimple-virtual-device-security"></a>Segurança do dispositivo virtual StorSimple

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

A seguir estão algumas perguntas e respostas sobre segurança e StorSimple do Microsoft Azure.

**Q:** Meu serviço está comprometido. O que deve ser Minhas próximas etapas?

**A:** Imediatamente, você deve alterar a chave de criptografia de dados de serviço e as teclas de conta de armazenamento para a conta de armazenamento que está sendo usado para dados hierárquico. Para obter instruções, vá para: 

- [Alterar a chave de criptografia de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Rotação de chaves de contas de armazenamento](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Eu tenho um novo dispositivo StorSimple que está solicitando a chave do registro de serviço. Como recuperá-lo?

**A:** Esta tecla foi criada quando você criou primeiro o serviço do Gerenciador de StorSimple. Quando você usa o serviço do Gerenciador de StorSimple para conectar o dispositivo, você pode usar a página de início rápido do serviço para exibir ou gerar novamente a chave do registro de serviço. Gerar uma nova chave de registro do serviço não afetará os dispositivos registrados existentes. Para obter instruções, vá para:

- [Exibir ou gerar novamente a chave do registro de serviço](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** Perdi minha chave de criptografia de dados de serviço. O que fazer?

**A:** Contate o suporte da Microsoft. Ele podem fazer logon em uma sessão de suporte no seu dispositivo e ajuda para recuperar a chave (desde que pelo menos um dispositivo estiver online). Imediatamente depois de obter a chave de criptografia de dados de serviço, você deve alterá-la para garantir que a nova chave é conhecida somente para você. Para obter instruções, vá para:

- [Alterar a chave de criptografia de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  Autorizado um dispositivo para uma alteração de chave de criptografia de dados serviço, mas não iniciou o processo de alteração da chave. O que devo fazer?

**A:** Se o período de tempo limite tiver expirado, você precisará autorize o dispositivo para que a alteração de chave de criptografia de dados serviço de novo e iniciar o processo novamente.

**Q:**  Mudei a chave de criptografia de dados de serviço, mas não foi possível atualizar outros dispositivos dentro de 4 horas. Tenho que iniciar novamente?

**A:** O período de tempo de 4 horas é somente para iniciar a alteração. Depois de iniciar o processo de atualização do dispositivo StorSimple autorizados, a autorização é válida até que todos os dispositivos sejam atualizados.

**Q:** Nosso administrador StorSimple deixou a empresa. O que devo fazer?

**A:** Alterar e redefinir as senhas que permitem o acesso ao dispositivo StorSimple e alterar a chave de criptografia de dados de serviço para garantir que as novas informações não são conhecidas a pessoas não autorizadas. Para obter instruções, vá para:

- [Usar o serviço do Gerenciador de StorSimple para alterar suas senhas storsimple](storsimple-change-passwords.md)
- [Alterar a chave de criptografia de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Configurar o CHAP para seu dispositivo de StorSimple](storsimple-configure-chap.md)

**Q:** Quero fornecer a senha do Gerenciador de instantâneo StorSimple para um host que está se conectando ao dispositivo StorSimple, mas a senha não está disponível. O que posso fazer?

**A:** Se você tiver esquecido a senha, você deve criar um novo. Em seguida, certifique-se de informar todos os usuários existentes que a senha foi alterada e que eles devem atualizar seus clientes para usar a nova senha. Para obter instruções, vá para:

- [Alterar a senha de StorSimple Gerenciador de instantâneo](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** O certificado para acesso remoto para o Windows PowerShell para StorSimple foi alterado no dispositivo. Como posso atualizar meus clientes de acesso remoto?

**A:** Você pode baixar o novo certificado do serviço do Gerenciador de StorSimple e fornecê-lo para ser instalado no repositório de certificados seus clientes de acesso remoto. Para obter instruções, vá para:

- [Cmdlet do certificado de importação](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** É Meus dados protegido o Gerenciador de StorSimple serviço for comprometido?

**A:** Dados de configuração do serviço é sempre criptografados com sua chave pública, quando você vê-lo em um navegador da web. Porque o serviço não tem acesso à chave particular, o serviço não será capaz de ver todos os dados. Se o serviço Gerenciador de StorSimple for comprometido, há nenhum impacto, pois não há nenhuma chaves armazenadas no serviço do Gerenciador de StorSimple.

**Q:** Se alguém obtiver acesso ao certificado de criptografia de dados, serão meus dados comprometidos?

**A:** Microsoft Azure armazena a chave de criptografia de dados do cliente (arquivo. pfx) em um formato criptografado. Porque o arquivo. pfx está criptografado e o serviço de StorSimple não tem a chave de criptografia de dados de serviço para descriptografar o arquivo. pfx, simplesmente obtendo acesso ao arquivo. pfx não expõe qualquer segredos.

**Q:** O que acontece se uma entidade governamental solicitar Microsoft meus dados?

**A:** Como todos os dados são criptografados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental deve pergunte ao cliente para os dados. 

## <a name="next-steps"></a>Próximas etapas

[Implantar seu dispositivo StorSimple](storsimple-deployment-walkthrough.md).
 
