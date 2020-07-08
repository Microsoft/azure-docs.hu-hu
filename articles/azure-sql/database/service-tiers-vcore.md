---
title: Virtuális mag beszerzési modell áttekintése
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: A virtuális mag vásárlási modellje lehetővé teszi a számítási és tárolási erőforrások egymástól független méretezését, a helyszíni teljesítmény egyeztetését, valamint a Azure SQL Database és az Azure SQL felügyelt példány árának optimalizálását.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 7b5e4174da3ffa0dff5c840e5da1d98435e8d07b
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985550"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Virtuális mag-modell áttekintése – Azure SQL Database és az Azure SQL felügyelt példánya 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példányai által használt Virtual Core (virtuális mag) vásárlási modell számos előnnyel jár:

- Nagyobb számítási, memória-, I/O-és tárolási korlátok.
- Szabályozhatja a hardver generációját, hogy jobban megfeleljen a számítási és memória-követelményeknek.
- A [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) és a [fenntartott példány (ri)](reserved-capacity-overview.md)díjszabási kedvezményei.
- A hardver részletes adatainak nagyobb átláthatósága a számítási teljesítmény érdekében megkönnyíti a helyszíni telepítések áttelepítésének megtervezését.

## <a name="service-tiers"></a>Szolgáltatásszintek

A virtuális mag modellben található szolgáltatási rétegek beállításai közé tartozik a általános célú, a üzletileg kritikus és a nagy kapacitású. A szolgáltatási szintek általában meghatározzák a tárolási architektúrát, a tárhelyet és az I/O-korlátokat, valamint a rendelkezésre állással és a vész-helyreállítással kapcsolatos üzletmenet-folytonossági lehetőségeket.

|-|**általános célú**|**üzletileg kritikus**|**Rugalmas skálázás**|
|---|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladat. A szolgáltatás költségvetés-orientált, kiegyensúlyozott és méretezhető számítási és tárolási lehetőségeket kínál. |Több elkülönített replika használatával a lehető legnagyobb rugalmasságot nyújtja az üzleti alkalmazások számára, és az adatbázis-replikák esetében a legmagasabb I/O-teljesítményt biztosítja.|A legtöbb üzleti számítási feladat nagy mértékben méretezhető tárolási és olvasási méretezési követelményekkel.  Nagyobb rugalmasságot biztosít a hibákhoz azáltal, hogy lehetővé teszi több elkülönített adatbázis-replika konfigurációját. |
|Storage|Távoli tárterületet használ.<br/>**SQL Database kiépített számítás**:<br/>5 GB – 4 TB<br/>**Kiszolgáló nélküli számítás**:<br/>5 GB – 3 TB<br/>**SQL felügyelt példány**: 32 GB – 8 TB |A helyi SSD-tárolót használ.<br/>**SQL Database kiépített számítás**:<br/>5 GB – 4 TB<br/>**SQL felügyelt példány**:<br/>32 GB – 4 TB |A tárterület rugalmas automatikus növekedése igény szerint. Akár 100 TB tárterületet is támogat. A helyi SSD-tárolót használ a helyi puffer-készlet gyorsítótárához és a helyi adattároláshoz. Az Azure-beli távoli tárterületet használja végső hosszú távú adattárként. |
|IOPS és átviteli sebesség (hozzávetőleges)|**SQL Database**: az [önálló adatbázisok](resource-limits-vcore-single-databases.md) és a [rugalmas készletek](resource-limits-vcore-elastic-pools.md)erőforrás-korlátai.<br/>**SQL felügyelt példány**: lásd az [Azure SQL felügyelt példányok erőforrás-korlátainak áttekintése](../managed-instance/resource-limits.md#service-tier-characteristics)című témakört.|Tekintse meg az [önálló adatbázisok](resource-limits-vcore-single-databases.md) és a [rugalmas készletek](resource-limits-vcore-elastic-pools.md)erőforrás-korlátozásait.|A nagy kapacitású egy többrétegű architektúra, több szinten történő gyorsítótárazással. A hatékony IOPS és az átviteli sebesség a munkaterheléstól függ.|
|Rendelkezésre állás|1 replika, nincsenek olvasási méretezésű replikák|3 replika, 1 [olvasási léptékű replika](read-scale-out.md),<br/>zóna – redundáns magas rendelkezésre állás (HA)|1 írható-olvasható replika, valamint 0-4 [-es olvasási léptékű replika](read-scale-out.md)|
|Biztonsági másolatok|[Olvasási hozzáférés – geo-redundáns tárolás (ra-GRS)](../../storage/common/geo-redundant-design.md), 7-35 nap (alapértelmezés szerint 7 nap)|[Ra-GRS](../..//storage/common/geo-redundant-design.md), 7-35 nap (alapértelmezés szerint 7 nap)|Pillanatkép-alapú biztonsági másolatok az Azure-beli távoli tárolóban. A visszaállítja ezeket a pillanatképeket a gyors helyreállításhoz. A biztonsági másolatok azonnaliek, és nem befolyásolják a számítási I/O-teljesítményt. A visszaállítások gyorsak, és nem az adatmennyiség (óra vagy nap helyett percekben).|
|Memóriabeli|Nem támogatott|Támogatott|Nem támogatott|
|||


### <a name="choosing-a-service-tier"></a>Szolgáltatási szint kiválasztása

Az adott munkaterhelés szolgáltatási szintjeinek kiválasztásával kapcsolatos információkért tekintse meg a következő cikkeket:

- [Mikor válassza ki a általános célú szolgáltatási szintet](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Mikor válassza ki a üzletileg kritikus szolgáltatási szintet](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Mikor válassza ki a nagy kapacitású szolgáltatási szintet](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Számítási szintek

A virtuális mag modellben a számítási rétegek lehetőségei közé tartoznak a kiépített és a kiszolgáló nélküli számítási szintek.


### <a name="provisioned-compute"></a>Kiépített számítás

A kiépített számítási szintek meghatározott mennyiségű számítási erőforrást biztosítanak, amelyek a munkaterhelés-tevékenységektől függetlenül folyamatosan vannak kiépítve, és az óránként rögzített számítási kapacitás mennyiségét számlázzák.


### <a name="serverless-compute"></a>Kiszolgáló nélküli számítástechnika

A [kiszolgáló nélküli számítási réteg](serverless-tier-overview.md) automatikusan méretezi a számítási erőforrásokat a munkaterhelés-tevékenységek alapján, és a másodpercenként felhasznált számítási kapacitás mennyiségét.



## <a name="hardware-generations"></a>Hardver generációi

A virtuális mag modell hardver-létrehozási lehetőségei közé tartozik a Gen 4/5, az M-Series (előzetes verzió) és a Fsv2 sorozat (előzetes verzió). A hardverek létrehozása általában meghatározza a számítási és a memóriabeli korlátokat, valamint azokat a jellemzőket, amelyek hatással vannak a munkaterhelés teljesítményére.

### <a name="gen4gen5"></a>Gen4/Gen5

- A Gen4/Gen5-hardver kiegyensúlyozott számítási és memória-erőforrásokat biztosít, és a legtöbb olyan adatbázis-számítási feladathoz alkalmas, amely nem rendelkezik nagyobb memóriával, magasabb virtuális mag vagy gyorsabb, virtuális mag követelményekkel, amelyet a Fsv2-sorozat vagy az M sorozat biztosít.

Azokon a régiókban, ahol a Gen4/Gen5 elérhető, tekintse meg a [Gen4/Gen5 rendelkezésre állását](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Fsv2 sorozat (előzetes verzió)

- A Fsv2 sorozat egy számítási optimalizált hardveres beállítás, amely alacsony CPU-késést és nagy órajelet biztosít a legtöbb CPU-igényű munkaterheléshez.
- A számítási feladattól függően a Fsv2 sorozat több CPU-teljesítményt biztosíthat virtuális mag, mint a Gen5, és az 72 virtuális mag mérete nagyobb CPU-teljesítményt biztosít, kevesebb, mint 80 virtuális mag a Gen5. 
- A Fsv2 kevesebb memóriát és tempdb biztosít, mint a többi hardver, így a korlátokra érzékeny munkaterhelések Ehelyett a Gen5 vagy az M sorozatokat is érdemes figyelembe venni.  

A Fsv2 sorozat csak a általános célú szinten támogatott.  Azokon a régiókban, ahol elérhető a Fsv2 sorozat, tekintse meg a [Fsv2-sorozat elérhetőségét](#fsv2-series)ismertető témakört.


### <a name="m-seriespreview"></a>M-sorozat (előzetes verzió)

- Az M-sorozat egy memória-optimalizált hardveres beállítás, amely több memóriát és nagyobb számítási korlátot igényel, mint amennyit a Gen5 biztosít.
- Az M-sorozat 29 GB-ot biztosít virtuális mag és 128 virtuális mag, ami növeli a Gen5 viszonyított memória korlátját a 8x-tól közel 4 TB-ig.

Az M-sorozat csak a üzletileg kritikus szinten támogatott, és nem támogatja a zóna-redundanciát.  Az előfizetésnek fizetős ajánlat típusúnak kell lennie, beleértve az utólagos elszámolású vagy a Nagyvállalati Szerződés (EA) szolgáltatásokat.  Az m-sorozat rendelkezésre állását tartalmazó régiók esetében lásd: az [m-sorozat elérhetősége](#m-series).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>Számítási és memória-specifikációk


|Hardver létrehozása  |Compute  |Memory (Memória)  |
|:---------|:---------|:---------|
|Gen4     |-Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorok<br>-Akár 24 virtuális mag (1 virtuális mag = 1 fizikai mag)  |-7 GB/virtuális mag<br>– Akár 168 GB-nyi kiépítés|
|Gen5     |**Kiépített számítás**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) * processzorok<br>– Akár 80 virtuális mag (1 virtuális mag = 1 Hyper-thread)<br><br>**Kiszolgáló nélküli számítástechnika**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) * processzorok<br>– Akár 16 virtuális mag automatikus méretezés (1 virtuális mag = 1 Hyper-thread)|**Kiépített számítás**<br>-5,1 GB/virtuális mag<br>– Akár 408 GB-nyi kiépítés<br><br>**Kiszolgáló nélküli számítástechnika**<br>– Akár 24 GB-os automatikus méretezés virtuális mag<br>-Legfeljebb 48 GB-ig terjedő automatikus méretezés|
|Fsv2 sorozat     |-Intel Xeon Platinum 8168 (SkyLake) processzorok<br>– A 3,4 GHz-es és az összes Core Turbo órajel-sebesség, valamint a 3,7 GHz-es maximális, egyetlen Core Turbo órajel.<br>-Kiépítés 72 virtuális mag (1 virtuális mag = 1 Hyper-thread)|-1,9 GB/virtuális mag<br>– 136 GB kiépítése|
|M sorozat     |-Intel Xeon E7-8890 v3 2,5 GHz és Intel Xeon Platinum 8280M 2,7 GHz (Cascade Lake) processzorok<br>-Kiépítés 128 virtuális mag (1 virtuális mag = 1 Hyper-thread)|– 29 GB/virtuális mag<br>– 3,7 TB kiépítése|

\*A [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dinamikus felügyeleti nézetében az Intel SP-8160 (Skylake) processzorokkal rendelkező Gen5-adatbázisok hardveres generálása Gen6 néven jelenik meg. Az összes Gen5-adatbázis erőforrás-korlátja azonos a processzor típusától (Broadwell vagy Skylake) függetlenül.

Az erőforrás-korlátokkal kapcsolatos további információkért lásd: [az önálló adatbázisok erőforrás-korlátai (virtuális mag)](resource-limits-vcore-single-databases.md)vagy a [rugalmas készletek erőforrás-korlátai (virtuális mag)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Hardver-létrehozás kiválasztása

A Azure Portalban kiválaszthatja a hardver generációját a létrehozáskor SQL Database adatbázis vagy készlet számára, vagy megváltoztathatja egy meglévő adatbázis vagy készlet hardveres létrehozását.

**Hardver létrehozásának kiválasztása SQL Database vagy-készlet létrehozásakor**

Részletes információkat a [SQL Database létrehozása](single-database-create-quickstart.md)című témakörben talál.

Az **alapvető** beállítások lapon válassza az **adatbázis konfigurálása** hivatkozást a **számítás + tárolás** szakaszban, majd válassza a **konfiguráció módosítása** hivatkozást:

  ![adatbázis konfigurálása](./media/service-tiers-vcore/configure-sql-database.png)

Válassza ki a kívánt hardver-generálást:

  ![hardver kiválasztása](./media/service-tiers-vcore/select-hardware.png)


**Meglévő SQL Database vagy készlet hardveres létrehozásának módosítása**

Adatbázis esetén az Áttekintés lapon válassza ki a **díjszabási szintet** mutató hivatkozást:

  ![hardver módosítása](./media/service-tiers-vcore/change-hardware.png)

Készlet esetén az Áttekintés lapon válassza a **Konfigurálás**lehetőséget.

Kövesse a konfiguráció módosításához szükséges lépéseket, és válassza ki a hardver generációját az előző lépésekben leírtak szerint.

**Hardveres generáció kiválasztása SQL felügyelt példány létrehozásakor**

Részletes információkért lásd: [SQL felügyelt példány létrehozása](../managed-instance/instance-create-quickstart.md).

Az **alapvető beállítások** lapon válassza az **adatbázis konfigurálása** hivatkozást a **számítás + tárolás** szakaszban, majd válassza a kívánt hardver-létrehozás lehetőséget:

  ![Felügyelt SQL-példány konfigurálása](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Meglévő SQL felügyelt példány hardveres létrehozásának módosítása**

# <a name="the-azure-portal"></a>[Az Azure Portal](#tab/azure-portal)

Az SQL felügyelt példánya lapon válassza ki az **árképzési** csomag hivatkozását a beállítások szakaszban.

![SQL felügyelt példány hardverének módosítása](./media/service-tiers-vcore/change-managed-instance-hardware.png)

A **díjszabási** csomag lapon az előző lépésekben leírtaknak megfelelően módosíthatja a hardverek generációját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsfájlt:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

További részletekért keresse [meg a set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) parancsot.

# <a name="the-azure-cli"></a>[Az Azure CLI](#tab/azure-cli)

Használja az alábbi CLI-parancsot:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

További részletekért lásd [az SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) parancsot.

---

### <a name="hardware-availability"></a>Hardver rendelkezésre állása

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

A Gen4 hardverek fokozatos kiépítése [folyamatban](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) van, és már nem érhető el az új üzemelő példányokhoz. Minden új adatbázist telepíteni kell a Gen5 hardveren.

A Gen5 a világ legtöbb régiójában elérhető.

#### <a name="fsv2-series"></a>Fsv2 sorozat

A Fsv2 sorozat a következő régiókban érhető el: Ausztrália középső régiója, Ausztrália középső régiója, Kelet-Ausztrália, Kelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Ázsia, USA keleti régiója, Közép-Németország, Közép-India, Közép-India, Korea középső régiója, Dél-Korea, Észak-Európa, Dél-Afrika északi régiója, Délkelet-Egyesült Királyság nyugati régiója Egyesült Királyság déli régiója Ázsia


#### <a name="m-series"></a>M sorozat

Az M-sorozat a következő régiókban érhető el: USA keleti régiója, Észak-Európa, Nyugat-Európa, USA 2. nyugati régiója.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>További lépések

Első lépésként tekintse meg a következőt: 
- [SQL Database létrehozása a Azure Portal használatával](single-database-create-quickstart.md)
- [SQL felügyelt példány létrehozása a Azure Portal használatával](../managed-instance/instance-create-quickstart.md)

A díjszabással kapcsolatos részletekért tekintse meg a [Azure SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/single/).

Az általános célú és az üzleti szempontból kritikus szolgáltatási szinten elérhető konkrét számítási és tárolási méretek részletes ismertetését lásd:

- [Azure SQL Database virtuális mag-alapú erőforrás-korlátai](resource-limits-vcore-single-databases.md).
- [a készletezett Azure SQL Database virtuális mag-alapú erőforrás-korlátai](resource-limits-vcore-elastic-pools.md).
- [virtuális mag-alapú erőforrás-korlátok az Azure SQL felügyelt példányaihoz](../managed-instance/resource-limits.md). 

