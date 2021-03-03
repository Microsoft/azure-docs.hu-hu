---
title: SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és megosztott lemezen az Azure-beli SAP ASCS/SCS-példányhoz | Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SAP NetWeaver HA szolgáltatást egy Windows feladatátvevő fürtön, illetve egy SAP ASCS/SCS-példány megosztott lemezén.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12cfd39cf1358dcef79c3843627f0b45dc688c9e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667948"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és megosztott lemezen az Azure-beli SAP ASCS/SCS-példányhoz

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

Ez a cikk azt ismerteti, hogyan telepíthet és konfigurálhat egy magas rendelkezésre állású SAP-rendszert az Azure-ban egy olyan Windows Server feladatátvevő fürt és fürtözött megosztott lemez használatával, amely egy SAP ASCS/SCS-példány fürtözésére szolgál. A következő témakörben leírtak szerint [: az architektúra-útmutatóban szereplő SAP ASCS/SCS-példányok fürt megosztott lemez használatával történő használata a Windows feladatátvevő fürtön][sap-high-availability-guide-wsfc-shared-disk]a *fürt megosztott lemezének* két alternatívája van:

- [Azure megosztott lemezek](../../disks-shared.md)
- A [SIOS DataKeeper cluster Edition](https://us.sios.com/products/datakeeper-cluster/) használatával tükrözött tárhely hozható létre, amely a fürtözött megosztott lemezt szimulálja. 

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át a következő dokumentumokat:

* [Architektúra-útmutató: SAP ASCS/SCS-példány fürtözése Windows feladatátvevő fürtön fürt megosztott lemezének használatával][sap-high-availability-guide-wsfc-shared-disk]

* [Készítse elő az Azure-infrastruktúrát az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-infrastructure-wsfc-shared-disk]

Ebben a cikkben nem írja le az adatbázis-kezelői konfigurációt, mert a beállítások a használt adatbázis-kezelő rendszertől függően változnak. Feltételezzük, hogy az adatbázis-kezelők magas rendelkezésre állással kapcsolatos problémái az Azure-t használó különböző adatbázis-kezelők által támogatott funkciókkal foglalkoznak. Ilyenek például a AlwaysOn vagy az adatbázis-tükrözés a SQL Server és az Oracle-alapú adatvédelemhez az Oracle-adatbázisokhoz. Ebben a cikkben nem szerepelnek a magas rendelkezésre állást biztosító adatkezelői forgatókönyvek.

Nincsenek különleges megfontolások, ha a különböző adatbázis-kezelő szolgáltatások egy fürtözött SAP-ASCS vagy SCS-konfigurációval működnek az Azure-ban.

> [!NOTE]
> Az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek telepítési eljárásai csaknem azonosak. A legfontosabb különbség az, hogy egy SAP ABAP rendszernek van egy ASCS-példánya. Az SAP Java-rendszeren egy SCS-példány van. Az SAP ABAP + Java rendszer egy ASCS-példánnyal és egy olyan SCS-példánnyal rendelkezik, amely ugyanabban a Microsoft feladatátvételi fürtben fut. A rendszer explicit módon megemlíti az egyes SAP NetWeaver telepítési verem telepítési különbségeit. Tegyük fel, hogy a többi lépés ugyanaz.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Az SAP telepítése magas rendelkezésre állású ASCS/SCS-példánnyal

> [!IMPORTANT]
> Ha a SIOS-t használja a megosztott lemez bemutatására, ne helyezze a lapozófájlt a SIOS DataKeeper tükrözött kötetekre. A lapozófájlt egy Azure-beli virtuális gép ideiglenes meghajtójába helyezheti el, amely az alapértelmezett. Ha még nem tette meg, helyezze át a Windows-lapozófájlt az Azure-beli virtuális gép D meghajtóján.  

A magas rendelkezésre állású ASCS/SCS-példánnyal rendelkező SAP telepítése a következő feladatokat foglalja magában:

* Hozzon létre egy virtuális állomásnevet a fürtözött SAP ASCS/SCS-példányhoz.
* Telepítse az SAP-t az első fürtcsomóponton.
* Módosítsa az ASCS/SCS-példány SAP-profilját.
* Vegyen fel egy mintavételi portot.
* Nyissa meg a Windows tűzfal mintavételi portját.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz

1. A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomásnevét.

   > [!IMPORTANT]
   > A ASCS/SCS-példány virtuális állomásnevét hozzárendelt IP-címnek meg kell egyeznie a Azure Load Balancerhoz rendelt IP-címmel.  

   ![1. ábra: az SAP ASCS/SCS-fürt virtuális neve és a TCP/IP-cím DNS-bejegyzésének megadása][sap-ha-guide-figure-3046]

   _Adja meg a DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális neve és TCP/IP-címe számára._

2. Ha az új SAP sorba helyezni Replication Server 2-et használja, amely fürtözött példány is, akkor le kell foglalni a DNS virtuális ERS2 is. 

   > [!IMPORTANT]
   > A ERS2-példány virtuális állomásnevét hozzárendelt IP-címnek a másodiknak kell lennie a Azure Load Balancerhoz rendelt IP-címnek.    

   ![1A. ábra: az SAP ASCS/SCS-fürt virtuális neve és a TCP/IP-cím DNS-bejegyzésének megadása][sap-ha-guide-figure-3046-ers2]

   _Adja meg az SAP ERS2-fürt virtuális neve és a TCP/IP-cím DNS-bejegyzését_

3. A virtuális gazdagép neveként hozzárendelt IP-cím megadásához válassza a **DNS-kezelő**  >  **tartomány** lehetőséget.

   ![2. ábra: új virtuális név és TCP/IP-cím az SAP ASCS/SCS-fürt konfigurációjához][sap-ha-guide-figure-3047]

   _Új virtuális név és TCP/IP-cím az SAP ASCS/SCS-fürt konfigurációjához_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Az SAP első fürtcsomópont telepítése

1. Hajtsa végre az A fürt csomópontjának első csomópontját. Válassza a következőt:

   * **ABAP-System**: **ASCS** -példány száma **00**
   * **Java-System**: **SCS** -példány száma **01**
   * **ABAP + Java System**: **ASCS** -példány száma **00** és **SCS** -példány száma **01**

   > [!IMPORTANT]
   > Ne feledje, hogy az Azure belső terheléselosztó terheléselosztási szabályaiban (alapszintű SKU használata esetén) a konfigurációnak meg kell egyeznie a kiválasztott SAP-példányok számával.

2. Kövesse az SAP által ismertetett telepítési eljárást. Győződjön meg arról, hogy a "első fürtcsomópont" telepítésének megkezdése beállításnál a "fürt megosztott lemeze" lehetőséget választja.

> [!TIP]
> Az SAP telepítési dokumentációja leírja, hogyan telepítheti az első ASCS/SCS fürtcsomópont-csomópontot.

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Az ASCS/SCS-példány SAP-profiljának módosítása

Ha az 1. sorba helyezni replikációs kiszolgálóval rendelkezik, `enque/encni/set_so_keepalive` az alább leírtak szerint adja hozzá az SAP-profil paramétert. A profil paraméter megakadályozza a kapcsolódást az SAP-munkafolyamatok és a sorba helyezni-kiszolgáló között, ha túl sokáig tétlenek. Az SAP-paraméter nem szükséges a ERS2. 

1. Adja hozzá ezt a profil paramétert az SAP ASCS/SCS instance profilhoz, ha az ERS1-t használja.

   ```
   enque/encni/set_so_keepalive = true
   ```

   A ERS1 és a ERS2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.   

2. Az SAP-profil paramétereinek alkalmazásához indítsa újra az SAP ASCS/SCS-példányt.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Mintavételi Port hozzáadása

A belső terheléselosztó mintavételi funkciójának használatával biztosíthatja, hogy a teljes fürtkonfiguráció működjön a Azure Load Balancer. Az Azure belső terheléselosztó általában a bejövő munkaterheléseket a részt vevő virtuális gépek között egyenlően osztja el.

Ez azonban nem fog működni bizonyos fürtkonfiguráció esetén, mert csak egy példány aktív. A másik példány passzív, és nem fogadja el a számítási feladatok egyikét sem. A mintavételi funkciók segítségével az Azure belső terheléselosztó felismeri, hogy melyik példány aktív, és csak az aktív példányt célozza meg.  

> [!IMPORTANT]
> Ebben a példában a **ProbePort** a 620 **Nr** értékre van állítva. A **00** -as számú SAP ASCS-példány esetében 620 **00**. A konfigurációt úgy kell beállítani, hogy az megfeleljen az SAP-példányok számának és az SAP SID-nek.

Mintavételi port hozzáadásához futtassa ezt a PowerShell-modult az egyik fürtözött virtuális gépen:

- SAP ASC/SCS-példány esetén 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- ERS2 használata esetén, amely fürtözött. Nem kell konfigurálni a mintavételi portot a ERS1, mert nem fürtözött.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 A függvény kódja a következőhöz `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` hasonlóan fog kinézni:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> A Windows tűzfal mintavételi portjának megnyitása

Nyisson meg egy Windows tűzfal mintavételi portot mindkét fürtcsomópontokon. A következő parancsfájl használatával nyisson meg egy Windows tűzfal mintavételi portot. Frissítse a környezet PowerShell-változóit.  
Ha a ERS2-t használja, meg kell nyitnia a ERS2 mintavételi portjának tűzfal-portját is.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Az adatbázis-példány telepítése

Az adatbázis-példány telepítéséhez kövesse az SAP telepítési dokumentációjában leírt eljárást.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> A második fürtcsomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatójában ismertetett lépéseket.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Az SAP Primary Application Server telepítése

Telepítse az elsődleges Application Server-(PAS-) példányt a \<SID\> -di-0-ra azon a virtuális gépen, amelyet a Pas számára jelölt ki. Nincsenek függőségek az Azure-ban. A SIOS használata esetén nincsenek DataKeeper-specifikus beállítások.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Az SAP további alkalmazáskiszolgáló telepítése

Telepítsen egy SAP további alkalmazáskiszolgáló (AAS) szolgáltatást minden olyan virtuális gépre, amelyet az SAP Application Server-példány üzemeltetésére jelölt ki. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Az SAP-ASCS/SCS-példány feladatátvételének tesztelése

A vázolt feladatátvételi tesztek esetében feltételezzük, hogy az SAP ASCS aktív az A csomóponton.  

1. Ellenőrizze, hogy az SAP-rendszer képes-e az A csomópontról a B csomópontra való feladatátvételre, és válassza ki az egyik lehetőséget, hogy elindítsa az SAP-fürtcsomópont feladatátvételét a (z) " \<SID\> a" fürt "b" csomópontjára:
    - Feladatátvevőfürt-kezelő  
    - Feladatátvevő fürt PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Indítsa újra az A fürtcsomópont-csomópontot a Windows vendég operációs rendszeren. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> -fürtből az a csomópontról a B csomópontra.  
3. Indítsa újra az A fürtcsomópont-csomópontot a Azure Portal. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> -fürtből az a csomópontról a B csomópontra.  
4. Indítsa újra az A fürtcsomópont Azure PowerShell használatával. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> -fürtből az a csomópontról a B csomópontra.

5. Ellenőrzés
   - A feladatátvételt követően ellenőrizze, hogy az SAP- \<SID\> fürt a B fürtcsomóponton fut-e. 

      ![8. ábra: a Feladatátvevőfürt-kezelő az SAP \< SID- \> fürt csoport a B csomóponton fut.][sap-ha-guide-figure-5002]

      _Feladatátvevőfürt-kezelő az SAP- \<SID\> fürt a B fürtcsomóponton fut._

   - A feladatátvételt követően ellenőrizze, hogy a megosztott lemez most már csatlakoztatva van-e a B fürtcsomóponton. 
   - A feladatátvételt követően, ha a SIOS-t használja, ellenőrizze, hogy a SIOS DataKeeper replikálja-e a B csomóponton lévő forrás kötet meghajtójának adatait a (z) "A" fürtcsomóponton. 

      ![9. ábra: a SIOS DataKeeper a B csomópontról replikálja a helyi kötetet a "A" fürt "A" csomópontjára][sap-ha-guide-figure-5003]

      _A SIOS DataKeeper replikálja a B fürtcsomópont helyi kötetét az A fürt csomópontjának_