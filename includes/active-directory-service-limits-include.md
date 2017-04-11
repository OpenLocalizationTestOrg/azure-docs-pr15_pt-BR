Aqui estão as restrições de uso e outros limites de serviço para o serviço do Active Directory do Azure.

| Categoria | Limites |
|---|---|
| Diretórios | Um único usuário só pode ser associado um máximo de 20 diretórios do Active Directory do Azure.<br />Exemplos de combinações possíveis: <ul> <li>Um único usuário cria 20 diretórios.</li><li>Um único usuário é adicionado a 20 diretórios como membro.</li><li>Um único usuário cria 10 diretórios e posterior é adicionado por outras pessoas ao 10 diretórios diferentes.</li></ul> |  
| Objetos | <ul><li>Um máximo de 500.000 objetos pode ser usado em um único diretório pelos usuários da edição gratuita do Azure Active Directory.</li><li>Um usuário não-administrador pode criar objetos não mais de 250.</li></ul> |
| Extensões de esquema | <ul><li>Extensões de tipo de cadeia de caracteres podem ter o máximo de 256 caracteres. </li><li>Extensões de tipo binário limitam-se a 256 bytes.</li><li>100 valores de extensão (em todos os tipos e todos os aplicativos) podem ser escritos para qualquer objeto único.</li><li>Somente "usuário", "Grupo", "TenantDetail", "Dispositivo", "Aplicativo" e "ServicePrincipal" entidades podem ser estendidas com tipo de "Cadeia" ou atributos de valor único tipo de "Binário".</li><li>Extensões de esquema estão disponíveis apenas na versão de API do Graph 1.21-visualização. O aplicativo deverá ter acesso de gravação para registrar uma extensão.</li></ul> |
| Aplicativos | Um máximo de 10 usuários pode ser proprietários de um único aplicativo. |
| Grupos | <ul><li>Um máximo de 10 usuários pode ser proprietários de um único grupo.</li><li>Qualquer número de objetos pode ser membros de um único grupo no Active Directory do Azure.</li><li>O número de membros em um grupo que você pode sincronizar a partir do Active Directory local ao Azure Active Directory está limitado aos membros de 15K, usando Azure diretório sincronização do Active Directory (DirSync).</li><li>O número de membros em um grupo que você pode sincronizar a partir do Active Directory local ao Azure Active Directory usando o Azure AD Connect está limitado aos membros de 50K.</li></ul> |
| Painel de acesso | <ul><li>Não há nenhum limite para o número de aplicativos que podem ser vistos no painel de acesso por usuário final, para usuários atribuídos licenças para Azure AD Premium ou o pacote de mobilidade do Enterprise.</li><li>Um máximo de 10 blocos de aplicativo (exemplos: caixa, Salesforce ou Dropbox) podem ser vistos no painel de acesso para cada usuário final para usuários atribuídos licenças gratuitamente ou edições Azure AD básicas do Active Directory do Azure. Esse limite não se aplica a contas de administrador.</li></ul> |
| Relatórios | Um máximo de 1.000 linhas pode ser visualizado ou baixado em qualquer relatório. Dados adicionais serão truncados. |