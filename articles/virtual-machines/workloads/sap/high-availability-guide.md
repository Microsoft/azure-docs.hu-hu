---
title: Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára
description: Magas rendelkezésre állási útmutató az Azure-beli SAP NetWeaver-Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6aedaf829df941a225cd0684318f28bb06ba89d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674611"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms_guide_general.md
[dbms-guide-2.1]:dbms_guide_general.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms_guide_general.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms_guide_general.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms_guide_general.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms_guide_general.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms_guide_general.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms_guide_general.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms_guide_general.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms_guide_general.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms_guide_general.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms_guide_general.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms_guide_general.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms_guide_general.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms_guide_general.md#642f746c-e4d4-489d-bf63-73e80177a0a8

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

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állási konfiguráció)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-termék rendelkezésre állási mátrixa)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Az Azure Virtual Machines a számítási, tárolási és hálózati erőforrásokat, minimális idő és hosszú beszerzési ciklusok nélkül igénylő szervezeteknek szóló megoldás. Az Azure Virtual Machines használatával olyan klasszikus alkalmazásokat telepíthet, mint például az SAP NetWeaver-alapú ABAP, a Java és a ABAP + Java stack. Kiterjesztheti a megbízhatóságot és a rendelkezésre állást további helyszíni erőforrások nélkül. Az Azure Virtual Machines támogatja a létesítmények közötti kapcsolatokat, így integrálhatja az Azure Virtual Machinest a szervezet helyi tartományában, a privát felhőkben és az SAP-rendszer környezetében.

Ebben a cikkben a magas rendelkezésre állású SAP-rendszerek üzembe helyezéséhez szükséges lépéseket ismertetjük az Azure-ban a Azure Resource Manager üzemi modell használatával. A főbb feladatok elvégzése:

* Keresse meg a megfelelő SAP-megjegyzéseket és telepítési útmutatókat, amelyek az [erőforrások][sap-ha-guide-2] szakaszban vannak felsorolva. Ez a cikk az SAP telepítési dokumentációját és az SAP-megjegyzéseket egészíti ki, amelyek az elsődleges erőforrások, amelyek segítségével az SAP-szoftvereket telepítheti és telepítheti adott platformokon.
* Ismerje meg a Azure Resource Manager üzemi modell és a klasszikus Azure üzemi modell közötti különbségeket.
* Ismerje meg a Windows Server feladatátvételi fürtszolgáltatás Kvórumának módozatait, így kiválaszthatja az Azure-beli üzembe helyezéshez legmegfelelőbb modellt.
* Ismerje meg a Windows Server feladatátvételi fürtszolgáltatás megosztott tárolóját az Azure-szolgáltatásokban.
* Ismerje meg, hogyan biztosítható az olyan egypontos meghibásodású összetevők, mint például a speciális üzleti alkalmazások programozása (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) és adatbázis-felügyeleti rendszerek (adatbázisok), valamint a redundáns összetevők, például az SAP Application Server az Azure-ban.
* Az Azure-beli Windows Server feladatátvételi Fürtszolgáltatású fürt Azure Resource Manager használatával történő telepítésének és konfigurációjának lépésenkénti példáját követve telepítheti a magas rendelkezésre állású SAP-rendszereket.
* Ismerje meg a Windows Server feladatátvételi fürtszolgáltatás Azure-ban való használatához szükséges további lépéseket, de nincs szükség helyszíni telepítésre.

Az üzembe helyezés és a konfiguráció egyszerűsítése érdekében ebben a cikkben a SAP háromrétegű magas rendelkezésre állású Resource Manager-sablonokat használjuk. A sablonok a magas rendelkezésre állású SAP-rendszerhez szükséges teljes infrastruktúra üzembe helyezését automatizálják. Az infrastruktúra Emellett támogatja az SAP-rendszeren az SAP Application Performance standard (NEDV) méretezését is.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy megfelel az alábbi szakaszokban ismertetett előfeltételeknek. Ügyeljen arra is, hogy ellenőrizze az [erőforrások][sap-ha-guide-2] szakaszban felsorolt összes erőforrást.

Ebben a cikkben a háromrétegű [SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/)Azure Resource Manager sablonjait használjuk. A sablonok hasznos áttekintését lásd: [SAP Azure Resource Manager-sablonok](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Erőforrások
Ezek a cikkek a SAP üzembe helyezéseit fedik le az Azure-ban:

* [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP NetWeaver számára][dbms-guide]
* [Azure Virtual Machines magas rendelkezésre állás az SAP NetWeaver számára (ez az útmutató)][sap-ha-guide]

> [!NOTE]
> Amikor csak lehetséges, a hivatkozó SAP telepítési útmutatóra mutató hivatkozást adunk (lásd: [SAP telepítési útmutatók][sap-installation-guides]). A telepítési folyamatra vonatkozó előfeltételek és információk esetén érdemes alaposan beolvasni az SAP NetWeaver telepítési útmutatóit. Ez a cikk csak az Azure Virtual Machines használatával használható SAP NetWeaver-alapú rendszerek meghatározott feladatait ismerteti.
>
>

Ezek az SAP-megjegyzések a SAP Azure-beli témaköréhez kapcsolódnak:

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: támogatott termékek és méretezés |
| [2015553] |SAP on Microsoft Azure: támogatási előfeltételek |
| [1999351] |Továbbfejlesztett Azure-figyelés az SAP-hoz |
| [2178632] |Az SAP-hez Microsoft Azure legfontosabb monitorozási metrikák |
| [1999351] |Virtualizáció Windows rendszeren: továbbfejlesztett figyelés |
| [2243692] |Az Azure prémium SSD Storage használata az SAP adatbázis-kezelő példányához |

További információ az [Azure-előfizetések korlátairól][azure-resource-manager/management/azure-subscription-service-limits-subscription], beleértve az általános alapértelmezett korlátozásokat és a maximális korlátozásokat.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Magas rendelkezésre állású SAP a Azure Resource Manager és a klasszikus Azure üzemi modellel
A Azure Resource Manager és a klasszikus Azure-beli üzembe helyezési modellek a következő területeken különböznek:

- Erőforráscsoportok
- Azure-beli belső terheléselosztó-függőség az Azure-erőforráscsoporthoz
- SAP multi-SID-forgatókönyvek támogatása

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Erőforráscsoportok
Az Azure Resource Manager az Azure-előfizetésében lévő összes alkalmazás-erőforrást az erőforráscsoportok használatával kezelheti. Egy adott erőforráscsoport integrált megközelítése minden erőforrásnak azonos életciklusa van. Például az összes erőforrás ugyanabban az időben jön létre, és egyszerre törlődik. További információk az [erőforráscsoportokról](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure-beli belső terheléselosztó-függőség az Azure-erőforráscsoporthoz

A klasszikus Azure üzembe helyezési modellben függőség van az Azure belső terheléselosztó (Azure Load Balancer szolgáltatás) és a Cloud Service-csoport között. Minden belső terheléselosztó egy felhőalapú szolgáltatási csoportra van szüksége.

Azure Resource Manager a Azure Load Balancer használatához nincs szükség Azure-erőforráscsoport használatára. A környezet egyszerűbb és rugalmasabb.

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP multi-SID-forgatókönyvek támogatása

Azure Resource Manager több SAP System Identifier (SID) ASCS/SCS-példányt is telepíthet egy fürtben. Több SID-példány is lehetséges, mivel az egyes Azure-beli belső terheléselosztó több IP-címet támogat.

A klasszikus Azure-telepítési modell használatához kövesse az SAP NetWeaver az Azure-ban című témakörben ismertetett eljárásokat [: az SAP ASCS/SCS-példányok fürtözése a Windows Server feladatátvételi fürtszolgáltatással az Azure-ban a SIOS DataKeeper használatával](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Javasoljuk, hogy az SAP-telepítések Azure Resource Manager üzemi modelljét használja. Számos előnyt kínál, amelyek nem érhetők el a klasszikus üzemi modellben. További információ az Azure [üzembe helyezési modelljeiről][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatás a magas rendelkezésre állású SAP ASCS/SCS-telepítés és az adatbázis-kezelő rendszer alapja a Windows rendszerben.

A feladatátvevő fürt 1 + n független kiszolgálók (csomópontok) csoportja, amelyek együttműködve fokozzák az alkalmazások és szolgáltatások rendelkezésre állását. Csomópont meghibásodása esetén a Windows Server feladatátvételi fürtszolgáltatás kiszámítja a hibák számát, amely egy kifogástalan állapotú fürtnek az alkalmazások és szolgáltatások biztosítására való fenntartása közben fordulhat elő. A feladatátvételi fürtszolgáltatás eléréséhez különböző kvórum módok közül választhat.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Kvórum üzemmódok
A Windows Server feladatátvételi fürtszolgáltatás használatakor négy kvórum mód közül választhat:

* A **csomópontok többsége**. A fürt minden csomópontja szavazhat. A fürt csak a szavazatok többségével működik, azaz a szavazatok több mint fele. Ez a beállítás olyan fürtök esetében ajánlott, amelyek egyenlőtlen számú csomóponttal rendelkeznek. Például egy hét csomópontos fürt három csomópontja meghiúsulhat, és a fürt továbbra is a többséget futtatja, és folytatja a futtatást.  
* **A csomópont és a lemez többsége**. Minden csomópont és egy kijelölt lemez (a tanúsító lemez) a fürt tárterületén szavazhat, ha elérhetők és a kommunikációban is. A fürt csak a szavazatok többségével működik, azaz a szavazatok több mint fele. Ez a mód egy fürtözött környezetben is működik, ahol páros számú csomópont van. Ha a csomópontok fele és a lemez online állapotban van, a fürt állapota Kifogástalan marad.
* **A csomópont-és a fájlmegosztás többsége**. Minden csomópont és egy kijelölt fájlmegosztás (egy tanúsító fájlmegosztás), amelyet a rendszergazda hoz létre, szavazhat, függetlenül attól, hogy elérhetők-e a csomópontok és a fájlmegosztás. A fürt csak a szavazatok többségével működik, azaz a szavazatok több mint fele. Ez a mód egy fürtözött környezetben is működik, ahol páros számú csomópont van. Ez a csomópont és a lemez többségi módjához hasonló, de tanúsító lemez helyett egy tanúsító fájlmegosztást használ. Ez a mód könnyen megvalósítható, de ha maga a fájlmegosztás nem túl hosszú, akkor előfordulhat, hogy egy meghibásodási pont lehet.
* **Nincs többség: csak lemez**. A fürt kvórumot tartalmaz, ha az egyik csomópont elérhető, és a fürt egy adott lemezével kommunikál. Csak azok a csomópontok csatlakozhatnak a fürthöz, amelyek a lemezzel is kommunikálnak. Azt javasoljuk, hogy ne használja ezt a módot.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Helyszíni Windows Server feladatátvételi fürtszolgáltatás
Az 1. ábra két csomópontból álló fürtöt mutat be. Ha a csomópontok közötti hálózati kapcsolat meghiúsul, és mindkét csomópont megmarad és fut, a kvórum lemez vagy fájlmegosztás határozza meg, hogy melyik csomópont fogja továbbra is biztosítani a fürt alkalmazásait és szolgáltatásait. A kvórum lemezének vagy fájlmegosztás hozzáférését biztosító csomópont a csomópont, amely biztosítja a szolgáltatások folytatását.

Mivel ez a példa egy két csomópontos fürtöt használ, a csomópont és a fájlmegosztás többsége kvórum üzemmódot használjuk. A csomópont és a lemez többsége is érvényes beállítás. Éles környezetben javasoljuk, hogy használjon kvórum lemezt. A hálózati és tárolási rendszertechnológia segítségével elérhetővé teheti azt.

![1. ábra: példa a Windows Server feladatátvételi Fürtszolgáltatására az Azure-beli SAP ASCS/SCS-konfigurációhoz][sap-ha-guide-figure-1000]

_**1. ábra:** Windows Server feladatátvételi fürtszolgáltatási konfiguráció – példa az Azure-beli SAP ASCS/SCS-re_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Megosztott tároló
Az 1. ábrán egy két csomópontos megosztott tároló fürt is látható. Egy helyszíni megosztott tároló fürtben a fürt összes csomópontja felismeri a megosztott tárolót. A zárolási mechanizmus védi az adatok sérülését. Az összes csomópont képes észlelni, ha egy másik csomópont meghibásodik. Ha egy csomópont meghibásodik, a fennmaradó csomópont a tárolási erőforrások tulajdonjogát veszi figyelembe, és biztosítja a szolgáltatások rendelkezésre állását.

> [!NOTE]
> A magas rendelkezésre álláshoz nincs szükség megosztott lemezekre, néhány adatbázis-kezelő alkalmazással, mint például a SQL Server. SQL Server always on replikálja az adatbázis-kezelői adatok és naplófájlok egyik fürtcsomópont helyi lemezéről egy másik fürtcsomópont helyi lemezére. Ebben az esetben a Windows-fürt konfigurációjának nincs szüksége megosztott lemezre.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Hálózatkezelés és névfeloldás
Az ügyfélszámítógépek a virtuális IP-címen és a DNS-kiszolgáló által biztosított virtuális állomásnévn keresztül érik el a fürtöt. A helyszíni csomópontok és a DNS-kiszolgáló több IP-címet is képes kezelni.

Egy tipikus beállításban két vagy több hálózati kapcsolatot használ:

* Dedikált kapcsolódás a tárolóhoz
* Fürt – belső hálózati kapcsolatok a szívveréshez
* Az ügyfelek által a fürthöz való kapcsolódáshoz használt nyilvános hálózat

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server feladatátvételi fürtszolgáltatás az Azure-ban
Az operációs rendszer nélküli vagy a saját felhőalapú üzemelő példányokhoz képest az Azure Virtual Machines további lépéseket igényel a Windows Server feladatátvételi fürtszolgáltatás konfigurálásához. Megosztott fürtözött lemez létrehozásakor több IP-címet és virtuális állomásnevet kell beállítania az SAP ASCS/SCS példányhoz.

Ebben a cikkben a főbb fogalmakat és a SAP magas rendelkezésre állású központi szolgáltatások Azure-beli létrehozásához szükséges további lépéseket tárgyaljuk. Bemutatjuk, hogyan állíthatja be a külső gyártótól származó eszközt a SIOS DataKeeper, és hogyan konfigurálhatja az Azure belső Load balancert. Ezekkel az eszközökkel létrehozhat egy, az Azure-ban tanúsító fájlmegosztás használatával létrehozott Windows feladatátvevő fürtöt.

![2. ábra: Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**2. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban megosztott lemez nélkül_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Megosztott lemez az Azure-ban a SIOS DataKeeper
A magas rendelkezésre állású SAP ASCS/SCS-példányhoz fürt megosztott tárterületre van szükség. Szeptember 2016-én az Azure nem kínál megosztott tárolót, amellyel megosztott tárolókat hozhat létre. A külső gyártóktól származó szoftverek SIOS DataKeeper cluster Edition használatával létrehozhat egy tükrözött tárolót, amely szimulálja a fürt megosztott tárolóját. A SIOS megoldás valós idejű szinkron adatreplikálást biztosít. Így hozhat létre megosztott lemezes erőforrást egy fürthöz:

1. Csatoljon egy további Azure-beli virtuális merevlemezt (VHD-t) minden egyes virtuális géphez (VM) a Windows-fürt konfigurációjában.
2. Futtassa a SIOS DataKeeper cluster Editiont mindkét virtuálisgép-csomóponton.
3. Konfigurálja a SIOS DataKeeper-fürt kiadását úgy, hogy az a forrás virtuális gépről származó további VHD-kötet tartalmát átadja a célként megadott virtuális gép további VHD-re erősített kötetének. A SIOS DataKeeper elküldi a forrás-és a célként megadott helyi köteteket, majd a Windows Server feladatátvételi fürtszolgáltatást egy megosztott lemezként jeleníti meg.

További információ a [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![3. ábra: Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban a SIOS DataKeeper][sap-ha-guide-figure-1002]

_**3. ábra:** Windows Server feladatátvételi fürtszolgáltatás konfigurálása az Azure-ban a SIOS DataKeeper_

> [!NOTE]
> A magas rendelkezésre álláshoz nincs szükség megosztott lemezekre, néhány adatbázis-kezelő termékkel, például a SQL Serversal. SQL Server always on replikálja az adatbázis-kezelői adatok és naplófájlok egyik fürtcsomópont helyi lemezéről egy másik fürtcsomópont helyi lemezére. Ebben az esetben a Windows-fürt konfigurációjának nincs szüksége megosztott lemezre.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Névfeloldás az Azure-ban
Az Azure Cloud platform nem nyújt lehetőséget a virtuális IP-címek, például a lebegő IP-címek konfigurálására. Egy alternatív megoldásra van szükség egy virtuális IP-cím beállításához, hogy elérje a fürt erőforrását a felhőben.
Az Azure-ban belső terheléselosztó található a Azure Load Balancer szolgáltatásban. A belső terheléselosztó révén az ügyfelek a fürt virtuális IP-címén keresztül érik el a fürtöt.
Telepítenie kell a belső terheléselosztót a fürtcsomópontokon tartalmazó erőforráscsoporthoz. Ezután konfigurálja az összes szükséges port továbbítási szabályt a belső terheléselosztó mintavételi portjaival.
Az ügyfelek csatlakozhatnak a virtuális gazdagép nevével. A DNS-kiszolgáló feloldja a fürt IP-címét, és a belső terheléselosztó kezeli a port továbbítását a fürt aktív csomópontjára.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver magas rendelkezésre állás az Azure infrastruktúra szolgáltatásában (IaaS)
Az SAP-alkalmazások magas rendelkezésre állásának, például az SAP-szoftverek összetevőinek eléréséhez a következő összetevőket kell védelemmel ellátnia:

* SAP Application Server-példány
* SAP ASCS/SCS-példány
* Adatbázis-kezelő kiszolgáló

További információ az SAP-összetevők magas rendelkezésre állású helyzetekben való védelméről: [Azure Virtual Machines tervezése és implementálása az SAP NetWeaver](planning-guide.md)-ben.

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Magas rendelkezésre állású SAP Application Server
Általában nincs szükség speciális, magas rendelkezésre állású megoldásra az SAP-alkalmazáskiszolgáló és a párbeszédpanel-példányok esetében. A redundancia révén magas rendelkezésre állást érhet el, és az Azure Virtual Machines különböző példányain több párbeszédpanel-példányt is konfigurálhat. Legalább két, az Azure Virtual Machines két példányán telepített SAP-alkalmazás példányának kell lennie.

![4. ábra: magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**4. ábra:** Magas rendelkezésre állású SAP Application Server_

Az SAP Application Server-példányokat futtató összes virtuális gépet ugyanabba az Azure-beli rendelkezésre állási csoportba kell helyeznie. Az Azure rendelkezésre állási készlete biztosítja a következőket:

* Minden virtuális gép ugyanahhoz a frissítési tartományhoz tartozik. Egy frissítési tartomány például gondoskodik arról, hogy a virtuális gépek ne frissüljenek egyszerre a tervezett karbantartási állásidő során.
* Minden virtuális gép ugyanahhoz a tartalék tartományhoz tartozik. A tartalék tartomány például gondoskodik arról, hogy a virtuális gépek telepítve legyenek, így egyetlen meghibásodási pont sem befolyásolja az összes virtuális gép rendelkezésre állását.

További információ a [virtuális gépek rendelkezésre állásának kezeléséről][virtual-machines-manage-availability].

Mivel az Azure Storage-fiók egy lehetséges meghibásodási pont, fontos, hogy legalább két Azure Storage-fiókkal rendelkezzen, amelyekben legalább két virtuális gép terjeszthető. Egy ideális beállítás esetén az SAP-példányt futtató virtuális gépek lemezei egy másik Storage-fiókban lesznek telepítve.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Magas rendelkezésre állású SAP ASCS/SCS-példány
Az 5. ábra egy példa egy magas rendelkezésre állású SAP ASCS/SCS-példányra.

![5. ábra: magas rendelkezésre állású SAP ASCS/SCS-példány][sap-ha-guide-figure-2001]

_**5. ábra:** Magas rendelkezésre állású SAP ASCS/SCS-példány_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS-példány magas rendelkezésre állása a Windows Server feladatátvételi fürtszolgáltatással az Azure-ban
Az operációs rendszer nélküli vagy a saját felhőalapú üzemelő példányokhoz képest az Azure Virtual Machines további lépéseket igényel a Windows Server feladatátvételi fürtszolgáltatás konfigurálásához. Windows feladatátvevő fürt létrehozásához szükség van egy megosztott fürtözött lemezre, több IP-címre, több virtuális állomásnévre és egy Azure belső terheléselosztó használatára, amely egy SAP ASCS/SCS-példány fürtözéséhez szükséges. Ezt részletesebben is megbeszéljük a cikk későbbi részében.

![6. ábra: Windows Server feladatátvételi fürtszolgáltatás az Azure-beli SAP ASCS/SCS-konfigurációhoz az SIOS DataKeeper használatával][sap-ha-guide-figure-1002]

_**6. ábra:** Windows Server feladatátvételi fürtszolgáltatás az Azure-beli SAP ASCS/SCS-konfigurációhoz az Azure-ban a SIOS DataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Magas rendelkezésre állású adatbázis-kezelő példány
Az adatbázis-kezelő az SAP-rendszer egyetlen kapcsolódási pontja is. Magas rendelkezésre állású megoldás használatával kell védelemmel ellátnia. A 7. ábrán egy SQL Server always on magas rendelkezésre állású megoldás látható az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatással és az Azure belső Load balancerrel. SQL Server always on replikálja az adatbázis-kezelői és naplófájlokat a saját adatbázis-replikációs szolgáltatásának használatával. Ebben az esetben nincs szükség fürt megosztott lemezekre, ami leegyszerűsíti a teljes telepítést.

![7. ábra: példa a magas rendelkezésre állású SAP adatbázis-kezelőre, SQL Server always on][sap-ha-guide-figure-2003]

_**7. ábra:** Példa magas rendelkezésre állású SAP adatbázis-kezelőre, SQL Server always on_

További információ az Azure-beli fürtözési SQL Server az Azure Resource Manager üzembe helyezési modell használatával:

* [Az Always On rendelkezésre állási csoport konfigurálása az Azure Virtual Machines manuálisan a Resource Manager használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Azure belső terheléselosztó konfigurálása always on rendelkezésre állási csoport számára az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Végpontok közötti magas rendelkezésre állású központi telepítési forgatókönyvek

### <a name="deployment-scenario-using-architectural-template-1"></a>Üzembe helyezési forgatókönyv az 1. építészeti sablon használatával

A 8. ábrán egy példa látható egy SAP NetWeaver magas rendelkezésre állású architektúrára az Azure-ban **egy** SAP-rendszer számára. Ez a forgatókönyv a következőképpen van beállítva:

- Az SAP ASCS/SCS példányhoz egy dedikált fürtöt használunk.
- Az adatbázis-kezelő példányhoz egy dedikált fürtöt használunk.
- Az SAP Application Server-példányok saját dedikált virtuális gépeken vannak telepítve.

![8. ábra: SAP magas rendelkezésre állású építészeti sablon 1, dedikált fürttel a ASCS/SCS és az adatbázis-kezelő rendszerhez][sap-ha-guide-figure-2004]

_**8. ábra:** SAP magas rendelkezésre állású építészeti sablon 1, dedikált fürtök a ASCS/SCS és az adatbázis-kezelő rendszerekhez_

### <a name="deployment-scenario-using-architectural-template-2"></a>Üzembe helyezési forgatókönyv a 2. építészeti sablon használatával

A 9. ábra egy SAP NetWeaver magas rendelkezésre állású architektúrát mutat be az Azure-ban **egy** SAP-rendszer számára. Ez a forgatókönyv a következőképpen van beállítva:

- **Az SAP** ASCS/SCS-példányhoz és az adatbázis-kezelő rendszerhez egy dedikált fürtöt használunk.
- Az SAP Application Server-példányok saját dedikált virtuális gépeken vannak üzembe helyezve.

![9. ábra: SAP magas rendelkezésre állású építészeti sablon 2, dedikált fürttel a ASCS/SCS számára, valamint egy dedikált fürt az adatbázis-kezelő szolgáltatáshoz][sap-ha-guide-figure-2005]

_**9. ábra:** SAP magas rendelkezésre állású építészeti sablon 2, dedikált fürttel a ASCS/SCS számára, valamint egy dedikált fürt az adatbázis-kezelő szolgáltatáshoz_

### <a name="deployment-scenario-using-architectural-template-3"></a>Üzembe helyezési forgatókönyv 3. építészeti sablon használatával

A 10. ábrán egy példa látható egy SAP NetWeaver magas rendelkezésre állású architektúrára az Azure-ban **két** SAP-rendszer számára, a &lt; SID1 &gt; és a &lt; SID2 &gt; . Ez a forgatókönyv a következőképpen van beállítva:

- A rendszer egy dedikált fürtöt **használ az SAP** ASCS/SCS SID1-példányhoz *és* az SAP ASCS/SCS SID2-példányhoz (egy fürthöz).
- A rendszer egy dedikált fürtöt használ az adatbázis-kezelő SID1, és egy másik dedikált fürtöt használ az adatbázis-kezelői SID2 (két fürt).
- Az SAP-rendszer SID1 tartozó SAP Application Server-példányokhoz saját dedikált virtuális gépek tartoznak.
- Az SAP-rendszer SID2 tartozó SAP Application Server-példányokhoz saját dedikált virtuális gépek tartoznak.

![10. ábra: SAP magas rendelkezésre állású építészeti sablon 3, dedikált fürttel különböző ASCS/SCS-példányokhoz][sap-ha-guide-figure-6003]

_**10. ábra:** SAP magas rendelkezésre állású építészeti sablon 3, dedikált fürttel különböző ASCS/SCS-példányokhoz_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Az infrastruktúra előkészítése

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az építészeti sablonhoz 1
Az SAP-hez készült Azure Resource Manager-sablonok megkönnyítik a szükséges erőforrások üzembe helyezését.

A Azure Resource Manager háromrétegű sablonjai a magas rendelkezésre állású forgatókönyveket is támogatják, például az 1. építészeti sablonban, amely két fürttel rendelkezik. Mindegyik fürt egy SAP-meghibásodási pont az SAP ASCS/SCS és az adatbázis-kezelő számára.

Itt bemutathatja Azure Resource Manager sablonokat a jelen cikkben ismertetett példához:

* [Azure Marketplace-rendszerkép](https://github.com/Azure/azure-quickstart-templates/)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/)

Az infrastruktúra előkészítése az 1. építészeti sablonhoz:

- A Azure Portal a **Paraméterek** panel **SYSTEMAVAILABILITY** mezőjében válassza a **Ha** lehetőséget.

  ![11. ábra: az SAP magas rendelkezésre állású Azure Resource Manager paramétereinek beállítása][sap-ha-guide-figure-3000]

_**11. ábra:** SAP magas rendelkezésre állású Azure Resource Manager paramétereinek beállítása_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP Application Server Virtual Machines: <*SAPSystemSID*>-di-<*száma*>
    * ASCS/SCS-fürt virtuális gépei: <*SAPSystemSID*>-ASCS-<*száma*>
    * Adatbázis-kezelő fürt: <*SAPSystemSID*>-db-<*száma*>

  * **Hálózati kártyák az összes virtuális géphez, társított IP-címekkel**:
    * <*SAPSystemSID*>-NIC-di-<*szám*>
    * <*SAPSystemSID*>-NIC-ascs-<*szám*>
    * <*SAPSystemSID*>-NIC-db-<*szám*>

  * **Azure Storage-fiókok**

  * **Rendelkezésre állási csoportok** a következőhöz:
    * SAP Application Server virtuális gépek: <*SAPSystemSID*>-avset-di
    * SAP-ASCS/SCS-fürt virtuális gépei: <*SAPSystemSID*>-avset-ASCS
    * Adatbázis-kezelő fürt virtuális gépei: <*SAPSystemSID*>-avset-db

  * **Azure belső terheléselosztó**:
    * A ASCS/SCS-példány összes portja és az IP-cím <*SAPSystemSID*>-LB-ASCS
    * Az SQL Server adatbázis-kezelő és IP-cím összes portja <*SAPSystemSID*>-LB-db

  * **Hálózati biztonsági csoport**: <*SAPSystemSID*>-NSG-ASCs-0  
    * Nyitott külső RDP protokoll (RDP) porttal a <*SAPSystemSID*>-ASCs-0 virtuális géphez

> [!NOTE]
> Alapértelmezés szerint a hálózati kártyák és az Azure belső terheléselosztó összes IP-címe **dinamikus** . Módosítsa **statikus** IP-címekre. Ezt a cikket a cikk későbbi részében ismertetjük.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Virtuális gépek üzembe helyezése vállalati hálózati kapcsolattal (telephelyek közötti) az éles környezetben való használatra
Az éles környezetben futó SAP-rendszerek esetében az Azure-beli virtuális gépeket üzembe helyezheti vállalati hálózati kapcsolattal az Azure webhelyek közötti VPN vagy az Azure ExpressRoute használatával.

> [!NOTE]
> Használhatja az Azure Virtual Network-példányát. A virtuális hálózat és az alhálózat már létrehozva és előkészített állapotban van.
>
>

1. A Azure Portal a **Paraméterek** panel **NEWOREXISTINGSUBNET** mezőjében válassza a **meglévő** lehetőséget.
2. A **DEnetid** mezőben adja meg az előkészített Azure-hálózat teljes karakterláncát, ahol az Azure-beli virtuális gépek üzembe helyezését tervezi.
3. Az összes Azure-beli hálózati alhálózat listájának lekéréséhez futtassa a következő PowerShell-parancsot:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Az **azonosító** mező a **denetid**-t jeleníti meg.
4. A következő PowerShell-parancs futtatásával kérheti le, hogy a rendszer hogyan olvassa be **az összes** alalkalmazási értéket:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   A  következőhöz hasonlóan néz ki:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Csak felhőalapú SAP-példányok üzembe helyezése teszteléshez és bemutatóhoz
A magas rendelkezésre állású SAP-rendszer üzembe helyezése csak felhőalapú üzemi modellben végezhető el. Az ilyen típusú központi telepítés elsősorban bemutató és tesztelési célú esetekben hasznos. Éles használati esetekhez nem alkalmas.

- A Azure Portal a **Paraméterek** panel **NEWOREXISTINGSUBNET** mezőjében válassza az **új** lehetőséget. Hagyja üresen a **deneti** mezőt.

  Az SAP Azure Resource Manager sablon automatikusan létrehozza az Azure-beli virtuális hálózatot és alhálózatot.

> [!NOTE]
> A Active Directory és a DNS szolgáltatáshoz is telepítenie kell legalább egy dedikált virtuális gépet ugyanabban az Azure Virtual Network-példányban. A sablon nem hozza létre ezeket a virtuális gépeket.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Az infrastruktúra előkészítése a 2. építészeti sablonhoz

Az SAP-hez készült Azure Resource Manager-sablon segítségével egyszerűbbé teheti a szükséges infrastruktúra-erőforrások üzembe helyezését az SAP architektúra 2. sablonjában.

A következő helyen kaphat Azure Resource Manager sablonokat ehhez a telepítési forgatókönyvhöz:

* [Azure Marketplace-rendszerkép](https://github.com/Azure/azure-quickstart-templates/)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Az infrastruktúra előkészítése a 3. építészeti sablonhoz

Előkészítheti az infrastruktúrát, és konfigurálhatja az SAP-t a **többszörös SID-** hez. Hozzáadhat például egy további SAP ASCS/SCS-példányt egy *meglévő* fürtkonfiguráció-konfigurációhoz. További információkért lásd: [további SAP ASCS/SCS-példány konfigurálása meglévő fürtkonfiguráció a Azure Resource Manager SAP multi-SID konfigurációjának létrehozásához][sap-ha-multi-sid-guide].

Ha új, több SID-alapú fürtöt szeretne létrehozni, használhatja a több SID-alapú [Gyorsindítás-sablonokat a githubon](https://github.com/Azure/azure-quickstart-templates).
Új, több SID-fürt létrehozásához a következő három sablont kell telepítenie:

* [ASCS/SCS-sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Application Server-sablon](#application-servers-template)

A következő szakaszokban további részleteket talál a sablonokkal és a sablonokban megadható paraméterekkel kapcsolatban.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> ASCS/SCS-sablon

A ASCS/SCS-sablon két virtuális gépet telepít, amelyek segítségével több ASCS/SCS-példányt üzemeltető Windows Server feladatátvevő fürt hozható létre.

A ASCS/SCS multi-SID sablon beállításához a [ASCS/SCS multi-SID sablonban][sap-templates-3-tier-multisid-xscs-marketplace-image]adja meg a következő paraméterek értékeit:

  - **Erőforrás-előtag**.  Állítsa be az erőforrás-előtagot, amely az üzembe helyezés során létrehozott összes erőforrás előállítására szolgál. Mivel az erőforrások nem csak egy SAP-rendszerhez tartoznak, az erőforrás előtagja nem egy SAP-rendszer SID-azonosítója.  Az előtagnak **három és hat karakter** közöttinek kell lennie.
  - **Verem típusa** Válassza ki az SAP-rendszerek halmozási típusát. A verem típusától függően Azure Load Balancer rendelkezik egy (ABAP vagy Java only) vagy két (ABAP + Java) magánhálózati IP-címmel SAP-rendszeren.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerét.
  -  Az **SAP-rendszerek száma**. Válassza ki a fürtben telepíteni kívánt SAP-rendszerek számát.
  -  A **rendszerek rendelkezésre állása**. Válassza a **Ha** lehetőséget.
  -  **Rendszergazdai Felhasználónév és rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely használható a gépre való bejelentkezéshez.
  -  **Új vagy meglévő alhálózat**. Állítsa be, hogy új virtuális hálózatot és alhálózatot kell-e létrehozni, vagy egy meglévő alhálózatot kell használni. Ha már van olyan virtuális hálózata, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő** lehetőséget.
  -  **Alhálózat-azonosító**. Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában a következőképpen néz ki:/Subscriptions/ *<előfizetés-azonosító*>/resourceGroups/<*erőforráscsoport neve*>/Providers/Microsoft.Network/virtualNetworks/<*virtuális hálózat neve*>/Subnets/<*alhálózat neve*>

A sablon egy Azure Load Balancer példányt telepít, amely több SAP-rendszert is támogat.

- A ASCS-példányok a 00, 10, 20 példányra vannak konfigurálva...
- Az SCS-példányok a következő példányhoz vannak konfigurálva: 01, 11, 21...
- A ASCS sorba helyezni replikációs kiszolgáló (ERS) (csak Linux) példányok a 02, 12, 22... számú példányra vannak konfigurálva...
- Az SCS ERS (csak Linux) példányok konfigurálva vannak a következő példányhoz: 03, 13, 23...

A terheléselosztó 1 (2 Linux) virtuális IP-címet, 1x VIP-t (ASCS/SCS és 1x VIP for ERS) tartalmaz (csak Linux).

Az alábbi lista tartalmazza az összes terheléselosztási szabályt (ahol az x az SAP-rendszer száma, például 1, 2, 3...):
- Windows-specifikus portok minden SAP-rendszerhez: 445, 5985
- ASCS-portok (példányok száma X0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portok (X1-példány száma): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS-portok Linuxon (példány száma x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-portok Linux rendszeren (3. példány száma): 33x3, 5x313, 5x314, 5x316

A terheléselosztó a következő mintavételi portok használatára van konfigurálva (ahol az x az SAP-rendszer száma, például 1, 2, 3...):
- ASCS/SCS belső terheléselosztó mintavételi portja: 620x0
- ERS belső terheléselosztó mintavételi portja (csak Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Adatbázis-sablon

Az adatbázis-sablon egy vagy két virtuális gépet telepít, amelyekkel egy SAP-rendszerhez a (z) RDBMS-kezelő rendszer telepíthető. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor öt alkalommal kell telepítenie ezt a sablont.

Az adatbázis multi-SID sablonjának beállításához az [adatbázis multi-SID sablonjában][sap-templates-3-tier-multisid-db-marketplace-image]adja meg az alábbi paraméterek értékeit:

- **SAP-rendszerazonosító**. Adja meg a telepíteni kívánt SAP-System AZONOSÍTÓját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használva.
- **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerét.
- **DbType**. Válassza ki a fürtön telepíteni kívánt adatbázis típusát. Válassza az **SQL** lehetőséget, ha Microsoft SQL Server szeretné telepíteni. Válassza a **HANA** lehetőséget, ha SAP HANA telepítését tervezi a virtuális gépeken. Ügyeljen arra, hogy a megfelelő operációsrendszer-típust válassza: válassza a **Windows** for SQL lehetőséget, és válasszon egy Linux-disztribúciót a HANA számára. A virtuális gépekhez csatlakozó Azure Load Balancer a kiválasztott adatbázis-típus támogatásához lesz konfigurálva:
  * **SQL**. A terheléselosztó a 1433-es portot fogja betölteni. Ügyeljen arra, hogy ezt a portot használja a SQL Server mindig a telepítőn.
  * **HANA**. A terheléselosztó a 35015-es és a 35017-es portot fogja betölteni. Győződjön meg arról, hogy a **50**-as számú példánnyal telepíti a SAP HANA.
  A terheléselosztó a 62550-es mintavételi portot fogja használni.
- Az **SAP-rendszerméret**. Állítsa be, hogy az új rendszer hány SAP-t adjon meg. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
- A **rendszerek rendelkezésre állása**. Válassza a **Ha** lehetőséget.
- **Rendszergazdai Felhasználónév és rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely használható a gépre való bejelentkezéshez.
- **Alhálózat-azonosító**. Adja meg annak az alhálózatnak az AZONOSÍTÓját, amelyet a ASCS/SCS sablon telepítése során használt, vagy a ASCS/SCS-sablon üzembe helyezésének részeként létrehozott alhálózat AZONOSÍTÓját.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Application Server-sablon

Az alkalmazáskiszolgáló sablon két vagy több virtuális gépet telepít, amelyek SAP Application Server-példányként használhatók egy SAP-rendszerhez. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor öt alkalommal kell telepítenie ezt a sablont.

Az alkalmazáskiszolgáló több SID-sablonjának beállításához adja meg a következő paraméterek értékeit az [Application Servers multi-SID sablonban][sap-templates-3-tier-multisid-apps-marketplace-image]:

  -  **SAP-rendszerazonosító**. Adja meg a telepíteni kívánt SAP-System AZONOSÍTÓját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használva.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerét.
  -  Az **SAP-rendszerméret**. Az új rendszer által biztosított SAP-t. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
  -  A **rendszerek rendelkezésre állása**. Válassza a **Ha** lehetőséget.
  -  **Rendszergazdai Felhasználónév és rendszergazdai jelszó**. Hozzon létre egy új felhasználót, amely használható a gépre való bejelentkezéshez.
  -  **Alhálózat-azonosító**. Adja meg annak az alhálózatnak az AZONOSÍTÓját, amelyet a ASCS/SCS sablon telepítése során használt, vagy a ASCS/SCS-sablon üzembe helyezésének részeként létrehozott alhálózat AZONOSÍTÓját.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure-beli virtuális hálózat
Példánkban az Azure-beli virtuális hálózat 10.0.0.0/16. Az **alhálózat** nevű alhálózat egy 10.0.0.0/24 címtartomány. Az összes virtuális gép és belső terheléselosztó üzembe helyezése ebben a virtuális hálózaton történik.

> [!IMPORTANT]
> Ne módosítsa a hálózati beállításokat a vendég operációs rendszeren belül. Ide tartoznak az IP-címek, a DNS-kiszolgálók és az alhálózatok. Konfigurálja az összes hálózati beállítást az Azure-ban. A Dynamic Host Configuration Protocol (DHCP) szolgáltatás propagálja a beállításokat.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP-címek

A szükséges DNS IP-címek megadásához hajtsa végre a következő lépéseket.

1. A Azure Portal **DNS-kiszolgálók** paneljén győződjön meg arról, hogy a virtuális hálózati **DNS-kiszolgálók** beállítás értéke **Egyéni DNS**.
2. Válassza ki a beállításokat a hálózat típusa alapján. További információkat találhat az alábbi forrásokban:
   * Adja hozzá a helyszíni DNS-kiszolgálók IP-címeit.  
   A helyszíni DNS-kiszolgálókat az Azure-ban futó virtuális gépekre is kiterjesztheti. Ebben az esetben felveheti azon Azure-beli virtuális gépek IP-címeit, amelyeken a DNS szolgáltatást futtatja.
   * Az Azure-ban elkülönített központi telepítések esetén: helyezzen üzembe egy további virtuális gépet ugyanabban a Virtual Network-példányban, amely DNS-kiszolgálóként szolgál. Adja hozzá a DNS-szolgáltatás futtatásához beállított Azure-beli virtuális gépek IP-címeit.

   ![12. ábra: az Azure-hoz készült DNS-kiszolgálók konfigurálása Virtual Network][sap-ha-guide-figure-3001]

   _**12. ábra:** DNS-kiszolgálók konfigurálása az Azure Virtual Network_

   > [!NOTE]
   > Ha megváltoztatja a DNS-kiszolgálók IP-címeit, újra kell indítania az Azure-beli virtuális gépeket a módosítás alkalmazásához és az új DNS-kiszolgálók propagálásához.
   >
   >

A példánkban a DNS szolgáltatás telepítve van és konfigurálva van ezeken a Windows-alapú virtuális gépeken:

| Virtuális gépi szerepkör | Virtuális gép gazdagépének neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr – 0 |PR1-NIC-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr – 1 |PR1-NIC-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Az SAP ASCS/SCS fürtözött példány és az adatbázis-kezelő fürtözött példány állomásneve és statikus IP-címei

Helyszíni központi telepítés esetén ezekre a fenntartott állomásnévekre és IP-címekre van szükség:

| Virtuális gazdagép neve szerepkör | Virtuális gazdagép neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS első fürt virtuális gazdagépének neve (fürtözési felügyelethez) |PR1-ASCs-VIR |10.0.0.42 |
| SAP-ASCS/SCS-példány virtuális gazdagépének neve |PR1-ASCs-SAP |10.0.0.43 |
| SAP adatbázis-kezelő második fürt virtuális gazdagépének neve (fürtszolgáltatás) |PR1 – adatbázis-kezelő – VIR |10.0.0.32 |

A fürt létrehozásakor hozza létre a **PR1-ASCs-VIR** és a **PR1-adatbázis-VIR** nevű virtuális gazdagép nevét, valamint a FÜRTÖt kezelő társított IP-címeket. Ennek módjával kapcsolatos további információkért lásd: fürtcsomópontok [gyűjtése a fürt konfigurációjában][sap-ha-guide-8.12.1].

A DNS-kiszolgálón manuálisan is létrehozhatja a másik két virtuális gazdagép nevét, a **PR1-ASCs-SAP** és a **PR1-adatbázis-SAP protokollt**, valamint a hozzájuk tartozó IP-címeket. A fürtözött SAP ASCS/SCS-példány és a fürtözött adatbázis-kezelő példány ezeket az erőforrásokat használja. Ennek módjáról a következő témakörben talál további információt: [virtuális állomásnév létrehozása FÜRTÖZÖTT SAP ASCS/SCS-példányhoz][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Statikus IP-címek beállítása az SAP-beli virtuális gépekhez
Miután telepítette a virtuális gépeket a fürtben való használatra, statikus IP-címeket kell megadnia az összes virtuális géphez. Ezt megteheti az Azure Virtual Network konfigurációjában, nem pedig a vendég operációs rendszeren.

1. A Azure Portal válassza az **erőforráscsoport**  >  **hálózati kártya**  >  **beállításai**  >  **IP-cím** elemet.
2. Az **IP-címek** panel **hozzárendelés** területén válassza a **statikus** lehetőséget. Az **IP-cím** mezőbe írja be a használni kívánt IP-címet.

   > [!NOTE]
   > Ha módosítja a hálózati kártya IP-címét, a módosítás alkalmazásához újra kell indítania az Azure-beli virtuális gépeket.  
   >
   >

   ![13. ábra: az egyes virtuális gépek hálózati kártyájának statikus IP-címeinek beállítása][sap-ha-guide-figure-3002]

   _**13. ábra:** Statikus IP-címek beállítása az egyes virtuális gépek hálózati kártyája számára_

   Ismételje meg ezt a lépést minden hálózati adapter esetében, azaz minden virtuális gép esetében, beleértve a Active Directory/DNS szolgáltatáshoz használni kívánt virtuális gépeket is.

A példánkban a következő virtuális gépek és statikus IP-címek vannak:

| Virtuális gépi szerepkör | Virtuális gép gazdagépének neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP Application Server-példány |PR1-di-0 |PR1-NIC-di-0 |10.0.0.50 |
| Második SAP Application Server-példány |PR1-di-1 |PR1-NIC-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP Application Server-példány |PR1-di-5 |PR1-NIC-di-5 |10.0.0.55 |
| Első fürtcsomópont a ASCS/SCS-példányhoz |PR1-ASCs-0 |PR1-NIC-ASCs-0 |10.0.0.40 |
| Második fürtcsomópont a ASCS/SCS-példányhoz |PR1-ASCs-1 |PR1-NIC-ASCs-1 |10.0.0.41 |
| Első fürtcsomópont az adatbázis-kezelő példányhoz |PR1-db-0 |PR1-NIC-db-0 |10.0.0.30 |
| Második fürtcsomópont az adatbázis-kezelő példányhoz |PR1-db-1 |PR1-NIC-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Statikus IP-cím beállítása az Azure belső terheléselosztó számára

Az SAP Azure Resource Manager sablon egy belső Azure Load balancert hoz létre, amely az SAP ASCS/SCS instance-fürthöz és az adatbázis-kezelő fürthöz használható.

> [!IMPORTANT]
> Az SAP ASCS/SCS virtuális állomásneve IP-címe megegyezik az SAP ASCS/SCS belső terheléselosztó IP-címével: **PR1-LB-ASCS**.
> Az adatbázis-kezelőrendszer virtuális nevének IP-címe megegyezik az adatbázis-kezelő belső terheléselosztó IP-címével: **PR1-LB-adatbázis-kezelő**.
>
>

Statikus IP-cím beállítása az Azure belső terheléselosztó számára:

1. A kezdeti telepítés a belső terheléselosztó IP-címét a **dinamikus** értékre állítja. A Azure Portal **IP-címek** paneljének **hozzárendelés** területén válassza a **statikus** lehetőséget.
2. Állítsa be a belső terheléselosztó **PR1-LB-ASCS** IP-címét az SAP ASCS/SCS-példány virtuális állomásneve IP-címére.
3. Állítsa be a belső terheléselosztó **PR1-LB-adatbázis IP-** címét az adatbázis-kezelő példány virtuális állomásneve IP-címére.

   ![14. ábra: statikus IP-címek beállítása a belső terheléselosztó számára az SAP ASCS/SCS-példányhoz][sap-ha-guide-figure-3003]

   _**14. ábra:** Statikus IP-címek beállítása a belső terheléselosztó számára az SAP ASCS/SCS-példányhoz_

A példánkban két Azure belső terheléselosztó van, amelyek rendelkeznek ezekkel a statikus IP-címekkel:

| Azure belső terheléselosztó szerepkör | Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS-példány belső terheléselosztó |PR1-LB – ASCs |10.0.0.43 |
| SAP adatbázis-kezelő belső terheléselosztó |PR1-LB – adatbázis-kezelő |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztó számára

Az SAP Azure Resource Manager sablon létrehozza a szükséges portokat:
* ABAP ASCS-példány, az alapértelmezett **00** -as számú példánnyal
* Egy Java SCS-példány, amely az alapértelmezett **1** . számú példánnyal rendelkezik

Az SAP-ASCS/SCS-példány telepítésekor a következő alapértelmezett példányt kell használnia a ABAP ASCS-példányához, valamint a Java SCS-példányhoz tartozó alapértelmezett **01** - **es számú** példányt.

Ezután hozza létre a szükséges belső terheléselosztási végpontokat az SAP NetWeaver-portok számára.

A szükséges belső terheléselosztási végpontok létrehozásához először hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver ABAP ASCS-portok számára:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Beton portok (a 00-as példánnyal rendelkező ASCS-példány esetében) (10-ESEK) |
| --- | --- | --- |
| Sorba helyezni-kiszolgáló/ *lbrule3200* |32<*Példányszám*> |3200 |
| ABAP üzenetkezelő kiszolgáló/ *lbrule3600* |36<*Példányszám*> |3600 |
| Belső ABAP üzenet/ *lbrule3900* |39<*Példányszám*> |3900 |
| Message Server HTTP/ *Lbrule8100* |81<*Példányszám*> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5<*példányszám*>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5<*példányszám*>14 |50014 |
| Sorba helyezni-replikáció/ *Lbrule50016* |5<*példányszám*>16 |50016 |
| SAP Start Service ERS HTTP- *Lbrule51013* |5<*példányszám*>13 |51013 |
| SAP Start Service ERS HTTP- *Lbrule51014* |5<*példányszám*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**1. táblázat:** Az SAP NetWeaver ABAP ASCS-példányok portszámai_

Ezután hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver Java SCS-portok számára:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Beton portok (a 01-es példánnyal rendelkező SCS-példány esetében) (11-ESEK) |
| --- | --- | --- |
| Sorba helyezni-kiszolgáló/ *lbrule3201* |32<*Példányszám*> |3201 |
| Átjárókiszolgáló/ *lbrule3301* |33<*Példányszám*> |3301 |
| Java-üzenet kiszolgálója/ *lbrule3900* |39<*Példányszám*> |3901 |
| Message Server HTTP/ *Lbrule8101* |81<*Példányszám*> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5<*példányszám*>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5<*példányszám*>14 |50114 |
| Sorba helyezni-replikáció/ *Lbrule50116* |5<*példányszám*>16 |50116 |
| SAP Start Service ERS HTTP- *Lbrule51113* |5<*példányszám*>13 |51113 |
| SAP Start Service ERS HTTP- *Lbrule51114* |5<*példányszám*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**2. táblázat:** Az SAP NetWeaver Java SCS-példányok portszámai_

![15. ábra: az Azure belső terheléselosztó alapértelmezett ASCS/SCS terheléselosztási szabályai][sap-ha-guide-figure-3004]

_**15. ábra:** Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztó számára_

Állítsa be a terheléselosztó **PR1-LB-adatbázis IP-** címét az adatbázis-kezelő példány virtuális állomásneve IP-címére.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Az Azure belső terheléselosztó ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása

Ha az SAP-ASCS vagy az SCS-példányokhoz eltérő számokat szeretne használni, akkor a portok nevét és értékeit az alapértelmezett értékekkel kell módosítania.

1. A Azure Portal válassza a **< *SID*>-LB-ASCs terheléselosztó** terheléselosztási  >  **szabályokat**.
2. Az SAP-ASCS vagy az SCS-példányhoz tartozó összes terheléselosztási szabály esetében módosítsa a következő értékeket:

   * Name
   * Port
   * Háttérbeli port

   Ha például meg szeretné változtatni az alapértelmezett ASCS-példány számát 00 és 31 között, akkor az 1. táblázatban felsorolt összes porton módosítania kell a módosításokat.

   Az alábbi példa egy frissítést mutat be a port *lbrule3200*.

   ![16. ábra: az Azure belső terheléselosztó ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása][sap-ha-guide-figure-3005]

   _**16. ábra:** Az Azure belső terheléselosztó ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows rendszerű virtuális gépek hozzáadása a tartományhoz

Miután hozzárendelt egy statikus IP-címet a virtuális gépekhez, adja hozzá a virtuális gépeket a tartományhoz.

![17. ábra: virtuális gép hozzáadása tartományhoz][sap-ha-guide-figure-3006]

_**17. ábra:** Virtuális gép hozzáadása tartományhoz_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Beállításjegyzék-bejegyzések hozzáadása az SAP ASCS/SCS-példányhoz tartozó fürtcsomópontok esetében

A Azure Load Balancer belső terheléselosztó zárja be a kapcsolatokat, ha a kapcsolatok meghatározott időtartamon belül üresjáratban vannak (Üresjárati időkorlát). A párbeszédpanelen lévő SAP-munkafolyamatok az első sorba helyezni/deüzenetsor-kérelem elküldését követően megnyitják az SAP sorba helyezni folyamathoz való kapcsolódást. Ezek a kapcsolatok általában mindaddig megmaradnak, amíg a munkahelyi folyamat vagy a sorba helyezni folyamat újraindul. Ha azonban a kapcsolat üresjáratban van egy meghatározott időtartamon belül, az Azure belső terheléselosztó kizárja a kapcsolatokat. Ez nem jelent problémát, mert az SAP munkafolyamata újra létrehozza a kapcsolatot a sorba helyezni folyamattal, ha már nem létezik. Ezek a tevékenységek dokumentálva vannak az SAP-folyamatok fejlesztői nyomkövetésében, de nagy mennyiségű extra tartalmat hoznak létre ezekben a nyomkövetésekben. Érdemes megváltoztatnia a TCP/IP-t `KeepAliveTime` és `KeepAliveInterval` a fürt csomópontjait is. Kombinálja ezeket a módosításokat a TCP/IP-paraméterekben az SAP-profil paramétereinek leírásával, a cikk későbbi részében leírtak szerint.

Ha az SAP ASCS/SCS-példányt mindkét fürtcsomóponton szeretné felvenni, először adja hozzá ezeket a Windows-beállításjegyzékbeli bejegyzéseket az SAP ASCS/SCS-hez készült Windows-fürtcsomópontokon:

| Elérési út | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**3. táblázat:** Az első TCP/IP-paraméter módosítása_

Ezt követően adja hozzá ezt a Windows-beállításjegyzékbeli bejegyzést a Windows-fürtcsomópontokon az SAP ASCS/SCS-hez:

| Elérési út | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**4. táblázat:** A második TCP/IP-paraméter módosítása_

**A módosítások alkalmazásához indítsa újra a fürtcsomópontok csomópontját**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Windows Server feladatátvételi fürtszolgáltatási fürt beállítása SAP ASCS/SCS-példányhoz

A Windows Server feladatátvételi fürtszolgáltatási fürt SAP ASCS/SCS-példányhoz való beállítása a következő feladatokat foglalja magában:

- Fürtcsomópontok összegyűjtése a fürt konfigurációjában
- Fürt fájlmegosztás-megosztásának konfigurálása

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Fürt csomópontjainak összegyűjtése

1. A szerepkörök és szolgáltatások hozzáadása varázslóban adja hozzá a feladatátvételi fürtszolgáltatást a fürtcsomópontokon.
2. Állítsa be a feladatátvevő fürtöt Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelő területen válassza a **fürt létrehozása** lehetőséget, majd adja hozzá a csak az első fürt nevét, a csomópontot. Még ne adja hozzá a második csomópontot; egy későbbi lépésben hozzáadja a második csomópontot.

   ![18. ábra: az első fürtcsomópont kiszolgálójának vagy virtuális gépnek a nevének hozzáadása][sap-ha-guide-figure-3007]

   _**18. ábra:** Adja hozzá az első fürtcsomópont-kiszolgáló vagy virtuális gép nevét_

3. Adja meg a fürt hálózatnév (virtuális állomásnév) nevét.

   ![19. ábra: a fürt nevének megadása][sap-ha-guide-figure-3008]

   _**19. ábra:** Adja meg a fürt nevét_

4. A fürt létrehozása után futtasson egy fürt-ellenőrzési tesztet.

   ![20. ábra: a fürt érvényesítési ellenőrzésének futtatása][sap-ha-guide-figure-3009]

   _**20. ábra:** A fürt érvényesítési ellenőrzésének futtatása_

   A folyamat ezen pontján a lemezekkel kapcsolatos figyelmeztetések figyelmen kívül hagyhatók. Később a tanúsító fájlmegosztás és a SIOS megosztott lemezek is felvehetők. Ebben a szakaszban nem kell aggódnia a kvórumtal kapcsolatban.

   ![21. ábra: nem található kvórum lemez][sap-ha-guide-figure-3010]

   _**21. ábra:** Nem található kvórum lemez_

   ![22. ábra: az alapfürt erőforrásának új IP-címnek kell lennie][sap-ha-guide-figure-3011]

   _**22. ábra:** Az alapszintű fürt erőforrásának új IP-címnek kell lennie_

5. Módosítsa az alapszintű fürtszolgáltatás IP-címét. A fürt addig nem indul el, amíg meg nem változtatja az alapvető fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címe a virtuálisgép-csomópontok egyikére mutat. Ezt az alapszintű fürtszolgáltatás IP-erőforrásának **Tulajdonságok** lapján teheti meg.

   Például hozzá kell rendelni egy IP-címet (példánkban a **10.0.0.42**) a fürt virtuális **PR1-ASCs-VIR**-hez.

   ![23. ábra: a Properties (Tulajdonságok) párbeszédpanelen módosítsa az IP-címet][sap-ha-guide-figure-3012]

   _**23. ábra:** A **Tulajdonságok** párbeszédpanelen módosítsa az IP-címet_

   ![24. ábra: a fürt számára fenntartott IP-cím kiosztása][sap-ha-guide-figure-3013]

   _**24. ábra:** A fürt számára fenntartott IP-cím kiosztása_

6. A fürt virtuális gazdagépének neve online állapotba hozása.

   ![25. ábra: a cluster Core szolgáltatás működik, és a megfelelő IP-címmel rendelkezik.][sap-ha-guide-figure-3014]

   _**25. ábra:** A cluster Core szolgáltatás működik, és a megfelelő IP-címmel rendelkezik_

7. Adja hozzá a második fürtcsomópont-csomópontot.

   Most, hogy az alapszintű fürtszolgáltatás működik, felveheti a második fürtcsomópont-csomópontot.

   ![26. ábra: a második fürtcsomópont hozzáadása][sap-ha-guide-figure-3015]

   _**26. ábra:** A második fürtcsomópont hozzáadása_

8. Adja meg a második fürtcsomópont-gazdagép nevét.

   ![27. ábra: adja meg a fürt második csomópontjának állomásnevét][sap-ha-guide-figure-3016]

   _**27. ábra:** Adja meg a fürt második csomópontjának állomásnevét_

   > [!IMPORTANT]
   > Ügyeljen arra, hogy az **összes megfelelő tároló hozzáadása a fürthöz** jelölőnégyzet **ne** legyen bejelölve.  
   >
   >

   ![28. ábra: ne jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

   _**28. ábra:** Ne **jelölje be** a jelölőnégyzetet_

   A kvórumtal és a lemezekkel kapcsolatos figyelmeztetések figyelmen kívül hagyhatók. Állítsa be a kvórumot, és ossza meg később a lemezt a következő témakörben leírtak szerint: a [SIOS DataKeeper-fürt kiadásának telepítése az SAP ASCS/SCS-fürt megosztott lemeze számára][sap-ha-guide-8.12.3].

   ![29. ábra: a lemez kvórumával kapcsolatos figyelmeztetések figyelmen kívül hagyása][sap-ha-guide-figure-3018]

   _**29. ábra:** A lemez kvórumával kapcsolatos figyelmeztetések figyelmen kívül hagyása_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Fürt fájlmegosztás-megosztásának konfigurálása

A fürt fájlmegosztás-megosztásának konfigurálása a következő feladatokat foglalja magában:

- Fájlmegosztás létrehozása
- A tanúsító fájlmegosztás Kvórumának beállítása Feladatátvevőfürt-kezelő

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Fájlmegosztás létrehozása

1. Jelöljön ki egy tanúsító fájlmegosztást a kvórum lemeze helyett. A SIOS DataKeeper támogatja ezt a beállítást.

   A jelen cikkben szereplő példákban a tanúsító fájlmegosztás az Azure-ban futó Active Directory/DNS-kiszolgálón található. A tanúsító fájlmegosztás neve **domcontr-0**. Mivel a VPN-kapcsolat az Azure-hoz lett konfigurálva (helyek közötti VPN-vagy Azure-ExpressRoute keresztül), a Active Directory/DNS szolgáltatás a helyszínen található, és nem alkalmas a tanúsító fájlmegosztás futtatására.

   > [!NOTE]
   > Ha a Active Directory/DNS szolgáltatás csak a helyszínen fut, ne konfigurálja a tanúsító fájlmegosztás a helyszínen futó Active Directory/DNS Windows operációs rendszeren. Az Azure-ban futó fürtcsomópontok és a helyszíni Active Directory/DNS helyszíni hálózati késése túl nagy lehet, és kapcsolódási problémákhoz vezethet. Ügyeljen arra, hogy a tanúsító fájlmegosztás olyan Azure-beli virtuális gépen legyen konfigurálva, amely a fürtcsomópont mellett fut.  
   >
   >

   A kvórum meghajtójának legalább 1 024 MB szabad területtel kell rendelkeznie. A kvórum meghajtójának 2 048 MB szabad területét javasoljuk.

2. Adja hozzá a fürt neve objektumot.

   ![30. ábra: a fürt nevének objektumához tartozó megosztási engedélyek kiosztása][sap-ha-guide-figure-3019]

   _**30. ábra:** Rendelje hozzá az engedélyeket a fürt neve objektum megosztásához_

   Győződjön meg arról, hogy az engedélyek tartalmazzák a hatóságot a fürt neve objektum (példánkban: **PR1-ASCs-VIR $**) megosztásában tárolt adatmódosításra.

3. A fürt neve objektum listához való hozzáadásához válassza a **Hozzáadás** lehetőséget. Módosítsa a szűrőt a számítógép-objektumok kereséséhez a 31. ábrán láthatók mellett.

   ![31. ábra: az Objektumtípusok módosítása a számítógépek belefoglalásához][sap-ha-guide-figure-3020]

   _**31. ábra:** Az Objektumtípusok módosítása a számítógépek belefoglalásához_

   ![32. ábra: a számítógépek jelölőnégyzet bejelölése][sap-ha-guide-figure-3021]

   _**32. ábra:** Jelölje be a **számítógépek** jelölőnégyzetet_

4. Adja meg a fürt neve objektumot a 31. ábrán látható módon. Mivel a rekord már létrejött, módosíthatja az engedélyeket, ahogy az a 30. ábrán látható.

5. Válassza ki a megosztás **Biztonság** lapját, majd állítsa be részletesebben a fürt neve objektumra vonatkozó engedélyeket.

   ![33. ábra: a fürt neve objektum biztonsági attribútumainak beállítása a fájlmegosztás kvórumán][sap-ha-guide-figure-3022]

   _**33. ábra:** A fürt neve objektum biztonsági attribútumainak beállítása a fájlmegosztás kvórumán_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> A tanúsító fájlmegosztás Kvórumának beállítása Feladatátvevőfürt-kezelő

1. Nyissa meg a kvórum beállításának konfigurálása varázslót.

   ![34. ábra: a fürt Kvórumának beállítása varázsló elindítása][sap-ha-guide-figure-3023]

   _**34. ábra:** A fürt Kvórumának beállítása varázsló elindítása_

2. A **kvórum konfigurációjának kiválasztása** lapon válassza **a tanúsító kvórum kijelölése** lehetőséget.

   ![35. ábra: az Ön által választható kvórum-konfigurációk][sap-ha-guide-figure-3024]

   _**35. ábra:** Olyan kvórum-konfigurációk, amelyek közül választhat_

3. A **tanúsító kvórum kijelölése** lapon válassza a **tanúsító fájlmegosztás konfigurálása** lehetőséget.

   ![36. ábra: a tanúsító fájlmegosztás kiválasztása][sap-ha-guide-figure-3025]

   _**36. ábra:** Tanúsító fájlmegosztás kiválasztása_

4. Adja meg a fájlmegosztás UNC elérési útját (példánkban: \\ domcontr-0\FSW). Ha meg szeretné tekinteni az elvégezhető módosítások listáját, válassza a **tovább** lehetőséget.

   ![37. ábra: a tanúsító megosztás fájlmegosztási helyének megadása][sap-ha-guide-figure-3026]

   _**37. ábra:** A tanúsító megosztás fájlmegosztás helyének megadása_

5. Válassza ki a kívánt módosításokat, majd kattintson a **tovább** gombra. A fürtkonfiguráció sikeres újrakonfigurálását az 38. ábrán látható módon kell megadnia.  

   ![38. ábra: a fürt újrakonfigurálásának megerősítése][sap-ha-guide-figure-3027]

   _**38. ábra:** Erősítse meg, hogy újrakonfigurálta a fürtöt_

Miután sikeresen telepítette a Windows feladatátvevő fürtöt, módosítania kell néhány küszöbértéket a feladatátvétel észlelésének az Azure-beli feltételekhez való hozzáigazításához. A módosítandó paramétereket ebben a blogban dokumentáljuk: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) . Feltételezve, hogy a ASCS/SCS-hoz készült Windows-fürt konfigurációját felépítő két virtuális gép ugyanabban az alhálózatban található, a következő paramétereket kell módosítania ezekre az értékekre:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Ezek a beállítások az ügyfelekkel lettek tesztelve, és jó kompromisszumot biztosítanak ahhoz, hogy az egyik oldalon elég rugalmasak legyenek. Másfelől ezek a beállítások gyors feladatátvételt biztosítanak a valós hibákra vonatkozóan az SAP szoftver vagy a csomópont/virtuális gép meghibásodása esetén. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> A SIOS DataKeeper-fürt kiadásának telepítése az SAP ASCS/SCS-fürt megosztott lemezéhez

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatási konfigurációval az Azure-ban. Egy SAP ASCS/SCS-példány telepítéséhez azonban szükség van egy megosztott lemez erőforrásra. Nem hozható létre az Azure-ban szükséges megosztott lemez erőforrásai. A SIOS DataKeeper-fürt kiadása egy külső gyártótól származó megoldás, amellyel megosztott lemezes erőforrásokat hozhat létre.

A SIOS DataKeeper-fürt kiadásának az SAP ASCS/SCS-fürt megosztott lemezre való telepítése a következő feladatokat foglalja magában:

- A .NET-keretrendszer 3,5 hozzáadása
- SIOS-DataKeeper telepítése
- SIOS DataKeeper beállítása

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> A .NET-keretrendszer 3,5-es hozzáadása
A Microsoft .NET Framework 3,5 nem aktiválódik automatikusan, vagy nincs telepítve a Windows Server 2012 R2 rendszerre. Mivel a SIOS DataKeeper a .NET-keretrendszert a DataKeeper telepített összes csomóponton telepíteni kell, a .NET-keretrendszer 3,5-es verziójának telepítése szükséges a fürtben lévő összes virtuális gép vendég operációs rendszerén.

A .NET-keretrendszer 3,5 kétféleképpen adható hozzá:

- A Windows szerepkörök és szolgáltatások hozzáadása varázslójával a 39 ábrán látható módon.

  ![39. ábra: a .NET-keretrendszer 3,5 telepítése a szerepkörök és szolgáltatások hozzáadása varázsló használatával][sap-ha-guide-figure-3028]

  _**39. ábra:** A .NET-keretrendszer 3,5-es telepítése a szerepkörök és szolgáltatások hozzáadása varázsló használatával_

  ![40. ábra: telepítési folyamatjelző sáv a .NET-keretrendszer 3,5-es verziójának a szerepkörök és szolgáltatások hozzáadása varázsló használatával történő telepítésekor][sap-ha-guide-figure-3029]

  _**40. ábra:** Telepítési folyamatjelző sáv a .NET-keretrendszer 3,5-es verziójának a szerepkörök és szolgáltatások hozzáadása varázslóval történő telepítésekor_

- Használja a dism.exe parancssori eszközt. Ilyen típusú telepítés esetén el kell érnie a SxS könyvtárat a Windows telepítési adathordozóján. Egy rendszergazda jogú parancssorba írja be a következőt:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> A SIOS DataKeeper telepítése

Telepítse a SIOS DataKeeper-fürt kiadását a fürt mindegyik csomópontján. Ha virtuális megosztott tárolót szeretne létrehozni a SIOS DataKeeper, hozzon létre egy szinkronizált tükröt, majd szimulálja a fürt megosztott tárolóját.

A SIOS szoftver telepítése előtt hozza létre a tartományi felhasználó **DataKeeperSvc**.

> [!NOTE]
> Adja hozzá a **DataKeeperSvc** felhasználót a **helyi rendszergazda** csoporthoz mindkét fürtcsomóponton.
>
>

A SIOS DataKeeper telepítése:

1. Telepítse a SIOS szoftvert mindkét fürtcsomóponton.

   ![SIOS-telepítő][sap-ha-guide-figure-3030]

   ![41. ábra: a SIOS DataKeeper-telepítésének első lapja][sap-ha-guide-figure-3031]

   _**41. ábra:** A SIOS-DataKeeper telepítésének első lapja_

2. Az 42 ábrán látható párbeszédpanelen válassza az **Igen** lehetőséget.

   ![42. ábra: a DataKeeper értesíti, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

   _**42. ábra:** A DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3. A 43 ábrán látható párbeszédpanelen azt javasoljuk, hogy válasszon ki egy **tartományi vagy kiszolgálói fiókot**.

   ![43. ábra: felhasználó kiválasztása a SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**43. ábra:** Felhasználó kiválasztása a SIOS DataKeeper_

4. Adja meg a SIOS DataKeeper létrehozott tartományi fiók felhasználónevét és jelszavát.

   ![44. ábra: adja meg a SIOS-DataKeeper telepítésének tartományhoz tartozó felhasználónevét és jelszavát][sap-ha-guide-figure-3034]

   _**44. ábra:** Adja meg a SIOS-DataKeeper telepítésének tartományhoz tartozó felhasználónevét és jelszavát_

5. Telepítse a SIOS DataKeeper-példányának licenckulcs értékét az 45. ábrán látható módon.

   ![45. ábra: a SIOS DataKeeper-licenckulcs megadása][sap-ha-guide-figure-3035]

   _**45. ábra:** Adja meg a SIOS DataKeeper-licenckulcs_

6. Ha a rendszer kéri, indítsa újra a virtuális gépet.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper beállítása

Miután mindkét csomóponton telepítette a SIOS DataKeeper, el kell indítania a konfigurációt. A konfiguráció célja, hogy szinkron adatreplikációt lehessen végrehajtani az egyes virtuális gépekhez csatlakoztatott további virtuális merevlemezek között.

1. Indítsa el a DataKeeper-kezelő és a konfigurációs eszközt, majd válassza a **kiszolgáló összekapcsolását**. (Az 46. ábrán ez a beállítás piros színnel van elválasztva.)

   ![46. ábra: SIOS DataKeeper-kezelő és-konfigurációs eszköz][sap-ha-guide-figure-3036]

   _**46. ábra:** SIOS DataKeeper-kezelő és-konfigurációs eszköz_

2. Adja meg az első csomópont nevét vagy TCP/IP-címét, amelyhez a felügyelet és a konfigurációs eszköz csatlakozni kíván, a második lépésben pedig a második csomópontot.

   ![47. ábra: az első csomópont nevének vagy TCP/IP-címének beszúrása a felügyeleti és konfigurációs eszköznek csatlakoznia kell a kiszolgálóhoz, a második lépésben pedig a második csomópontot.][sap-ha-guide-figure-3037]

   _**47. ábra:** Szúrja be az első csomópont nevét vagy TCP/IP-címét a felügyeleti és konfigurációs eszközhöz, és a második lépésben a második csomópontot._

3. Hozza létre a replikációs feladatot a két csomópont között.

   ![48. ábra: replikációs feladatok létrehozása][sap-ha-guide-figure-3038]

   _**48. ábra:** Replikációs feladatok létrehozása_

   A varázsló végigvezeti a replikációs feladatok létrehozásának folyamatán.
4. Adja meg a forrás-csomópont nevét, TCP/IP-címét és a lemez kötetét.

   ![49. ábra: a replikációs feladatok nevének megadása][sap-ha-guide-figure-3039]

   _**49. ábra:** A replikációs feladatok nevének megadása_

   ![50. ábra: a csomópont alapadatának megadása, amelynek az aktuális forrás-csomópontnak kell lennie.][sap-ha-guide-figure-3040]

   _**50. ábra:** Adja meg a csomópont alapadatait, amelyeknek az aktuális forrás-csomópontnak kell lennie._

5. Adja meg a cél csomópont nevét, TCP/IP-címét és a lemez kötetét.

   ![51. ábra: a csomópont alapadatának megadása, amelynek az aktuális célként megadott csomópontnak kell lennie][sap-ha-guide-figure-3041]

   _**51. ábra:** Adja meg a csomópont alapadatait, amelyeknek az aktuális célként megadott csomópontnak kell lennie._

6. Adja meg a tömörítési algoritmusokat. A példánkban azt javasoljuk, hogy tömörítse a replikálási adatfolyamot. Különösen újraszinkronizálási helyzetekben a replikálási adatfolyam tömörítése jelentősen csökkenti az újraszinkronizálás idejét. Vegye figyelembe, hogy a tömörítés a virtuális gép processzor-és RAM-erőforrásait használja. A tömörítési sebesség növekszik, így a felhasznált CPU-erőforrások mennyisége. Ezt a beállítást később is módosíthatja.

7. Egy másik beállításnak ellenőriznie kell, hogy a replikáció aszinkron módon vagy szinkronban történjen-e. Az *SAP ASCS/SCS-konfigurációk védetté tételéhez szinkron replikálást kell használni*.  

   ![52. ábra: a replikáció részleteinek meghatározása][sap-ha-guide-figure-3042]

   _**52. ábra:** Replikáció részleteinek megadása_

8. Annak megadása, hogy a replikálási feladatokkal replikált kötetet a Windows Server feladatátvételi fürtszolgáltatási fürt megosztott lemezének konfigurációjában kell-e megjeleníteni. Az SAP ASCS/SCS konfiguráció esetében válassza az **Igen** lehetőséget, hogy a Windows-fürt a replikált kötetet megosztott lemezként látja, amelyet fürtözött kötetként használhat.

   ![53. ábra: válassza az Igen lehetőséget a replikált kötet fürtözött kötetként való beállításához.][sap-ha-guide-figure-3043]

   _**53. ábra:** Válassza az **Igen** lehetőséget a replikált kötet fürtözött kötetként való beállításához_

   A kötet létrehozása után a DataKeeper-kezelés és-konfiguráció eszköz azt mutatja, hogy a replikációs feladatok aktívak.

   ![54. ábra: az SAP ASCS/SCS-megosztási lemez DataKeeper szinkron tükrözése aktív][sap-ha-guide-figure-3044]

   _**54. ábra:** Az SAP ASCS/SCS-megosztási lemez DataKeeper szinkron tükrözése aktív_

   Feladatátvevőfürt-kezelő most a lemezt DataKeeper lemezként jeleníti meg, ahogy az a 55. ábrán látható.

   ![55. ábra: az Feladatátvevőfürt-kezelő a replikált DataKeeper lemezt mutatja][sap-ha-guide-figure-3045]

   _**55. ábra:** Feladatátvevőfürt-kezelő megjeleníti a replikált DataKeeper lemezt_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Az SAP NetWeaver System telepítése

Nem írja le az adatbázis-kezelő rendszer beállítását, mert a beállítások a használt adatbázis-kezelő rendszertől függően változnak. Feltételezzük azonban, hogy az adatbázis-kezelői szolgáltatással kapcsolatos magas rendelkezésre állási problémákat a különböző adatbázis-kezelők gyártói támogatják az Azure-ban. Például a SQL Server és az Oracle-adatvédelmet az Oracle-adatbázisok esetében mindig vagy adatbázis-tükrözéssel. Az ebben a cikkben használt forgatókönyvben nem adtak hozzá további védelmet az adatbázis-kezelő szolgáltatáshoz.

Nincsenek különleges megfontolások, ha a különböző adatbázis-kezelő szolgáltatások az Azure-ban ilyen típusú fürtözött SAP ASCS/SCS-konfigurációval működnek.

> [!NOTE]
> Az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek telepítési eljárásai csaknem azonosak. A legfontosabb különbség az, hogy egy SAP ABAP rendszernek van egy ASCS-példánya. Az SAP Java-rendszeren egy SCS-példány van. Az SAP ABAP + Java rendszer egy ASCS-példánnyal és egy olyan SCS-példánnyal rendelkezik, amely ugyanabban a Microsoft feladatátvételi fürtben fut. A rendszer explicit módon megemlíti az egyes SAP NetWeaver telepítési verem telepítési különbségeit. Feltételezzük, hogy minden más rész azonos.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Az SAP telepítése magas rendelkezésre állású ASCS/SCS-példánnyal

> [!IMPORTANT]
> Ügyeljen arra, hogy a DataKeeper tükrözött köteteken ne helyezze el a lapozófájlt. A DataKeeper nem támogatja a tükrözött köteteket. A lapozófájlt egy Azure-beli virtuális gép ideiglenes meghajtójába helyezheti el, amely az alapértelmezett. Ha még nem tette meg, helyezze át a Windows-lapozófájlt az Azure-beli virtuális gép D meghajtóján.
>
>

A magas rendelkezésre állású ASCS/SCS-példánnyal rendelkező SAP telepítése a következő feladatokat foglalja magában:

- Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz
- Az SAP első fürtcsomópont telepítése
- Az ASCS/SCS-példány SAP-profiljának módosítása
- Mintavételi Port hozzáadása
- A Windows tűzfal mintavételi portjának megnyitása

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz

1. A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomásnevét.

   > [!IMPORTANT]
   > A ASCS/SCS-példány virtuális állomásnevét hozzárendelt IP-címnek meg kell egyeznie a Azure Load Balancerhoz rendelt IP-címmel (**< *SID*>-LB-ASCS**).  
   >
   >

   A virtuális SAP ASCS/SCS-állomásnév (**PR1-ASCS-SAP**) IP-címe megegyezik a Azure Load Balancer IP-címével (**PR1-LB-ASCS**).

   ![56. ábra: az SAP ASCS/SCS-fürt virtuális neve és a TCP/IP-cím DNS-bejegyzésének megadása][sap-ha-guide-figure-3046]

   _**56. ábra:** Adja meg a DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális neve és TCP/IP-címe számára._

2. A virtuális gazdagép neveként hozzárendelt IP-cím megadásához válassza a **DNS-kezelő**  >  **tartomány** lehetőséget.

   ![57. ábra: új virtuális név és TCP/IP-cím az SAP ASCS/SCS-fürt konfigurációjához][sap-ha-guide-figure-3047]

   _**57. ábra:** Új virtuális név és TCP/IP-cím az SAP ASCS/SCS-fürt konfigurációjához_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Az SAP első fürtcsomópont telepítése

1. Hajtsa végre az A fürt első csomópontjának beállítását a csomóponton. Például a **PR1-ASCs-0** gazdagépen.
2. Az Azure belső terheléselosztó alapértelmezett portjainak megtartásához válassza a következő lehetőséget:

   * **ABAP-System**: **ASCS** -példány száma **00**
   * **Java-System**: **SCS** -példány száma **01**
   * **ABAP + Java System**: **ASCS** -példány száma **00** és **SCS** -példány száma **01**

   Ha a 00-nál nem nagyobb példányszámot kíván használni a Java SCS-példányhoz tartozó ABAP ASCS-példányhoz és 01-nél, először módosítania kell az Azure belső terheléselosztó alapértelmezett terheléselosztási szabályait az [Azure belső TERHELÉSELOSZTÓ ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása][sap-ha-guide-8.9]című cikkben leírtak szerint.

A következő néhány feladat nem szerepel a szabványos SAP-telepítési dokumentációban.

> [!NOTE]
> Az SAP telepítési dokumentációja leírja, hogyan telepítheti az első ASCS/SCS fürtcsomópont-csomópontot.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Az ASCS/SCS-példány SAP-profiljának módosítása

Új profil paramétert kell hozzáadnia. A profil paraméter megakadályozza a kapcsolódást az SAP-munkafolyamatok és a sorba helyezni-kiszolgáló között, ha túl sokáig tétlenek. Említettük a probléma forgatókönyvét a [beállításjegyzék-bejegyzések hozzáadása az SAP ASCS/SCS-példányon mindkét fürtcsomóponton][sap-ha-guide-8.11]. Ebben a szakaszban két módosítást is bevezetünk néhány Alapszintű TCP/IP-kapcsolódási paraméterre. Egy második lépésben be kell állítania a sorba helyezni-kiszolgálót egy jel küldéséhez, `keep_alive` hogy a kapcsolatok ne elérjenek az Azure belső terheléselosztó üresjárati küszöbértékét.

Az ASCS/SCS-példány SAP-profiljának módosítása:

1. Adja hozzá ezt a profil paramétert az SAP ASCS/SCS instance profilhoz, ha a ENSA1-t használja:

   ```
   enque/encni/set_so_keepalive = true
   ```
   A példánkban az elérési út a következőket eredményezi:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Például az SAP SCS-példány profiljához és a megfelelő elérési úthoz:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

   A ENSA1 és a ENSA2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.  

2. A módosítások alkalmazásához indítsa újra az SAP ASCS/SCS-példányt.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Mintavételi Port hozzáadása

A belső terheléselosztó mintavételi funkciójának használatával biztosíthatja, hogy a teljes fürtkonfiguráció működjön a Azure Load Balancer. Az Azure belső terheléselosztó általában a bejövő munkaterheléseket a részt vevő virtuális gépek között egyenlően osztja el. Ez azonban nem fog működni bizonyos fürtkonfiguráció esetén, mert csak egy példány aktív. A másik példány passzív, és nem fogadja el a számítási feladatok egyikét sem. A mintavételi funkciók segítenek abban az esetben, ha az Azure belső terheléselosztó csak aktív példányhoz rendeli a munkát. A mintavétel funkcióval a belső terheléselosztó képes érzékelni, hogy mely példányok aktívak, és csak a számítási feladattal rendelkező példányt célozzák meg.

Mintavételi Port hozzáadása:

1. A következő PowerShell-parancs futtatásával keresse meg az aktuális **ProbePort** -beállítást. A fürt konfigurációjában lévő egyik virtuális gépen hajtsa végre.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Adjon meg egy mintavételi portot. A mintavétel alapértelmezett portszáma **0**. A példánkban a **62000**-es mintavételi portot használjuk.

   ![58. ábra: a fürtkonfiguráció mintavételi portja alapértelmezés szerint 0.][sap-ha-guide-figure-3048]

   _**58. ábra:** Az alapértelmezett fürtkonfiguráció-mintavételi port a 0_

   A portszám az SAP Azure Resource Manager-sablonokban van meghatározva. A portszám a PowerShellben is hozzárendelhető.

   Ha új ProbePort értéket szeretne beállítani az **SAP <*SID*> IP-** fürterőforrás erőforráshoz, futtassa a következő PowerShell-parancsfájlt. Frissítse a környezet PowerShell-változóit. A szkript futtatása után a rendszer felszólítja, hogy indítsa újra az SAP-fürtöt a módosítások aktiválásához.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Az **SAP <*SID* >** -fürt szerepkör online állapotba hozása után ellenőrizze, hogy az **ProbePort** az új értékre van-e állítva.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![59. ábra: a fürt portjának mintavétele az új érték beállítása után][sap-ha-guide-figure-3049]

   _**59. ábra:** A fürt portjának mintavétele az új érték beállítása után_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> A Windows tűzfal mintavételi portjának megnyitása

A fürt csomópontjain egy Windows tűzfal mintavételi portot kell megnyitni. A következő parancsfájl használatával nyisson meg egy Windows tűzfal mintavételi portot. Frissítse a környezet PowerShell-változóit.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** értéke **62000**. Most már elérheti a fájlmegosztás **\\ \ascsha-clsap\sapmnt** más gazdagépekről, például a következőről: **ascsha-adattervezők**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Az adatbázis-példány telepítése

Az adatbázis-példány telepítéséhez kövesse az SAP telepítési dokumentációjában leírt eljárást.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> A második fürtcsomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatójának lépéseit.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Az SAP ERS Windows-szolgáltatás példányának indítási típusának módosítása

Módosítsa az SAP ERS Windows szolgáltatás indítási típusát **automatikus (Késleltetett indítás)** értékre mindkét fürtcsomóponton.

![60. ábra: az SAP ERS-példány szolgáltatástípus-típusának módosítása késleltetett automatikusra][sap-ha-guide-figure-3050]

_**60. ábra:** Az SAP ERS-példány szolgáltatás típusának megváltoztatása késleltetett automatikusra_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Az SAP Primary Application Server telepítése

Telepítse az elsődleges alkalmazáskiszolgáló (PAS) példányát <*SID*>-di-0 értéket azon a virtuális gépen, amelyet a Pas számára jelölt ki. Nincsenek függőségek az Azure-ban vagy a DataKeeper-specifikus beállításokban.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Az SAP további alkalmazáskiszolgáló telepítése

Telepítsen egy SAP további alkalmazáskiszolgáló (AAS) szolgáltatást minden olyan virtuális gépre, amelyet az SAP Application Server-példány üzemeltetésére jelölt ki. Például <*sid*>-di-1 <*SID*>-di- &lt; n &gt; .

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver rendszer telepítését. Ezután folytassa a feladatátvételi teszttel.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Az SAP-ASCS/SCS-példány feladatátvételi és SIOS-replikálásának tesztelése
A Feladatátvevőfürt-kezelő és a SIOS DataKeeper-kezelő és konfigurációs eszköz használatával egyszerűen tesztelheti és figyelheti az SAP ASCS/SCS-példányok feladatátvételét és SIOS.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS-példány fut az A fürtcsomóponton

Az **SAP PR1** -fürt az a fürtcsomóponton fut. Például: **PR1-ASCs-0**. Rendelje hozzá a (z) az **SAP PR1** tartozó megosztott lemezmeghajtót, amely a ASCS/SCS-példány által használt, az a csomópontra.

![61. ábra: Feladatátvevőfürt-kezelő: az SAP <SID> fürtözött csoport az A fürtcsomóponton fut][sap-ha-guide-figure-5000]

_**61. ábra:** Feladatátvevőfürt-kezelő: az SAP <*SID*> a fürt csomópontja az a fürtcsomóponton fut_

A SIOS DataKeeper-kezelés és-konfigurálás eszközben láthatja, hogy a megosztott lemez adatainak szinkron módon replikálódnak a (z) "A" fürt " Például a rendszer replikálja a **PR1-ASCs-0 [10.0.0.40]** típusról a **PR1-ASCs-1 [10.0.0.41]** értékre.

![62. ábra: a SIOS DataKeeper-ben replikálja a helyi kötetet a fürt csomópontból a B csomópontba][sap-ha-guide-figure-5001]

_**62. ábra:** A SIOS DataKeeper-ben replikálja a helyi kötetet a fürt csomópontból a B csomópontba._

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Feladatátvétel az A csomópontról a B csomópontra

1. Válassza ki az alábbi lehetőségek egyikét az SAP <*SID*>-fürt feladatátvételének elindításához a (z) "a" fürt "a" csomópontról a B fürtre:
   - Feladatátvevőfürt-kezelő használata  
   - A feladatátvevő fürt PowerShell használata

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Indítsa újra az A fürtöt a Windows vendég operációs rendszeren belül (ez automatikusan feladatátvételt kezdeményez az SAP <*SID*> fürtcsomóponton az a csomópontról a B csomópontra).  
3. Indítsa újra az A fürtcsomópont-csomópontot a Azure Portalból (Ezzel elindítja az SAP <*SID*> fürtözött csoport automatikus feladatátvételét az a csomópontról a B csomópontra).  
4. Indítsa újra az A csomópontot a Azure PowerShell használatával (Ezzel elindítja az SAP <*SID*> fürtözött csoport automatikus feladatátvételét az a csomópontról a B csomópontra).

   A feladatátvételt követően az SAP <*SID*> a fürt a B csomóponton fut. Például a **PR1-ASCs-1** számítógépen fut.

   ![63. ábra: Feladatátvevőfürt-kezelő az SAP <SID> fürterőforrás a B fürtcsomóponton fut.][sap-ha-guide-figure-5002]

   _**63. ábra**: FELADATÁTVEVŐFÜRT-kezelő az SAP <*SID*> fürterőforrás a B fürtcsomóponton fut._

   A megosztott lemez most már csatolva van a B csomóponthoz. a SIOS DataKeeper a B csomóponton lévő forrás kötet-meghajtóról származó adatok replikálását célozza meg a (z) "A" fürt "A" csomópontjára. A replikálás például a **PR1-ASCs-1 [10.0.0.41]** verzióról a **PR1-ASCs-0 [10.0.0.40]** verzióra.

   ![64. ábra: a SIOS DataKeeper replikálja a B fürtcsomópont helyi kötetét az A fürtcsomópont-csomópontra][sap-ha-guide-figure-5003]

   _**64. ábra:** A SIOS DataKeeper replikálja a B fürtcsomópont helyi kötetét az A fürt csomópontjának_
