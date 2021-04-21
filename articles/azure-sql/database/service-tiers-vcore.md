---
title: Virtuálismag-alapú vásárlási modell áttekintése
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: A virtuálismag-alapú vásárlási modell lehetővé teszi a számítási és tárolási erőforrások egymástól független méretezését, a helyszíni teljesítménynek való megfelelőt, valamint a számítási és Azure SQL Database Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779182"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Virtuálismag-alapú modell áttekintése – Azure SQL Database és Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A virtuális mag (vCore) vásárlási modellje, amelyet a Azure SQL Database és Azure SQL Managed Instance használ, számos előnyt biztosít:

- Magasabb számítási, memória-, I/O- és tárolási korlátok.
- Szabályozhatja a hardvergenerációt, hogy jobban megfeleljen a számítási feladat számítási és memóriakövetelményeinek.
- Díjszabási kedvezmények a [Azure Hybrid Benefit (AHB) és](../azure-hybrid-benefit.md) [fenntartott példányok (RI) számára.](reserved-capacity-overview.md)
- A számítási erőforrások hardveres részleteinek nagyobb átláthatósága, ami megkönnyíti a helyszíni környezetből való migrálás megtervezését.

## <a name="service-tiers"></a>Szolgáltatásszintek

A virtuálismag-alapú modell szolgáltatásszint-beállításai közé tartozik a általános célú, üzletileg kritikus és a hyperscale. A szolgáltatási szint általában meghatározza a tárolási architektúrát, a tárhely- és I/O-korlátokat, valamint a rendelkezésre álláshoz és a vészhelyreállításhoz kapcsolódó üzletmenet-folytonossági lehetőségeket.

|-|**Általános célú**|**Üzletileg kritikus**|**Rugalmas skálázás**|
|---|---|---|---|
|A következőkre alkalmas|A legtöbb üzleti számítási feladat. Költséghatékony, kiegyensúlyozott és skálázható számítási és tárolási lehetőségeket nyújt. |Több elkülönített replikával a legnagyobb rugalmasságot biztosítja az üzleti alkalmazások számára a hibákkal szemben, és adatbázis-replikánként a legmagasabb I/O-teljesítményt biztosítja.|A legtöbb üzleti számítási feladat nagymértékben skálázható tárolási és olvasási skálázhatósági követelményekkel.  Nagyobb rugalmasságot biztosít a hibákkal szemben, mert több elkülönített adatbázis-replika konfigurálható. |
|Tárolás|Távoli tárolót használ.<br/>**SQL Database számítás:**<br/>5 GB – 4 TB<br/>**Kiszolgáló nélküli számítás:**<br/>5 GB – 3 TB<br/>**SQL Managed Instance:** 32 GB – 8 TB |Helyi SSD-tárolót használ.<br/>**SQL Database számítás:**<br/>5 GB – 4 TB<br/>**SQL Managed Instance:**<br/>32 GB – 4 TB |Rugalmas tárterület-növekedés szükség szerint. Akár 100 TB tárhelyet is támogat. Helyi SSD-tárolót használ a helyi pufferkészlet gyorsítótára és a helyi adattároláshoz. Az Azure távoli tárolót használja végső hosszú távú adattárként. |
|IOPS és átviteli sebesség (hozzávetőleges)|**SQL Database:** Lásd: Az egyes adatbázisokra és rugalmas [készletekre](resource-limits-vcore-single-databases.md) vonatkozó [erőforráskorlátok.](resource-limits-vcore-elastic-pools.md)<br/>**SQL Managed Instance:** Lásd [az erőforráskorlátok Azure SQL Managed Instance áttekintését.](../managed-instance/resource-limits.md#service-tier-characteristics)|Lásd: Erőforráskorlátok az [egyes adatbázisokhoz és](resource-limits-vcore-single-databases.md) [rugalmas készletekhez.](resource-limits-vcore-elastic-pools.md)|A hyperscale egy többrétegű architektúra, amely több szinten gyorsítótáraz. A tényleges IOPS és átviteli sebesség a számítási feladattól függ.|
|Rendelkezésre állás|1 replika, nincs olvasási léptékű replika|3 replika, 1 [olvasási léptékű replika,](read-scale-out.md)<br/>zónaredundáns magas rendelkezésre állás (HA)|1 olvasási-írási replika, valamint 0–4 olvasási léptékű [replika](read-scale-out.md)|
|Biztonsági másolatok|[Írási hozzáférésű georedundáns tárolás (RA-GRS),](../../storage/common/geo-redundant-design.md)1–35 nap (alapértelmezés szerint 7 nap)|[RA-GRS,](../..//storage/common/geo-redundant-design.md)1–35 nap (alapértelmezés szerint 7 nap)|Pillanatkép-alapú biztonsági másolatok az Azure távoli tárolóban. A visszaállítások ezeket a pillanatképeket használják a gyors helyreállításhoz. A biztonsági mentések azonnaliak, és nincsenek hatással a számítási I/O-teljesítményre. A visszaállítások gyorsak, és nem adatméret-műveletek (órák vagy napok helyett perceket vesz igénybe).|
|Memóriabeli|Nem támogatott|Támogatott|Nem támogatott|
|||


### <a name="choosing-a-service-tier"></a>Szolgáltatási szint kiválasztása

A szolgáltatási szint adott számítási feladathoz való kiválasztásával kapcsolatos információkért tekintse meg a következő cikkeket:

- [Mikor melyiket válassza általános célú szolgáltatási szinthez?](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Mikor melyiket válassza üzletileg kritikus szolgáltatási szinthez?](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Mikor válassza a hyperscale szolgáltatási szintet?](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Számítási szintek

A virtuálismag-alapú modell számítási rétege a kiépített és a kiszolgáló nélküli számítási szinteket is tartalmazza.


### <a name="provisioned-compute"></a>Kiépített számítás

A kiépített számítási szint meghatározott mennyiségű számítási erőforrást biztosít, amelyek a számítási feladatok tevékenységtől függetlenül folyamatosan ki vannak építve, és a kiépített számítási mennyiségért óránként rögzített áron kell fizetni.


### <a name="serverless-compute"></a>Kiszolgáló nélküli számítástechnika

A [kiszolgáló nélküli számítási szint](serverless-tier-overview.md) automatikusan skáláz számítási erőforrásokat a számítási feladatok tevékenysége alapján, és a másodpercenként felhasznált számítási mennyiség alapján számláz.



## <a name="hardware-generations"></a>Hardvergenerációk

A virtuálismag-alapú modell hardvergenerációs lehetőségei közé tartozik a Gen 4/5, az M sorozat, az Fsv2 sorozat és a DC sorozat. A hardvergeneráció általában meghatározza a számítási és memóriakorlátokat és egyéb jellemzőket, amelyek hatással vannak a számítási feladat teljesítményére.

### <a name="gen4gen5"></a>Gen4/Gen5

- A Gen4/Gen5 hardver kiegyensúlyozott számítási és memória-erőforrásokat biztosít, és a legtöbb olyan adatbázisbeli számítási feladathoz megfelelő, amelyek nem rendelkezik az Fsv2-sorozat vagy az M-sorozat által biztosított magasabb memóriával, magasabb virtuális magokkal vagy gyorsabb egy virtuálismag-igényekkel.

Azok a régiók, ahol a Gen4/Gen5 elérhető, lásd: [Gen4/Gen5 rendelkezésre állás.](#gen4gen5-1)

### <a name="fsv2-series"></a>Fsv2 sorozat

- Az Fsv2 sorozat egy számításra optimalizált hardveres megoldás, amely alacsony PROCESSZOR-késést és magas órajel-sebességet biztosít a legnagyobb processzorigényű számítási feladatokhoz.
- A számítási feladattól függően az Fsv2 sorozat virtuális magonként több PROCESSZORteljesítményt tud nyújtani, mint a Gen5, a 72 virtuális magos méret pedig nagyobb processzorteljesítményt biztosít 80 virtuális magnál alacsonyabb költség mellett a Gen5-ben. 
- Az Fsv2 virtuális magonként kevesebb memóriát és tempdb-t biztosít, mint más hardverek, ezért az ezen korlátoknak megfelelő számítási feladatok esetében érdemes megfontolni a Gen5 vagy az M sorozatú megoldásokat.  

Az Fsv2 sorozat csak a általános célú támogatott. Az Fsv2-sorozattal elérhető régiókért lásd: [Fsv2-sorozat rendelkezésre állása.](#fsv2-series-1)

### <a name="m-series"></a>M sorozat

- Az M-sorozat memóriaoptimalált hardveres megoldás olyan számítási feladatokhoz, amelyek a Gen5 által biztosítottnál több memóriát és számítási korlátokat követelnek meg.
- Az M-sorozat virtuális magonként 29 GB-ot és legfeljebb 128 virtuális magot biztosít, ami a Gen5-hez képest 8x-ről közel 4 TB-ra növeli a memóriakorlátot.

Az M-sorozat csak az üzletileg kritikus támogatott, és nem támogatja a zónaredundaniát.  Az M-sorozattal elérhető régiókért lásd: [M-sorozat rendelkezésre állása.](#m-series-1)

#### <a name="azure-offer-types-supported-by-m-series"></a>Az M sorozat által támogatott Azure-ajánlattípusok

Az M-sorozat eléréséhez az előfizetésnek fizetős ajánlattípusnak kell lennie, beleértve a pay-as-Nagyvállalati Szerződés (EA) lehetőséget.  Az M-sorozat által támogatott Azure-ajánlattípusok teljes listájáért tekintse meg az aktuális, költségkeret nélküli [ajánlatokat.](https://azure.microsoft.com/support/legal/offer-details)

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC sorozat

> [!NOTE]
> A DC-sorozat jelenleg nyilvános **előzetes verzióban érhető el.**

- A DC-sorozat hardverei Intel-processzorokat Software Guard Extensions (Intel SGX) technológiával.
- A biztonságos [enklávokkal](/sql/relational-databases/security/encryption/always-encrypted-enclaves)Always Encrypted tartományvezérlő-sorozat szükséges, amely más hardverkonfigurációk esetén nem támogatott.
- A DC-sorozat olyan számítási feladatokhoz lett kialakítva, amelyek bizalmas adatokat kezelnek, és a biztonságos enklávokkal Always Encrypted bizalmas lekérdezésfeldolgozási képességeket követelnek meg.
- A DC-sorozat hardvere kiegyensúlyozott számítási és memória-erőforrásokat biztosít.

A DC-sorozat csak a kiépített számításhoz támogatott (a kiszolgáló nélküli számítás nem támogatott), és nem támogatja a zónaredundanciát. Olyan régiók esetében, ahol a DC-sorozat elérhető, lásd: [DC-sorozatok rendelkezésre állása.](#dc-series-1)

#### <a name="azure-offer-types-supported-by-dc-series"></a>A DC sorozat által támogatott Azure-ajánlattípusok

A DC-sorozat eléréséhez az előfizetésnek fizetős ajánlattípusnak kell lennie, beleértve a pay-As-You-Go vagy Nagyvállalati Szerződés (EA) lehetőséget.  A DC-sorozat által támogatott Azure-ajánlattípusok teljes listájáért tekintse meg a jelenlegi, költségkeret [nélküli ajánlatokat.](https://azure.microsoft.com/support/legal/offer-details)

### <a name="compute-and-memory-specifications"></a>Számítási és memóriasokajátok


|Hardvergeneráció  |Compute  |Memória  |
|:---------|:---------|:---------|
|Gen4     |- Intel® E5-2673 v3 (Haswell) 2,4 GHz-es processzorok<br>- Legfeljebb 24 virtuális mag kiépítése (1 virtuális mag = 1 fizikai mag)  |– 7 GB virtuális magonként<br>– 168 GB-ig|
|Gen5     |**Kiépített számítás**<br>- Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) \* és Intel® 8272CL (Cascade Lake) 2,5 GHz-es \* processzorok<br>- Legfeljebb 80 virtuális mag kiépítése (1 virtuális mag = 1 hiperszál)<br><br>**Kiszolgáló nélküli számítástechnika**<br>- Intel® E5-2673 v4 (Broadwell) 2.3-GHz és Intel® SP-8160 (Skylake)* processzorok<br>- Automatikus skálázás akár 40 virtuális magra (1 virtuális mag = 1 hiperszál)|**Kiépített számítás**<br>– 5,1 GB virtuális magonként<br>– 408 GB-ig<br><br>**Kiszolgáló nélküli számítástechnika**<br>– Automatikus skálázás virtuális magonként 24 GB-ig<br>– Automatikus skálázás legfeljebb 120 GB-ig|
|Fsv2 sorozat     |- Intel® 8168-as (Skylake-) processzorok<br>– Tartós, 3,4 GHz-es processzorral és 3,7 GHz-es maximális egymagos turbo órajel-sebességgel rendelkezik.<br>– Legfeljebb 72 virtuális mag kiépítése (1 virtuális mag = 1 hiperszál)|– 1,9 GB virtuális magonként<br>– 136 GB-ig|
|M sorozat     |- Intel® E7-8890 v3 2,5 GHz és Intel® 8280M 2,7 GHz (Cascade Lake) processzorok<br>- Legfeljebb 128 virtuális mag kiépítése (1 virtuális mag = 1 hiperszál)|– 29 GB virtuális magonként<br>– 3,7 TB-os kiépítés|
|DC sorozat     | - Intel XEON E-2288G processzorok<br>– Az Intel Software Guard bővítmény (Intel SGX))<br>- Legfeljebb 8 virtuális mag kiépítése (1 virtuális mag = 1 fizikai mag) | 4,5 GB virtuális magonként |

\* Az [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dinamikus felügyeleti nézetében az Intel® SP-8160 (Skylake) processzorokat használó adatbázisok hardvergenerációja Gen6-ként jelenik meg, míg az Intel® 8272CL-t (Cascade Lake) használó adatbázisok hardvergenerációja Gen7-ként jelenik meg. Az összes Gen5-adatbázis erőforráskorlátai a processzor típusától (Broadwell, Skylake vagy Cascade Lake) függetlenül azonosak.

Az erőforráskorlátokkal kapcsolatos további információkért lásd: Az egy adatbázisra [(vCore)](resource-limits-vcore-single-databases.md)vonatkozó erőforráskorlátok vagy rugalmas készletek [(vCore) erőforráskorlátai.](resource-limits-vcore-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Hardvergeneráció kiválasztása

A Azure Portal a létrehozáskor kiválaszthatja egy adatbázis vagy készlet hardvergenerációját a SQL Database-ban, vagy módosíthatja egy meglévő adatbázis vagy készlet hardvergenerációját.

**Hardvergeneráció kiválasztása a SQL Database vagy készlet létrehozásakor**

Részletes információkért [lásd: Create a SQL Database.](single-database-create-quickstart.md)

Az Alapvető **beállítások lapon**  válassza az Adatbázis konfigurálása hivatkozást a Számítás **+ tárolás** szakaszban, majd válassza a Konfiguráció **módosítása** hivatkozást:

  ![adatbázis konfigurálása](./media/service-tiers-vcore/configure-sql-database.png)

Válassza ki a kívánt hardvergenerációt:

  ![hardver kiválasztása](./media/service-tiers-vcore/select-hardware.png)


**Meglévő adatbázis vagy készlet hardvergenerációjának SQL Database módosítása**

Adatbázis esetében az Áttekintés lapon válassza a **Tarifacsomag hivatkozást:**

  ![hardver módosítása](./media/service-tiers-vcore/change-hardware.png)

Készlethez az Áttekintés lapon válassza a **Konfigurálás lehetőséget.**

Kövesse a konfiguráció módosítása lépéseit, és válassza ki a hardvergenerációt az előző lépésekben leírtak szerint.

**Hardvergeneráció kiválasztása az új SQL Managed Instance**

Részletes információkért [lásd: Create a SQL Managed Instance.](../managed-instance/instance-create-quickstart.md)

Az Alapvető **beállítások lapon**  válassza az Adatbázis konfigurálása hivatkozást a Számítás **+ tárolás** szakaszban, majd válassza ki a kívánt hardvergenerációt:

  ![konfigurálás SQL Managed Instance](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Meglévő hardvergenerációk hardvergenerációjának SQL Managed Instance**

# <a name="the-azure-portal"></a>[Az Azure Portal](#tab/azure-portal)

A SQL Managed Instance a **Beállítások** szakaszban elhelyezett Tarifacsomag hivatkozásra kattintva

![hardver SQL Managed Instance módosítása](./media/service-tiers-vcore/change-managed-instance-hardware.png)

A Tarifacsomag lapon módosíthatja a hardvergenerációt az előző lépésekben leírtak szerint.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsfájlt:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

További részletekért tekintse meg [a Set-AzSqlInstance parancsot.](/powershell/module/az.sql/set-azsqlinstance)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a következő CLI-parancsot:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

További részletekért tekintse meg az [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) parancsot.

---

### <a name="hardware-availability"></a>Hardver rendelkezésre állása

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

A Gen4 [hardvert jelenleg](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) nem érhetők el az új üzemelő példányok. Minden új adatbázist Gen5 hardveren kell üzembe helyezni.

A Gen5 világszerte minden nyilvános régióban elérhető.

#### <a name="fsv2-series"></a>Fsv2 sorozat

Az Fsv2 sorozat a következő régiókban érhető el: Ausztrália középső régiója, Ausztrália 2. középső régiója, Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Ázsia, Usa keleti régiója, Közép-Franciaország, Közép-India, Korea középső régiója, Dél-Korea, Észak-Európa, Dél-Afrika északi régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA 2. nyugati régiója.


#### <a name="m-series"></a>M sorozat

Az M sorozat a következő régiókban érhető el: USA keleti régiója, Észak-Európa, Nyugat-Európa, USA 2. nyugati régiója.
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

#### <a name="dc-series"></a>DC sorozat

> [!NOTE]
> A DC-sorozat jelenleg nyilvános **előzetes verzióban érhető el.**

A DC-sorozat a következő régiókban érhető el: Közép-Kanada, Kelet-Kanada, USA keleti régiója, Észak-Európa, Egyesült Királyság déli régiója, Nyugat-Európa, USA nyugati régiója.

Ha a DC-sorozatra jelenleg nem támogatott régióban van szüksége, küldjön egy támogatási jegyet [a](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Kérelemkvóta-emelés kérése az Azure SQL Database és a [SQL Managed Instance.](quota-increase-request.md)

## <a name="next-steps"></a>Következő lépések

Első lépések: 
- [Új SQL Database létrehozása a Azure Portal](single-database-create-quickstart.md)
- [Új SQL Managed Instance létrehozása a Azure Portal](../managed-instance/instance-create-quickstart.md)

A díjszabás részleteiért tekintse meg a [Azure SQL Database oldalon.](https://azure.microsoft.com/pricing/details/sql-database/single/)

Az általános célú és az üzleti kritikus szolgáltatási szinteken elérhető számítási és tárolási méretekkel kapcsolatos részletekért lásd:

- [Virtuálismag-alapú erőforráskorlátok a Azure SQL Database.](resource-limits-vcore-single-databases.md)
- [Virtuálismag-alapú erőforráskorlátok a készletbe Azure SQL Database.](resource-limits-vcore-elastic-pools.md)
- [Virtuálismag-alapú erőforráskorlátok a Azure SQL Managed Instance.](../managed-instance/resource-limits.md)
