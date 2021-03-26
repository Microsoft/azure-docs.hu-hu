---
title: Számítási és tárolási lehetőségek – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgáló számítási és tárolási lehetőségeit ismerteti.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 93a439c814b6bac630c5231f46d7a022a131687c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608170"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Számítási és tárolási lehetőségek Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A Azure Database for PostgreSQL-kiszolgálót a három különböző díjszabási csomag egyikében hozhatja létre: a feltört, a általános célú és a memória optimalizált. Az árképzési csomagokat a virtuális mag olyan számítási mennyisége különbözteti meg, amely kiépíthető, memóriát virtuális mag, valamint az adatok tárolására szolgáló tárolási technológiát. Az összes erőforrást a PostgreSQL-kiszolgáló szintjén kell kiépíteni. A kiszolgálók egy vagy több adatbázissal rendelkezhetnek.

| Erőforrás/szintek | **Burstable** | **Általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Virtuális mag | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memória/virtuális mag | Változó | 4 GB | 6,75 – 8 GB |
| Tárterület mérete | 32 GB – 16 TB | 32 GB – 16 TB | 32 GB – 16 TB |
| Adatbázis biztonsági másolatának megőrzési időtartama | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Árképzési szintek kiválasztásához használja a következő táblázatot kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Burstable | A legjobb olyan számítási feladatokhoz, amelyek folyamatosan nem igényelnek teljes CPU-t. |
| Általános célú | A legtöbb üzleti számítási feladat, amely kiegyensúlyozott számítást és memóriát igényel a méretezhető I/O-átviteli sebességgel. Ilyenek például a web- és mobilalkalmazások üzemeltetésére szolgáló kiszolgálók, valamint az egyéb nagyvállalati alkalmazások.|
| Memóriaoptimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek memóriabeli teljesítményt igényelnek a gyorsabb tranzakció-feldolgozáshoz és a nagyobb egyidejűséghez. Ilyenek például a valós idejű adatokat feldolgozó kiszolgálók és a nagy teljesítményű, tranzakciós vagy elemző alkalmazások.|

A kiszolgáló létrehozása után a számítási szintet, a virtuális mag és a tárterület méretét másodpercek alatt módosíthatja. A biztonsági másolatok megőrzési időszakának egymástól függetlenül is beállítható. További információ: [Scale Resources (erőforrások méretezése](#scale-resources) ) szakasz.

## <a name="compute-tiers-vcores-and-server-types"></a>Számítási szintek, virtuális mag és kiszolgálói típusok

A számítási erőforrások a rétegek, a virtuális mag és a memória mérete alapján választhatók ki. a virtuális mag az alapul szolgáló hardver logikai PROCESSZORát képviselik.

A rendelkezésre álló kiszolgálói típusok részletes leírása a következő:

| SKU neve             | Virtuális mag | Memória mérete | Támogatott IOPS maximális száma | Támogatott I/O-sávszélesség maximális száma |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Burstable**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/mp                  |
| B2s                  | 2      | 4. GiB       | 1280               | 15 MiB/mp                  |
| **Általános célú**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/mp                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/mp                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/mp                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/mp                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/mp                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/mp                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/mp                 |
| **Memória optimalizálva** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/mp                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/mp                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/mp                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/mp                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/mp                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/mp                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/mp                 |

## <a name="storage"></a>Tárolás

Az Ön által kiépített tárterület a Azure Database for PostgreSQL-kiszolgáló számára elérhető tárolási kapacitás mennyisége. A tárterületet az adatbázisfájlok, az ideiglenes fájlok, a tranzakciónaplók és a PostgreSQL-kiszolgáló naplófájljai használják. A kiépített tárterület teljes mennyisége határozza meg a kiszolgáló számára elérhető I/O-kapacitást is.

A tárterület a következő rögzített méretekben érhető el:

| Lemezméret | IOPS |
|:---|:---|
| 32 GiB | Kiépített 120, legfeljebb 3 500 |
| 64 GiB | Kiépített 240, legfeljebb 3 500 |
| 128 GiB | Kiépített 500, legfeljebb 3 500 |
| 256 GiB | Kiépített 1100, legfeljebb 3 500 |
| 512 GiB | Kiépített 2300, legfeljebb 3 500 |
| 1 TiB | 5000 |
| 2 tebibájt | 7500 |
| 4 TiB | 7500 |
| 8 TiB | 16000 |
| 16 TiB | 18000 |

Vegye figyelembe, hogy a virtuális gép típusa is korlátozza a IOPS. Annak ellenére, hogy bármelyik tárolási méretet a kiszolgáló típusától függetlenül is kiválaszthatja, előfordulhat, hogy nem fogja tudni használni az összes olyan IOPS, amelyet a tárterület biztosít, különösen akkor, ha kis számú virtuális mag rendelkező kiszolgálót választ.

A kiszolgáló létrehozása után további tárolókapacitást is hozzáadhat.

>[!NOTE]
> A tárterületet csak felfelé, nem lefelé lehet méretezni.

Az I/O-használatot a Azure Portal vagy az Azure CLI-parancsok használatával figyelheti. A figyelni kívánt mérőszámok a [tárolási korlát, a tárolási százalék, a felhasznált tárterület és az IO százalék](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>A konfiguráció maximális IOPS

|SKU neve            |Tárolási méret a GiB-ban                       |32 |64 |128 |256 |512  |1 024|2048|4 096|8,192 |16,384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maximális IOPS                              |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Burstable**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2s                 |1280 IOPS                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Általános célú** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |6 400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |12 800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|D16s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D32s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D48s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D64s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Memória optimalizálva**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |6 400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |12 800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800 *|12800 *|
|E16s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E32s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E48s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E64s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |

A (a) jelölésű \* IOPS a kiválasztott VM-típus korlátozza. Ellenkező esetben a IOPS a kiválasztott tárolási méret korlátozza.

>[!NOTE]
> A metrikák nagyobb IOPS jelenhetnek meg a lemezek szintjének kitörése miatt. További részletekért tekintse meg a [dokumentációt](../../virtual-machines/disk-bursting.md#disk-level-bursting) . 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Maximális I/O-sávszélesség (MiB/s) a konfigurációhoz

|SKU neve            |Tárterület mérete, GiB                             |32 |64 |128 |256 |512  |1 024|2048|4 096|8,192 |16,384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Tárolási sávszélesség, MiB/s**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Burstable**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/mp                                    |10|10|10 |10 |10  |10  |10  |10  |10   |10   |
|B2s                 |15 MiB/mp                                    |15|15|15 |15 |15  |15  |15  |15  |15   |15   |
|**Általános célú** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/mp                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MiB/mp                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MiB/mp                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Memória optimalizálva**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/mp                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MiB/mp                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MiB/mp                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/mp                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Ha egy \* , I/O-sávszélességgel van megjelölve, a kiválasztott virtuálisgép-típus korlátozza. Az egyéb I/O-sávszélességet a kiválasztott tárterület korlátozza.

### <a name="reaching-the-storage-limit"></a>Tárhelykorlát elérése

Amikor eléri a tárolási korlátot, a kiszolgáló a hibák visszaadását és a további módosítások elkerülését fogja megkezdeni. Ez problémákat okozhat más működési tevékenységekkel, például a biztonsági mentésekkel és a WAL archiválással kapcsolatban.

Ha el szeretné kerülni ezt a helyzetet, amikor a tárterület-használat eléri a 95%-ot, vagy ha a rendelkezésre álló kapacitás kevesebb, mint 5 GiB, a kiszolgáló automatikusan **írásvédett módra** vált.

Javasoljuk, hogy aktívan figyelje a használatban lévő lemezterületet, és növelje a lemez méretét a tárolási helyzetben lévők előtt. Beállíthat egy riasztást, amely értesíti, ha a kiszolgáló tárterülete elfogyott a lemezről, így elkerülhetők a lemezről való kifogyás okozta hibák. További információt a [riasztás beállításával](howto-alert-on-metrics.md)kapcsolatos dokumentációban talál.


### <a name="storage-auto-grow"></a>Tárterület automatikus növekedése

A Storage automatikus növekedése még nem érhető el a rugalmas kiszolgálók számára.

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági másolatot készít a kiszolgálóról. A megőrzési időtartamot 7 és 35 nap közé is kiválaszthatja. További információ a biztonsági mentésekről a [fogalmakat ismertető cikkben](concepts-backup-restore.md).

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után egymástól függetlenül módosíthatja a virtuális mag, a számítási szintet, a tárterület mennyiségét és a biztonsági mentés megőrzési idejét. A virtuális mag száma növelhető felfelé vagy lefelé. A biztonsági másolat megőrzési időtartama 7 – 35 nap között méretezhető. A tárterület mérete csak növelni lehet. Az erőforrások méretezése történhet a portálon vagy az Azure CLI-n keresztül.

> [!NOTE]
> A tárterület mérete csak növelni lehet. A növekedés után nem térhet vissza kisebb tárterület-méretre.

Ha megváltoztatja a virtuális mag vagy a számítási szintet, a kiszolgáló újraindul, hogy az új kiszolgáló típusa érvénybe lépjen. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. Ez az időtartam változó, de a legtöbb esetben egy percnél kevesebb időt vesz igénybe. A tároló skálázása ugyanúgy működik, és rövid újraindítást is igényel.

A biztonsági mentés megőrzési időtartamának módosítása online művelet.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/PostgreSQL/). A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a kiválasztott beállítások alapján megjeleníti a havi költséget a **díjszabási szintek** lapon. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és **Azure Database for PostgreSQL** a beállítások testreszabásához.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre PostgreSQL-kiszolgálót a portálon](how-to-manage-server-portal.md).
- A [szolgáltatás korlátainak](concepts-limits.md)megismerése.