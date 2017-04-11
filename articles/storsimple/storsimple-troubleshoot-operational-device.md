<properties 
   pageTitle="Solucionar problemas de um dispositivo StorSimple implantado | Microsoft Azure"
   description="Descreve como diagnosticar e corrigir os erros que ocorrem em um dispositivo de StorSimple que está implantado e operacional."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>Solucionar problemas de um dispositivo StorSimple operacional

## <a name="overview"></a>Visão geral

Este artigo fornece orientações úteis de solução de problemas para resolver problemas de configuração que podem ocorrer após o dispositivo StorSimple é implantado e operacional. Ele descreve problemas comuns, possíveis causas e etapas recomendadas para ajudá-lo a resolver problemas que podem ocorrer quando você executa o Microsoft Azure StorSimple. Essas informações se aplicam ao dispositivo físico StorSimple local e o dispositivo virtual StorSimple.

No final deste artigo, que você pode encontrar uma lista de códigos de erro que podem ocorrer durante a operação do Microsoft Azure StorSimple, bem como as etapas, você pode tomar para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo do Assistente de configuração para dispositivos operacionais

Usar o Assistente de configuração ([Chamar HcsSetupWizard][1]) para verificar a configuração do dispositivo e tomar medidas corretivas se necessário.

Quando você executa o Assistente de configuração em um dispositivo operacional e configurado anteriormente, o fluxo de processo é diferente. Você pode alterar somente as entradas a seguir:

- Endereço IP, máscara de sub-rede e gateway
- Servidor DNS primário
- Servidor NTP primário
- Configuração de proxy web opcional

O Assistente de configuração não executar as operações relacionadas ao registro de dispositivo e conjunto de senha.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante execuções subsequentes do Assistente de configuração

A tabela a seguir descreve os erros que podem ocorrer quando você executar o Assistente de configuração em um dispositivo operacional, causas possíveis para os erros e recomendados ações para resolvê-los. 

| Não. | Mensagem de erro ou condição | Causas possíveis | Ação recomendada |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Erro 350032: Este dispositivo já foi desativado. | Se você executar o Assistente de configuração em um dispositivo que está desativado, você verá este erro. | [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) para as próximas etapas. Um dispositivo desativado não pode ser colocado em serviço. A redefinição de fábrica pode ser necessária antes do dispositivo pode ser ativado novamente. |
|  2  | Invocar-HcsSetupWizard: ERROR_INVALID_FUNCTION (exceção de HRESULT: 0x80070001) | A atualização do servidor DNS está falhando. Configurações de DNS são configurações globais e são aplicadas em todas as interfaces de rede habilitado. | Ativar a interface e aplique as configurações de DNS novamente. Isso pode afetar a rede para outras interfaces habilitados porque essas configurações são globais. |
|  3  | O dispositivo parece estar online no portal de serviço do Gerenciador de StorSimple, mas quando você tenta concluir a configuração mínima e salvar a configuração, a operação falhará. | Durante a configuração inicial, o proxy da web não foi configurado, mesmo que houve um servidor proxy real no lugar. | Usar o [cmdlet Test-HcsmConnection] [ 2] para localizar o erro. [Contate o suporte do Microsoft](storsimple-contact-microsoft-support.md) se não for possível corrigir o problema. |
|  4  | Invocar-HcsSetupWizard: Valor não ficar dentro do intervalo esperado. | Uma máscara de sub-rede incorretas produz esse erro. Causas possíveis são: <ul><li> A máscara de sub-rede está ausente ou vazio.</li><li>O formato de prefixo Ipv6 está incorreto.</li><li>A interface está habilitado para a nuvem, mas o gateway está ausente ou incorreto.</li></ul>Observe que dados 0 são automaticamente habilitado para a nuvem se configurado através do Assistente de configuração. | Para determinar o problema, use sub-rede 0.0.0.0 ou 256.256.256.256 e examine a saída. Insira valores corretos para a máscara de sub-rede, gateway e prefixo Ipv6, conforme necessário. |
 
## <a name="error-codes"></a>Códigos de erro

Erros são listados em ordem numérica.

|Número do erro|Texto de erro ou descrição|Ação de usuário recomendadas|
|:---|:---|:---|
|10502|Ocorreu um erro ao acessar sua conta de armazenamento.|Aguarde alguns minutos e tente novamente. Se o erro persistir, faça o suporte da Microsoft de contato para as próximas etapas.|
|40017|A operação de backup falhou como um volume especificado na política de backup não foi encontrado no dispositivo.|Repetir o backup operação, se o erro persistir, contate o Microsoft Support. para as próximas etapas.|
|40018|A operação de backup falhou pois nenhum dos volumes de especificada na diretiva de backup foram encontrados no dispositivo. |Repetir o backup operação, se o erro persistir, contate o Microsoft Support. para as próximas etapas.|
|390061|O sistema está ocupado ou indisponível.|Aguarde alguns minutos e tente novamente. Se o erro persistir, faça o suporte da Microsoft de contato para as próximas etapas.|
|390143|Ocorreu um erro com código de erro 390143. (Erro desconhecido).|Se o erro persistir, contate o Microsoft Support para as próximas etapas.|

## <a name="next-steps"></a>Próximas etapas

Se você não consegue resolver o problema, [entre em contato com o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
