---
title: Az Azure SQL Database erőforráskorlátok – felügyelt példány |} A Microsoft Docs
description: Ez a cikk az Azure SQL Database erőforráskorlátok áttekintést nyújt a felügyelt példányokhoz.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: e091ec29c810fce7a39ad5aa5cc8f0ddae711752
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016407"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Áttekintés az Azure SQL Database felügyelt példányain erőforráskorlátok

Ez a cikk az Azure SQL Database felügyelt példányába erőforráskorlátok áttekintést nyújt, és ismerteti, hogyan hozhat létre az alapértelmezett területi előfizetési korlátozásait növelésére.

> [!NOTE]
> Egyéb felügyelt példány korlátozások is érvényesek, lásd: [Virtuálismag-alapú vásárlási modell](sql-database-managed-instance.md#vcore-based-purchasing-model) és [felügyelt példány szolgáltatásszintek](sql-database-managed-instance.md#managed-instance-service-tiers). Támogatott szolgáltatások és a T-SQL eltérései utasításokat lásd [különbségek](sql-database-features.md) és [T-SQL utasítás támogatása](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>A példányszintű erőforráskorlátok

Felügyelt példány jellemzőit és az adott díjcsomagtól függ az alapul szolgáló infrastruktúra és architektúra erőforráskorlátok rendelkezik. Korlátok hardver és a szolgáltatási szint függenek.

### <a name="hardware-generation-characteristics"></a>Hardverjellemzők generáció

Az Azure SQL Database felügyelt példánya is telepíthető a két hardver generációja (Gen4 és Gen5). Hardvergenerációk az alábbi táblázatban ismertetett más jellemzőkkel rendelkeznek:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardver | Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokkal, SSD virtuális mag csatolt = 1 PP (fizikai mag) | Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorokkal, gyors NVMe SSD, virtuális mag = 1. LP (a hyper-szál) |
| Virtuális magok | 8, 16, 24 virtuális mag | 8, 16, 24, 32, 40, 64, 80 virtuális magok |
| Memória | 7 GB / virtuális mag | 5.1 GB / virtuális mag |
| Maximális In-Memory OLTP memória | 3 GB / virtuális mag | 2.6-os GB / virtuális mag |
| Maximális Egypéldányos tárolás (általános célú) |  8 TB | 8 TB |
| Maximális Egypéldányos tárolás (üzletileg kritikus) | 1 TB | 1 TB-os, 2 TB vagy 4 TB-os attól függően, a magok számát |

### <a name="service-tier-characteristics"></a>Szolgáltatási szint tulajdonságok

Felügyelt példány két szolgáltatási csomagban – általános célú és a kritikus fontosságú üzleti rendelkezik. Ezek a csomagok különböző képességeket biztosítanak, az alábbi táblázatban leírtak szerint:

| **Funkció** | **Általános célú** | **Üzletileg kritikus** |
| --- | --- | --- |
| Virtuális magok száma\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Memória | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB – 408 GB (5.1 GB/virtuális mag) | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB – 408 GB (5.1 GB/virtuális mag) |
| Maximális példányméret storage | 8 TB | Gen4: 1 TB <br/> Gen5: <br/>– 1 TB-os 8, 16 virtuális mag<br/>– A 24 virtuális mag 2 TB<br/>– 4 TB-os 32, 40, 64, 80 virtuális magok |
| Maximális tárterület adatbázisonként | Határozza meg a maximális tárhelyméretet a példány | Határozza meg a maximális tárhelyméretet a példány |
| Egy példány adatbázisok maximális száma | 100 | 100 |
| Maximális adatbázisfájlok példányonként | Legfeljebb 280 | – 32 767 fájlt adatbázisonként |
| Adatok/Log/iops-érték (becsült) | 500 – 7500 fájlonként<br/>\*[A fájl mérete attól függ](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K - 110 K (1375/vCore) |
| Napló átviteli sebesség | 3MB/s / virtuális mag<br/>Maximális 22-es MB/s-példányonként | 4 MB/s / virtuális mag<br/>Maximális száma 48 MB/s-példányonként|
| A fájlmegosztásra (becsült) | 100 - fájlonként 250 MB/s<br/>\*[A fájl mérete attól függ](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | |
| IO-késés (becsült) | 5-10 ms | 1-2 ms |
| Max. tempDB mérete | 192 - 1,920 GB (24 GB / virtuális mag) | Nincsenek megkötések - korlátozza a maximális példányméret |

**Megjegyzések**:

- Adat- és a naplófájlok méretét a felhasználó- és rendszer-adatbázisokat a storage példányméret, a rendszer összehasonlítja a maximális méretkorlát szerepelnek. Használat <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> rendszernézet meghatározni az összes adatbázis által felhasznált terület. Hibanaplók nem megőrzött és a méret nem tartalmazza. Biztonsági mentések nem szerepelnek a tárterület méretét.
- Adatátviteli sebessége és IOPS is függ az oldal méretét, amely a felügyelt példány explicit módon nem korlátozódik.

## <a name="supported-regions"></a>Támogatott régiók

Csak a felügyelt Instanced hozható létre [támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Ha szeretne egy felügyelt példány létrehozása jelenleg nem támogatott a régióban, akkor [küldési támogatási kérést az Azure Portalon keresztül](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Támogatott előfizetéstípusok

Felügyelt példány üzembe helyezési jelenleg csak a következő típusú előfizetések a használatát támogatja:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Nagyvállalati fejlesztés és tesztelés](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> Ez a korlátozás csak átmenetileg létezik. Új előfizetés-típusok a jövőben engedélyezve lesz.

## <a name="regional-resource-limitations"></a>Regionális erőforrás-korlátozások

Támogatott előfizetéstípusok erőforrások régiónként csak korlátozott számú is tartalmazhat. Felügyelt példány két alapértelmezett korlátokkal rendelkeznek a Azure-régiónként egy előfizetés típusú típusától függően:

- **Alhálózathoz megadott korlátot**: Alhálózatok, ahol egy adott régióban felügyelt példányok üzembe helyezése maximális számát.
- **A maximális szám példány**: A maximális száma, amely telepíthető egy adott régióban.

> [!Note]
> Ezek a korlátok alapértelmezett beállításokat, és nem technikai korlátai. A korlátok lehet nagyobb az igény szerinti speciális létrehozásával [támogatási kérést az Azure Portalon](#obtaining-a-larger-quota-for-sql-managed-instance) Ha a jelenlegi régióban több felügyelt példány van szüksége. Alternatív megoldásként a támogatási kérések elküldése nélkül létrehozhat új felügyelt példányok egy másik Azure-régióban.

Az alábbi táblázatban láthatók a támogatott előfizetések alapértelmezett regionális korlátozásait:

|Előfizetés típusa| Felügyelt példány alhálózatok maximális száma | Példányok maximális száma |Maximális száma a csoportházirend által felügyelt példány *|BC maximális száma a felügyelt példány *|
| :---| :--- | :--- |:--- |:--- |
|Utólagos, használatalapú fizetés|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|Fejlesztés/tesztelés használatalapú fizetéssel|1*|4*|4*|1*|
|Enterprise Dev/Test|1*|4*|4*|1*|
|Nagyvállalati szerződés|3**|12**|12**|3**|

\* Vagy telepítheti 1 BC vagy az egyik alhálózat 4 GP-példány, hogy az alhálózat "példány egységek" száma soha nem meghaladja a 4.

** A példányok egy szolgáltatási rétegben található maximális száma vonatkozik, ha nincsenek példányok egy másik szolgáltatási rétegben található. Abban az esetben, ha azt tervezi, a csoportházirend és BC példányok ugyanazon az alhálózaton belül vegyesen, használja a következő szakasz referenciaként engedélyezett kombinációját. Egyszerű szabály alhálózatok száma nem haladhatja meg a 3, és a példány egységek száma nem haladhatja meg a 12.



> [!IMPORTANT]
> A telepítések megtervezésekor vegye figyelembe, hogy a kritikus fontosságú üzleti (BC) példány (miatt hozzáadott redundancia) általában felhasznál a nagyobb kapacitást, mint egy általános célú (GP) példány x 4. Igen, a számítások, 1 a csoportházirend-példány = 1 példány egység és 1 BC példány = 4 példány egység. Egyszerűsítése érdekében a felhasználási elemzés, szemben az alapértelmezés szerinti korlátozásoknak, a példány egységek összesítése a régióban, ahol felügyelt példányok üzembe helyezése összes alhálózat között, valamint az eredményeket hasonlítsa össze az előfizetés-típus példánykorlátok egység.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Vegyes általános célú és az üzletileg kritikus példányok üzembe helyezéséhez stratégiák

[Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) előfizetések kombinációja csoportházirend és BC-példányok is rendelkezhet. Vannak azonban bizonyos az alhálózatokon a példányok elhelyezésére vonatkozó korlátozások.

> [!Note]
> [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/) és [Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) típusú előfizetésessel is rendelkezik, vagy egy fontos üzleti vagy akár 4 általános célú példányok.

A következő példákban üzembe helyezési esetekre, nem üres alhálózattal, és vegyes GP és a BC szolgáltatásszintek.

|Alhálózatok száma|1. alhálózat|2. alhálózat|3. alhálózat|
|:---|:---|:---|:---|
|1.|1 BC és legfeljebb 8 GP<br>2 BC és akár 4 általános védelmi|–| –|
|2|0 BC, akár 4 általános védelmi|1 BC, akár 4 általános védelmi<br>2 BC, 0 A CSOPORTHÁZIREND|–|
|2|1 BC, 0 GP|0 BC, legfeljebb 8 GP<br>1 BC, akár 4 általános védelmi|–|
|2|2 BC, 0 A CSOPORTHÁZIREND|0 BC, akár 4 általános védelmi|–|
|3|1 BC, 0 GP|1 BC, 0 GP|0 BC, akár 4 általános védelmi|
|3|1 BC, 0 GP|0 BC, akár 4 általános védelmi|0 BC, akár 4 általános védelmi|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL felügyelt példánya a nagyobb kvótát beszerzése

Ha több felügyelt példány az aktuális régióban, elküldheti a támogatási kérelmet a kvóta az Azure portal használatával kiterjesztheti.
A folyamat lehet beszerezni a nagyobb kvótát kezdeményezéséhez:

1. Nyissa meg **súgó + támogatás**, és kattintson a **új támogatási kérelem**.

   ![Súgó és támogatás](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Az alapismeretek lapon az új támogatási kérelem:
   - A **Problématípus**válassza **szolgáltatás és az előfizetések korlátai (kvóták)**.
   - Az **Előfizetés** beállításnál válassza ki az előfizetését.
   - A **kvótatípus**válassza **SQL Database felügyelt példányain**.
   - A **támogatási csomag**, válassza ki a támogatási csomagot.

     ![Probléma típusa kvóta](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kattintson a **tovább**.
4. A probléma lapon az új támogatási kérelem:
   - A **súlyossági**, válassza ki a súlyossági szintet a problémát.
   - A **részletek**, adjon meg további információt a problémáról, beleértve a hibaüzeneteket is.
   - A **fájlfeltöltés**, és azok bővebb fájl csatolása (legfeljebb 4 MB).

     ![Probléma részletei](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Egy érvényes kérelmet kell tartalmaznia:
     > - Régió, hogy melyik előfizetéssel korlátot növelni kell
     > - Példányok száma a szolgáltatási rétegben lévő alhálózatok a kvóta növelése után szükséges száma (ha az egyik létező alhálózathoz ki kell bővíteni kell növelése
     > - Szükséges új alhálózatok számát és a példányok száma a szolgáltatási szinten belül az új alhálózatokra teljes száma (ha üzembe kell helyeznie az új alhálózatokra felügyelt példányok).

5. Kattintson a **tovább**.
6. Adja meg a kapcsolattartási adatai lap az új támogatási kérelmet az elsődleges kapcsolattartási módszert (e-mail cím vagy telefonszám) és a kapcsolattartási adatait.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

- Felügyelt példánnyal kapcsolatos további információkért lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Díjszabási információkért tekintse meg a [SQL Database felügyelt példányain díjszabás](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Ismerje meg, hogyan hozhat létre az első felügyelt példányhoz, lásd: [gyors üzembe helyezési útmutató](sql-database-managed-instance-get-started.md).
