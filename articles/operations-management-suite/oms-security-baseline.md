<properties
   pageTitle="Segurança do pacote de gerenciamento de operações e linha de base de solução de auditoria | Microsoft Azure"
   description="Este documento explica como usar a segurança de OMS e auditoria solução para realizar uma avaliação de linha de base de todos os computadores monitoradas para fins de conformidade e segurança."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Avaliação de linha de base na solução de auditoria e segurança do pacote de gerenciamento de operações

Este documento ajuda você a usar a [solução de auditoria de segurança do pacote (OMS) de gerenciamento de operações e](operations-management-suite-overview.md) recursos de avaliação da linha de base para acessar o estado de segurança de seus recursos monitorados.

## <a name="what-is-baseline-assessment"></a>O que é a avaliação de linha de base?

Microsoft, juntamente com organizações e governamentais mundiais, define uma configuração de Windows que representa implantações de servidor altamente seguro. Esta configuração é um conjunto de chaves do registro, configurações de política de auditoria e configurações de política de segurança juntamente com valores de recomendados da Microsoft para essas configurações. Este conjunto de regras é conhecido como linha de base de segurança. Capacidade de avaliação da linha de base OMS segurança e auditoria perfeitamente pode examinar todos os seus computadores para fins de conformidade. 

Há três tipos de regras:

- **Regras do registro**: Verifique se as chaves do registro estão definidas corretamente.
- **Regras de política de auditoria**: regras relacionadas a sua política de auditoria.
- **Regras de política de segurança**: regras sobre as permissões do usuário na máquina.

> [AZURE.NOTE] Leia [Usar OMS segurança para avaliar a linha de base de configuração de segurança](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) para uma visão geral sobre esse recurso.

## <a name="security-baseline-assessment"></a>Avaliação da linha de base de segurança

Você pode examinar a avaliação da linha de base de segurança atual para todos os computadores monitorados por segurança de OMS e auditoria usando o painel.  Execute as seguintes etapas para acessar o painel de avaliação de linha de base de segurança:

1. No painel principal do **Pacote de gerenciamento de operações do Microsoft** , clique em bloco de **segurança e auditoria** .
2. No painel de **segurança e auditoria** , clique em **Avaliação da linha de base** em **Domínios de segurança**. No painel de **Avaliação da linha de base de segurança** é exibida conforme mostrado na imagem a seguir:
    
    ![Segurança OMS e avaliação de linha de base de auditoria](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Esse painel é dividido em três áreas principais:

- **Computadores em comparação com linha de base**: Esta seção fornece um resumo do número de computadores que foram acessados e a porcentagem de computadores que passado a avaliação. Ele também dá superiores 10 computadores e o resultado de porcentagem a avaliação.
- **Status de regras obrigatório**: Esta seção tem a intenção de trazer percepção das regras falhou por gravidade e falha regras por tipo. Observando o primeiro gráfico você pode identificar rapidamente se a maioria das regras de falha são críticas, ou não. Ele também fornece uma lista das 10 regras principais falhas e sua gravidade. O segundo gráfico mostra o tipo de regra que falhou durante a avaliação. 
- **Computadores ausentes avaliação da linha de base**: Esta seção listar os computadores que não foram acessados devido a incompatibilidade do sistema operacional ou falhas. 

### <a name="accessing-computers-compared-to-baseline"></a>Acessando computadores comparadas a linha de base

Ideal todos os computadores estão seja compatível com a avaliação da linha de base de segurança. No entanto Esperamos que em algumas circunstâncias isso não acontecem. Como parte do processo de gerenciamento de segurança, é importante incluir revisando os computadores que não conseguiu passar todos os testes de avaliação de segurança. Uma maneira rápida de visualizar que está selecionando a opção **computadores acessados** localizado na seção **computadores comparada a linha de base** . Você deve ver o resultado de pesquisa de log mostrando a lista de computadores, como mostra a tela a seguir:

![Resultados de computador acessado](./media/oms-security-baseline/oms-security-baseline-fig2.png)

O resultado de pesquisa é mostrado em um formato de tabela, onde a primeira coluna tem o nome do computador e a segunda cor tem o número de regras que falhou. Para recuperar as informações sobre o tipo de regra que falhou, clique no número de regras com falha além do nome do computador. Você verá um resultado semelhante ao mostrado na imagem a seguir:

![Detalhes de resultados do computador acessado](./media/oms-security-baseline/oms-security-baseline-fig3.png)

Esse resultado de pesquisa, você tem o total de regras de acessadas, o número de regras críticos que falhou, as regras de aviso e as regras de informações falhou.

### <a name="accessing-required-rules-status"></a>Acessando o status das regras necessárias

Depois de obter as informações sobre o número de porcentagem de computadores que passado a avaliação, talvez você queira obter mais informações sobre quais regras estão falhando de acordo com a importância. Esta visualização ajuda você a priorizar quais computadores devem ser abordados primeiro para garantir que eles serão compatíveis na próxima avaliação. Passe o mouse sobre a parte essencial do gráfico localizado no bloco **falhou regras por gravidade** , em **status de regras requerido** e clique nele. Você verá um resultado semelhante a tela a seguir:

![Falha regras por detalhes de gravidade](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

Este resultado de log você verá o tipo de regra de linha de base que falhou, a descrição dessa regra e a ID de enumeração de configuração comuns (CCE) dessa regra de segurança. Esses atributos devem ser suficiente para executar uma ação corretiva para corrigir esse problema no computador de destino.

> [AZURE.NOTE] Para obter mais informações sobre CCE, acesse o [Banco de dados de vulnerabilidade nacional](https://nvd.nist.gov/cce/index.cfm).

### <a name="accessing-computers-missing-baseline-assessment"></a>Acessando computadores ausentes avaliação da linha de base

OMS é compatível com o perfil de linha de base do membro de domínio no Windows Server 2008 R2 até o Windows Server 2012 R2. Linha de base do Windows Server 2016 ainda não está final e será adicionada assim que ele é publicado. Todos os outros sistemas operacionais examinados por meio de avaliação de linha de base de segurança de OMS e auditoria aparece sob a seção de **computadores ausentes avaliação da linha de base** .

## <a name="see-also"></a>Consulte também

Neste documento, você aprendeu sobre segurança do OMS e auditoria avaliação de linha de base. Para saber mais sobre a segurança do OMS, consulte os seguintes artigos:

- [Visão geral do pacote de gerenciamento (OMS) operações](operations-management-suite-overview.md)
- [Monitoramento e responder a alertas de segurança na segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-responding-alerts.md)
- [O monitoramento de recursos de segurança do pacote de gerenciamento de operações e solução de auditoria](oms-security-monitoring-resources.md)

