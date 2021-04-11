---
title: SQL Server Azure Virtual Machines adatbázis-kezelő rendszer üzembe helyezése SAP-munkaterheléshez | Microsoft Docs
description: SQL Server rendszerű Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: Azure, SQL Server, SAP, AlwaysOn
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4eb7e64065e311dc18f33dffb169d5c27a34008d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673038"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines adatbázis-kezelő rendszerbe állítás az SAP NetWeaver számára

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Ez a dokumentum több különböző területet ölel fel, amelyeket figyelembe kell venni, amikor az Azure IaaS SAP-számítási feladatait SQL Server üzembe helyezi. Ennek a dokumentumnak az előfeltétele, hogy olvassa el az [azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP](./dbms_guide_general.md) -számítási feladatokhoz, valamint az [Azure-dokumentáció SAP-munkaterhelésének](./get-started.md)egyéb útmutatói című dokumentumait. 



> [!IMPORTANT]
> A dokumentum hatóköre az SQL Server Windows-verziója. Az SAP nem támogatja a SQL Server linuxos verzióját az SAP szoftverrel. A dokumentum nem Microsoft Azure SQL Database tárgyal, amely a Microsoft Azure platform szolgáltatásként kínált platform. Az ebben a dokumentumban ismertetett vita arról szól, hogyan fut a SQL Server termék, mivel ismert az Virtual Machines Azure-beli helyszíni környezetekben, az infrastruktúrát pedig az Azure szolgáltatási képességének megfelelően hasznosítja. A két ajánlat közötti adatbázis-képességek és-funkciók eltérnek egymással, és nem lehetnek összekeverve. Lásd még: <https://azure.microsoft.com/services/sql-database/>
> 
>

Általánosságban érdemes figyelembe venni a legújabb SQL Server kiadásokat az SAP-munkaterhelések Azure IaaS való futtatásához. A legújabb SQL Server kiadások jobb integrációt biztosítanak az egyes Azure-szolgáltatásokkal és funkciókkal. Vagy az Azure IaaS-infrastruktúrában végzett műveleteket optimalizáló módosításokat tartalmaz.

A folytatás előtt javasoljuk, hogy tekintse át a következő cikket: [mi az SQL Server az Azure Virtual Machines (Windows)] [ https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview ].

A következő részekben a fenti hivatkozás alatt található dokumentáció egyes részeit összesítjük és megemlítik. Az SAP-vel kapcsolatos specifikus információk is megtalálhatók, és néhány fogalmat részletesebben is ismertetünk. A SQL Server-specifikus dokumentáció elolvasása előtt azonban erősen ajánlott a fenti dokumentáción keresztül dolgozni.

A folytatás előtt néhány SQL Server a IaaS-specifikus információkkal:

* **SQL-verzió támogatása**: az SAP-ügyfelek esetében a SQL Server 2008 R2 és újabb verziók Microsoft Azure virtuális gépeken támogatottak. A korábbi kiadások nem támogatottak. További részletekért tekintse át ezt az általános [támogatási nyilatkozatot](https://support.microsoft.com/kb/956893) . Általánosságban SQL Server a 2008-es verzió is támogatott a Microsoft számára. Azonban az SAP jelentős funkcionalitása miatt, amelyet SQL Server 2008 R2-vel vezették be, SQL Server 2008 R2 az SAP minimális kiadása. Általánosságban érdemes figyelembe venni a legújabb SQL Server kiadásokat az SAP-munkaterhelések Azure IaaS való futtatásához. A legújabb SQL Server kiadások jobb integrációt biztosítanak az egyes Azure-szolgáltatásokkal és funkciókkal. Vagy az Azure IaaS-infrastruktúrában végzett műveleteket optimalizáló módosításokat tartalmaz. Ezért a papír SQL Server 2016 és SQL Server 2017.
* **SQL-teljesítmény**: Microsoft Azure üzemeltetett Virtual Machines jól teljesítenek a nyilvános Felhőbeli virtualizációs ajánlatokkal szemben, de az egyéni eredmények eltérőek lehetnek. Tekintse meg az [Azure Virtual Machines SQL Server teljesítményének bevált gyakorlatait](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)ismertető cikket.
* **Rendszerképek használata az Azure Marketplace-ről**: az új Microsoft Azure virtuális gép üzembe helyezésének leggyorsabb módja az Azure piactéren elérhető lemezkép használata. Az Azure Marketplace-en található lemezképek tartalmazzák a legújabb SQL Server kiadásokat. Azok a lemezképek, amelyeken már telepítve van a SQL Server, nem használhatók azonnal SAP NetWeaver-alkalmazásokhoz. Ennek az az oka, hogy a rendszer az alapértelmezett SQL Server rendezést telepíti ezeken a lemezképeken, és nem az SAP NetWeaver Systems által igényelt rendezést. Az ilyen rendszerképek használatához tekintse meg a fejezetben ismertetett lépéseket a [Microsoft Azure Marketplace SQL Server rendszerképének használatával][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Javaslatok az SAP-hez kapcsolódó SQL Server központi telepítések virtuális gépekre/VHD-struktúrájára
Az általános leírás, az operációs rendszer, a SQL Server a végrehajtható fájlok, valamint az SAP 2 szintű rendszerek esetében az SAP-végrehajtható fájloknak külön Azure-lemezeket kell elhelyezniük vagy telepíteniük kell őket. Általában a SQL Server rendszeradatbázisok többségét az SAP NetWeaver számítási feladatait nem magas szinten használják. Ennek ellenére a SQL Server (Master, msdb és Model) rendszeradatbázisait, valamint egy különálló Azure-lemezen lévő többi SQL Server-címtárat kell használnia. SQL Server tempdb vagy a nonperisisted D:\ kell lennie meghajtó vagy külön lemezen.


* Az összes SAP-tanúsítvánnyal rendelkező virtuálisgép-típussal (lásd: SAP-Megjegyzés [1928533]), kivéve a-sorozatú virtuális gépeket, a tempdb és a naplófájlokat a nem megőrzött D:\ lehet helyezni meghajtó. 
* A régebbi SQL Server kiadások esetében, ahol a SQL Server alapértelmezés szerint egyetlen adatfájllal telepíti a tempdb-et, ajánlott több tempdb-adatfájlt használni. Legyen tisztában a D:\ a meghajtók kötetei a virtuális gép típusától függően eltérőek. A D:\ pontos mérete a különböző virtuális gépek meghajtója az [Azure-beli Windows rendszerű virtuális gépek méreteit](../../sizes.md)ismerteti.

Ezek a konfigurációk lehetővé teszik a tempdb számára, hogy több helyet fogyasszanak, és még fontosabb IOPS és tárolási sávszélességet használjanak, mint amennyit a rendszermeghajtó képes biztosítani. A nem állandó D:\ a meghajtó jobb I/O-késést és átviteli sebességet is biztosít (az a sorozatú virtuális gépek kivételével). A megfelelő tempdb-méret megállapításához megtekintheti a tempdb-méreteket a meglévő rendszereken. 

>[!NOTE]
> Ha a tempdb adatfájljait és a naplófájlt egy mappába helyezi a D:\ a létrehozott meghajtónak meg kell győződnie arról, hogy a mappa már létezik a virtuális gép újraindítása után. A D:\ óta a meghajtó frissen van inicializálva egy virtuális gép újraindítása után, és a rendszer törli az összes fájlt és a címtár-struktúrát. Lehetőség az esetleges címtár-struktúrák újbóli létrehozására a D:\ [ebben a cikkben](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)a SQL Server szolgáltatás megkezdése előtti meghajtót ismertetjük.

Egy virtuálisgép-konfiguráció, amely SQL Servert futtat egy SAP-adatbázissal, valamint a tempdb-és tempdb-naplófájlok elhelyezése a D:\ a meghajtó így néz ki:

![Egyszerű virtuálisgép-lemez konfigurációjának ábrája SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

A fenti ábrán egy egyszerű eset jelenik meg. Az [azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP-munkaterheléshez](dbms_guide_general.md), az Azure Storage-beli típus, a szám és a lemezek mérete a különböző tényezőktől függ. Általánosságban azonban azt javasoljuk, hogy:

- Egy nagy kötet használata, amely tartalmazza a SQL Server adatfájlokat. Ennek a konfigurációnak az oka, hogy a valós életben számos, különböző I/O-munkaterheléssel rendelkező, különböző méretű adatbázis-fájllal rendelkező SAP-adatbázis létezik.
- Használja a D:\drive a tempdb, ha a teljesítmény elég jó. Ha a teljes munkaterhelést a tempdb a D:\-ben található teljesítmény korlátozza Előfordulhat, hogy az [ebben a cikkben](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)javasolt módon érdemes megfontolni a tempdb áthelyezését az Azure Premium Storage-ba vagy az ultravékony lemezekre.


### <a name="special-for-m-series-vms"></a>Speciális az M-sorozatú virtuális gépekhez
Az Azure M sorozatú virtuális gépek esetében a tranzakciónaplóba való írás a tranzakciós naplóba való beírásával csökkenthető az Azure-Premium Storage teljesítményéhez képest, az Azure írásgyorsító használata esetén. Ezért telepítenie kell az Azure-írásgyorsító a SQL Server tranzakciónapló kötetét alkotó VHD (k) számára. A részletek olvashatók a dokumentumban [írásgyorsító](../../how-to-enable-write-accelerator.md).
  

### <a name="formatting-the-disks"></a>Lemezek formázása
SQL Server esetén a SQL Server-és naplófájlokat tartalmazó lemezek NTFS-blokkjának mérete 64 KB. Nincs szükség a D:\ formázására meghajtó. A meghajtó előre formázva van.

Annak biztosítása érdekében, hogy az adatbázisok visszaállítása vagy létrehozása ne inicializálja az adatfájlokat a fájlok tartalmának nullázásával, győződjön meg arról, hogy az SQL Server szolgáltatásnak a-t futtató felhasználói környezete rendelkezik bizonyos engedéllyel. Általában a Windows rendszergazda csoportba tartozó felhasználók rendelkeznek ezekkel az engedélyekkel. Ha a SQL Server szolgáltatás a nem Windows rendszergazda felhasználójának felhasználói környezetében fut, akkor a felhasználónak hozzá kell rendelnie a felhasználói jogosultságot a **kötet-karbantartási feladatok végrehajtásához**.  Tekintse meg a Microsoft Tudásbázis következő cikkének részleteit: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Az adatbázis-tömörítés hatása
Azon konfigurációk esetében, amelyekben az I/O-sávszélesség korlátozási tényezővé válhat, minden mérték, amely csökkenti a IOPS, segíthet a számítási feladatok egy IaaS-forgatókönyvben, például az Azure-ban való futtatásában. Ezért ha még nem tette meg, alkalmazza SQL Server oldal tömörítését az SAP és a Microsoft javasolta, mielőtt meglévő SAP-adatbázist tölt fel az Azure-ba.

Az adatbázis-tömörítés az Azure-ba való feltöltés előtt történő végrehajtásának javaslata két okból áll fenn:

* A feltölteni kívánt adatmennyiség alacsonyabb.
* A tömörítés végrehajtásának időtartama rövidebb, feltételezve, hogy az egyik nagyobb mennyiségű processzorral vagy magasabb I/O-sávszélességgel vagy kisebb I/O-késéssel a helyszínen is használható.
* A kisebb adatbázis-méretek kevesebb költséget okozhatnak a lemez kiosztása során

Az adatbázis-tömörítés egy Azure-Virtual Machines is működik, mint a helyszínen. A meglévő SAP NetWeaver SQL Server adatbázisok tömörítésével kapcsolatos további információkért olvassa el az SAP- [tömörítési eszköz továbbfejlesztett MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress)című cikket. 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 és újabb adatbázis-fájlok tárolása közvetlenül az Azure-on Blob Storage
SQL Server 2014-es és újabb verziók megnyitják az adatbázisfájlok közvetlenül az Azure Blob store-on való tárolásának lehetőségét a körülöttük található virtuális merevlemez "burkolója" nélkül. Különösen a standard szintű Azure Storage vagy a kisebb virtuálisgép-típusok használata esetén ez a típusú telepítés olyan forgatókönyveket tesz lehetővé, amelyekkel leküzdheti a korlátozott számú, kisebb virtuálisgép-típusokhoz csatlakoztatható lemezek IOPS korlátozásait. Az üzembe helyezés ilyen módon működik a felhasználói adatbázisokhoz, azonban a SQL Server rendszeradatbázisai esetében nem. A SQL Server adatkezelési és naplófájljaiban is működik. Ha egy SAP SQL Server-adatbázist szeretne üzembe helyezni a virtuális merevlemezekre való "becsomagolás" helyett, vegye figyelembe a következőket:

* A használt Storage-fióknak ugyanabban az Azure-régióban kell lennie, mint a virtuális gép üzembe helyezéséhez használt SQL Server fut.
* A korábban a különböző Azure Storage-fiókokban lévő VHD-k eloszlásával kapcsolatban felsorolt megfontolások is érvényesek erre a telepítési módszerre. Azt jelenti, hogy az I/O-műveletek száma az Azure Storage-fiók korlátai szerint történik.
* A virtuális gép tárolási I/O-kvótájának könyvelése helyett a SQL Server adatokat és naplófájlokat jelképező tárolási Blobok forgalmát a rendszer az adott virtuálisgép-típus hálózati sávszélességére veszi át. Egy adott virtuálisgép-típus hálózati és tárolási sávszélességét az [Azure-beli Windows rendszerű virtuális gépek méreteit](../../sizes.md)ismertető cikkben találja.
* A fájlok I/O hálózati kvótán keresztüli leküldésének eredményeképpen a tárolási kvótát többnyire a virtuális gép teljes sávszélességével kell kiszolgálni, amely kizárólag részlegesen használható.
* A IOPS és az I/O-átviteli teljesítmény azon célja, hogy az Azure Premium Storage a különböző méretű lemezek esetében ne legyenek többé érvényesek. Még akkor is, ha a létrehozott Blobok az Azure Premium Storageon találhatók. A célok a [virtuális gépek nagy teljesítményű Premium Storage és felügyelt lemezei](../../disks-types.md#premium-ssd)című cikkben vannak dokumentálva. SQL Server adatfájlok és naplófájlok közvetlenül az Azure-Premium Storage tárolt blobokra való helyezésének eredményeképpen a teljesítmény jellemzői eltérőek lehetnek az Premium Storage Azure-beli virtuális merevlemezekhez képest.
* Az Azure Premium Storage-lemezek számára elérhető gazdagép-alapú gyorsítótárazás nem érhető el SQL Server adatfájlok közvetlenül az Azure-blobokon való elhelyezésekor.
* Az M sorozatú virtuális gépeken az Azure írásgyorsító nem használható az ezredmásodperces írások támogatásához a SQL Server tranzakciós naplófájlban. 

A funkció részletei a következő cikkben találhatók: [SQL Server adatfájlok Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)

Az éles rendszerekre vonatkozó javaslat elkerüli ezt a konfigurációt, és nem közvetlenül az Azure-Blobok helyett az Azure Premium Storage VHD-k SQL Server-és naplófájljainak elhelyezését választja.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 puffer-készlet kiterjesztése
A SQL Server 2014 egy új funkciót vezetett be, amelyet a rendszer [puffer-készlet bővítménynek](/sql/database-engine/configure-windows/buffer-pool-extension)nevez. Ez a funkció kibővíti SQL Server pufferének készletét, amelyet a memóriában tartanak egy második szintű gyorsítótárral, amelyet egy kiszolgáló vagy virtuális gép helyi SSD-je támogat. A puffer-készlet kiterjesztése lehetővé teszi a nagyobb munkakészletek tárolását a memóriában. Az Azure standard Storage-hoz való hozzáféréshez képest az Azure-beli virtuális gépek helyi SSD-készletéből tárolt puffer kiterjesztésére való hozzáférést számos tényező okozza. A puffer-bővítmény az Azure-ba való összehasonlításával Premium Storage az olvasási gyorsítótárat SQL Server adatfájlok esetében ajánlott, nem várható jelentős előny a puffer-bővítmények számára. Ennek oka, hogy a gyorsítótárak (SQL Server a pufferek és a Premium Storage olvasási gyorsítótár) az Azure számítási csomópontjának helyi lemezeit használják.

Az időközben szerzett tapasztalatok az SAP számítási feladattal együtt SQL Server a pufferek készletének bővítményét, és továbbra sem egyértelmű javaslatokat tesznek arra, hogy minden esetben használhatók-e. Az ideális esetben az, hogy az SAP-alkalmazás munkakészlete megfelel a fő memóriának. Az Azure-ban az akár 4 TB-os memóriával rendelkező virtuális gépeket is elérhetővé kell tennie a munkakészletnek a memóriában való megtartásához. Ezért a pufferelési készlet kiterjesztésének használata néhány ritka esetre korlátozódik, és nem lehet általános eset.  

## <a name="backuprecovery-considerations-for-sql-server"></a>A SQL Server biztonsági mentési/helyreállítási szempontjai
SQL Server Azure-ba történő telepítésekor a biztonsági mentési módszert felül kell vizsgálni. Ha a rendszer nem éles rendszer, akkor a SQL Server által üzemeltetett SAP-adatbázist rendszeresen biztonsági mentéssel kell elkészíteni. Mivel az Azure Storage három lemezképet tart fenn, a biztonsági mentés mostantól kevésbé fontos a tárolási összeomlás kompenzálása tekintetében. A megfelelő biztonsági mentési és helyreállítási terv fenntartásának prioritása nagyobb, mint a logikai/manuális hibák kompenzálása az időponthoz kapcsolódó helyreállítási képességek biztosításával. A cél az, hogy a biztonsági mentések használatával visszaállítsa az adatbázist egy adott időpontra, vagy az Azure-beli biztonsági mentések használatával egy másik rendszert a meglévő adatbázis másolásával. 

A különböző SQL Server biztonsági mentési lehetőségek az Azure-ban való megjelenítéséhez olvassa el az [Virtual Machines Azure-beli SQL Server biztonsági mentését és visszaállítását](../../../azure-sql/virtual-machines/windows/backup-restore.md)ismertető cikket. A cikk több különböző lehetőségre is kiterjed.

### <a name="manual-backups"></a>Manuális biztonsági másolatok
Több lehetősége van a manuális biztonsági mentések végrehajtására:

1. A hagyományos SQL Server biztonsági mentések elvégzése közvetlenül csatlakoztatott Azure-lemezekre. Ennek a módszernek az az előnye, hogy a biztonsági másolatok gyorsan elérhetők a rendszer frissítéseihez, és új rendszerek készíthetők a meglévő SAP-rendszerek példányaiként.
2.  A SQL Server 2012 CU4 és újabb rendszerű adatbázisok biztonsági mentését végezheti el az Azure Storage URL-címére.
3.  File-Snapshot az Azure-Blob Storage adatbázisfájlok biztonsági másolatait. Ez a módszer csak akkor működik, ha a SQL Server adatai és naplófájljai az Azure Blob Storage-ban találhatók

Az első módszer jól ismert, és számos esetben alkalmazható a helyszíni világban is. A feladat azonban a hosszú távú biztonsági mentés helyének megoldására is elhagyható. Mivel nem szeretné, hogy a biztonsági másolatok 30 vagy több napig maradjanak a helyileg csatlakoztatott Azure-tárolóban, Azure Backup-szolgáltatásokat vagy más, harmadik féltől származó biztonsági mentési/helyreállítási eszközt kell használnia, amely magában foglalja a biztonsági mentések hozzáférését és adatmegőrzési felügyeletét. Emellett egy nagyméretű fájlkiszolgáló is kiépíthető az Azure-ban a Windows Storage Spaces használatával.

A második módszert a [SQL Server biztonsági mentés URL-címére](../../../azure-sql/virtual-machines/windows/backup-restore.md)című cikk ismerteti részletesebben. A SQL Server különböző kiadásai rendelkeznek a funkció néhány változatával. Ezért érdemes megtekinteni az adott SQL Server kiadás-ellenőrzési dokumentációját. Fontos megjegyezni, hogy ez a cikk sok korlátozást sorol fel. Lehetősége van arra, hogy a biztonsági mentést a következő módokon hajtsa végre:

- Egyetlen Azure-oldal blobja, amely a biztonsági mentés méretét 1000 GB-ra korlátozza. Ez a korlátozás korlátozza az elérhető átviteli sebességet is.
- Több (akár 64) Azure Block-Blobok, amelyek a 12 TB-os elméleti biztonsági mentési méretet teszik lehetővé. Az ügyfél-adatbázisokkal végzett tesztek azonban azt mutatták ki, hogy a biztonsági másolatok maximális mérete kisebb, mint az elméleti korlát. Ebben az esetben Ön felelős a biztonsági másolatok megőrzésének irányításáért és a biztonsági mentések eléréséhez.


### <a name="automated-backup-for-sql-server"></a>Automatikus biztonsági mentés az SQL Serverhez
Az automatikus biztonsági mentés automatikus biztonsági mentési szolgáltatást biztosít az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server Standard és Enterprise kiadásokhoz. Ezt a szolgáltatást a [SQL Server IaaS-ügynök bővítménye](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md)nyújtja, amely a Azure Portal SQL Server Windows rendszerű virtuálisgép-lemezképeken automatikusan települ. Ha a saját operációsrendszer-lemezképeit a SQL Server telepítettével telepíti, a virtuálisgép-bővítményeket külön kell telepítenie. A szükséges lépéseket a [cikk ismerteti](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

A módszer képességeiről további részleteket a következő cikkekben találhat:

- SQL Server 2014: [automatikus biztonsági mentés SQL Server 2014 Virtual Machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016/2017: [automatikus biztonsági mentés v2 Azure Virtual Machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

A dokumentációnak megfelelően láthatja, hogy a legújabb SQL Server kiadásokkal bővült a funkció. A SQL Server automatikus biztonsági mentéssel kapcsolatos további részletekért lásd a [felügyelt biztonsági mentés Microsoft Azurera SQL Server](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)című cikket. Az elméleti biztonsági mentés maximális mérete 12 TB.  Az automatikus biztonsági mentés jó módszer lehet a legfeljebb 12 TB méretű biztonsági másolatok méretéhez. Mivel több blobot párhuzamosan kell írni, az átviteli sebesség 100 MB/s-nál nagyobb lehet. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup SQL Server virtuális gépekhez
A SQL Server biztonsági mentések új módszerét a Azure Backup Services nyilvános előzetes verziója 2018. A biztonsági mentés módszere SQL Server ugyanaz, mint a más, harmadik féltől származó eszközök, azaz a SQL Server VSS/VDI felülete, hogy a biztonsági mentéseket egy célhelyre továbbítsa. Ebben az esetben a célhely az Azure Recovery Service Vault.

A biztonsági mentési módszer részletesebb leírása, amely számos előnyt [biztosít a](../../../backup/backup-azure-sql-database.md)központi biztonsági mentési konfigurációkhoz, a figyeléshez és az adminisztrációhoz. 


### <a name="third-party-backup-solutions"></a>Harmadik féltől származó biztonsági mentési megoldások
A sok SAP-ügyfél esetében nem volt lehetőség a kezdésre, és bevezetni az Azure-ban futó SAP-környezet részét képező teljes körű új biztonsági mentési megoldásokat. Ennek eredményeképpen a meglévő biztonsági mentési megoldások szükségesek az Azure-ban való használathoz és bővítéshez. A meglévő biztonsági mentési megoldások az Azure-ba való kiterjesztése általában jól működik a legtöbb fő szállítóval ezen a területen. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>SQL Server rendszerkép használata a Microsoft Azure Marketplace
A Microsoft olyan virtuális gépeket kínál az Azure piactéren, amelyek már tartalmazzák a SQL Server verzióit. A SQL Server és a Windows rendszerhez szükséges licenceket igénylő SAP-ügyfelek esetén a lemezképek segítségével lefedi a licencek igényét, ha a virtuális gépeket SQL Server már telepítette. Az SAP-hez készült rendszerképek használatához a következő szempontokat kell figyelembe venni:

* A SQL Server nem próbaverziós verziók magasabb költségeket igényelnek, mint az Azure Marketplace-ről üzembe helyezett "Windows-only" virtuális gép. Tekintse meg az alábbi cikkeket az árak összehasonlításához: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> és <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> . 
* Csak a SAP által támogatott SQL Server kiadásokat használhatja.
* Az Azure Marketplace-en elérhető virtuális gépekre telepített SQL Server példány rendezése nem a SQL Server példány futtatásához szükséges. A rendezést a következő szakasz utasításai szerint módosíthatja.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Microsoft Windows/SQL Server VM SQL Server rendezésének módosítása
Mivel az Azure Marketplace-en lévő SQL Server rendszerképek nincsenek beállítva a rendezés használatára, amelyet az SAP NetWeaver-alkalmazások igényelnek, az üzembe helyezés után azonnal meg kell változtatni. SQL Server esetében ez a rendezési változás a virtuális gép üzembe helyezése után a következő lépésekkel végezhető el, és a rendszergazda bejelentkezhet a telepített virtuális gépre:

* Nyisson meg egy Windows-parancssorablakot rendszergazdaként.
* A könyvtár módosítása a C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Hajtsa végre a parancsot: Setup.exe/QUIET/ACTION = REBUILDDATABASE/INSTANCENAME = MSSQLSERVER/SQLSYSADMINACCOUNTS = `<local_admin_account_name`>/SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> az a fiók, amely rendszergazdai fiókként lett meghatározva a virtuális gép első telepítésekor a katalóguson keresztül.

A folyamat csak néhány percet vesz igénybe. A következő lépések végrehajtásával győződjön meg arról, hogy a lépés a megfelelő eredménnyel zárult-e:

* Nyissa meg az SQL Server Management Studiót.
* Nyisson meg egy lekérdezési ablakot.
* Hajtsa végre a sp_helpsort parancsot a SQL Server Master adatbázisban.

A kívánt eredménynek a következőhöz hasonlóan kell kinéznie:

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

Ha az eredmény eltérő, állítsa le az SAP telepítését, és vizsgálja meg, miért nem a várt módon működik a telepítési parancs. Az SAP NetWeaver-alkalmazások üzembe helyezése SQL Server példányra, amely a fent említettnél több SQL Server kódlap használata esetén **nem** támogatott.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server High-Availability az Azure-beli SAP-ban
Az SAP-ben az Azure IaaS üzemelő SQL Server használatával számos különböző lehetőség közül választhat az adatbázis-kezelő réteg elérhetővé tételéhez. Az Azure [Virtual Machines adatbázis-kezelő üzembe helyezése az SAP](dbms_guide_general.md) számítási feladatokhoz című témakörben leírtaknak megfelelően az Azure különböző, az Azure-beli rendelkezésre állási csoportokban üzembe helyezett virtuális gépeket és több virtuális gépet is tartalmaz. Feltételezi, hogy az Azure-beli rendelkezésre állási csoportokban üzemelő éles környezetekben való üzembe helyezéshez szükséges idő Ilyen esetben legalább két virtuális gépet kell üzembe helyeznie egy ilyen rendelkezésre állási csoporton belül. Egy virtuális gép az aktív SQL Server példányt fogja futtatni. A másik virtuális gép a passzív példányt fogja futtatni

### <a name="sql-server-clustering-using-windows-scale-out-file-server-or-azure-shared-disk"></a>Fürtözés SQL Server a Windows kibővíthető fájlkiszolgáló vagy az Azure megosztott lemez használatával
A Windows Server 2016-es verziójával a Microsoft bevezette [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Közvetlen tárolóhelyek központi telepítésen alapuló SQL Server-alapú fürtözést általában a rendszer támogatja. Az Azure olyan [Azure-beli megosztott lemezeket](../../disks-shared-enable.md?tabs=azure-cli) is biztosít, amelyek a Windows fürtözéshez használhatók. Az SAP-munkaterhelések esetében ezek a beállítások nem támogatottak. 

### <a name="sql-server-log-shipping"></a>SQL Server napló szállítása
A magas rendelkezésre állású módszerek egyike SQL Server a napló szállítása. Ha a HA-konfigurációban részt vevő virtuális gépek működnek a névfeloldással, nincs probléma, és az Azure-beli beállítás nem különbözik a helyszínen végzett telepítéstől. A naplózási szállítás beállításával és a napló szállításával kapcsolatos alapelvekkel kapcsolatban. SQL Server napló szállításával kapcsolatos részletek a következő cikkben találhatók: a [log Shipping (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server).

A SQL Server log szállítási funkciót alig használták az Azure-ban, hogy magas rendelkezésre állást érjenek el egy Azure-régión belül. A következő helyzetekben azonban az SAP-ügyfelek sikeresen használták a log Shipping-t az Azure-ban:

- Vész-helyreállítási forgatókönyvek az egyik Azure-régióból egy másik Azure-régióba
- Helyi vész-helyreállítási konfiguráció Azure-régióba
- Kibővített forgatókönyvek a helyszínen az Azure-ba. Ezekben az esetekben a naplózási szolgáltatás az új adatbázis-kezelő rendszer Azure-beli üzembe helyezését is szinkronizálja az Azure-ban a folyamatos üzemi rendszerrel a helyszínen. A kivágás időpontjában a termelés leáll, és gondoskodni kell arról, hogy a legutóbbi és a legutóbbi tranzakciónapló biztonsági mentése átkerüljön az Azure adatbázis-kezelő üzembe helyezésére. Ezután az Azure-beli adatbázis-kezelő üzembe helyezése megnyílt az éles környezetben.  



### <a name="database-mirroring"></a>Adatbázis-tükrözés
Az SAP által támogatott adatbázis-tükrözés (lásd: [965908]-es SAP-Megjegyzés) a feladatátvételi partner definiálására támaszkodik az SAP-kapcsolati karakterláncban. A létesítmények közötti esetekben feltételezzük, hogy a két virtuális gép ugyanabban a tartományban van, és a felhasználói környezet, amelyben a két SQL Server példány egy tartományi felhasználó alatt fut, valamint megfelelő jogosultságokkal rendelkezik a két SQL Server érintett példányban. Ezért az Azure-beli adatbázis-tükrözés beállítása nem különbözik egy tipikus helyszíni beállítás/konfiguráció között.

Cloud-Only üzemelő példányok esetében a legegyszerűbb módszer egy másik tartomány beállítása az Azure-ban, hogy az adatbázis-kezelő virtuális gépek (és ideális dedikált SAP virtuális gépek) egy tartományon belül legyenek.

Ha egy tartomány nem lehetséges, akkor az adatbázis-tükrözési végpontokhoz is használhat tanúsítványokat az itt leírtak szerint: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Az adatbázis-tükrözés Azure-ban való beállításának oktatóanyaga itt található: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Ahogy az a minden esetben támogatja a helyszíni SAP-t (lásd: SAP-Megjegyzés [1772688]), az SAP-vel együtt támogatott az Azure-ban. Az SQL Server rendelkezésre állási csoport figyelője (nem tévesztendő össze az Azure rendelkezésre állási készlettel) üzembe helyezésének néhány különleges szempontja van, mivel az Azure ebben az időpontban nem teszi lehetővé AD/DNS-objektum létrehozását, mivel az lehetséges a helyszínen. Ezért néhány különböző telepítési lépésre van szükség az Azure adott viselkedésének leküzdéséhez.

A rendelkezésre állási csoport figyelőjét használó megfontolások a következők:

* A rendelkezésre állási csoport figyelője csak a Windows Server 2012 vagy újabb verzióban lehetséges a virtuális gép vendég operációs rendszereként. A Windows Server 2012-hez meg kell győződnie arról, hogy a javítás alkalmazása megtörténik: <https://support.microsoft.com/kb/2854082> 
* A Windows Server 2008 R2 esetében ez a javítás nem létezik, és a mindig az adatbázis-tükrözéssel megegyező módon kell használni, ha a kapcsolati sztringben megadta a feladatátvételi partnert (az alapértelmezett SAP-n keresztül. PFL paraméter: adatbázisok/MSS/kiszolgáló – lásd: SAP-Megjegyzés [965908]).
* A rendelkezésre állási csoport figyelője használatakor az adatbázis-alapú virtuális gépeket dedikált Load Balancerhoz kell csatlakoztatni. Annak érdekében, hogy az Azure ne rendeljen új IP-címeket azokhoz az esetekhez, ahol mindkét virtuális gép leállt, az egyiknek statikus IP-címet kell rendelnie ezeknek a virtuális gépeknek a hálózati adapteréhez az Always On Configuration (statikus [IP-cím meghatározása című cikkben)][virtual-networks-reserved-private-ip] .
* Speciális lépések szükségesek a WSFC-fürt konfigurálásához, ahol a fürthöz hozzá kell rendelni egy speciális IP-címet, mert az Azure és a jelenlegi funkciója a fürt nevét ugyanazzal az IP-címmel rendeli hozzá, mint a fürt létrehozásához használt csomópontot. Ez a viselkedés azt jelenti, hogy manuális lépést kell végrehajtani egy másik IP-cím fürthöz való hozzárendeléséhez.
* A rendelkezésre állási csoport figyelője az Azure-ban TCP/IP-végpontokkal lesz létrehozva, amelyek a rendelkezésre állási csoport elsődleges és másodlagos replikáit futtató virtuális gépekhez vannak rendelve.
* Előfordulhat, hogy a végpontokat ACL-ekkel kell védenie.

Részletes dokumentáció a SQL Server Azure-beli virtuális gépeken történő üzembe helyezéséhez, például:

- [SQL Server always on rendelkezésre állási csoportok bemutatása az Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)szolgáltatásban.
- [Always On rendelkezésre állási csoport konfigurálása az Azure-beli virtuális gépeken különböző régiókban](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
- [Terheléselosztó konfigurálása az Azure always on rendelkezésre állási csoportjához](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

>[!NOTE]
> Ha az Azure Load balancert konfigurálja a rendelkezésre állási csoport figyelő virtuális IP-címéhez, győződjön meg arról, hogy a DirectServerReturn konfigurálva van. a beállítás konfigurálása csökkenti az SAP-alkalmazás rétege és az adatbázis-kezelő réteg közötti hálózati adatelérési időt. 

A SQL Server always on a leggyakrabban használt magas rendelkezésre állású és vész-helyreállítási funkciók az Azure-ban az SAP számítási feladatainak üzembe helyezéséhez. A legtöbb ügyfél mindig a magas rendelkezésre állást használja egyetlen Azure-régión belül. Ha a központi telepítés csak két csomópontra korlátozódik, két lehetőség közül választhat:

- A rendelkezésre állási csoport figyelője használatával. A rendelkezésre állási csoport figyelője esetében telepítenie kell egy Azure Load balancert. Így a telepítés alapértelmezett módszere. Az SAP-alkalmazások úgy lesznek konfigurálva, hogy csatlakozzanak a rendelkezésre állási csoport figyelője és nem egyetlen csomóponton
- SQL Server adatbázis-tükrözés kapcsolati paramétereinek használata. Ebben az esetben konfigurálnia kell az SAP-alkalmazások kapcsolatát úgy, hogy mindkét csomópont nevét elnevezi. Az SAP-oldal konfigurációjának pontos részleteit az SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908)dokumentációja ismerteti. Ha ezt a lehetőséget választja, nem kell konfigurálnia a rendelkezésre állási csoport figyelőjét. És azzal, hogy a SQL Server magas rendelkezésre állásához nem rendelkezik Azure Load balancerrel. Ennek eredményeképpen az SAP-alkalmazás rétege és az adatbázis-kezelő réteg közötti hálózati késés alacsonyabb, mert az SQL Server-példány felé irányuló bejövő forgalom nem az Azure Load Balancer használatával van átirányítva. Ha azonban ezt a lehetőséget választja, akkor ez a lehetőség csak akkor működik, ha a rendelkezésre állási csoport két példányra van korlátozva. 

Az Azure-régiók közti további vész-helyreállítási funkciók esetében a sok ügyfelünk kihasználja a SQL Server always on funkciót. Több ügyfél is használhatja a biztonsági mentéseket egy másodlagos replikáról. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server transzparens adattitkosítás
SQL Server [transzparens adattitkosítást (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) használó ügyfelek száma az SAP SQL Server-adatbázisainak az Azure-ban való telepítésekor. Az SAP a SQL Server TDE funkcióit teljes mértékben támogatja (lásd: SAP-Megjegyzés [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>SQL Server TDE alkalmazása
Azokban az esetekben, amikor egy másik, a helyszínen futtatott adatbázisból származó, heterogén áttelepítést hajt végre az Azure-ban futó Windows/SQL Server rendszerre, az üres céladatbázis SQL Server előtt kell létrehoznia. A következő lépésként alkalmazza SQL Server TDE funkciót. Miközben továbbra is a helyi üzemi rendszert futtatja. Ennek a lépésnek az az oka, hogy az üres adatbázis titkosításának folyamata hosszabb ideig is eltarthat. Az SAP importálási folyamatai ezután importálják az adatbevitelt a titkosított adatbázisba az állásidős fázisban. A titkosított adatbázisba való importálás a legkevesebb időt befolyásolja, mint az adatbázis titkosítása az exportálási fázis letelt időfázisában. Negatív élmények történtek, amikor a rendszer megpróbálta alkalmazni a TDE-t az adatbázis tetején futó SAP-munkaterheléssel. Ezért javasoljuk, hogy a TDE üzembe helyezését olyan tevékenységként kezelje, amelyet SAP-munkaterhelés nélkül kell elvégezni az adott adatbázison.

Azokban az esetekben, amikor a helyszíni SAP SQL Server-adatbázisokat az Azure-ba helyezi át, javasoljuk, hogy tesztelje, melyik infrastruktúrát lehet a lehető leggyorsabbvé tennie. Ehhez tartsa szem előtt ezeket a tényeket:

- Nem határozható meg, hogy a rendszer hány szálat alkalmazzon adattitkosításra az adatbázison. A szálak száma nagy mértékben függ attól, hogy hány köteten vannak elosztva a SQL Server-adat és a naplófájlok. Azt jelenti, hogy a több különböző kötet (meghajtóbetűjel), a több szál párhuzamosan fog megjelenni a titkosítás végrehajtásához. Egy ilyen konfiguráció a korábbi lemezes konfigurációval kapcsolatos javaslattal ellentétben egy vagy kevesebb tárolóhely létrehozásához az Azure-beli virtuális gépek SQL Server adatbázisfájlok számára. Egy kis mennyiségű kötettel rendelkező konfiguráció kis számú, a titkosítást végrehajtó szálat eredményezne. Az egyszálas titkosítás 64 KB-os értéket olvas, titkosítja, majd egy rekordot ír a tranzakciós naplófájlba, ami azt jelzi, hogy a mérték titkosított. Ennek eredményeképpen a tranzakciós napló terhelése mérsékelt.
- A régebbi SQL Server kiadásokban a biztonsági másolatok tömörítése többé nem tudta megszerezni a hatékonyságot, amikor titkosította a SQL Server-adatbázist. Ez a viselkedés olyan problémákhoz vezethet, amikor a csomag a SQL Server-adatbázis helyszíni titkosítását, majd az Azure-ban történő biztonsági mentést másolja az Azure-ban. SQL Server biztonsági mentési tömörítés általában a 4. faktor tömörítési arányát éri el.
- A SQL Server 2016 SQL Server olyan új funkciókat vezetett be, amelyek lehetővé teszik a titkosított adatbázisok hatékony tömörítését is. További részletekért tekintse meg [ezt a blogot](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) .
 
A TDE titkosítás alkalmazásának kezelése csak kis SAP-munkaterhelések nélkül, az adott konfigurációban kell tesztelni, hogy jobb-e az TDE alkalmazása a helyszíni SAP-adatbázisra, vagy az Azure-ban. Az Azure-ban bizonyára nagyobb rugalmasságot biztosít a több mint kiépítési infrastruktúra és az infrastruktúra zsugorodása a TDE alkalmazása után.

### <a name="using-azure-key-vault"></a>Azure Key Vault használata
Az Azure biztosítja a titkosítási kulcsok tárolására szolgáló [Key Vault](https://azure.microsoft.com/services/key-vault/) szolgáltatását. SQL Server a másik oldalon egy összekötőt biztosítanak, amely a TDE-tanúsítványok tárolására szolgáló Azure Key Vault használja.

További részletek a Azure Key Vault SQL Server TDE-listához, például:

- [Bővíthető kulcskezelő Azure Key Vault (SQL Server) használatával](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- [SQL Server a TDE Azure Key Vault telepítésének lépéseivel bővíthető](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault).
- [SQL Server Connector karbantartási & hibaelhárítás](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting).
- [További kérdések a SQL Server transzparens adattitkosítás – TDE + Azure Key Vault ügyfeleitől](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault).


>[!IMPORTANT]
>SQL Server TDE, különösen az Azure Key Vault használata esetén ajánlott a SQL Server 2014, SQL Server 2016 és SQL Server 2017 legújabb javításait használni. Ennek oka az, hogy az ügyfelek visszajelzései, az optimalizálások és a javítások a kódra lettek alkalmazva. Példaként tekintse meg a [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Általános SQL Server az SAP-hez az Azure-ban – összefoglalás
Ebben az útmutatóban számos javaslat található, és azt javasoljuk, hogy az Azure-beli üzembe helyezés megtervezése előtt többször is olvassa el. Általánosságban azonban mindenképp kövesse az Azure-specifikus javaslatok legfontosabb általános adatbázis-kezelői szolgáltatásait:

1. Használja a legújabb adatbázis-kezelői kiadást, mint például a SQL Server 2017, amely az Azure legtöbb előnyét biztosítja. 
2. Körültekintően tervezze meg az SAP-rendszerkörnyezetet az Azure-ban az adatfájlok elrendezésének és az Azure-korlátozások kiegyensúlyozásához:
   * Ne legyen túl sok lemeze, de elegendő ahhoz, hogy el tudja érni a szükséges IOPS.
   * Ha nem használja a Managed Diskst, ne feledje, hogy az Azure Storage-fiókok IOPS is korlátozottak, és a Storage-fiókok az egyes Azure-előfizetéseken belül korlátozottak ([További részletek][azure-resource-manager/management/azure-subscription-service-limits]). 
   * Csak a lemezek közötti sáv, ha magasabb átviteli sebességet kell elérnie.
3. Soha ne telepítsen szoftvert, vagy ne helyezzen olyan fájlokat, amelyek adatmegőrzést igényelnek a D:\ Ez a meghajtó nem állandó, és a meghajtón található összes adat elvész a Windows újraindításakor.
4. Ne használjon lemezes gyorsítótárazást az Azure standard Storage-hoz.
5. Ne használja az Azure geo-replikált Azure standard Storage-fiókokat.  Az adatbázis-kezelői munkaterhelések esetében helyileg redundánsan használható.
6. Az adatbázis-adatai replikálásához használja az adatbázis-KEZELŐi gyártójának HA/DR-megoldását.
7. Mindig használja a névfeloldást, ne bízza az IP-címekre.
8. SQL Server TDE alkalmazásával alkalmazza a legújabb SQL Server-javításokat.
9. Használja a legmagasabb adatbázis-tömörítést. A SQL Server oldalának tömörítése.
10. Ügyeljen arra, hogy SQL Server rendszerképeket használjon az Azure piactéren. Ha a SQL Server használja, akkor az SAP NetWeaver rendszer telepítése előtt módosítania kell a példány rendezését.
11. Telepítse és konfigurálja az Azure SAP-gazdagépének figyelését a [telepítési útmutató][deployment-guide]című témakörben leírtak szerint.


## <a name="next-steps"></a>Következő lépések
A cikk elolvasása 

- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](dbms_guide_general.md)