---
title: SQL Server always on rendelkezésre állási csoport konfigurálása különböző régiók között
description: Ez a cikk azt ismerteti, hogyan konfigurálható a SQL Server always on rendelkezésre állási csoport az Azure-beli virtuális gépeken egy másik régióban lévő replikával.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 7ef3535158c99226da135ad3726266023ac0690f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102509404"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>SQL Server always on rendelkezésre állási csoport konfigurálása különböző Azure-régiók között

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan konfigurálható a SQL Server always on rendelkezésre állási csoport replikája az Azure-beli virtuális gépeken egy távoli Azure-helyen. Ezt a konfigurációt használhatja a vész-helyreállítás támogatásához.

Ez a cikk az Azure Virtual Machines Resource Manager módban használható.

Az alábbi képen egy rendelkezésre állási csoport általános telepítése látható az Azure Virtual Machines szolgáltatásban:

   ![Diagram, amely megjeleníti az Azure Load balancert és a rendelkezésre állási csoportot "Windows Server feladatátvevő fürt" és "always on rendelkezésre állási csoport" értékkel.](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

Ebben az üzembe helyezésben minden virtuális gép egy Azure-régióban található. A rendelkezésre állási csoport replikái szinkron véglegesítve lehetnek az SQL-1 és az SQL-2 automatikus feladatátvételével. Az architektúra létrehozásához tekintse meg a [rendelkezésre állási csoport sablonját vagy az oktatóanyagot](availability-group-overview.md).

Ez az architektúra a leállás esetén is sebezhető, ha az Azure-régió elérhetetlenné válik. A biztonsági rés megszüntetéséhez adjon hozzá egy replikát egy másik Azure-régióban. Az alábbi ábra az új architektúra megjelenését mutatja be:

   ![Rendelkezésre állási csoport – DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

Az előző ábrán egy SQL-3 nevű új virtuális gép látható. Az SQL-3 egy másik Azure-régióban található. Az SQL-3 megjelenik a Windows Server feladatátvevő fürthöz. Az SQL-3 képes a rendelkezésre állási csoport replikájának tárolására. Végezetül figyelje meg, hogy az SQL-3 Azure-régiója új Azure Load balancert tartalmaz.

>[!NOTE]
> Egy Azure-beli rendelkezésre állási csoportra akkor van szükség, ha több virtuális gép is ugyanabban a régióban van. Ha csak egy virtuális gép van a régióban, akkor a rendelkezésre állási csoport nem szükséges. A virtuális gépeket csak a rendelkezésre állási csoportba helyezheti a létrehozási időpontban. Ha a virtuális gép már rendelkezésre állási csoportba esik, később hozzáadhat egy virtuális gépet egy újabb replikához.

Ebben az architektúrában a távoli régióban található replika általában aszinkron véglegesíthető rendelkezésre állási móddal és manuális feladatátvételi móddal van konfigurálva.

Ha a rendelkezésre állási csoport replikái különböző Azure-régiókban található Azure-beli virtuális gépeken találhatók, minden régióhoz a következők szükségesek:

* Virtuális hálózati átjáró
* Virtuális hálózati átjáróval létesített kapcsolatok

Az alábbi ábra bemutatja, hogyan kommunikálnak a hálózatok az adatközpontok között.

   ![Diagram, amely a különböző Azure-régiókban található két virtuális hálózatot mutatja be, amelyek a V P N átjárók használatával kommunikálnak.](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Ez az architektúra a kimenő adatforgalmi díjat az Azure-régiók között replikált adatokra terheli. Lásd: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Távoli replika létrehozása

Ha egy távoli adatközpontban szeretne replikát létrehozni, hajtsa végre a következő lépéseket:

1. [Hozzon létre egy virtuális hálózatot az új régióban](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [VNet-VNet kapcsolatok konfigurálása a Azure Portal használatával](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Bizonyos esetekben előfordulhat, hogy a PowerShell használatával kell létrehoznia a VNet-VNet-kapcsolatokat. Ha például különböző Azure-fiókokat használ, akkor nem konfigurálhatja a kapcsolatokat a portálon. Ebben az esetben lásd: [VNet-VNet-kapcsolatok konfigurálása a Azure Portal használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Hozzon létre egy tartományvezérlőt az új régióban](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100).

   Ez a tartományvezérlő hitelesítést biztosít, ha az elsődleges hely tartományvezérlője nem érhető el.

1. [Hozzon létre egy SQL Server virtuális gépet az új régióban](create-sql-vm-portal.md).

1. [Hozzon létre egy Azure Load balancert a hálózatban az új régióban](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

   A terheléselosztó a következőket kell tennie:

   - Ugyanabban a hálózatban és alhálózatban kell lennie, mint az új virtuális gép.
   - Statikus IP-címmel kell rendelkeznie a rendelkezésre állási csoport figyelője számára.
   - Olyan háttér-készletet adjon meg, amely csak a terheléselosztó azonos régiójában lévő virtuális gépekből áll.
   - Használjon az IP-címhez tartozó, TCP-porthoz tartozó mintavételt.
   - Az azonos régióban lévő SQL Serverhoz tartozó terheléselosztási szabályt kell megegyeznie.  
   - Standard Load Balancer, ha a háttér-készletben lévő virtuális gépek nem részei egyetlen rendelkezésre állási csoportnak vagy virtuálisgép-méretezési csoportnak sem. További információkért tekintse át a [Azure Load Balancer standard áttekintést](../../../load-balancer/load-balancer-overview.md).
   - Standard Load Balancer, ha a két különböző régióban található két virtuális hálózat a globális VNet-társítással van összefoglalva. További információ: [Azure Virtual Network gyakori kérdések (GYIK)](../../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

1. [Adja hozzá a feladatátvételi fürtszolgáltatást az új SQL Serverhoz](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Csatlakoztassa az új SQL Servert a tartományhoz](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [Az új SQL Server szolgáltatásfiók beállítása tartományi fiók használatára](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [Adja hozzá az új SQL Server a Windows Server feladatátvevő fürthöz](availability-group-manually-configure-tutorial.md#addNode).

1. Adjon hozzá egy IP-cím erőforrást a fürthöz.

   Az IP-cím erőforrást Feladatátvevőfürt-kezelőban is létrehozhatja. Válassza ki a fürt nevét, majd kattintson a jobb gombbal a fürt nevére a fürt **alapvető erőforrásai** területen, és válassza a **Tulajdonságok** elemet: 

   ![Képernyőfelvétel: a "Feladatátvevőfürt-kezelő" a fürt nevével, a "kiszolgáló neve" és a "Properties" beállítással.](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   A **Tulajdonságok** párbeszédpanelen válassza a **Hozzáadás** az **IP-cím** alatt lehetőséget, majd adja hozzá a fürt nevének IP-címét a távoli hálózati régióból. Válassza az **OK** lehetőséget az **IP-cím** párbeszédpanelen, majd kattintson ismét az **OK gombra** a **fürt tulajdonságai** párbeszédpanelen az új IP-cím mentéséhez. 

   ![Fürt IP-címének hozzáadása](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Adja hozzá az IP-címet az alapszintű fürt nevéhez.

   Nyissa meg a fürt tulajdonságait még egyszer, és válassza a **függőségek** lapot. A két IP-cím vagy a függőség beállítása: 

   ![Fürt tulajdonságai](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Adjon hozzá egy IP-cím erőforrást a fürt rendelkezésre állási csoport szerepköréhez. 

   Kattintson a jobb gombbal a rendelkezésre állási csoport szerepkörre a Feladatátvevőfürt-kezelőban, majd válassza az **erőforrás hozzáadása**, **További erőforrások**, majd az **IP-cím** lehetőséget.

   ![IP-cím létrehozása](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Konfigurálja ezt az IP-címet a következőképpen:

   - Használja a hálózatot a távoli adatközpontból.
   - Rendelje hozzá az IP-címet az új Azure Load Balancerhez. 

1. Adja hozzá az IP-cím erőforrást a figyelő ügyfél-hozzáférési pont (hálózatnév) fürtjének függőségéhez.

   Az alábbi képernyőfelvételen egy megfelelően konfigurált IP-címkészlet-erőforrás látható:

   ![Rendelkezésreállási csoport](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >A fürterőforrás-csoport mindkét IP-címet tartalmazza. Mindkét IP-cím a figyelő ügyfél-hozzáférési pontjának függőségei. Használja a **vagy** az operátort a fürt függőségi konfigurációjában.

1. [A fürt paramétereinek beállítása a PowerShellben](availability-group-manually-configure-tutorial.md#setparam).

   Futtassa a PowerShell-parancsfájlt az új régióban a terheléselosztó által konfigurált fürthálózat-névvel, IP-címmel és mintavételi porttal.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. A SQL Server Konfigurációkezelő új SQL Server [engedélyezze az Always On rendelkezésre állási csoportokat](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Nyissa meg a tűzfal portjait az új SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   A megnyitásához szükséges portszámok a környezettől függenek. Nyissa meg a tükrözési végpont és az Azure Load Balancer Health mintavétel portjait.


1. [Adjon hozzá egy replikát a rendelkezésre állási csoporthoz az új SQL Serveron](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   Egy távoli Azure-régióban található replika esetében állítsa be az aszinkron replikációhoz manuális feladatátvételsel.  

## <a name="set-connection-for-multiple-subnets"></a>Több alhálózat közötti kapcsolatok beállítása

A távoli adatközpontban lévő replika a rendelkezésre állási csoport része, de egy másik alhálózaton van. Ha ez a replika az elsődleges replika lesz, akkor előfordulhat, hogy az alkalmazás kapcsolatainak időtúllépése. Ez a viselkedés megegyezik a helyszíni rendelkezésre állási csoporttal a több alhálózaton üzemelő példányokban. Az ügyfélalkalmazások közötti kapcsolatok engedélyezéséhez frissítse az ügyfélkapcsolatot, vagy konfigurálja a névfeloldás gyorsítótárazását a fürt hálózatnév-erőforrásán.

Lehetőleg frissítse az ügyfélkapcsolati karakterláncokat a beállításhoz `MultiSubnetFailover=Yes` . Lásd: [Csatlakozás a MultiSubnetFailover](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0)-hoz.

Ha nem tudja módosítani a kapcsolatok karakterláncait, beállíthatja a névfeloldás gyorsítótárazását. Tekintse [meg az időtúllépési hibát, és nem tud csatlakozni egy SQL Server 2012 AlAlwaysOnon rendelkezésre állási csoport figyelőhöz egy több alhálózattal rendelkező környezetben](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Feladatátvétel a távoli régióba

Ha tesztelni szeretné a figyelőt a távoli régióval, feladatátvételt hajthat végre a távoli régióban. Amíg a replika aszinkron, a feladatátvétel sebezhető a lehetséges adatvesztéssel szemben. Az adatvesztés nélküli feladatátvételhez módosítsa a rendelkezésre állási módot szinkron értékre, és állítsa a feladatátvételi módot automatikusra. Ehhez a következő lépések szükségesek:

1. **Object Explorer** az elsődleges replikát futtató SQL Server példányához kapcsolódjon.
1. A **AlwaysOn rendelkezésre állási csoportok**, **rendelkezésre állási csoportok** területen kattintson a jobb gombbal a rendelkezésre állási csoportra, és válassza a **Tulajdonságok** lehetőséget.
1. Az **általános** lapon, a **rendelkezésre állási replikák** területen állítsa a Dr hely másodlagos replikáját **szinkron véglegesítő** rendelkezésre állási mód és **automatikus** feladatátvételi mód használatára.
1. Ha a magas rendelkezésre állás érdekében az elsődleges replikával azonos helyen található másodlagos replika, állítsa ezt a replikát **aszinkron véglegesítés** és **manuális** értékre.
1. Kattintson az OK gombra.
1. **Object Explorer** kattintson a jobb gombbal a rendelkezésre állási csoportra, majd válassza az **irányítópult megjelenítése** elemet.
1. Az irányítópulton ellenőrizze, hogy a DR helyen lévő replika szinkronizálva van-e.
1. A **Object Explorer** kattintson a jobb gombbal a rendelkezésre állási csoportra, és válassza a **feladatátvétel...** lehetőséget. SQL Server felügyeleti stúdiók megnyitnak egy varázslót, amely feladatátvételt SQL Server.  
1. Válassza a **tovább** lehetőséget, majd válassza ki a SQL Server példányt a Dr helyen. Válassza a **tovább** gombot.
1. Kapcsolódjon a DR-hely SQL Server-példányához, és kattintson a **tovább** gombra.
1. Az **Összefoglalás** lapon ellenőrizze a beállításokat, majd válassza a **Befejezés** lehetőséget.

A kapcsolat tesztelése után helyezze vissza az elsődleges replikát az elsődleges adatközpontba, és állítsa vissza a rendelkezésre állási módot a normál működési beállításokra. A következő táblázat a jelen dokumentumban ismertetett architektúra normál működési beállításait mutatja be:

| Hely | Kiszolgálópéldány | Szerepkör | Rendelkezésre állási mód | Feladatátvételi mód
| ----- | ----- | ----- | ----- | -----
| Elsődleges adatközpont | SQL-1 | Elsődleges | Szinkron | Automatikus
| Elsődleges adatközpont | SQL-2 | Másodlagos | Szinkron | Automatikus
| Másodlagos vagy távoli adatközpont | SQL-3 | Másodlagos | Aszinkron | Kézi


### <a name="more-information-about-planned-and-forced-manual-failover"></a>További információ a tervezett és kényszerített manuális feladatátvételről

További információkért tekintse át a következők témaköröket:

- [Rendelkezésre állási csoport tervezett manuális feladatátvételének végrehajtása (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [Egy rendelkezésre állási csoport kényszerített manuális feladatátvételének végrehajtása (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>Következő lépések

* [Always On rendelkezésre állási csoportok](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Azure Virtual Machines](../../../virtual-machines/index.yml)
* [Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Azure rendelkezésre állási készletek](../../../virtual-machines/availability.md)