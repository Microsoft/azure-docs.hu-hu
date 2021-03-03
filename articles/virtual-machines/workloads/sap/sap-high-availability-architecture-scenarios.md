---
title: Azure virtuális gépek HA architektúra és forgatókönyvek az SAP NetWeaver esetében | Microsoft Docs
description: Magas rendelkezésre állású architektúra és forgatókönyvek az Azure-beli SAP NetWeaver-Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d689db4916758e13311ad181fb3b5d7ca9a85260
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675316"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver-hoz

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állási konfiguráció)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Terminológiai definíciók

**Magas rendelkezésre állás**: olyan technológiákat mutat be, amelyek az informatikai szolgáltatások folytonosságát biztosítják a redundáns, hibatűrő vagy feladatátvételi védelemmel ellátott összetevőkön keresztül *ugyanazon* az adatközponton belül. Ebben az esetben az adatközpont egy Azure-régión belül helyezkedik el.

Vész- **helyreállítás**: az IT-szolgáltatások megszakadásának és helyreállításának minimalizálására is utal, de *különböző* adatközpontokban, amelyek több száz kilométerre lehetnek egymástól. Ebben az esetben az adatközpontok különböző Azure-régiókba kerülhetnek, ugyanabban a geopolitikai régióban vagy az Ön által Ön által létrehozott helyen.


## <a name="overview-of-high-availability"></a>A magas rendelkezésre állás áttekintése
Az Azure-ban az SAP magas rendelkezésre állása három típusba különíthető el:

* **Azure-infrastruktúra magas rendelkezésre állása**: 

    A magas rendelkezésre állás például magában foglalhatja a számítási (VM), a hálózat vagy a tárterület előnyeit, valamint az SAP-alkalmazások rendelkezésre állásának növelését.

* Az **Azure-infrastruktúra virtuális gépek újraindítása az SAP-alkalmazások *magasabb rendelkezésre állásának* elérése érdekében**: 

    Ha úgy dönt, hogy nem használ olyan funkciókat, mint például a Windows Server feladatátvételi fürtszolgáltatás (WSFC) vagy a pacemaker Linux rendszeren, az Azure-beli virtuális gépek újraindítását használja a rendszer. Az SAP-rendszereket az Azure fizikai kiszolgálói infrastruktúra és a teljes mögöttes Azure platform tervezett és nem tervezett leállásával védi.

* **SAP-alkalmazás magas rendelkezésre állása**: 

    Az SAP-rendszerek teljes körű rendelkezésre állásának eléréséhez meg kell felelnie az összes kritikus fontosságú SAP-rendszerösszetevőnek. Például:
    * Redundáns SAP-alkalmazás-kiszolgálók.
    * Egyedi összetevők. Ilyen lehet például egy meghibásodási pont (SPOF) összetevő (például egy SAP ASCS/SCS-példány vagy egy adatbázis-kezelő rendszer).

Az Azure-ban az SAP magas rendelkezésre állása különbözik az SAP magas rendelkezésre állásával egy helyszíni fizikai vagy virtuális környezetben. Az alábbi, a [VMware és a Hyper-V rendszerű virtuális környezetekben található, a Microsoft Windows rendszeren futó, magas rendelkezésre állást és üzletmenet-folytonosságot][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] ismertető tanulmány a Windows rendszerben virtualizált környezetekben a szabványos SAP magas rendelkezésre állási konfigurációkat ismerteti.

A Linux rendszerhez nincs sapinst integrált SAP magas rendelkezésre állási konfiguráció, mivel a Windows rendszerben van. További információ a Linux rendszerhez készült SAP magas rendelkezésre állásáról: [magas rendelkezésre állású partner adatai][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Azure-infrastruktúra – magas rendelkezésre állás

### <a name="sla-for-single-instance-virtual-machines"></a>Egypéldányos virtuális gépek SLA-ja

A Premium Storage szolgáltatással jelenleg a 99,9%-os virtuális gépekre vonatkozó SLA érhető el. Annak megismeréséhez, hogy az egyes virtuális gépek rendelkezésre állása milyen lehet, a különböző elérhető [Azure-szolgáltatói szerződések][azure-sla]termékeit is felépítheti.

A számítás alapja havi 30 nap, vagy 43 200 perc. Például a 0,05%-os állásidő megfelel a 21,6 percnek. A szokásos módon a különböző szolgáltatások rendelkezésre állását a következőképpen számítjuk ki:

(Rendelkezésre állási szolgáltatás #1/100) * (rendelkezésre állási szolgáltatás #2/100) * (rendelkezésre állási szolgáltatás #3/100) \* ...

Például:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 vagy a 99,75% teljes rendelkezésre állása.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Ugyanazon rendelkezésre állási csoportba tartozó virtuális gépek több példánya
Minden olyan virtuális gép esetében, amelynek két vagy több példánya ugyanazon *rendelkezésre állási csoportba* tartozik, garantáljuk, hogy a Virtuálisgép-kapcsolat legalább egy példányhoz legalább az idő 99,95%-ában elérhető lesz.

Ha két vagy több virtuális gép ugyanannak a rendelkezésre állási csoportnak a részét képezi, a rendelkezésre állási csoport minden virtuális gépe egy *frissítési tartományt* és egy tartalék *tartományt* rendel hozzá a mögöttes Azure platformhoz.

* A **frissítési tartományok** garantálják, hogy az Azure-infrastruktúra tervezett karbantartása során egyszerre több virtuális gép sem indul újra. Egyszerre csak egy virtuális gép újraindul.

* A tartalék **tartományok** garantálják, hogy a virtuális gépek olyan hardver-összetevőkön legyenek telepítve, amelyek nem rendelkeznek közös áramforrással és hálózati kapcsolóval. Ha a kiszolgálók, hálózati kapcsolók vagy áramforrások nem tervezett állásidőbe esnek, csak egy virtuális gépet érint.

További információ: [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban][azure-virtual-machines-manage-availability].

A rendelkezésre állási csoport a magas rendelkezésre állás eléréséhez használható:

* Redundáns SAP-alkalmazás-kiszolgálók.  
* Fürtök két vagy több csomóponttal (például a virtuális gépekkel), amelyek védik a SPOFs, például egy SAP ASCS/SCS-példányt vagy egy adatbázis-kezelő rendszert.


### <a name="azure-availability-zones"></a>Azure Availability Zones
Az Azure a különböző [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/) [Azure Availability Zones](../../../availability-zones/az-overview.md) fogalmakat ismerteti. Az Azure-régiókban, ahol a Availability Zones kínálják, az Azure-régiók több adatközponttal rendelkeznek, amelyek függetlenek az áramforrás, a hűtés és a hálózat kínálatával. A különböző zónák egyetlen Azure-régión belüli felajánlásának oka az, hogy lehetővé teszi az alkalmazások üzembe helyezését két vagy három Availability Zonesban. Feltételezve, hogy az áramforrások és/vagy a hálózatok problémái csak az egyik rendelkezésre állási zóna infrastruktúráját érintik, az alkalmazások Azure-régión belüli üzembe helyezése továbbra is teljesen működőképes. Az egyes zónákban lévő virtuális gépek esetében előfordulhat, hogy a rendszer néhány kisebb kapacitással is elvész. A másik két zónában lévő virtuális gépek azonban még mindig működnek. A zónákat felkínáló Azure-régiók a [Azure Availability Zonesban](../../../availability-zones/az-overview.md)szerepelnek.

A Availability Zones használatával néhány megfontolandó szempontot figyelembe kell venni. A szempontok listája, például:

- Az Azure rendelkezésre állási csoportjai nem helyezhetők üzembe egy rendelkezésre állási zónán belül. Ki kell választania egy rendelkezésre állási zónát vagy egy rendelkezésre állási készletet egy virtuális gép üzembe helyezési kerete.
- A Windows feladatátvételi fürtszolgáltatás vagy a Linux pacemaker alapján nem használhatja az [Alapszintű Load Balancer](../../../load-balancer/load-balancer-overview.md) a feladatátvevő fürtre vonatkozó megoldások létrehozásához. Ehelyett az [Azure standard Load BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) -t kell használnia
- A Azure Availability Zones nem biztosítanak bizonyos távolságot a különböző zónák között az egyik régión belül
- A különböző Azure-régiókban lévő különböző Azure Availability Zones közötti hálózati késés eltérő lehet az Azure-régiótól a régióig. Előfordulnak olyan esetek, amikor az ügyfél a különböző zónákon üzembe helyezett SAP-alkalmazási réteget ésszerű módon futtatja, mivel az egyik zónából az aktív adatbázis-kezelő virtuális gépre irányuló hálózati késés továbbra is elfogadható az üzleti folyamatok hatására. Mivel az egyik zónában található aktív adatbázis-kezelő virtuális gép és egy másik zónában lévő virtuális gép SAP-alkalmazási példánya közötti késés túlságosan zavaró lehet, és nem fogadható el az SAP üzleti folyamatok számára. Ennek eredményeképpen az üzembe helyezési architektúrának eltérőnek kell lennie az alkalmazás aktív/aktív architektúrája vagy az aktív/passzív architektúra esetében, ha a késés túl magas.
- Az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) használata kötelező a Azure Availability Zonesba való üzembe helyezéshez 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>A virtuális gépek tervezett és nem tervezett karbantartása

A virtuális gépek rendelkezésre állását az Azure-platform két különböző eseménye érintheti:

* A **tervezett karbantartási** események a Microsoft által a mögöttes Azure platformon végzett időszakos frissítések. A frissítések javítják a virtuális gépek által futtatott platform-infrastruktúra teljes megbízhatóságát, teljesítményét és biztonságát.

* Nem **tervezett karbantartási** események történnek, ha a virtuális gép alapjául szolgáló hardver vagy fizikai infrastruktúra valamilyen módon meghiúsult. Ilyen lehet például a helyi hálózati hibák, a helyi lemezek meghibásodása vagy más rack szintű meghibásodások. Ha a rendszer ilyen hibát észlel, az Azure platform automatikusan áttelepíti a virtuális gépet a sérült fizikai kiszolgálóról, amely a virtuális gépet egy kifogástalan állapotú fizikai kiszolgálóra helyezi. Ilyen események ritkán fordulnak elő, de előfordulhat, hogy a virtuális gép újraindítását is okozhatja.

További információ: [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundancia az Azure Storage szolgáltatásban
A Storage-fiókban lévő adatait a rendszer mindig replikálja a tartósság és a magas rendelkezésre állás biztosítása érdekében, az Azure Storage SLA-nak az átmeneti hardver meghibásodása esetén is.

Mivel az Azure Storage alapértelmezés szerint három rendszerképet tárol, a RAID 5 vagy RAID 1 több Azure-lemezre való használata szükségtelen.

További információ: [Azure Storage-replikáció][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
A Managed Disks Azure Resource Manager, amelyet az Azure Storage-fiókokban tárolt virtuális merevlemezek (VHD-k) helyett ajánlott használni. A felügyelt lemezek automatikusan a virtuális gép Azure-beli rendelkezésre állási készletével csatlakoznak. Megnövelik a virtuális gép és a rajta futó szolgáltatások rendelkezésre állását.

További információ: az  [Azure Managed Disks áttekintése][azure-storage-managed-disks-overview].

Javasoljuk, hogy felügyelt lemezeket használjon, mivel ezek egyszerűbbé teszik a virtuális gépek üzembe helyezését és felügyeletét.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Az Azure-infrastruktúrák magas rendelkezésre állásának kihasználása az SAP-alkalmazások *magasabb rendelkezésre állásának* eléréséhez

Ha úgy dönt, hogy nem használ olyan funkciókat, mint például a WSFC vagy a pacemaker a Linux rendszeren (jelenleg csak SUSE Linux Enterprise Server [SLES] 12-es és újabb verziók esetében támogatott), az Azure-beli virtuális gépek újraindítása is használatban van. Az SAP-rendszereket az Azure fizikai kiszolgálói infrastruktúra és a teljes mögöttes Azure platform tervezett és nem tervezett leállásával védi.

További információ erről a megközelítésről: [Az Azure-infrastruktúra virtuális gépek újraindításának használata az SAP-rendszer magasabb rendelkezésre állásának eléréséhez][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> SAP-alkalmazások magas rendelkezésre állása az Azure IaaS

Az SAP-rendszerek teljes körű rendelkezésre állásának eléréséhez meg kell felelnie az összes kritikus fontosságú SAP-rendszerösszetevőnek. Például:
  * Redundáns SAP-alkalmazás-kiszolgálók.
  * Egyedi összetevők. Ilyen lehet például egy meghibásodási pont (SPOF) összetevő (például egy SAP ASCS/SCS-példány vagy egy adatbázis-kezelő rendszer).

A következő részekben azt mutatjuk be, hogyan lehet magas rendelkezésre állást elérni mindhárom kritikus fontosságú SAP rendszerösszetevőnél.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Magas rendelkezésre állású architektúra SAP-alkalmazások kiszolgálói számára

> Ez a szakasz a következőkre vonatkozik:
>
> ![Windows-embléma.][Logo_Windows] Windows és ![Linux-embléma.][Logo_Linux] Linux
>

Általában nincs szükség speciális, magas rendelkezésre állású megoldásra az SAP-alkalmazáskiszolgáló és a párbeszédpanel-példányok esetében. A redundancia révén magas rendelkezésre állást érhet el, és az Azure Virtual Machines különböző példányain több párbeszédpanel-példányt is konfigurálhat. Legalább két, az Azure-beli virtuális gépek két példányán telepített SAP-alkalmazás példányának kell lennie.

![1. ábra: magas rendelkezésre állású SAP Application Server][sap-ha-guide-figure-2000]

_**1. ábra:** Magas rendelkezésre állású SAP Application Server_

Az SAP Application Server-példányokat futtató összes virtuális gépet ugyanabba az Azure-beli rendelkezésre állási csoportba kell helyeznie. Az Azure rendelkezésre állási készlete biztosítja a következőket:

* Az összes virtuális gép nem tartozik ugyanahhoz a frissítési tartományhoz.  
    A frissítési tartományok gondoskodnak arról, hogy a virtuális gépek ne frissüljenek egyidejűleg a tervezett karbantartási állásidő során.

    Az Azure skálázási egységben különböző frissítési és tartalék tartományokra épülő alapszintű funkciók már bekerültek a [frissítési tartományok][planning-guide-3.2.2] szakaszba.

* Az összes virtuális gép nem tartozik ugyanahhoz a tartalék tartományhoz.  
    A tartalék tartomány biztosítja a virtuális gépek üzembe helyezését, így egyetlen meghibásodási pont sem befolyásolja az összes virtuális gép rendelkezésre állását.

Az Azure-beli rendelkezésre állási csoport által az Azure-méretezési egységben használható frissítési és tartalék tartományok száma véges. Ha továbbra is egyetlen rendelkezésre állási csoportba helyezi a virtuális gépeket, két vagy több virtuális gép végül ugyanabban a hiba-vagy frissítési tartományban lesz.

Ha a dedikált virtuális gépeken néhány SAP Application Server-példányt telepít, feltételezve, hogy öt frissítési tartománnyal rendelkezünk, az alábbi kép látható. A rendelkezésre állási csoporton belüli frissítési és tartalék tartományok tényleges maximális száma a jövőben változhat:

![2. ábra: az SAP-alkalmazások kiszolgálóinak magas rendelkezésre állása az Azure rendelkezésre állási készletében ][planning-guide-figure-3000]
 _**2. ábra:** az SAP-alkalmazások kiszolgálóinak magas rendelkezésre állása Azure rendelkezésre állási csoportokban_

További információ: [a Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban][azure-virtual-machines-manage-availability].

További információ: az Azure Virtual Machines tervezésének és megvalósításának Azure-beli [rendelkezésre állási csoportjai][planning-guide-3.2.3] című rész, SAP NetWeaver-dokumentum.

**Csak a nem felügyelt lemezek:** Mivel az Azure Storage-fiók egy lehetséges meghibásodási pont, fontos, hogy legalább két Azure Storage-fiókkal rendelkezzen, amelyekben legalább két virtuális gép terjeszthető. Egy ideális beállítás esetén az SAP-példányt futtató virtuális gépek lemezei egy másik Storage-fiókban lesznek telepítve.

> [!IMPORTANT]
> Erősen ajánlott az Azure Managed Disks használata az SAP magas rendelkezésre állású telepítéséhez. Mivel a felügyelt lemezek automatikusan összehangolják a virtuális gép rendelkezésre állási csoportját, amelyhez csatlakoznak, a virtuális gép és a rajta futó szolgáltatások rendelkezésre állását növelhetik.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Magas rendelkezésre állású architektúra egy SAP ASCS/SCS-példányhoz Windows rendszeren

> ![Windows-embléma.][Logo_Windows] Windows
>

WSFC-megoldás használatával biztosíthatja az SAP ASCS/SCS-példányok elleni védekezést. A megoldás két változattal rendelkezik:

* **Fürt az SAP ASCS/SCS-példány fürtözött megosztott lemezek használatával**: az architektúrával kapcsolatos további információkért tekintse meg a fürt [SAP ASCS/SCS-példánya egy Windows feladatátvevő fürtön megosztott lemez használatával][sap-high-availability-guide-wsfc-shared-disk]című témakört.   

* **Fürt az SAP ASCS/SCS-példány a fájlmegosztás használatával**: az architektúrával kapcsolatos további információkért lásd: [SAP ASCS/SCS-példány fürthöz való használata Windows feladatátvevő fürtön a fájlmegosztás használatával][sap-high-availability-guide-wsfc-file-share].

* **Az SAP ASCS/SCS-példány fürtje a ANF SMB-megosztás használatával**: az architektúrával kapcsolatos további információkért tekintse meg a fürt [SAP ASCS/SCS-példánya egy Windows FELADATÁTVEVŐ FÜRTön ANF SMB-fájlmegosztás használatával](./high-availability-guide-windows-netapp-files-smb.md)című részt.

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Magas rendelkezésre állású architektúra egy SAP ASCS/SCS-példányhoz Linux rendszeren

> ![Linux-embléma.][Logo_Linux] Linux
> 
> Az SAP ASCS/SCS-példány SLES-keretrendszerrel való fürtözésével kapcsolatos további információkért lásd: [magas rendelkezésre állás az SAP NetWeaver Azure-beli virtuális gépeken az SAP-alkalmazások SUSE Linux Enterprise Server][sap-suse-ascs-ha]. A magas rendelkezésre állású NFS-SLES esetében, amely nem igényel magas rendelkezésre állású NFS-t, az SAP [NetWeaver SUSE Linux Enterprise Server Azure NETAPP Files SAP-alkalmazásokhoz című témakör magas rendelkezésre állású útmutatója][sap-suse-ascs-ha-anf].

Az SAP ASCS/SCS-példány Red Hat cluster Framework használatával történő fürtözésével kapcsolatos további információkért lásd: [Azure Virtual Machines magas rendelkezésre állás az SAP NetWeaver számára a Red Hat Enterprise Linux](./high-availability-guide-rhel.md)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver multi-SID konfiguráció egy fürtözött SAP ASCS/SCS-példányhoz

> ![Windows-embléma.][Logo_Windows] Windows
> 
> A többszörös SID a WSFC és a megosztott lemez használatával támogatott.
> 
> A multi-SID magas rendelkezésre állású architektúrával kapcsolatos további információkért tekintse meg a következőt:

* [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatás és a fájlmegosztás számára][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatás és a megosztott lemez számára][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux-embléma.][Logo_Linux] Linux
> 
> A többszörös SID-fürtszolgáltatás támogatott a Linux pacemaker-fürtökön az SAP ASCS/ERS számára, amely ugyanazon a fürtön **öt** SAP-SID-re korlátozódik.
> A több SID magas rendelkezésre állású architektúrával kapcsolatos további információkért lásd:

* [HA SAP NW Azure-beli virtuális gépeken, SLES for SAP Applications multi-SID útmutató](./high-availability-guide-suse-multi-sid.md)
* [HA SAP NW Azure-beli virtuális gépeken, RHEL for SAP Applications multi-SID útmutató](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>Magas rendelkezésre állású adatbázis-kezelő példány

Az adatbázis-kezelő az SAP-rendszer egyetlen kapcsolódási pontja is. Magas rendelkezésre állású megoldás használatával kell védelemmel ellátnia. Az alábbi ábra egy SQL Server AlwaysOn magas rendelkezésre állású megoldását mutatja be az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatással és az Azure belső Load balancerrel. SQL Server a AlwaysOn a saját adatbázis-replikációs szolgáltatásával replikálja az adatbázis-kezelői és-naplófájlokat. Ebben az esetben nincs szükség a fürt megosztott lemezére, ami leegyszerűsíti a teljes telepítést.

![3. ábra: példa egy magas rendelkezésre állású SAP adatbázis-kezelőre, SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**3. ábra:** Magas rendelkezésre állású SAP adatbázis-kezelő – példa SQL Server AlwaysOn_

További információ az Azure-beli SQL Server adatbázis-kezelőről az Azure Resource Manager üzembe helyezési modell használatával:

* [AlwaysOn rendelkezésre állási csoport konfigurálása az Azure Virtual Machines szolgáltatásban manuálisan a Resource Manager használatával][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure-beli belső terheléselosztó konfigurálása egy alAlwaysOnon rendelkezésre állási csoport számára az Azure-ban][virtual-machines-windows-portal-sql-alwayson-int-listener]

További információ az Azure-beli SAP HANA adatbázis-kezelőről az Azure Resource Manager üzembe helyezési modell használatával: az [Azure Virtual Machines (virtuális gépek) SAP HANA magas rendelkezésre állása][sap-hana-ha].
