---
title: Azure-infrastruktúra SAP ASCS/SCS és WSFC&megosztott lemezrel | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-infrastruktúrát az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS-példány megosztott lemezének használatával.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673621"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Az Azure-infrastruktúra előkészítése az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS közös lemezének használatával

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állási konfiguráció)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows operációs rendszer][Logo_Windows] Windows


Ez a cikk azokat a lépéseket ismerteti, amelyekkel előkészítheti az Azure-infrastruktúrát a magas rendelkezésre állású SAP-ASCS/SCS-példányok Windows feladatátvevő fürtön való telepítésére és konfigurálására egy olyan *fürt megosztott lemezének* használatával, amely az SAP ASCS-példányok fürtözését teszi elérhetővé.
A *fürt megosztott lemezének* két alternatívája jelenik meg a dokumentációban:

- [Azure megosztott lemezek](../../disks-shared.md)
- A [SIOS DataKeeper cluster Edition](https://us.sios.com/products/datakeeper-cluster/) használatával tükrözött tárhely hozható létre, amely a fürtözött megosztott lemezt szimulálja. 

A bemutatott konfiguráció az [Azure Proximity-elhelyezési csoportjaira (PPG)](./sap-proximity-placement-scenarios.md) támaszkodik az SAP-munkaterhelések optimális hálózati késése érdekében. A dokumentáció nem fedi le az adatbázis rétegét.  

> [!NOTE]
> Az Azure-beli Proximity-elhelyezési csoportok az Azure Shared Disk használatának előfeltételei.
 

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át a következő cikket:

* [Architektúra-útmutató: SAP ASCS/SCS-példány fürtözése Windows feladatátvevő fürtön fürt megosztott lemezének használatával][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>A ASCS virtuális gépek létrehozása

Az SAP ASCS/SCS-fürt esetén két virtuális gépet helyezzen üzembe az Azure rendelkezésre állási készletében. Telepítse a virtuális gépeket ugyanabba a közelségi elhelyezési csoportba. A virtuális gépek üzembe helyezését követően:  
- Azure belső Load Balancer létrehozása SAP ASCS/SCS-példányhoz 
- Windows rendszerű virtuális gépek hozzáadása az AD-tartományhoz

A megjelenített forgatókönyv állomásneve és IP-címe a következő:

| Állomásnév szerepkör | Állomásnév | Statikus IP-cím | Rendelkezésre állási csoport | Proximity elhelyezési csoport |
| --- | --- | --- |---| ---|
| első fürtcsomópont ASCS/SCS-fürt |PR1-ASCs-10 |10.0.0.4 |PR1-ASCs-avset |PR1PPG |
| második fürtcsomópont ASCS/SCS-fürt |PR1-ASCs-11 |10.0.0.5 |PR1-ASCs-avset |PR1PPG |
| Fürt hálózatnév | pr1clust |10.0.0.42 (**csak** a Win 2016-fürt esetében) | n.a. | n.a. |
| ASCS-fürt hálózatnév | PR1 – ascscl |10.0.0.43 | n.a. | n.a. |
| ERS-fürt hálózatának neve (**csak** ERS2 esetében) | PR1 – erscl |10.0.0.44 | n.a. | n.a. |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure belső terheléselosztó létrehozása

Az SAP ASCS, az SAP SCS és az új SAP-ERS2 a virtuális állomásnév és a virtuális IP-címek használata. Az Azure-ban a virtuális IP-címek használatához [Load Balancer](../../../load-balancer/load-balancer-overview.md) szükséges. Javasoljuk, hogy használja a [standard Load balancert](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.    


Az alábbi lista az (A) SCS/ERS Load Balancer konfigurációját mutatja be. Az SAP-ASCS és a ERS2 konfigurációja is ugyanabban az Azure Load balancerben történik.  

**Egy SCS**
- Előtér-konfiguráció
    - Statikus ASCS/SCS IP- **10.0.0.43**
- Háttér-konfiguráció  
    Adja hozzá az összes olyan virtuális gépet, amely az (A) SCS/ERS fürt részét képezi. Ebben a példában a virtuális gépek **PR1-ASCs-10** és **PR1-ASCs-11**.
- Mintavételi port
    - Az 620 **-** es port a protokoll (TCP), az intervallum (5), a nem Kifogástalan állapot küszöbértéke (2) alapértelmezett beállítását hagyja meg.
- Terheléselosztási szabályok
    - Ha standard Load Balancer használ, válassza a hektár portok elemet.
    - Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
        - 32 **Nr** TCP
        - 36 **Nr** TCP
        - 39 **Nr** TCP
        - 81 **Nr** TCP
        - 5 **Nr** 13 TCP
        - 5 **Nr** 14 TCP
        - 5 **Nr** 16 TCP

    - Győződjön meg arról, hogy az Üresjárati időkorlát (perc) a 30 értékre van beállítva, és hogy a lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása) engedélyezve van.

**ERS2**

Ahogy a sorba helyezni Replication Server 2 (ERS2) is fürtözött, a ERS2 virtuális IP-címet is konfigurálni kell az Azure ILB az SAP ASCS/SCS IP felett. Ez a szakasz csak akkor érvényes, ha az sorba helyezni Replication Server 2 architektúrát használja.  
- második frontend-konfiguráció
    - Statikus SAP-ERS2 IP- **10.0.0.44**

- Háttér-konfiguráció  
  A virtuális gépek már hozzá lettek adva a ILB backend-készlethez.  

- második mintavételi port
    - Port 621 **Nr**  
    Hagyja meg az alapértelmezett beállítást a protokoll (TCP), az intervallum (5), a nem Kifogástalan állapot küszöbértéke (2) beállításnál.

- 2. terheléselosztási szabályok
    - Ha standard Load Balancer használ, válassza a hektár portok elemet.
    - Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
        - 32 **Nr** TCP
        - 33 **Nr** TCP
        - 5 **Nr** 13 TCP
        - 5 **Nr** 14 TCP
        - 5 **Nr** 16 TCP

    - Győződjön meg arról, hogy az Üresjárati időkorlát (perc) a 30 értékre van beállítva, és hogy a lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása) engedélyezve van.


> [!TIP]
> Azure Resource Manager az [Azure Shared disktel rendelkező SAP ASCS/SCS-példányhoz készült WSFC-sablonnal](https://github.com/robotechredmond/301-shared-disk-sap)automatizálható az infrastruktúra-előkészítés, amely az Azure Shared Disk használatával egy ERS1-vel rendelkező SAP SID-hez használható.  
> Az Azure ARM-sablon két Windows 2019 vagy 2016 virtuális gépet hoz létre, hozzon létre Azure-beli megosztott lemezt, és csatolja őket a virtuális gépekhez. Az Azure belső Load Balancer létrehozása és konfigurálása is megtörténik. Részletekért tekintse meg az ARM-sablont. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Beállításjegyzék-bejegyzések hozzáadása a ASCS/SCS-példányhoz tartozó fürtcsomópontokon

Azure Load Balancer a kapcsolatok lezárva lehetnek, ha a kapcsolatok egy adott időszakban tétlenek, és túllépik az üresjárati időkorlátot. Az SAP-munkafolyamatok úgy dolgozzák fel a nyitott kapcsolatokat az SAP sorba helyezni folyamatba, hogy az első sorba helyezni/deüzenetsor-kérelmet el kell juttatni. A kapcsolatok megszakításának elkerüléséhez módosítsa a TCP/IP-KeepAliveTime és a KeepAliveInterval értékeket mindkét fürtcsomóponton. A ERS1 használata esetén az SAP-profil paramétereinek hozzáadására is szükség van, a cikk későbbi részében leírtak szerint.
A következő beállításjegyzékbeli bejegyzéseket mindkét fürtcsomóponton módosítani kell:

- KeepAliveTime
- KeepAliveInterval

| Elérési út| Változó neve | Változó típusa  | Érték | Dokumentáció |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (decimális) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (decimális) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


A módosítások alkalmazásához indítsa újra a fürtcsomópontok csomópontját.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows rendszerű virtuális gépek hozzáadása a tartományhoz
Miután statikus IP-címeket rendelt a virtuális gépekhez, adja hozzá a virtuális gépeket a tartományhoz. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Windows feladatátvevő fürt telepítése és konfigurálása 

### <a name="install-the-windows-failover-cluster-feature"></a>A Windows feladatátvételi fürtszolgáltatás telepítése

Futtassa ezt a parancsot a fürtcsomópontok egyikén:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

A szolgáltatás telepítésének befejezése után indítsa újra a fürtcsomópontok csomópontot.  

### <a name="test-and-configure-windows-failover-cluster"></a>Windows feladatátvevő fürt tesztelése és konfigurálása 

Windows 2019 rendszeren a fürt automatikusan felismeri, hogy az Azure-ban fut, és a fürt felügyeleti IP-címének alapértelmezett beállításaként az elosztott hálózat nevét fogja használni. Ezért a fürt csomópontjainak helyi IP-címeit fogja használni. Ennek eredményeképpen nincs szükség dedikált (virtuális) hálózatnév használatára a fürthöz, és nem szükséges konfigurálni ezt az IP-címet az Azure belső Load Balancerján.

További információ: [Windows Server 2019 feladatátvételi fürtszolgáltatás új szolgáltatások](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) futtassa ezt a parancsot a fürtcsomópontok egyik csomópontján:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>A fürt Felhőbeli Kvórumának konfigurálása
A Windows Server 2016-es vagy a 2019-es használatakor javasoljuk, hogy konfigurálja az [Azure Cloud tanúsító](/windows-server/failover-clustering/deploy-cloud-witness)a fürt Kvórumának megfelelően.

Futtassa ezt a parancsot a fürtcsomópontok egyikén:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>A Windows feladatátvevő fürt küszöbértékének finomhangolása
 
Miután sikeresen telepítette a Windows feladatátvevő fürtöt, néhány küszöbértéket módosítania kell, hogy az megfeleljen az Azure-ban üzembe helyezett fürtöknek. A módosítandó paraméterek leírása a [feladatátvevő fürt hálózati küszöbértékének finomhangolása](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)című dokumentumban található. Feltételezve, hogy a ASCS/SCS-hez készült Windows-fürtöt alkotó két virtuális gép ugyanabban az alhálózatban található, módosítsa a következő paramétereket az értékekre:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Ezek a beállítások az ügyfelekkel lettek tesztelve, és jó kompromisszumot biztosítanak. Elég rugalmasak, de olyan feladatátvételt is biztosítanak, amely elég gyors az SAP-munkaterhelések vagy a virtuális gépek meghibásodása esetén.  

## <a name="configure-azure-shared-disk"></a>Az Azure megosztott lemez konfigurálása
Ez a szakasz csak akkor alkalmazható, ha Azure-beli megosztott lemezt használ. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Azure-beli megosztott lemez létrehozása és csatlakoztatása a PowerShell-lel
Futtassa ezt a parancsot a fürtcsomópontok egyikén. Módosítania kell az erőforráscsoport, az Azure-régió, a SAPSID és egyebek értékét.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>A megosztott lemez formázása a PowerShell-lel
1. A lemez számának beolvasása. Futtassa ezeket a PowerShell-parancsokat az egyik fürtcsomóponton:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formázza a lemezt. Ebben a példában a lemez 2-es számú. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Ellenőrizze, hogy a lemez most már fürtözött lemezként jelenik-e meg.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Regisztrálja a lemezt a fürtben.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SIOS DataKeeper-fürt kiadása az SAP ASCS/SCS-fürt megosztott lemeze számára
Ez a szakasz csak akkor alkalmazható, ha a külső gyártótól származó szoftvert SIOS DataKeeper-fürt kiadásával hoz létre egy tükrözött tárolót, amely szimulálja a fürt megosztott lemezét.  

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatási konfigurációval az Azure-ban. Az SAP ASCS/SCS példány telepítéséhez megosztott lemezes erőforrásra van szükség. Az egyik lehetőség, hogy a SIOS DataKeeper cluster Edition használata egy külső gyártótól származó megoldás, amellyel megosztott lemezes erőforrásokat hozhat létre.  

A SIOS DataKeeper-fürt kiadásának az SAP ASCS/SCS-fürt megosztott lemezre való telepítése a következő feladatokat foglalja magában:
- Szükség esetén vegyen fel Microsoft .NET keretrendszert. Lásd: [SIOS dokumentációja] (( https://us.sios.com/products/datakeeper-cluster/) a legfrissebb .NET-keretrendszerre vonatkozó követelményekért 
- A SIOS DataKeeper telepítése
- SIOS-DataKeeper konfigurálása

### <a name="install-sios-datakeeper"></a>A SIOS DataKeeper telepítése
Telepítse a SIOS DataKeeper-fürt kiadását a fürt mindegyik csomópontján. Ha virtuális megosztott tárolót szeretne létrehozni a SIOS DataKeeper, hozzon létre egy szinkronizált tükröt, majd szimulálja a fürt megosztott tárolóját.

A SIOS szoftver telepítése előtt hozza létre a DataKeeperSvc tartományi felhasználót.

> [!NOTE]
> Adja hozzá a DataKeeperSvc tartományi felhasználót a helyi rendszergazda csoporthoz mindkét fürtcsomóponton.
>

1. Telepítse a SIOS szoftvert mindkét fürtcsomóponton.

   ![SIOS-telepítő][sap-ha-guide-figure-3030]

   ![31. ábra: a SIOS DataKeeper-telepítésének első lapja][sap-ha-guide-figure-3031]

   _A SIOS-DataKeeper telepítésének első lapja_

2. A párbeszédpanelen válassza az **Igen** lehetőséget.

   ![32. ábra: a DataKeeper értesíti, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

   _A DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3. Javasoljuk, hogy a párbeszédpanelen válassza a **tartomány vagy a kiszolgáló fiók** lehetőséget.

   ![33. ábra: felhasználó kiválasztása a SIOS DataKeeper][sap-ha-guide-figure-3033]

   _Felhasználó kiválasztása a SIOS DataKeeper_

4. Adja meg a SIOS DataKeeper létrehozott tartományi fiók felhasználónevét és jelszavát.

   ![34. ábra: adja meg a SIOS-DataKeeper telepítésének tartományhoz tartozó felhasználónevét és jelszavát][sap-ha-guide-figure-3034]

   _Adja meg a SIOS-DataKeeper telepítésének tartományhoz tartozó felhasználónevét és jelszavát_

5. Telepítse a SIOS DataKeeper-példányának licenckulcs értékét az 35. ábrán látható módon.

   ![35. ábra: a SIOS DataKeeper-licenckulcs megadása][sap-ha-guide-figure-3035]

   _Adja meg a SIOS DataKeeper-licenckulcs_

6. Ha a rendszer kéri, indítsa újra a virtuális gépet.

### <a name="configure-sios-datakeeper"></a>SIOS-DataKeeper konfigurálása
Miután mindkét csomópontra telepítette a SIOS DataKeeper, indítsa el a konfigurációt. A konfiguráció célja, hogy szinkron adatreplikációt lehessen végrehajtani az egyes virtuális gépekhez csatolt további lemezek között.

1. Indítsa el a DataKeeper-kezelő és a konfigurációs eszközt, majd válassza a **kiszolgáló összekapcsolását**.

   ![36. ábra: SIOS DataKeeper-kezelő és-konfigurációs eszköz][sap-ha-guide-figure-3036]

   _SIOS DataKeeper-kezelő és-konfigurációs eszköz_

2. Adja meg az első csomópont nevét vagy TCP/IP-címét, amelyhez a felügyelet és a konfigurációs eszköz csatlakozni kíván, a második lépésben pedig a második csomópontot.

   ![37. ábra: az első csomópont nevének vagy TCP/IP-címének beszúrása a felügyeleti és konfigurációs eszköznek csatlakoznia kell a kiszolgálóhoz, a második lépésben pedig a második csomópontot.][sap-ha-guide-figure-3037]

   _Szúrja be az első csomópont nevét vagy TCP/IP-címét a felügyeleti és konfigurációs eszközhöz, és a második lépésben a második csomópontot._

3. Hozza létre a replikációs feladatot a két csomópont között.

   ![38. ábra: replikációs feladatok létrehozása][sap-ha-guide-figure-3038]

   _Replikációs feladatok létrehozása_

   A varázsló végigvezeti a replikációs feladatok létrehozásának folyamatán.

4. Adja meg a replikációs feladatoknak a nevét.

   ![39. ábra: a replikációs feladatok nevének megadása][sap-ha-guide-figure-3039]

   _A replikációs feladatok nevének megadása_

   ![40. ábra: a csomópont alapadatának megadása, amelynek az aktuális forrás-csomópontnak kell lennie.][sap-ha-guide-figure-3040]

   _Adja meg a csomópont alapadatait, amelyeknek az aktuális forrás-csomópontnak kell lennie._

5. Adja meg a cél csomópont nevét, TCP/IP-címét és a lemez kötetét.

   ![41. ábra: az aktuális cél csomópont nevének, TCP/IP-címének és lemezének megadása][sap-ha-guide-figure-3041]

   _Adja meg az aktuális cél csomópont nevét, TCP/IP-címét és a lemez kötetét._

6. Adja meg a tömörítési algoritmusokat. A példánkban azt javasoljuk, hogy tömörítse a replikálási adatfolyamot. Különösen újraszinkronizálási helyzetekben a replikálási adatfolyam tömörítése jelentősen csökkenti az újraszinkronizálás idejét. A tömörítés a virtuális gép processzor-és RAM-erőforrásait használja. A tömörítési sebesség növekszik, így a felhasznált CPU-erőforrások mennyisége. Ezt a beállítást később módosíthatja.

7. Egy másik beállításnak ellenőriznie kell, hogy a replikáció aszinkron módon vagy szinkronban történjen-e. Az SAP ASCS/SCS-konfigurációk védetté tételéhez szinkron replikálást kell használni.  

   ![42. ábra: a replikáció részleteinek meghatározása][sap-ha-guide-figure-3042]

   _Replikáció részleteinek megadása_

8. Annak megadása, hogy a replikálási feladatokkal replikált kötetet egy Windows Server feladatátvevő fürt konfigurációjának kell-e képviselnie megosztott lemezként. Az SAP ASCS/SCS konfiguráció esetében válassza az **Igen** lehetőséget, hogy a Windows-fürt a replikált kötetet megosztott lemezként látja, amelyet fürtözött kötetként használhat.

   ![43. ábra: válassza az Igen lehetőséget a replikált kötet fürtözött kötetként való beállításához.][sap-ha-guide-figure-3043]

   _Válassza az **Igen** lehetőséget a replikált kötet fürtözött kötetként való beállításához_

   A kötet létrehozása után a DataKeeper-kezelés és-konfiguráció eszköz azt mutatja, hogy a replikációs feladatok aktívak.

   ![44. ábra: az SAP ASCS/SCS-megosztási lemez DataKeeper szinkron tükrözése aktív][sap-ha-guide-figure-3044]

   _Az SAP ASCS/SCS-megosztási lemez DataKeeper szinkron tükrözése aktív_

   Feladatátvevőfürt-kezelő most a lemezt DataKeeper lemezként jeleníti meg, az 45. ábrán látható módon:

   ![45. ábra: az Feladatátvevőfürt-kezelő a replikált DataKeeper lemezt mutatja][sap-ha-guide-figure-3045]

   _Feladatátvevőfürt-kezelő megjeleníti a replikált DataKeeper lemezt_


## <a name="next-steps"></a>Következő lépések

* [Telepítse az SAP NetWeaver HA szolgáltatást egy Windows feladatátvevő fürt és egy SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-installation-wsfc-shared-disk]