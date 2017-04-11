<properties
   pageTitle="Active Directory do Azure Reporting latências | Microsoft Azure"
   description="Quantidade de tempo que leva para relatar eventos apareça no seu Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-latencies"></a>Latências de relatório do Active Directory do Azure

*Esta documentação é parte do [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Relatório                                                  | Mínimo  | Média    | Máximo
------------------------------------------------------- | -------- | ---------- | ----------
**Relatórios de segurança**                                    |          |            |
Atividade de entrada irregular                              | 2 horas  | 4 horas    | 8 horas
Suplementos de sinal de fontes desconhecidas                           | 2 horas  | 4 horas    | 8 horas
Suplementos de entrada após várias falhas                        | 2 horas  | 4 horas    | 8 horas
Suplementos de entrada de várias geografia                      | 2 horas  | 4 horas    | 8 horas
Suplementos de entrada de endereços IP com atividades suspeitas     | 2 horas  | 4 horas    | 8 horas
Suplementos de entrada de dispositivos possivelmente infectados                 | 2 horas  | 4 horas    | 8 horas
Usuários com atividade anormal entrar                   | 2 horas  | 4 horas    | 8 horas
Usuários com credenciais perdidas                           | 2 horas  | 4 horas    | 8 horas
**Relatórios de aplicativo**                                 |          |            |
Atividade de provisionamento de conta                           | 2 horas  | 4 horas    | 8 horas
Erros de provisionamento de conta                             | 2 horas  | 4 horas    | 8 horas
Uso do aplicativo                                       | 2 horas  | 4 horas    | 8 horas
Status de sobreposição de senha                                | 2 horas  | 4 horas    | 8 horas
**Auditoria e relatórios de atividade**                            |          |            |
Relatório de auditoria                                            | 1 minuto | 15 minutos | 30 minutos
Atividade (Azure AD) de redefinição de senha                      | 2 horas  | 4 horas    | 8 horas
Atividade (Gerenciador de identidades) de redefinição de senha              | 2 horas  | 4 horas    | 8 horas
Atividade de registro (Azure AD) de redefinição de senha         | 2 horas  | 4 horas    | 8 horas
Atividade de registro (Gerenciador de identidades) de redefinição de senha | 2 horas  | 4 horas    | 8 horas
Autoatendimento atividade de grupos (Azure AD)                 | 2 horas  | 4 horas    | 8 horas
Autoatendimento atividade de grupos (Gerenciador de identidades)         | 2 horas  | 4 horas    | 8 horas
**Relatórios de RMS**                                         |          |            |
Usuários do RMS mais ativos                                   | 2 horas  | 4 horas    | 8 horas
Uso do RMS                                               | 2 horas  | 4 horas    | 8 horas
Uso do dispositivo de RMS                                        | 2 horas  | 4 horas    | 8 horas
Uso do aplicativo de RMS habilitado                           | 2 horas  | 4 horas    | 8 horas
**Particular visualizar relatórios**                             |          |            |
Todas as atividades de acesso do usuário                               | 2 horas  | 4 horas    | 8 horas
