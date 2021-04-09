---
title: Virtuális gépek előkészítése a rendszerhez
description: Készítse elő az Azure-beli virtuális gépeket, hogy azok egy feladatátvevő fürt példányával (és az Azure Virtual Machines SQL Server használatával legyenek felhasználva.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 10f01fd5943928eda1f1e4518f30c8e3ccf56b46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737795"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Virtuális gépek előkészítése a (z) rendszerhez (SQL Server Azure-beli virtuális gépeken)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan lehet előkészíteni az Azure-beli virtuális gépeket (VM-EK) egy SQL Server feladatátvevő fürt-példánnyal (-vel) való használatra. A konfigurációs beállítások attól függően változnak, hogy milyen típusú tárolási megoldásra van szüksége, ezért ellenőrizze, hogy a megfelelő konfigurációt választotta-e a környezetéhez és a vállalatához. 

További információért lásd: az Azure-beli [virtuális gépekkel](failover-cluster-instance-overview.md) és a [fürttel kapcsolatos ajánlott eljárások](hadr-cluster-best-practices.md)a SQL Server-es és újabb változatának áttekintése. 

## <a name="prerequisites"></a>Előfeltételek 

- Egy Microsoft Azure-előfizetés. Az első lépések [ingyenesek](https://azure.microsoft.com/free/). 
- Egy Windows-tartomány az Azure Virtual Machines szolgáltatásban, vagy egy helyszíni adatközpont, amely az Azure-ra bővült a virtuális hálózat párosításával.
- Egy olyan fiók, amely rendelkezik objektumok létrehozásához szükséges engedélyekkel az Azure Virtual Machines szolgáltatásban és a Active Directoryban.
- Az alábbi összetevőkhöz elegendő IP-címmel rendelkező Azure-beli virtuális hálózat és alhálózat:
   - Mindkét virtuális gép
   - A Windows feladatátvevő fürt IP-címe
   - Egy IP-cím minden egyes
- DNS konfigurálva az Azure-hálózaton, amely a tartományvezérlőkre mutat.

## <a name="choose-an-fci-storage-option"></a>Válassza ki az egy%-os tárolási beállítást

A virtuális gép konfigurációs beállításai attól függően változnak, hogy milyen tárterületet kíván használni a SQL Server feladatátvevő fürt példányához. A virtuális gép előkészítése előtt tekintse át a [rendelkezésre álló](failover-cluster-instance-overview.md#storage) , és a környezet és az üzleti igényeknek leginkább megfelelő lehetőséget. Ezután gondosan válassza ki a megfelelő virtuálisgép-konfigurációs beállításokat a cikk során a tároló kiválasztása alapján. 

## <a name="configure-vm-availability"></a>VIRTUÁLIS gépek rendelkezésre állásának konfigurálása 

A feladatátvételi fürtszolgáltatás megköveteli, hogy a virtuális gépek egy [rendelkezésre állási csoportba](../../../virtual-machines/linux/tutorial-availability-sets.md) vagy egy [rendelkezésre állási zónába](../../../availability-zones/az-overview.md#availability-zones)kerüljenek. Ha a rendelkezésre állási csoportok lehetőséget választja, a virtuális gépek szorosabb megkereséséhez használhatja a [közelségi csoportokat](../../../virtual-machines/co-location.md#proximity-placement-groups) . Valójában a közelségi elhelyezési csoportok az Azure-beli megosztott lemezek használatának előfeltételei. 

Körültekintően válassza ki a virtuális gép rendelkezésre állási beállítását, amely megfelel a kívánt fürtkonfiguráció: 

- **Azure megosztott lemezek**: a rendelkezésre állási lehetőség akkor változik, ha prémium SSD-ket vagy UltraDisk használ:
   - Prémium SSD: a [rendelkezésre állási](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) csoport a prémium SSD-k különböző meghibásodási/frissítési tartományokban található, a [közelségi elhelyezési csoportokba](../../../virtual-machines/windows/proximity-placement-groups-portal.md)helyezve.
   - Ultra Disk: [rendelkezésre állási zóna](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) , de a virtuális gépeket ugyanabban a rendelkezésre állási zónában kell elhelyezni, amely a fürt rendelkezésre állását a 99,9%-ra csökkenti. 
- **Prémium fájlmegosztás**: [rendelkezésre állási csoport](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) vagy [rendelkezésre állási zóna](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Közvetlen tárolóhelyek**: [rendelkezésre állási csoport](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> A virtuális gép létrehozása után a rendelkezésre állási csoport nem állítható be és nem módosítható.

## <a name="create-the-virtual-machines"></a>A virtuális gépek létrehozása

A virtuális gép rendelkezésre állásának konfigurálása után készen áll a virtuális gépek létrehozására. Választhat, hogy olyan Azure Marketplace-rendszerképet szeretne használni, amely SQL Server már telepítve van vagy sem. Ha azonban az Azure-beli virtuális gépeken SQL Server rendszerképet választ, akkor a feladatátvevő fürt példányának konfigurálása előtt el kell távolítania a SQL Servert a virtuális gépről. 

### <a name="considerations"></a>Megfontolandó szempontok

Egy Azure-beli virtuális gép vendég feladatátvevő fürtön egyetlen NIC-kiszolgálót (fürtcsomópont) és egyetlen alhálózatot ajánlunk. Az Azure-hálózatkezelés fizikai redundanciával rendelkezik, ami felesleges hálózati adaptereket és alhálózatokat tesz lehetővé az Azure IaaS VM-vendég fürtön. Bár a fürtellenőrzési jelentés figyelmeztetést küld, amely szerint a csomópontok csak egyetlen hálózaton érhetők el, ez a figyelmeztetés nyugodtan figyelmen kívül hagyható az Azure IaaS virtuális gépek vendég feladatátvevő fürtjein.

Mindkét virtuális gép elhelyezése:

- Ha rendelkezésre állási csoportokat használ, ugyanabban az Azure-erőforráscsoportban használja a rendelkezésre állási csoportot.
- Ugyanazon a virtuális hálózaton, mint a tartományvezérlő vagy egy olyan virtuális hálózat, amely megfelelő kapcsolattal rendelkezik a tartományvezérlőhöz.
- Olyan alhálózaton, amely elegendő IP-címmel rendelkezik a virtuális gépekhez és a fürtön esetlegesen használt összes FCIs.
- Az Azure rendelkezésre állási csoport vagy rendelkezésre állási zónában.

Létrehozhat egy Azure-beli virtuális gépet SQL Server előre [telepített vagy](sql-vm-create-portal-quickstart.md) [anélküli](../../../virtual-machines/windows/quick-create-portal.md) rendszerképpel. Ha a SQL Server rendszerképet választja, manuálisan el kell távolítania a SQL Server példányt a feladatátvevő fürt példányának telepítése előtt. 


## <a name="uninstall-sql-server"></a>SQL Server eltávolítása

A létrehozási folyamat során a SQL Server fürtözött példányként telepíti a feladatátvevő fürtbe. *Ha SQL Server nélkül telepített egy Azure Marketplace-lemezképpel rendelkező virtuális gépet, akkor kihagyhatja ezt a lépést.* Ha SQL Server előre telepített lemezképet telepített, akkor törölnie kell a SQL Server VM az SQL IaaS-ügynök bővítménnyel, majd el kell távolítania a SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Az SQL IaaS-ügynök bővítmény regisztrációjának törlése

Az Azure Marketplace-ről SQL Server VM rendszerképek automatikusan regisztrálva lesznek az SQL IaaS-ügynök bővítménnyel. Az előre telepített SQL Server példány eltávolítása előtt törölnie kell [minden SQL Server VM az SQL IaaS-ügynök bővítményből](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>SQL Server eltávolítása

A bővítményből való regisztráció törlése után eltávolíthatja SQL Server. Kövesse az alábbi lépéseket az egyes virtuális gépeken: 

1. Kapcsolódjon a virtuális géphez RDP használatával.

   Amikor az RDP használatával először csatlakozik egy virtuális géphez, a rendszer megkérdezi, hogy szeretné-e, hogy a számítógép felderíthető legyen a hálózaton. Válassza az **Igen** lehetőséget.

1. Ha a SQL Server-alapú virtuálisgép-rendszerképek egyikét használja, távolítsa el a SQL Server példányt:

   1. A **programok és szolgáltatások** területen kattintson a jobb gombbal az **Microsoft SQL Server 201_ (64 bites)** elemre, és válassza az **Eltávolítás/módosítás** parancsot.
   1. Válassza az **Eltávolítás** lehetőséget.
   1. Válassza ki az alapértelmezett példányt.
   1. Távolítsa el az összes funkcióját az **adatbázismotor-szolgáltatások** területen. Ne távolítson el semmit a **megosztott szolgáltatások** területen. A következő képernyőképhez hasonlóan fog megjelenni:

      ![Funkciók kiválasztása](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Válassza a **tovább**, majd az **Eltávolítás** lehetőséget.
   1. A példány sikeres eltávolítása után indítsa újra a virtuális gépet. 

## <a name="open-the-firewall"></a>A tűzfal megnyitása 

Az egyes virtuális gépeken nyissa meg a SQL Server által használt Windows tűzfal TCP-portot. Alapértelmezés szerint ez a 1433-es port. A SQL Server portot azonban módosíthatja egy Azure-beli virtuális gép üzembe helyezése során, így megnyithatja azt a portot, amelyet SQL Server használ a környezetben. Ez a port automatikusan megnyílik az Azure Marketplace-en üzembe helyezett SQL Server rendszerképeken. 

[Terheléselosztó](failover-cluster-instance-vnn-azure-load-balancer-configure.md)használata esetén meg kell nyitnia azt a portot is, amelyet az állapot-mintavétel használ. Alapértelmezés szerint ez a 59999-es port. A terheléselosztó létrehozásakor azonban bármilyen TCP-port is megadható. 

Ez a tábla a szükséges portokat részletezi az Ön által megnyitható portoktól függően: 

   | Cél | Port | Jegyzetek
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Normál port a SQL Server alapértelmezett példányaihoz. Ha a katalógusból rendszerképet használt, a rendszer automatikusan megnyitja a portot. </br> </br> **Felhasználta**: az összes%-os konfiguráció. |
   | Állapotadat-mintavétel | TCP 59999 | Bármilyen nyitott TCP-port. Konfigurálja a terheléselosztó [állapotának](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) mintavételét és a fürtöt a port használatára. </br> </br> **A**(z): a Load Balancer használatával. |
   | Fájlmegosztás | UDP 445 | A fájlmegosztási szolgáltatás által használt port. </br> </br> **A**(z): verzió prémium fájlmegosztás használatával. |

## <a name="join-the-domain"></a>Csatlakozás a tartományhoz

A virtuális gépeket is csatlakoztatnia kell a tartományhoz. Ezt megteheti egy rövid útmutató [sablon](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)használatával. 

## <a name="review-storage-configuration"></a>Tárolási konfiguráció áttekintése

Az Azure piactéren létrehozott virtuális gépekhez csatlakoztatott tároló tartozik. Ha a prémium szintű fájlmegosztás vagy az Azure Shared Disks használatával tervezi beállítani a (z)%-os tárhelyet, eltávolíthatja a csatlakoztatott tárolót a költségek mentéséhez, mivel a rendszer a feladatátvevő fürt példányához nem használja a helyi tárterületet. Azonban lehetséges, hogy a csatlakoztatott tárolót Közvetlen tárolóhelyek-es verziós megoldásokhoz is használhatja, ezért előfordulhat, hogy a rendszer nem segít eltávolítani őket ebben az esetben. Tekintse át a következőt:-es tárolási megoldás annak megállapításához, hogy a csatlakoztatott tároló eltávolítása optimális-e a költségek megtakarításához 


## <a name="next-steps"></a>Következő lépések

Most, hogy előkészítette a virtuális gép környezetét, készen áll a feladatátvevő fürt példányának konfigurálására. 

A következő útmutatók közül választva konfigurálhatja a vállalata számára megfelelő, az-ra vonatkozó működési környezetet: 
- [A szolgáltatás konfigurálása Azure-beli megosztott lemezekkel](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [A verzió konfigurálása prémium fájlmegosztás esetén](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Az Közvetlen tárolóhelyek konfigurálása](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

További információ: az Azure-beli [virtuális gépek és a](failover-cluster-instance-overview.md) [támogatott HADR-konfigurációk](hadr-cluster-best-practices.md)SQL Serverának áttekintése. 

További információt a következő témakörben talál: 
- [Windows-fürtök technológiái](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server feladatátvevő fürt példányai](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)