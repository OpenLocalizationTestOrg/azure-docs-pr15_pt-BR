<properties
   pageTitle="Instalar o DC/SO CLI | Microsoft Azure"
   description="Instale o DC/SO CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contêineres, Microserviços, DC/sistema operacional, do Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Isso é para trabalhar com clusters ACS baseado no DC/sistema operacional. Não é necessário fazer isso para clusters baseados em por nuvem ACS.

Primeiro, [se conectar ao seu cluster ACS baseado em DC/sistema operacional](../articles/container-service/container-service-connect.md). Após você ter feito isso, você pode instalar a CLI DC/sistema operacional do computador cliente com os comandos abaixo:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Se você estiver usando uma versão antiga do Python, você pode perceber alguns "InsecurePlatformWarnings". Ignore essas.

Para começar a sem reiniciar o shell, execute:

```bash
source ~/.bashrc
```

Esta etapa não será necessária ao iniciar conchas novo.

Agora você pode confirmar que a CLI estiver instalada:

```bash
dcos --help
```