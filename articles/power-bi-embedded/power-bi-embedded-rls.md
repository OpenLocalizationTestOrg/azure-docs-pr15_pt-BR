<properties
   pageTitle="Segurança em nível de linha com o Power BI inserida"
   description="Detalhes sobre a segurança em nível de linha com o Power BI inserida"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>Segurança em nível de linha com o Power BI inserida

Segurança em nível de linha (RLS) pode ser usada para restringir o acesso do usuário aos dados específicos dentro de um relatório ou um conjunto de dados, permitindo que vários usuários diferentes usar o mesmo relatório enquanto todos os dados de diferentes vendo. Power BI incorporado agora suporta configurados com RLS de conjuntos de dados.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Para aproveitar RLS, é importante que compreender três conceitos principais; Usuários, funções e regras. Vamos dar uma olhada mais atenta cada:

**Usuários** – estes são os usuários finais reais exibindo relatórios. No Power BI incorporados, os usuários são identificados pela propriedade username em um Token de aplicativo.

**Funções** – usuários pertencem a funções. Uma função é um contêiner para regras e pode ser nomeada algo como "Gerente de vendas" ou "Representante de vendas". No Power BI incorporados, os usuários são identificados pela propriedade funções em um Token de aplicativo.

**Regras** – funções tem regras, e essas regras são os filtros reais que vai ser aplicado aos dados. Isso pode ser tão simple quanto "país = EUA" ou algo muito mais dinâmico.

### <a name="example"></a>Exemplo

Para o restante deste artigo, forneceremos um exemplo de criação RLS e, em seguida, consumindo que dentro de um aplicativo inserido. Nosso exemplo usa o arquivo PBIX de [Amostra de análise de varejo](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Nosso exemplo de análise de varejo mostra as vendas de todos os armazenamentos em uma cadeia de varejo específico. Sem RLS, não importa qual Distrito gerente entrarem nele e modos de exibição do relatório, eles verá os mesmos dados. A gerência sênior tem determinado cada gerente de Distrito deverá ver apenas as vendas para os armazenamentos gerenciam e para fazer isso, podemos usar RLS.

RLS é criado na área de trabalho do Power BI. Quando o conjunto de dados e o relatório são abertos, podemos alternar para modo de exibição de diagrama para ver o esquema:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Aqui estão algumas coisas a serem observadas com esse esquema:

-   Todas as medidas, como o **Total de vendas**, são armazenadas na tabela de fatos de **vendas** .
-   Há quatro tabelas de dimensão relacionada adicionais: **Item**, **hora**, **armazenar**e **Distrito**.
-   As setas nas linhas de relação indicam que maneira filtros podem fluir de uma tabela para outra. Por exemplo, se um filtro for colocado na **hora [Data]**, no esquema atual-seria apenas filtrar valores na tabela **Sales** . Não há outras tabelas seriam afetadas por esse filtro desde que todas as setas nas linhas de relação apontem para a tabela de vendas e não ausente.
-   A tabela de **Distrito** indica quem é o gerente para cada Distrito:

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Com base neste esquema, se podemos aplicar um filtro para a coluna de **Gerente de Distrito** na tabela Distrito e se que filtrar correspondências o usuário exibindo o relatório, filtro também irá filtrar para baixo as tabelas de **armazenamento** e **vendas** para mostrar somente dados para esse determinado Distrito manager.

Veja como:

1.  Na guia modelagem, clique em **Gerenciar funções**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Crie uma nova função chamada **Gerenciador**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  Na tabela **Distrito** insira a seguinte expressão DAX: **[Distrito Manager] = username ()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Para certificar-se de que as regras estão funcionando, na guia **modelagem** , clique em **Exibir como funções**e, em seguida, insira o seguinte:  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Os relatórios agora mostrará dados como se você estava conectado como **Paulo Ma**.

Aplicar o filtro, a maneira como fizemos aqui, filtre para baixo todos os registros das tabelas **Distrito**, **armazenar**e **vendas** . No entanto, devido a direção de filtro nas relações entre **vendas** e a **hora**, tabelas de **vendas** e o **Item**e o **Item** e o **tempo** não serão filtradas para baixo.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Que pode ser okey para esse requisito, no entanto, se não quisermos gerentes para ver os itens para os quais não tenham as vendas, podemos poderia ativar bidirecional filtragem cruzada para a relação e fluem o filtro de segurança em ambas as direções. Isso pode ser feito editando a relação entre **as vendas** e **Item**, assim:

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

Agora, filtros também podem fluir da tabela de vendas para a tabela de **Item** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Observação** Se você estiver usando o modo DirectQuery para seus dados, você precisará ativar a filtragem de bidirecional entre selecionando essas duas opções:

1.  **Arquivo** -> **Opções e configurações** -> **Preview recursos** -> **Habilitar cruzada filtragem em ambas as direções para DirectQuery**.
2.  **Arquivo** -> **Opções e configurações** -> **DirectQuery** -> **Permitir medida irrestrita no modo DirectQuery**.


Para saber mais sobre filtragem cruzada bidirecional, baixe o whitepaper [bidirecional filtragem cruzada no SQL Server Analysis Services 2016 e Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) .

Isso quebra todo o trabalho que precisa ser feito no Power BI Desktop, mas não há um mais parte do trabalho que precisa ser feito para tornar a RLS regras que definimos trabalho no Power BI inserida. Os usuários são autenticados e autorizados pelo seu aplicativo e tokens de aplicativo são usados para conceder esse usuário acesso a um relatório do Power BI incorporado específico. Power BI inserida não tem quaisquer informações específicas sobre quem é o usuário. Para RLS funcione, você precisará passar algum contexto adicional como parte do seu token de aplicativo:
-   **nome de usuário** (opcional) – usado com RLS isso é uma cadeia de caracteres que pode ser usada para ajudar a identificar o usuário quando aplicando regras RLS. Consulte usando linha inserida de segurança de nível com o Power BI
-   **funções** – uma cadeia de caracteres que contém as funções para selecionar quando aplicando regras de segurança de nível de linha. Se passando mais de uma função, eles devem ser passados como uma matriz de cadeia de caracteres.

Se a propriedade username estiver presente, você também deve passar pelo menos um valor em funções.

O token de aplicativo completo terá aparência semelhante a esta:

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Agora, com todas as partes juntas, quando alguém fizer em nosso aplicativo para exibir este relatório, eles só poderá ver os dados que eles têm permissão para ver, conforme definido pelo nossa segurança em nível de linha.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Consulte também
[Segurança em nível de linha (RLS) com alimentação](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
