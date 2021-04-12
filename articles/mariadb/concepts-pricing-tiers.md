---
title: Tarifacsomagok – Azure Database for MariaDB
description: Ismerkedjen meg a Azure Database for MariaDB különböző díjszabási szintjeivel, beleértve a számítási generációkat, a tárolási típusokat, a tárterület méretét, a virtuális mag, a memóriát és a biztonsági másolatok megőrzési időszakait.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 92803c1e3062fb9f093012db3e3637746dedf87f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107040"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Árképzési szintek Azure Database for MariaDB

A Azure Database for MariaDB-kiszolgálót a három különböző díjszabási szint egyikében hozhatja létre: alapszintű, általános célú és memória optimalizálva. Az árképzési csomagokat a virtuális mag olyan számítási mennyisége különbözteti meg, amely kiépíthető, memóriát virtuális mag, valamint az adatok tárolására szolgáló tárolási technológiát. Az összes erőforrást a MariaDB-kiszolgáló szintjén kell kiépíteni. A kiszolgálók egy vagy több adatbázissal rendelkezhetnek.

| Erőforrás | **Basic** | **Általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Számítási generáció | 5. generáció |5. generáció | 5. generáció |
| Virtuális mag | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória/virtuális mag | 2 GB | 5 GB | 10 GB |
| Tárterület mérete | 5 GB – 1 TB | 5 GB – 4 TB | 5 GB – 4 TB |
| Adatbázis biztonsági másolatának megőrzési időtartama | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Árképzési szintek kiválasztásához használja a következő táblázatot kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Könnyű számítási és I/O-teljesítményt igénylő munkaterhelések. Ilyenek például a fejlesztéshez és teszteléshez használt kiszolgálók, vagy a kisméretű, ritkán használt alkalmazások. |
| Általános célú | A legtöbb üzleti számítási feladat, amely kiegyensúlyozott számítást és memóriát igényel a méretezhető I/O-átviteli sebességgel. Ilyenek például a web- és mobilalkalmazások üzemeltetésére szolgáló kiszolgálók, valamint az egyéb nagyvállalati alkalmazások.|
| Memóriaoptimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek memóriabeli teljesítményt igényelnek a gyorsabb tranzakció-feldolgozáshoz és a nagyobb egyidejűséghez. Ilyenek például a valós idejű adatokat feldolgozó kiszolgálók és a nagy teljesítményű, tranzakciós vagy elemző alkalmazások.|

A kiszolgáló létrehozása után a virtuális mag száma és az árképzési szint (kivéve az alapszintű és az alapszintű) módosítható másodperceken belül. A tárolási mennyiség és a biztonsági mentés megőrzési időszaka egymástól függetlenül is beállítható az alkalmazás leállása nélkül. A biztonsági mentési tár típusa nem módosítható a kiszolgáló létrehozása után. További információ: [Scale Resources (erőforrások méretezése](#scale-resources) ) szakasz.

## <a name="compute-generations-and-vcores"></a>Számítási generációk és virtuális mag

A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. A Gen 5 logikai CPU-alapú Intel E5-2673 v4 (Broadwell) 2,3-GHz-es processzorok.

## <a name="storage"></a>Tárolás

Az Ön által kiépített tárterület a Azure Database for MariaDB-kiszolgáló számára elérhető tárolási kapacitás mennyisége. A tároló az adatbázisfájlok, az ideiglenes fájlok, a tranzakciós naplók és a MariaDB-kiszolgáló naplóihoz használható. A kiépített tárterület teljes mennyisége határozza meg a kiszolgáló számára elérhető I/O-kapacitást is.

| Tárolási attribútumok   | Alapszintű | Általános célú | Memóriaoptimalizált |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Alapszintű tárterület | általános célú Storage | általános célú Storage |
| Tárterület mérete | 5 GB – 1 TB | 5 GB – 16 TB | 5 GB – 16 TB |
| Tárolási növekmény mérete | 1 GB | 1 GB | 1 GB |
| IOPS | Változó |3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 6000 IOPS | 3 IOPS/GB<br/>Minimális 100 IOPS<br/>Max. 6000 IOPS |

>[!IMPORTANT]
> A 16TB és a 20 000 IOPS a következő régiókban támogatott: USA keleti régiója, USA 2. keleti régiója, az USA középső régiója, Dél-Brazília, USA nyugati régiója, Észak-Európa, az USA déli középső régiója, Észak-Európa, Nyugat-Európa, Dél-Kelet-Ausztrália Korea, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Délkelet-Ázsia, Kelet-Ázsia, Kelet-Japán, Nyugat-Korea , Kelet-Kanada, Közép-Kanada.
> Minden más régió akár 4TB, akár 6000 IOPS is támogat.

A kiszolgáló létrehozásakor és után további tárolókapacitást is hozzáadhat, és lehetővé teheti, hogy a rendszer automatikusan növelje a tárterületet a számítási feladatok tárolási feladatainak megfelelően.

>[!NOTE]
> A tárterületet csak felfelé, nem lefelé lehet méretezni.

Az alapszintű csomag nem biztosít IOPS garanciát. A általános célú és a memória optimalizált díjszabási szintjein a IOPS a kiépített tároló méretével, 3:1 arányban méretezhető.

Az I/O-használatot a Azure Portal vagy az Azure CLI-parancsok használatával figyelheti. A figyelni kívánt mérőszámok a [tárolási korlát, a tárolási százalék, a felhasznált tárterület és az IO százalék](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Tárhelykorlát elérése

Azok a kiszolgálók, amelyek legfeljebb 100 GB kiépített tárterülettel rendelkeznek, csak olvashatónak lesznek jelölve, ha az elérhető tárterület kisebb, mint a kiépített tárhely méretének 5%-a. A 100 GB-nál nagyobb kiépített tárhellyel rendelkező kiszolgálók csak olvashatónak lesznek jelölve, ha az elérhető tárterület kisebb, mint 5 GB.

Ha például 110 GB tárhellyel rendelkezik, és a tényleges kihasználtság meghaladja az 105 GB-ot, a kiszolgáló csak olvashatóként van megjelölve. Ha 5 GB tárterületet osztott ki, akkor a kiszolgáló csak olvashatóként van megjelölve, ha az ingyenes tárterület 256 MB-nál kevesebbet ér el.

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. A kiépített tárterület növelése után a kiszolgáló ismét készen fog állni az írási tranzakciók elfogadására.

Javasoljuk, hogy kapcsolja be a tárterület automatikus növekedését, vagy hozzon létre egy riasztást, amely értesíti, ha a kiszolgáló tárterülete eléri a küszöbértéket, így elkerülhető a csak olvasható állapot. További információt a [riasztás beállításával](howto-alert-metric.md)kapcsolatos dokumentációban talál.

### <a name="storage-auto-grow"></a>Tárterület automatikus növekedése

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló kifogyjon a tárolóból, és csak olvasható legyen. Ha engedélyezve van a tárterület automatikus növekedése, a tárterület automatikusan növekszik a munkaterhelés befolyásolása nélkül. A 100 GB-nál kisebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-kal nő, ha az ingyenes tárterület a kiépített tároló 10%-a alá esik. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete nem éri el a kiosztott tárterület méretének 10 GB-ot. A fent megadott maximális tárolási korlátok érvényesek.

Ha például 1000 GB tárhelyet használ, és a tényleges kihasználtság meghaladja az 990 GB-ot, a kiszolgáló tárterületének mérete az 1050 GB-ra nő. Ha 10 GB tárterületet telepített, akkor a tárterület mérete 15 GB-ra nő, ha kevesebb, mint 1 GB tárterület ingyenes.

Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="backup"></a>Backup

A Azure Database for MariaDB a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Az ezen összegnél nagyobb mennyiségű biztonsági mentési tárterületért havi GB-ot kell fizetni. Ha például 250 GB tárterülettel rendelkező kiszolgálót hoz létre, akkor a kiszolgáló biztonsági mentései esetében a 250 GB-nyi további tárterület is rendelkezésre áll. A 250 GB-nál nagyobb biztonsági másolatok tárolására a [díjszabási modellnek](https://azure.microsoft.com/pricing/details/mariadb/)megfelelően kerül sor. A biztonsági mentési tárhely használatának befolyásolására, a biztonsági mentési tárolási költségek figyelésére és szabályozására szolgáló tényezők megismeréséhez tekintse meg a [biztonsági mentési dokumentációt](concepts-backup.md).

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után egymástól függetlenül módosíthatja a virtuális mag, az árképzési szintet (kivéve az alapszintű és az alapszintű), a tárterület mennyiségét és a biztonsági mentés megőrzési idejét. A biztonsági mentési tár típusa nem módosítható a kiszolgáló létrehozása után. A virtuális mag száma növelhető felfelé vagy lefelé. A biztonsági másolat megőrzési időtartama 7 – 35 nap között méretezhető. A tárterület mérete csak növelni lehet. Az erőforrások méretezése történhet a portálon vagy az Azure CLI-n keresztül. 

Ha megváltoztatja a virtuális mag számát vagy az árképzési szintet, az eredeti kiszolgáló egy példánya létrejön az új számítási foglalással. Amint a kiszolgáló üzemel, a kapcsolatok átállnak az új kiszolgálóra. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. Ez az időtartam változó, de a legtöbb esetben egy percnél kevesebb időt vesz igénybe.

A tárterület skálázása és a biztonsági mentés megőrzési időtartamának módosítása igaz online művelet. Nincs leállás, és az alkalmazása nincs hatással. A kiépített tároló méretével IOPS a IOPS elérhetővé teheti a kiszolgáló számára a tárterület növelésével.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/mariadb/). A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) a kiválasztott beállítások alapján megjeleníti a havi költséget a **díjszabási szintek** lapon. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és **Azure Database for MariaDB** a beállítások testreszabásához.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg a [szolgáltatási korlátozásokat](concepts-limits.md).
- Megtudhatja, hogyan [hozhat létre MariaDB-kiszolgálót a Azure Portalban](quickstart-create-mariadb-server-database-using-azure-portal.md).
