---
title: SQL Server virtuális gépek tárolási konfigurációja | Microsoft Docs
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
ms.openlocfilehash: d713faf7062f82110be5fa8378faca368b9bb7a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356714"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Tárolási konfiguráció SQL Server rendszerű virtuális gépekhez
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server virtuális gép (VM) rendszerképének az Azure-ban való konfigurálásakor a Azure Portal segíti a tárolási konfiguráció automatizálását. Ide tartozik a tároló csatlakoztatása a virtuális géphez, így a tárterület elérhetővé válik a SQL Server számára, és beállítható úgy, hogy optimalizálja az adott teljesítményre vonatkozó követelményeket.

Ez a témakör azt ismerteti, hogyan konfigurálja az Azure a SQL Server virtuális gépek tárterületét a kiépítés és a meglévő virtuális gépek esetében. Ez a konfiguráció a SQL Server rendszert futtató Azure-beli virtuális gépek [teljesítményének ajánlott eljárásain](performance-guidelines-best-practices.md) alapul.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek

Az automatikus tárolási konfigurációs beállítások használatához a virtuális gépnek a következő jellemzőkkel kell rendelkeznie:

* Kiépítve [SQL Server Gallery-képpel](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).
* A [Resource Manager](../../../azure-resource-manager/management/deployment-models.md)-alapú üzemi modellt használja.
* [Prémium SSD](../../../virtual-machines/disks-types.md)-ket használ.

## <a name="new-vms"></a>Új virtuális gépek

A következő szakaszok azt ismertetik, hogyan konfigurálható a tároló az új SQL Server virtuális gépekhez.

### <a name="azure-portal"></a>Azure Portal

Ha egy Azure-beli virtuális gépet kiépít egy SQL Server Gallery-rendszerkép használatával, válassza a **konfiguráció módosítása** lehetőséget a **SQL Server beállítások** lapon a teljesítményre optimalizált tárolási konfiguráció lap megnyitásához. Meghagyhatja az alapértelmezett értékeket, vagy módosíthatja az igényeinek leginkább megfelelő lemez-konfigurációt a munkaterhelés alapján. 

![Képernyőkép, amely kiemeli a SQL Server beállítások lapot és a konfiguráció módosítása lehetőséget.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Válassza ki, hogy milyen típusú számítási feladatot kíván üzembe helyezni a SQL Server a **tárolási optimalizálás** alatt. Az **általános** optimalizálási beállítással alapértelmezés szerint egy 5000-os maximális IOPS rendelkező adatlemez lesz, és ugyanazt a meghajtót fogja használni az adataihoz, a tranzakciónaplóhoz és a tempdb-tárolóhoz. A **tranzakciós feldolgozás** (OLTP) vagy **az adattárházak** kiválasztása külön lemezt hoz létre az adattároláshoz, egy külön lemezt a tranzakciónaplóhoz, és a helyi SSD-t használja a tempdb. A **tranzakciós feldolgozás** és **az adattárház** közötti különbség nem változik, de a [sáv konfigurációját és a nyomkövetési jelzőket](#workload-optimization-settings)is megváltoztatja. Ha a Premium Storage-t választja, a gyorsítótárazást *readonly* értékre állítja az adatmeghajtón, és a naplófájlok *egyikét sem* [SQL Server VM teljesítményre vonatkozó ajánlott eljárások](performance-guidelines-best-practices.md)alapján. 

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

További információ arról, hogy az Azure hogyan konfigurálja a tárolási beállításokat: [tárolási konfiguráció szakasz](#storage-configuration). A Azure Portal SQL Server VM létrehozásával kapcsolatos teljes útmutatóért tekintse meg [az üzembe helyezési oktatóanyagot](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

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


## <a name="storage-configuration"></a>Tároló konfigurálása

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


## <a name="workload-optimization-settings"></a>Munkaterhelés-optimalizálási beállítások

Az alábbi táblázat az elérhető három munkaterhelés-típust és a hozzájuk tartozó optimalizálási lehetőségeket ismerteti:

| Munkaterhelés típusa | Description | Optimalizálás |
| --- | --- | --- |
| **Általános** |Alapértelmezett beállítás, amely támogatja a legtöbb munkaterhelést |Nincsenek |
| **Tranzakciós feldolgozás** |Optimalizálja a tárolót a hagyományos adatbázis-OLTP számítási feladatokhoz |Nyomkövetési jelző 1117<br/>Nyomkövetési jelző 1118 |
| **Adattárház** |Optimalizálja a tárolót analitikai és jelentéskészítési számítási feladatokhoz |Nyomkövetési jelző 610<br/>Nyomkövetési jelző 1117 |

> [!NOTE]
> A számítási feladatok típusát csak akkor adhatja meg, ha kijelöli a SQL Server virtuális gépet a tárolási konfiguráció lépésben.

## <a name="next-steps"></a>Következő lépések

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további témakörökért lásd: [SQL Server az azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).