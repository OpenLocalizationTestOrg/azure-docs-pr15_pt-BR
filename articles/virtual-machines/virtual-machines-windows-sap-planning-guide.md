<properties
   pageTitle="SAP NetWeaver em máquinas virtuais do Windows (VMs) – guia de planejamento e implementação | Microsoft Azure"
   description="SAP NetWeaver em máquinas virtuais do Windows (VMs) – guia de planejamento e implementação"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-windows-virtual-machines-vms--planning-and-implementation-guide"></a>SAP NetWeaver em máquinas virtuais do Windows (VMs) – guia de planejamento e implementação

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver no Windows VMs (máquinas virtuais) – guia de implantação do DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (cache para VMs e VHDs) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (armazenamento do Microsoft Azure) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (estrutura de uma implantação de RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (alta disponibilidade e recuperação de dados com VMs do Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versões anteriores) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (usando uma imagens do SQL Server fora do Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (geral do SQL Server para SAP em Azure resumo) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (especificações do SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (configuração de armazenamento) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e restauração) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (considerações de desempenho de Backup e restauração) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (outros) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver no Windows VMs (máquinas virtuais) – guia de implantação) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP recursos) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantando uma máquina virtual com uma imagem personalizada) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (cenário 1: Implantando uma máquina virtual fora do Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (cenário 2: Implantando uma máquina virtual com uma imagem personalizada para SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md# a9a60133-a763-4de8-8986-ac0fa33aa8c1 (cenário 3: mover uma máquina virtual do local usando um VHD de Azure não generalizado com SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (implantação de cenários de VMs para SAP em Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (cmdlets do PowerShell do Azure Implantando) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download e SAP de importação relevantes cmdlets do PowerShell) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (ingressar máquina virtual para o domínio local - somente para Windows) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md# 6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (baixar, instalar e ativar agente de máquina virtual do Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar Azure Enhanced monitoramento extensão para SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (preparação para verificar se há Azure Enhanced monitoramento para SAP) [implantação-guia-5.2]: Virtual-Machines-Windows-SAP-Deployment-Guide.MD#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (verificação de integridade de configuração de infraestrutura de monitoramento Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (mais solução de problemas de infraestrutura de monitoramento do Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver em máquinas virtuais do Windows (VMs) – guia de planejamento e implementação) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (recursos) [planning-guide-11.4]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilidade (HA) e a recuperação de desastres (DR) para SAP NetWeaver em execução em máquinas virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (alta disponibilidade para servidores de aplicativos do SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (usando iniciar automaticamente para instâncias SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md# 1625df66-4cc6-4d60-9202-de8a0b77f803 (somente na nuvem - implantações de máquina Virtual para o Azure sem dependências na rede local do cliente) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (entre local - implantação de único ou várias VMs SAP em Azure com a exigência de sendo totalmente integrado à rede local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regiões) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (falhas domínios) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (atualizar domínios) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088- F9be - 4c 97-958a - 27996255c 665 (conjuntos de disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (o Microsoft Azure Máquina Virtual conceito) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (movendo uma máquina virtual do local para o Azure com um disco não generalizado) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantando uma máquina virtual com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparação para mover uma máquina virtual do local para o Azure com um disco não generalizado) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (preparação para implantar uma máquina virtual com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparando VMs com SAP para Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (diferença entre um disco de Azure e imagem Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (carregando um VHD do local para o Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (copiando discos entre contas de armazenamento do Azure) [planejamento-guia-5.5.1]: Virtual-Machines-Windows-SAP-Planning-Guide.MD#4efec401-91e0-40c0-8e64-f2dceadff646 (estrutura de máquina virtual/VHD para implantações de SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (montagem do configuração automática para discos anexados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (única máquina virtual com o SAP NetWeaver demonstração/treinamento cenário) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (implantação de conceitos de Cloud-Only de instâncias do SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitoramento solução para SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md# ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (armazenamento do Azure Premium)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

Microsoft Azure permite que as empresas adquiridas recursos de computação e armazenamento em tempo mínimo sem ciclos de compras longa. Azure máquinas virtuais permitem que as empresas implantar clássicos aplicativos, como SAP NetWeaver com base em aplicativos em Azure e estender a confiabilidade e a disponibilidade sem ter ainda mais recursos disponíveis no local. Serviços de máquina Virtual Azure também oferece suporte a conectividade entre locais, o que permite que as empresas ativamente integrar máquinas virtuais do Azure seus domínios de locais, suas nuvens particular e seu cenário de sistema SAP.
Este white paper descreve os conceitos básicos da máquina Virtual da Microsoft Azure e fornece uma orientação sobre considerações de planejamento e implementação para instalações do SAP NetWeaver no Azure e como tal deve ser o documento para ler antes de iniciar implantações reais do SAP NetWeaver no Azure.
Papel complementa a documentação de instalação do SAP e anotações de SAP que representam os recursos principais para implantações do software SAP e instalações em determinada plataformas.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="summary"></a>Resumo
Computação em nuvem é um termo amplamente usado que está obtendo mais importância no setor de TI, de pequenas empresas até multinacionais e grandes corporações.

Microsoft Azure é a plataforma de serviços de nuvem da Microsoft que oferece uma ampla variedade de novas possibilidades. Agora os clientes são capazes de aplicativos rapidamente provisionar e provisionar eliminação como um serviço na nuvem, para que eles não são limitados às restrições técnicas ou orçamentos. Em vez de investir tempo e orçamento em infraestrutura de hardware, empresas podem focalizar o aplicativo, processos de negócios e seus benefícios para os clientes e usuários.

Com os serviços de máquina Virtual do Microsoft Azure, a Microsoft oferece uma infraestrutura abrangente como uma plataforma de serviço (IaaS). Aplicativos do SAP NetWeaver com base são suportados em máquinas virtuais do Azure (IaaS). Este white paper descreverá como planejar e implementar aplicativos SAP NetWeaver com base em Microsoft Azure como a plataforma de escolha.

O papel em si se concentrará na dois aspectos principais:

* A primeira parte descreverá dois padrões de implantação com suporte para aplicativos SAP NetWeaver com base no Azure. Ele também descreverá manipulação geral do Azure com implantações de SAP em mente.
* A segunda parte detalhes Implementando os dois cenários diferentes descritos na primeira parte.

Para obter recursos adicionais consulte Capítulo [recursos] [planejamento guia-1.2] neste documento.

### <a name="definitions-upfront"></a>Definições antecipadamente
Em todo o documento usaremos os seguintes termos:

* IaaS: Infraestrutura como um serviço.
* PaaS: Plataforma como um serviço.
* SaaS: Software como um serviço.
* BRAÇO: Gerenciador de recursos Azure
* Componente do SAP: um SAP aplicativo individual como ECC, BW, gerente de solução ou EP.  Componentes do SAP podem ser baseadas em tecnologias tradicionais de ABAP ou Java ou um aplicativo com base não-NetWeaver como objetos comerciais.
* Ambiente de SAP: um ou mais componentes SAP agrupados logicamente para realizar uma função de negócios como desenvolvimento, QAS, treinamento, DR ou produção.
* Cenário SAP: Refere-se aos ativos SAP inteiros em um cliente panorama de TI. Cenário SAP inclui todos os ambientes de produção e não-produção.
* Sistema SAP: A combinação de camada DBMS e camada de aplicativo de por exemplo, um sistema de desenvolvimento de ERP SAP, sistema de teste do SAP BW, o sistema de produção SAP CRM, etc... Em implantações Azure-não há suporte para dividir essas duas camadas entre locais e do Azure. Isso significa que um sistema SAP seja implantado local ou ele é implantado no Azure. No entanto, você pode implantar os sistemas de um cenário SAP no Azure ou em locais diferentes. Por exemplo, você pode implantar o desenvolvimento de CRM da SAP e testar sistemas no Azure, mas o SAP CRM produção sistema local.
* Implantação somente na nuvem: uma implantação onde a assinatura do Azure não estiver conectada através de um site-site ou conexão de rota expressa na infraestrutura de rede local. Em comum documentação Azure esses tipos de implantações também são descritos como 'Somente na nuvem' implantações. Máquinas virtuais implantadas com esse método são acessadas através da internet e um endereço ip público e/ou um nome DNS público atribuído a VMs no Azure. Para o local do Active Directory (AD) do Microsoft Windows e DNS não está estendido para Azure nesses tipos de implantações. Portanto, as VMs não fazem parte do Active Directory local. Mesmo se verdadeiro para implementações Linux usando por exemplo OpenLDAP + Kerberos.

> [AZURE.NOTE] Somente na nuvem implantações neste documento é definido como concluídos cenários SAP estão executando exclusivamente no Azure sem extensão do Active Directory / OpenLDAP ou resolução de nome do local em nuvem pública. Configurações somente na nuvem não são suportadas para sistemas SAP de produção ou configurações onde STMS SAP ou outros recursos de local precisam ser usada entre sistemas SAP hospedados no Azure e recursos que residem no local.

* Entre locais: Descreve um cenário onde VMs são implantadas em uma assinatura do Azure que possui-to-site, vários locais ou conectividade de rota expressa entre o local datacenter(s) e Azure. Documentação em comum Azure, esses tipos de implantações também são descritos como cenários de cruz local. O motivo para a conexão é estender domínios local, local do Active Directory / OpenLDAP e local DNS para o Azure. O cenário de local é estendido para os ativos Azure da assinatura. Tendo essa extensão, VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e serviços podem ser executados nessas VMs (como DBMS serviços). Resolução de nomes e comunicação entre VMs implantadas locais e Azure VMs implantadas é possível. Esse é o cenário em que esperamos que a maioria dos recursos do SAP para ser implantado.  Consulte [Este] [ vpn-gateway-cross-premises-options] artigo e [Este] [ vpn-gateway-site-to-site-create] para obter mais informações.

> [AZURE.NOTE] Implantações entre locais do sistemas SAP onde máquinas virtuais do Azure executando sistemas SAP são membros de um domínio de locais são suportadas para sistemas SAP de produção. Configurações de cruz locais são suportadas para implantar partes ou concluir cenários SAP em Azure. Mesmo estiver executando o cenário SAP concluído no Azure requer tendo aqueles VMs sendo parte do domínio local e anúncios / OpenLDAP. Em versões anteriores da documentação falamos sobre cenários de TI híbrida, onde o termo 'Híbrido' raiz no fato de que não há uma conectividade entre locais entre locais e do Azure. Além disso, o fato de que as VMs no Azure fazem parte do Active Directory local / OpenLDAP.

Alguns documentação Microsoft descreve os cenários de locais entre um pouco diferente, especialmente para DBMS HA configurações. No caso do SAP documentos relacionados, o cenário de cruz local apenas resume a ter uma conectividade de (rota expressa)-to-site ou privada e o fato de que o cenário SAP é distribuído entre locais e do Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
Os seguintes guias adicionais estão disponíveis para o tópico de implantações de SAP no Azure:

* [SAP NetWeaver em máquinas virtuais do Windows (VMs) – guia de planejamento e implementação (Este documento)] [-guia de planejamento]
* [SAP NetWeaver no Windows VMs (máquinas virtuais) – guia de implantação] [-guia de implantação]
* [SAP NetWeaver no Windows VMs (máquinas virtuais) – guia de implantação do DBMS] [guia dbms]
* [SAP NetWeaver no Windows VMs (máquinas virtuais) – guia de implantação de alta disponibilidade][ha-guide]

> [AZURE.IMPORTANT] Sempre que possível um link para o guia de instalação do SAP referência é usado (referência InstGuide-01, consulte <http://service.sap.com/instguides>). Quando se trata dos pré-requisitos e o processo de instalação, os guias de instalação do SAP NetWeaver sempre deve ser lido cuidadosamente, como este documento cobre apenas tarefas específicas para sistemas SAP NetWeaver instalados em uma máquina Virtual Microsoft Azure.

As seguintes observações SAP estão relacionadas ao tópico de SAP no Azure:

| Número de nota | Título |
|--------------|-------|
| [1928533] | Aplicativos do SAP no Azure: suporte produtos e dimensionamento |
| [2015553] | SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] | Solução de problemas de monitoramento Azure avançados para SAP |
| [2178632] | Principais medidas de monitoração do SAP no Microsoft Azure |
| [1409604] | Virtualização no Windows: monitoramento avançado |
| [2191498] | SAP no Linux com o Azure: monitoramento avançado
| [2243692] | Linux no Microsoft Azure (IaaS) máquina virtual: problemas de licença do SAP
| [1984787] | SUSE LINUX Enterprise Server 12: Notas de instalação
| [2002167] | Red Hat Enterprise Linux 7. x: instalação e atualização

Leia também o [Wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as anotações do SAP para Linux.

Limitações de padrão geral e limitações máximos do Azure assinaturas podem ser encontradas [neste] artigo[azure-subscription-service-limits-subscription] 

## <a name="possible-scenarios"></a>Cenários possíveis
SAP geralmente é visto como um dos aplicativos mais essenciais nas empresas. A arquitetura e as operações desses aplicativos principalmente é muito complexo e garantir que você atende aos requisitos na disponibilidade e desempenho é importante.

Portanto, as empresas têm pensar cuidadosamente sobre quais aplicativos podem ser executados em um ambiente de nuvem pública, independentemente do provedor de nuvem escolhido.

Tipos de sistema possíveis para implantar SAP NetWeaver base aplicativos em nuvem pública ambientes estão listadas abaixo:

1. Sistemas de produção de tamanho médio
1. Sistemas de desenvolvimento
1. Sistemas de testes
1. Sistemas de protótipo
1. Aprendizagem / sistemas de demonstração

Para implantar sistemas SAP em Azure IaaS ou IaaS em geral, é importante entender as diferenças entre as ofertas de terceirizados tradicionais ou hosters e ofertas de IaaS significativas. Enquanto o hoster tradicional ou terceiros serão adaptar infraestrutura (tipo de rede, servidor e armazenamento) para um cliente quiser hospedar a carga de trabalho, em vez disso, é responsabilidade do cliente para escolher a carga de trabalho certa para implantações de IaaS.

A primeira etapa, os clientes precisam Verifique os seguintes itens:

* Tipos de máquina virtual do Azure compatíveis com o SAP
* O SAP produtos/versões com suporte no Azure
* O sistema operacional e DBMS compatíveis com versões para as versões específicas do SAP no Azure
* Produtividade SAPS fornecida por diferentes SKUs do Azure

As respostas para essas perguntas podem ser lidos em SAP anotação [1928533]. 

Como uma segunda etapa, limitações de recursos e largura de banda Azure precisam ser comparado a consumo de recurso real dos sistemas de local. Portanto, os clientes precisam estar familiarizados com os recursos diferentes dos tipos de Azure compatíveis com o SAP na área de:

* Recursos de CPU e memória de diferentes tipos de máquina virtual e
* Largura de banda IOPS de diferentes tipos de máquina virtual e 
* Recursos de rede de diferentes tipos de máquina virtual.

A maioria dos dados pode ser encontrada [aqui][virtual-machines-sizes]

Tenha em mente que os limites listados no link acima são limites superiores. Isso não significa que os limites para qualquer um dos recursos, por exemplo, IOPS podem ser fornecidos em todas as circunstâncias. As exceções apesar são os recursos de CPU e memória de um tipo de máquina virtual escolhido. Para os tipos de máquina virtual com suporte pelo SAP, os recursos de CPU e memória são reservadas e como tal disponível em qualquer ponto no tempo para consumo dentro a máquina virtual.

A plataforma do Microsoft Azure como outras plataformas IaaS é uma plataforma de vários locatário. Isso significa que o armazenamento, rede e outros recursos são compartilhados entre locatários. Lógica de otimização e cota inteligente é usada para impedir que um locatário afetar o desempenho do outro locatário (ruído vizinho) de maneira drástica. Embora lógica no Azure tenta manter variações na largura de banda experiente plataformas altamente compartilhado pequenas tendem a apresentar variações maiores na disponibilidade do recurso/largura de banda que muitos clientes são usadas para nas suas implantações locais. Como resultado, você pode enfrentar níveis diferentes de largura de banda em relação ao armazenamento ou rede e/s (o volume bem como latência) de um minuto a minuto. A probabilidade de que um sistema SAP no Azure poderia enfrentar variações maiores que em um sistema local precisa ser levadas em conta.

Última etapa é avaliar requisitos de disponibilidade. Ele pode acontecer, que a infraestrutura de Azure subjacente precisa atualizados e requer os hosts executando VMs a ser reiniciado. Nesses casos, VMs executando nesses hosts seriam desligadas e reiniciadas também. O intervalo de manutenção, é feito durante o horário comercial não central para uma determinada região, mas a janela possível de algumas horas durante o qual uma reinicialização ocorrerá é relativamente ampla. Há várias tecnologias dentro da plataforma Azure que podem ser configurados para atenuar alguns ou todos o impacto das tais atualizações. Futuros aprimoramentos da plataforma Windows Azure, o aplicativo DBMS e SAP foram projetados para minimizar o impacto de tal reiniciar. 

Para implantar um sistema SAP no Azure, o local SAP sistemas sistema operacional, banco de dados e aplicativos SAP devem aparecer na matriz de suporte da SAP Azure, ajustá-la dentro os recursos pode fornecer infraestrutura do Azure e que pode trabalhar com as ofertas de disponibilidade SLAs Microsoft Azure. Como esses sistemas são identificados, você precisa decidir sobre um dos seguintes cenários de implantação de duas.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Somente na nuvem - implantações de máquina Virtual para o Azure sem dependências na rede local do cliente
 
![Máquina virtual único com demonstração SAP ou o cenário de treinamento implantado no Azure][planning-guide-figure-100]

Este cenário é normal treinamentos ou sistemas de demonstração, onde todos os componentes do SAP e não-SAP software estão instalados dentro de uma única VM. Este cenário de implantação não são compatíveis com os sistemas de produção SAP. Em geral, esse cenário atende aos seguintes requisitos:

* As VMs próprios são acessíveis pela rede pública. Conectividade de rede direta para os aplicativos executados nas VMs à rede local da empresa possui o conteúdo demonstrações ou treinamentos ou o cliente não é necessária. 
* No caso de várias VMs que representa a treinamentos ou o cenário de demonstração, a resolução de nome e comunicações de rede precisa trabalhar entre VMs. Mas as comunicações entre o conjunto de VMs precisam ser isolados para que vários conjuntos de VMs podem ser implantados lado a lado sem interferência.  
* Conectividade com a Internet é necessária para o usuário final para fazer logon remoto em VMs hospedado no Azure. Dependendo do convidado SO, serviços de Terminal/RDS ou VNC/ssh é usada para acessar a máquina virtual para realizar as tarefas de treinamento ou executar as demonstrações. Se SAP portas como 3200, 3300 e 3600 pode também ser expostos a instância do aplicativo SAP pode ser acessada em qualquer desktop conectada da Internet.
* Os sistemas SAP (e VM(s)) representam um cenário de autônoma em Azure que somente requer conectividade com a internet pública para acesso de usuário final e não exige uma conexão para outras VMs no Azure.
* SAPGUI e um navegador são instalados e executados diretamente na máquina virtual. 
* É necessário uma rápida redefinição de uma máquina virtual ao estado original e a nova implantação do estado original novamente. 
* No caso de demonstração e cenários de treinamento que são realizados em várias VMs, um Active Directory / serviço OpenLDAP e/ou o DNS é necessário para cada conjunto de VMs.


![Grupo de máquina virtual que representa uma demonstração ou o cenário de treinamento em um serviço de nuvem do Azure][planning-guide-figure-200]

É importante ter em mente que o VM(s) em cada um dos conjuntos precisa ser implantado em paralelo, onde os nomes de máquina virtual em cada do conjunto são a mesma.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Entre local - implantação de único ou várias VMs SAP em Azure com a exigência de sendo totalmente integrado à rede local
 
![VPN com conectividade To-Site (entre local)][planning-guide-figure-300]

Este cenário é um cenário de cruz local com muitos padrões de possíveis de implantação. Ele pode ser descrito simplesmente como executando algumas partes da SAP paisagem locais e outras partes do SAP paisagem no Azure. Todos os aspectos do fato de que parte dos componentes SAP estejam em execução no Azure devem ser transparentes para usuários finais. Portanto, o sistema de correção de transporte (STMS) SAP RFC comunicação, impressão, segurança (como SSO), etc. funcionará perfeitamente para os sistemas SAP em execução no Azure. Mas o cenário de cruz local também descreve um cenário onde o cenário SAP concluído é executado no Azure com o domínio do cliente e DNS estendido em Azure. 

> [AZURE.NOTE] Este é o cenário de implantação que tem suporte para execução produtivos sistemas SAP.

Leia [Este artigo] [ vpn-gateway-create-site-to-site-rm-powershell] para obter mais informações sobre como conectar sua rede local ao Microsoft Azure

> [AZURE.IMPORTANT] Quando estamos falando cenários entre locais entre implantações de cliente do Azure e local, vemos o detalhamento de inteiros sistemas SAP. Cenários que são _não têm suporte_ para locais entre cenários são:
> 
> * Executando diferentes camadas de aplicativos SAP em diferentes métodos de implantação. Por exemplo executando o DBMS camada local, mas a camada de aplicativo SAP em VMs implantadas como VMs Azure ou vice-versa.
> * Alguns componentes de uma camada de SAP no Azure e alguns locais. Por exemplo dividindo instâncias da camada do aplicativo SAP entre locais e VMs do Azure. 
> * Não há suporte para a distribuição de VMs executando instâncias SAP de um sistema por várias regiões do Azure.
> 
> O motivo para essas restrições é o requisito para uma rede de alto desempenho de latência muito baixa dentro de um sistema SAP, especialmente entre as instâncias do aplicativo e a camada DBMS de um sistema SAP.



### <a name="supported-os-and-database-releases"></a>Suporte para o sistema operacional e versões de banco de dados

* Software de servidor da Microsoft com suporte para serviços de máquina Virtual do Azure está listado neste artigo: <http://support.microsoft.com/kb/2721672>. 
* Versões de sistema operacional compatível, versões do sistema de banco de dados com suporte nos serviços de máquina Virtual do Azure em conjunto com software SAP estão documentadas na anotação SAP [1928533]. 
* Versões com suporte nos serviços de máquina Virtual do Azure e os aplicativos da SAP estão documentadas no SAP anotação [1928533].
* Imagens de 64 bits somente são suportadas para executar como convidadas no Azure para cenários do SAP. Isso também significa que apenas aplicativos da SAP de 64 bits e bancos de dados têm suporte.

## <a name="microsoft-azure-virtual-machine-services"></a>Serviços de máquina Virtual do Windows Azure
A plataforma do Microsoft Azure é uma plataforma de serviços de nuvem de escala de internet hospedado e operado em centros de dados da Microsoft. A plataforma inclui os serviços de máquina Virtual do Microsoft Azure (infraestrutura como um serviço ou IaaS) e um conjunto de plataforma avançada como um recursos de serviço (PaaS).

Plataforma Windows Azure reduz a necessidade de tecnologia prévio e compras de infraestrutura. Ele simplifica a manutenção e operacional aplicativos fornecendo computação sob demanda e armazenamento para hospedar, dimensionar e gerenciar o aplicativo da web e aplicativos conectados. Gerenciamento de infraestrutura é automatizada com uma plataforma que foi projetada para alta disponibilidade e dinâmico de dimensionamento para atender às necessidades de uso com a opção de um modelo de preços flexível.


 
![Posicionamento dos serviços de máquina Virtual do Windows Azure][planning-guide-figure-400]

Com os serviços de máquina Virtual do Azure, Microsoft é permitindo que você implantar imagens de servidor personalizado para o Azure como instâncias de IaaS (consulte a Figura 4). As máquinas virtuais no Azure são baseados em unidades de disco rígido virtuais (VHD) de Hyper-V e são capazes de executar diferentes sistemas operacionais como SO convidado.

De uma perspectiva operacional, o serviço de máquina Virtual do Azure oferece experiências similares como máquinas virtuais implantadas no local. No entanto, ela tem a vantagem significativa que você não precisa adquirir, administrar e gerenciar a infraestrutura. Desenvolvedores e administradores têm controle total de imagem do sistema operacional dentro essas máquinas virtuais. Os administradores podem fazer logon remotamente nessas máquinas virtuais para realizar manutenção e solução de problemas de tarefas, bem como as tarefas de implantação de software. Sobre implantação, as restrições somente são os recursos de VMs do Azure e tamanhos. Esse pode não ser tão boa granular na configuração como isso pode ser feito no local. Há uma variedade de tipos de máquina virtual que representam uma combinação de:

* Número de vCPUs,
* Memória,
* Número de VHDs que pode ser anexado
* Larguras de banda de rede e de armazenamento.

O tamanho e limitações dos diversos máquinas virtuais diferentes tamanhos oferecidas podem ser vistos em uma tabela [neste] artigo[virtual-machines-sizes]

Como você obterá há diferentes famílias ou série de máquinas virtuais. A partir de dezembro de 2015, você pode distinguir as seguintes famílias de VMs:

* Tipos de máquina virtual a0 A7: nem todos os certificados para SAP. Primeira série de máquina virtual do Azure IaaS adquiriu introduzidas com.
* Tipos de máquina virtual A11 a8: instâncias de computação de alto desempenho. Hosts que outras VMs de uma série de computação de execução sobre a realização de melhor diferentes.
* Tipos de máquina virtual série D: melhor desempenho que A0 A7. Nem todos os tipos de máquina virtual são certificados com SAP.
* Tipos de máquina virtual série DS: usar mesmos hosts como série D, mas são capazes de se conectar ao armazenamento do Azure Premium (consulte Capítulo [Azure Premium armazenamento] [planejamento-guia-3.3.2] deste documento). Novamente nem todos os tipos de máquina virtual são certificados com SAP.
* Tipos de máquina virtual G série: tipos de máquina virtual de memória alta. 
* Tipos de máquina virtual série GS: como G série mas incluindo a opção para usar o armazenamento do Azure Premium (consulte Capítulo [Azure Premium armazenamento] [planejamento-guia-3.3.2] deste documento). Ao usar VMs série GS como servidores de banco de dados é obrigatório para usar o armazenamento de Premium para arquivos de log de dados e transações de banco de dados


Você pode encontrar a mesma CPU e configurações de memória diferentes série de máquina virtual. No entanto, quando você procurar o desempenho de produtividade dessas VMs fora da série diferentes eles podem diferir significativamente. Apesar de ter a mesma configuração de CPU e memória. Motivo é que o hardware de servidor de host subjacente na introdução os diferentes tipos de máquina virtual tinha características de produtividade diferentes.  Normalmente a diferença mostrada no desempenho da produtividade também é refletida no preço das VMs diferentes.

Observe que diferentes não todas as séries de máquina virtual podem ser oferecidos em cada uma das regiões Azure (para regiões do Azure consulte próximo capítulo). Além disso, lembre-se de que nem todas as VMs ou série de máquina virtual é certificado para SAP.

> [AZURE.IMPORTANT] Para o uso de aplicativos do SAP NetWeaver com base, apenas o subconjunto de tipos de máquina virtual e configurações listadas na anotação SAP [1928533] são aceitos.

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões Azure
Microsoft permite implantar máquinas virtuais em então chamado 'Azure regiões'. Uma região do Azure pode ser um ou vários data centers que estão localizados no proximidade. Para a maioria das regiões geopolíticas no mundo Microsoft tem pelo menos duas regiões do Azure. Por exemplo na Europa, há uma região do Azure de 'Norte da Europa' e um dos 'Oeste Europa'. Essas duas regiões de Azure dentro de uma região geopolíticas são separadas por distância significativa o suficiente para que desastres técnicos ou naturais não afetam ambas as regiões do Azure na mesma região geopolíticas. Desde que a Microsoft está constantemente desenvolvendo novas áreas do Azure em diferentes regiões geopolíticas globalmente, o número dessas regiões é crescimento gradual e a partir de dezembro de 2015 atinge o número de 20 regiões do Azure com regiões adicionais anunciados já. Você como um cliente pode implantar sistemas SAP em todos esses regiões, incluindo as duas regiões de Azure na China. Para atual até data informações sobre regiões Azure consulte este site: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>O conceito de máquina Virtual do Windows Azure
Microsoft Azure oferece uma infraestrutura como uma solução de serviço (IaaS) para o host máquinas virtuais com funcionalidades semelhantes como uma solução de virtualização do local. Você é capaz de criar máquinas virtuais no Portal do Azure, PowerShell ou CLI, que também oferecem recursos de gerenciamento e implantação.

Gerenciador de recursos de Azure permite provisionar seus aplicativos usando um modelo declarativo. Em um único modelo, você pode implantar vários serviços juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Mais informações sobre como usar modelos de BRAÇO podem ser encontradas aqui:

* [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de recursos do Azure e a CLI do Azure][virtual-machines-linux-cli-deploy-templates]
* [Gerenciar máquinas virtuais usando o Gerenciador de recursos do Azure e PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://Azure.microsoft.com/documentation/Templates/>

Outro recurso interessante é a capacidade de criar imagens de máquinas virtuais, que permite que você preparar determinados repositórios do qual é possível implantar rapidamente instâncias de máquina Virtual que atender às suas necessidades.

Mais informações sobre a criação de imagens de máquinas virtuais podem ser encontradas [neste] artigo (Windows)[ virtual-machines-windows-capture-image] ou [neste]artigo (Linux)[virtual-machines-linux-capture-image].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios de falha
Domínios de falha representam uma unidade física de falha, muito estreitamente relacionada na infraestrutura física contida em data centers e enquanto um blade física ou rack pode ser considerado um domínio de falha, não há nenhum mapeamento direto entre os dois. 

Quando você implanta várias máquinas virtuais como parte de um sistema SAP nos serviços de máquina Virtual do Microsoft Azure, você pode influenciar o controlador de tecidos do Azure para implantar seu aplicativo em diferentes domínios de falhas, reunião, portanto, os requisitos do Microsoft Azure SLA. No entanto, a distribuição dos domínios de falha ao longo de uma unidade de escala do Azure (coleção de centenas de nós de computação ou nós de armazenamento e redes) ou a atribuição de VMs a um domínio específico de falhas é algo que você não tem controle direto. Para direcionar o controlador de tecidos Azure implantar um conjunto de VMs em diferentes domínios de falha, é necessário atribuir um conjunto de disponibilidade do Azure para VMs no momento da implantação. Para obter mais informações sobre conjuntos de disponibilidade do Azure, consulte Capítulo [Azure disponibilidade conjuntos] [planejamento-guia-3.2.3] neste documento.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Atualizar domínios
Atualização de domínios representam uma unidade lógica que ajudam a determinar como uma máquina virtual dentro de um sistema SAP, que consiste em instâncias do SAP executado em várias VMs, será atualizada. Quando ocorre uma atualização, o Microsoft Azure percorre o processo de atualização esses domínios atualização um por vez. Distribuindo VMs no momento da implantação sobre domínios diferentes de atualização, você pode proteger seu sistema SAP parcialmente de tempo de inatividade potencial. Para forçar o Azure para implantar as VMs de um sistema SAP distribuídas sobre domínios diferentes de atualização, você precisa definir um atributo específico em tempo de implantação de cada máquina virtual. Semelhante aos domínios de falhas, uma unidade de escala Azure é dividida em vários domínios de atualização. Para direcionar o controlador de tecidos Azure para implantar um conjunto de VMs sobre domínios diferentes de atualização, você precisa atribuir um conjunto de disponibilidade do Azure para VMs no momento da implantação. Para obter mais informações sobre conjuntos de disponibilidade do Azure, consulte Capítulo [Azure disponibilidade conjuntos] [planejamento-guia-3.2.3] abaixo.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade do Azure
Azure máquinas virtuais dentro de um conjunto de disponibilidade do Azure será distribuído pelo controlador de tecidos Azure sobre falhas e atualizar domínios diferentes. O objetivo da distribuição sobre diferentes falhas e atualizar domínios é impedir que todas as VMs de um sistema SAP sendo desligado no caso de manutenção de infraestrutura ou uma falha dentro de um domínio de falha. Por padrão, VMs não são parte de um conjunto de disponibilidade. A participação de uma máquina virtual em um conjunto de disponibilidade é definida no momento da implantação ou posterior por uma reconfiguração e implantação re de uma máquina virtual.

Para compreender o conceito de conjuntos de disponibilidade do Azure e a maneira conjuntos de disponibilidade relacionar falhas e domínios de atualização, leia [Este artigo][virtual-machines-manage-availability]

Para definir a disponibilidade conjuntos para BRAÇO por meio de um modelo de json consulte [as especificações de api rest](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) e procure por "disponibilidade".

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Armazenamento do Microsoft Azure e discos de dados
Máquinas virtuais do Microsoft Azure utilizar tipos de armazenamento diferentes. Ao implementar o SAP em serviços de máquina Virtual do Azure é importante entender as diferenças entre esses dois tipos principais de armazenamento:

* Armazenamento não-persistente e voláteis.
* Armazenamento persistente.

O armazenamento não-persistente é conectado diretamente às máquinas virtuais em execução e reside nos nós de computação próprios – o armazenamento de instância local (armazenamento temporário). O tamanho depende do tamanho da máquina Virtual escolhido quando a implantação iniciado. Esse tipo de armazenamento é voláteis e, portanto, o disco está inicializado quando uma instância de máquina Virtual seja reiniciada. Geralmente, o arquivo de paginação para o sistema operacional está localizado no disco temporário.

___

> ![Windows][Logo_Windows] Windows
>
> No Windows VMs a unidade temp estiver montada como unidade D:\ em uma máquina virtual implantada.
>
> ![Linux][Logo_Linux] Linux
> 
> Em Linux VMs ele é montado como /mnt/resource ou /mnt. Veja mais detalhes aqui:
> 
> * [Como anexar um disco de dados a um computador Virtual Linux][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/Archive/2013/12/07/Understanding-the-Temporary-drive-on-Windows-Azure-Virtual-Machines.aspx>

___

A unidade real é voláteis porque ela está obtendo armazenada no servidor host em si. Se a máquina virtual movido em uma reimplantação (por exemplo, devido a manutenção no host ou desligamento e reinicialização), o conteúdo da unidade será perdido. Portanto, não é uma opção para armazenar quaisquer dados importantes nesta unidade. O tipo de mídia usada para esse tipo de armazenamento difere entre diferentes séries de máquina virtual com características de desempenho muito diferente que a partir de junho de 2015 aparência:

* A5-A7: Desempenho muito limitado. Não é recomendado para qualquer coisa além do arquivo de página
* A8-A11: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg.
* Série D: Características de desempenho muito bom com alguns e milhar IOPS e > produtividade de 1GB/seg.
* Série de DS: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg.
* Série de G: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg.
* Série de GS: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg.

Instruções acima estão aplicando aos tipos de máquina virtual que são certificados com SAP. A série de máquina virtual com excelente IOPS e produtividade se qualifica para aproveitar alguns recursos do DBMS. Consulte [DBMS guia de implantação] [dbms-guia] para obter mais detalhes.

Armazenamento do Microsoft Azure fornece armazenamento persistente e os níveis típicos de proteção e redundância vistos em armazenamento SAN. Disco com base no Azure armazenamento é disco rígido virtual (VHDs) localizado nos serviços de armazenamento do Azure. O sistema operacional-disco local (c: Windows\, Linux / (/ desenvolvimento/sda1)) está armazenada no armazenamento do Azure, e adicional Volumes/disco montado para a máquina virtual obter armazenado nesse local, também.

É possível carregar um VHD existente do local ou criar os vazios de dentro do Azure e anexar aqueles em VMs implantadas. Esses VHDs referenciadas como discos do Azure. 

Após criar ou carregar um VHD para o armazenamento do Azure, é possível para montar e anexar aqueles a uma máquina Virtual existente e copiar VHD (desmontado) existente.

Como esses VHDs são persistentes, dados e alterações dentro aqueles são confiáveis quando reiniciar e recriar uma instância de máquina Virtual. Mesmo que uma instância é excluída, esses VHDs fique seguros e podem ser redistribuídos ou no caso de não-OS discos podem ser montados em outras VMs.

Dentro da rede de armazenamento do Azure níveis de redundância diferentes podem ser configurados:

* Nível mínimo que pode ser selecionado é 'local redundância', que equivale a três-réplica dos dados dentro do mesmo data center de uma região do Azure (consulte Capítulo [Azure Regions][planning-guide-3.1]). 
* Armazenamento de zona redundante que afete as três imagens sobre os dados de diferentes centros dentro da mesma região do Azure.
* Nível de redundância de padrão é redundância geográfica que assíncrona replica o conteúdo em outro imagens 3 dos dados em outra região do Azure que está hospedado na mesma região geopolíticas.

Consulte também a tabela na parte superior deste artigo em relação às opções de redundância diferentes: <https://azure.microsoft.com/pricing/details/storage/> 

Mais informações em relação ao armazenamento do Azure podem ser encontradas aqui: 

* <https://Azure.microsoft.com/documentation/Services/Storage/>
* <https://Azure.microsoft.com/Services/Site-Recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/Archive/2015/11/17/Azure-Disk-Encryption-for-Linux-and-Windows-Virtual-Machines-Public-Preview.aspx>


#### <a name="azure-standard-storage"></a>Armazenamento padrão do Azure
Armazenamento de BLOB padrão do Azure era o tipo de armazenamento disponível quando o Azure IaaS foi lançado. Havia cotas IOPS impostas por VHD único. Latência experiente não era na mesma classe como dispositivos de SAN/NAS normalmente implantados para ponta sistemas SAP hospedado no local. No entanto, o armazenamento padrão do Azure demonstrou suficiente para centenas sistemas SAP, enquanto isso, implantados no Azure.

Azure armazenamento padrão é cobrado com base nos dados reais que estão armazenados, o volume de transações de armazenamento, transferências de dados de saída e opção redundância escolhida. Muitos VHDs podem ser criadas no máximo 1TB em tamanho, mas contanto que aqueles permaneçam vazias não é cobrado. Se você, em seguida, preencher um VHD com 100GB, você será cobrado para 100GB de armazenamento e não para o tamanho nominal com que VHD adquiriu criado.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Armazenamento do Azure Premium
Em abril de 2015 Microsoft introduziu o armazenamento do Azure Premium. Armazenamento de Premium adquiriu introduzido com o objetivo de fornecer:

* Latência de i/o melhor.
* Taxa de transferência melhor.
* Menos variabilidade de latência e/s.

Para essa finalidade, muitas alterações foram introduzidas das quais são os dois mais significativos:

* Uso de discos SSD em nós de armazenamento do Azure
* Um novo cache que é suportado pela SSD local de um nó de computação Azure de leitura

Em se oposta à armazenamento padrão onde recursos não alterou dependentes no tamanho do disco (ou VHD), Premium armazenamento atualmente tem 3 disco diferentes categorias que são mostradas no final deste artigo antes da seção Perguntas Frequentes: <https://azure.microsoft.com/pricing/details/storage/>

Você vê que IOPS/VHD e disco produtividade/VHD dependem na categoria de tamanho dos discos

Base de custo no caso de armazenamento de Premium não é o volume de dados reais armazenados em tais VHDs, mas a categoria de tamanho de tal um VHD, independente da quantidade de dados que estão armazenados dentro do VHD.

Você também pode criar VHDs armazenamento Premium que não está mapeando diretamente nas categorias tamanho mostradas. Isso pode ser o caso, especialmente quando copiando VHDs do armazenamento padrão para o armazenamento de Premium. Nesses casos, um mapeamento para a próxima opção de disco Premium armazenamento maior é executado. 

Esteja ciente de que somente determinadas séries de máquina virtual podem se beneficiar o armazenamento do Azure Premium. A partir de dezembro de 2015, estas são as séries de DS e de GS. A série DS é basicamente o mesmo série D com exceção de que a série DS tem a capacidade de armazenamento de montagem Premium baseado VMs além da VHDs hospedadas no armazenamento padrão do Azure. Mesmo é válido para série G comparada a série GS.

Se você estiver verificando a parte das VMs série DS [neste] artigo[ virtual-machines-sizes] você também obterá que há dados volume limitações para Premium armazenamento VHDs no detalhamento do nível de máquina virtual. Série de DS diferente ou série GS VMs também tem diferentes limitações em relação ao número de VHDs que pode ser montado. Esses limites estão documentados no artigo mencionado acima também. Mas em essência, isso significa que se montar 32 x P30 discos/VHDs para uma única VM DS14 por exemplo, você não pode obter 32 x a taxa de transferência máxima de um disco de P30. Em vez disso, a taxa de transferência máxima no nível de máquina virtual conforme documentadas no artigo limita transferência dos dados. 

Obter mais informações sobre o armazenamento de Premium podem ser encontradas aqui: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Contas de armazenamento do Azure
Ao implantar os serviços ou VMs no Azure, implantação de VHDs e imagens de máquina virtual deve ser organizada em unidades chamadas contas de armazenamento do Azure. Ao planejar uma implantação do Azure, você precisa considerar cuidadosamente as restrições do Azure. Um lado, há um número limitado de contas de armazenamento por assinatura Azure. Embora cada conta de armazenamento do Azure pode conter um grande número de arquivos VHD, há um limite fixo no IOPS total por conta de armazenamento. Ao implantar centenas de VMs SAP com sistemas DBMS criar significativas chamadas de IO, é recomendável para distribuir alta VMs de DBMS IOPS entre várias contas de armazenamento do Azure. Deve ter cuidado para não exceder o limite atual de contas de armazenamento do Azure por assinatura. Como o armazenamento é uma parte vital da implantação do banco de dados para um sistema SAP, esse conceito é discutido mais detalhadamente já referenciada [DBMS guia de implantação] [dbms-guia].

Mais informações sobre contas de armazenamento do Azure podem ser encontradas [neste]artigo[storage-scalability-targets]. Lendo este artigo, você descobrirá que existem diferenças nas limitações entre contas de armazenamento padrão do Azure e contas de armazenamento Premium. Principais diferenças são o volume de dados que podem estar armazenados em tal uma conta de armazenamento. No armazenamento padrão o volume é uma magnitude maior do que com o armazenamento de Premium. No outro lado a conta de armazenamento padrão é seriamente limitada IOPS (consulte coluna 'Taxa de solicitação Total'), enquanto a conta de armazenamento do Azure Premium não tem nenhum tal limitação. Abordaremos detalhes e os resultados dessas diferenças quando abordando as implantações de sistemas SAP, especialmente os servidores DBMS.

Dentro de uma conta de armazenamento, você tem a possibilidade de criar diferentes contêineres para organizar e categorizar VHDs diferentes. Esses contêineres geralmente são usadas para ex.: separadas VHDs de VMs diferentes. Não há nenhum implicações de desempenho usando apenas um ou mais contêineres vários sob uma única conta de armazenamento do Azure.

Dentro do Azure um nome VHD segue a conexão de nomenclatura seguinte que precisa fornecer um nome exclusivo para o VHD dentro do Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Conforme mencionado que a cadeia de caracteres acima precisa identificar exclusivamente o VHD que está armazenado no armazenamento do Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Rede do Microsoft Azure
Microsoft Azure fornecerá uma infraestrutura de rede que permite o mapeamento de todos os cenários que queremos perceber com software SAP. Os recursos são:

* Acesso externo, diretamente às VMs por meio de serviços de Terminal do Windows ou ssh/VNC
* Acesso aos serviços e portas específicas usadas por aplicativos dentro de VMs
* Resolução de nomes entre um grupo de VMs implantadas como VMs do Azure e comunicação interna
* Conectividade entre locais entre de um cliente local e rede do Azure
* Entre Azure região ou data center conectividade entre sites Azure 

Mais informações podem ser encontradas aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Há muitas possibilidades diferentes para configurar o nome e resolução IP no Azure. Neste documento, somente na nuvem cenários dependem do padrão usando o Azure DNS (em contraste com a definição de um serviço DNS próprio). Há também um novo serviço de DNS do Azure que pode ser usado em vez de configurar seu próprio servidor DNS. Mais informações podem ser encontradas [neste] artigo[ virtual-networks-manage-dns-in-vnet] e [nesta](https://azure.microsoft.com/services/dns/)página.

Para cenários de locais entre nós depender do fato de que o local OpenLDAP/AD/DNS foi estendido via VPN ou conexão particular no Azure. Para alguns cenários como documentadas aqui, talvez seja necessário ter uma réplica do AD/OpenLDAP instalada no Azure.

Porque a rede e resolução de nomes é essencial para a implantação do banco de dados para um sistema SAP, esse conceito é discutido mais detalhadamente [DBMS guia de implantação] [dbms-guia].


##### <a name="azure-virtual-networks"></a>Redes virtuais Azure

Criando uma rede Virtual do Azure, você pode definir o intervalo de endereço dos endereços IP particulares alocada por funcionalidade DHCP do Azure. Em cenários de cruz local, o intervalo de endereços IP definido será ainda alocado usando DHCP por Azure. Entretanto, a resolução de nome de domínio será feita no local (presumindo que as VMs são uma parte de um domínio local) e, portanto, pode resolver endereços além dos diferentes serviços de nuvem do Azure.

[comentário]: <>  (MSSedusch ainda é necessário? TODO originalmente uma rede Virtual Azure foi vinculado a um grupo de afinidade. Com que obtive restrita para a unidade de escala Azure que grupo afinidade adquiriu atribuídos a uma rede Virtual no Azure. No final, isso significa que a rede Virtual foi restrita aos recursos disponíveis na unidade de escala do Azure. Isso já foi alterado e agora redes virtuais Azure pode alongar para mais de uma unidade de escala do Azure. No entanto, que exige que são redes virtuais Azure * * não * * associadas afinidade grupos mais no momento da criação. Já mencionado anteriormente que no se oposta à recomendações ano atrás, você deve * * não aproveitar mais grupos de afinidade Azure * *. Para obter detalhes, consulte < https://azure.microsoft.com/blog/regional-virtual-networks/>)

Cada máquina Virtual no Azure precisa estar conectado a uma rede Virtual.

Mais detalhes podem ser encontrados [neste] artigo[ resource-groups-networking] e [nesta](https://azure.microsoft.com/documentation/services/virtual-network/)página.

[comentário]: <>  (MShermannd TODO não foi possível encontrar um artigo que inclui o tópico OpenLDAP + BRAÇO;)
[comentário]: <>  (MSSedusch < https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [AZURE.NOTE] Por padrão, quando uma máquina virtual é implantada, você não pode alterar a configuração de rede Virtual. As configurações de TCP/IP devem ser deixadas para o servidor DHCP do Azure. Comportamento padrão é a atribuição de IP dinâmico.

O endereço de MAC do cartão de rede virtual pode alterar por exemplo, após redimensionar e o convidado Windows ou Linux SO selecionará a nova placa de rede e automaticamente usará DHCP para atribuir os endereços IP e DNS nesse caso.

##### <a name="static-ip-assignment"></a>Atribuição de IP estático
É possível atribuir endereços IP fixos ou reservados a VMs dentro de uma rede Virtual do Azure. A execução de VMs em uma rede Virtual do Azure abre uma ótima possibilidade para aproveitar essa funcionalidade se necessário ou necessário para alguns cenários. A atribuição de IP permanece válida em toda a existência da máquina virtual, independentemente de se a máquina virtual está em execução ou desligamento. Como resultado, você precisa levar o número total de VMs (VMS em execução e paradas) em consideração ao definir o intervalo de endereços IP para a rede Virtual. O endereço IP permanece atribuído até que a máquina virtual e sua Interface de rede é excluído ou até que o endereço IP eliminação obtém atribuído novamente. Consulte informações detalhadas contidas [neste]artigo[virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Várias placas de rede por máquina virtual
Você pode definir várias placas de interface de rede virtual (vNIC) para uma máquina Virtual do Azure. A capacidade de ter vários vNICs, que você pode começar a configurar o tráfego de rede com separação onde por exemplo, o tráfego de cliente é roteado por meio de um tráfego vNIC e back-end é roteada por meio de uma segunda vNIC. Dependentes no tipo de máquina virtual lá são diferentes limitações em relação ao número de vNICs. Restrições, funcionalidade e detalhes exatas podem ser encontradas nestes artigos:
 
* [Criar uma máquina virtual com várias placas de rede][virtual-networks-multiple-nics]
* [Implantar vários NIC VMs usando um modelo][virtual-network-deploy-multinic-arm-template]
* [Implantar vários NIC VMs usando o PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implantar vários NIC VMs utilizando a CLI do Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade de-to-Site
Entre locais é VMs do Azure e vinculado com uma conexão de VPN transparente e permanente no local. Ele deverá se tornar o padrão de implantação de SAP mais comuns no Azure. Pressupõe-se que procedimentos operacionais e processos com instâncias do SAP no Azure devem funcionar de forma transparente. Este significa que você deve ser capaz de imprimir esses sistemas bem como usar o sistema de gerenciamento de transporte (TMS) da SAP para transporte muda de um sistema de desenvolvimento do Azure para um sistema de teste que é implantado no local. Mais documentação ao redor to-site pode ser encontrada [neste] artigo[vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN
Para criar uma conexão de-to-site (Centro de dados local para Azure data center), você precisará obter e configurar um dispositivo VPN ou use o roteamento e acesso remoto (RRAS) que foi introduzido como um componente de software com o Windows Server 2012. 

* [Criar uma rede virtual com uma conexão de VPN-to-site usando o PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Sobre dispositivos VPN para conexões do Gateway VPN-to-Site][vpn-gateway-about-vpn-devices]
* [Gateway VPN perguntas Frequentes][vpn-gateway-vpn-faq]

![Conexão de-to-site entre locais e do Azure][planning-guide-figure-600]

A figura acima mostra duas assinaturas Azure tem subintervalos de endereço IP reservado para uso em redes virtuais no Azure. A conectividade da rede local ao Azure é estabelecida via VPN.

#### <a name="point-to-site-vpn"></a>Ponto-a-Site VPN
Ponto-a-site VPN requer cada computador cliente para conectar-se com sua própria VPN para o Azure. Para os cenários SAP que vemos, ponto-a-site conectividade não é prática. Portanto, não há mais referências terá conectividade VPN ponto-a-site.

[comentário]: <>  (MSSedusch – mais informações podem ser encontradas aqui)
[comentário]: <>  (MShermannd TODO Link não é mais válida; Mas BRAÇO mesmo assim não tem suporte - consulte próximo link abaixo)
[comentário]: <>  (MSSedusch - < http://msdn.microsoft.com/library/azure/dn133798.aspx>.)
[comentário]: <>  (Ponto de TODO MShermannd para sites que não têm suportado ainda com BRAÇO)
[comentário]: <>  (MSSedusch - < https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### <a name="multi-site-vpn"></a>VPN de vários local
Azure também atualmente oferece a possibilidade de criar conectividade VPN de vários locais para uma assinatura do Azure. Anteriormente uma única assinatura foi limitada a uma conexão de VPN-to-site. Essa limitação sumiu com conexões VPN de vários locais para uma única assinatura. Isso possibilita aproveitar mais de uma região do Azure para uma assinatura específica por meio de configurações de cruz local.

Consulte [Este artigo]para obter mais documentação[vpn-gateway-create-site-to-site-rm-powershell]
[comentário]: <> (MShermannd TODO não encontrado nenhum link de documento ARM)

#### <a name="vnet-to-vnet-connection"></a>VNet para Conexão de VNet
Usando vários locais VPN, você precisa configurar uma rede Virtual separada do Azure em cada uma das regiões. No entanto muito frequentemente, você tem o requisito de que os componentes de software em diferentes regiões devem se comunicar com os outros. Ideal essa comunicação não deve ser roteada de uma região do Azure para o local e a partir daí à região do Azure. Atalho, o Azure oferece a possibilidade de configurar uma conexão de uma rede Virtual do Azure em uma região para outra rede Virtual do Azure hospedado em outro região. Esta funcionalidade é chamada de conexão de VNet para VNet. Obter mais detalhes sobre essa funcionalidade podem ser encontradas aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure--expressroute"></a>Conexão particular no Azure – rota expressa
Rota expressa do Microsoft Azure permite a criação de conexões privadas entre Azure data centers e infraestrutura de local do cliente ou em um ambiente de localização conjunta. Rota expressa oferecida por MPLS vários provedores VPN (pacote mudado) ou outros provedores de serviços de rede. Rota expressa conexões não vá na Internet pública. Rota expressa conexões oferecem maior segurança, mais confiabilidade através de vários circuitos paralelos, rapidez e latências mais conexões típicas pela Internet. 

Encontre mais detalhes sobre rota expressa do Azure e ofertas aqui:

* <https://Azure.microsoft.com/documentation/Services/expressroute/>
* <https://Azure.microsoft.com/Pricing/Details/expressroute/>
* <https://Azure.microsoft.com/documentation/articles/expressroute-FAQs/>

Rota expressa permite várias assinaturas Azure por meio de um circuito de rota expressa como documentadas aqui 

* <https://Azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-ARM/> 
* <https://Azure.microsoft.com/documentation/articles/expressroute-howto-Circuit-ARM/>


#### <a name="forced-tunneling-in-case-of-cross-premise"></a>Forçado túnel em caso de cruz local
Para VMs ingressando em domínios locais por meio-to-site, ponto-a-site ou rota expressa, você precisa certificar-se de que as configurações de proxy de Internet estão recebendo implantadas para todos os usuários nessas VMs também. Por padrão, o software em execução nesses VMs ou usuários usando um navegador para acessar a internet não faria através do proxy de empresa, mas seria conectar direto Azure com a internet. Mas, mesmo a configuração do proxy não é uma solução de 100% para direcionar o tráfego através do proxy de empresa, pois é responsabilidade de software e serviços para verificar o proxy. Se software executado na máquina virtual não está fazendo que ou um administrador manipula as configurações, o tráfego para a Internet pode ser detoured novamente diretamente por meio do Azure com a Internet. 

Para evitar isso, você pode configurar forçado túnel com-to-site conectividade entre locais e do Azure. A descrição detalhada do recurso forçado túnel é publicado aqui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/> 

Tunelamento forçado com rota expressa está habilitado por clientes anunciando uma rota padrão via as sessões de correspondência ExpressRoute BGP.

#### <a name="summary-of-azure-networking"></a>Resumo das redes Azure
Este capítulo continha muitos pontos importantes sobre rede do Azure. Aqui está um resumo dos pontos principais:


* Redes virtuais Azure permite configurar a rede de acordo com suas necessidades
* Redes virtuais Azure pode ser utilizadas para atribuir intervalos de endereços IP para VMs ou endereços IP fixos para VMs
* Para configurar uma conexão-To-Site ou ponto-a-Site necessárias para criar uma rede Virtual do Azure primeiro
* Depois que uma máquina virtual foi implantada não é mais possível alterar a rede Virtual atribuído para a máquina virtual

### <a name="quotas-in-azure-virtual-machine-services"></a>Cotas nos serviços do Azure Máquina Virtual
Precisamos seja claro sobre o fato de que a infraestrutura de rede e de armazenamento é compartilhada entre VMs executando uma variedade de serviços de infraestrutura do Azure. E, assim como os centros de dados do cliente, excesso de provisionamento de alguns dos recursos de infraestrutura ocorram em um grau. A plataforma do Microsoft Azure usa disco, CPU, rede e outras cotas para limitar o consumo de recursos e preservar o desempenho consistente e determinante.  Os diferentes tipos de máquina virtual (A5, A6, etc) têm diferentes cotas para o número de discos, CPU, RAM e rede.

> [AZURE.NOTE] Recursos de CPU e memória dos tipos de máquina virtual com suporte pelo SAP são previamente alocados em nós de host. Isso significa que, quando a máquina virtual é implantada, os recursos do host estará disponíveis, conforme definido pelo tipo de máquina virtual.

Ao planejar e dimensionamento SAP em soluções Azure as cotas para cada tamanho de máquina virtual devem ser consideradas.  As cotas de máquina virtual descritas [aqui][virtual-machines-sizes].

As cotas descritas representam os valores máximos teóricos.  O limite de IOPS por VHD pode ser obtido com IOs pequenas (8kb), mas possivelmente não pode ser obtido com IOs grandes (1Mb).  O limite IOPS é aplicado no detalhamento de VHDs único.

Como uma árvore de decisão aproximada para decidir se um sistema SAP se encaixa no serviços de máquina Virtual do Azure e seus recursos ou se um sistema existente precisa estar configurado de maneira diferente para implantar o sistema no Azure, a árvore de decisão abaixo pode ser usada:
 
![Árvore de decisão para decidir a capacidade de implantar SAP no Azure][planning-guide-figure-700]

**Etapa 1**: as informações mais importantes são começar com o requisito de SAPS para um determinado sistema SAP. Os requisitos de SAPS precisam ser separadas check-out em parte DBMS e a parte do aplicativo SAP, mesmo se o sistema SAP já está implantado local em uma configuração de 2 camadas. Sistemas existentes, os SAPS relacionados ao hardware em uso geralmente podem ser determinados ou estimados com base nos parâmetros de comparação SAP existentes. Os resultados podem ser encontrados aqui: <http://global.sap.com/campaigns/benchmark/index.epx>. Para sistemas SAP recentemente implantados, você deve passaram por meio de um exercício de dimensionamento que deve determinar os requisitos de SAPS do sistema.
Consulte também este blog e documento anexado para dimensionamento SAP no Azure: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Etapa 2**: sistemas existentes, o volume de e/s e operações de e/s por segundo no servidor DBMS devem ser medidas. Para sistemas recentemente planejados, o exercício de dimensionamento para o novo sistema também deve fornecer ideias aproximadas dos requisitos e/s no lado DBMS. Se não tiver certeza, você precisará eventualmente conduzir uma prova de conceito.

**Etapa 3**: comparar o requisito de SAPS de servidor DBMS com os SAPS os diferentes tipos de máquina virtual do Azure podem fornecer. As informações sobre SAPS os diferentes tipos de máquina virtual do Azure é documentadas na anotação SAP [1928533]. O foco deve ser primeiro na VM DBMS desde que a camada de banco de dados é a camada em um sistema SAP NetWeaver que não dimensionar na maioria das implantações. Em contraste, a camada de aplicativo SAP pode ser dimensionada check-out. Se nenhuma do SAP suporte para tipos de máquina virtual do Azure podem fornecer os SAPS necessários, a carga de trabalho do sistema SAP planejado não pode ser executada no Azure. Você precisa alterar o volume de carga de trabalho para o sistema ou que seja necessário implantar o sistema local.

**Etapa 4**: como documentadas [aqui][virtual-machines-sizes], Azure impõe uma cota IOPS por VHD independente se você usa o armazenamento padrão ou Premium armazenamento. Depende do tipo de máquina virtual, o número de VHDs que pode ser montado varia. Como resultado, você pode calcular um número máximo de IOPS que pode ser obtido com cada um dos diferentes tipos de máquina virtual. Dependente o layout de arquivo de banco de dados, você pode distribuição VHDs para se tornar um volume no sistema operacional convidado. No entanto, se o volume IOPS atual do sistema SAP implantado excede os limites de cálculo do tipo máquina virtual maior do Azure e se não há nenhuma chance para compensar com mais memória, a carga de trabalho do sistema SAP pode ser afetada seriamente. Nesses casos, você pode pressionar um ponto onde você não deve implantar o sistema no Azure.

**Etapa 5**: especialmente no SAP sistemas que são implantados no local em configurações de 2 camadas, as chances são que o sistema talvez precise ser configurado no Azure em uma configuração de nível 3. Nesta etapa, você precisa verificar se há um componente na camada de aplicativo SAP que não podem ser dimensionados e que prefere não se encaixam os recursos de CPU e memória que oferecem os diferentes tipos de máquina virtual do Azure. Se realmente houver tal componente, o sistema SAP e sua carga de trabalho não podem ser implantados em Azure. Mas se você pode fora de escala os componentes do aplicativo SAP em várias VMs do Azure, o sistema pode ser implantado em Azure. 

**Etapa 6**: se os componentes de camada de aplicativo DBMS e SAP podem ser executados em VMs do Azure, a configuração precisa ser definido com relação ao:

* Número de VMs Azure
* Tipos de máquina virtual para os componentes individuais
* Número de VHDs em DBMS VM para fornecer suficiente IOPS

## <a name="managing-azure-assets"></a>Gerenciando ativos do Azure

### <a name="azure-portal"></a>Portal do Azure
Portal do Azure é um dos três interfaces para gerenciar implantações de máquina virtual do Azure. As tarefas de gerenciamento básicas, como Implantando VMs de imagens, podem ser feitas por meio do Portal do Azure. Além disso, a criação de contas de armazenamento, redes virtuais e outros componentes Azure também são tarefas Portal do Azure pode manipular muito bem. Entretanto, funcionalidade como Carregando VHDs de locais no Azure ou copiar um VHD dentro do Azure é tarefas que exigem ferramentas de terceiros ou administração através do PowerShell ou CLI.
 
![Portal do Microsoft Azure - visão geral de máquina Virtual][planning-guide-figure-800]

[comentário]: <>  (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comentário]: <>  (MSSedusch * < https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Tarefas de administração e configuração para a instância de máquina Virtual são possíveis de dentro do Portal do Azure. 

Além de reiniciar e desligar uma máquina Virtual pode também anexar, desanexar e criar discos de dados para a instância de máquina Virtual, para capturar a instância para preparação da imagem e configurar o tamanho da instância de máquina Virtual.

Portal do Azure fornece funcionalidade básica para implantar e configurar VMs e muitos outros serviços do Azure. Funcionalidade porém não todos disponível é coberta pelo Portal do Azure. No Portal do Azure, não é possível realizar tarefas como:

* Carregando VHDs Azure
* Copiando VMs

[comentário]: <>  (MShermannd TODO o que são automação de serviço para VMs SAP?)
[comentário]: <>  (Implantação de MSSedusch de vários SO VMs, enquanto isso, possíveis)
[comentário]: <>  (MSSedusch também qualquer tipo de automação sobre implantação não é possível com o portal do Azure. Tarefas como implantação de scripts de várias VMs não é possível através do Portal do Azure.) 

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gerenciamento via cmdlets do PowerShell do Microsoft Azure
Windows PowerShell é uma estrutura poderosa e extensível que foi amplamente adotada por clientes implantando números maiores de sistemas no Azure. Após a instalação de cmdlets do PowerShell em um desktop, laptop ou estação de gerenciamento dedicada, cmdlets do PowerShell pode ser executados remotamente.

O processo para habilitar um desktop/laptop local para o uso de cmdlets do PowerShell do Azure e como configurar aqueles para o uso com as inscrições Azure é descrito [neste artigo][powershell-install-configure]. 

Etapas mais detalhadas sobre como instalar, atualizar e configurar os cmdlets do PowerShell do Azure também podem ser encontradas [neste capítulo do guia de implantação] [implantação-guia-4.1].

Experiência do cliente até o momento foi que PowerShell (PS) é certamente a ferramenta mais eficiente para implantar VMs e criar etapas personalizadas na implantação de VMs. Todos os clientes executando instâncias SAP no Azure estão usando cmdlets PS para complementar tarefas de gerenciamento que eles no Portal do Azure ou ainda estiver usando o PS cmdlets exclusivamente para gerenciar suas implantações no Azure. Como os cmdlets específicos Azure compartilhar a mesma convenção de nomenclatura como as mais de 2000 relacionados os cmdlets do Windows, é uma tarefa fácil para os administradores do Windows aproveitar esses cmdlets.

Consulte exemplo aqui: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comentário]: <>  (MShermannd TODO descrevem novo comando CLI quando testado)
Implantação da extensão de monitoramento do Azure para SAP (consulte Capítulo [Azure monitoramento solução para SAP] [planejamento-guia-9.1] neste documento) só é possível por meio do PowerShell ou CLI. Portanto é obrigatório para configurar o PowerShell ou CLI ao implantar ou administrar um sistema SAP NetWeaver no Azure.  

Como Azure oferece mais funcionalidade, novos cmdlets PS vai ser adicionado requer uma atualização dos cmdlets. Portanto faz sentido para verificar o site de Download do Azure pelo menos uma vez o mês <https://azure.microsoft.com/downloads/> para uma nova versão dos cmdlets. A nova versão apenas será instalada sobre a versão mais antiga.

Para uma lista geral do Azure relacionadas comandos do PowerShell Verifique aqui: <https://msdn.microsoft.com/library/azure/dn708514.aspx>. 

### <a name="management-via-microsoft-azure-cli-commands"></a>Gerenciamento por meio de comandos do Microsoft Azure

Para clientes que usam Linux e deseja gerenciar Azure recursos Powershell talvez não seja uma opção. Como alternativa, a Microsoft oferece CLI do Azure.
A CLI do Azure fornece um conjunto de código-fonte aberto, entre plataformas comandos para trabalhar com a plataforma Azure. A CLI do Azure fornece praticamente a mesma funcionalidade encontrada no portal do Azure.

Para obter informações sobre instalação, configuração e como usar CLI ver comandos para realizar tarefas Azure

* [Instalar o Azure CLI][xplat-cli]
* [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de recursos do Azure e a CLI do Azure][virtual-machines-linux-cli-deploy-templates]
* [Usar a CLI Azure para Mac, Linux e Windows com o Azure Resource Manager][xplat-cli-azure-resource-manager]

Leia também o capítulo [Azure CLI para Linux VMs] [implantação-guia-4.5.2] em [guia de implantação] [-guia de planejamento] sobre como usar CLI do Azure para implantar a extensão de monitoramento do Azure para SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Maneiras diferentes para implantar VMs para SAP no Azure
Neste capítulo, você aprenderá as diferentes maneiras de implantar uma máquina virtual no Azure. Procedimentos de preparação adicional, bem como manipulação de VHDs e VMs no Azure é abordados neste capítulo.

### <a name="deployment-of-vms-for-sap"></a>Implantação de VMs SAP
Microsoft Azure oferece várias maneiras para implantar VMs e discos associados. Portanto, é muito importante entender as diferenças desde preparativos das VMs podem diferir dependendo do método de implantação. Em geral, podemos será dar uma olhada nos seguintes cenários:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Mover uma máquina virtual do local para o Azure com um disco não generalizado
Você planeja mover um sistema SAP específico de locais no Azure. Isso pode ser feito carregando o VHD que contém os binários do sistema operacional, os binários de SAP e DBMS plus os VHDs com os arquivos de dados e log do DBMS no Azure. Em contraste para [cenário #2 abaixo] [planejamento-guia-5.1.2], você manter o hostname, SAP SID e contas de usuário SAP a máquina virtual do Azure conforme eles foram configurados no ambiente local. Portanto, não é necessário generalizar a imagem. Consulte capítulos [preparação para mover uma máquina virtual do local para o Azure com um disco não generalizado] [planejamento-guia-5.2.1] deste documento para etapas de preparação do local e o carregamento de VMs não generalizado ou VHDs no Azure. Leia capítulo [cenário 3: mover uma máquina virtual do local usando um VHD de Azure não generalizado com SAP] [implantação-guia-3.4] em [guia de implantação] [-guia de implantação] para obter etapas detalhadas de implantação como uma imagem no Azure.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implantando uma máquina virtual com uma imagem específica do cliente
Devido a requisitos de patch específico da sua versão do sistema operacional ou DBMS, as imagens fornecidas do Azure Marketplace podem não atender às suas necessidades. Portanto, talvez seja necessário criar uma máquina virtual usando sua própria imagem de máquina virtual do sistema operacional/DBMS 'private', que pode ser implementada várias vezes posteriormente. Para preparar uma imagem como 'private' para duplicação, os seguintes itens devem ser considerados:

___

> ![Windows][Logo_Windows] Windows
>
> As configurações do Windows (como Windows SID e o nome do host) devem ser abstraídos/generalizado na VM local por meio do comando sysprep.
[comentário]: <>  (MSSedusch > ver mais detalhes aqui:)
[comentário]: <>  (MShermannd TODO primeiro link é sobre o modelo clássico. Não encontrar um artigo de documento Azure)
[comentário]: <>  (MSSedusch >< https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>)
[comentário]: <>  (MSSedusch >< http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)
>
> ![Linux][Logo_Linux] Linux
>
> Siga as etapas descritas neste artigo para [SUSE] [ virtual-machines-linux-create-upload-vhd-suse] ou [Red Hat] [ virtual-machines-linux-redhat-create-upload-vhd] para preparar um VHD para ser carregado Azure.

___

Se você já tiver instalado o conteúdo SAP em sua máquina virtual de local (especialmente para sistemas de 2 camadas), você pode adaptar as configurações do sistema SAP após a implantação da máquina virtual do Azure através da instância renomear procedimento compatível com o Gerenciador de provisionamento de Software do SAP (Observação SAP [1619720]). Consulte capítulos [preparação para implantar uma máquina virtual com uma imagem específica do cliente para SAP] [planejamento-guia-5.2.2] e [carregar um VHD do local para o Azure] [planejamento-guia-5.3.2] deste documento para etapas de preparação do local e o carregamento de uma máquina virtual generalizado no Azure. Leia capítulo [cenário 2: Implantando uma máquina virtual com uma imagem personalizada para SAP] [implantação-guia-3.3] em [guia de implantação] [-guia de implantação] para obter etapas detalhadas de implantação como uma imagem no Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implantando uma máquina virtual fora do Azure Marketplace
Você gostaria de usar um Microsoft ou festa 3º fornecido imagem de máquina virtual do Azure Marketplace para implantar sua máquina virtual. Após você implantado sua máquina virtual no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP e/ou DBMS dentro de sua máquina virtual, como você faria em um ambiente local. Para obter descrição de implantação mais detalhadas, consulte Capítulo [cenário 1: Implantando uma máquina virtual fora do Azure Marketplace para SAP] [implantação-guia-3,2] em [guia de implantação] [-guia de implantação].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparando VMs com SAP do Azure
Antes de carregar VMs em Azure que você precisa para garantir que as VMs e VHDs atendem a certos requisitos. Existem diferenças pequenas dependendo do método de implantação que é usado. 

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover uma máquina virtual do local para o Azure com um disco não generalizado
Um método de implantação comum é mover uma máquina virtual existente que executa um sistema SAP do local para o Azure. Que máquina virtual e o sistema SAP na VM apenas deverão ser executado no Azure usando o mesmo nome de host e muito provavelmente o mesmo SID do SAP. Nesse caso, o sistema operacional da máquina virtual do convidado não deve ser generalizado para várias implantações. Se a rede local adquiriu estendida em Azure (consulte Capítulo [cruzado local - implantação de único ou várias VMs SAP em Azure com a exigência de sendo totalmente integrado à rede local] [planejamento-guia-2.2] neste documento), e em seguida, até mesmo as mesmas contas de domínio podem ser usadas dentro a máquina virtual, como aqueles foram usados antes de local. 

Requisitos ao preparar seu próprio disco de máquina virtual do Azure são:

* Originalmente o VHD contendo o sistema operacional pode ter apenas um tamanho máximo de 127GB. Essa limitação adquiriu eliminada no final de março de 2015. Agora o VHD contendo o sistema operacional pode ser até 1TB de tamanho como qualquer outro armazenamento do Azure hospedado VHD também.
[comentário]: <>  (MShermannd TODO precisa verificar se CLI também converte estático)
* Ele precisa estar no formato VHD fixo. Dinâmico VHDs ou VHDs no formato de VHDx ainda não são aceitas no Azure. VHDs dinâmicos serão convertidas em VHDs estáticos quando você carrega o VHD com cmdlets do PowerShell ou CLI
* VHDs que são montados para a máquina virtual e devem ser montados novamente no Azure para a necessidade de máquina virtual estar em um formato VHD fixo. Aplica o mesmo limite de tamanho do disco SO discos de dados também. VHDs podem ter um tamanho máximo de 1TB. VHDs dinâmicos serão convertidas em VHDs estáticos quando você carrega o VHD com cmdlets do PowerShell ou CLI
* Adicione outra conta local com privilégios de administrador que podem ser usadas pelo suporte da Microsoft ou que pode ser atribuído como o contexto para serviços e aplicativos para ser executado em até que a máquina virtual é implantada e usuários mais apropriados pode ser usada.
* No caso de uso de um cenário de implantação somente na nuvem (consulte Capítulo [somente na nuvem - implantações de máquina Virtual para o Azure sem dependências na rede de clientes do local] [planejamento-guia-2.1] deste documento) em combinação com este método de implantação, contas de domínio podem não funcionar depois que o disco do Azure for implantado no Azure. Isso é especialmente verdadeiro para as contas que são usadas para executar serviços, como os aplicativos DBMS ou SAP. Portanto, você precisa substituir tais contas de domínio com contas locais de máquina virtual e excluir as contas de domínio local na VM. Manter usuários de domínio do local da imagem de máquina virtual não é um problema quando a máquina virtual é implantada no cenário entre locais, conforme descrito no capítulo [cruzado local - implantação de único ou várias VMs SAP em Azure com a exigência de sendo totalmente integrado à rede local] [planejamento-guia-2.2] neste documento.
* Se as contas de domínio foram usadas como logon DBMS ou os usuários quando executando o sistema local e essas VMs devem ser implantado em cenários somente na nuvem, os usuários do domínio precisam ser excluída. Você precisa certificar-se de que o administrador local além de outro usuário local de máquina virtual é adicionado como um logon/usuário para o DBMS como administradores.
* Adicione outras contas locais como aqueles podem ser necessárias para o cenário de implantação específicas.

___

> ![Windows][Logo_Windows] Windows
>
> Neste cenário nenhuma generalização (sysprep) da máquina virtual é necessário para carregar e implantar a máquina virtual no Azure.
> Verifique se a unidade D:\ não é usada montagem automática de disco do conjunto para discos anexados conforme descrito no capítulo [configuração montagem automática para discos anexados] [planejamento-guia-5.5.3] neste documento.
> 
> ![Linux][Logo_Linux] Linux
>
> Neste cenário nenhuma generalização (waagent-deprovision) da máquina virtual é necessária para carregar e implantar a máquina virtual no Azure.
> Certifique-se de que/manutenção/recurso não será usado e que todos os discos são montados via uuid. Para o disco de sistema operacional certificar-se de que a entrada de carregador de inicialização também reflete a montagem baseado em uuid.

___

#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implantar uma máquina virtual com uma imagem específica do cliente para SAP
Arquivos VHD que contêm um sistema operacional generalizado também são armazenados em contêineres de contas de armazenamento do Azure. Você pode implantar uma nova VM de tal uma imagem VHD referenciando VHD como uma fonte de VHD em seus arquivos de modelo de implantação, conforme descrito no capítulo [cenário 2: Implantando uma máquina virtual com uma imagem personalizada para SAP] [implantação-guia-3.3] [guia de implantação do] [-guia de implantação]. 

Requisitos ao preparar sua própria imagem de máquina virtual do Azure são:

* Originalmente o VHD contendo o sistema operacional pode ter apenas um tamanho máximo de 127GB. Essa limitação adquiriu eliminada no final de março de 2015. Agora o VHD contendo o sistema operacional pode ser até 1TB de tamanho como qualquer outro armazenamento do Azure hospedado VHD também.
[comentário]: <>  (MShermannd TODO precisa verificar se CLI também converte estático)
* Ele precisa estar no formato VHD fixo. Dinâmico VHDs ou VHDs no formato de VHDx ainda não são aceitas no Azure. VHDs dinâmicos serão convertidas em VHDs estáticos quando você carrega o VHD com cmdlets do PowerShell ou CLI
* VHDs que são montados para a máquina virtual e devem ser montados novamente no Azure para a necessidade de máquina virtual estar em um formato VHD fixo. Aplica o mesmo limite de tamanho do disco SO discos de dados também. VHDs podem ter um tamanho máximo de 1TB. VHDs dinâmicos serão convertidas em VHDs estáticos quando você carrega o VHD com cmdlets do PowerShell ou CLI
* Desde que todos os usuários de domínio registrado como os usuários a máquina virtual não irá existir em um cenário de somente na nuvem (consulte Capítulo [somente na nuvem - implantações de máquina Virtual para o Azure sem dependências na rede de clientes do local] [planejamento-guia-2.1] deste documento), serviços usando esse domínio contas podem não funcionar depois que a imagem for implantada no Azure. Isso é especialmente verdadeiro para as contas que são usadas para executar serviços como aplicativos DBMS ou SAP. Portanto, você precisa substituir tais contas de domínio com contas locais de máquina virtual e excluir as contas de domínio local na VM. Manter usuários de domínio do local da imagem de máquina virtual pode não ser um problema quando a máquina virtual é implantada no cenário cruzado local conforme descrito no capítulo [cruzado local - implantação de único ou várias VMs SAP em Azure com a exigência de sendo totalmente integrado à rede local] [planejamento-guia-2.2] neste documento.
* Adicione outra conta local com privilégios de administrador que podem ser usadas pelo suporte da Microsoft em investigações de problema ou que pode ser atribuído como o contexto para serviços e aplicativos para ser executado em até que a máquina virtual é implantada e usuários mais apropriados pode ser usada.
* Em implantações somente na nuvem e onde as contas de domínio foram usadas como logon DBMS ou os usuários quando executando o sistema local, os usuários do domínio deverão ser excluídos. Você precisa certificar-se de que o administrador local além de outro usuário local de máquina virtual é adicionado como um logon/usuário do DBMS como administradores.
* Adicione outras contas locais como aqueles podem ser necessárias para o cenário de implantação específicas.
* Se a imagem contém uma instalação do SAP NetWeaver e a renomeação do nome do host do nome original no ponto de implantação do Azure é provável, é recomendável copiar as versões mais recentes do DVD de Gerenciador de provisionamento do SAP Software para o modelo. Isso permitirá que você use facilmente a funcionalidade de renomear SAP fornecido para adaptar o nome de host alterado e/ou alterar o SID do sistema SAP dentro da imagem de máquina virtual implantado assim que uma nova cópia é iniciada.

___

> ![Windows][Logo_Windows] Windows
>
> Verifique se a unidade D:\ não é usada montagem automática de disco do conjunto para discos anexados conforme descrito no capítulo [configuração montagem automática para discos anexados] [planejamento-guia-5.5.3] neste documento.
> 
> ![Linux][Logo_Linux] Linux
>
> Certifique-se de que/manutenção/recurso não será usado e que todos os discos são montados via uuid. Para o disco de sistema operacional Verifique se a entrada do carregador de inicialização também reflete a montagem baseado em uuid.

___

* Interface usuário SAP (para administrativo e configuração fins) pode ser pré-instalado em como um modelo.
* Outros softwares necessários para executar as VMs com êxito em cenários de cruz local podem ser instalado como este software pode trabalhar com a renomeação da máquina virtual.

Se a máquina virtual está preparada suficientemente para ser genérico e eventualmente independentes de contas/usuários não está disponíveis no cenário de implantação Azure alvo, a última etapa de preparação da generalizar como uma imagem é conduzida.

##### <a name="generalizing-a-vm"></a>Generalizar uma máquina virtual 

___

[comentário]: <>  (MShermannd TODO precisa procurar artigos melhores / documentação sobre generalizar VMs para BRAÇO)
> ![Windows][Logo_Windows] Windows
>
> A última etapa é fazer logon uma máquina virtual com uma conta de administrador. Abra uma janela de comando do Windows como 'Administrador'. Vá para ...\windows\system32\sysprep e executar sysprep.exe.
> Uma pequena janela será exibida. É importante verificar a opção de 'Generalize' (o padrão é desmarcado) e altere a opção de desligamento seu padrão de 'Reinicializar' para 'Desligamento'. Este procedimento pressupõe que o processo de sysprep é executado local no SO convidado de uma máquina virtual.
> Se você quiser executar o procedimento com uma máquina virtual esteja em execução no Azure, siga as etapas descritas [neste]artigo[virtual-machines-windows-capture-image].
> 
> ![Linux][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual Linux usar como um modelo do Gerenciador de recursos][virtual-machines-linux-capture-image]

___

### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferindo VMs e VHDs entre locais no Azure
Como carregar imagens de máquina virtual e discos Azure não é possível através do Portal do Azure, você precisa usar cmdlets do PowerShell do Azure ou CLI. Outra possibilidade é o uso da ferramenta 'AzCopy'. A ferramenta pode copiar VHDs entre locais e Azure (em ambas as direções). Ele também pode copiar VHDs entre regiões do Azure. Consulte [esta documentação] [ storage-use-azcopy] para download e o uso da AzCopy.

Uma terceira alternativa seria usar várias ferramentas de interface gráfica orientada por terceiros. No entanto, certifique-se de que essas ferramentas são suporte Blobs de página do Azure. Para nossas finalidades precisamos usar o armazenamento de Blob de página do Azure (as diferenças são descritas aqui: <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>). Além disso, as ferramentas fornecidas pelo Azure são muito eficientes em compactando as VMs e VHDs que precisa ser carregado. Isso é importante porque essa eficiência em compactação reduz o tempo de carregamento (que varia mesmo assim dependendo o link de carregamento com a internet do recurso local e a região de implantação Azure direcionadas). É uma suposição justa que carregando uma máquina virtual ou VHD de Europeu local para os Estados Unidos com base em dados Azure centros levará mais de carregar os mesmos VMs/VHDs os centros de dados do Azure europeu. 

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carregar um VHD do local para o Azure
Para carregar uma máquina virtual existente ou VHD da rede local precisa atender os requisitos conforme listado no capítulo [preparação para mover uma máquina virtual do local para o Azure com um disco não generalizado] tal máquina virtual ou VHD [planejamento-guia-5.2.1] deste documento.

Como uma máquina virtual não precisa ser generalizado e pode ser carregada no estado e forma tem após desligamento no lado local. O mesmo é verdadeiro para VHDs adicionais que não contêm qualquer sistema operacional. 

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregar um VHD e tornando um disco do Azure
Nesse caso queremos carregar um VHD, com ou sem um sistema operacional e colocá-la em uma máquina virtual como um disco de dados ou usá-la como disco do sistema operacional. Esse é um processo de várias etapa 

__PowerShell__

* Login à sua assinatura com _AzureRmAccount de logon_
* Definir a assinatura do seu contexto com _Set-AzureRmContext_ e parâmetro SubscriptionId ou o nome de inscrição - consulte <https://msdn.microsoft.com/library/mt619263.aspx>
* Carregue o VHD com _AzureRmVhd de adicionar_ a uma conta de armazenamento do Azure - consulte <https://msdn.microsoft.com/library/mt603554.aspx>
* Configurar o disco de sistema operacional de uma nova configuração de máquina virtual para o VHD com _Set-AzureRmVMOSDisk_ - consulte <https://msdn.microsoft.com/library/mt603746.aspx>
* Criar uma nova VM de config máquina virtual com _New-AzureRmVM_ - consulte <https://msdn.microsoft.com/library/mt603754.aspx>
* Adicionar um disco de dados a uma nova VM com _Add-AzureRmVMDataDisk_ - consulte <https://msdn.microsoft.com/library/mt603673.aspx>

__CLI Azure__

* Alternar para o modo de Gerenciador de recursos do Azure com _braço de modo config azure_
* Login à sua assinatura com _login azure_
* Selecione a assinatura com _conjunto de conta do Microsoft azure `<subscription name or id` _
* Carregue o VHD com _blob de armazenamento do azure carregue_ - consulte [utilizando a CLI Azure com o armazenamento do Azure][storage-azure-cli]
* Criar uma nova máquina virtual especificando o VHD carregado como disco do sistema operacional com _máquina virtual azure criar_ e parâmetro -d
* Adicionar um disco de dados a uma nova VM com _máquina virtual disco anexar-novo_

__Modelo__

* Carregue o VHD com Powershell ou Azure CLI
* Implante a máquina virtual com um modelo JSON referenciando o VHD, conforme mostrado [neste](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json)modelo de JSON de exemplo.

#### <a name="deployment-of-a-vm-image"></a>Implantação de uma imagem de máquina virtual
Para carregar uma máquina virtual existente ou VHD da rede local para usá-la como uma imagem de máquina virtual do Azure tal máquina virtual ou VHD precisa atender aos requisitos listados no capítulo [preparação para implantar uma máquina virtual com uma imagem específica do cliente para SAP] [planejamento-guia-5.2.2] deste documento.

* Usar o _sysprep_ no Windows ou _waagent-deprovision_ no Linux generalizar sua máquina virtual - veja [como capturar uma máquina de virtual do Windows no modelo de implantação do Gerenciador de recursos] [ virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [como capturar uma máquina virtual Linux usar como um modelo do Gerenciador de recursos][virtual-machines-linux-capture-image-capture]
* Login à sua assinatura com _AzureRmAccount de logon_
* Definir a assinatura do seu contexto com _Set-AzureRmContext_ e parâmetro SubscriptionId ou o nome de inscrição - consulte <https://msdn.microsoft.com/library/mt619263.aspx>
* Carregue o VHD com _AzureRmVhd de adicionar_ a uma conta de armazenamento do Azure - consulte <https://msdn.microsoft.com/library/mt603554.aspx>
* Configurar o disco de sistema operacional de uma nova configuração de máquina virtual para o VHD com _Set-AzureRmVMOSDisk - SourceImageUri - CreateOption fromImage_ -consulte <https://msdn.microsoft.com/library/mt603746.aspx>
* Criar uma nova VM de config máquina virtual com _New-AzureRmVM_ - consulte <https://msdn.microsoft.com/library/mt603754.aspx>

__CLI Azure__

* Usar o _sysprep_ no Windows ou _waagent-deprovision_ no Linux generalizar sua máquina virtual - veja [como capturar uma máquina de virtual do Windows no modelo de implantação do Gerenciador de recursos] [ virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [como capturar uma máquina virtual Linux usar como um modelo do Gerenciador de recursos] [ virtual-machines-linux-capture-image-capture] para Linux
* Alternar para o modo de Gerenciador de recursos do Azure com _braço de modo config azure_
* Login à sua assinatura com _login azure_
* Selecione a assinatura com _conjunto de conta do Microsoft azure `<subscription name or id` _
* Carregue o VHD com _blob de armazenamento do azure carregue_ - consulte [utilizando a CLI Azure com o armazenamento do Azure][storage-azure-cli]
* Criar uma nova máquina virtual especificando o VHD carregado como disco do sistema operacional com _máquina virtual azure criar_ e parâmetro -Q

__Modelo__

* Usar o _sysprep_ no Windows ou _waagent-deprovision_ no Linux generalizar sua máquina virtual - veja [como capturar uma máquina de virtual do Windows no modelo de implantação do Gerenciador de recursos] [ virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] ou [como capturar uma máquina virtual Linux usar como um modelo do Gerenciador de recursos] [ virtual-machines-linux-capture-image-capture] para Linux
* Carregue o VHD com Powershell ou Azure CLI
* Implante a máquina virtual com um modelo JSON fazer referência à imagem VHD conforme mostrado [neste](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json)modelo de JSON de exemplo.

#### <a name="downloading-vhds-to-on-premises"></a>Baixando VHDs ao local
Azure infraestrutura como um serviço não é uma rua unidirecional de apenas ser capaz de carregar VHDs e SAP sistemas. Você pode mover SAP sistemas do Azure de volta no mundo de local.

Durante o horário do download os VHDs não podem estar ativos. Mesmo quando o download VHDs que são montados em VMs, a máquina virtual precisa ser desligada. Se desejar somente baixar o conteúdo do banco de dados que deve ser usado para configurar um novo sistema local e se for aceitável que durante o horário do download e a instalação do novo sistema que o sistema do Azure ainda pode estar operacional, você pode evitar um longo tempo de inatividade executando um backup do banco de dados compactados em um VHD e apenas baixar desse VHD em vez de também baixando a máquina virtual de base de sistema operacional.

#### <a name="powershell"></a>PowerShell

Depois que o sistema SAP for interrompido e a máquina virtual está desligado, você pode usar o cmdlet do PowerShell AzureRmVhd salvar no destino local para baixar os discos VHD voltar para o mundo de local. Para fazer o que, você precisa da URL do VHD que você pode encontrar no 'Armazenamento seção' do Portal do Azure (necessidade para navegar para a conta de armazenamento e o contêiner de armazenamento onde o VHD foi criado) e você precisa saber onde o VHD deve ser copiado.

Em seguida, você pode aproveitar o comando simplesmente definindo o parâmetro SourceUri como a URL do VHD para baixar e o LocalFilePath como o local físico do VHD (incluindo seu nome). O comando pode parecer com:

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

Para obter mais detalhes do cmdlet salvar AzureRmVhd, verifique aqui <https://msdn.microsoft.com/library/mt622705.aspx>. 

#### <a name="cli"></a>CLI

Depois que o sistema SAP for interrompido e a máquina virtual está desligado, você pode usar o download do Azure CLI comando armazenamento do azure blob no destino local para baixar os discos VHD voltar para o mundo de local. Para fazer isso, você precisa do nome e o contêiner do VHD que você pode encontrar na 'Armazenamento seção' do Portal do Azure (necessidade para navegar para a conta de armazenamento e o contêiner de armazenamento onde o VHD foi criado) e você precisa saber onde o VHD deve ser copiado.

Em seguida, você pode aproveitar o comando simplesmente definindo a blob de parâmetros e o contêiner do VHD para download e o destino como o local de destino físico do VHD (incluindo seu nome). O comando pode parecer com:

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download> 
```

### <a name="transferring-vms-and-vhds-within-azure"></a>Transferindo VMs e VHDs dentro do Azure

#### <a name="copying-sap-systems-within-azure"></a>Copiando sistemas SAP dentro do Azure

Um sistema SAP ou até mesmo um servidor DBMS dedicado uma camada de aplicativo SAP de suporte provavelmente consiste vários VHDs que contêm o sistema operacional com os binários ou os arquivos de dados e log do banco de dados SAP. Nem a funcionalidade Azure da copiando VHDs nem a funcionalidade Azure da salvando VHDs disco tem um mecanismo de sincronização que faria instantâneo diversos VHDs sincronia. Portanto, o estado dos VHDs copiados ou salvos mesmo que aqueles são feitos contra a máquina virtual mesma seriam diferente. Isso significa que no caso concreto de ter dados diferentes e logfile(s) contidas os VHDs diferentes, o banco de dados no final seria inconsistente. 

**Conclusão: Para copiar ou salvar VHDs que fazem parte de uma configuração de sistema SAP é necessário parar o sistema SAP e também precisa desligar a máquina virtual implantada. Somente depois que você copiar ou baixar o conjunto de VHDs ou criar uma cópia do sistema SAP no Azure ou local.**

Discos de dados são armazenados como arquivos VHD em uma conta de armazenamento do Azure e podem ser diretamente anexar uma máquina virtual ou ser usada como uma imagem. Nesse caso, o VHD é copiado para outro local antes de beeing anexado à máquina virtual. O nome completo do arquivo VHD no Azure deve ser exclusivo dentro do Azure. Conforme mencionado anteriormente já, o nome é o tipo de um nome de três partes que se parece com: 

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### <a name="powershell"></a>PowerShell
Você pode usar cmdlets do PowerShell do Azure para copiar um VHD, conforme mostrado [neste]artigo[storage-powershell-guide-full-copy-vhd].

##### <a name="cli"></a>CLI
Você pode usar CLI do Azure para copiar um VHD, conforme mostrado [neste] artigo[storage-azure-cli-copy-blobs]

##### <a name="azure-storage-tools"></a>Azure ferramentas armazenamento

* <http://azurestorageexplorer.codeplex.com/releases/View/125870>

Também há profissionais edições do gerenciadores de armazenamento do Azure que podem ser encontradas aqui:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer> 


A cópia de um VHD dentro de uma conta de armazenamento é um processo que leva apenas alguns segundos (semelhante ao hardware de SAN criando instantâneos com preguiça cópia e na gravação). Depois que você tiver uma cópia do arquivo VHD, você pode anexá-lo em uma máquina virtual ou usá-la como uma imagem para anexar cópias do VHD a máquinas virtuais.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### <a name="cli"></a>CLI
```
azure config mode arm 

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiando discos entre contas de armazenamento do Azure
Esta tarefa não pode ser executada no Portal do Azure. Você pode ise Azure PowerShell cmdlets, CLI Azure ou uma terceira festa armazenamento navegador. Os cmdlets do PowerShell ou comandos podem criar e gerenciar blobs, que incluem a capacidade de copiar assíncrona blobs entre contas de armazenamento e regiões na assinatura Azure.

##### <a name="powershell"></a>PowerShell 

Também é possível copiar VHDs entre assinaturas. Para obter mais informações, leia [Este artigo][storage-powershell-guide-full-copy-vhd]. 

O fluxo básico da lógica de cmdlet do PS tem esta aparência:

* Criar um contexto de conta de armazenamento para a conta de armazenamento de origem com _New-AzureStorageContext_ - consulte <https://msdn.microsoft.com/library/dn806380.aspx>
* Criar um contexto de conta de armazenamento para a conta de armazenamento de destino com _New-AzureStorageContext_ - consulte <https://msdn.microsoft.com/library/dn806380.aspx>
* Inicie a cópia com

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verificar o status da cópia em um loop com
 
```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anexe o novo VHD para uma máquina virtual, conforme descrito acima.

Para obter exemplos, consulte [Este artigo][storage-powershell-guide-full-copy-vhd]

##### <a name="cli"></a>CLI
* Inicie a cópia com

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* Verificar o status, se a cópia em um loop com

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```
  
* Anexe o novo VHD para uma máquina virtual, conforme descrito acima.

Para obter exemplos, consulte [Este artigo][storage-azure-cli-copy-blobs]

### <a name="disk-handling"></a>Manipulação de disco
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura de máquina virtual/VHD para implantações do SAP
Ideal a manipulação da estrutura de uma máquina virtual e os VHDs associados deve ser muito simple. Em instalações de locais, clientes desenvolveu várias maneiras de estruturar uma instalação de servidor. 

* Um VHD base que contém o sistema operacional e todos os binários DBMS e/ou SAP. Desde março de 2015, este VHD pode ser até 1TB de tamanho em vez de restrições anteriores que limitado-lo a 127 GB. 
* Um ou vários VHDs que contém o arquivo de log DBMS do banco de dados SAP e o arquivo de log da área de armazenamento temporário DBMS (se o DBMS dá suporte a isso). Se os requisitos de IOPS de log de banco de dados forem altos, você precisa diversos VHDs de distribuição para atingir o volume IOPS obrigatório. 
* Um número de VHDs contendo um ou dois arquivos de banco de dados do banco de dados SAP e os arquivos de dados temp DBMS também (se o DBMS dá suporte a isso).

![Configuração de referência do Azure IaaS máquina virtual SAP][planning-guide-figure-1300]

[comentário]: <>  (MShermannd TODO descrever Linux estrutura)

___

> ![Windows][Logo_Windows] Windows
>
> Com muitos clientes observamos configurações onde, por exemplo, SAP e DBMS binários não foram instalados na unidade c:\ onde o sistema operacional foi instalado. Havia vários motivos para isso, mas quando podemos deu voltar à raiz, geralmente era que as unidades foram pequenas e atualizações do sistema operacional necessário espaço adicional de 10 a 15 anos. As duas condições não aplicar esses dias geralmente muito mais. Hoje a unidade c:\ pode ser mapeada em discos de grande volume ou VMs. Para manter as implantações simples em sua estrutura, é recomendável siga o procedimento padrão de implantação de sistemas SAP NetWeaver no Azure
>
> O arquivo de paginação do sistema operacional Windows deve estar na unidade d: (disco não-persistente) 
> 
> ![Linux][Logo_Linux] Linux
>
> Coloque swapfile do Linux em /mnt/manutenção/recurso no Linux, conforme descrito [neste artigo][virtual-machines-linux-agent-user-guide]. O arquivo de troca pode ser configurado no arquivo de configuração de /etc/waagent.conf o agente de Linux. Adicionar ou alterar as configurações a seguir:

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, é necessário reiniciar o agente de Linux com

```
sudo service waagent restart
```

Leia SAP anotação [1597355] para obter mais detalhes sobre o tamanho do arquivo de troca recomendado

___

O número de VHDs usado para os arquivos de dados DBMS e o tipo de armazenamento do Azure estas VHDs hospedadas em deve ser determinado pelos requisitos de IOPS e a latência obrigatório. Cotas exatas são descritas [neste] artigo[virtual-machines-sizes]

Experiência de implantações de SAP nos últimos dois anos ministrada conosco algumas lições que podem ser resumidas como:

* Tráfego IOPS aos arquivos de dados diferentes nem sempre é a mesma desde que os sistemas de cliente existentes podem diferente dimensionados arquivos de dados que representa seus bancos de dados do SAP. Como resultado acontece melhor usando uma configuração RAID sobre diversos VHDs para colocar os arquivos de dados que LUNs gravados fora aqueles. Houve situações, especialmente com onde uma taxa IOPS acertar a cota de um único VHD contra o log de transação DBMS padrão de armazenamento do Azure. Em cenários assim o uso de armazenamento Premium é recomendado ou Alternativamente agregação diversos VHDs de armazenamento padrão com um software RAID.

___

> ![Windows][Logo_Windows] Windows
>
> * [Práticas recomendadas de desempenho para o SQL Server em máquinas virtuais do Azure][virtual-machines-sql-server-performance-best-practices]
> 
> ![Linux][Logo_Linux] Linux
>
> * [Configurar o Software RAID no Linux][virtual-machines-linux-configure-raid]
> * [Configurar LVM em uma máquina virtual de Linux no Azure][virtual-machines-linux-configure-lvm]
> * [Azure segredos de armazenamento e otimizações Linux e/s](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)

___

* Armazenamento de Premium está mostrando significativo melhor desempenho, especialmente para gravações do log de transação crítico. Cenários de SAP que são esperados para oferecer produção como o desempenho, é altamente recomendável usar série máquina virtual que pode aproveitar o armazenamento do Azure Premium.

Lembre-se de que o VHD que contém o sistema operacional, e como Recomendamos, os binários do SAP e o banco de dados (base VM) também, não está mais limitado a 127GB. Agora ele pode ter até 1TB em tamanho. Isso deve ser espaço suficiente para manter todo o arquivo por exemplo, incluindo logs de trabalho do SAP lote.

Para obter mais detalhes, especificamente para VMs DBMS e sugestões mais, consulte [DBMS guia de implantação] [guia dbms]


#### <a name="disk-handling"></a>Manipulação de disco
Na maioria dos cenários, você precisa criar discos adicionais para implantar o banco de dados do SAP para a máquina virtual. Vamos falar sobre as considerações de número de VHDs capítulo [estrutura de máquina virtual/VHD para implantações de SAP] [planejamento-guia-5.5.1] deste documento. Portal do Azure permite anexar e desanexar discos depois que uma máquina virtual base for implantada. Os discos podem ser anexados/desanexado quando a máquina virtual está ativo e em execução, além de quando ela for interrompida. Ao anexar um disco, o Portal do Azure oferece anexar um disco vazio ou um disco existente que neste momento não está conectado a outra VM. 

**Observação**: VHDs só podem ser anexados a uma VM a qualquer momento.
 
![Anexar / desanexar discos com armazenamento padrão do Azure][planning-guide-figure-1400]

Você precisa decidir se deseja criar um VHD novo e vazio (que pode ser criado na mesma conta de armazenamento como base em que máquina virtual está) ou se você deseja selecionar um VHD existente que foi carregado anteriormente e deve ser anexado a máquina virtual agora. 

**Importante**: você **Não** deseja usar o cache de Host com o armazenamento padrão do Azure. Você deve deixar a preferência de Cache Host no padrão de nenhum. Com o armazenamento do Azure Premium você deve ativar o cache de leitura se a característica e/s é lida, principalmente, como o tráfego de i/o típico com arquivos de dados do banco de dados. No caso de arquivo de log de transação do banco de dados sem cache é recomendado.

___

> ![Windows][Logo_Windows] Windows
>
> [Como anexar um disco de dados no portal do Azure][virtual-machines-windows-attach-disk-portal]
>
> Se discos estiverem conectados, você precisa login na máquina virtual para abrir o Gerenciador de disco do Windows. Se a montagem automática não estiver habilitada conforme recomendado capítulo [configuração montagem automática para discos anexados] [planejamento-guia-5.5.3], o volume de recentemente anexado precisa ficar online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Se discos são anexados, você precisará login para a máquina virtual e inicializar os discos conforme descrito [neste] artigo[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]

___

Se o novo disco for um disco vazio, você precisa formatar o disco também. Para a formatação, especialmente para arquivos de log e dados DBMS as mesmas recomendações para implantações de depender do DBMS se aplicam.

Conforme mencionado no capítulo [o Microsoft Azure Máquina Virtual conceito] [planejamento-guia-3,2], uma conta de armazenamento do Azure não fornece recursos infinitos em termos de volume e/s, volume IOPS e dados. Geralmente DBMS VMs mais são afetadas por este. Talvez seja melhor usar uma conta de armazenamento separada para cada máquina virtual, se você tiver alguns alto volume e/s VMs para implantar para ficar dentro do limite do volume de conta de armazenamento do Azure. Caso contrário, você precisa ver como você pode comparar essas VMs entre diferentes contas de armazenamento sem atingir o limite de cada única conta de armazenamento. Mais detalhes serão discutidas [DBMS guia de implantação] [dbms-guia]. Você também deve ter essas limitações em mente para o aplicativo SAP puro VMs do servidor ou outras VMs que eventualmente podem exigir VHDs adicionais.

Outro tópico que é relevante para as contas de armazenamento é se os VHDs em uma conta de armazenamento estão recebendo replicado localização geográfica. Replicação geográfica está habilitada ou desabilitada no nível da conta de armazenamento e não no nível de máquina virtual. Se replicação geográfica estiver habilitada, os VHDs dentro da conta de armazenamento seriam ser replicados em outro Azure data center dentro da mesma região. Antes de decidir sobre isso, você deve pensar sobre a seguinte restrição:

Replicação geográfica Azure funciona localmente em cada VHD em uma máquina virtual e não replicar o IOs em ordem cronológica em vários VHDs em uma máquina virtual. Portanto, o VHD que representa a máquina virtual base bem como qualquer VHDs adicionais anexados para a máquina virtual são replicados independentes umas das outras. Isso significa que há uma sincronização entre as alterações nos VHDs diferentes. O fato de que o IOs é duplicado independentemente da ordem na qual elas são escritas significa que a localização geográfica-replicação não é do valor para os servidores de banco de dados que têm seus bancos de dados distribuídos ao longo de diversos VHDs. Além de DBMS, também poderá haver outros aplicativos onde processos escrever ou manipular os dados em diferentes VHDs e onde ele é importante manter a ordem das alterações. Se esse for um requisito, replicação geográfica no Azure não deve ser ativada. Dependente se preciso ou deseje replicação geográfica para um conjunto de VMs, mas não para outro conjunto, você pode já categorizar VMs e seus VHDs relacionados em diferentes contas de armazenamento que têm replicação geográfica ativado ou desativado.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Configuração de montagem automática para discos anexados

___


> ![Windows][Logo_Windows] Windows
> 
> Para VMs que são criadas a partir próprias imagens ou discos, é necessário verificar e possivelmente definir o parâmetro de montagem automática. Definir esse parâmetro permitirá a máquina virtual após a reinicialização ou reimplantação no Azure para montar as unidades anexados/montado automaticamente. 
> O parâmetro é definido para as imagens fornecidas pela Microsoft no Azure Marketplace.
>
> Para definir a montagem automática, verifique a documentação da linha de comando executável diskpart.exe aqui: 
> 
> * [Opções de linha de comando DiskPart](https://technet.microsoft.com/library/cc766465.aspx)
> * [Montagem automática](http://technet.microsoft.com/library/cc753703.aspx)
> 
> A janela de linha de comando do Windows deve ser aberta como administrador.
> 
> Se discos estiverem conectados, você precisa login na máquina virtual para abrir o Gerenciador de disco do Windows. Se a montagem automática não estiver habilitada conforme recomendado capítulo [configuração montagem automática para discos anexados] [planejamento-guia-5.5.3], o volume recentemente anexado > precisa ser feita online e inicializado.
>
> ![Linux][Logo_Linux] Linux
>
> Você precisa inicializar um disco vazio recentemente anexado conforme descrito [neste]artigo[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Você também precisa adicionar novos discos a /etc/fstab.

___


### <a name="final-deployment"></a>Implantação final
Para a implantação final e etapas exatas, especialmente com relação a implantação do SAP estendido Monitoring, consultem [guia de implantação] [-guia de implantação].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Acessando sistemas SAP executados nas VMs do Azure
Cenários somente na nuvem, você talvez queira conectar-se a esses sistemas SAP através da internet pública usando interface usuário SAP. Nesses casos, os procedimentos a seguir precisam ser aplicadas.

Posteriormente no documento abordaremos o outro cenário principal, conectar-se aos sistemas SAP em implantações entre locais que têm uma conexão de-to-site (túnel VPN) ou conexão de rota expressa do Azure entre os sistemas de locais e Azure.


### <a name="remote-access-to-sap-systems"></a>Acesso remoto aos sistemas SAP

Com o Gerenciador de recursos do Azure há sem pontos de extremidade padrão mais como o modelo clássico antigo. Todas as portas de uma máquina virtual BRAÇO do Azure estão abertas desde que:

1. Nenhum grupo de segurança de rede está definido para a sub-rede ou da interface de rede. O tráfego de rede para o Azure VMs pode ser protegido por meio de chamados "grupos de segurança de rede". Para obter mais informações, consulte [o que é um grupo de segurança de rede (NSG)?][virtual-networks-nsg]
1. Nenhum balanceador de carga do Azure está definida para a interface de rede   
 
Consulte a diferença de arquitetura entre modelo clássico e ARM conforme descrito [neste]artigo[virtual-machines-azure-resource-manager-architecture].
 
#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Configuração da conectividade sistema SAP e interface usuário SAP para cenário somente na nuvem
Consulte este artigo que descreve detalhes este tópico: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx> 

#### <a name="changing-firewall-settings-within-vm"></a>Alterar as configurações de Firewall dentro de máquina virtual
Talvez seja necessário configurar o firewall nas máquinas virtuais para permitir o tráfego de entrada para seu sistema SAP. 

___

> ![Windows][Logo_Windows] Windows
>
> Por padrão, o Firewall do Windows dentro de uma VM implantada Azure está ativado. Agora, você precisa permitir a porta SAP para serem abertos, caso contrário, a interface de usuário do SAP não será capaz de se conectar.
> Para fazer isso:
>
>  * Controle abrir Panel\System e Security\Windows Firewall para 'Configurações avançadas'.
>  * Agora clique com botão direito em regras de entrada e escolher 'Nova regra'.
>  * No escolheu seguinte do Assistente para criar uma nova regra de 'Porta'.
>  * Na próxima etapa do assistente, deixe a configuração em TCP e digite o número da porta que você deseja abrir. Como nosso ID da instância SAP é 00, tivemos 3200. Se sua instância possui um número de instância diferente, a porta definida anteriormente com base no número instância deve ser aberta.
>  * Na próxima parte do assistente, você precisa deixar o item 'Permitir Conexão' marcada.
>  * Na próxima etapa do assistente, você precisa definir se a regra se aplica para rede de domínio, particular e público. Ajuste-se for necessário às suas necessidades. No entanto, se conectando com interface gráfica SAP de fora através da rede pública, você precisa ter a regra aplicada à rede pública.
>  * Na última etapa do assistente, você precisa dê um nome para a regra e salve a regra pressionando 'Concluir'
>
>  A regra entra em vigor imediatamente.
>
> ![Definição de regra de porta][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> As imagens de Linux do Azure Marketplace não habilite o firewall iptables por padrão e a conexão ao seu sistema SAP deve funcionar. Se você habilitou iptables ou outro firewall, consulte a documentação do iptables ou o firewall usado para permitir o tráfego de entrada tcp à porta 32xx (onde xx é o número do sistema de seu sistema SAP). 

___

#### <a name="security-recommendations"></a>Recomendações de segurança

Interface do usuário SAP não se conectam imediatamente a qualquer uma das instâncias do SAP (porta 32xx) qual estão executando, mas primeiro se conecta através da porta aberta para o processo de servidor de mensagens do SAP (porta 36xx). No passado muito mesma porta foi usada pelo servidor de mensagens para a comunicação interna para as instâncias do aplicativo. Para impedir que locais servidores de aplicativo de comunicar-se com um servidor de mensagem no Azure as portas de comunicação interna podem ser alteradas acidentalmente. É altamente recomendável para alterar a comunicação interna entre o servidor de mensagens do SAP e suas instâncias de aplicativo para um número de porta diferente em sistemas que tiver sido duplicado de sistemas locais, como um clonar de desenvolvimento etc de teste do projeto. Isso pode ser feito com o parâmetro de perfil padrão:

>   rdisp/msserv_internal

conforme documentadas nas: <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm> 

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Conceitos de implantação somente na nuvem de instâncias do SAP

### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Única máquina virtual com o SAP NetWeaver demonstração/treinamento cenário
 
![Executando sistemas de máquina virtual SAP demonstração simples com os mesmos nomes de máquina virtual, isolados em serviços de nuvem do Azure][planning-guide-figure-1700]

Neste cenário (consulte Capítulo [somente na nuvem] [planejamento-guia-2.1] deste documento) estamos implementando um cenário de sistema de treinamento/demonstração típico onde o cenário de treinamento/demonstração completo está contido em uma única VM. Vamos supor que a implantação é feita por meio de modelos de imagem de máquina virtual. Nós também presumem que múltiplo destas demonstração/treinamentos VMs precisam ser implantado com VMs com o mesmo nome.

Pressupõe-se que você criou uma imagem de máquina virtual, conforme descrito em algumas seções do capítulo [Preparando VMs com SAP para Azure] [planejamento-guia-5.2] neste documento.

A sequência de eventos para implementar o cenário tem esta aparência:

[comentário]: <>  (MShermannd TODO deve fornecer amostras ARM descrição usando o modelo de json + explicação sobre o nome de máquina virtual exclusivo dentro de uma rede virtual BRAÇO)   
##### <a name="powershell"></a>PowerShell

* Criar um novo grupo de recurso para cada cenário de treinamento/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* Criar uma nova conta de armazenamento

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada cenário de treinamento/demonstração habilitar o uso da mesma hostname e endereços IP. A rede virtual está protegida por um grupo de segurança de rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso de área de trabalho remota e 22 SSH. 

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que pode ser usado para acessar a máquina virtual da internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede da máquina virtual

```powershell 
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip 
```

* Crie uma máquina virtual. Para o cenário somente na nuvem a cada máquina virtual terá o mesmo nome. O SID SAP das instâncias do SAP NetWeaver nessas VMs será o mesmo assim. No grupo de recursos do Azure, o nome da máquina virtual deve ser exclusivo, mas em diferentes grupos de recursos do Azure você pode executar VMs com o mesmo nome. A conta de 'Administrador' padrão do Windows ou raiz para Linux não são válidos. Portanto, um novo nome de usuário de administrador precisa ser definido junto com uma senha. O tamanho da máquina virtual também precisa ser definido.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, adicione discos adicionais e restaurar conteúdo necessário. Lembre-se de que todos os nomes de blob (URLs para os blobs) devem ser exclusivos dentro do Azure.

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### <a name="cli"></a>CLI

O código de exemplo a seguir pode ser usado no Linux. Para Windows, por favor, use PowerShell conforme descrito acima ou adaptar o exemplo para usar % rgName % em vez de $rgName e defina a variável de ambiente usando o comando do Windows _Definir_.

* Criar um novo grupo de recurso para cada cenário de treinamento/demonstração

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* Criar uma nova conta de armazenamento

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* Crie uma nova rede virtual para cada cenário de treinamento/demonstração habilitar o uso da mesma hostname e endereços IP. A rede virtual está protegida por um grupo de segurança de rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso de área de trabalho remota e 22 SSH. 

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* Criar um novo endereço IP público que pode ser usado para acessar a máquina virtual da internet

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede da máquina virtual

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet 
```

* Crie uma máquina virtual. Para o cenário somente na nuvem a cada máquina virtual terá o mesmo nome. O SID SAP das instâncias do SAP NetWeaver nessas VMs será o mesmo assim. No grupo de recursos do Azure, o nome da máquina virtual deve ser exclusivo, mas em diferentes grupos de recursos do Azure você pode executar VMs com o mesmo nome. A conta de 'Administrador' padrão do Windows ou raiz para Linux não são válidos. Portanto, um novo nome de usuário de administrador precisa ser definido junto com uma senha. O tamanho da máquina virtual também precisa ser definido.

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* Opcionalmente, adicione discos adicionais e restaurar conteúdo necessário. Lembre-se de que todos os nomes de blob (URLs para os blobs) devem ser exclusivos dentro do Azure.

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### <a name="template"></a>Modelo
Você pode usar os modelos de exemplo no repositório de modelos de início rápido do azure no github.

* [Máquina virtual de Linux simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Máquina virtual do Windows simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Máquina virtual da imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementar um conjunto de VMs que precisam se comunicar dentro do Azure
Esse cenário somente na nuvem é um cenário típico para treinamento e demonstração fins onde o software que representa o demonstração/treinamento cenário estiver distribuído ao longo de várias VMs. Os diferentes componentes instalados em VMs diferentes precisam se comunicar com os outros. Novamente, neste cenário sem local comunicação de rede ou cenário cruzado local é necessária.

Este cenário é uma extensão da instalação descrita no capítulo [única máquina virtual com o SAP NetWeaver demonstração/treinamento cenário] [planejamento-guia-7.1] deste documento. Nesse caso mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo a seguir o cenário de treinamento consiste em uma VM de ASCS/SCS SAP, uma máquina virtual executando um DBMS e uma instância do servidor de aplicativos do SAP máquina virtual.

Antes de criar esse cenário, você precisa pensar sobre configurações básicas de como já realizadas no cenário antes.

#### <a name="resource-group-and-virtual-machine-naming"></a>Grupo de recursos e máquina Virtual de nomenclatura
Todos os nomes de grupo de recursos devem ser exclusivos. Desenvolver seu próprio esquema de nomenclatura de seus recursos, tais como `<rg-name`>-sufixo. 

Nome da máquina virtual deve ser exclusivo dentro do grupo de recursos. 

#### <a name="setup-network-for-communication-between-the-different-vms"></a>Configuração de rede para a comunicação entre as diferentes VMs
 
![Conjunto de VMs dentro de uma rede Virtual Azure][planning-guide-figure-1900]

Para evitar conflitos com clones dos mesmos cenários de treinamento/demonstração de nomes, você precisa criar uma rede Virtual do Azure para cada cenário. Resolução de nomes DNS será fornecida pelo Azure ou você pode configurar seu próprio servidor DNS fora do Azure (não a ainda mais ser discutido aqui). Neste cenário não podemos configurar nossas própria DNS. Para todas as máquinas virtuais dentro de uma rede Virtual do Azure comunicação via nomes de host será habilitada. 

Os motivos para separar cenários de treinamento ou demonstração por redes virtuais e não apenas os grupos de recursos podem ser:

* Precisa de cenário SAP como configurar o seu próprio AD/OpenLDAP e um servidor de domínio precisa fazer parte de cada um dos cenários.  
* O cenário SAP como configurar tem componentes que precisam trabalhar com endereços IP fixos.

Mais detalhes sobre redes virtuais do Azure e como defini-los podem ser encontradas [neste]artigo[virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implantando VMs SAP com conectividade de rede corporativa (entre locais)

Executar um cenário SAP e deseja dividir a implantação entre depender de ponta servidores DBMS, ambientes de locais virtualizados para camadas de aplicativo e menor 2 camadas configurado sistemas SAP e Azure IaaS. Base pressupõe-se que os sistemas SAP em um cenário SAP precisam se comunicar com os outros e muitos outros componentes de software implantados na empresa, independentemente de sua forma de implantação. Também deve haver sem diferenças introduzidas pelo formulário implantação para o usuário final se conectar com a interface do SAP ou outras interfaces. Essas condições só podem ser atendidas quando temos a locais Active Directory/OpenLDAP e serviços DNS estendidos para os sistemas Azure por meio de conectividade de site-para-site/vários sites ou conexões privadas como rota expressa do Azure.

Para obter mais explicações sobre os detalhes da implementação do SAP no Azure, recomendamos que você leia capítulo [implantação conceitos de Cloud-Only de instâncias do SAP] [planejamento-guia-7] deste documento que explica algumas das construções Noções básicas do Azure e como eles devem ser usados com aplicativos do SAP no Azure.

### <a name="scenario-of-a-sap-landscape"></a>Cenário de um cenário SAP

O cenário de cruz local pode ser descrito aproximadamente como nos gráficos abaixo:
 
![Conectividade de-to-Site entre locais e ativos do Azure][planning-guide-figure-2100]

O cenário mostrado acima descreve um cenário onde o local AD/OpenLDAP e DNS é estendido para Azure. No lado local, um determinado intervalo de endereços IP é reservado por assinatura Azure. O intervalo de endereços IP será atribuído a uma rede Virtual do Azure no lado do Azure.

#### <a name="security-considerations"></a>Considerações de segurança

O requisito mínimo é o uso de protocolos de comunicação segura como SSL/TLS para acesso a navegador ou conexões baseadas em VPN para acesso de sistema para os serviços do Azure. Pressupõe-se que empresas lidar com a conexão VPN entre sua rede corporativa e Azure muito diferente. Algumas empresas podem abrir um olhar vazio todas as portas. Algumas outras empresas talvez queira ser muito precisas em quais portas precisam abrir, etc. 

Na tabela abaixo SAP típico portas de comunicação são listadas. Basicamente é suficiente para abrir a porta de gateway do SAP.

| Serviço | Nome da porta | Exemplo `<nn`> = 01 | Intervalo de padrão (Mín-Máx) | Comentário |
|---------|-----------|-------------------|-------------------------|---------|
| Distribuidor | sapdp`<nn>` consulte * | 3201 | 3200 – 3299 | Distribuidor SAP, usado pelo SAP interface gráfica para Windows e Java |
| Servidor de mensagens | sapms`<sid`> ver * * | 3600 | sapms livre`<anySID`> | SID = ID de sistema SAP |
| Gateway | sapgw`<nn`> ver * | 3301 | livre | Gateway do SAP, usado para comunicação CPIC e RFC |
| Roteador SAP | sapdp99 | 3299 | livre | Apenas os nomes de serviço CI (instância central) podem ser reatribuídos no /etc/Services. para um valor aleatório após a instalação. |

*) nn = número de instância do SAP

*) sid = ID de sistema SAP

Informações mais detalhadas sobre portas necessárias para diferentes produtos SAP ou serviços por produtos SAP podem ser encontrados aqui <http://scn.sap.com/docs/DOC-17124>. Com este documento você deve ser capaz de abrir portas dedicadas no dispositivo VPN necessário para cenários e produtos específicos do SAP.

Medidas de outra segurança quando implantar VMs em tal cenário poderia ser criar um [Grupo de segurança de rede] [ virtual-networks-nsg] para definir regras de acesso.

### <a name="dealing-with-different-virtual-machine-series"></a>Lidando com diferentes séries de máquina Virtual

Durante a últimos 12 meses Microsoft adicionado muitos mais tipos de máquina virtual que diferem em número de vCPUs, memória ou mais importante em hardware que ele é executado em. Nem todas essas VMs são compatíveis com o SAP (consulte suportada a tipos de máquina virtual na anotação SAP [1928533]). Alguns dessas VMs executam em gerações de hardware do host diferente. Estas gerações de hardware do host são obtendo implantadas no detalhamento de uma unidade de escala Azure. Casos significa que podem surgir onde os diferentes tamanhos de máquina virtual escolhido não podem ser executados a mesma unidade de escala. Um conjunto de disponibilidade é limitado a capacidade de abranger unidades de escala com base de hardware diferente.  Por exemplo Se você quiser executar o DBMS em A5 A11 VMs e a camada de aplicativo SAP em série G VMs, você seria forçado a implantar um único sistema SAP ou sistemas SAP diferentes em diferentes conjuntos de disponibilidade.


#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimindo em uma impressora de rede local da instância do SAP no Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Imprimindo em TCP/IP cenário entre locais


Configurar suas impressoras de rede local TCP/IP com base em uma máquina virtual do Azure geral é iguais aos sua rede corporativa, supondo que você tenha um túnel VPN Site-To-Site ou conexão de rota expressa estabelecida. 

___

> ![Windows][Logo_Windows] Windows
>
> Para fazer isso:
> - Algumas impressoras de rede vêm com um Assistente de configuração que torna mais fácil configurar sua impressora em uma máquina virtual do Azure. Se nenhum software assistente foi distribuído com a impressora a maneira "manual" para configurar a impressora é criar uma nova porta de impressora TCP/IP.
> - Abra o painel de controle -> dispositivos e impressoras -> Adicionar uma impressora 
> - Escolha adicionar uma impressora usando um endereço de TCP/IP ou o nome do host
> - Digite o endereço IP da impressora
> - Porta de impressora padrão 9100
> - Se for necessário instale manualmente o driver de impressora apropriado. 
> 
> ![Linux][Logo_Linux] Linux
>
> - o procedimento padrão para instalar uma impressora de rede, como para acompanhamento apenas do Windows
> - Siga as guias de Linux públicas para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sobre como adicionar uma impressora.

___

 
![Impressão de rede][planning-guide-figure-2200]



##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Baseada em host impressora sobre SMB (impressora compartilhada) cenário entre locais

Impressoras baseadas em host não são compatíveis rede por design. Mas uma impressora com base em host pode ser compartilhada entre computadores em uma rede, desde que a impressora está conectada a um computador ligado. Conecte seu corporativo-To-Site ou rota expressa de rede e compartilhe sua impressora local. O protocolo SMB usa NetBIOS em vez de DNS como serviço de nome. O nome de host NetBIOS pode ser diferente do nome do host DNS. Caso padrão é que o nome de host NetBIOS e o nome de host DNS são idênticos. O domínio DNS não faz sentido no espaço para nome NetBIOS. Portanto, o nome de host DNS totalmente qualificado consiste no nome de host DNS e o domínio DNS não deve ser usado no espaço para nome NetBIOS.

O compartilhamento da impressora é identificado por um nome exclusivo na rede:

* Nome do host do host SMB (sempre necessário). 
* Nome do compartilhamento (sempre necessário). 
* Nome do domínio se impressora compartilhar não está no mesmo domínio do sistema SAP. 
* Além disso, um nome de usuário e uma senha podem ser necessário para acessar o compartilhamento de impressora.

Como:

___

> ![Windows][Logo_Windows] Windows
>
> Compartilhe sua impressora local.
> Na máquina virtual do Azure, abra o Windows Explorer e digite o nome de compartilhamento da impressora.
> Um Assistente de instalação de impressora o guiará pelo processo de instalação.
>
> ![Linux][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre configuração de impressoras de rede no Linux ou incluindo um capítulo sobre impressão no Linux. Ele funcionam da mesma maneira uma máquina virtual Linux do Azure desde a máquina virtual faz parte de uma VPN:
>
> * SLES <_Share_or_Windows_Share https://en.opensuse.org/SDB:Printing_via_SMB_ (Samba)>
> * RHEL <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>

___


##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (encaminhamento de impressora) 

A capacidade dos serviços de área de trabalho remota para fornecer aos usuários acesso aos seus dispositivos de impressora local em uma sessão remota não está disponível no Azure.

___

> ![Windows][Logo_Windows] Windows
>
> Mais detalhes sobre como imprimir com Windows podem ser encontradas aqui: <http://technet.microsoft.com/library/jj590748.aspx>.

___

 
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração do SAP Azure sistemas em correção e sistema de transporte (TMS) entre locais

A alteração do SAP e o sistema de transporte (TMS) deve ser configurado para exportar e importar solicitação de transporte em sistemas no cenário. Vamos supor que as instâncias de desenvolvimento de um sistema SAP (DES) estão localizadas no Azure enquanto os sistemas de produtivos (PRD) e qualidade (QA) são locais. Além disso, vamos supor que não há um diretório de transporte central.

##### <a name="configuring-the-transport-domain"></a>Configurando o domínio de transporte
Configure seu domínio de transporte no sistema que você designado como o controlador de domínio de transporte conforme descrito em [Configurando o controlador de domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Um usuário de sistema que TMSADM será criado e o destino RFC necessário serão gerados. Você pode verificar essas conexões RFC usando a SM59 da transação. Resolução de nome do host deve estar habilitada no seu domínio de transporte. 

Como:

* Em nosso cenário podemos decidiu que o sistema QAS de local será o controlador de domínio CTS. Chame transação STMS. A caixa de diálogo TMS aparece. Uma caixa de diálogo Configurar domínio de transporte é exibida. (Esta caixa de diálogo só aparece se você ainda não tiver configurado um domínio de transporte.)
* Certifique-se de que o usuário criado automaticamente TMSADM está autorizado (SM59 -> Conexão ABAP -> TMSADM@E61.DOMAIN_E61 -> detalhes -> Utilities(M) -> teste de autorização). A tela inicial da transação STMS deve mostrar o que este sistema SAP agora está funcionando como o controlador de domínio de transporte, como mostrado aqui:
 
![Tela inicial da transação STMS no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte

A sequência de incluir um sistema SAP em um domínio de transporte tem a seguinte aparência:

* No sistema de desenvolvimento no Azure acesse o sistema de transporte (cliente 000) e transação STMS de chamadas. Escolha outra configuração na caixa de diálogo e continue com sistema incluir no domínio. Especifica o controlador de domínio como o host de destino ([Incluindo sistemas SAP no domínio de transporte](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema agora está aguardando para ser incluído no domínio de transporte.
* Por razões de segurança, em seguida, você precisa voltar para o controlador de domínio para confirmar sua solicitação. Escolha visão geral do sistema e aprovar do sistema em espera. Confirme o aviso e a configuração serão distribuídos.

Este sistema SAP agora contém as informações necessárias sobre todos os outros sistemas SAP no domínio de transporte. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados a todos os outros sistemas SAP e o sistema SAP é inserido no perfil de transporte do programa de controle de transporte. Verifique se trabalham RFCs e acesso ao diretório de transporte do domínio.

Continue com a configuração do seu sistema de transporte como de costume conforme descrito na documentação [alteração e sistema de transporte](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Como:

* Verifique se que seu STMS local está configurado corretamente.
* Verifique se que o nome do host do controlador de domínio de transporte pode ser resolvido por sua máquina virtual no Azure e vice visto.
* Chamada transação STMS -> Configuração de outros -> sistema incluir no domínio.
* Confirme a conexão do sistema TMS local ativado.
* Configure rotas de transporte, grupos e camadas como de costume.

Em cenários de cruz local conectados-to-site, a latência entre locais e Azure ainda pode ser substancial. Se nós siga a sequência de transporte objetos através de desenvolvimento e testar sistemas de produção ou pensar sobre como aplicar transportes ou pacotes para os sistemas diferentes de suporte, você percebe que, o local do diretório de transporte central dependente, alguns dos sistemas encontrarão alta latência ler ou gravar dados no diretório de transporte central. A situação é semelhante a configurações de paisagem SAP onde os sistemas diferentes são distribuídos por meio dos centros de dados diferentes com substancial distância entre os centros de dados.

Para contornar esse latência e ter os sistemas trabalhar rapidamente no ler ou gravar para ou a partir do diretório de transporte, você pode configurar dois STMS domínios de transporte (uma para locais e outra com os sistemas no Azure e vincular os domínios de transporte. Verifique esta documentação que explica os princípios atrás esse conceito em ao TMS SAP: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>. 

Como:

* Configurar um domínio de transporte em cada local (local e Azure) usando transação STMS <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Vincular os domínios com um link de domínio e confirme o vínculo entre os dois domínios. 
  <http://help.SAP.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/Content.htm>
* Distribua a configuração para o sistema vinculada.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego RFC entre instâncias SAP localizada no Azure e local (entre locais)

Tráfego RFC entre sistemas que são locais e no Azure precisa trabalhar. Configurar uma transação de chamada de conexão SM59 em um sistema de origem em que você precisa definir uma conexão de RFC na direção do sistema de destino. A configuração é semelhante à configuração padrão de uma Conexão de RFC.

Vamos supor que no cenário entre locais, VMs que executar sistemas SAP que precisam se comunicar com os outros estão no mesmo domínio. Portanto, a configuração de uma conexão de RFC entre sistemas SAP não difere as etapas de configuração e entradas em cenários de local.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Ao acessar compartilhamentos de arquivos 'locais' de instâncias do SAP localizados no Azure ou vice-versa

Instâncias SAP localizadas no Azure precisam acessar compartilhamentos de arquivos que estão dentro das instalações corporativas. Além disso, instâncias SAP local precisam acessar compartilhamentos de arquivos que estão localizados no Azure. Para habilitar os compartilhamentos de arquivos, você deve configurar as permissões e opções de compartilhamento no sistema local. Verifique se você abrir as portas a conexão VPN ou rota expressa entre Azure e data center.

## <a name="supportability"></a>Suporte
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Solução de monitoramento para SAP do Azure
Para habilitar o monitoramento de sistemas de missão crítica SAP no Azure o SAP ferramentas SAPOSCOL ou SAP Host Agent obter dados de fora do host de serviço de máquina Virtual do Azure por meio de uma extensão de monitoramento do Azure para SAP de monitoramento. Como as exigências pela SAP eram muito específicas para aplicativos do SAP, Microsoft decidiu não forma genérica implementar a funcionalidade necessária para o Azure, mas deixá-lo para clientes implantar as configurações e componentes de monitoramento necessários em suas máquinas virtuais em execução no Azure. No entanto, gerenciamento de ciclo de vida e implantação os componentes de monitoramento será automatizado principalmente por Azure.

#### <a name="solution-design"></a>Design de solução

A solução desenvolvida para permitir o monitoramento do SAP baseia-se na arquitetura do agente de máquina virtual do Azure e framework de extensão. A ideia do framework agente de máquina virtual do Azure e extensão é permitir a instalação de aplicativos de software disponíveis na Galeria de extensão de máquina virtual do Azure dentro de uma máquina virtual. A ideia de princípio por trás esse conceito é permitir (em casos como a extensão de monitoramento do Azure para SAP), a implantação de funcionalidade especial em uma máquina virtual e a configuração de tal software no momento da implantação. 

Desde fevereiro de 2014, 'Azure máquina virtual agente' que permite manipulação de extensões específicas de máquina virtual do Azure dentro a máquina virtual é injetado Windows VMs por padrão na criação de máquina virtual no Portal do Azure. No caso de SUSE ou Red Hat Linux a máquina virtual agente já é parte da imagem Azure Marketplace. No caso de um seria carregar uma Linux VM do local para Azure o agente de máquina virtual deve ser instalado manualmente.


Blocos de construção básicos da solução monitoramento no Azure para SAP aparências assim:
 
![Componentes de extensão do Microsoft Azure][planning-guide-figure-2400]

Como mostrado no diagrama de bloco acima, uma parte da solução de monitoração para SAP é hospedada na imagem de máquina virtual do Azure e a Galeria de extensão do Azure que é um repositório replicado globalmente gerenciado por operações do Azure. É responsabilidade da equipe do SAP/MS joint trabalhando a implementação Azure do SAP para trabalhar com operações do Azure para publicar novas versões da extensão de monitoramento do Azure para SAP. Essa extensão de monitoramento do Azure para SAP usará a extensão do Microsoft Azure diagnóstico (WAD) ou o diagnóstico de Azure Linux (LAD) para obter as informações necessárias. 

Quando você implanta uma nova VM do Windows, o 'Agente de máquina virtual do Azure' é adicionado automaticamente para a máquina virtual. A função do agente é coordenar o carregamento e configuração das extensões do Azure para monitoramento de sistemas do SAP NetWeaver. Para Linux VMs o agente de máquina virtual do Azure já é parte da imagem do sistema operacional do Azure Marketplace.

No entanto, há uma etapa que ainda deve ser executado pelo cliente. Esta é a habilitação e configuração do conjunto de desempenho. O processo relacionado à 'configuração' é automatizado por um comando CLI ou script do PowerShell. O script do PowerShell pode ser baixado no Microsoft Azure Script Center conforme descrito em [guia de implantação] [-guia de implantação].

A arquitetura geral da solução de monitoração Azure para SAP aparência:
 
![Solução de monitoramento para SAP NetWeaver do Azure][planning-guide-figure-2500]

**Para ver as instruções exatas e para obter etapas detalhadas de como usar esses cmdlets do PowerShell ou comando CLI durante implantações, siga as instruções fornecidas em [guia de implantação] [-guia de implantação].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração do Azure localizados instância do SAP em SAProuter

Instâncias SAP em execução no Azure precisam estar acessíveis a partir do SAProuter também.
 
![Conexão de rede do roteador SAP][planning-guide-figure-2600]

Um SAProuter habilita a comunicação de TCP/IP entre sistemas participantes se não houver nenhuma conexão IP direta. Isso fornece a vantagem que nenhuma conexão de ponta a ponta entre os parceiros de comunicação é necessária no nível de rede. O SAProuter é ouvir na porta 3299 por padrão.
Para se conectar a instâncias SAP por meio de uma SAProuter você precisa dar o nome de host e de cadeia de caracteres de SAProuter com qualquer tentativa de se conectar.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver como Java

Até agora o foco do documento foi SAP NetWeaver em geral ou o SAP NetWeaver ABAP pilha. Nesta seção pequeno, as considerações específicas para pilha SAP Java são listadas. Um dos aplicativos da SAP NetWeaver Java exclusivamente com base mais importantes é o SAP Enterprise Portal. Outros SAP NetWeaver aplicativos baseados em como PI do SAP e SAP solução Manager usam o SAP NetWeaver ABAP tanto pilhas de Java. Portanto, certamente é necessário considerar aspectos específicos relacionados à pilha de SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>Portal de empresarial do SAP

A configuração de um Portal de SAP em uma máquina Virtual do Azure não difere uma instalação local ativado se você estiver implantando em cenários entre locais. Como o DNS é feito por local, as configurações de porta das instâncias individuais podem ser feitas como local configurado. As recomendações e as restrições descritas neste documento até aqui se aplicam para um aplicativo como o SAP Enterprise Portal ou a pilha de SAP NetWeaver Java em geral. 

![Portal SAP expostos][planning-guide-figure-2700]

Um cenário de implantação especial por alguns clientes é a exposição direta do Portal Empresarial do SAP à Internet enquanto o host de máquina virtual está conectado à rede da empresa por meio de túnel VPN-to-site ou rota expressa. Para esse cenário, você precisa certificar-se de que portas específicas estão abertas e não bloqueada por grupo de segurança do firewall ou rede. A mesma mecânica precisarão ser aplicada quando você deseja conectar a uma instância do SAP Java no local em um cenário de somente na nuvem.

O portal inicial URI é http (s):`<Portalserver`>: 5XX00/irj onde a porta é formada por 50000 plus (Systemnumber x 100). O sistema de URI do SAP portal padrão 00 é `<dns name`>. `<azure region`>.Cloudapp.azure.com:PublicPort/irj. Para obter mais detalhes, dê uma olhada um <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>. 
 
![Configuração de ponto de extremidade][planning-guide-figure-2800]

Se você quiser personalizar o URL e/ou portas do Portal Empresarial SAP, verifique esta documentação:

* [Alterar URL do Portal](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL) 
* [Alterar números de porta padrão, números de porta de Portal](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers) 


## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Alta disponibilidade (HA) e a recuperação de desastres (DR) para SAP NetWeaver em execução em máquinas virtuais do Azure
### <a name="definition-of-terminologies"></a>Definição de terminologias

O termo **alta disponibilidade (HA)** geralmente está relacionada a um conjunto de tecnologias que minimiza interrupções de IT, fornecendo continuidade de negócios de serviços de TI por meio de tolerância, redundantes ou failover protegida componentes dentro do **mesmo** data center. Em nosso caso, dentro de uma região do Azure.

**Recuperação de dados (DR)** também é direcionamento minimizar as interrupções de serviços IT e a recuperação de dados, mas entre dados **diferentes** centros, que costumam ser localizados centenas de quilômetros ausente. Em nosso caso geralmente entre diferentes áreas do Azure dentro da mesma região geopolíticas ou como estabelecida por você como um cliente.

### <a name="overview-of-high-availability"></a>Visão geral de alta disponibilidade
Nós pode separar a discussão sobre SAP alta disponibilidade no Azure em duas partes:

* **Alta disponibilidade de infraestrutura azure**, por exemplo, HA de computação (VMs), rede, armazenamento etc e seus benefícios para aumentar a disponibilidade de aplicativo do SAP.
* **Alta disponibilidade de aplicativos SAP**, por exemplo, os componentes de software do HA da SAP:
    * Servidores de aplicativo do SAP
    * Instância do SAP ASCS/SCS 
    * Servidor de banco de dados

e como ele pode ser combinado com infraestrutura Azure HA.

SAP alta disponibilidade no Azure tem algumas diferenças em comparação com o SAP alta disponibilidade em um ambiente físico ou virtual do local. O seguinte documento do SAP descreve as configurações padrão do SAP alta disponibilidade em ambientes virtualizados no Windows: <http://scn.sap.com/docs/DOC-44415>. Não há nenhuma integrada sapinst SAP HA configuração para Linux como existir para Windows. Sobre o SAP HA local para Linux encontrar mais informações aqui: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Infraestrutura Azure alta disponibilidade
Não há nenhum SLA único-máquina virtual disponível em máquinas virtuais do Azure agora. Para obter uma ideia de como a disponibilidade de uma única VM pode parecer você pode simplesmente criar o produto dos diferentes SLAs disponíveis do Azure: <https://azure.microsoft.com/support/legal/sla/>.

Base para o cálculo é 30 dias por mês ou 43200 minutos. Portanto, o tempo de inatividade de 0,05% corresponde à 21.6 minutos. Normalmente, a disponibilidade de diferentes serviços será multiplique da seguinte maneira:

(Serviço de disponibilidade #1/100) *(Serviço de disponibilidade #2/100)* (Serviço de disponibilidade #3/100) *...

Como:

(99,95/100) *(99,9/100)* (99,9/100) = 0.9975 ou uma disponibilidade geral de 99.75%.

#### <a name="virtual-machine-vm-high-availability"></a>Alta disponibilidade de máquina virtual (VM)

Há dois tipos de eventos de plataforma Windows Azure que podem afetar a disponibilidade de suas máquinas virtuais: planejado manutenção planejada e a manutenção.

* Eventos de manutenção planejada são periódicas atualizações feitas pela Microsoft para a plataforma Windows Azure subjacente para melhorar a confiabilidade, desempenho e segurança da infraestrutura plataforma suas máquinas virtuais executados em geral.
* Eventos de manutenção planejadas ocorrem quando o hardware ou infraestrutura física subjacente sua máquina virtual tem com defeito de alguma maneira. Isso pode incluir falhas de rede local, falhas de disco local ou outras falhas de nível de rack. Quando essa falha for detectada, a plataforma Windows Azure será migrar automaticamente sua máquina virtual do servidor físico não íntegro sua máquina virtual a um servidor físico íntegra de hospedagem. Tais eventos são raros, mas também podem causar sua máquina virtual reinicializar.

Mais detalhes podem ser encontrados nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redundância de armazenamento do Azure

Os dados em sua conta de armazenamento do Microsoft Azure é sempre replicados para garantir durabilidade e alta disponibilidade, o SLA de armazenamento do Azure mesmo diante de falhas de hardware temporárias de reunião

Como o armazenamento do Azure é manter 3 imagens dos dados por padrão, RAID5 ou RAID1 em vários discos Azure não são necessárias.

Mais detalhes podem ser encontrados neste artigo: <http://azure.microsoft.com/documentation/articles/storage-redundancy/> 

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilizando reiniciar de máquina virtual do Azure infraestrutura para obter "Maior disponibilidade" dos aplicativos SAP

Se você decidir não usar funcionalidades como cluster de Failover do Windows Server (WSFC) ou um Linux equivalente (o um ainda não é suportado no Azure em combinação com software SAP último), reinicie o Azure máquina virtual é utilizado para proteger um sistema SAP contra tempo de inatividade planejado e da infraestrutura de servidor físico Azure e geral plataforma Windows Azure subjacente. 
 
> [AZURE.NOTE] É importante mencionar que principalmente reiniciar de máquina virtual do Azure protege VMs e não aplicativos. Reinicie o máquina virtual não oferece alta disponibilidade para aplicativos SAP, mas ela oferece um determinado nível de disponibilidade de infraestrutura e, portanto, indiretamente "maior disponibilidade" de sistemas SAP. Também não há nenhuma SLA para o tempo levará para reiniciar uma máquina virtual após uma interrupção de host planejadas ou não. Portanto, esse método de 'alta disponibilidade' não é adequado para componentes críticos de um sistema SAP como (A) SCS ou DBMS.

Outro elemento de infraestrutura importantes para alta disponibilidade é armazenamento. Por exemplo Azure SLA de armazenamento é disponibilidade do 99,9%. Se uma implanta todas VMs com seus discos em uma única conta de armazenamento do Azure, armazenamento Azure potencial indisponibilidade causará indisponibilidade de todas as VMs que são colocadas nessa conta de armazenamento do Azure e também todos os componentes SAP executando dentro essas VMs.  

Em vez de colocar todas as VMs para uma única conta de armazenamento do Azure, você também pode usar o armazenamento dedicado contas para cada máquina virtual e dessa maneira aumentam a disponibilidade geral do aplicativo de máquina virtual e SAP usando várias contas de armazenamento do Azure independentes. 

Uma arquitetura de exemplo de um sistema SAP NetWeaver que usa infraestrutura Azure HA pode ter esta aparência:
 
![Utilizando a infraestrutura Azure HA para atingir "maior" de disponibilidade de aplicativo do SAP][planning-guide-figure-2900]

Para componentes SAP críticos podemos obtida procedimentos até o momento:

* Alta disponibilidade dos servidores de aplicativo SAP (AS)

Instâncias de servidor de aplicativo SAP são componentes redundantes. Cada SAP como instância é implantada em sua própria máquina virtual, que está executando em um local diferente, falhas do Azure e atualização do domínio (consulte capítulos [domínios de falha] [planejamento-guia-3.2.1] e [Domains][planning-guide-3.2.2]) atualizar. Isso é verificado usando conjuntos de disponibilidade do Azure (consulte Capítulo [Sets][planning-guide-3.2.3]) de disponibilidade do Azure. Possível indisponibilidade planejada ou de um domínio de atualização ou falha do Azure causará indisponibilidade de um número restrito de VMs com seus SAP instâncias. Cada SAP como instância é colocada em sua própria conta de armazenamento do Azure – indisponibilidade possível de uma conta de armazenamento do Azure causará indisponibilidade da máquina virtual apenas uma com seus SAP instância. No entanto, lembre-se de que não há um limite de contas de armazenamento do Azure dentro de uma assinatura do Azure. Para garantir o início automático da instância (A) SCS após a reinicialização de máquina virtual, certifique-se de configurar parâmetro de iniciar automaticamente no (A) perfil de início de instância SCS descrito no capítulo [usando iniciar automaticamente para instâncias SAP] [planejamento-guia-11.5].
Leia também o capítulo [alta disponibilidade para servidores de aplicativo SAP] [planejamento-guia-11.4.1] para obter mais detalhes.

* _Superior_ Disponibilidade da instância SCS SAP (A)
 
Aqui utilizamos reiniciar de máquina virtual do Azure para proteger a máquina virtual com instância SCS SAP (A) instalada. No caso de tempo de inatividade planejado ou do Azure servidores, VMs será reiniciado em outro servidor disponível. Conforme mencionado anteriormente, principalmente reiniciar de máquina virtual do Azure protege VMs e não aplicativos, nesta instância do SCS caso o (A). Por meio de reiniciar a máquina virtual será chegarmos indiretamente "maior disponibilidade" da instância SCS SAP (A). Para garantir o início automático da instância (A) SCS após a reinicialização de máquina virtual, certifique-se de definir o parâmetro de iniciar automaticamente no (A) perfil de início de instância SCS descrito no capítulo [usando iniciar automaticamente para instâncias SAP] [planejamento-guia-11.5]. Isso significa que o SCS (A) instância como um ponto de falha único (SPOF) executado em uma única VM será o fator determinative a disponibilidade do cenário SAP inteiro. 

* _Superior_ Disponibilidade de servidor DBMS

Aqui, semelhante ao caso de uso de instância SCS SAP (A), utilizamos o Azure máquina virtual reiniciar para proteger a máquina virtual com software DBMS instalado, e podemos alcançar "maior disponibilidade" softwares de DBMS por meio de máquina virtual reiniciar. DBMS executar uma máquina virtual única também é uma SPOF e é o fator de determinative para a disponibilidade do cenário SAP inteiro. 

### <a name="sap-application-high-availability-on-azure-iaas"></a>SAP alta disponibilidade de aplicativos no Azure IaaS
Para obter completo SAP sistema alta disponibilidade, precisamos proteger todos os componentes críticos de sistema SAP, por exemplo, redundantes SAP servidores de aplicativos, e componentes exclusivos (por exemplo, ponto de falha único) como instância SCS SAP (A) e DBMS. 

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidade para servidores de aplicativo do SAP
Para as instâncias de servidores/caixa de diálogo de aplicativo SAP não é necessário pensar sobre uma solução específica de alta disponibilidade. Alta disponibilidade é simplesmente obtida por redundância e, portanto, ter suficientes em máquinas virtuais diferentes. Eles devem todos ser colocados no mesmo Azure disponibilidade conjunto para evitar que as VMs podem ser atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planejada. A funcionalidade básica que cria em diferentes atualização e domínios de falha em uma unidade de escala Azure já foi introduzida em capítulo [atualizar domínios] [planejamento-guia-3.2.2]. Conjuntos de disponibilidade Azure apresentados no capítulo [Azure disponibilidade conjuntos] [planejamento-guia-3.2.3] deste documento. 

Há um número infinito de falhas e domínios de atualização que podem ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure. Isso significa que colocar um número de VMs em um conjunto de disponibilidade mais cedo ou mais tarde no fato de que mais de uma VM acaba na mesma falha ou atualização do domínio

Implantando várias instâncias do servidor de aplicativo SAP em suas VMs dedicadas e presumindo que obtivemos 5 atualizar domínios, a imagem a seguir surge no final. O número máximo real de falhas e atualizar domínios dentro de um conjunto de disponibilidade pode mudar no futuro:
 
![HA SAP servidores de aplicativos no Azure][planning-guide-figure-3000]

Mais detalhes podem ser encontrados nesta documentação: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>


#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Alta disponibilidade para a instância SCS SAP (A) no Windows

Windows Server Failover Cluster (WSFC) é uma solução usada com frequência para proteger a instância SCS SAP (A). Ele também é integrado ao sapinst no formulário de uma "instalação de HA". Neste momento infraestrutura do Azure não é capaz de fornecer a funcionalidade para configurar o Cluster de Failover do Windows Server necessária da mesma maneira como tem feito no local.

A partir de janeiro de 2016 a plataforma de nuvem Azure executando o sistema operacional Windows não oferece a possibilidade de usar um volume cluster compartilhado em um disco compartilhado entre duas VMs do Azure.

Uma solução válida apesar é o uso de software de terceiros 3º que fornece um volume compartilhado por replicação de disco síncrono e transparente que pode ser integrada em WSFC. Essa abordagem significa que apenas o nó de cluster ativo é possível acessar uma das cópias disco em um ponto no tempo. A partir de janeiro de 2016 este HA configuração é suportada para proteger a instância SCS SAP (A) no Windows SO convidado em VMs do Azure em combinação com software de terceiros 3º SIOS DataKeeper.

A solução de SIOS DataKeeper fornece um recurso de cluster do disco compartilhado para Clusters de Failover do Windows fazendo:

* Um VHD Azure adicionais anexados a cada uma das máquinas virtuais (VMs) que estão em uma configuração de Cluster do Windows
* SIOS DataKeeper Cluster Edition em execução em ambos os nós de máquina virtual
* Tendo SIOS DataKeeper Cluster Edition configurado da maneira que ele sincronia reflete o conteúdo do VHD adicional anexado volume de fonte VMs adicionais VHD anexado volume de destino máquina virtual.
* SIOS DataKeeper é abstraindo os volumes de local de origem e destino e apresentá-los ao Cluster de Failover do Windows como um único disco compartilhado.
 
Você pode encontrar todos os detalhes sobre como instalar um Cluster de Failover do Windows com SIOS Datakeeper e SAP no [cluster instância do SAP ASCS usando Cluster de Failover do Windows Server no Azure com SIOS DataKeeper] [ ha-guide-classic] white paper. 

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Alta disponibilidade para a instância SCS SAP (A) no Linux
 
A partir de dezembro de 2015 também não há nenhum equivalente para disco compartilhado WSFC para Linux VMs no Azure. Soluções alternativas usando software de terceiros 3º como SIOS para Windows não é validado ainda para execução SAP em Linux no Azure.



#### <a name="high-availability-for-the-sap-database-instance"></a>Alta disponibilidade para a instância de banco de dados do SAP
A SAP DBMS HA configuração típica baseia-se em duas VMs DBMS onde a funcionalidade de alta disponibilidade DBMS é usada para duplicar dados da instância DBMS ativa para a segunda máquina de virtual em uma instância DBMS passiva.

Funcionalidade de recuperação de desastres e alta disponibilidade a para DBMS em geral, assim como específicas DBMS são descritas no [DBMS Deployment Guide] [dbms-guia].


#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidade de ponta a ponta para o sistema SAP concluída

Aqui estão dois exemplos de um SAP NetWeaver HA arquitetura completa no Azure - uma para Windows e outra para Linux.
Os conceitos conforme explicado abaixo talvez precise ser comprometida um pouco quando você implantar muitos sistemas SAP e o número de VMs implantadas é exceder o limite máximo de contas de armazenamento por assinatura. Nesses casos, VHDs de VMs precisa ser combinado dentro de uma conta de armazenamento. Normalmente você faria isso combinando camada de aplicativo VHDs da SAP VMs de sistemas SAP diferentes.  Nós também combinados VHDs diferentes de VMs DBMS diferente de sistemas SAP diferentes em uma conta de armazenamento do Azure. Mantendo os limites de IOPS de contas de armazenamento do Azure em mente ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )

##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] HA no Windows

![Arquitetura do SAP NetWeaver aplicativo HA com SQL Server no Azure IaaS][planning-guide-figure-3200]

As seguintes construções Azure são usadas para o sistema SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e hospedar patches:

* O sistema concluído é implantado no Azure (obrigatório - camada DBMS, (A) instância SCS e camada de aplicativo completo necessário para executar no mesmo local).
* O sistema completo executa dentro de uma assinatura Azure (obrigatória).
* O sistema completo executa dentro de uma rede Virtual do Azure (obrigatório).
* A separação das VMs de um sistema SAP em três conjuntos de disponibilidade é possível mesmo com todas as VMs pertencentes à mesma rede Virtual.
* Todas as VMs executando instâncias DBMS de um sistema SAP estão em um conjunto de disponibilidade. Vamos supor que não há mais de uma máquina virtual executando instâncias DBMS por sistema desde nativo DBMS alta disponibilidade recursos são usados, como o SQL Server AlwaysOn ou protetor de dados do Oracle.
* Todas as VMs executando instâncias DBMS usam sua própria conta de armazenamento. Arquivos de log e dados DBMS são duplicados de uma conta de armazenamento para outra conta de armazenamento usando as funções de alta disponibilidade DBMS que sincronizar os dados. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows SQL, mas não o serviço SQL Server inteiro. 
* Todas as VMs executando (A) instância SCS de um sistema SAP estão em um conjunto de disponibilidade. Dentro dessas VMs é configurar o Windows Server Failover Cluster (WSFC) para proteger (A) instância SCS.
* Todas as VMs executando (A) instâncias SCS usam sua própria conta de armazenamento. (A) Arquivos de instância SCS e pasta global SAP são duplicados de uma conta de armazenamento para outra conta de armazenamento usando replicação SIOS DataKeeper. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster (A) SCS Windows, mas não todo o serviço (A) SCS. 
* TODAS as VMs que representa a camada de servidor de aplicativo SAP em um terceiro disponibilidade definida.
* TODAS as VMs executando servidores de aplicativo SAP usam sua própria conta de armazenamento. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um servidor de aplicativo do SAP, onde AS outras SAP continuar a executar.

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] HA no Linux

A arquitetura para SAP HA no Linux no Azure é basicamente o mesmo Windows conforme descrito acima. A partir de Jan de 2016 existem duas restrições apesar:

* apenas o SAP ASE 16 é suportado atualmente no Linux no Azure sem os recursos de replicação ASE. 
* Há uma solução SAP (A) SCS HA ainda têm suportada no Linux no Azure

Como consequência a partir de janeiro de 2016 um sistema SAP-Linux-Azure não alcançar a disponibilidade mesma como um sistema SAP-Windows-Azure devido ausente HA para o SCS (A) instância e o banco de dados do SAP ASE instância única.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Usando o iniciar automaticamente para instâncias SAP

SAP oferecida a funcionalidade para iniciar instâncias SAP imediatamente após o início do sistema operacional dentro a máquina virtual. As etapas exatas foram documentadas no artigo da Base de Conhecimento de SAP [1909114] - como iniciar o SAP instâncias automaticamente usando o parâmetro iniciar automaticamente. No entanto, SAP recomenda não usar a configuração mais porque não há nenhum controle na ordem de instância reiniciar, presumindo que máquina virtual mais de uma adquiriu afetado ou várias instâncias executou por máquina virtual. Considerando que um cenário Azure típico de uma instância do servidor de aplicativo SAP em uma máquina virtual e no caso de uma única máquina virtual eventualmente obtendo reiniciado, o iniciar automaticamente não é realmente fundamental e pode ser habilitado adicionando esse parâmetro:

    Autostart = 1

Para o perfil de início da instância SAP ABAP e/ou Java.

> [AZURE.NOTE] 
> O parâmetro iniciar automaticamente pode ter algumas dificuldades também. De forma mais detalhada, o parâmetro aciona o início de um SAP ABAP ou instância Java quando o serviço Windows/Linux relacionado da instância é iniciado. Certamente, que é o caso quando os sistemas operacionais inicializa. Entretanto, reinicialização dos serviços do SAP também é uma coisa comum para a funcionalidade de gerenciamento de ciclo de vida de Software SAP como soma ou outras atualizações ou atualizações. Essas funcionalidades não estava esperando uma instância ser reiniciado automaticamente em todos. Portanto, o parâmetro iniciar automaticamente deve ser desativado antes de executar essas tarefas. O parâmetro iniciar automaticamente também não deve ser usado para instâncias do SAP que estão agrupadas, como o SCS/ASCS/CI.

Consulte informações adicionais sobre iniciar automaticamente para SAP instâncias aqui:

* [Iniciar/Parar SAP junto com seu servidor Unix iniciar/parar](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciar e parar agentes de gerenciamento do SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como habilitar automático Iniciar do HANA banco de dados](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)


### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP 3 camadas maiores
Aspectos de alta disponibilidade das configurações do nível 3 SAP adquiriu discutidos nas seções anteriores já. Mas e quanto os sistemas onde os requisitos de servidor DBMS são muito grandes para que ele localizada no Azure, mas a camada de aplicativo SAP podem ser implantados em Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Local das configurações de SAP de nível 3

Não há suporte para dividir a camada de aplicativo próprio ou o aplicativo e DBMS camada entre locais e do Azure. Um sistema SAP é locais completamente implantados ou no Azure. Ele também não é permitido para ter alguns dos servidores de aplicativo executar no local e alguns outros no Azure. Esse é o ponto de partida da discussão. Nós também não oferecem suporte para que os componentes DBMS de um sistema SAP e a camada de servidor de aplicativo SAP implantado em duas regiões diferentes do Azure. Por exemplo DBMS na camada de aplicativo Oeste EUA e SAP no centro dos EUA. Motivo não suporta essas configurações é a sensibilidade de latência da arquitetura do SAP NetWeaver.

No entanto, ao longo de ano passado central parceiros desenvolvidos co locais de dados a regiões do Azure. Esses locais co geralmente são muito perto os dados Azure físicos centros de dentro de uma região do Azure. A distância curta e conexão de ativos no local co por meio de rota expressa em Azure podem resultar em uma latência que é menor que 2 ms. Nesses casos, para localizar a camada DBMS (incluindo armazenamento SAN/NAS) como um local de colegas e o SAP camada de aplicativo no Azure é possível. A partir de dezembro de 2015, não temos qualquer implantações com essa aparência. Mas diferentes clientes com implantações de aplicativos não-SAP estão usando tais abordagens já. 

### <a name="offline-backup-of-sap-systems"></a>Sistemas de Backup da SAP off-line

Depende da configuração de SAP escolhida (2 ou 3-camadas) lá poderia ser uma necessidade de backup. O conteúdo da máquina virtual em si mais ter um backup do banco de dados. O DBMS relacionados backups devem ser feito com métodos de banco de dados. Uma descrição detalhada para os bancos de dados diferentes, podem ser encontradas na [DBMS guia] [dbms-guia]. Por outro lado, SAP podem ser feito backup dos dados de uma maneira offline (incluindo o conteúdo do banco de dados também) conforme descrito nesta seção, ou online, conforme descrito na próxima seção.

O backup offline basicamente, você precisa de um desligamento da máquina virtual por meio do Portal do Azure e uma cópia do disco de máquina virtual base mais todos anexada VHDs para a máquina virtual. Isso seria preservar um ponto no período da máquina virtual e seu disco associado. É recomendável copiar os backups para uma conta diferente de armazenamento do Azure. Portanto, o procedimento descrito em capítulo [copiando discos entre contas de armazenamento do Azure] [planejamento-guia-5.4.2] deste documento seria aplicado.
Além do desligamento usando o Portal do Azure um também poderá fazer isso por meio do Powershell ou CLI conforme descrito aqui: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Uma restauração de estado consistem excluindo a máquina virtual base bem como os discos originais da máquina virtual base e montado VHDs, copiando os VHDs salvos de volta para a conta de armazenamento original e, em seguida, reimplantar o sistema.
Este artigo mostra um exemplo de como fazer um script esse processo no Powershell: <http://www.westerndevs.com/azure-snapshots/>

Certifique-se de instalar uma nova licença do SAP, pois restoing um backup de máquina virtual, conforme descrito acima cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup online de um sistema SAP

Backup do DBMS é executado com métodos específicos do DBMS conforme descrito no [DBMS guia] [dbms-guia]. 

Outras VMs no sistema SAP podem ser feitos usando a funcionalidade de Backup de máquinas virtuais do Azure. Azure Máquina Virtual Backup adquiriu introduzido no início de 2015 e, enquanto isso, é um método padrão para fazer o backup de uma máquina virtual concluída no Azure. Backup Azure armazena os backups no Azure e permite uma restauração de uma máquina virtual novamente. 

> [AZURE.NOTE] 
> A partir de dezembro de 2015 usando Backup de máquina virtual não mantém a identificação exclusiva da máquina virtual que é usado para SAP licenciamento. Isso significa que uma restauração de um backup de máquina virtual requer a instalação de uma nova chave de licença do SAP conforme a máquina virtual restaurada é considerada uma nova VM e não uma substituição do antigo que foi salvo. A partir de Jan de 2016 Backup de máquina virtual do Azure não dá suporte VMs que são implantadas com o Azure Resourc Manager ainda.

> ![Windows][Logo_Windows] Windows
>
> Teoricamente VMs que bancos de dados de execução podem ser feitos de maneira consistente também se os sistemas DBMS for compatível com o Windows VSS (serviço de cópia de sombra de Volume <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> ) como por exemplo, SQL Server faz.
> No entanto, lembre-se de que com base em backups de máquina virtual do Azure no momento restaura de bancos de dados não são possíveis. Portanto, a recomendação é realizar backups de bancos de dados com a funcionalidade DBMS em vez de depender de Backup de máquina virtual do Azure
>
> Para se familiarizar com Backup de máquinas virtuais do Azure Inicie aqui: <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>.
>
> Outras possibilidades são usar uma combinação do Microsoft Data Protection Manager instalado em uma máquina virtual do Azure e o Azure Backup aos bancos de dados de backup e restauração. Mais informações podem ser encontradas aqui: <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>.  


> ![Linux][Logo_Linux] Linux
> 
> Não há nenhum equivalente para Windows VSS no Linux. Portanto, somente arquivo consistente backups são possíveis, mas não consistentes com o aplicativo. O backup SAP DBMS deve ser feito usando a funcionalidade DBMS. O sistema de arquivos que inclui o SAP relacionada a dados podem ser salvos por exemplo, usando tar conforme descrito aqui: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>


### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como site DR para cenários do SAP de produção

Desde Mid 2014, extensões para vários componentes em torno de Hyper-V, o System Center e o Azure habilitar o uso do Azure como local de DR para VMs em execução no local com base em Hyper-V. 

Um blog detalha como implantar esta solução é documentado aqui: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## <a name="summary"></a>Resumo
Os principais pontos de alta disponibilidade para sistemas SAP no Azure são:

* Neste momento, o ponto de falha único SAP não pode ser protegido exatamente da mesma maneira como ele pode ser feito em implantações locais. O motivo é disco compartilhado clusters ainda não podem ser criados no Azure sem o uso de software de terceiros 3º.
* Para a camada DBMS, você precisa usar a funcionalidade DBMS que não dependem de tecnologia de cluster de disco compartilhado. Detalhes estão documentadas no [DBMS guia] [dbms-guia].
* Para minimizar o impacto dos problemas em domínios de falha na manutenção de infraestrutura ou host Azure, você deve usar conjuntos de disponibilidade do Azure:
    * É recomendável ter um conjunto de disponibilidade da camada de aplicativo do SAP.
    * É recomendável ter um conjunto de disponibilidade de separada para a camada de DBMS do SAP.
    * NÃO é recomendável para aplicar a mesma disponibilidade definido para VMs de sistemas SAP diferentes.
* Para fins de Backup da camada DBMS do SAP, verifique o [dbms-guia] [DBMS guia].
* Fazer backup de instâncias do SAP diálogo faz sentido desde que é geralmente mais rápido reimplantar instâncias de diálogo simples.
* Fazer backup a máquina virtual que contém o diretório global do sistema SAP e com ele todos os perfis das instâncias diferentes, faz sentido e devem ser executadas com o Backup do Windows ou por exemplo tar no Linux. Uma vez que existem diferenças entre o Windows Server 2008 (R2) e o Windows Server 2012 (R2), que facilitam o backup com o Windows Server mais recente lançamentos, recomendamos para executar o Windows Server 2012 (R2) como o sistema operacional convidado Windows. 
