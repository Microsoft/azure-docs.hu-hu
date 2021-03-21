---
title: Árképzési szintek – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server számítási és tárolási lehetőségeit ismerteti.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 74d2654c54d1ab2ac5d47aa24449c41c20a90b74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791138"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Az egykiszolgálós Azure Database for PostgreSQL tarifacsomagjai

A Azure Database for PostgreSQL-kiszolgálót a három különböző díjszabási szint egyikében hozhatja létre: alapszintű, általános célú és memória optimalizálva. Az árképzési csomagokat a virtuális mag olyan számítási mennyisége különbözteti meg, amely kiépíthető, memóriát virtuális mag, valamint az adatok tárolására szolgáló tárolási technológiát. Az összes erőforrást a PostgreSQL-kiszolgáló szintjén kell kiépíteni. A kiszolgálók egy vagy több adatbázissal rendelkezhetnek.

| Erőforrás/szintek | **Basic** | **Általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Számítási generáció | Gen 4, Gen 5 | Gen 4, Gen 5 | 5. generáció |
| Virtuális mag | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória/virtuális mag | 2 GB | 5 GB | 10 GB |
| Tárterület mérete | 5 GB – 1 TB | 5 GB – 16 TB | 5 GB – 16 TB |
| Adatbázis biztonsági másolatának megőrzési időtartama | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Árképzési szintek kiválasztásához használja a következő táblázatot kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Könnyű számítási és I/O-teljesítményt igénylő munkaterhelések. Ilyenek például a fejlesztéshez és teszteléshez használt kiszolgálók, vagy a kisméretű, ritkán használt alkalmazások. |
| Általános célú | A legtöbb üzleti számítási feladat, amely kiegyensúlyozott számítást és memóriát igényel a méretezhető I/O-átviteli sebességgel. Ilyenek például a web- és mobilalkalmazások üzemeltetésére szolgáló kiszolgálók, valamint az egyéb nagyvállalati alkalmazások.|
| Memóriaoptimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek memóriabeli teljesítményt igényelnek a gyorsabb tranzakció-feldolgozáshoz és a nagyobb egyidejűséghez. Ilyenek például a valós idejű adatokat feldolgozó kiszolgálók és a nagy teljesítményű, tranzakciós vagy elemző alkalmazások.|

A kiszolgáló létrehozása után a virtuális mag, a hardver-létrehozási és az árképzési szint (kivéve az alapszintű és az alapszintű) számát másodpercek alatt módosíthatja. A tárolási mennyiség és a biztonsági mentés megőrzési időszaka egymástól függetlenül is beállítható az alkalmazás leállása nélkül. A biztonsági mentési tár típusa nem módosítható a kiszolgáló létrehozása után. További információ: [Scale Resources (erőforrások méretezése](#scale-resources) ) szakasz.

## <a name="compute-generations-and-vcores"></a>Számítási generációk és virtuális mag

A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. Kelet-Kína 1, Észak-Kína 1, US DoD – középső régió és US DoD – keleti régió az Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokon alapuló Gen 4 logikai processzorokat használ. Minden más régió az Intel E5-2673 v4 (Broadwell) 2,3 GHz-es processzorokon alapuló Gen 5 logikai CPU-ket használ.

## <a name="storage"></a>Tárolás

Az Ön által kiépített tárterület a Azure Database for PostgreSQL-kiszolgáló számára elérhető tárolási kapacitás mennyisége. A tárterületet az adatbázisfájlok, az ideiglenes fájlok, a tranzakciónaplók és a PostgreSQL-kiszolgáló naplófájljai használják. A kiépített tárterület teljes mennyisége határozza meg a kiszolgáló számára elérhető I/O-kapacitást is.

| Tárolási attribútumok | **Basic** | **Általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Alapszintű tárterület | általános célú Storage | általános célú Storage |
| Tárterület mérete | 5 GB – 1 TB | 5 GB – 16 TB | 5 GB – 16 TB |
| Tárolási növekmény mérete | 1 GB | 1 GB | 1 GB |
| IOPS | Változó |3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 20 000 IOPS | 3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 20 000 IOPS |

> [!NOTE]
> A 16TB és a 20 000 IOPS a következő régiókban támogatott: Kelet-Ausztrália, Kelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-USA, Kelet-Kína 2, Észak-Kína 2, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Korea, Dél-Korea, Észak-Észak-Svájc Európa , Nyugat-Svájc, US Gov Kelet, US Gov SouthCentral, US Gov SouthWest, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, az USA nyugati középső régiója, az USA nyugati régiója és az USA 2. nyugati régiója.
>
> Minden más régió támogatja a 4TB és a 6000 IOPS.
>

A kiszolgáló létrehozásakor és után további tárolókapacitást is hozzáadhat, és lehetővé teheti, hogy a rendszer automatikusan növelje a tárterületet a számítási feladatok tárolási feladatainak megfelelően.

>[!NOTE]
> A tárterületet csak felfelé, nem lefelé lehet méretezni.

Az alapszintű csomag nem biztosít IOPS garanciát. A általános célú és a memória optimalizált díjszabási szintjein a IOPS a kiépített tároló méretével, 3:1 arányban méretezhető.

Az I/O-használatot a Azure Portal vagy az Azure CLI-parancsok használatával figyelheti. A figyelni kívánt mérőszámok a [tárolási korlát, a tárolási százalék, a felhasznált tárterület és az IO százalék](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Tárhelykorlát elérése

A 100 GB-nál kisebb kiépített tárolóval rendelkező kiszolgálók csak olvashatók, ha az ingyenes tárterület kevesebb, mint 512 MB vagy 5% a kiépített tároló mérete. A 100 GB-nál nagyobb kiépített tárhellyel rendelkező kiszolgálók csak olvashatónak lesznek jelölve, ha az elérhető tárterület kisebb, mint 5 GB.

Ha például 110 GB tárhellyel rendelkezik, és a tényleges kihasználtság meghaladja az 105 GB-ot, a kiszolgáló csak olvashatóként van megjelölve. Ha 5 GB tárterületet osztott ki, akkor a kiszolgáló csak olvashatóként van megjelölve, ha az ingyenes tárterület 512 MB-nál kevesebbet ér el.

Ha a kiszolgáló csak olvasható, az összes meglévő munkamenet le van választva, és a nem véglegesített tranzakciók vissza lesznek állítva. A későbbi írási műveletek és tranzakciók véglegesítve sikertelenek lesznek. Az összes további olvasási lekérdezés megszakítás nélkül működik.  

Megnövelheti a kiépített tároló mennyiségét a kiszolgálóra, vagy új munkamenetet indíthat az írási és olvasási módban, és elvégezheti az adattárolást az ingyenes tárterület visszaigényléséhez. `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;`A futó az aktuális munkamenetet olvasási írási módba állítja. Az adatok sérülésének elkerülése érdekében ne végezzen írási műveleteket, ha a kiszolgáló még csak olvasható állapotú.

Javasoljuk, hogy kapcsolja be a tárterület automatikus növekedését, vagy hozzon létre egy riasztást, amely értesíti, ha a kiszolgáló tárterülete eléri a küszöbértéket, így elkerülhető a csak olvasható állapot. További információt a [riasztás beállításával](howto-alert-on-metric.md)kapcsolatos dokumentációban talál.

### <a name="storage-auto-grow"></a>Tárterület automatikus növekedése

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló kifogyjon a tárolóból, és csak olvasható legyen. Ha engedélyezve van a tárterület automatikus növekedése, a tárterület automatikusan növekszik a munkaterhelés befolyásolása nélkül. A 100 GB-nál kisebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiépített tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület a kiosztott tárterület mérete meghaladja a 10 GB-ot vagy az 5%-ot. A fent megadott maximális tárolási korlátok érvényesek.

Ha például 1000 GB tárhelyet használ, és a tényleges kihasználtság meghaladja az 950 GB-ot, a kiszolgáló tárterületének mérete az 1050 GB-ra nő. Ha 10 GB tárterületet telepített, akkor a tárterület mérete 15 GB-ra nő, ha kevesebb, mint 1 GB tárterület ingyenes.

Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="backup-storage"></a>Biztonsági mentési tár

A Azure Database for PostgreSQL a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Az ezen összegnél nagyobb mennyiségű biztonsági mentési tárterületért havi GB-ot kell fizetni. Ha például 250 GB tárterülettel rendelkező kiszolgálót hoz létre, akkor a kiszolgáló biztonsági mentései esetében a 250 GB-nyi további tárterület is rendelkezésre áll. A 250 GB-nál nagyobb biztonsági másolatok tárolására a [díjszabási modellnek](https://azure.microsoft.com/pricing/details/postgresql/)megfelelően kerül sor. A biztonsági mentési tárhely használatának befolyásolására, a biztonsági mentési tárolási költségek figyelésére és szabályozására szolgáló tényezők megismeréséhez tekintse meg a [biztonsági mentési dokumentációt](concepts-backup.md).

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után egymástól függetlenül módosíthatja a virtuális mag, a hardverek generációját, az árképzési szintet (kivéve az alapszintű és az alapszintű), a tárterület mennyiségét és a biztonsági mentés megőrzési időtartamát. A biztonsági mentési tár típusa nem módosítható a kiszolgáló létrehozása után. A virtuális mag száma növelhető felfelé vagy lefelé. A biztonsági másolat megőrzési időtartama 7 – 35 nap között méretezhető. A tárterület mérete csak növelni lehet. Az erőforrások méretezése történhet a portálon vagy az Azure CLI-n keresztül. Az Azure CLI-vel történő skálázásra példa: Azure Database for PostgreSQL- [kiszolgáló figyelése és méretezése az Azure CLI használatával](scripts/sample-scale-server-up-or-down.md).

> [!NOTE]
> A tárterület mérete csak növelni lehet. A növekedés után nem térhet vissza kisebb tárterület-méretre.

Ha megváltoztatja a virtuális mag számát, a hardver generálását vagy az árképzési szintet, az eredeti kiszolgáló egy példánya létrejön az új számítási foglalással. Amint a kiszolgáló üzemel, a kapcsolatok átállnak az új kiszolgálóra. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. Ez az időtartam változó, de a legtöbb esetben egy percnél kevesebb időt vesz igénybe.

A tárterület skálázása és a biztonsági mentés megőrzési időtartamának módosítása igaz online művelet. Nincs leállás, és az alkalmazása nincs hatással. A kiépített tároló méretével IOPS a IOPS elérhetővé teheti a kiszolgáló számára a tárterület növelésével.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/PostgreSQL/). A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a kiválasztott beállítások alapján megjeleníti a havi költséget a **díjszabási szintek** lapon. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és **Azure Database for PostgreSQL** a beállítások testreszabásához.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre PostgreSQL-kiszolgálót a portálon](tutorial-design-database-using-azure-portal.md).
- A [szolgáltatás korlátainak](concepts-limits.md)megismerése.
- Ismerje meg, hogyan [bővítheti az olvasási replikákat](howto-read-replicas-portal.md).
