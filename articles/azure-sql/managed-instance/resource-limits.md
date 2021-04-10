---
title: Erőforráskorlátok
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk áttekintést nyújt az Azure SQL felügyelt példányának erőforrás-korlátairól.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: ee0c673027892e6d3ee55b6ec5f67a30d88be683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780344"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>A felügyelt Azure SQL-példány erőforráskorlátainak áttekintése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk áttekintést nyújt az Azure SQL felügyelt példányainak technikai jellemzőiről és erőforrás-korlátairól, és információt nyújt arról, hogyan kérheti le a határértékek növelését.

> [!NOTE]
> A támogatott funkciók és a T-SQL utasítások közötti különbségekért lásd a [funkciók](../database/features-comparison.md) és a [t-SQL-utasítások támogatását](transact-sql-tsql-differences-sql-server.md). A Azure SQL Database és az SQL felügyelt példányának szolgáltatási szintjei közötti általános különbségekért lásd a [szolgáltatási szintek összehasonlítását](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Hardver generálási jellemzői

Az SQL felügyelt példányának jellemzői és erőforrás-korlátai a mögöttes infrastruktúrától és architektúrától függenek. Az SQL felügyelt példánya két hardveres generációra telepíthető: Gen4 és Gen5. A hardveres generációk különböző jellemzőkkel rendelkeznek, az alábbi táblázatban leírtak szerint:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **Hardver** | Intel® E5-2673 v3 (Haswell) 2,4 GHz-es processzorok, csatlakoztatott SSD virtuális mag = 1 PP (fizikai mag) | Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) és Intel® 8272CL (Cascade Lake) 2,5 GHz processzorok, gyors NVMe SSD, virtuális mag = 1 LP (Hyper-thread) |
| **Virtuális magok száma** | 8, 16, 24 virtuális mag | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| **Maximális memória (memória/mag arány)** | 7 GB/virtuális mag<br/>További virtuális mag hozzáadásával további memóriát érhet el. | 5,1 GB/virtuális mag<br/>További virtuális mag hozzáadásával további memóriát érhet el. |
| **Maximális In-Memory OLTP memória** | Példány korlátja: 1 – 1,5 GB/virtuális mag| Példány korlátja: 0,8 – 1,65 GB/virtuális mag |
| **Példányok maximálisan fenntartott tárterülete** |  Általános célú: 8 TB<br/>Üzletileg kritikus: 1 TB | Általános célú: 8 TB<br/> Üzletileg kritikus 1 TB, 2 TB vagy 4 TB a magok számától függően |

> [!IMPORTANT]
> - A Gen4 hardverek fokozatos kiépítése folyamatban van, és az új telepítések esetén már nem érhető el. A felügyelt SQL-példányok minden új példányát telepíteni kell a Gen5 hardveren.
> - Érdemes [áthelyezni az SQL felügyelt példányának példányát az 5. generációs](../database/service-tiers-vcore.md) hardverre, hogy a virtuális mag és a tárterület méretezhetősége, a gyorsított hálózatkezelés, a legjobb IO-teljesítmény és a minimális késések széles skáláját tapasztalja.

### <a name="in-memory-oltp-available-space"></a>Memóriában tárolt OLTP szabad területe 

[Üzletileg kritikus](../database/service-tier-business-critical.md) szolgáltatási szinten a memóriában lévő OLTP-terület mennyisége a virtuális mag és a hardveres generáció számától függ. A következő táblázat a memóriában tárolt OLTP-objektumokhoz használható memória korlátozásait sorolja fel.

| Memóriában tárolt OLTP terület  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 virtuális mag  | 3,14 GB | |   
| 8 virtuális mag  | 6,28 GB | 8 GB |
| 16 virtuális mag | 15,77 GB | 20 GB |
| 24 virtuális mag | 25,25 GB | 36 GB |
| 32 virtuális mag | 37,94 GB | |
| 40 virtuális mag | 52,23 GB | |
| 64 virtuális mag | 99,9 GB    | |
| 80 virtuális mag | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Szolgáltatási szintek jellemzői

Az SQL felügyelt példányának két szolgáltatási szintje van: [általános célú](../database/service-tier-general-purpose.md) és [üzletileg kritikus](../database/service-tier-business-critical.md). Ezek a szintek [különböző képességeket](../database/service-tiers-general-purpose-business-critical.md)biztosítanak, az alábbi táblázatban leírtak szerint.

> [!Important]
> A üzletileg kritikus Service-szintű szolgáltatás a felügyelt SQL-példány (másodlagos replika) egy további beépített példányát biztosítja, amely csak olvasható számítási feladatokhoz használható. Ha elkülönítheti az írási és olvasási lekérdezéseket és a csak olvasható/analitikai/jelentéskészítési lekérdezéseket, akkor a virtuális mag és a memóriát is megkezdheti ugyanarra az árakra. Előfordulhat, hogy a másodlagos replika néhány másodperccel az elsődleges példány mögött van, ezért olyan jelentéskészítési/elemzési munkaterhelések kiszervezésére lett kialakítva, amelyeknek nincs szükségük pontos aktuális állapotára. Az alábbi táblázatban a **csak olvasható lekérdezések** a másodlagos replikán végrehajtott lekérdezések.

| **Szolgáltatás** | **Általános célú** | **Üzletileg kritikus** |
| --- | --- | --- |
| Virtuális magok száma\* | Gen4:8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*A csak olvasható lekérdezések esetében azonos számú virtuális mag van hozzárendelve. |
| Maximális memória | Gen4:56 GB – 168 GB (7GB/virtuális mag)<br/>Gen5:20,4 GB – 408 GB (5.1 GB/virtuális mag)<br/>További virtuális mag hozzáadásával további memóriát érhet el. | Gen4:56 GB – 168 GB (7GB/virtuális mag)<br/>Gen5:20,4 GB-408 GB (5.1 GB/virtuális mag) olvasási és írási lekérdezésekhez<br/>+ további 20,4 GB – 408 GB (5.1 GB/virtuális mag) a csak olvasható lekérdezésekhez.<br/>További virtuális mag hozzáadásával további memóriát érhet el. |
| Példány maximális tárolási mérete (fenntartott) | -2 TB 4 virtuális mag (csak Gen5)<br/>– 8 TB más méretekben | Gen4:1 TB <br/> Gen5 <br/>-1 TB 4, 8, 16 virtuális mag<br/>-2 TB 24 virtuális mag<br/>-4 TB 32, 40, 64, 80 virtuális mag |
| Adatbázisok maximális mérete | Az aktuálisan elérhető példány mérete (legfeljebb 2 TB-8 TB a virtuális mag számától függően). | Akár jelenleg elérhető példány mérete (max. 1 TB-4 TB a virtuális mag számától függően). |
| Maximális tempDB-méret | Legfeljebb 24 GB/virtuális mag (96-1 920 GB) és jelenleg elérhető példányok tárolási mérete.<br/>További virtuális mag hozzáadásával további TempDB lemezterületet érhet el.<br/> A naplófájl mérete legfeljebb 120 GB lehet.| Az aktuálisan elérhető példányok tárolási mérete. |
| Adatbázisok maximális száma egy példányon | 100 felhasználói adatbázisok, kivéve, ha elérte a példány tárolási méretének korlátját. | 100 felhasználói adatbázisok, kivéve, ha elérte a példány tárolási méretének korlátját. |
| Adatbázisfájlok maximális száma egy példányban | Akár 280-ig, kivéve, ha elérte a példány tárolási méretét vagy az [Azure Premium lemez tárterületének lefoglalási](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) korlátját. | 32 767 fájl/adatbázis, kivéve, ha elérte a példány tárolási méretének korlátját. |
| Az adatfájlok maximális mérete | A jelenleg elérhető példányok tárolási mérete (max. 2 TB-8 TB) és az [Azure Premium Disk Storage kiosztási területe](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)korlátozódik. | A jelenleg elérhető példányok tárolási méretére korlátozódik (legfeljebb 1 TB-4 TB). |
| Naplófájl maximális mérete | Legfeljebb 2 TB és jelenleg elérhető példány tárolási mérete. | Legfeljebb 2 TB és jelenleg elérhető példány tárolási mérete. |
| Adat/napló IOPS (hozzávetőleges) | Akár 30-40 K IOPS *, 500-7500/fájl<br/>\*[A fájlméret növelésével további IOPS érhet el](#file-io-characteristics-in-general-purpose-tier)| 16 k-320 K (4000 IOPS/virtuális mag)<br/>További virtuális mag hozzáadásával jobb i/o-teljesítményt érhet el. |
| Napló írási átviteli korlátja (/példány) | 3 MB/s/virtuális mag<br/>Maximális 120 MB/s/példány<br/>22-65 MB/s/DB<br/>\*[A fájlméret növelése jobb i/o-teljesítmény eléréséhez](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s/virtuális mag<br/>Maximális 96 MB/s |
| Adatátviteli sebesség (hozzávetőleges) | 100 – 250 MB/s/fájl<br/>\*[A fájlméret növelése jobb i/o-teljesítmény eléréséhez](#file-io-characteristics-in-general-purpose-tier) | Nem korlátozott. |
| Tárolási IO-késés (hozzávetőleges) | 5-10 MS | 1-2 MS |
| Memóriabeli OLTP | Nem támogatott | Elérhető, [a méret a virtuális mag számától függ](#in-memory-oltp-available-space) . |
| Munkamenetek maximális száma | 30000 | 30000 |
| Egyidejű feldolgozók maximális száma (kérelem) | Gen4: 210 x virtuális magok száma + 800<br>Gen5: 105 x virtuális magok száma + 800 | Gen4: 210 x virtuális magok száma + 800<br>Gen5: 105 x virtuális magok száma + 800 |
| [Írásvédett replikák](../database/read-scale-out.md) | 0 | 1 (az ár tartalmazza) |
| Számítási elkülönítés | A Gen5 nem támogatott, mert általános célú példányok megoszthatják a fizikai hardvereszközöket más példányokkal<br/>Az Gen4 nem támogatott az elavultság miatt|Gen5<br/>– 40, 64, 80 virtuális mag esetén támogatott<br/>-más méretek esetében nem támogatott<br/><br/>Az Gen4 nem támogatott az elavultság miatt|


Néhány további szempont: 

- A **jelenleg elérhető példányok tárolási mérete** a fenntartott példányok méretének és a felhasznált tárolóhelynek a különbsége.
- A felhasználói és a rendszeradatbázisokban lévő adatfájlok és a naplófájlok mérete is szerepel a tárolók maximális méretével összehasonlítva. A [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) rendszernézet segítségével meghatározhatja az adatbázisok által felhasznált teljes területet. A hibanapló nem marad meg, és nem szerepel a méretben. A tárolók mérete nem tartalmazza a biztonsági mentéseket.
- Az átviteli sebesség és a IOPS az általános célú szinten attól függ, hogy az SQL felügyelt példánya nem kifejezetten korlátozza a [fájlméretet](#file-io-characteristics-in-general-purpose-tier) .
  Egy másik Azure-régióban is létrehozhat egy további olvasható replikát az [automatikus feladatátvételi csoportok](../database/auto-failover-group-configure.md) használatával
- A példányok maximális IOPS a fájlok elrendezésével és a számítási feladatok eloszlásával függ. Tegyük fel például, hogy ha 7 x TB-os fájlt hoz létre, amely Max 5K IOPS-t és 7 kis fájlt (128 GB-nál kisebb) a 500 38500 IOPS Vegye figyelembe, hogy a rendszer néhány IOPS is használ az automatikus biztonsági mentéshez.

Ebben a cikkben további információt talál az [SQL felügyelt példány-készletek erőforrás-korlátairól](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>A fájl i/o-jellemzői általános célú szinten

A általános célú szolgáltatási szinten minden adatbázisfájl dedikált IOPS és átviteli sebességet kap, amely a fájlmérettől függ. A nagyobb fájlok több IOPS és átviteli sebességet kapnak. Az adatbázisfájlok IO-jellemzői az alábbi táblázatban láthatók:

| Fájlméret | >= 0 és <= 128 GiB | >128 és <= 512 GiB | >0,5 és <= 1 TiB    | >1 és <= 2 TiB    | >2 és <= 4 TiB | >4 és <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS       | 500   | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Átviteli sebesség fájlonként | 100 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Ha bizonyos adatbázisfájlok esetében magas IO-késleltetést észlel, vagy ha azt látja, hogy a IOPS/átviteli sebesség eléri a korlátot, [a fájlméret növelésével](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)növelheti a teljesítményt.

A napló írási sebességének maximális átviteli sebessége (lásd a fenti értékeket (pl. 22 MB/s), ezért előfordulhat, hogy a naplófájlban nem érhető el a maximális fájl, mert a példány átviteli sebességének korlátja.

## <a name="supported-regions"></a>Támogatott régiók

A felügyelt SQL-példányok csak a [támogatott régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)hozhatók létre. Ha az SQL felügyelt példányt olyan régióban szeretné létrehozni, amely jelenleg nem támogatott, akkor [a Azure Portalon keresztül küldhet támogatási kérést](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az SQL felügyelt példány jelenleg csak a következő típusú előfizetések esetében támogatja a telepítést:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőalapú szolgáltató (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Előfizetések havi Azure-Kredittel a Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionális erőforrásokra vonatkozó korlátozások

> [!Note]
> Az előfizetések régiójának rendelkezésre állásával kapcsolatos legfrissebb információkért tekintse meg a [régió kiválasztása](https://aka.ms/sqlcapacity)című témakört.

A támogatott előfizetési típusok régiónként korlátozott számú erőforrást tartalmazhatnak. A felügyelt SQL-példányok Azure-régiónként két alapértelmezett korláttal rendelkeznek (ez igény szerint növelhető, ha egy speciális [támogatási kérést hoz létre a Azure Portal az](../database/quota-increase-request.md) előfizetés típusától függően:

- **Alhálózat korlátja**: azon alhálózatok maximális száma, amelyekben az SQL felügyelt példány példányai egyetlen régióban vannak üzembe helyezve.
- **virtuális mag-egység korlátja**: az egyetlen régió összes példányán üzembe helyezhető virtuális mag egységek maximális száma. Az egyik GP-virtuális mag egy virtuális mag egységet használ, és az egyik BC-virtuális mag 4 virtuális mag-egységet vesz igénybe. A példányok teljes száma nincs korlátozva, amíg az virtuális mag-egység korlátján belül van.

> [!Note]
> Ezek a korlátok alapértelmezett beállítások, és nem technikai korlátozások. A határértékek igény szerinti növeléséhez hozzon létre egy speciális [támogatási kérelmet a Azure Portal,](../database/quota-increase-request.md) ha az aktuális régióban több példányra van szüksége. Alternatív megoldásként új SQL-példányokat is létrehozhat egy másik Azure-régióban a támogatási kérések küldése nélkül.

A következő táblázat a támogatott előfizetési típusok **alapértelmezett regionális korlátait** mutatja be (az alapértelmezett határértékek az alább ismertetett támogatási kérelem használatával bővíthetők):

|Előfizetés típusa| SQL felügyelt példányok alhálózatai maximális száma | Virtuális mag egységek maximális száma * |
| :---| :--- | :--- |
|Utólagos elszámolás|3|320|
|CSP |8 (15 egyes régiókban * *)|960 (1440 egyes régiókban * *)|
|Utólagos elszámolású fejlesztési/tesztelési funkciók|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 egyes régiókban * *)|960 (1440 egyes régiókban * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional és MSDN platformok|2|32|

\* Az üzembe helyezések megtervezése során vegye figyelembe, hogy üzletileg kritikus (BC) szolgáltatási szintet négy (4) alkalommal kell virtuális mag, mint a általános célú (GP) szolgáltatási szintet. Például: 1 GP virtuális mag = 1 virtuális mag egység és 1 BC virtuális mag = 4 virtuális mag. Ha egyszerűsíteni szeretné a használati elemzést az alapértelmezett korlátokkal, foglalja össze a virtuális mag egységeket azon régió összes alhálózatán, amelyben az SQL felügyelt példánya telepítve van, és hasonlítsa össze az eredményeket az előfizetési típushoz tartozó példány-egység korlátaival. A **virtuális mag-egységek maximális száma** a régió minden előfizetésére érvényes. Az egyes alhálózatokon nincs korlát, kivéve, hogy a több alhálózaton üzembe helyezett összes virtuális mag összegének kisebbnek vagy egyenlőnek kell lennie a **virtuális mag egységek maximális számával**.

\*\* A nagyobb alhálózat-és virtuális mag korlátozások a következő régiókban érhetők el: Kelet-Ausztrália, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, Dél-Európa, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, USA 2. nyugati régiója.

> [!IMPORTANT]
> Ha a virtuális mag és az alhálózati korlát értéke 0, az azt jelenti, hogy az előfizetése típusának alapértelmezett területi korlátja nincs beállítva. A kvóta-növelési kérést is használhatja az előfizetések hozzáférésének adott régióban való beszerzéséhez, ugyanezen eljárással, amely a szükséges virtuális mag és alhálózati értékeket is megadja.

## <a name="request-a-quota-increase"></a>Kvóta növelésének kérése

Ha az aktuális régiókban több példányra van szüksége, küldjön egy támogatási kérést a kvóta kiterjesztéséhez a Azure Portal használatával. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](../database/quota-increase-request.md).

## <a name="next-steps"></a>Következő lépések

- További információ az SQL felügyelt példányáról: [Mi az SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- A díjszabással kapcsolatos információkért lásd: az [SQL felügyelt példányának díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Ha meg szeretné tudni, hogyan hozhatja létre az első SQL felügyelt példányát, tekintse meg [a rövid útmutató útmutatóját](instance-create-quickstart.md).
