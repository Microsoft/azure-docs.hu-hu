---
title: Tároló konfigurálása SQL Server virtuális gépekhez | Microsoft Docs
description: Ez a témakör azt ismerteti, hogy az Azure hogyan konfigurálja a tárolót SQL Server virtuális gépek számára a kiépítés során (Azure Resource Manager üzembehelyezési modell). Azt is ismerteti, hogyan konfigurálhatja a tárhelyet a meglévő SQL Server virtuális gépekhez.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565422"
---
# <a name="configure-storage-for-sql-server-vms"></a>Tároló konfigurálása SQL Server virtuális gépekhez
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a tárhelyét az Azure Virtual Machines (VM) SQL Server.

A Piactéri lemezképeken keresztül üzembe helyezett SQL Server virtuális gépek automatikusan követik az alapértelmezett [tárolási ajánlott eljárásokat](performance-guidelines-best-practices-storage.md) , amelyek az üzembe helyezés során módosíthatók. Ezen konfigurációs beállítások némelyike az üzembe helyezés után módosítható. 


## <a name="prerequisites"></a>Előfeltételek

Az automatikus tárolási konfigurációs beállítások használatához a virtuális gépnek a következő jellemzőkkel kell rendelkeznie:

* Kiépítve egy [SQL Server Gallery-lemezképpel](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) , vagy az [SQL IaaS bővítménnyel]()van regisztrálva.
* A [Resource Manager](../../../azure-resource-manager/management/deployment-models.md)-alapú üzemi modellt használja.
* [Prémium SSD](../../../virtual-machines/disks-types.md)-ket használ.

## <a name="new-vms"></a>Új virtuális gépek

A következő szakaszok azt ismertetik, hogyan konfigurálható a tároló az új SQL Server virtuális gépekhez.

### <a name="azure-portal"></a>Azure Portal

Ha egy Azure-beli virtuális gépet kiépít egy SQL Server Gallery-rendszerkép használatával, válassza a **konfiguráció módosítása** lehetőséget a **SQL Server beállítások** lapon a teljesítményre optimalizált tárolási konfiguráció lap megnyitásához. Meghagyhatja az alapértelmezett értékeket, vagy módosíthatja az igényeinek leginkább megfelelő lemez-konfigurációt a munkaterhelés alapján. 

![Képernyőkép, amely kiemeli a SQL Server beállítások lapot és a konfiguráció módosítása lehetőséget.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Válassza ki, hogy milyen típusú számítási feladatot kíván üzembe helyezni a SQL Server a **tárolási optimalizálás** alatt. Az **általános** optimalizálási beállítással alapértelmezés szerint egy 5000-os maximális IOPS rendelkező adatlemez lesz, és ugyanazt a meghajtót fogja használni az adataihoz, a tranzakciónaplóhoz és a tempdb-tárolóhoz. 

A **tranzakciós feldolgozás** (OLTP) vagy **az adattárházak** kiválasztása külön lemezt hoz létre az adattároláshoz, egy külön lemezt a tranzakciónaplóhoz, és a helyi SSD-t használja a tempdb. A **tranzakciós feldolgozás** és **az adattárház** közötti különbség nem változik, de a [sáv konfigurációját és a nyomkövetési jelzőket](#workload-optimization-settings)is megváltoztatja. Ha a Premium Storage-t választja, a gyorsítótárazást *readonly* értékre állítja az adatmeghajtón, és a naplófájlok *egyikét sem* [SQL Server VM teljesítményre vonatkozó ajánlott eljárások](performance-guidelines-best-practices.md)alapján. 

![SQL Server VM tárolási konfiguráció a kiépítés során](./media/storage-configuration/sql-vm-storage-configuration.png)

A lemez konfigurációja teljesen testreszabható, így beállíthatja a SQL Server VM számítási feladathoz szükséges tárolási topológiát, lemez típusát és IOPs. Lehetősége van arra is, hogy a UltraSSD (előzetes verzió) lehetőséget adja a **lemez típusának** , ha a SQL Server VM az egyik támogatott régióban van (az USA 2. keleti régiója, Délkelet-Ázsia és Észak-Európa), és az [előfizetése számára](../../../virtual-machines/disks-enable-ultra-ssd.md)engedélyezte az ultra-lemezek használatát.  

Ezen kívül lehetősége van a lemezek gyorsítótárazásának beállítására is. Az Azure-beli virtuális gépek többrétegű gyorsítótárazási technológiával rendelkeznek, amelyet a [prémium szintű lemezek](../../../virtual-machines/disks-types.md#premium-ssd)használatakor használ a [blob cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) . A blob cache a virtuális gép RAM és a helyi SSD kombinációját használja a gyorsítótárazáshoz. 

A prémium SSD lemezes gyorsítótárazása *readonly*, *READWRITE* vagy *none* lehet. 

- A *readonly* gyorsítótárazás nagyon hasznos SQL Server Premium Storage tárolt adatfájlok esetében. Az *írásvédett* gyorsítótárazási szolgáltatás alacsony olvasási késést, nagy olvasási IOPS és átviteli sebességet biztosít, olvasások a gyorsítótárból, amely a virtuális gép memóriájában és a helyi SSD-n belül található. Ezek az olvasások sokkal gyorsabbak, mint az Azure Blob Storage-ból származó adatlemezek olvasása. A Premium Storage nem számítja ki a gyorsítótárból kiszolgált adatokat a lemez IOPS és az átviteli sebesség felé. Ezért az Ön megfelelője magasabb teljes IOPS és átviteli sebesség elérésére képes. 
- *Nincs* szükség gyorsítótár-konfigurációra a SQL Server naplófájlt futtató lemezekhez, mivel a naplófájlt a rendszer szekvenciálisan írja le, és nem használja az *írásvédett* gyorsítótárazást. 
- A *READWRITE* gyorsítótárazás nem használható SQL Server fájlok üzemeltetéséhez, mivel SQL Server nem támogatja az adatkonzisztencia a *READWRITE* cache-sel. Az *írásvédett* blobos gyorsítótárban lévő adatmennyiséget írja, és a késleltetések kis mértékben megnő, ha az írások *readonly* blob cache-rétegeken mennek át. 


   > [!TIP]
   > Győződjön meg arról, hogy a tárolási konfiguráció megfelel a kiválasztott virtuálisgép-méret által támasztott korlátozásoknak. A virtuálisgép-méretet meghaladó tárolási paraméterek kiválasztásakor a rendszer figyelmeztetést eredményez: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Csökkentse a IOPs a lemez típusának módosításával, vagy növelje a teljesítmény korlátját a virtuális gép méretének növelésével. Ez nem fogja leállítani a kiépítés kihelyezését. 


A lehetőségek alapján az Azure a következő tárolási konfigurációs feladatokat hajtja végre a virtuális gép létrehozása után:

* A prémium SSD-k létrehozása és csatolása a virtuális géphez.
* Az adatlemezek SQL Server számára elérhetővé való hozzáférhetőségének beállítása.
* A megadott méret és teljesítmény (IOPS és átviteli sebesség) követelményei alapján konfigurálja az adatlemezeket a tárolási készletekbe.
* Társítja a tárolót egy új meghajtóval a virtuális gépen.
* Optimalizálja ezt az új meghajtót a megadott munkaterhelés-típus (adattárház, tranzakciós feldolgozás vagy általános) alapján.

A Azure Portal SQL Server VM létrehozásával kapcsolatos teljes útmutatóért tekintse meg [az üzembe helyezési oktatóanyagot](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Ha a következő Resource Manager-sablonokat használja, a rendszer alapértelmezés szerint két prémium szintű adatlemezt csatol, és nem rendelkezik tároló-konfigurációval. Ezeket a sablonokat azonban testreszabhatja úgy, hogy megváltoztassa a virtuális géphez csatolt prémium szintű adatlemezek számát.

* [Automatikus biztonsági mentéssel rendelkező virtuális gép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Automatikus javítással rendelkező virtuális gép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Virtuális gép létrehozása AKV-integrációval](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Gyorsindítási sablon

A következő rövid útmutató sablon használatával telepítheti SQL Server VM a tárolási optimalizálással. 

* [Virtuális gép létrehozása tárolási optimalizálással](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Virtuális gép létrehozása a UltraSSD használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A meglévő SQL Server virtuális gépek esetében a Azure Portal egyes tárolási beállításait is módosíthatja. Nyissa meg az [SQL Virtual Machines-erőforrást](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), és válassza az **Áttekintés** lehetőséget. A SQL Server áttekintő oldal a virtuális gép aktuális tárterület-használatát jeleníti meg. Ebben a diagramban a virtuális gépen található összes meghajtó megjelenik. Az egyes meghajtók esetében a tárolóhely négy szakaszban jelenik meg:

* SQL-adatszolgáltatások
* SQL-napló
* Egyéb (nem SQL-alapú tárolás)
* Elérhető

A tárolási beállítások módosításához kattintson a **Konfigurálás** elemre a **Beállítások** területen. 

![Képernyőfelvétel: a konfigurálási beállítás és a tárterület-használat szakasz.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Módosíthatja a SQL Server VM létrehozási folyamat során konfigurált meghajtók lemezének beállításait. A **kibővített meghajtó** kiválasztásával megnyithatja a meghajtó módosítása lapot, amely lehetővé teszi a lemez típusának módosítását, valamint további lemezek hozzáadását. 

![Meglévő SQL Server VM tárterületének konfigurálása](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Automatikus változtatások

Ez a szakasz az Azure által a Azure Portal SQL Server VM üzembe helyezése vagy konfigurálása során automatikusan elvégezett tárolási konfigurációs módosításokat ismerteti.

* Az Azure a virtuális gépről kiválasztott tárolóból konfigurálja a tárolási készletet. A témakör következő szakasza részletesen ismerteti a Storage-készlet konfigurációját.
* Az automatikus tárolási konfiguráció mindig [prémium SSD](../../../virtual-machines/disks-types.md) -P30 adatlemezeket használ. Ennek következtében a kiválasztott számú terabájt és a virtuális géphez csatolt adatlemezek száma között van egy 1:1 leképezés.

A díjszabással kapcsolatos információkért tekintse meg a [Storage díjszabását](https://azure.microsoft.com/pricing/details/storage) ismertető oldalt a **Disk Storage** lapon.

### <a name="creation-of-the-storage-pool"></a>A Storage-készlet létrehozása

Az Azure a következő beállítások használatával hozza létre a tárolót SQL Server virtuális gépeken.

| Beállítás | Érték |
| --- | --- |
| Sáv mérete |256 KB (adattárház); 64 KB (tranzakciós) |
| Lemezek mérete |1 TB |
| Gyorsítótár |Olvasás |
| Foglalás mérete |64 KB NTFS-foglalási egység mérete |
| Helyreállítási | Egyszerű helyreállítás (nincs rugalmasság) |
| Oszlopok száma |Adatlemezek száma legfeljebb 8<sup>1</sup> |


<sup>1</sup> a Storage-készlet létrehozása után nem módosítható a tárolóban lévő oszlopok száma.


### <a name="workload-optimization-settings"></a>Munkaterhelés-optimalizálási beállítások

Az alábbi táblázat az elérhető három munkaterhelés-típust és a hozzájuk tartozó optimalizálási lehetőségeket ismerteti:

| Munkaterhelés típusa | Description | Optimalizálás |
| --- | --- | --- |
| **Általános** |Alapértelmezett beállítás, amely támogatja a legtöbb munkaterhelést |Nincsenek |
| **Tranzakciós feldolgozás** |Optimalizálja a tárolót a hagyományos adatbázis-OLTP számítási feladatokhoz |Nyomkövetési jelző 1117<br/>Nyomkövetési jelző 1118 |
| **Adattárház** |Optimalizálja a tárolót analitikai és jelentéskészítési számítási feladatokhoz |Nyomkövetési jelző 610<br/>Nyomkövetési jelző 1117 |

> [!NOTE]
> A számítási feladatok típusát csak akkor adhatja meg, ha kijelöli a SQL Server virtuális gépet a tárolási konfiguráció lépésben.

## <a name="enable-caching"></a>Gyorsítótárazás engedélyezése 

Módosítsa a gyorsítótárazási házirendet a lemez szintjén. Ezt a Azure Portal, a [PowerShell](/powershell/module/az.compute/set-azvmdatadisk)vagy az [Azure CLI](/cli/azure/vm/disk)használatával teheti meg. 

Ha módosítani szeretné a gyorsítótárazási házirendet a Azure Portalban, kövesse az alábbi lépéseket:

1. Állítsa le SQL Server szolgáltatását. 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon a virtuális géphez, és válassza a **lemezek** elemet a **Beállítások** területen. 
   
   ![Képernyőfelvétel: a Azure Portal lévő virtuálisgép-lemez konfigurációjának panelje.](./media/storage-configuration/disk-in-portal.png)

1. A legördülő listából válassza ki a megfelelő gyorsítótárazási házirendet a lemezhez. 

   ![Képernyőfelvétel: a lemez-gyorsítótárazási házirend konfigurációja a Azure Portalban.](./media/storage-configuration/azure-disk-config.png)

1. A módosítás életbe léptetéséhez indítsa újra a SQL Server VM, és indítsa el a SQL Server szolgáltatást. 


## <a name="enable-write-accelerator"></a>írásgyorsító engedélyezése

Az írási gyorsítás egy olyan lemez-szolgáltatás, amely csak az M sorozatú Virtual Machines (VM) esetében érhető el. Az írási gyorsítás célja, hogy javítsa az írások I/O-késését az Azure-Premium Storageon, ha a nagy mennyiségű, kritikus fontosságú OLTP számítási feladatnak vagy adattárház-környezetnek köszönhetően egyszámjegyű I/O-késésre van szüksége. 

Az írási gyorsítási szabályzat módosítása előtt állítsa le az összes SQL Server tevékenységet, és állítsa le a SQL Server szolgáltatást. 

Ha a lemezek csíkozottak, minden egyes lemez esetében engedélyezze az írási gyorsítást, és az Azure-beli virtuális gépet le kell állítani a módosítások végrehajtása előtt. 

Az írási gyorsítás a Azure Portal használatával történő engedélyezéséhez kövesse az alábbi lépéseket:

1. Állítsa le SQL Server szolgáltatását. Ha a lemezek csíkozottak, állítsa le a virtuális gépet. 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon a virtuális géphez, és válassza a **lemezek** elemet a **Beállítások** területen. 
   
   ![Képernyőfelvétel: a Azure Portal lévő virtuálisgép-lemez konfigurációjának panelje.](./media/storage-configuration/disk-in-portal.png)

1. A legördülő menüben válassza a gyorsítótár lehetőséget a lemez **írásgyorsító** . 

   ![Képernyőfelvétel: az írási gyorssegéd gyorsítótárazási házirendje.](./media/storage-configuration/write-accelerator.png)

1. A módosítás érvénybe léptetése után indítsa el a virtuális gépet, és SQL Server a szolgáltatást. 

## <a name="disk-striping"></a>Lemezek csíkozása

További átviteli sebesség esetén további adatlemezeket adhat hozzá, és a lemezek csíkozását is használhatja. Az adatlemezek számának meghatározásához elemezze a SQL Server adatfájlokhoz szükséges átviteli sebességet és sávszélességet, beleértve a napló-és a tempdb. Az átviteli sebesség és a sávszélesség korlátai a virtuális gépek méretétől függően változnak. További információ: [virtuális gép mérete](../../../virtual-machines/sizes.md)


* Windows 8/Windows Server 2012 vagy újabb rendszer esetén a [tárolóhelyek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) a következő irányelvekkel használhatók:

  1. Állítsa be az Interleave (lépcsőzetes méret) értéket 64 KB-ra (65 536 bájt), hogy elkerülje a teljesítményre gyakorolt hatást a partíció helytelen igazítása miatt. Ezt a PowerShell-lel kell beállítani.

  2. Oszlopok számának beállítása = fizikai lemezek száma. A PowerShell használata a több mint 8 lemez konfigurálásához (a Kiszolgálókezelő felhasználói felülete nem).

A következő PowerShell például létrehoz egy új tárolási készletet az összevont mérettel 64 KB értékre, és az oszlopok számát, amely a tárolási készlet fizikai lemezének mennyiségével egyenlő:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Windows 2008 R2 vagy korábbi verzió esetén a dinamikus lemezeket (operációsrendszer-csíkozott kötetek) is használhatja, és a sáv mérete mindig 64 KB. Ez a beállítás a Windows 8/Windows Server 2012 operációs rendszertől kezdve elavult. További információ: a virtuális lemez szolgáltatás támogatási nyilatkozata a [Windows Storage Management API-ra való áttérést](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)ismerteti.
 
  * Ha [közvetlen tárolóhelyekt (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) használ [SQL Server feladatátvevő fürt példányaival](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure), egyetlen készletet kell konfigurálnia. Bár a különböző kötetek hozhatók létre az adott készleten, mind ugyanazok a jellemzők, mint például a gyorsítótárazási házirend.
 
  * Határozza meg a tárolási készlethez társított lemezek számát a betöltési várakozások alapján. Ne feledje, hogy a különböző virtuálisgép-méretek különböző számú csatlakoztatott adatlemezt tesznek lehetővé. További információ: [virtuális gépek méretei](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Következő lépések

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).
