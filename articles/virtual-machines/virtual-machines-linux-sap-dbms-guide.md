<properties
   pageTitle="SAP NetWeaver no Linux VMs (máquinas virtuais) – guia de implantação do DBMS | Microsoft Azure"
   description="SAP NetWeaver no Linux VMs (máquinas virtuais) – guia de implantação de DBMS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-azure-virtual-machines-vms--dbms-deployment-guide"></a>SAP NetWeaver no Azure VMs (máquinas virtuais) – guia de implantação de DBMS

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

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (SAP NetWeaver no Linux VMs (máquinas virtuais) – guia de implantação do DBMS) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (cache para VMs e VHDs) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (armazenamento do Microsoft Azure) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (estrutura de uma implantação de RDBMS) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (alta disponibilidade e recuperação de dados com VMs do Azure) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versões anteriores) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (usando uma imagens do SQL Server fora do Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (geral do SQL Server para SAP em Azure resumo) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (especificações do SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (configuração de armazenamento) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e restauração) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (considerações de desempenho de Backup e restauração) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (outros) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (SAP NetWeaver no Linux VMs (máquinas virtuais) – guia de implantação) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP recursos) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantando uma máquina virtual com uma imagem personalizada) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (cenário 1: Implantando uma máquina virtual fora do Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (cenário 2: Implantando uma máquina virtual com uma imagem personalizada para SAP) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 ( Cenário 3: Mover uma máquina virtual do local usando um VHD de Azure não generalizado com SAP) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (implantação de cenários de VMs para SAP em Microsoft Azure) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (cmdlets do PowerShell do Azure Implantando) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download e SAP de importação relevantes cmdlets do PowerShell) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (ingressar máquina virtual para o domínio local - somente para Windows) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [implantação-guia-4.4]: Virtual-Machines-Linux-SAP-Deployment-Guide.MD#c7cbb0dc-52a4-49DB-8e03-83e7edc2927d (baixar, instalar e ativar agente de máquina virtual do Azure) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar Azure Enhanced monitoramento extensão para SAP) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (preparação para verificar se há Azure Enhanced monitoramento para SAP) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (verificação de integridade do Azure Configuração de infraestrutura de monitoramento) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (mais solução de problemas de infraestrutura de monitoramento do Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (SAP NetWeaver em Linux máquinas virtuais (VMs) – guia de planejamento e implementação) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (recursos) [planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilidade (HA) e a recuperação de desastres (DR) para SAP NetWeaver em execução em máquinas virtuais do Azure) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (alta disponibilidade para servidores de aplicativos do SAP) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (usando iniciar automaticamente para instâncias SAP) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md# 1625df66-4cc6-4d60-9202-de8a0b77f803 (somente na nuvem - implantações de máquina Virtual para o Azure sem dependências na rede local do cliente) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (entre local - implantação de único ou várias VMs SAP em Azure com a exigência de sendo totalmente integrado à rede local) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure regiões) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (falhas domínios) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (atualizar domínios) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088- F9be - 4c 97-958a - 27996255c 665 (conjuntos de disponibilidade do Azure) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (o Microsoft Azure Máquina Virtual conceito) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (movendo uma máquina virtual do local para o Azure com um disco não generalizado) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantando uma máquina virtual com uma imagem específica do cliente) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparação para mover uma máquina virtual do local para o Azure com um disco não generalizado) [ Planning-Guide-5.2.2]:Virtual-Machines-Linux-SAP-Planning-Guide.MD#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (preparação para implantar uma máquina virtual com uma imagem específica do cliente para SAP) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparando VMs com SAP para Azure) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (diferença entre um disco de Azure e imagem Azure) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (carregando um VHD do local para o Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (copiando discos entre contas de armazenamento do Azure) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md# 4efec401-91e0-40c0-8e64-f2dceadff646 (estrutura de máquina virtual/VHD para implantações de SAP) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (montagem do configuração automática para discos anexados) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (única máquina virtual com o SAP NetWeaver demonstração/treinamento cenário) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (implantação de conceitos de Cloud-Only de instâncias do SAP) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitoramento solução para SAP) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure Premium armazenamento)

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
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

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
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
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
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Este guia faz parte da documentação sobre como implementar e implantar o software do SAP no Microsoft Azure. Antes de ler este guia, leia a [guia de planejamento e implementação] [-guia de planejamento]. Este documento aborda a implantação de vários sistemas de gerenciamento de banco de dados relacional (RDBMS) e produtos relacionados em combinação com SAP no Microsoft Azure VMs (máquinas virtuais) usando a infraestrutura do Azure como um recursos de serviço (IaaS).

Papel complementa a documentação de instalação do SAP e anotações de SAP que representam os recursos principais para implantações do software SAP e instalações em determinada plataformas

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="general-considerations"></a>Considerações gerais
Neste capítulo, considerações da execução SAP relacionadas sistemas de DBMS no Azure VMs são introduzidos. Há algumas referências para sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específico são tratados neste documento, após este capítulo.

### <a name="definitions-upfront"></a>Definições antecipadamente
Em todo o documento usaremos os seguintes termos:

* IaaS: Infraestrutura como um serviço.
* PaaS: Plataforma como um serviço.
* SaaS: Software como um serviço.
* Componente do SAP: um SAP aplicativo individual como ECC, BW, gerente de solução ou EP.  Componentes do SAP podem ser baseadas em tecnologias tradicionais de ABAP ou Java ou um aplicativo com base não-NetWeaver como objetos comerciais.
* Ambiente de SAP: um ou mais componentes SAP agrupados logicamente para realizar uma função de negócios como desenvolvimento, QAS, treinamento, DR ou produção.
* Cenário SAP: Refere-se aos ativos SAP inteiros em um cliente panorama de TI. Cenário SAP inclui todos os ambientes de produção e não-produção.
* Sistema SAP: A combinação de camada DBMS e camada de aplicativo de por exemplo, um sistema de desenvolvimento de ERP SAP, sistema de teste do SAP BW, o sistema de produção SAP CRM, etc. Em implantações Azure-não há suporte para dividir essas duas camadas entre locais e do Azure. Isso significa que um sistema SAP seja implantado local ou ele é implantado no Azure. No entanto, você pode implantar os sistemas diferentes de um cenário SAP no Azure ou local. Por exemplo, você pode implantar o desenvolvimento de CRM da SAP e testar sistemas no Azure, mas o SAP CRM produção sistema local.
* Implantação somente na nuvem: uma implantação onde a assinatura do Azure não estiver conectada através de um site-site ou conexão de rota expressa na infraestrutura de rede local. Em comum documentação Azure esses tipos de implantações também são descritos como 'Somente na nuvem' implantações. Máquinas virtuais implantadas com esse método são acessadas através da Internet e pontos de extremidade Internet públicos atribuídos a VMs no Azure. O local do Active Directory (AD) e DNS não está estendido para Azure nesses tipos de implantações. Portanto, as VMs não fazem parte do Active Directory local. Observação: Somente na nuvem implantações neste documento são definidas como concluídos cenários SAP que estão em execução exclusivamente no Azure sem extensão do Active Directory ou resolução do nome do local em nuvem pública. Configurações somente na nuvem não são suportadas para sistemas SAP de produção ou configurações onde STMS SAP ou outros recursos de local precisam ser usada entre sistemas SAP hospedados no Azure e recursos que residem no local.
* Entre locais: Descreve um cenário onde VMs são implantadas em uma assinatura do Azure que possui-to-site, vários locais ou conectividade de rota expressa entre o local datacenter(s) e Azure. Documentação em comum Azure, esses tipos de implantações também são descritos como cenários de cruz local. O motivo para a conexão é estender domínios locais, do Active Directory local e local DNS para o Azure. O cenário de local é estendido para os ativos Azure da assinatura. Tendo essa extensão, VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e serviços podem ser executados nessas VMs (como DBMS serviços). Resolução de nomes e comunicação entre VMs implantado local e VMs implantadas no Azure é possível. Esperamos que este é o cenário mais comum para implantar ativos do SAP no Azure. Consulte [Este] [ vpn-gateway-cross-premises-options] artigo e [Este] [ vpn-gateway-site-to-site-create] para obter mais informações.

> [AZURE.NOTE] Implantações entre locais do sistemas SAP onde máquinas virtuais do Azure executando sistemas SAP são membros de um domínio de locais são suportadas para sistemas SAP de produção. Configurações de cruz locais são suportadas para implantar partes ou concluir cenários SAP em Azure. Mesmo estiver executando o cenário SAP concluído no Azure requer tendo essas VMs sendo parte do domínio local e anúncios. Em versões anteriores da documentação falamos sobre cenários de TI híbrida, onde o termo 'Híbrido' raiz no fato de que não há uma conectividade entre locais entre locais e do Azure. Nesse caso 'Híbrido' também significa que as VMs no Azure fazem parte do Active Directory local.

Alguns documentação Microsoft descreve os cenários de locais entre um pouco diferente, especialmente para DBMS HA configurações. No caso do SAP de documentos, o local entre cenário apenas concentra-se para baixo até ter uma conectividade de (rota expressa)-to-site ou privada e ao fato de que o cenário SAP é distribuído entre locais e Azure relacionados.

### <a name="resources"></a>Recursos
Os guias a seguir estão disponíveis para o tópico de implantações de SAP no Azure:

* [SAP NetWeaver no Azure VMs (máquinas virtuais) – guia de planejamento e implementação] [-guia de planejamento]
* [SAP NetWeaver no Azure VMs (máquinas virtuais) – guia de implantação] [-guia de implantação]
* [SAP NetWeaver no Azure VMs (máquinas virtuais) – guia de implantação do DBMS (Este documento)] [guia dbms]

As seguintes observações SAP estão relacionadas ao tópico de SAP no Azure:

| Número de nota   | Título
|------------|--------
| [1928533] | Aplicativos do SAP no Azure: tipos de produtos com suporte e máquina virtual do Azure
| [2015553] | SAP no Microsoft Azure: pré-requisitos de suporte
| [1999351] | Solução de problemas de monitoramento Azure avançados para SAP
| [2178632] | Principais medidas de monitoração do SAP no Microsoft Azure
| [1409604] | Virtualização no Windows: monitoramento avançado
| [2191498] | SAP no Linux com o Azure: monitoramento avançado
| [2039619] | Aplicativos do SAP no Microsoft Azure usando o banco de dados do Oracle: suporte para produtos e versões
| [2233094] | DB6: Aplicativos da SAP no Azure usando o IBM DB2 para Linux, UNIX e Windows - informações adicionais
| [2243692] | Linux no Microsoft Azure (IaaS) máquina virtual: problemas de licença do SAP
| [1984787] | SUSE LINUX Enterprise Server 12: Notas de instalação
| [2002167] | Red Hat Enterprise Linux 7. x: instalação e atualização

Leia também o [Wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contém todas as anotações do SAP para Linux.

Você deve ter um conhecimento de trabalho sobre a arquitetura do Microsoft Azure e como máquinas virtuais do Microsoft Azure são implantadas e operado. Você pode encontrar mais informações aqui <https://azure.microsoft.com/documentation/>
 
> [AZURE.NOTE] Podemos **não** está abordando a plataforma do Microsoft Azure como um ofertas de serviço (PaaS) da plataforma Microsoft Azure. Este artigo é sobre um sistema de gerenciamento de banco de dados (DBMS) no Microsoft Azure máquinas virtuais em execução (IaaS) tal como faria executa o DBMS no seu ambiente local. Recursos de banco de dados e funcionalidades entre essas duas ofertas são muito diferentes e não devem ser misturadas uns com os outros. Consulte também: <https://azure.microsoft.com/services/sql-database/>

Desde que estão sendo discutido IaaS, em geral a instalação do Windows, Linux e DBMS e configuração são essencialmente iguais a qualquer máquina virtual ou vazia máquina de metal instale local. No entanto, há algumas decisões de implementação de gerenciamento arquitetura e sistema que serão diferentes ao utilizar IaaS. A finalidade deste documento é explicar a arquitetura específicas e diferenças de gerenciamento de sistema que você deve estar preparado para ao usar IaaS.

Em geral, as áreas gerais de diferença que deste documento abordará são:

* Planejando o layout de máquina virtual/VHD adequado de sistemas SAP para garantir que você tenha os dados apropriados layout de arquivo e pode obter bastante IOPS para sua carga de trabalho.
* Considerações de rede ao usar IaaS.
* Recursos de banco de dados específico para usar para otimizar o layout de banco de dados.
* Considerações de backup e restauração no IaaS.
* Utilizando diferentes tipos de imagens para implantação.
* Alta disponibilidade no Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de uma implantação de RDBMS
Em ordem, siga este capítulo, é necessário entender o que foi apresentado [neste] capítulo [implantação-guia-3] [guia de implantação do] [-guia de implantação]. Conhecimento sobre a série de máquina virtual diferente e suas diferenças e diferenças de padrão do Azure e armazenamento de Premium deve ser compreendido e conhecido antes de ler este capítulo.

Até março de 2015, VHDs Azure que contêm um sistema operacional era limitados a 127 GB de tamanho. Essa limitação adquiriu ativada em março de 2015 (para mais informações sobre seleção <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/> ). A partir daí VHDs contendo o sistema operacional pode ter o mesmo tamanho que qualquer outro VHD. No entanto, nós ainda prefira uma estrutura de implantação onde o sistema operacional, DBMS e eventual binários SAP são separados dos arquivos de banco de dados. Portanto, podemos esperar sistemas SAP em execução em máquinas virtuais do Azure terá a base máquina virtual (ou VHD) instalado com o sistema operacional, banco de dados gerenciamento sistema executáveis e executáveis do SAP. Os arquivos de dados e log do DBMS serão armazenados em armazenamento do Azure (Standard ou Premium armazenamento) em arquivos VHD separados e anexados como discos lógicos para a imagem original do Azure sistema operacional máquina virtual. 

Dependente aproveitando lá Azure padrão ou Premium armazenamento (por exemplo, usando a série DS ou série GS VMs) são outras cotas no Azure que estão documentadas [aqui][virtual-machines-sizes]. Ao planejar seu VHDs Azure, você precisará encontrar o melhor equilíbrio das cotas para o seguinte:

* O número de arquivos de dados.
* O número de VHDs que contêm os arquivos.
* As cotas IOPS de um único VHD.
* A taxa de transferência de dados por VHD.
* O número de possíveis VHDs adicionais por tamanho de máquina virtual.
* A produtividade de armazenamento geral pode fornecer uma máquina virtual.
 
Azure aplicará uma cota IOPS por drive VHD. Essas cotas são diferentes para VHDs hospedados no armazenamento padrão do Azure e Premium. Latências de e/s também será muito diferentes entre os dois tipos de armazenamento com armazenamento Premium oferecer fatores melhores latências de e/s. Cada um dos diferentes tipos de máquina virtual tem um número limitado de VHDs que podem anexar. Outra restrição é que somente determinados tipos de máquina virtual podem aproveitar o armazenamento do Azure Premium. Isso significa que a decisão para um determinado tipo de máquina virtual pode não ser orientada somente pelos requisitos de CPU e memória, mas também por IOPS, requisitos de produtividade de latência e disco que geralmente são dimensionados com o número de VHDs ou o tipo de discos de armazenamento de Premium. Especialmente com armazenamento Premium o tamanho de um VHD também pode ser determinado pelo número de IOPS e produtividade que precisa ser obtido por cada VHD.

O fato de que a taxa IOPS geral, o número de VHDs montado e o tamanho da máquina virtual estão todos vinculados juntos, pode causar uma configuração Azure de um sistema SAP para ser diferente da sua implantação local. Os limites IOPS por LUN são geralmente configuráveis em implantações locais. Enquanto com o armazenamento do Azure são esses limites fixo ou como armazenamento Premium depende do tipo de disco. Assim com implantações locais podemos ver configurações do cliente de servidores de banco de dados que estão usando muitos volumes diferentes para executáveis especial como SAP e o DBMS ou volumes especiais para bancos de dados temporários ou espaços de tabela. Quando tal um sistema local é movido para o Azure ele pode levar a um desperdício de largura de banda potencial IOPS por desperdício um VHD para executáveis ou bancos de dados que não executará qualquer ou não muitas IOPS. Portanto, no Azure VMs recomendamos que os executáveis DBMS e SAP ser instalado no disco SO se possível.

O posicionamento dos arquivos de banco de dados e arquivos de log e o tipo de armazenamento do Azure usado, devem ser definidas pelo IOPS, latência e requisitos de produtividade. Para ter suficiente IOPS para o log de transação, você pode ser forçado para aproveitar os diversos VHDs para o arquivo de log de transação ou use um disco de armazenamento de Premium maior. Nesse caso uma simplesmente criaria um software RAID (por exemplo, Windows Pool de armazenamento para Windows ou MDADM e LVM (Gerenciador de Volume lógico) para Linux) com os VHDs que conterão o log de transação.

___

> ![Windows][Logo_Windows] Windows
>
> Unidade D:\ em uma máquina virtual do Azure é uma unidade não persistente que é feita por alguns discos locais no nó computação Azure. Porque ele é não persistente, isso significa que as alterações feitas ao conteúdo na unidade D:\ é perdido quando a máquina virtual seja reinicializada. Por "quaisquer alterações", podemos dizer de arquivos salvos, diretórios criados, aplicativos instalados, etc.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VMs montar automaticamente uma unidade em /mnt/resource que é uma unidade não persistente feita por discos locais no nó computação Azure. Porque ele é não persistente, isso significa que as alterações feitas ao conteúdo em /mnt/resource é perdido quando a máquina virtual seja reinicializada. Por qualquer alteração, podemos dizer de arquivos salvos, diretórios criados, aplicativos instalados, etc.

___

A série de máquina virtual Azure dependente, os discos locais no nó computação mostram desempenho diferentes que pode ser categorizado como:

* A0-A7: Desempenho muito limitado. Não pode ser usado para qualquer coisa além do arquivo de página do windows
* A8-A11: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg
* Série D: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg
* Série de DS: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg
* Série de G: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg
* Série de GS: Características de desempenho muito bom com alguns IOPS dez mil e > produtividade de 1GB/seg

Instruções acima estão aplicando aos tipos de máquina virtual que são certificados com SAP. A série de máquina virtual com excelente IOPS e produtividade se qualifica para aproveitar por alguns recursos DBMS, como tempdb ou espaço de tabela temporário.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cache de VMs e VHDs
Quando criamos esses discos/VHDs por meio do portal ou quando podemos montar VHDs carregados em VMs, podemos pode escolher se o tráfego de i/o entre a máquina virtual e essas VHDs localizadas no armazenamento do Azure são armazenadas em cache. Azure Standard e Premium armazenamento usam duas tecnologias diferentes para este tipo de cache. Em ambos os casos o cache próprio será disco feito nas mesmas unidades usadas pelo disco temporário (D:\ no Windows) ou /mnt/resource no Linux da máquina virtual.
 
Armazenamento padrão do Azure os tipos de cache possíveis são:

* Sem cache
* Cache de leitura
* Ler e gravar o cache

Para obter desempenho consistente e determinante, você deve definir o cache em armazenamento padrão do Azure para todos os VHDs contendo **DBMS relacionados arquivos de dados, arquivos de log e espaço de tabela para 'Nenhum'**. O cache da máquina virtual pode permanecer com o padrão.

Para o armazenamento do Azure Premium existem as seguintes opções de cache:

* Sem cache
* Cache de leitura

Recomendação de armazenamento do Azure Premium é aproveitar o **cache para arquivos de dados de leitura** do banco de dados SAP e optou **sem armazenamento em cache para a VHD(s) dos arquivos de log**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>RAID de software
Como já mencionado anteriormente, você precisará de equilíbrio entre o número de IOPS necessária para os arquivos de banco de dados entre o número de VHDs, você pode configurar e digite o máximo de IOPS uma máquina virtual do Azure fornecerá por disco VHD ou armazenamento Premium. A maneira mais fácil de lidar com a carga IOPS sobre VHDs é criar um software RAID sobre os VHDs diferentes. Coloque um número de arquivos de dados do SAP DBMS nos LUNS gravados fora do software RAID. Dependente dos requisitos que você talvez queira considerar o uso de armazenamento Premium também desde dois ou três discos de armazenamento de Premium diferentes fornecem maior cota IOPS que VHDs com base em armazenamento padrão. Além da latência de i/o melhor significativa fornecida pelo armazenamento do Azure Premium. 

Mesmo se aplica para o log de transação dos sistemas DBMS diferentes. Com muitas delas apenas adicionar mais arquivos de Tlog não ajudam desde que os sistemas DBMS gravar em um dos arquivos de uma vez só. Se forem necessárias maiores taxas IOPS que um único padrão baseada em armazenamento VHD pode fornecer, você pode distribuição ao longo de diversos VHDs padrão de armazenamento ou você pode usar um tipo de disco Premium armazenamento maior que, além de taxas IOPS maiores, também oferece fatores menor latência para a gravação/s no log de transação.
 
Situações com experiência em implantações Azure que faria preferir usando um software RAID são:

* Log de transação da Log/Refazer exigem mais IOPS do Azure fornece para um único VHD. Conforme mencionado acima isso pode ser resolvido criando um LUN sobre diversos VHDs usando um software de RAID.
* I / carga distribuição desigual sobre os arquivos de dados diferente do banco de dados SAP. Nesses casos, um pode experimentar um arquivo de dados atingir a cota preferir com frequência. Enquanto outros arquivos de dados não são mesmo chegando perto da cota de IOPS de um único VHD. Nesses casos, a solução mais fácil é criar um LUN em diversos VHDs usando um software de RAID. 
* Você não sabe o que é a carga de trabalho e/s exata por arquivo de dados e apenas aproximadamente saber qual é a carga de trabalho geral IOPS contra o DBMS. É fácil fazer construir um LUN com a Ajuda de um software RAID. A soma de cotas de diversos VHDs atrás esse LUN depois deve atender a taxa IOPS conhecida.

___

> ![Windows][Logo_Windows] Windows
>
> Uso do Windows Server 2012 ou espaços de armazenamento maiores é preferível, pois é mais eficiente do que a distribuição do Windows de versões anteriores do Windows. Esteja ciente de que você precisa criar os Pools de armazenamento do Windows e espaços de armazenamento por comandos do PowerShell ao usar o Windows Server 2012 como sistema operacional. Os comandos do PowerShell podem ser encontrados aqui <https://technet.microsoft.com/library/jj851254.aspx>

> 
> ![Linux][Logo_Linux] Linux
>
> Somente MDADM e LVM (Gerenciador de Volume lógico) são suportados para criar um software RAID no Linux. Para obter mais informações, leia os artigos a seguir:
>
> * [Configurar o Software RAID no Linux] [ virtual-machines-linux-configure-raid] (para MDADM)
> * [Configurar LVM em uma máquina virtual de Linux no Azure][virtual-machines-linux-configure-lvm]


___

Considerações para aproveitar a série de máquina virtual que é capaz de trabalhar com o armazenamento do Azure Premium geralmente são:

* Pedidos para latências i/o que são Fechar para quais dispositivos SAN/NAS entregar.
* Demanda de latência de e/s melhor fatores que pode oferecer armazenamento padrão do Azure.
* Maior IOPS por máquina virtual que o que pode ser feito com diversos VHDs padrão de armazenamento em relação a um determinado tipo de máquina virtual.

Como o armazenamento do Azure subjacente replica cada VHD para pelo menos três nós de armazenamento, simple RAID 0 distribuição pode ser usada. Não é necessário para implementar RAID5 ou RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Armazenamento do Microsoft Azure
Armazenamento do Microsoft Azure armazenará a base máquina virtual (com o sistema operacional) e VHDs ou BLOBs a pelo menos 3 nós de armazenamento separado. Ao criar uma conta de armazenamento, há uma opção de proteção conforme mostrado aqui:

![Replicação geográfica habilitada para a conta de armazenamento do Azure][dbms-guide-figure-100]

Azure replicação Local de armazenamento (localmente redundante) fornece níveis de proteção contra perda de dados devido a falha de infraestrutura que alguns clientes podem pagar para implantar. Como mostrado acima são 4 diferentes opções com um quinto sendo uma variação de um dos três primeiros. Estamos olhando detalhadamente-los pode distinguir:

* **Premium localmente redundantes armazenamento (LRS)**: o armazenamento do Azure Premium oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais executando eu/O-que requer muita cargas de trabalho. Existem 3 réplicas dos dados dentro do data center Azure mesmo de uma região Azure. As cópias serão diferentes falhas e domínios de atualização (para conceitos consulte [isso] [planejamento-guia-3,2] o capítulo em [Guide][planning-guide]) planejamento. No caso de uma réplica dos dados indo Cancelar serviço devido a uma falha de disco ou falha de nó de armazenamento, uma nova réplica é gerada automaticamente.
* **Armazenamento localmente redundantes (LRS)**: nesse caso, existem 3 réplicas dos dados dentro do data center Azure mesmo de uma região Azure. As cópias serão diferentes falhas e domínios de atualização (para conceitos consulte [isso] [planejamento-guia-3,2] o capítulo em [Guide][planning-guide]) planejamento. No caso de uma réplica dos dados indo Cancelar serviço devido a uma falha de disco ou falha de nó de armazenamento, uma nova réplica é gerada automaticamente. 
* **Localização geográfica redundantes armazenamento (GRS)**: nesse caso, há uma replicação assíncrona que será feed um réplicas 3 adicionais dos dados em outra região do Azure que está na maioria dos casos na mesma região geográfica (como Norte da Europa e Europa Ocidental). Isso fará 3 duplicações adicionais, para que sejam 6 réplicas na soma. Uma variação desta é uma adição onde os dados na região geográfica replicado Azure podem ser usados para fins de leitura (acesso de leitura geográfica-redundante).
* **Zona redundantes armazenamento (ZRS)**: nesse caso 3 réplicas dos dados permanecem na mesma região do Azure. Conforme explicado neste [] [planejamento-guia-3.1] capítulo no [guia de planejamento] [-guia de planejamento] uma região Azure pode ser um número de data centers perto. No caso de LRS réplicas seriam distribuídas ao longo de diferentes dos data centers que compõem uma região Azure.

Mais informações podem ser encontradas [aqui][storage-redundancy].
 
> [AZURE.NOTE] Para implantações de DBMS, o uso de armazenamento redundante geográfica não é recomendado
>
> Replicação de localização geográfica de armazenamento Azure é assíncrona. Replicação de VHDs individuais montado em uma única VM não são sincronizados na etapa de bloqueio. Portanto, ele não é adequado replicar arquivos DBMS que foram distribuídos sobre VHDs diferentes ou implantados em relação a um software RAID com base em vários VHDs. Software DBMS requer que o armazenamento de disco persistente está sincronizado com precisão em diferentes LUNs e subjacentes em VHDs/discos/eixos. Software DBMS usa vários mecanismos às atividades de gravação de IO sequência e um DBMS informará que o armazenamento de disco alvo a replicação está corrompido se elas variam até mesmo alguns milissegundos. Portanto, se um realmente quer uma configuração de banco de dados com um banco de dados alongado entre diversos VHDs replicado geográfica, tal uma replicação precisa ser executada com meios de banco de dados e funcionalidade. Um não deve confiar no Azure geográfica replicação de armazenamento para executar esta tarefa. 
>
> O problema é mais simples explicar com um sistema de exemplo. Vamos supor que você tenha um sistema SAP carregado no Azure que tem 8 VHDs que contém os arquivos de dados do DBMS mais um VHD que contém o arquivo de log de transação. Cada um desses 9 VHDs terá dados gravados em um método consistente de acordo com o DBMS, se os dados estão sendo gravados nos arquivos de log de transação ou de dados.
>
> Em ordenar a localização geográfica replicar corretamente os dados e manter uma imagem consistente do banco de dados, o conteúdo de todos os nove VHDs teria de ser replicados geográfica na ordem exata as operações de e/s foram executadas os nove VHDs diferentes. No entanto, localização geográfica-replicação de armazenamento do Azure não permite declarar dependências entre VHDs. Isso significa que o armazenamento do Microsoft Azure replicação geográfica não conhece o fato de que o conteúdo nessas nove VHDs diferentes estão relacionados uns aos outros e que as alterações de dados são consistentes somente quando replicar na ordem as operações de e/s aconteceu em todos os 9 VHDs.
>
> Além de chances sendo alto que as imagens geográfica replicado no cenário não fornecem uma imagem de banco de dados consistente, há também uma perda de desempenho que aparece com armazenamento redundante geográfica que pode afetar seriamente o desempenho. Em resumo não use esse tipo de redundância de armazenamento para cargas de trabalho de tipo DBMS.
 
#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapeando VHDs em contas de armazenamento de serviço do Azure Máquina Virtual
Uma conta de armazenamento do Azure não é apenas uma construção administrativa, mas também um assunto das limitações. Enquanto as limitações variam em se falarmos sobre uma conta de armazenamento padrão do Azure ou uma conta de armazenamento do Azure Premium. Os recursos exatos e limitações são listadas [aqui][storage-scalability-targets]
 
Portanto para armazenamento padrão do Azure, é importante observar que há um limite no IOPS por conta de armazenamento (linha contendo 'Taxa de solicitação Total' no [artigo][storage-scalability-targets]). Além disso, há um limite inicial de 100 contas de armazenamento por assinatura Azure (a partir de julho de 2015). Portanto, é recomendável para saldo IOPS de VMs entre várias contas de armazenamento ao usar o armazenamento padrão do Azure. Enquanto uma única VM ideal usa uma conta de armazenamento se possível. Portanto se falarmos sobre implantações de DBMS onde cada VHD que está hospedado no armazenamento padrão do Azure poderia atingir seu limite de cota, você deverá implantar somente VHDs de 30 a 40 por conta de armazenamento do Azure que usa armazenamento padrão do Azure. Por outro lado, se você aproveitar o armazenamento do Azure Premium e deseja armazenar volumes de banco de dados grande, talvez seja pequeno em termos de IOPS. Mas, uma conta de armazenamento do Azure Premium é maneira mais restritiva do volume de dados que uma conta de armazenamento padrão do Azure. Como resultado, você só pode implantar um número limitado de VHDs dentro de uma conta de armazenamento do Azure Premium antes de atingir o limite de volume de dados. Ao pensar a fim de uma conta de armazenamento do Azure como um "SAN Virtual" que tem recursos limitados em IOPS e/ou capacidade. Como resultado, a tarefa permanece, como em implantações de locais, para definir o layout dos VHDs dos sistemas SAP diferentes sobre o diferente 'dispositivos de SAN imaginários' ou contas de armazenamento do Azure.
 
Para o armazenamento do Azure padrão não é recomendável para apresentar o armazenamento de contas de armazenamento diferentes para uma única VM se possível.

Enquanto usando o DS ou série GS do Azure VMs é possível VHDs montagem sair contas de armazenamento padrão do Azure e contas de armazenamento Premium. Casos de uso como escrever backups em armazenamento padrão feito VHDs enquanto ter dados DBMS e arquivos de log do armazenamento de Premium vêm à mente onde tal armazenamento heterogêneo poderia ser utilizado. 

Com base em implantações de cliente e testes em torno de 30 a 40 VHDs que contém os arquivos de dados do banco de dados e arquivos de log podem ser provisionados em uma única conta do Azure padrão armazenamento com desempenho aceitável. Conforme mencionado anteriormente, a limitação de uma conta de armazenamento do Azure Premium é provavelmente a capacidade de dados pode conter e não IOPS.

Como com SAN dispositivos no local, compartilhamento requer alguns monitoramento para detectar eventualmente gargalos em uma conta de armazenamento do Azure. A extensão de monitoramento do Azure para SAP e o Portal do Azure são ferramentas que podem ser usadas para detectar ocupado contas de armazenamento do Azure que podem ser oferecer o desempenho de IO inferior.  Se essa situação for detectada, é recomendável mover VMs ocupadas para outra conta de armazenamento do Azure. Consulte [guia de implantação] [-guia de implantação] para obter detalhes sobre como ativar o host do SAP recursos de monitoramento.

Outro artigo resumindo práticas recomendadas sobre armazenamento padrão do Azure e contas de armazenamento padrão do Azure pode ser encontrado aqui <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>
 
#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Movendo implantado VMs DBMS do armazenamento padrão do Azure para o armazenamento do Azure Premium
Encontramos bastante alguns cenários você como cliente para onde deseja mover uma máquina virtual implantada do armazenamento padrão do Azure para o armazenamento do Azure Premium. Isso não é possível sem movimentação física os dados. Há várias maneiras para atingir o objetivo:

* Você pode simplesmente copiar todos os VHDs, VHD base, bem como VHDs de dados para uma nova conta de armazenamento do Azure Premium. Frequentemente você escolheu o número de VHDs em armazenamento padrão do Azure não devido ao fato de que é necessário o volume de dados. Mas é necessário que muitos VHDs devido o IOPS. Agora que você mover para o armazenamento do Azure Premium poderá ir maneira menos VHDs alcançar a alguns produtividade IOPS. Dado o fato de que no Azure armazenamento padrão você pagar os dados usados e não o tamanho do disco nominal, o número de VHDs realmente importa em termos de custos. No entanto, com o armazenamento do Azure Premium, você pagaria o tamanho do disco nominal. Portanto, a maioria do cliente tente manter o número de Azure VHDs em armazenamento Premium o número necessário para alcançar a produtividade IOPS necessárias. Portanto, a maioria dos clientes decidir contra a maneira de um simples 1:1 cópia.
* Se ainda não montado, você pode montar um único VHD que pode conter um backup do banco de dados do seu banco de dados do SAP. Após o backup, você desmontar VHDs todos, incluindo o VHD contendo o backup e copie o VHD base e o VHD com o backup para uma conta de armazenamento do Azure Premium. Você poderia implantar a máquina virtual com base no VHD base e montar o VHD com o backup. Agora você criar discos de armazenamento Premium vazia adicionais para a máquina virtual que são usados para restaurar o banco de dados em. Isso pressupõe que o DBMS permite alterar caminhos para os arquivos de log e de dados como parte do processo de restauração.
* Outra possibilidade é uma variação do processo anterior, onde você apenas copie o backup VHD para o armazenamento do Azure Premium e anexá-lo em relação a uma máquina virtual que recentemente implantado e instalado.
* A possibilidade de quarta escolha quando você estiver em necessidade alterar o número de arquivos de dados do seu banco de dados. Nesse caso, você deve executar uma cópia de homogêneo sistema SAP usando importação/exportação. Colocar aqueles exportar arquivos em um VHD que é copiado para uma conta de armazenamento do Azure Premium e anexá-lo em uma máquina virtual que você usa para executar os processos de importação. Os clientes usar essa possibilidade principalmente quando desejarem diminuir o número de arquivos de dados.

### <a name="deployment-of-vms-for-sap-in-azure"></a>Implantação de VMs SAP no Azure
Microsoft Azure oferece várias maneiras para implantar VMs e discos associados. Portanto, é muito importante entender as diferenças desde preparativos das VMs podem diferir dependente da maneira de implantação. Em geral, vamos examinar os cenários descritos nos capítulos a seguir.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Implantando uma máquina virtual do Azure Marketplace
Você deseja tirar uma Microsoft ou festa 3º fornecido imagem do Azure Marketplace para implantar sua máquina virtual. Após você implantado sua máquina virtual no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP dentro de sua máquina virtual como você faria em um ambiente local. Para instalar o software SAP dentro da VM Azure, SAP e Microsoft recomendam para carregar e armazenar a mídia de instalação do SAP no Azure VHDs ou criar uma máquina virtual do Azure trabalhando como 'servidor de arquivos' que contém todas as SAP instalação mídias necessárias.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Implantando uma máquina virtual com uma imagem de generalizado específico do cliente
Devido a requisitos de patch específico em relação à sua versão do sistema operacional ou DBMS, as imagens fornecidas do Azure Marketplace podem não atender às suas necessidades. Portanto, talvez seja necessário criar uma máquina virtual usando sua própria imagem de máquina virtual do sistema operacional/DBMS 'private', que pode ser implementada várias vezes posteriormente. Para preparar uma imagem como 'private' para duplicação, o sistema operacional deve ser generalizado na VM local. Consulte [guia de implantação] [-guia de implantação] para obter detalhes sobre como generalize uma máquina virtual.

Se você já tiver instalado o conteúdo SAP em sua máquina virtual de local (especialmente para sistemas de 2 camadas), você pode adaptar as configurações do sistema SAP após a implantação da máquina virtual do Azure através da instância renomear procedimento compatível com o Gerenciador de provisionamento de Software do SAP (Observação SAP [1619720]). Caso contrário, você pode instalar o software SAP mais tarde após a implantação da máquina virtual do Azure.
 
A partir do banco de dados conteúdo usado pelo aplicativo SAP, você pode gerar o conteúdo recentemente por uma instalação de SAP ou você pode importar seu conteúdo para Azure usando um VHD com um backup do banco de dados DBMS ou aproveitando os recursos do DBMS backup diretamente para o armazenamento do Microsoft Azure. Nesse caso, você pode também preparar VHDs com os DBMS dados e log arquivos locais e, em seguida, importar aqueles como discos para Azure. Mas, a transferência de dados DBMS que está sendo carregados do local para o Azure funcionaria sobre discos VHD que precisam ser preparados local.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Mover uma máquina virtual do local para o Azure com um disco não generalizado
Você planeja mover um sistema SAP específico de locais para Azure (levantar e shift). Isso pode ser feito carregando o VHD que contém o sistema operacional, os binários SAP e eventual binários DBMS além os VHDs com os arquivos de dados e log do DBMS no Azure. Em se oposta à cenário #2 acima, você manter o hostname, SAP SID e contas de usuário SAP a máquina virtual do Azure conforme eles foram configurados no ambiente local. Portanto, não é necessário generalizar a imagem. Neste caso, principalmente, serão aplicadas para cenários entre locais onde uma parte do cenário SAP é executada no local e peças no Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Alta disponibilidade e recuperação de dados com VMs Azure
Azure oferece as seguintes funcionalidades alta disponibilidade (HA) e a recuperação de desastres (DR) que se aplicam a diferentes componentes que usaríamos para implantações de SAP e DBMS

### <a name="vms-deployed-on-azure-nodes"></a>VMs implantadas em nós do Azure
A plataforma Azure não oferece recursos como migração ao vivo para VMs implantadas. Isso significa que se houver manutenção necessárias em um cluster de servidor em que uma máquina virtual é implantada, a máquina virtual precisa obter interrompido e reiniciado. Manutenção no Azure é executada usando portanto chamado atualizar domínios dentro clusters de servidores. Está sendo mantido apenas uma atualização de domínio por vez. Durante a reinicialização tal haverá uma interrupção de serviço enquanto a máquina virtual está desligada, manutenção seja executada e reiniciado de máquina virtual. No entanto, a maioria dos fornecedores DBMS fornecer funcionalidade de alta disponibilidade e recuperação de dados que rapidamente irá reiniciar os serviços DBMS em outro nó se o nó primário não estiver disponível. A plataforma Azure oferece funcionalidade para distribuir VMs, armazenamento e outros serviços do Azure em domínios atualizar para garantir que falhas planejadas de manutenção ou infraestrutura somente impacto um pequeno subconjunto de VMs ou serviços.  Com o planejamento de cuidado é possível atingir níveis de disponibilidade comparáveis à infra-estruturas de local.

Os conjuntos de disponibilidade do Microsoft Azure são um agrupamento lógico de VMs ou serviços que garante VMs e outros serviços são distribuídos para diferentes falhas e atualizar domínios dentro de um cluster, de forma que só haja um desligamento de nó em qualquer um ponto no tempo (ler [Esta] [ virtual-machines-manage-availability] artigo para obter mais detalhes).

Ela precisa ser configurada por finalidade ao implantar VMs como visto aqui:

![Definição de disponibilidade definida para configurações DBMS HA][dbms-guide-figure-200]

Se quisermos criar configurações altamente disponíveis de implantações de DBMS (independente da individuais DBMS HA funcionalidade usada), as VMs DBMS precisaria:

* Adicionar VMs ao mesmo Azure rede Virtual (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* As VMs da configuração HA também devem estar na mesma sub-rede. Resolução de nome entre as diferentes sub-redes não é possível em implantações somente na nuvem, funcionará somente resolução IP. Usando-to-site ou conectividade de rota expressa para implantações entre locais, uma rede com pelo menos uma sub-rede será já estabelecida. Resolução do nome será feita acordo com o local infraestrutura de rede e políticas do AD. 
[comentário]: <>  (Teste de TODO MSSedusch se ainda verdadeiro no BRAÇO)

#### <a name="ip-addresses"></a>Endereços IP
É altamente recomendável configurar as VMs para HA configurações de maneira flexível. Contar com endereços IP para os parceiros HA dentro da configuração de HA de endereço não é confiável no Azure, a menos que os endereços IP estáticos são usados. Há dois conceitos de "Desligamento" no Azure:

* Desligar por meio do Portal do Azure ou Azure PowerShell cmdlet parada AzureRmVM: neste caso a máquina Virtual obtém desligamento e eliminação alocado. Sua conta do Azure não mais será cobrada para essa máquina virtual para que os encargos somente que provocará são para o armazenamento usado. Entretanto, se o endereço IP privado da interface da rede não foi estático, o endereço IP é lançado, e ele não garantimos que a interface de rede obtém o endereço IP antigo atribuído novamente após a reinicialização da máquina virtual. Realizar desligar para baixo por meio do Portal do Azure ou chamando AzureRmVM parar automaticamente causará alocação. Se você não deseja deallocat o uso de máquina parada-AzureRmVM - StayProvisioned 
* Se você desligar a máquina virtual de um nível de sistema operacional, a máquina virtual obtém desligar e não eliminação alocada. No entanto, nesse caso, sua conta do Azure será ainda cobrada para a máquina virtual, apesar do fato de que se trata de desligamento. Nesse caso, a atribuição do endereço IP para uma máquina virtual parou permanecerão intacta. Desligar a máquina virtual do dentro não fará automaticamente alocação.

Mesmo para cenários de entre locais, por padrão uma alocação e desligamento significará atribuição eliminação dos endereços IP da máquina virtual, mesmo se as diretivas de local nas configurações de DHCP são diferentes. 

* A exceção é se atribui um endereço IP estático para uma interface de rede como descrito [aqui][virtual-networks-reserved-private-ip].
* Nesse caso o endereço IP permanece fixo desde a interface de rede não é excluída.

> [AZURE.IMPORTANT] Para manter a implantação inteira simples e gerenciável, a recomendação clara é configurar as VMs parceria em um DBMS HA DR configuração ou dentro do Azure de uma forma que há uma resolução de nome funcional entre as diferentes VMs envolvidas.
 
## <a name="deployment-of-host-monitoring"></a>Implantação de monitoramento de Host
Para uso produtivo dos aplicativos da SAP em máquinas virtuais do Azure, SAP requer a capacidade de obter dados de hosts físicos executando as máquinas virtuais do Azure de monitoramento de host. Um nível de patch específico do SAP HostAgent será necessário que permite que esse recurso no SAPOSCOL e HostAgent do SAP. O nível exato patch é documentado na anotação SAP [1409604].

Para obter os detalhes sobre a implantação dos componentes que fornecem dados host SAPOSCOL e SAPHostAgent e o gerenciamento do ciclo de vida desses componentes, consulte [guia de implantação] [-guia de implantação]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Especificações do Microsoft SQL Server

### <a name="sql-server-iaas"></a>SQL Server IaaS
Começando com o Microsoft Azure, você pode facilmente migrar seus aplicativos existentes do SQL Server criados na plataforma Windows Server em máquinas virtuais do Azure. SQL Server em uma máquina Virtual permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de abrangência corporativos migrando facilmente esses aplicativos Microsoft Azure. Com o SQL Server em uma máquina Virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmo ferramentas de desenvolvimento e administração que estão disponíveis no local. 

> [AZURE.IMPORTANT] Observe que não estão sendo discutido banco de dados do Microsoft Azure SQL que é uma plataforma como uma oferta de serviço da plataforma Microsoft Azure. A discussão neste artigo é sobre executando o produto do SQL Server como é conhecido para implantações locais em máquinas virtuais do Azure, aproveitando a infraestrutura como um recurso de serviço do Azure. Recursos de banco de dados e funcionalidades entre essas duas ofertas são diferentes e não devem ser misturadas uns com os outros. Consulte também: <https://azure.microsoft.com/services/sql-database/>
 
É altamente recomendável revisar [Este] [ virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas seções a seguir partes de partes da documentação sob o link acima serão agregados e mencionados. Detalhes em torno da SAP são mencionadas também e alguns conceitos descritos mais detalhadamente. No entanto, é altamente recomendável trabalhar através da documentação acima primeiro antes de ler a documentação específica do SQL Server.

Há algumas SQL Server IaaS informações específicas que você deve saber antes de continuar:

* **Máquina virtual SLA**: não há um SLA para máquinas virtuais em execução no Azure que pode ser encontrado aqui: <https://azure.microsoft.com/support/legal/sla/>  
* **Suporte de versão do SQL**: para clientes SAP, oferecemos suporte para SQL Server 2008 R2 e superior na máquina Virtual da Microsoft Azure. Edições anteriores não são suportadas. Examine essa geral [Política de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Observe que em geral SQL Server 2008 é suportado pela Microsoft também. No entanto, devido a funcionalidade significativa para SAP que foi introduzido com o SQL Server 2008 R2, SQL Server 2008 R2 é a versão mínima do SAP. Tenha em mente que o SQL Server 2012 e 2014 adquiriu estendido com uma maior integração com o cenário de IaaS (como backup diretamente em relação ao armazenamento do Azure). Portanto, podemos restringir deste documento para SQL Server 2012 e 2014 com seu nível de patch mais recente do Azure.
* **Suporte de recursos do SQL**: recursos de mais SQL Server são suportados em máquinas virtuais do Microsoft Azure com algumas exceções. **Não há suporte para o SQL Server cluster de Failover usando discos compartilhados**.  Distribuído tecnologias como espelhamento de banco de dados, grupos de disponibilidade sempre ativado, replicação, envio de Log e agente de serviços têm suporte dentro de uma única região do Azure. SQL Server AlwaysOn também é suportado entre diferentes regiões Azure conforme documentadas aqui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Examine a [Declaração de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Um exemplo de como implantar uma configuração de AlwaysOn é mostrado neste [] [ virtual-machines-workload-template-sql-alwayson] artigo. Além disso, confira as práticas recomendadas documentadas [aqui][virtual-machines-sql-server-infrastructure-services] 
* **Desempenho do SQL**: temos certeza de que o Microsoft Azure hospedado máquinas virtuais executará muito bem em comparação com outras ofertas de virtualização de nuvem pública, mas resultados individuais podem variar. Confira [Esta] [ virtual-machines-sql-server-performance-best-practices] artigo.
* **Usando imagens do Azure Marketplace**: A maneira mais rápida para implantar um novo Microsoft Azure VM é usar uma imagem do Azure Marketplace. Há imagens do Azure Marketplace que contêm o SQL Server. As imagens onde SQL Server já está instalado não podem ser usadas imediatamente para aplicativos SAP NetWeaver. O motivo é que o agrupamento do SQL Server padrão é instalado dentro dessas imagens e não o agrupamento necessários sistemas SAP NetWeaver. Para poder usar essas imagens, verifique as etapas documentadas no capítulo [usando um imagens do SQL Server fora do Microsoft Azure Marketplace] [dbms-guia-5.6]. 
* Veja os [Detalhes de preços](https://azure.microsoft.com/pricing/) para obter mais informações. A [Guia de licenciamento do SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) e o [Guia de licenciamento de 2014 do SQL Server](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) também são um recurso importante.
 
### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Diretrizes de configuração do SQL Server para SAP relacionados instalações do SQL Server em VMs do Azure

#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura de máquina virtual/VHD para SAP relacionados implantações do SQL Server
De acordo com a descrição geral, SQL Server executáveis devem ser localizado ou instalado na unidade de sistema do VHD base da VM (unidade c:\).  Normalmente, a maioria dos bancos de dados de sistema do SQL Server não é utilizada em um alto nível por carga de trabalho do SAP NetWeaver. Portanto, os bancos de dados de sistema do SQL Server (master, msdb e modelo) podem permanecer na unidade C:\ também. Uma exceção poderia ser tempdb, que, no caso de alguns SAP ERP e todas as cargas de trabalho de p & b, pode exigir maior volume de dados ou volume de operações de e/s que não se encaixam com a máquina virtual original. Para esses sistemas, as seguintes etapas devem ser executadas:

* Mova os arquivos de dados tempdb primário para a mesma unidade lógica os arquivos de dados principal do banco de dados SAP.
* Adicione os arquivos de dados tempdb adicionais para cada uma das outras unidades lógicas que contém um arquivo de dados do banco de dados de usuário do SAP.
* Adicione o arquivo de log de tempdb a unidade lógica que contém o arquivo de log do banco de dados de usuário.
* **Exclusivamente para os tipos de máquina virtual que usam SSDs local** em que os arquivos de log e dados de tempdb do nó computação pode ser colocado na unidade D:\. No entanto, pode ser recomendado usar vários arquivos de dados tempdb. Esteja ciente de volumes de unidade D:\ são diferentes com base no tipo de máquina virtual.
 
Essas configurações permitem tempdb consumir mais espaço do que a unidade do sistema é capaz de fornecer. Para determinar o tamanho do tempdb adequada, um pode verificar os tamanhos de tempdb em sistemas existentes que são executados no local. Além disso, essa configuração permite números de IOPS contra tempdb que não pode ser fornecido com a unidade do sistema. Novamente, sistemas que executam o local podem ser usados para monitorar a carga de trabalho e/s contra tempdb para que você pode gerar os números IOPS que esperava ver em tempdb.

Uma configuração de máquina virtual que executa o SQL Server com um banco de dados do SAP e onde os dados de tempdb e arquivo de log de tempdb são colocadas na unidade D:\ teria o seguinte:
 
![Configuração de referência do Azure IaaS máquina virtual SAP][dbms-guide-figure-300]

Lembre-se de que a unidade D:\ tem tamanhos diferentes dependentes do tipo de máquina virtual. Dependente o requisito de tamanho de tempdb você pode ser forçado a arquivos de log e dados de tempdb de par com os arquivos SAP de dados e de log de banco de dados em casos onde a unidade D:\ é muito pequena.

#### <a name="formatting-the-vhds"></a>Os VHDs de formatação
Para o SQL Server NTFS Bloquear tamanho para VHDs contendo dados do SQL Server e arquivos de log devem ser 64K. Não é necessário para formatar a unidade D:\. Esta unidade vem pré-formatado.

Para certificar-se de que a restauração ou a criação de bancos de dados é não inicializar os arquivos de dados, concentrando-se o conteúdo dos arquivos, um deverá garantir que o contexto de usuário em que o serviço SQL Server é executado uma determinada permissão. Geralmente, os usuários do grupo de administrador do Windows têm essas permissões. Se o serviço SQL Server for executado no contexto do usuário do usuário não - Windows administrador, você precisa atribuir esse usuário o direito de usuário 'Executar tarefas de manutenção de volume'.  Ver os detalhes neste artigo da Base de Conhecimento Microsoft: <https://support.microsoft.com/kb/2574695>
 
#### <a name="impact-of-database-compression"></a>Impacto da compactação de banco de dados
Em configurações onde largura de banda de e/s pode se tornar um fator de limitação, todas as medidas que reduz IOPS podem ajudar a alongar a carga de trabalho que podem ser executados em um cenário de IaaS como o Azure. Portanto, caso ainda não tenha feito, aplicar compactação de página SQL Server é altamente recomendável pela SAP e Microsoft antes de carregar um SAP existente bancos de dados no Azure.
 
A recomendação para executar compactação de banco de dados antes de carregar no Azure é dado fora dois motivos:

* A quantidade de dados a ser carregado é menor.
* A duração da execução compactação é menor, supondo que é possível usar hardware mais forte com mais CPUs ou largura de banda de i/o maior ou menor e/s latência local.
* Menores de banco de dados podem levar menos custos de alocação em disco

A compactação de banco de dados também funciona em um máquinas virtuais do Azure como faz no local. Para obter mais detalhes sobre como compactar um SQL Server existente do SAP banco de dados, consulte aqui: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>
  
### <a name="sql-server-2014--storing-database-files-directly-on-azure-blog-storage"></a>SQL Server 2014 – armazenamento de banco de dados arquivos diretamente em Blog armazenamento do Azure
SQL Server 2014 abre a possibilidade para armazenar arquivos de banco de dados diretamente no armazenamento de Blob do Azure sem a 'embalagem' de um VHD ao redor delas. Especialmente com o uso de armazenamento do Azure padrão ou tipos de máquina virtual menores Isso permite cenários onde você pode solucionar os limites de IOPS que poderia ser aplicada por um número limitado de VHDs que pode ser montado em alguns tipos de máquina virtual menores. Isso funciona para bancos de dados do usuário no entanto, nem para bancos de dados de sistema do SQL Server. Ele também funciona para arquivos de log e dados do SQL Server. Se você gostaria de implantar um banco de dados do SQL Server do SAP dessa forma, em vez de 'disposição' em VHDs, tenha o seguinte em mente:

* A conta de armazenamento costumava necessidades na mesma região Azure como aquele que é usado para implantar o SQL Server de máquina virtual é executado em.
* Considerações listadas anteriormente em relação ao distribuir VHDs em diferentes contas de armazenamento do Azure candidatar-se esse método de implantações também. Significa que a contagem de operações de e/s contra os limites da conta de armazenamento do Azure.
[comentário]: <>  (MSSedusch TODO mas isso usará largura de banda de rede e não armazenamento largura de banda, não é?)

Detalhes sobre esse tipo de implantação são listados aqui: <https://msdn.microsoft.com/library/dn385720.aspx>
 
Para armazenar arquivos de dados do SQL Server diretamente no armazenamento do Azure Premium, você precisa ter uma versão mínima de patch de 2014 do SQL Server que é documentada aqui: <https://support.microsoft.com/kb/3063054>. Armazenar arquivos de dados do SQL Server em armazenamento padrão do Azure funciona com o versão de lançamento do SQL Server 2014. No entanto, os mesmos muito patches contêm outra série de correções que tornam o uso direto de armazenamento de Blob do Azure para arquivos de dados do SQL Server e backups mais confiável. Portanto, é recomendável usar esses patches em geral.

### <a name="sql-server-2014-buffer-pool-extension"></a>Extensão de Pool de Buffer do SQL Server de 2014
SQL Server 2014 introduziu um novo recurso que é chamado de extensão do Pool de Buffer. Esta funcionalidade estende o pool de buffer do SQL Server que é mantido na memória com um segundo cache nível que é suportado por SSDs local de um servidor ou máquina virtual. Isso permite para manter um conjunto de trabalho maior de dados 'na memória'. O acesso para a extensão do pool de buffer que está armazenada no local SSDs de uma máquina virtual do Azure comparado a acessar o armazenamento padrão do Azure é muitos fatores mais rápido.  Portanto, aproveitar D:\ unidade local dos tipos de máquina virtual que possuem excelente IOPS e produtividade poderia ser uma maneira bastante razoável para reduzir a carga IOPS contra o armazenamento do Azure e melhorar os tempos de resposta de consultas drasticamente. Isso se aplica especialmente quando não usando o armazenamento de Premium. No caso de armazenamento de Premium e o uso do Cache Premium Azure lido no nó de computadores, conforme recomendado para arquivos de dados, sem diferenças grandes espera. Motivo é que ambos os caches (extensão de Pool de Buffer do SQL Server e o Cache de leitura de armazenamento Premium) estão usando unidades de local de nós de computação.
Para obter mais detalhes sobre essa funcionalidade, verifique esta documentação: <https://msdn.microsoft.com/library/dn133176.aspx> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Considerações de backup/recuperação para SQL Server
Ao implantar o SQL Server em Azure sua metodologia de backup deve ser revisada. Mesmo se o sistema não é um sistema produtivo, o banco de dados do SAP hospedado pelo SQL Server deve ser copiado periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em relação às compensating uma falha de armazenamento. O motivo de prioridade para manter um plano de recuperação e backup PRI é mais do que você pode compensar erros lógicos/manual, fornecendo ponto em recursos de recuperação de tempo. Para que o objetivo é um dos backups de uso para restaurar o banco de dados para um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração de SAP 2 camadas para a configuração de um sistema de 3 camadas do mesmo sistema restaurando um backup.

Há três maneiras diferentes de backup do SQL Server para armazenamento do Azure:
 
1. SQL Server 2012 CU4 e maiores can nativamente backup bancos de dados para uma URL. Isso é detalhado no blog [nova funcionalidade no SQL Server 2014 – parte 5 – melhorias de Backup e restauração](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Consulte Capítulo [SQL Server 2012 SP1 CU4 e posterior] [dbms-guia-5.5.1].
1. Versões do SQL Server anteriores ao SQL 2012 CU4 podem usar uma funcionalidade de redirecionamento para fazer backup em um VHD e mover basicamente o fluxo de gravação na direção de um local de armazenamento do Azure que foi configurado. Consulte Capítulo [SQL Server 2012 SP1 CU3 e versões anteriores] [dbms-guia-5.5.2].
1. O método final é executar um backup convencional do SQL Server para comando de disco em um dispositivo de disco VHD.  Isso é idêntico ao padrão de implantação local e não é discutido detalhadamente neste documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 e posterior
Esta funcionalidade permite backup diretamente ao armazenamento de BLOB do Azure. Sem esse método, você deve fazer backup para outros VHDs Azure que faria consumir capacidade VHD e IOPS. A ideia é basicamente:
 
 ![Usando o Backup do SQL Server 2012 ao armazenamento do Microsoft Azure BLOB][dbms-guide-figure-400]

A vantagem nesse caso é que um não precisa gastar VHDs para armazenar backups do SQL Server. Portanto, você tem menos VHDs alocados e a largura de banda inteira de VHD IOPS pode ser usada para dados e arquivos de log. Observe que o tamanho máximo de um backup está limitado a um máximo de 1 TB conforme documentadas na seção 'Limitações' neste artigo: <https://msdn.microsoft.com/library/dn435916.aspx#limitations>. Se o tamanho do backup, apesar usando compactação de Backup do SQL Server ultrapassar 1 TB em tamanho, a funcionalidade descrita capítulo [SQL Server 2012 SP1 CU3 e versões anteriores] [dbms-guia-5.5.2] neste documento precisa ser usado.

[Documentação relacionada](https://msdn.microsoft.com/library/dn449492.aspx) descrevendo a restauração de bancos de dados de backups em relação a armazenamento de Blob do Azure recomendável não para restaurar diretamente do armazenamento de BLOB do Azure se os backups é > 25 GB. A recomendação neste artigo simplesmente se baseia na considerações de desempenho e não devido a restrições funcionais. Portanto, diferentes condições podem se aplicar caso a caso.

Documentação em como esse tipo de backup é configurar e aproveitado pode ser encontrada [neste](https://msdn.microsoft.com/library/dn466438.aspx) tutorial
 
Um exemplo da sequência de etapas pode ser lidos [aqui](https://msdn.microsoft.com/library/dn435916.aspx).

Automatizar backups, é de prioridade mais alta para garantir que os BLOBs para cada backup são nomeados de forma diferente. Caso contrário, elas serão substituídas e a cadeia de restauração foi desfeita.
 
Em ordem para não misturar as coisas entre os diferentes tipos de backups a 3, é recomendável criar diferentes contêineres sob a conta de armazenamento usada para backups. Os contêineres podem ser apenas por máquina virtual ou por tipo de máquina virtual e Backup. O esquema pode parecer com:
 
 ![Usando o Backup do SQL Server 2012 ao Microsoft Azure armazenamento de BLOB – diferentes contêineres em conta de armazenamento separada][dbms-guide-figure-500]

No exemplo acima, os backups não seriam realizados para a mesma conta de armazenamento onde as VMs são implantadas. Deve haver uma nova conta de armazenamento especificamente para os backups. Em contas de armazenamento, deve haver diferentes contêineres criados com uma matriz do tipo de backup e o nome da máquina virtual. Tal segmentação tornará mais fácil administrar os backups das VMs diferentes.

Os BLOBs um grava diretamente os backups a, não está adicionando como a contagem dos VHDs de uma máquina virtual. Portanto, um pode maximizar o número máximo de VHDs montado da VM SKU específicos para os dados e arquivo de log de transação e ainda executar um backup em relação a um contêiner de armazenamento. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 e versões anteriores
A primeira etapa, você deve executar para obter um backup diretamente em relação ao armazenamento do Azure seria baixar o msi que esteja vinculado a [Este](https://www.microsoft.com/download/details.aspx?id=40740) artigo KBA Knowledge.
 
Arquivo de instalação do download do x64 e a documentação. O arquivo será instalado um programa chamado: 'Microsoft SQL Server Backup para Microsoft Azure ferramenta'. Leia a documentação do produto completamente.  A ferramenta basicamente funciona da seguinte maneira:

* Do lado do SQL Server, é definido um local do disco para o backup do SQL Server (não use a unidade D:\ para isso).
* A ferramenta permitirá que você defina regras que podem ser usadas para direcionar tipos diferentes de backups para diferentes contêineres de armazenamento do Azure.
* Depois que as regras são no lugar, a ferramenta irá redirecioná o fluxo de gravação do backup para um dos VHDs/discos para o local de armazenamento do Azure que foi definida anteriormente.
* A ferramenta deixará um esboço pequeno arquivo de tamanho KB alguns no VHD/disco que foi definido para o SQL Server backup. **Este arquivo deve ser deixado no local de armazenamento, como ele é necessária para restaurar novamente do armazenamento do Azure.**
    * Se você tiver perdido o arquivo Esboço (por exemplo, por meio de perda de mídia de armazenamento que continha o arquivo esboço) e você escolheu a opção de backup para uma conta de armazenamento do Microsoft Azure, você pode recuperar o arquivo de esboço por meio de armazenamento do Microsoft Azure baixando-a partir do contêiner de armazenamento na qual ele foi colocado. Você deve colocar o arquivo de esboço em uma pasta no computador local onde a ferramenta está configurada para detectar e carregar o mesmo recipiente com a mesma senha de criptografia se criptografia foi usada com a regra original. 

Isso significa que o esquema conforme descrito acima para versões mais recentes do SQL Server pode ser colocado em vigor também para versões do SQL Server que não permitiu endereço direto um local de armazenamento do Azure.
 
Este método não deve ser usado com versões mais recentes do SQL Server que suportam o backup nativamente contra o armazenamento do Azure. Exceções são onde limitações do backup nativo em Azure estão bloqueando a execução de backup nativa em Azure.

#### <a name="other-possibilities-to-backup-sql-server-databases"></a>Outras possibilidades para backup bancos de dados do SQL Server
Outras possibilidades para bancos de dados de backup é anexar VHDs adicionais a uma máquina virtual que você usa para armazenar backups em. Nesse caso, você precisaria certificar-se de que os VHDs não estejam executando completos. Se esse for o caso, você precisaria desmontar o VHD e por assim dizer 'arquivar'-lo e substituí-la por um novo VHD vazio. Se você for aquele caminho, você deseja manter esses VHDs em contas de armazenamento do Azure separadas daqueles que os VHDs com os arquivos de banco de dados.

A possibilidade de segunda é usar uma máquina virtual grande pode ter vários VHDs anexadas. Por exemplo D14 com 32VHDs. Use espaços de armazenamento para criar um ambiente flexível onde você pode criar compartilhamentos usados depois como destinos de backup para diferentes servidores DBMS.
 
Algumas práticas recomendadas adquiriu documentadas [aqui](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) também. 

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações sobre desempenho para backups/restaura
Como em implantações de depender, desempenho de backup e restauração depende quantos volumes podem ser lido em paralelo e que pode ser a produtividade desses volumes. Além disso, o consumo de CPU usado pelo compactação de backup pode ser reproduzidos uma função significativa em VMs com apenas até 8 threads de CPU. Portanto, um pode assumir:

* Quanto menor o número de VHDs usada para armazenar os dados de arquivos, quanto menor for a produtividade geral em leitura.
* Menor que número de CPU threads a máquina virtual, a mais grave o impacto da compactação de backup.
* Menos destinos (BLOBs ou VHDs) para gravar o backup, a menor a taxa de transferência.
* Quanto menor for a máquina virtual tamanho, menor a produtividade cota de armazenamento gravar e ler do armazenamento do Azure. Independente de se os backups são armazenados diretamente no Azure Blob ou se elas são armazenadas em VHDs novamente armazenados em bolhas do Azure.

Ao usar um BLOB de armazenamento do Microsoft Azure como destino de backup em versões mais recentes, você está restrito designar apenas um destino de URL para cada backup específico.
 
Mas ao usar o 'Microsoft SQL Server Backup para Microsoft Azure ferramenta' em versões mais antigas, você pode definir mais de um destino de arquivo. Com mais de um destino, o backup pode dimensionar e a produtividade do backup é maior. Isso resultaria em seguida, em vários arquivos também na conta de armazenamento do Azure. Em nossos testes, usar vários destinos de arquivo um definitivamente pode alcançar a produtividade que um pode atingir com as extensões de backup implementada do SQL Server 2012 SP1 CU4 em. Você também não é bloqueadas pelo limite de 1TB como o backup nativo no Azure.

No entanto, tenha em mente, a produtividade também depende o local da conta de armazenamento do Azure usada para o backup. Talvez seja uma ideia localizar a conta de armazenamento em uma região diferente que VMs estão em execução. Por exemplo Você deve executar a configuração de máquina virtual na Europa Oeste, mas colocar a conta de armazenamento que você usa para fazer backup contra na Europa do Norte. Certamente que terá impacto sobre a taxa de transferência de backup e não provavelmente gerar uma taxa de transferência de 150MB/seg quanto parece ser possíveis em casos onde o armazenamento de destino e VMs estejam em execução no mesmo data center regional.

#### <a name="managing-backup-blobs"></a>Gerenciando BLOBs Backup
Não há um requisito para gerenciar os backups por conta própria. Como as expectativas é que muitos blobs serão criados executando backups de log de transação frequente, administração desses blobs facilmente pode sobrecarregar o Portal do Azure. Portanto, é recommendable aproveitar um Gerenciador de armazenamento do Azure. Existem vários bons disponíveis que podem ajudar a gerenciar uma conta de armazenamento do Azure

* Microsoft Visual Studio com o SDK do Azure instalado (<https://azure.microsoft.com/downloads/>)
* Gerenciador de armazenamento do Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* 3º ferramentas de terceiros

[comentário]: <>  (Ainda não têm suporte no BRAÇO)
[comentário]: <>  (# Backup de máquina virtual do azure)
[comentário]: <>  (VMs no sistema SAP pode ser feito usando a funcionalidade de Backup de máquinas virtuais do Azure. Azure Máquina Virtual Backup adquiriu introduzido no início do ano de 2015 e, enquanto isso, é um método padrão para fazer o backup de uma máquina virtual concluída no Azure. Backup Azure armazena os backups no Azure e permite uma restauração de uma máquina virtual novamente.) 
[comentário]: <> (VMs que bancos de dados de execução podem ser feitos de maneira consistente também se os sistemas DBMS for compatível com o Windows VSS (Volume Shadow cópia Service - <https://msdn.microsoft.com/library/windows/desktop/bb968832.aspx>) como por exemplo, SQL Server. Usando o backup de máquina virtual do Azure pode ser uma maneira de acessar um restaurável backup de um banco de dados do SAP. No entanto, lembre-se de que com base em backups de máquina virtual do Azure no momento restaura de bancos de dados não é possível. Portanto, a recomendação é realizar backups de bancos de dados com a funcionalidade DBMS em vez de depender de Backup de máquina virtual do Azure.) [comentário]: <> (para se familiarizar com Backup de máquinas virtuais do Azure Inicie aqui <https://azure.microsoft.com/documentation/services/backup/>)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Usando um imagens do SQL Server fora do Microsoft Azure Marketplace
A Microsoft oferece VMs do Azure Marketplace que já contêm versões do SQL Server. Para clientes SAP que precisam de licenças para SQL Server e o Windows, isso pode ser uma oportunidade para cobrir basicamente a necessidade de licenças por bagunçar VMs com o SQL Server esteja instalado. Para poder usar essas imagens para SAP, as considerações a seguir precisam ser feitas:

* As versões do SQL Server não sejam de avaliação adquirem custos mais altos que apenas uma 'Somente Windows' máquina virtual implantado do Azure Marketplace. Consulte estes artigos para comparar os preços: <https://azure.microsoft.com/pricing/details/virtual-machines/> e <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql>. 
* Você só pode usar versões do SQL Server que são suportados pelo SAP, como o SQL Server 2012.
* O agrupamento da instância do SQL Server que é instalado em VMs oferecidas do Azure Marketplace não é o que SAP NetWeaver requer instância do SQL Server para executar. Você pode alterar o agrupamento embora com as instruções na seção a seguir.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterar o agrupamento de servidor SQL de um máquina virtual do Microsoft Windows/SQL Server
Desde que as imagens do SQL Server do Azure Marketplace não estão configuradas para usar o agrupamento que é requerido pelo SAP NetWeaver aplicativos, ele precisa ser alterada imediatamente após a implantação. Para o SQL Server 2012, isso pode ser feito com as seguintes etapas assim que a máquina virtual foi implantada e um administrador é capaz de login a máquina virtual implantada:

* Abra uma janela de comando do Windows 'como administrador'.
* Altere o diretório para C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Execute o comando: Setup.exe/de ação /QUIET = REBUILDDATABASE /INSTANCENAME = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
    * `<local_admin_account_name`> é a conta que foi definida como a conta de administrador ao implantar a máquina virtual pela primeira vez por meio da Galeria.

O processo só deve levar alguns minutos. Para garantir que se a etapa terminei com o resultado correto, execute as seguintes etapas:

* Abra o SQL Server Management Studio.
* Abra uma janela de consulta.
* Execute o comando sp_helpsort do banco de dados mestre do SQL Server.

O resultado desejado deve parecer com:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se esse não for o resultado, pare a implantação de SAP e investigar por que o comando de instalação não funcionam como esperado. Implantação de aplicativos SAP NetWeaver em instância do SQL Server com diferentes páginas de código do SQL Server daquele mencionado acima **não** é suportada.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server alta disponibilidade para SAP no Azure
Conforme mencionado anteriormente neste documento, não há nenhuma possibilidade criar armazenamento compartilhado que é necessário para o uso dos recursos de alta disponibilidade do SQL Server mais antigo. Essa funcionalidade seria instalar duas ou mais instâncias do SQL Server em um Windows Server Failover Cluster (WSFC) usando um disco compartilhado para os bancos de dados do usuário (e eventualmente tempdb). Este é o método de alta disponibilidade padrão de tempo que também é suportado pelo SAP. Como Azure não dá suporte a armazenamento compartilhado, configurações de alta disponibilidade do SQL Server com uma configuração de cluster de disco compartilhado não podem ser realizadas. No entanto, muitos outros métodos de alta disponibilidade ainda são possíveis e estão descritos nas seções a seguir.

[comentário]: <>  (Artigo está ainda fazendo referência a ASM)
[comentário]: <>  (Antes de ler as tecnologias específicas diferentes de alta disponibilidade utilizáveis para SQL Server no Azure, há um documento muito boa que oferece mais detalhes e ponteiros [here][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions])

#### <a name="sql-server-log-shipping"></a>Envio de Log do SQL Server
Um dos métodos de alta disponibilidade (HA) é o envio de Log do SQL Server. Se as VMs participando na configuração HA tem trabalhando resolução de nomes, não há nenhum problema e a configuração no Azure não será diferente de qualquer instalação que é feita no local. Não é recomendável depender somente resolução IP. Em relação ao configurar o envio de Log e os princípios em torno de envio de Log verificar esta documentação:

<https://technet.microsoft.com/library/ms187103.aspx>
 
Para realmente obter qualquer alta disponibilidade, é necessário implantar as VMs que estão dentro do tal envio de Log configuração estar dentro o mesmo conjunto disponibilidade do Azure.

#### <a name="database-mirroring"></a>Espelhamento de banco de dados
Espelhamento de banco de dados como suportada pelo SAP (consulte a Observação SAP [965908]) depende da definição de um parceiro de failover na cadeia de conexão de SAP. Para os casos entre locais, vamos supor que as duas VMs estão no mesmo domínio que o contexto de usuário as duas instâncias do SQL Server estão executando em são bem usuários de domínio e ter privilégios suficientes nas duas instâncias do SQL Server envolvidas. Portanto, a configuração de banco de dados espelhar no Azure não diferem entre uma configuração da instalação local típico.

A partir de implantações somente na nuvem, o método mais fácil é tem outra configuração de domínio no Azure tenha esses DBMS VMs (e o ideal seria dedicado VMs SAP) dentro de um domínio.

Se um domínio não for possível, é possível usar certificados para pontos de extremidade de espelhamento conforme descrito aqui: <https://technet.microsoft.com/library/ms191477.aspx>

Um tutorial para espelhamento de banco de dados de configuração no Azure pode ser encontrado aqui: <https://technet.microsoft.com/library/ms189852.aspx> 

#### <a name="alwayson"></a>Sempre ativado
Como sempre ativado é suportado para SAP local (consulte a Observação SAP [1772688]), há suporte para ser usado em combinação com a SAP no Azure. O fato de que não é possível criar discos compartilhados no Azure não significa que um não pode criar uma configuração de AlwaysOn Windows Server Failover Cluster (WSFC) entre VMs diferentes. Significa apenas que você não tenha a possibilidade de usar um disco compartilhado como um quorum na configuração do cluster. Portanto, é possível criar uma configuração de AlwaysOn WSFC no Azure e simplesmente não selecione o tipo de quorum que utiliza disco compartilhado. O ambiente do Azure essas VMs são implantadas em deve resolver VMs por nome e as VMs devem estar no mesmo domínio. Isso é verdadeiro para Azure somente e implantações entre locais. Há algumas considerações especiais para implantar o ouvinte de grupo de disponibilidade do servidor SQL (não confunda com o conjunto de disponibilidade do Azure) desde que o Azure neste momento não permite que simplesmente criar um objeto DNS/AD como é possível local. Portanto, algumas etapas de instalação diferentes são necessárias para resolver o comportamento específico do Azure.

Algumas considerações usando um ouvinte de grupo de disponibilidade são:

* Usar um ouvinte de grupo de disponibilidade só é possível com o Windows Server 2012 ou o Windows Server 2012 R2 como sistema operacional de convidado da máquina virtual. Para o Windows Server 2012 você precisa certificar-se de que esse patch é aplicado: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2 esse patch não existe e sempre ativado precisaria ser usada da mesma maneira como espelhamento de banco de dados especificando um parceiro de failover na cadeia de conexões (feito por meio do SAP default.pfl parâmetro bancos de dados/mss/servidor – Consulte a Observação SAP [965908]).
* Ao usar um ouvinte de grupo de disponibilidade, as VMs de banco de dados precisa estar conectado a um balanceador de carga dedicado. Resolução de nomes em implantações somente na nuvem ou exijam todas VMs de um sistema SAP (servidores de aplicativo, DBMS servidor e () SCS) estão na mesma rede virtual ou exijam de uma camada de aplicativo SAP a manutenção do arquivo etc\host para obter os nomes de máquina virtual do SQL Server VMs resolvido. Para evitar que o Azure está atribuindo novos endereços IP nos casos onde ambas as VMs são eventualmente desligamento, um deve atribuir endereços IP estáticos para as interfaces de rede daqueles VMs na configuração sempre ativado (definir um endereço IP estático é descrito [neste] [ virtual-networks-reserved-private-ip] artigo) [comentário]: <> (antigo blogs) [comentário]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx> <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Há etapas especiais necessárias ao criar a configuração de cluster WSFC onde cluster precisa de um endereço IP especial atribuída, porque o Azure com a funcionalidade atual seria atribuir o nome do cluster o mesmo endereço IP conforme o nó do cluster é criado no. Isso significa que uma etapa manual deve ser executada para atribuir um endereço IP diferente para o cluster.
* O ouvinte de grupo de disponibilidade vier a ser criado no Azure com pontos de extremidade de TCP/IP que são atribuídos a VMs executando as réplicas primárias e secundárias do grupo de disponibilidade.
* Pode haver a necessidade de proteger esses pontos de extremidade com ACLs.

[comentário]: <>  (Blog antigo TODO)
[comentário]: <>  (As etapas detalhadas e às necessidades de instalar uma configuração de sempre ativado no Azure melhor ocorrem quando examinando o [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups] disponíveis tutorial)
[comentário]: <>  (Configuração pré-configurado AlwaysOn via Galeria Azure < https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comentário]: <>  (Criar um ouvinte de grupo de disponibilidade é melhor descrito no tutorial [this][virtual-machines-windows-classic-ps-sql-int-listener])
[comentário]: <>  (Protegendo pontos de extremidade de rede com ACLs são explicados melhor aqui:)
[comentário]: <>  (* < https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comentário]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx>)
[comentário]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comentário]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

É possível implantar um grupo de disponibilidade do SQL Server AlwaysOn em diferentes regiões Azure também. Esta funcionalidade utilizará a conectividade Azure VNet para Vnet ([mais detalhes][virtual-networks-configure-vnet-to-vnet-connection]).
[comentário]: <> (TODO antigo blog) [comentário]: <> (na configuração de grupos de disponibilidade do SQL Server AlwaysOn em tal cenário é descrita aqui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Resumo no SQL Server alta disponibilidade no Azure
Considerando o fato de que o armazenamento do Azure está protegendo o conteúdo, há um menos motivo para insistem em uma imagem de espera ativo. Isso significa que seu cenário de alta disponibilidade precisa apenas proteger contra casos a seguir:

* Indisponibilidade da máquina virtual como um todo devido a manutenção no servidor cluster no Azure ou outras razões
* Problemas de software na instância do SQL Server
* Proteção contra erro manual onde dados são excluídos e recuperação no momento é necessária

Observando correspondentes tecnologias um pode discutir que os dois primeiros casos podem ser cobertos por espelhamento de banco de dados ou sempre ativado, enquanto o terceiro caso só pode ser coberto por envio de Log.

Você precisará saldo a configuração mais complexa do AlwaysOn, em comparação com o banco de dados espelhar, com as vantagens dos sempre ativado. Essas vantagens podem estar listadas como:

*   Réplicas secundárias legíveis.
*   Backups das réplicas secundários.
*   Melhor escalabilidade.
*   Mais de um réplicas secundário.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Geral SQL Server para SAP em resumo do Azure
Existem muitos recomendações neste guia e recomendamos que você lê-lo mais de uma vez antes de planejar sua implantação do Azure. Em geral, no entanto, certifique-se acompanhar o DBMS geral de dez principais no Azure pontos específicos:

[comentário]: <> taxa de transferência de  (2.3 maior que o que? Que um VHD?)
1. Use a versão mais recente do DBMS, como o SQL Server 2014, que tem a maioria das vantagens no Azure. Para SQL Server, este é o SQL Server 2012 SP1 CU4 que incluiria o recurso de apoio ao armazenamento do Azure. No entanto, em conjunto com SAP recomendamos pelo menos CU1 de SP1 de 2014 do SQL Server ou SQL Server 2012 SP2 e a atualização mais recente.
1. Planeje cuidadosamente seu cenário de sistema SAP no Azure equilíbrio entre o layout de arquivo de dados e restrições de Azure:
    * Não tem muitos VHDs, mas tem espaço suficiente para garantir que você pode acessar seu IOPS necessários.
    * Lembre-se de que IOPS também são limitados por conta de armazenamento do Azure e que contas de armazenamento são limitadas dentro de cada assinatura Azure ([mais detalhes][azure-subscription-service-limits]). 
    * Apenas distribuição entre VHDs se você precisar obter uma taxa de transferência maior.
1. Nunca instalar software ou coloque todos os arquivos que exigem persistência na unidade D:\ como é não permanente e nada nesta unidade serão perdido na reinicialização do Windows.
1. Não use o cache de VHD do Azure para armazenamento padrão do Azure.
1. Não use Azure contas de armazenamento replicado localização geográfica.  Use localmente redundantes para cargas de trabalho DBMS.
1. Use solução HA/DR do seu fornecedor DBMS para replicar o banco de dados.
1. Sempre usar resolução de nomes, não dependem de endereços IP.
1. Use a compactação de banco de dados mais alta possível. Para o SQL Server é compactação de página.
1. Tenha cuidado ao usar imagens do SQL Server do Azure Marketplace. Se você usa um SQL Server, você deve alterar o agrupamento de instância antes de instalar qualquer sistema SAP NetWeaver nele.
1. Instalar e configurar o monitoramento de Host SAP para Azure conforme descrito em [guia de implantação] [-guia de implantação].

## <a name="specifics-to-sap-ase-on-windows"></a>Especificações do SAP ASE no Windows
Começando com o Microsoft Azure, você pode facilmente migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. ASE SAP em uma máquina Virtual permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de abrangência corporativos migrando facilmente esses aplicativos Microsoft Azure. Com o SAP ASE em uma máquina Virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmo ferramentas de desenvolvimento e administração que estão disponíveis no local.

Não há um SLA para as máquinas virtuais do Azure que podem ser encontradas aqui: <https://azure.microsoft.com/support/legal/sla>

Temos certeza de que o Microsoft Azure hospedado máquinas virtuais executará muito bem em comparação com outras ofertas de virtualização de nuvem pública, mas resultados individuais podem variar. Números de SAPS dimensionamento SAP da SAP diferentes certified que SKUs de máquina virtual serão fornecidos em uma anotação de SAP separada [1928533].

Instruções e recomendações sobre o uso de armazenamento do Azure, implantação de VMs SAP ou SAP Monitoring se aplicam a implantações de SAP ASE junto com os aplicativos da SAP, tal como consta em todo os quatro primeiros capítulos deste documento.

### <a name="sap-ase-version-support"></a>Suporte a versão de ASE SAP 
SAP atualmente suporta SAP ASE versão 16.0 para uso com produtos do SAP Business Suite. Todas as atualizações para o servidor do SAP ASE ou drivers JDBC e ODBC a ser usado com produtos do SAP Business Suite são fornecidas somente por meio de SAP Service Marketplace em: <https://support.sap.com/swdc>.

Para instalações no local, não baixe atualizações para o servidor SAP ASE, ou os drivers JDBC e ODBC diretamente do Sybase sites. Para obter informações detalhadas sobre patches que têm suporte para uso com o SAP Business Suite produtos local e em máquinas virtuais do Azure ver as seguintes observações SAP:

* [1590719]
* [1973241]
 
Informações gerais sobre como executar SAP Business Suite em SAP ASE podem ser encontradas no [SCN](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para SAP relacionados SAP ASE instalações em VMs do Azure

#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implantação ASE SAP
De acordo com a descrição geral, SAP ASE executáveis devem ser localizado ou instalado na unidade de sistema do VHD base da VM (unidade c:\). Normalmente, a maioria dos SAP ASE ferramentas de sistema e bancos de dados não é realmente utilizada irreversível por carga de trabalho do SAP NetWeaver. Portanto, os ferramentas de sistema e bancos de dados (mestre, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecer no C:\drive também. 

Uma exceção poderia ser o banco de dados temporário contendo todas as tabelas de trabalho e temporárias tabelas criadas pela SAP ASE, que pode exigir mais alto volume de dados ou volume de operações de e/s que não se encaixa no VHD base da máquina virtual original no caso de alguns SAP ERP e todas as cargas de trabalho de p & b (unidade c:\).
 
Dependendo da versão do SAPInst/SWPM usada para instalar o sistema, o banco de dados pode conter:

* Um único tempdb ASE SAP que é criado durante a instalação do ASE SAP
* Um tempdb SAP ASE criado pela instalação ASE SAP e um saptempdb adicional criadas pela rotina de instalação do SAP
* Um tempdb SAP ASE criado pela instalação ASE SAP e um tempdb adicional que foi criada manualmente (por exemplo seguinte Observação SAP [1752266]) para atender aos requisitos de tempdb específico de ERP/p & b

No caso de ERP específica ou todas as cargas de trabalho de p & b faz sentido, em relação ao desempenho, manter os dispositivos de tempdb do tempdb Além disso criado (por SWPM ou manualmente) em uma unidade diferente de c:\. se nenhum tempdb adicional existi-lo é recomendado para criar uma (Observação SAP [1752266]).

Para esses sistemas as etapas a seguir devem ser executadas para o tempdb criado Além disso:

* Mova o primeiro dispositivo tempdb para o primeiro dispositivo do banco de dados SAP
* Adicionar dispositivos tempdb a cada os VHDs contendo um dispositivos do banco de dados SAP

Essa configuração permite tempdb ou consumir mais espaço do que a unidade do sistema é capaz de fornecer. Como uma referência um pode verificar os tamanhos de dispositivo tempdb em sistemas existentes que são executados no local. Essa configuração deve habilitar ou números de IOPS contra tempdb que não pode ser fornecido com a unidade do sistema. Novamente, sistemas que executam o local podem ser usados para monitorar a carga de trabalho e/s contra tempdb.

Nunca coloque quaisquer dispositivos SAP ASE na unidade D:\ da máquina virtual. Isso também se aplica a tempdb, mesmo se os objetos mantidos no tempdb são apenas temporários.

#### <a name="impact-of-database-compression"></a>Impacto da compactação de banco de dados
Em configurações onde largura de banda de e/s pode se tornar um fator de limitação, todas as medidas que reduz IOPS podem ajudar a alongar a carga de trabalho que podem ser executados em um cenário de IaaS como o Azure. Portanto, é altamente recomendável para certificar-se de que a compactação de SAP ASE é usada antes de carregar um banco de dados existente do SAP para o Azure.

A recomendação para executar compactação antes de carregar no Azure se ela já não é implementada é dado fora vários motivos:

* A quantidade de dados a ser carregado Azure é menor
* A duração da execução compactação é menor, supondo que é possível usar hardware mais forte com mais CPUs ou largura de banda de i/o maior ou menor e/s latência local
* Menores de banco de dados podem levar menos custos de alocação em disco

Compactação de dados e LOB funcionem em uma máquina virtual hospedada em máquinas virtuais do Azure como faz no local. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados existente do SAP ASE Verifique SAP anotação [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando DBACockpit para monitorar instâncias de banco de dados
Para os sistemas SAP que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit é acessível como janelas de navegador incorporado em transação DBACockpit ou Webdynpro. Entretanto, a funcionalidade total para monitoramento e administrando o banco de dados está disponível na implementação Webdynpro do somente o DBACockpit.

Como com sistemas locais várias etapas são necessárias para habilitar todas as funcionalidades do SAP NetWeaver usada pela implementação Webdynpro da DBACockpit. Siga SAP anotação [1245200] para habilitar o uso da webdynpros e gerar aquelas necessárias. Quando seguindo as instruções nas notas acima também configurará o Gerenciador de comunicação da Internet (icm) juntamente com as portas a ser usado para conexões http e https. A configuração padrão para http tem esta aparência:

> ICM/server_port_0 = PROT = HTTP, porta = 8000, PROCTIMEOUT = 600, tempo limite = 600
>
> ICM/server_port_1 = PROT = HTTPS, porta = $$ 443, PROCTIMEOUT = 600, tempo limite = 600

e os links gerados em transação DBACockpit terá uma aparência semelhante a esta:

> https://`<fullyqualifiedhostname`>: 44300/sap/continuidade de negócios/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: 8000/sap/continuidade de negócios/webdynpro/sap/dba_cockpit

Dependendo se e como a máquina Virtual do Azure hospedando o sistema SAP está conectada por meio de-to-site, vários locais ou rota expressa (implantação local cruzado), você precisa garantir que ICM está usando um nome de host totalmente qualificado que pode ser resolvido no computador onde você está tentando abrir o DBACockpit de. Consulte Observação SAP [773830] entender como ICM determina o nome de host totalmente qualificado dependendo de parâmetros de perfil e defina o parâmetro icm/host_name_full explicitamente se necessário.

Se você implantou a máquina virtual em um cenário de somente na nuvem sem conectividade entre locais entre locais e Azure, você precisa definir um endereço IP público e um domainlabel. O formato do nome do DNS público da máquina virtual, em seguida, ficará assim:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com

Mais detalhes relacionados ao nome DNS podem ser encontradas [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o icm host_name_full parâmetro SAP perfil para o nome DNS da máquina virtual do Azure o link pode ser semelhante a:

> https://mydomainlabel.westeurope.cloudapp.NET:44300/sap/continuidade de negócios/webdynpro/sap/dba_cockpit

> http://mydomainlabel.westeurope.cloudapp.NET:8000/sap/continuidade de negócios/webdynpro/sap/dba_cockpit

Nesse caso, você precisa Certifique-se de:

* Adicionar regras de entrada para o grupo de segurança de rede no Portal do Azure para as portas TCP/IP usadas para se comunicar com ICM
* Adicionar regras de entrada para a configuração de Firewall do Windows para as portas TCP/IP usado para se comunicar com o ICM

Para um automatizada importados de todas as correções disponíveis, é recomendável aplicar periodicamente a coleção de correção SAP Observação aplicável à sua versão do SAP:

* [1558958]
* [1619967]
* [1882376]

Mais informações sobre DBA Cockpit para SAP ASE podem ser encontradas nas notas de SAP a seguir:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496] 
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações de backup/recuperação para ASE SAP
Quando implantar ASE SAP em Azure sua metodologia de backup deve ser revisada. Mesmo se o sistema não é um sistema produtivo, o banco de dados do SAP hospedado pelo SAP ASE deve ser copiado periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em relação às compensating uma falha de armazenamento. O principal motivo para manter um plano de backup e restauração adequado é mais que você pode compensar erros lógicos/manual, fornecendo ponto em recursos de recuperação de tempo. Para que o objetivo é um dos backups de uso para restaurar o banco de dados para um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração de SAP 2 camadas para a configuração de um sistema de 3 camadas do mesmo sistema restaurando um backup.

Fazer backup e restaurar um banco de dados no Azure funcionam da mesma maneira como faz no local. Consulte Observações SAP:

* [1588316]
* [1585981]

Para obter detalhes sobre configurações de despejo criando e agendamento de backups. Dependendo de sua estratégia e necessidades, você pode configurar o banco de dados e log descarta para disco em um dos VHDs existentes ou adicionar um VHD adicional para o backup.  Para reduzir o risco de perda de dados no caso de um erro é recomendável usar um VHD onde se encontra nenhum dispositivo de banco de dados.

Além de dados e LOB compactação SAP ASE também oferece compactação de backup. Para ocupar menos espaço com os despejos de banco de dados e log é recomendável usar a compactação de backup. Consulte a Observação SAP [1588316] para obter mais informações. Compactar o backup também é fundamental para reduzir a quantidade de dados a serem transferidos se você planeja baixar backups ou VHDs contendo despejos de backup da máquina Virtual do Azure para o local.

Não use unidade D:\ como destino de despejo de banco de dados ou log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações sobre desempenho para Backups/restaura
Como em implantações de depender, desempenho de backup e restauração depende quantos volumes podem ser lido em paralelo e que pode ser a produtividade desses volumes. Além disso, o consumo de CPU usado pelo compactação de backup pode ser reproduzidos uma função significativa em VMs com apenas até 8 threads de CPU. Portanto, um pode assumir:

* Quanto menor o número de VHDs usada para armazenar os dispositivos de banco de dados, menor a produtividade geral em leitura
* Menor que número de CPU threads a máquina virtual, a mais grave o impacto da compactação de backup
* Os destinos de menos (diretórios de distribuição, VHDs) para gravar o backup, a menor a taxa de transferência

Para aumentar o número de destinos para gravar que há duas opções que pode ser usado/combinados dependendo das suas necessidades:

* Distribuição o volume de destino de backup sobre diversos VHDs montados para melhorar a produtividade IOPS nesse volume distribuído
* Criando uma configuração de despejo no nível de ASE SAP que usa mais de uma pasta de destino para gravar o despejo para

Distribuição um volume sobre diversos VHDs montados foi discutido neste guia. Para obter mais informações sobre como usar vários diretórios na configuração de despejo do SAP ASE consulte a documentação sobre sp_config_dump de procedimento armazenado que é usado para criar a configuração de despejo sobre o [Centro de informações de Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de dados com VMs Azure

#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o SAP Sybase servidor SRS (replicação) SAP ASE fornece uma solução de espera quente para transferir transações de banco de dados para um local distante assíncrona. 

A instalação e a operação de SRS funciona bem funcionalidade em uma máquina virtual hospedada em serviços de máquina Virtual do Azure como faz no local.

HADR ASE através do servidor de replicação do SAP está planejado com uma versão futura. Ele será testado com e lançado para plataformas do Microsoft Azure assim que ele fique disponível.

## <a name="specifics-to-sap-ase-on-linux"></a>Especificações do SAP ASE no Linux

Começando com o Microsoft Azure, você pode facilmente migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. ASE SAP em uma máquina Virtual permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de abrangência corporativos migrando facilmente esses aplicativos Microsoft Azure. Com o SAP ASE em uma máquina Virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmo ferramentas de desenvolvimento e administração que estão disponíveis no local.

Para a implantação do Azure VMs é importante saber os SLAs oficiais que podem ser encontrados aqui: <https://azure.microsoft.com/support/legal/sla>

Informações de dimensionamento do SAP e uma lista de SAP certificada SKUs de máquina virtual serão fornecidas na anotação SAP [1928533]. SAP adicional dimensionamento documentos para máquinas virtuais do Azure pode ser encontrada aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Instruções e recomendações sobre o uso de armazenamento do Azure, implantação de VMs SAP ou SAP Monitoring se aplicam a implantações de SAP ASE junto com os aplicativos da SAP, tal como consta em todo os quatro primeiros capítulos deste documento.

As seguintes observações SAP dois incluem informações gerais sobre ASE no Linux e ASE na nuvem:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Suporte a versão de ASE SAP 
SAP atualmente suporta SAP ASE versão 16.0 para uso com produtos do SAP Business Suite. Todas as atualizações para o servidor do SAP ASE ou drivers JDBC e ODBC a ser usado com produtos do SAP Business Suite são fornecidas somente por meio de SAP Service Marketplace em: <https://support.sap.com/swdc>.

Para instalações no local, não baixe atualizações para o servidor SAP ASE, ou os drivers JDBC e ODBC diretamente do Sybase sites. Para obter informações detalhadas sobre patches que têm suporte para uso com o SAP Business Suite produtos local e em máquinas virtuais do Azure ver as seguintes observações SAP:

* [1590719]
* [1973241]
 
Informações gerais sobre como executar SAP Business Suite em SAP ASE podem ser encontradas no [SCN](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para SAP relacionados SAP ASE instalações em VMs do Azure

#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implantação ASE SAP
De acordo com a descrição geral, SAP ASE executáveis devem ser localizado ou instalado no sistema de arquivos raiz da máquina virtual (/sybase). Normalmente, a maioria dos SAP ASE ferramentas de sistema e bancos de dados não é realmente utilizada irreversível por carga de trabalho do SAP NetWeaver. Portanto, ferramentas de sistema e bancos de dados (mestre, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecer no sistema de arquivos raiz também. 

Uma exceção poderia ser o banco de dados temporário contendo todas as tabelas de trabalho e temporárias tabelas criadas pela SAP ASE, que, no caso de alguns SAP ERP e todas as cargas de trabalho de p & b, pode exigir maior volume de dados ou volume de operações de e/s que não se encaixa no disco do sistema operacional da máquina virtual original.
 
Dependendo da versão do SAPInst/SWPM usada para instalar o sistema, o banco de dados pode conter:

* Um único tempdb ASE SAP que é criado durante a instalação do ASE SAP
* Um tempdb SAP ASE criado pela instalação ASE SAP e um saptempdb adicional criadas pela rotina de instalação do SAP
* Um tempdb SAP ASE criado pela instalação ASE SAP e um tempdb adicional que foi criada manualmente (por exemplo seguinte Observação SAP [1752266]) para atender aos requisitos de tempdb específico de ERP/p & b

No caso de ERP específica ou todas as cargas de trabalho de p & b faz sentido, em relação ao desempenho, manter os dispositivos de tempdb do tempdb Além disso criado (por SWPM ou manualmente) em um sistema de arquivo separado que pode ser representado por um disco de dados Azure único ou RAID Linux que se estendem por vários discos de dados do Azure. Se nenhuma tempdb adicional existe-é recomendável criar uma (Observação SAP [1752266]).

Para esses sistemas as etapas a seguir devem ser executadas para o tempdb criado Além disso:

* Mover o diretório de tempdb primeiro para o primeiro sistema de arquivo do banco de dados SAP
* Adicionar diretórios tempdb a cada os VHDs contendo um sistema de arquivos do banco de dados SAP

Essa configuração permite tempdb ou consumir mais espaço do que a unidade do sistema é capaz de fornecer. Como uma referência um pode verificar os tamanhos de diretório tempdb em sistemas existentes que são executados no local. Essa configuração deve habilitar ou números de IOPS contra tempdb que não pode ser fornecido com a unidade do sistema. Novamente, sistemas que executam o local podem ser usados para monitorar a carga de trabalho e/s contra tempdb.

Nunca coloque quaisquer diretórios de SAP ASE /mnt ou /mnt/resource da máquina virtual. Isso também se aplica a tempdb, mesmo se os objetos mantidos no tempdb são apenas temporários porque /mnt ou /mnt/resource é um espaço de máquina virtual do Azure temp de padrão que não é persistente. Mais detalhes sobre o espaço de temp de máquina virtual do Azure podem ser encontradas [neste] artigo[virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Impacto da compactação de banco de dados
Em configurações onde largura de banda de e/s pode se tornar um fator de limitação, todas as medidas que reduz IOPS podem ajudar a alongar a carga de trabalho que podem ser executados em um cenário de IaaS como o Azure. Portanto, é altamente recomendável para certificar-se de que a compactação de SAP ASE é usada antes de carregar um banco de dados existente do SAP para o Azure.

A recomendação para executar compactação antes de carregar no Azure se ela já não é implementada é dado fora vários motivos:

* A quantidade de dados a ser carregado Azure é menor
* A duração da execução compactação é menor, supondo que é possível usar hardware mais forte com mais CPUs ou largura de banda de i/o maior ou menor e/s latência local
* Menores de banco de dados podem levar menos custos de alocação em disco

Compactação de dados e LOB funcionem em uma máquina virtual hospedada em máquinas virtuais do Azure como faz no local. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados existente do SAP ASE Verifique SAP anotação [1750510]. Consulte também SAP anotação [2121797] para obter informações adicionais sobre a compactação de banco de dados.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando DBACockpit para monitorar instâncias de banco de dados
Para os sistemas SAP que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit é acessível como janelas de navegador incorporado em transação DBACockpit ou Webdynpro. Entretanto, a funcionalidade total para monitoramento e administrando o banco de dados está disponível na implementação Webdynpro do somente o DBACockpit.

Como com sistemas locais várias etapas são necessárias para habilitar todas as funcionalidades do SAP NetWeaver usada pela implementação Webdynpro da DBACockpit. Siga SAP anotação [1245200] para habilitar o uso da webdynpros e gerar aquelas necessárias. Quando seguindo as instruções nas notas acima também configurará o Gerenciador de comunicação da Internet (icm) juntamente com as portas a ser usado para conexões http e https. A configuração padrão para http tem esta aparência:

> ICM/server_port_0 = PROT = HTTP, porta = 8000, PROCTIMEOUT = 600, tempo limite = 600
>
> ICM/server_port_1 = PROT = HTTPS, porta = $$ 443, PROCTIMEOUT = 600, tempo limite = 600

e os links gerados em transação DBACockpit terá uma aparência semelhante a esta:

> https://`<fullyqualifiedhostname`>: 44300/sap/continuidade de negócios/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: 8000/sap/continuidade de negócios/webdynpro/sap/dba_cockpit

Dependendo se e como a máquina Virtual do Azure hospedando o sistema SAP está conectada por meio de-to-site, vários locais ou rota expressa (implantação local cruzado), você precisa garantir que ICM está usando um nome de host totalmente qualificado que pode ser resolvido no computador onde você está tentando abrir o DBACockpit de. Consulte Observação SAP [773830] entender como ICM determina o nome de host totalmente qualificado dependendo de parâmetros de perfil e defina o parâmetro icm/host_name_full explicitamente se necessário.

Se você implantou a máquina virtual em um cenário de somente na nuvem sem conectividade entre locais entre locais e Azure, você precisa definir um endereço IP público e um domainlabel. O formato do nome do DNS público da máquina virtual, em seguida, ficará assim:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com

Mais detalhes relacionados ao nome DNS podem ser encontradas [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o icm host_name_full parâmetro SAP perfil para o nome DNS da máquina virtual do Azure o link pode ser semelhante a:

> https://mydomainlabel.westeurope.cloudapp.NET:44300/sap/continuidade de negócios/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.NET:8000/sap/continuidade de negócios/webdynpro/sap/dba_cockpit

Nesse caso, você precisa Certifique-se de:

* Adicionar regras de entrada para o grupo de segurança de rede no Portal do Azure para as portas TCP/IP usadas para se comunicar com ICM
* Adicionar regras de entrada para a configuração de Firewall do Windows para as portas TCP/IP usado para se comunicar com o ICM

Para um automatizada importados de todas as correções disponíveis, é recomendável aplicar periodicamente a coleção de correção SAP Observação aplicável à sua versão do SAP:

* [1558958]
* [1619967]
* [1882376]

Mais informações sobre DBA Cockpit para SAP ASE podem ser encontradas nas notas de SAP a seguir:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496] 
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações de backup/recuperação para ASE SAP
Quando implantar ASE SAP em Azure sua metodologia de backup deve ser revisada. Mesmo se o sistema não é um sistema produtivo, o banco de dados do SAP hospedado pelo SAP ASE deve ser copiado periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em relação às compensating uma falha de armazenamento. O principal motivo para manter um plano de backup e restauração adequado é mais que você pode compensar erros lógicos/manual, fornecendo ponto em recursos de recuperação de tempo. Para que o objetivo é um dos backups de uso para restaurar o banco de dados para um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração de SAP 2 camadas para a configuração de um sistema de 3 camadas do mesmo sistema restaurando um backup.

Fazer backup e restaurar um banco de dados no Azure funcionam da mesma maneira como faz no local. Consulte Observações SAP:

* [1588316]
* [1585981]

Para obter detalhes sobre configurações de despejo criando e agendamento de backups. Dependendo de sua estratégia e necessidades, você pode configurar o banco de dados e log descarta para disco em um dos VHDs existentes ou adicionar um VHD adicional para o backup.  Para reduzir o risco de perda de dados no caso de um erro é recomendável usar um VHD onde se encontra nenhum diretório/arquivo de banco de dados.

Além de dados e LOB compactação SAP ASE também oferece compactação de backup. Para ocupar menos espaço com os despejos de banco de dados e log é recomendável usar a compactação de backup. Consulte a Observação SAP [1588316] para obter mais informações. Compactar o backup também é fundamental para reduzir a quantidade de dados a serem transferidos se você planeja baixar backups ou VHDs contendo despejos de backup da máquina Virtual do Azure para o local.

Não use a máquina virtual do Azure espaço temporário /mnt ou /mnt/resource como destino de despejo de banco de dados ou log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações sobre desempenho para Backups/restaura
Como em implantações de depender, desempenho de backup e restauração depende quantos volumes podem ser lido em paralelo e que pode ser a produtividade desses volumes. Além disso, o consumo de CPU usado pelo compactação de backup pode ser reproduzidos uma função significativa em VMs com apenas até 8 threads de CPU. Portanto, um pode assumir:

* Quanto menor o número de VHDs usada para armazenar os dispositivos de banco de dados, menor a produtividade geral em leitura
* Menor que número de CPU threads a máquina virtual, a mais grave o impacto da compactação de backup
* Os destinos de menos (Linux software RAID, VHDs) para gravar o backup, a menor a taxa de transferência

Para aumentar o número de destinos para gravar que há duas opções que pode ser usado/combinados dependendo das suas necessidades:

* Distribuição o volume de destino de backup sobre diversos VHDs montados para melhorar a produtividade IOPS nesse volume distribuído
* Criando uma configuração de despejo no nível de ASE SAP que usa mais de uma pasta de destino para gravar o despejo para

Distribuição um volume sobre diversos VHDs montados foi discutido neste guia. Para obter mais informações sobre como usar vários diretórios na configuração de despejo do SAP ASE consulte a documentação sobre sp_config_dump de procedimento armazenado que é usado para criar a configuração de despejo sobre o [Centro de informações de Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de dados com VMs Azure

#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o servidor de replicação do SAP Sybase
Com o SAP Sybase servidor SRS (replicação) SAP ASE fornece uma solução de espera quente para transferir transações de banco de dados para um local distante assíncrona. 

A instalação e a operação de SRS funciona bem funcionalidade em uma máquina virtual hospedada em serviços de máquina Virtual do Azure como faz no local.

HADR ASE através do servidor de replicação do SAP não é suportado neste momento. Ele pode ser testado com e lançado para plataformas do Microsoft Azure no futuro.

## <a name="specifics-to-oracle-database-on-windows"></a>Especificações do banco de dados Oracle no Windows
Desde meio do ano 2013, o software Oracle é compatível com Oracle para ser executado no Microsoft Windows Hyper-V e Azure. Leia este artigo para obter mais detalhes sobre o suporte geral do Windows Hyper-V e Azure pela Oracle: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Após o suporte geral, o cenário específico dos aplicativos SAP aproveitando bancos de dados Oracle é suportado também. Detalhes são nomeados nesta parte do documento.

### <a name="oracle-version-support"></a>Suporte de versão do Oracle
Todos os detalhes sobre versões do Oracle e versões de sistema operacional correspondentes que têm suporte para execução SAP em Oracle em máquinas virtuais do Azure podem ser encontrados na seguir de anotação do SAP [2039619]

Informações gerais sobre como executar SAP Business Suite em Oracle podem ser encontradas na SCN: <https://scn.sap.com/community/oracle>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do Oracle para instalações de SAP em VMs Azure

#### <a name="storage-configuration"></a>Configuração de armazenamento
Apenas instância Oracle usando discos NTFS formatado é suportada. Todos os arquivos de banco de dados devem ser armazenados no sistema de arquivos NTFS com base em discos VHD. Estas VHDs são montados para a máquina virtual do Azure e são baseados no armazenamento de BLOB do Azure página (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Qualquer tipo de unidades de rede ou compartilhamentos remotos como os serviços de arquivo Azure:
 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/Persisting-Connections-to-Microsoft-Azure-Files.aspx>
 
**não** há suporte para arquivos de banco de dados do Oracle!

Usando o Azure VHDs com base em armazenamento de BLOB de página do Azure, as declarações feitas neste documento capítulo [cache para VMs e VHDs] [dbms-guia-2.1] e [armazenamento do Microsoft Azure] [dbms-guia-2,3] se aplicam a implantações com o banco de dados Oracle.

Conforme explicado anteriormente na parte geral do documento, existem cotas em produtividade IOPS para VHDs do Azure. As cotas exatas são dependendo do tipo de máquina virtual usadas. Uma lista dos tipos de máquina virtual com suas cotas pode ser encontrada [aqui][virtual-machines-sizes]

Para identificar os tipos de máquina virtual do Azure com suporte, consulte SAP anotação [1928533]

Desde que a cota IOPS atual por disco satisfizer os requisitos, é possível armazenar todos os arquivos de banco de dados em um único VHD de Azure montado. 

Se mais IOPS forem necessários, é altamente recomendável usar Pools de armazenamento de janela (somente disponível no Windows Server 2012 e superior) ou distribuição do Windows para o Windows 2008 R2 para criar um grande dispositivo lógico através de vários discos VHD montados. Consulte também capítulo [Software RAID] [dbms-guia-2.2] deste documento. Essa abordagem simplifica a sobrecarga de administração para gerenciar o espaço em disco e evita o esforço para distribuir manualmente os arquivos em vários VHDs montados.

#### <a name="backup--restore"></a>Backup e restauração
Para backup / restaurar a funcionalidade, SAP BR * Tools for Oracle têm suporte da mesma maneira como no Hyper-V e sistemas operacionais Windows Server padrão. Também é compatível com o Gerenciador de recuperação de Oracle (RMAN) para backups em disco e restaurar do disco.

#### <a name="high-availability"></a>Alta disponibilidade
[comentário]: <>  (link se refere a ASM)
Proteção de dados Oracle tem suporte para fins de recuperação de desastres e alta disponibilidade. Detalhes podem ser encontrados neste [] [ virtual-machines-windows-classic-configure-oracle-data-guard] documentação.

#### <a name="other"></a>Outros
Todos os outros tópicos gerais como o monitoramento de conjuntos de disponibilidade do Azure ou SAP aplicam conforme descrito nos três primeiros capítulos deste documento para implantações de VMs com o banco de dados Oracle.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Especificações do banco de dados de MaxDB SAP no Windows

### <a name="sap-maxdb-version-support"></a>Suporte a versão de MaxDB SAP
SAP atualmente suporta SAP MaxDB versão 7,9 para uso com produtos baseados em SAP NetWeaver no Azure. Todas as atualizações para o servidor do SAP MaxDB ou drivers JDBC e ODBC a ser usado com produtos baseados em SAP NetWeaver são fornecidas exclusivamente através do SAP Service Marketplace no <https://support.sap.com/swdc>.
Informações gerais sobre como executar SAP NetWeaver em SAP MaxDB podem ser encontradas em <https://scn.sap.com/community/maxdb>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Suporte para tipos de versões do Microsoft Windows e máquina virtual do Azure para SAP MaxDB DBMS
Para localizar a versão do Microsoft Windows com suporte para SAP MaxDB DBMS no Azure, consulte:

* [Matriz de disponibilidade de produtos do SAP (PAM)][sap-pam]
* Observação SAP [1928533]

É altamente recomendável usar a versão mais recente do sistema operacional Microsoft Windows, que é o Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Documentação do SAP disponível MaxDB
Você pode encontrar a lista atualizada de SAP MaxDB documentação a seguir de anotação do SAP [767598]
    
### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP MaxDB para instalações de SAP em VMs Azure

#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuração de armazenamento
Práticas recomendadas de armazenamento do Azure para SAP MaxDB siga as recomendações gerais mencionadas capítulo [estrutura de uma implantação de RDBMS] [dbms-guia-2].

> [AZURE.IMPORTANT] Como outros bancos de dados, SAP MaxDB também tem dados e arquivos de log. No entanto, na terminologia SAP MaxDB o termo correto é "volume" (não "arquivo"). Por exemplo, há MaxDB SAP volumes de dados e de log. Não confunda essas com volumes de disco do sistema operacional. 

Em resumo, você terá que:

* Defina a conta de armazenamento do Azure que tem os SAP MaxDB dados e volumes de log (isto é, arquivos) ao **Armazenamento redundantes Local (LRS)** como especificado no capítulo [armazenamento do Microsoft Azure] [dbms-guia-2,3].
* Separe o caminho de IO para volumes de dados do SAP MaxDB (ou seja, arquivos) do caminho IO para volumes de log (isto é, arquivos). Isso significa que volumes de dados do SAP MaxDB (ou seja, arquivos) precisam ser instalado em uma unidade lógica e volumes de log do SAP MaxDB (ou seja, arquivos) precisam ser instalado em outra unidade lógica.
* Definir o cache de arquivo adequado para cada Azure blob, dependendo se você usá-lo para SAP MaxDB dados ou log volumes (ou seja, arquivos), e se você usar Azure padrão ou armazenamento do Azure Premium, conforme descrito no capítulo [cache para VMs] [dbms-guia-2.1].
* Desde que a cota IOPS atual por disco satisfizer os requisitos, é possível armazenar todos os volumes de dados em um único VHD montado do Azure e também armazenar todos os volumes de log de banco de dados em outra único VHD de Azure montado.
* Se mais IOPS e/ou espaço forem necessário, é altamente recomendável usar Pools de armazenamento do Microsoft janela (somente disponível no Microsoft Windows Server 2012 e superior) ou distribuição do Microsoft Windows para o Microsoft Windows 2008 R2 para criar um grande dispositivo lógico através de vários discos VHD montados. Consulte também capítulo [Software RAID] [dbms-guia-2.2] deste documento. Essa abordagem simplifica a sobrecarga de administração para gerenciar o espaço em disco e evita o esforço de distribuindo manualmente arquivos em vários VHDs montados.
* Para requisitos de IOPS mais altos, você pode usar o armazenamento do Azure Premium, que está disponível em série DS e série GS VMs.

![Configuração de referência do Azure IaaS máquina virtual SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Backup e restauração
Ao implantar MaxDB do SAP para o Azure, você deve examinar sua metodologia de backup. Mesmo se o sistema não é um sistema produtivo, o banco de dados do SAP hospedado pelo SAP MaxDB deve ser copiado periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em termos de proteger seu sistema contra falha de armazenamento e falhas de administrativas ou operacionais mais importantes. O principal motivo para manter uma adequada de backup e restauração plano é para que você pode compensar erros lógicos ou manuais, fornecendo recursos de recuperação no momento. Para que o objetivo é usar backups para restaurar o banco de dados para um determinado ponto no tempo ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração de SAP 2 camadas para a configuração de um sistema de 3 camadas do mesmo sistema restaurando um backup.

Fazer backup e restaurar um banco de dados no Azure funcionam da mesma maneira como faz para sistemas de locais, para que você possa usar ferramentas de backup e restauração SAP MaxDB padrão, que são descritas em um dos documentos SAP MaxDB documentação listados na anotação SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerações sobre o desempenho de Backup e restauração
Como em implantações de depender, o desempenho de backup e restauração depende quantos volumes podem ser lidos em paralelo e a produtividade desses volumes. Além disso, o consumo de CPU usado pelo compactação de backup pode reproduzir uma função significativa no VMs com até 8 threads de CPU. Portanto, um pode assumir:

* Quanto menor o número de VHDs usada para armazenar os dispositivos de banco de dados, menor a produtividade geral de leitura
* Menor que número de CPU threads a máquina virtual, a mais grave o impacto da compactação de backup
* Os destinos de menos (diretórios de distribuição, VHDs) para gravar o backup, menor a taxa de transferência

Para aumentar o número de destinos para gravar, há duas opções que você pode usar, possivelmente em combinação, dependendo das suas necessidades:

* Dedicar volumes separados para backup
* Distribuição o volume de destino de backup sobre diversos VHDs montados para melhorar a produtividade IOPS nesse volume de disco distribuído
* Com dispositivos de disco lógico dedicado separado para:
    * SAP volumes de backup MaxDB (ou seja, arquivos)
    * Volumes de dados de MaxDB (ou seja, arquivos) do SAP
    * Volumes de log de MaxDB (ou seja, arquivos) do SAP

Distribuição um volume sobre diversos VHDs montados foi discutido anteriormente no capítulo [Software RAID] [dbms-guia-2.2] deste documento. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Outros
Todos os outros tópicos gerais como monitoramento conjuntos de disponibilidade do Azure ou SAP também se aplicam a conforme descrito nos três primeiros capítulos deste documento para implantações de VMs com o banco de dados do SAP MaxDB.
Outras configurações específicas de SAP MaxDB são transparentes para VMs do Azure e são descritas nos documentos diferentes listados na anotação SAP [767598] e nestas notas do SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Especificações do SAP liveCache no Windows

### <a name="sap-livecache-version-support"></a>LiveCache suporte à versão do SAP
Versão mínima do SAP liveCache com suporte em máquinas virtuais do Azure é **SAP LC/LCAPPS 10.0 SP 25** incluindo **liveCache 7.9.08.31** e **25 LCA-Build**lançadas para **EhP 2 para SAP SCM 7.0** e superior.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Suporte a tipos de versões do Microsoft Windows e máquina virtual do Azure para SAP liveCache DBMS
Para localizar a versão do Microsoft Windows com suporte para liveCache do SAP no Azure, consulte:

* [Matriz de disponibilidade de produtos do SAP (PAM)][sap-pam]
* Observação SAP [1928533]

É altamente recomendável usar a versão mais recente do sistema operacional Microsoft Windows, que é o Microsoft Windows 2012 R2. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache diretrizes de configuração para instalações de SAP em VMs do Azure

#### <a name="recommended-azure-vm-types"></a>Recomendado tipos de máquina virtual Azure
Como liveCache do SAP é um aplicativo que realiza cálculos enormes, a quantidade e a velocidade de RAM e CPU tem uma principal influência sobre o desempenho de liveCache do SAP. 

Para os tipos de máquina virtual do Azure suportados pelo SAP (Observação SAP [1928533]), todos os recursos de CPU virtuais alocados para a máquina virtual contam com recursos de CPU físicos dedicados do hipervisor. Ocorrerá nenhum excesso de provisionamento (e, portanto, não há concorrência pelos recursos de CPU).

Da mesma forma, para todos os tipos de instância de máquina virtual do Azure suportados pelo SAP, a memória de máquina virtual é 100% mapeado para a memória física – excesso de provisionamento (compromisso excedente), por exemplo, não é usado.

Dessa perspectiva é altamente recomendável usar o novo tipo de série D ou máquina virtual do Azure série DS (em combinação com o armazenamento do Azure Premium), pois eles têm 60% processadores mais rápidos que a série de um. Para o carregamento de RAM e CPU mais alto, você pode usar séries G e VMs série GS (em combinação com o armazenamento do Azure Premium) com a família de v3 de E5 processador Intel® Xeon® mais recente, que tem o dobro de memória e quatro vezes o unidade armazenamento de estado sólido (SSDs) da D/DS-série.

#### <a name="storage-configuration"></a>Configuração de armazenamento
Como SAP liveCache é baseado na tecnologia de MaxDB do SAP, todo o armazenamento do Azure melhores práticas recomendadas mencionadas para SAP MaxDB capítulo [configuração de armazenamento] [dbms-guia-8.4.1] também são válidos para liveCache do SAP. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedicado Azure máquina virtual liveCache
Como SAP liveCache usa intensivamente power computação, para uso produtivo é altamente recomendável para implantar na máquina Virtual do Azure um dedicada. 
 
![Dedicado máquina virtual do Azure para liveCache para caso de uso produtivo][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Backup e restauração
Backup e restauração, incluindo considerações de desempenho, já são descritos nos capítulos relevantes SAP MaxDB [Backup e restauração] [dbms-guia-8.4.2] e [considerações de desempenho de Backup e restauração] [dbms-guia-8.4.3]. 

#### <a name="other"></a>Outros
Todos os outros tópicos gerais já são descritos no SAP MaxDB [isso] [dbms-guia-8.4.4] capítulo relevante. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Especificações do servidor de conteúdo de SAP no Windows
O servidor de conteúdo SAP é um componente separado, baseadas em servidor para armazenar conteúdo como documentos eletrônicos em diferentes formatos. O servidor de conteúdo do SAP é fornecido pelo desenvolvimento de tecnologia e deve ser usado entre aplicativos para todos os aplicativos SAP. Ele é instalado em um sistema separado. Conteúdo normal é material de treinamento e documentação de depósito de conhecimento ou desenhos técnicos detalhados provenientes mySAP PLM sistema de gerenciamento de documentos. 

### <a name="sap-content-server-version-support"></a>Suporte de versão do servidor de conteúdo SAP
SAP atualmente oferece suporte:

* **Servidor de conteúdo do SAP** com versão **6.50 (e superior)**
* **SAP MaxDB versão 7,9**
* **Microsoft IIS (Internet Information Server) versão 8.0 (e superior)**

É altamente recomendável usar a versão mais recente do servidor de conteúdo SAP, que, no momento do gravando este documento é **6.50 SP4**e a versão mais recente do **Microsoft IIS 8.5**. 

Verificar as versões mais recentes com suporte do servidor de conteúdo do SAP e o Microsoft IIS na [Matriz de disponibilidade de produto da SAP (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de Microsoft Windows e máquina virtual do Azure com suporte para o servidor de conteúdo do SAP
Para descobrir a versão do Windows com suporte para o servidor de conteúdo do SAP no Azure, consulte:

* [Matriz de disponibilidade de produtos do SAP (PAM)][sap-pam]
* Observação SAP [1928533]

É altamente recomendável usar a versão mais recente do Microsoft Windows, que é o **Windows Server 2012 R2**no momento da escrita deste documento.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração de servidor de conteúdo do SAP para instalações de SAP em VMs Azure

#### <a name="storage-configuration"></a>Configuração de armazenamento 
Se você configurar o servidor de conteúdo do SAP para armazenar arquivos do banco de dados do SAP MaxDB, todos os armazenamento do Azure melhores práticas recomendação mencionada para SAP MaxDB capítulo [configuração de armazenamento] [dbms-guia-8.4.1] também são válidos para o cenário de servidor de conteúdo do SAP. 

Se você configurar o servidor de conteúdo do SAP para armazenar arquivos no sistema de arquivos, é recomendável usar uma unidade lógica dedicada. Usar espaços de armazenamento permite também aumentar o tamanho de disco lógico e taxa de transferência IOPS, conforme descrito em capítulo [Software RAID] [dbms-guia-2.2]. 

#### <a name="sap-content-server-location"></a>Local de servidor de conteúdo do SAP
Servidor de conteúdo do SAP deve ser implantado na mesma região Azure e Azure VNET onde o sistema SAP é implantado. Você é livre para decidir se deseja implantar componentes de servidor de conteúdo do SAP em uma VM Azure dedicado ou na mesma máquina virtual onde o sistema SAP está sendo executado. 
 
![Dedicado Azure máquina virtual, servidor de conteúdo do SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Local do servidor de Cache SAP
O servidor de Cache SAP é um componente adicional baseadas em servidor para fornecer acesso a documentos (em cache) localmente. O servidor de Cache SAP armazena os documentos de um servidor de conteúdo do SAP. Isso é para otimizar o tráfego de rede se os documentos tenham a serem recuperados mais de uma vez em locais diferentes. A regra geral é que o servidor de Cache do SAP precisa estar operando Fechar para o cliente que acessa o servidor de Cache do SAP. 

Aqui, você tem duas opções:

1. **Cliente é um sistema SAP de back-end** Se um sistema SAP de back-end estiver configurado para acessar o servidor de conteúdo do SAP, o sistema SAP é um cliente. Como o sistema SAP e SAP Server conteúdo são implantados na mesma região Azure – no mesmo datacenter Azure – eles são física próximos uns dos outros. Portanto, não é necessário ter um servidor de Cache dedicado do SAP. Clientes de interface de usuário do SAP (interface usuário SAP ou navegador da web) acessam o sistema SAP diretamente, e o sistema SAP recupera documentos do servidor de conteúdo SAP.
1. **Cliente é um navegador da web no local** O servidor de conteúdo do SAP pode ser configurado para ser acessado diretamente pelo navegador da web. Nesse caso, um navegador da web em execução no local é um cliente do servidor de conteúdo SAP. Centro de dados locais e data center Azure são colocados em diferentes locais físicos (o ideal Fechar para outro). Data Center local está conectado ao Azure por meio do Azure-to-Site VPN ou rota expressa. Embora ambas as opções oferecem conexão de rede VPN seguro no Azure, conexão de rede to-site não oferece um SLA de latência e largura de banda de rede entre o data center local e data center Azure. Para acelerar o acesso a documentos, você pode fazer um destes procedimentos:
    1. Instalar o servidor do SAP Cache local, fechar para o local da web navegador (opção em Figura [this][dbms-guide-900-sap-cache-server-on-premises])
    1. Configure a rota expressa do Azure, que oferece uma conexão de rede dedicada de alta velocidade e baixa latência entre data center local e data center Azure.
 
![Opção para instalar o servidor do SAP Cache local][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup e restauração
Se você configurar o servidor de conteúdo do SAP para armazenar arquivos do banco de dados do SAP MaxDB, as considerações de procedimento e desempenho de backup e restauração já são descritas SAP MaxDB capítulo [Backup e restauração] [dbms-guia-8.4.2] e capítulo [considerações de desempenho de Backup e restauração] [dbms-guia-8.4.3]. 

Se você configurar o servidor de conteúdo do SAP para armazenar arquivos no sistema de arquivos, uma opção é executar backup/restauração manual da estrutura de arquivo inteiro onde se encontram os documentos. Semelhante ao SAP MaxDB backup e restauração, é recomendável ter um volume de disco dedicado para backup finalidade. 

#### <a name="other"></a>Outros
Outras configurações específicas do servidor de conteúdo do SAP são transparentes para VMs do Azure e são descritas em vários documentos e anotações do SAP:

* <https://Service.SAP.com/contentserver> 
* Observação SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Especificações para o IBM DB2 para LUW no Windows
Com o Microsoft Azure, você pode facilmente migrar seu aplicativo SAP existente em execução no IBM DB2 para Linux, UNIX e Windows (LUW) para Azure máquinas virtuais. Ao SAP no IBM DB2 para LUW, administradores e desenvolvedores ainda podem usar as mesmo ferramentas de desenvolvimento e administração que são locais disponíveis.
Informações gerais sobre como executar SAP Business Suite em IBM DB2 para LUW podem ser encontradas na rede de comunidade o SAP (SCN) em <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Para obter informações adicionais e atualizações sobre SAP em DB2 para LUW no Azure, consulte observação SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX e suporte de versão do Windows
SAP em IBM DB2 para LUW nos serviços de máquina Virtual do Microsoft Azure é suportado na versão de DB2 10.5.

Para obter informações sobre tipos de máquina virtual do Azure e produtos SAP com suporte, consulte observação SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 para Linux, UNIX e diretrizes de configuração do Windows para instalações de SAP em VMs Azure

#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os arquivos de banco de dados devem ser armazenados no sistema de arquivos NTFS com base em discos VHD. Estas VHDs são montados para a máquina virtual do Azure e são baseados no armazenamento de BLOB do Azure página (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Qualquer tipo de unidades de rede ou compartilhamentos remotos como os serviços de arquivo Azure a seguir estão **não** tem suportado para arquivos de banco de dados: 

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/Persisting-Connections-to-Microsoft-Azure-Files.aspx>
 
Se você estiver usando o Azure VHDs com base em armazenamento de BLOB de página do Azure, as declarações feitas neste documento capítulo [estrutura de uma implantação de RDBMS] [dbms-guia-2] também se aplicam a implantações com o IBM DB2 para banco de dados de LUW. 

Conforme explicado anteriormente na parte geral do documento, existem cotas em produtividade IOPS para VHDs do Azure. As cotas exatas dependem do tipo de máquina virtual usado. Uma lista dos tipos de máquina virtual com suas cotas pode ser encontrada [aqui][virtual-machines-sizes].

Desde que a cota IOPS atual por disco for suficiente, é possível armazenar todos os arquivos de banco de dados em um único VHD de Azure montado. 

Para um desempenho considerações consultem também capítulo "Segurança e desempenho considerações para banco de dados diretórios de dados" em guias de instalação do SAP.

Como alternativa, você pode usar Pools de armazenamento do Windows (somente disponível no Windows Server 2012 e superior) ou distribuição do Windows para o Windows 2008 R2 conforme descrito no capítulo [Software RAID] [dbms-guia-2.2] deste documento para criar um grande dispositivo lógico através de vários discos VHD montados.
Para discos que contém os caminhos de armazenamento de DB2 para suas pastas dadosSAP e saptmp, você deve especificar um tamanho de setor de disco físico de 512 KB. Ao usar Pools de armazenamento do Windows, você deve criar os pools de armazenamento manualmente por meio da interface de linha de comando usando o parâmetro "-LogicalSectorSizeDefault". Para obter detalhes, consulte <https://technet.microsoft.com/library/hh848689.aspx>.

#### <a name="backuprestore"></a>Backup e restauração
A funcionalidade de backup e restauração para IBM DB2 para LUW é suportada da mesma maneira que em sistemas operacionais Windows Server e Hyper-V padrão.

Você deve certificar-se de que você tenha uma estratégia de backup do banco de dados válido no lugar. 

Como em implantações de depender, desempenho de backup e restauração depende de quantos volumes podem ser lido em paralelo e que pode ser a produtividade desses volumes. Além disso, o consumo de CPU usado pelo compactação de backup pode ser reproduzidos uma função significativa em VMs com apenas até 8 threads de CPU. Portanto, um pode assumir:

* Quanto menor o número de VHDs usada para armazenar os dispositivos de banco de dados, menor a produtividade geral em leitura
* Menor que número de CPU threads a máquina virtual, a mais grave o impacto da compactação de backup
* Os destinos de menos (diretórios de distribuição, VHDs) para gravar o backup, menor a taxa de transferência

Para aumentar o número de destinos para gravar, duas opções podem ser usadas/combinados dependendo das suas necessidades:

* Distribuição o volume de destino de backup sobre diversos VHDs montados para melhorar a produtividade IOPS nesse volume distribuído
* Usando mais de uma pasta de destino para escrever o backup

#### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de dados
Não há suporte para o Microsoft Cluster Server (MSCS).

Há suporte para DB2 alta disponibilidade recuperação de dados (HADR). Se as máquinas virtuais da configuração HA tiver trabalhando resolução de nome, a configuração no Azure não serão diferentes de qualquer instalação que é feita no local. Não é recomendável depender somente resolução IP.

Não use Azure Store replicação geográfica. Para obter mais informações, consulte Capítulo [armazenamento do Microsoft Azure] [dbms-guia-2,3] e [alta disponibilidade e recuperação de dados com VMs do Azure] [dbms-guia-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais como o monitoramento de conjuntos de disponibilidade do Azure ou SAP aplicam conforme descrito nos três primeiros capítulos deste documento para implantações de VMs com IBM DB2 para LUW também. 

Consulte também capítulo [geral do SQL Server para SAP em Azure resumo] [dbms-guia-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Especificações para o IBM DB2 para LUW no Linux
Com o Microsoft Azure, você pode facilmente migrar seu aplicativo SAP existente em execução no IBM DB2 para Linux, UNIX e Windows (LUW) para Azure máquinas virtuais. Ao SAP no IBM DB2 para LUW, administradores e desenvolvedores ainda podem usar as mesmo ferramentas de desenvolvimento e administração que são locais disponíveis. Informações gerais sobre como executar SAP Business Suite em IBM DB2 para LUW podem ser encontradas na rede de comunidade o SAP (SCN) em <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Para obter informações adicionais e atualizações sobre SAP em DB2 para LUW no Azure, consulte observação SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 para Linux, UNIX e suporte de versão do Windows
SAP em IBM DB2 para LUW nos serviços de máquina Virtual do Microsoft Azure é suportado na versão de DB2 10.5.

Para obter informações sobre tipos de máquina virtual do Azure e produtos SAP com suporte, consulte observação SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 para Linux, UNIX e diretrizes de configuração do Windows para instalações de SAP em VMs Azure

#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os arquivos de banco de dados devem ser armazenados em um sistema de arquivo com base em discos VHD. Estas VHDs são montados para a máquina virtual do Azure e são baseados no armazenamento de BLOB do Azure página (<https://msdn.microsoft.com/library/azure/ee691964.aspx>).
Qualquer tipo de unidades de rede ou compartilhamentos remotos como os serviços de arquivo Azure a seguir estão **não** tem suportado para arquivos de banco de dados:

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-File-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/Persisting-Connections-to-Microsoft-Azure-Files.aspx>

Se você estiver usando o Azure VHDs com base em armazenamento de BLOB de página do Azure, as declarações feitas neste documento capítulo [estrutura de uma implantação de RDBMS] [dbms-guia-2] também se aplicam a implantações com o IBM DB2 para banco de dados de LUW.

Conforme explicado anteriormente na parte geral do documento, existem cotas em produtividade IOPS para VHDs do Azure. As cotas exatas dependem do tipo de máquina virtual usado. Uma lista dos tipos de máquina virtual com suas cotas pode ser encontrada [aqui][virtual-machines-sizes].

Desde que a cota IOPS atual por disco for suficiente, é possível armazenar todos os arquivos de banco de dados em um único VHD de Azure montado.

Para um desempenho considerações consultem também capítulo "Segurança e desempenho considerações para banco de dados diretórios de dados" em guias de instalação do SAP.

Como alternativa, você pode usar LVM (Gerenciador de Volume lógico) ou MDADM conforme descrito no capítulo [Software RAID] [dbms-guia-2.2] deste documento para criar um grande dispositivo lógico através de vários discos VHD montados.
Para discos que contém os caminhos de armazenamento de DB2 para suas pastas dadosSAP e saptmp, você deve especificar um tamanho de setor de disco físico de 512 KB.

#### <a name="backuprestore"></a>Backup e restauração
A funcionalidade de backup e restauração para IBM DB2 para LUW é suportada da mesma maneira que em padrão Linux instalação no local.

Você deve certificar-se de que você tenha uma estratégia de backup do banco de dados válido no lugar.

Como em implantações de depender, desempenho de backup e restauração depende de quantos volumes podem ser lido em paralelo e que pode ser a produtividade desses volumes. Além disso, o consumo de CPU usado pelo compactação de backup pode ser reproduzidos uma função significativa em VMs com apenas até 8 threads de CPU. Portanto, um pode assumir:

* Quanto menor o número de VHDs usada para armazenar os dispositivos de banco de dados, menor a produtividade geral em leitura
* Menor que número de CPU threads a máquina virtual, a mais grave o impacto da compactação de backup
* Os destinos de menos (diretórios de distribuição, VHDs) para gravar o backup, menor a taxa de transferência

Para aumentar o número de destinos para gravar, duas opções podem ser usadas/combinados dependendo das suas necessidades:

* Distribuição o volume de destino de backup sobre diversos VHDs montados para melhorar a produtividade IOPS nesse volume distribuído
* Usando mais de uma pasta de destino para escrever o backup

#### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de dados
Há suporte para DB2 alta disponibilidade recuperação de dados (HADR). Se as máquinas virtuais da configuração HA tiver trabalhando resolução de nome, a configuração no Azure não serão diferentes de qualquer instalação que é feita no local. Não é recomendável depender somente resolução IP.

Não use Azure Store replicação geográfica. Para obter mais informações, consulte Capítulo [armazenamento do Microsoft Azure] [dbms-guia-2,3] e [alta disponibilidade e recuperação de dados com VMs do Azure] [dbms-guia-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais como o monitoramento de conjuntos de disponibilidade do Azure ou SAP aplicam conforme descrito nos três primeiros capítulos deste documento para implantações de VMs com IBM DB2 para LUW também.

Consulte também capítulo [geral do SQL Server para SAP em Azure resumo] [dbms-guia-5.8].