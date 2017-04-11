O serviço de Backup do Azure tem dois tipos de compartimentos - Cofre de Backup e o Cofre de serviços de recuperação. O Cofre de Backup veio primeiro. Em seguida, o Cofre de serviços de recuperação surgirem para as implantações expandido do Gerenciador de recursos de suporte. Recomendamos utilizar implantações do Gerenciador de recursos, a menos que você precise especificamente uma implantação clássico.

| **Implantação** | **Portal** | **Cofre** |
|-----------|------|-----|
|Clássico|[Clássico](https://manage.windowsazure.com)|Backup|
|Gerenciador de recursos|[Azure](https://portal.azure.com)|Serviços de recuperação|

> [AZURE.NOTE] Compartimentos de backup não podem proteger implantado Gerenciador de recursos de soluções. No entanto, você pode usar um cofre de serviços de recuperação proteger servidores implantados modo clássico e VMs.  
