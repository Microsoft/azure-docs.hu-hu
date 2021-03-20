---
title: Oracle Azure Virtual Machines adatbázis-kezelői üzembe helyezés SAP-munkaterheléshez | Microsoft Docs
description: Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, Oracle, adatvédelem
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/18/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29b1bcec58d6350d0f63c3fe0ce11ef99a648019
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668978"
---
# <a name="azure-virtual-machines-oracle-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines Oracle adatbázis-kezelő üzembe helyezése SAP-munkaterheléshez

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

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


Ez a dokumentum több különböző területet ölel fel, amelyeket figyelembe kell venni, amikor az Azure IaaS-ben üzembe helyezi az SAP-munkaterhelések Oracle Databaseét. A dokumentum elolvasása előtt javasoljuk, hogy olvassa el [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésével kapcsolatos szempontokat az SAP-munkaterheléshez](dbms_guide_general.md). Azt is javasoljuk, hogy olvassa el az Azure- [dokumentáció SAP-munkaterhelésével kapcsolatos](./get-started.md)egyéb útmutatókat is. 

Az SAP az Azure-on való futtatásához támogatott Oracle-verziókról és a megfelelő operációsrendszer-verziókról a [2039619]-es SAP-megjegyzésekben talál információt.

Az SAP Business Suite Oracle-on való futtatásával kapcsolatos általános információk az [SAP on Oracle](https://www.sap.com/community/topic/oracle.html)webhelyen találhatók.
Az Oracle támogatja az Oracle-szoftverek futtatását Microsoft Azureon. A Windows Hyper-V és az Azure általános támogatásával kapcsolatos további információkért olvassa el az [Oracle és a Microsoft Azure gyakori kérdések](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)című témakört. 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Az Oracle, az SAP és az Azure fontos SAP-megjegyzései 

A következő SAP-megjegyzések az Azure-beli SAP-vel kapcsolatosak.

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: támogatott termékek és Azure-beli virtuális gépek típusai |
| [2015553] |SAP on Microsoft Azure: támogatási előfeltételek |
| [1999351] |Az SAP-hez készült bővített Azure monitoring hibaelhárítása |
| [2178632] |Az SAP-hez Microsoft Azure legfontosabb monitorozási metrikák |
| [2191498] |SAP Linuxon az Azure-ban: továbbfejlesztett monitorozás |
| [2039619] |SAP-alkalmazások Microsoft Azure az Oracle Database használatával: támogatott termékek és verziók |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP-licenccel kapcsolatos problémák |
| [2069760] |Oracle Linux 7. x SAP telepítése és frissítése |
| [1597355] |A Linux rendszerhez készült swap-space-javaslat |
| [2171857] |Oracle Database 12c – Linux rendszeren támogatott fájlrendszer-támogatás |
| [1114181] |Oracle Database 11g – Linux rendszeren támogatott fájlrendszer-támogatás |

Az Oracle és az SAP által az Azure-ban támogatott pontos konfigurációkat és funkciókat az SAP Megjegyzés [#2039619](https://launchpad.support.sap.com/#/notes/2039619)dokumentálja.

A Windows és a Oracle Linux az Azure-ban az Oracle és az SAP által támogatott operációs rendszerek. A széles körben használt SLES és RHEL Linux-disztribúciók nem használhatók Oracle-összetevők Azure-beli üzembe helyezéséhez. Az Oracle-összetevők közé tartozik az Oracle Database-ügyfél, amelyet az SAP-alkalmazások használnak az Oracle adatbázis-kezelői szolgáltatáshoz való kapcsolódáshoz. 

A kivételek az SAP-Megjegyzés [#2039619](https://launchpad.support.sap.com/#/notes/2039619)szerint olyan SAP-összetevők, amelyek nem használják a Oracle Database ügyfelet. Ilyen SAP-összetevők az SAP önálló sorba helyezni, az üzenetküldési kiszolgáló, a sorba helyezni replikációs szolgáltatások, a webfeladó és az SAP-átjáró.  

Akkor is futtathatja az SAP központi szolgáltatásait SLES vagy RHEL, ha az Oracle adatbázis-kezelő és az SAP-alkalmazások példányait is futtatja a Oracle Linuxon, és egy pacemaker-alapú fürtön is megóvja azt. A pacemaker magas rendelkezésre állási keretrendszerként nem támogatott Oracle Linuxon.

## <a name="specifics-for-oracle-database-on-windows"></a>A Windows Oracle Database vonatkozó sajátosságai

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Oracle-konfigurációs irányelvek az SAP-telepítésekhez az Azure-beli virtuális gépeken Windows rendszeren

Az SAP telepítési kézikönyvének megfelelően az Oracle-hez kapcsolódó fájlokat nem kell telepíteni, vagy a virtuális gép operációsrendszer-lemezén kell elhelyezni (c meghajtó:). A különböző méretű virtuális gépek különböző számú csatlakoztatott lemezt tudnak támogatni. A kisebb virtuálisgép-típusok kisebb számú csatlakoztatott lemezt is támogatnak. 

Ha kisebb virtuális gépekkel rendelkezik, és eléri a virtuális géphez csatlakoztatható lemezek számát, akkor az Oracle Home, a Stage, a, `saptrace` `saparch` a, `sapbackup` `sapcheck` vagy `sapreorg` az operációs rendszer lemezét is telepítheti/megkeresheti. Az Oracle adatbázis-kezelő összetevőinek ezen részei nem túl erősek az I/O-és az I/O-átviteli sebességnél. Ez azt jelenti, hogy az operációsrendszer-lemez képes kezelni az I/O-követelményeket. Az operációsrendszer-lemez alapértelmezett méretének 127 GB-nak kell lennie. 

A naplófájlokat Oracle Database és újra külön adatlemezeken kell tárolni. Kivételt jelent az Oracle ideiglenes tablespace-je. `Tempfiles` létrehozható a D:/használatával (nem állandó meghajtó). A nem állandó D:\ a meghajtó jobb I/O-késést és átviteli sebességet is biztosít (az a sorozatú virtuális gépek kivételével). 

A megfelelő mennyiségének meghatározásához a `tempfiles` meglévő rendszerek méreteit is megtekintheti `tempfiles` .

### <a name="storage-configuration"></a>Tároló konfigurálása
Csak az NTFS fájlrendszerű lemezeket használó egypéldányos Oracle támogatott. Az összes adatbázisfájlt a Managed Disks NTFS fájlrendszerén (ajánlott) vagy virtuális merevlemezeken kell tárolni. Ezek a lemezek az Azure-beli virtuális géphez vannak csatlakoztatva, és az [Azure-oldal blob Storage](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) vagy az [Azure Managed Diskson](../../managed-disks-overview.md)alapulnak. 

Tekintse meg az [Azure Storage-beli tárolási típusait az SAP](./planning-guide-storage.md) számítási feladatokhoz című cikket, amely részletesen ismerteti az adatbázis-kezelő számítási feladatokhoz megfelelő Azure Block-tárolók

Erősen ajánlott az [Azure Managed Disks](../../managed-disks-overview.md)használata. Javasoljuk továbbá, hogy az [Azure Premium Storage-t vagy az Azure Ultra diskt](../../disks-types.md) használja a Oracle Database üzemelő példányokhoz.

A hálózati meghajtók vagy távoli megosztások, például az Azure file Services nem támogatottak Oracle Database fájlok esetében. További információkért lásd:

- [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


Ha az Azure-beli blob Storage-on vagy a Managed Disks-on alapuló lemezeket használ, akkor az [azure Virtual Machines adatbázis-kezelő rendszerbe állítása az SAP-munkaterhelésre](dbms_guide_general.md) című szakaszban szereplő utasítások a Oracle Database is érvényesek az üzemelő példányokra.

Az Azure-lemezek IOPS átviteli sebességére vonatkozó kvóták léteznek. Ezt a koncepciót az [Azure Virtual Machines adatbázis-kezelői szolgáltatás SAP-munkaterheléshez való üzembe helyezésének szempontjai](dbms_guide_general.md)ismertetik. A pontos kvóták a használt virtuális gép típusától függenek. A kvótákkal rendelkező virtuálisgép-típusok listáját az [Azure-beli Windows rendszerű virtuális gépek méreteiben][virtual-machines-sizes-windows]találja.

A támogatott Azure-beli virtuálisgép-típusok azonosításához lásd: SAP-Megjegyzés [1928533].

A minimális konfiguráció a következő: 

| Összetevő | Lemez | Gyorsítótárazás | Storage-készlet |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA & mirrlogB | Prémium vagy Ultra Disk | Nincsenek | Nem szükséges |
| \oracle \<SID> \origlogaB & mirrlogA | Prémium vagy Ultra Disk | Nincsenek | Nem szükséges |
| \oracle \<SID> \sapdata1... n | Prémium vagy Ultra Disk | Csak olvasható | Prémium szintű használatra is használható |
| \oracle \<SID> \oraarch | Standard | Nincsenek | Nem szükséges |
| Oracle Home, `saptrace` ,... | OPERÁCIÓSRENDSZER-lemez (prémium) | | Nem szükséges |


Az online visszaállítási naplók futtatásához szükséges lemezeket IOPS követelményekkel kell vezérelni. Az összes sapdata1 tárolhatók... n (tablespaces) egyetlen csatlakoztatott lemezen, ha a méret, a IOPS és az átviteli sebesség megfelel a követelményeknek. 

A teljesítmény konfigurációja a következő:

| Összetevő | Lemez | Gyorsítótárazás | Storage-készlet |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA | Prémium vagy Ultra Disk | Nincsenek | Prémium szintű használatra is használható  |
| \oracle \<SID> \origlogaB | Prémium vagy Ultra Disk | Nincsenek | Prémium szintű használatra is használható |
| \oracle \<SID> \mirrlogAB | Prémium vagy Ultra Disk | Nincsenek | Prémium szintű használatra is használható |
| \oracle \<SID> \mirrlogBA | Prémium vagy Ultra Disk | Nincsenek | Prémium szintű használatra is használható |
| \oracle \<SID> \sapdata1... n | Prémium vagy Ultra Disk | Csak olvasható | Prémium szintű ajánlott  |
| \oracle\SID\sapdata (n + 1) * | Prémium vagy Ultra Disk | Nincsenek | Prémium szintű használatra is használható |
| \oracle \<SID> \oraarch * | Prémium vagy Ultra Disk | Nincsenek | Nem szükséges |
| Oracle Home, `saptrace` ,... | OPERÁCIÓSRENDSZER-lemez (prémium) | Nem szükséges |

* (n + 1): üzemeltetési rendszerek, TEMP és visszavonás tablespaces. A rendszer és a visszavonási eszközök I/O-mintája eltér más, az alkalmazásadatok futtatására szolgáló tablespace-modelltől. A rendszer teljesítményének és az tablespace-EK visszavonásának legjobb lehetősége a gyorsítótárazás.

* oraarch: a tárolási készlet nem szükséges a teljesítmény szempontjából. Több tárhelyet is felhasználhat.

Ha az Azure Premium Storage esetében további IOPS van szükség, javasoljuk, hogy a Windows Storage-készleteket (csak a Windows Server 2012-es és újabb verzióiban érhető el) hozzon létre egy nagyméretű logikai eszközt több csatlakoztatott lemezen. Ez a megközelítés leegyszerűsíti az adminisztrációs terhelést a lemezterület kezeléséhez, és segít elkerülni a fájlok manuális terjesztését több csatlakoztatott lemez között.


#### <a name="write-accelerator"></a>Írásgyorsító
Az Azure M sorozatú virtuális gépek esetében az Azure Premium Storage-hoz képest az online ismétlési naplókba való írás késése is csökkenhet. Engedélyezze az Azure-írásgyorsító a lemezekhez (VHD-k) az Azure-Premium Storage alapján, amelyek online visszaállítási naplófájlokhoz használatosak. További információ: [írásgyorsító](../../how-to-enable-write-accelerator.md). Vagy használja az Azure Ultra diskt az online visszaállítási naplózási kötethez.


### <a name="backuprestore"></a>Biztonsági mentés/visszaállítás
A biztonsági mentési/visszaállítási funkciók esetében az SAP BR * Tools for Oracle ugyanúgy támogatott, mint a szabványos Windows Server operációs rendszereken. Az Oracle Recovery Manager (Oláh Anna) szintén támogatott a lemezre történő biztonsági mentéshez és a lemezről történő visszaállításhoz.

Az alkalmazással konzisztens virtuális gépek biztonsági mentésének futtatásához Azure Backup is használhatja. A [virtuális gépek biztonsági mentési infrastruktúrájának megtervezése az Azure-ban](../../../backup/backup-azure-vms-introduction.md) című cikk azt ismerteti, hogyan használja a Azure Backup a Windows VSS funkciót az alkalmazás-konzisztens biztonsági mentések végrehajtásához. Az SAP által az Azure által támogatott Oracle-adatbázis-kiadások kihasználhatják a VSS funkcióit a biztonsági mentésekhez. További információ: az Oracle dokumentációja az [adatbázisok biztonsági mentésével és helyreállításával kapcsolatos alapvető fogalmakról a VSS-vel](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre állás és a vész-helyreállítási célú Oracle-adatőr támogatott. Az automatikus feladatátvétel eléréséhez az adatvédelemben Fast-Start feladatátvételt (FSFA) kell használnia. A megfigyelő (FSFA) elindítja a feladatátvételt. Ha nem használja a FSFA-t, akkor csak manuális feladatátvételi konfigurációt használhat.

Az Azure-beli Oracle-adatbázisok vész-helyreállításával kapcsolatos további információkért lásd: vész- [helyreállítás egy Oracle Database 12c-adatbázishoz egy Azure-környezetben](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Az Oracle-környezetek Windows rendszeren való üzembe helyezéséhez erősen ajánlott a gyorsított hálózatkezelés az [Azure gyorsított hálózatkezelésben](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)leírtak szerint. Tekintse át az [Azure Virtual Machines adatbázis-kezelői szolgáltatás SAP-munkaterheléshez való üzembe helyezésének szempontjaival](dbms_guide_general.md)kapcsolatos javaslatokat is. 
### <a name="other"></a>Egyéb
[Az azure Virtual Machines adatbázis-kezelői szolgáltatás SAP-alapú üzembe helyezésével kapcsolatos megfontolások](dbms_guide_general.md) a virtuális gépek Oracle Database-vel való üzembe helyezésével kapcsolatos egyéb fontos fogalmakat, például az Azure rendelkezésre állási készleteit és az SAP

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>A Oracle Linux Oracle Database vonatkozó sajátosságai
Az Oracle olyan Oracle-szoftvereket támogat, amelyeken a Microsoft Azure Oracle Linux vendég operációs rendszerként fut. A Windows Hyper-V és az Azure általános támogatásával kapcsolatos további információkért tekintse meg az [Azure és az Oracle gyakori kérdések](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)című témakört. 

Az Oracle-adatbázisokat hasznosító SAP-alkalmazások adott forgatókönyve is támogatott. A részleteket a dokumentum következő részében tárgyaljuk.

### <a name="oracle-version-support"></a>Oracle-verziók támogatása
További információ arról, hogy az Oracle-verziók és a hozzá tartozó operációsrendszer-verziók milyen támogatottak a SAP Azure-beli Virtual Machines való futtatásához: SAP-Megjegyzés [2039619].

Az SAP Business Suite Oracle-on való futtatásával kapcsolatos általános információk az [SAP on Oracle Community oldalon](https://www.sap.com/community/topic/oracle.html)találhatók.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Oracle-konfigurációs irányelvek az SAP-telepítésekhez az Azure-beli virtuális gépeken Linux rendszeren

Az SAP-telepítési kézikönyvek szerint az Oracle-hez kapcsolódó fájlokat nem kell telepíteni, vagy a virtuális gép rendszermeghajtóján kell elhelyezni. A különböző méretű virtuális gépek különböző számú csatlakoztatott lemezt támogatnak. A kisebb virtuálisgép-típusok kisebb számú csatlakoztatott lemezt is támogatnak. 

Ebben az esetben javasoljuk, hogy telepítse vagy keresse meg az Oracle Home, a Stage, a, a, `saptrace` `saparch` `sapbackup` `sapcheck` `sapreorg` a vagy a rendszerindító lemezt. Az Oracle adatbázis-kezelői összetevőinek ezen részei nem intenzívek az I/O-és az I/O-átviteli sebességnél. Ez azt jelenti, hogy az operációsrendszer-lemez képes kezelni az I/O-követelményeket. Az operációsrendszer-lemez alapértelmezett mérete 30 GB. A rendszerindító lemezt kibonthatja a Azure Portal, a PowerShell vagy a parancssori felület használatával. A rendszerindító lemez kibontása után további partíciót is hozzáadhat az Oracle bináris fájljaihoz.


### <a name="storage-configuration"></a>Tároló konfigurálása

A (z) ext4, XFS, NFSv 4.1 (csak Azure NetApp Files (ANF)) vagy Oracle ASM fájlrendszerben (lásd az SAP-Megjegyzés [#2039619](https://launchpad.support.sap.com/#/notes/2039619) kiadási/verziószámi követelmények esetén) támogatottak az Azure-beli Oracle Database-fájlok esetében. Az összes adatbázisfájlt a VHD-k, Managed Disks vagy ANF alapján kell tárolni ezeken a fájlrendszereken. Ezek a lemezek az Azure-beli virtuális géphez vannak csatlakoztatva, és az [Azure Page blob Storage](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs), az [Azure Managed Disks](../../managed-disks-overview.md)vagy a [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)alapján érhetők el.

Minimális követelmények listája, például: 

- Oracle Linux UEK kernelek esetében az [Azure Premium SSD](../../premium-storage-performance.md#disk-caching)-k támogatásához legalább 4 UEK 4-es verzió szükséges.
- Az Oracle és a ANF esetében a minimális támogatott Oracle Linux 8,2.
- Az Oracle és a ANF esetében a támogatott Oracle-verzió a 19c (19.8.0.0)

A cikkből megtudhatja, hogyan használható az [Azure Storage az SAP](./planning-guide-storage.md) számítási feladatokhoz, hogy további részleteket kapjon az adatbázis-kezelő számítási feladatokhoz megfelelő Azure Block-tárolási típusok

Az Azure Block Storage használata esetén erősen ajánlott az [Azure Managed Disks](../../managed-disks-overview.md) és az [Azure Premium SSD](../../disks-types.md) -k használata az Oracle Database üzemelő példányokhoz.

A Azure NetApp Files, más megosztott lemezek, hálózati meghajtók vagy távoli megosztások, például az Azure file Services (AFS) nem támogatottak Oracle Database fájlokhoz. További információkat a következő cikkekben talál: 

- [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Ha az Azure-beli blob Storage vagy a Managed Disks alapján lemezeket használ, az [azure Virtual Machines adatbázis-kezelői szolgáltatás az SAP-munkaterheléshez való üzembe helyezésével kapcsolatos megfontolások](dbms_guide_general.md) a Oracle Database is érvényesek.

Az Azure-lemezek IOPS átviteli sebességére vonatkozó kvóták léteznek. Ezt a koncepciót az [Azure Virtual Machines adatbázis-kezelői szolgáltatás SAP-munkaterheléshez való üzembe helyezésének szempontjai](dbms_guide_general.md)ismertetik. A pontos kvóták a használt virtuális gép típusától függenek. A kvótákkal rendelkező virtuálisgép-típusok listáját lásd: [a Linux rendszerű virtuális gépek méretei az Azure-ban][virtual-machines-sizes-linux].

A támogatott Azure-beli virtuálisgép-típusok azonosításához lásd: SAP-Megjegyzés [1928533].

Minimális konfiguráció:

| Összetevő | Lemez | Gyorsítótárazás | Csíkot |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA & mirrlogB | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Nem szükséges |
| /Oracle/ \<SID> /origlogaB & mirrlogA | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Nem szükséges |
| /Oracle/ \<SID> /sapdata1... n | Prémium, ultrakönnyű lemez vagy ANF | Csak olvasható | Prémium szintű használatra is használható |
| /Oracle/ \<SID> /oraarch | Standard vagy ANF | Nincsenek | Nem szükséges |
| Oracle Home, `saptrace` ,... | OPERÁCIÓSRENDSZER-lemez (prémium) | | Nem szükséges |

* Kiszerelés: LVM Stripe vagy MDADM a RAID0 használatával

Az Oracle online visszaállítási naplóinak üzemeltetéséhez szükséges lemezeket IOPS követelményekkel kell ellátni. Az összes sapdata1 tárolhatók... n (tablespaces) egyetlen csatlakoztatott lemezen, ha a kötet, a IOPS és az átviteli sebesség megfelel a követelményeknek. 

Teljesítmény konfigurációja:

| Összetevő | Lemez | Gyorsítótárazás | Csíkot |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Prémium szintű használatra is használható  |
| /Oracle/ \<SID> /origlogaB | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Prémium szintű használatra is használható |
| /Oracle/ \<SID> /mirrlogAB | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Prémium szintű használatra is használható |
| /Oracle/ \<SID> /mirrlogBA | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Prémium szintű használatra is használható |
| /Oracle/ \<SID> /sapdata1... n | Prémium, ultrakönnyű lemez vagy ANF | Csak olvasható | Prémium szintű ajánlott  |
| /Oracle/ \<SID> /sapdata (n + 1) * | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Prémium szintű használatra is használható |
| /Oracle/ \<SID> /oraarch * | Prémium, ultrakönnyű lemez vagy ANF | Nincsenek | Nem szükséges |
| Oracle Home, `saptrace` ,... | OPERÁCIÓSRENDSZER-lemez (prémium) | Nem szükséges |

* Kiszerelés: LVM Stripe vagy MDADM a RAID0 használatával

* (n + 1): üzemeltetési rendszer, ideiglenes és visszavonási tablespaces: a rendszer és a visszavonási tablespaces I/O-mintázata eltér más, az alkalmazásadatok futtatására szolgáló eszközöktől. A rendszer teljesítményének és az tablespace-EK visszavonásának legjobb lehetősége a gyorsítótárazás.

* oraarch: a tárolási készlet nem szükséges a teljesítmény szempontjából.


Ha további IOPS van szükség az Azure Premium Storage használatakor, javasoljuk, hogy az LVM (logikai kötet-kezelő) vagy a MDADM használatával hozzon létre egy nagy logikai kötetet több csatlakoztatott lemezen. További információkért lásd: [Az Azure Virtual Machines adatbázis-kezelő rendszerbeli üzembe helyezésének szempontjai az SAP](dbms_guide_general.md) -számítási feladatokhoz, valamint az LVM vagy a MDADM kihasználása az irányelvek és mutatók alapján. Ez a megközelítés leegyszerűsíti a lemezterület kezelésének adminisztrációs terhelését, és segít elkerülni a fájlok manuális terjesztését több csatlakoztatott lemez között.

Ha azt tervezi, hogy Azure NetApp Files használni, ellenőrizze, hogy a dNFS-ügyfél megfelelően van-e konfigurálva. A dNFS használata kötelező, ha támogatott környezettel rendelkezik. A dNFS konfigurációját a [Oracle Database közvetlen NFS-en való létrehozása](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA)című cikkben dokumentálja.

Az Azure NetApp Files-alapú NFS Oracle-adatbázisok használatát bemutató példa az [SAP AnyDB (Oracle 19c) Azure NetApp Files használatával történő üzembe helyezését](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)mutatja be a blogban.


#### <a name="write-accelerator"></a>Írásgyorsító
Az Azure M sorozatú virtuális gépek esetében az Azure írásgyorsító használatakor az Azure Premium Storage használatakor a késések az online ismétlési naplókba való beírásával csökkenthetők. Engedélyezze az Azure-írásgyorsító a lemezekhez (VHD-k) az Azure-Premium Storage alapján, amelyek online visszaállítási naplófájlokhoz használatosak. További információ: [írásgyorsító](../../how-to-enable-write-accelerator.md). Vagy használja az Azure Ultra diskt az online visszaállítási naplózási kötethez.


### <a name="backuprestore"></a>Biztonsági mentés/visszaállítás
A biztonsági mentési/visszaállítási funkciók esetében az SAP BR * Tools for Oracle ugyanúgy támogatott, mint az operációs rendszer nélküli és a Hyper-V. Az Oracle Recovery Manager (Oláh Anna) szintén támogatott a lemezre történő biztonsági mentéshez és a lemezről történő visszaállításhoz.

Az Oracle-adatbázisok biztonsági mentésére és helyreállítására szolgáló Azure Backup és Recovery Services használatával kapcsolatos további információkért lásd: [Oracle Database 12c-adatbázis biztonsági mentése és helyreállítása Azure Linux rendszerű virtuális gépen](../oracle/oracle-overview.md).

### <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre állás és a vész-helyreállítási célú Oracle-adatőr támogatott. Az automatikus feladatátvétel az adatvédelemben való eléréséhez Fast-Start feladatátvételt (FSFA) kell használnia. A megfigyelő funkció (FSFA) elindítja a feladatátvételt. Ha nem használja a FSFA-t, akkor csak manuális feladatátvételi konfigurációt használhat. További információ: Oracle- [Adatvédelem megvalósítása Azure Linux rendszerű virtuális gépen](../oracle/configure-oracle-dataguard.md).


Az Azure-beli Oracle-adatbázisok vész-helyreállítási szempontjait az Azure- [környezetben egy Oracle Database 12c-adatbázis vész-helyreállítását](../oracle/oracle-disaster-recovery.md)ismertető cikk ismerteti.

### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Az Azure-ban gyorsított hálózatkezelés támogatása a Oracle Linuxban Oracle Linux 7 Update 5 (Oracle Linux 7,5). Ha nem tudja frissíteni a legújabb Oracle Linux 7,5 kiadásra, lehet, hogy megkerülő megoldás a RedHat kompatibilis kernel (RHCK) használatával, az Oracle UEK kernel helyett. 

A Oracle Linuxon belüli RHEL kernel használata az SAP-Megjegyzés [#1565179](https://launchpad.support.sap.com/#/notes/1565179)szerint támogatott. Az Azure-beli gyorsított hálózatkezeléshez a minimális RHCKL kernel-kiadásnak 3.10.0-862.13.1. el7 kell lennie. Ha a UEK kernelt az [Azure gyorsított hálózatkezeléssel](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)együtt Oracle Linux használja, akkor az Oracle UEK kernel 5-ös verzióját kell használnia.

Ha olyan lemezképből helyez üzembe virtuális gépeket, amely nem az Azure Marketplace-en alapul, akkor további konfigurációs fájlokat kell átmásolnia a virtuális gépre a következő kód futtatásával: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>Következő lépések
A cikk elolvasása 

- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](dbms_guide_general.md)
