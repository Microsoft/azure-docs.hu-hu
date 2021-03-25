---
title: Számítási és tárolási lehetőségek – Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk a Azure Database for MySQL rugalmas kiszolgáló számítási és tárolási lehetőségeit ismerteti.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7165cdc072ffaa5b0d862e1fe17f94e35c35aeec
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034537"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Számítási és tárolási lehetőségek Azure Database for MySQL – rugalmas kiszolgáló (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

A három különböző számítási réteg egyikében hozhat létre Azure Database for MySQL rugalmas kiszolgálót: a feltört, a általános célú és a memória optimalizált. A számítási rétegeket a mögöttes virtuálisgép-SKU különbözteti meg, a B sorozat, a D sorozat és az E sorozat használata. A számítási szintek és méretek közül választhat a kiszolgálón elérhető memória-és virtuális mag. Ugyanazt a tárolási technológiát használják az összes számítási szinten. Minden erőforrást a MySQL-kiszolgáló szintjén kell kiépíteni. A kiszolgálók egy vagy több adatbázissal rendelkezhetnek.

| Erőforrás/szintek | **Burstable** | **Általános célú** | **Memória optimalizálva** |
|:---|:----------|:--------------------|:---------------------|
| Virtuálisgép-sorozatok| B sorozat | Ddsv4 sorozat | Edsv4 sorozat|
| Virtuális mag | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memória/virtuális mag | Változó | 4. GiB | 8 GiB * |
| Tárterület mérete | 5 GiB – 16 TiB | 5 GiB – 16 TiB | 5 GiB – 16 TiB |
| Adatbázis biztonsági másolatának megőrzési időtartama | 1 – 35 nap | 1 – 35 nap | 1 – 35 nap |

\* A E64ds_v4 (memória optimalizált) SKU kivételével, amely 504 GB memóriát tartalmaz

Számítási szintek kiválasztásához használja a következő táblázatot kiindulási pontként.

| Számítási szint | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Burstable | A legjobb olyan számítási feladatokhoz, amelyek folyamatosan nem igényelnek teljes CPU-t. |
| Általános célú | A legtöbb üzleti számítási feladat, amely kiegyensúlyozott számítást és memóriát igényel a méretezhető I/O-átviteli sebességgel. Ilyenek például a web- és mobilalkalmazások üzemeltetésére szolgáló kiszolgálók, valamint az egyéb nagyvállalati alkalmazások.|
| Memóriaoptimalizált | Nagy teljesítményű adatbázis-munkaterhelések, amelyek memóriabeli teljesítményt igényelnek a gyorsabb tranzakció-feldolgozáshoz és a nagyobb egyidejűséghez. Ilyenek például a valós idejű adatokat feldolgozó kiszolgálók és a nagy teljesítményű, tranzakciós vagy elemző alkalmazások.|

A kiszolgáló létrehozása után a számítási rétegek, a számítási méret és a tárterület mérete megváltozott. A számítási skálázáshoz újraindításra van szükség, és 60-120 másodperc közötti időt vesz igénybe, míg a tárolási skálázás nem igényel újraindítást. A biztonsági másolatok megőrzési időszakának egymástól függetlenül is beállítható. További információ: [Scale Resources (erőforrások méretezése](#scale-resources) ) szakasz.

## <a name="compute-tiers-size-and-server-types"></a>Számítási szintek, méret és kiszolgálói típusok

A számítási erőforrások a rétegek és a méretek alapján választhatók ki. Ez határozza meg a virtuális mag és a memória méretét. a virtuális mag az alapul szolgáló hardver logikai PROCESSZORát képviselik.

A rendelkezésre álló kiszolgálói típusok részletes leírása a következő:

| Számítási méret         | Virtuális mag | Memória mérete (GiB) | Támogatott IOPS maximális száma | Támogatott I/O-sávszélesség maximális száma (MBps)|
|----------------------|--------|-------------------| ------------------ |-----------------------------------|
| **Burstable**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 
| Standard_B1ms        | 1      | 2                 | 640                | 10                                |
| Standard_B2s         | 2      | 4                 | 1280               | 15                                |
| **Általános célú**  |        |                   |                    |                                   |
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                |
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                |
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               |
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               |
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               |
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              |
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              |
| **Memória optimalizálva** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                |
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                |
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               |
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               |
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               |
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              |
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              |

Ha további részleteket szeretne megtudni a rendelkezésre álló számítási sorozatokról, tekintse meg az Azure virtuális gép dokumentációját, amely a [feltört (B sorozatú)](../../virtual-machines/sizes-b-series-burstable.md), [általános célú (Ddsv4 sorozat)](../../virtual-machines/ddv4-ddsv4-series.md)és a [memóriára optimalizált (Edsv4-sorozat)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>Ha a virtuális gép indítása/leállítása vagy újraindítása után a kihasználható [(B sorozatos) számítási réteg](../../virtual-machines/sizes-b-series-burstable.md) elvész, előfordulhat, hogy a kreditek elvesznek. További információ: [feltört (B sorozatú) gyakori kérdések](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Tárolás

A kiépített tárterület a rugalmas kiszolgáló számára elérhető tárolókapacitás mennyisége. A tároló az adatbázisfájlok, az ideiglenes fájlok, a tranzakciónaplók és a MySQL-kiszolgálói naplók tárolására szolgál. Az összes számítási szinten a támogatott minimális tárterület 5 GiB, a maximum pedig 16 TiB. A tárterület 1 GiB-onként méretezhető, és a kiszolgáló létrehozása után méretezhető.

>[!NOTE]
> A tárterületet csak felfelé, nem lefelé lehet méretezni.

A tárterület-felhasználást a Azure Portalban (Azure Monitor) figyelheti a tárolási korlát, a tárolási százalék és a felhasznált tároló metrikáinak használatával. A metrikák megismeréséhez tekintse meg a [figyelési cikket](./concepts-monitoring.md) . 

### <a name="reaching-the-storage-limit"></a>Tárhelykorlát elérése

Ha a kiszolgálón felhasznált tárterület közel van a kiosztott korlát eléréséhez, a kiszolgáló írásvédett módba kerül, hogy megvédje az elveszett írásokat a kiszolgálón. Azok a kiszolgálók, amelyek kisebb, mint 100 GiB kiépített tárolóval rendelkeznek, csak olvashatóként vannak megjelölve, ha az ingyenes tárterület kevesebb, mint 5%-a kiépített tároló mérete. A több mint 100 GiB kiosztott tárterülettel rendelkező kiszolgálók csak olvashatóként vannak megjelölve, ha az ingyenes tárterület 5 GiB-nál kisebb.

Ha például 110 GiB tárterületet hozott létre, és a tényleges kihasználtság a 105 GiB-ra esik, a kiszolgáló csak olvashatóként van megjelölve. Ha 5 GiB tárterületet osztott ki, akkor a kiszolgáló csak olvashatóként van megjelölve, ha az ingyenes tárterület 256 MB-nál kevesebbet ér el.

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. 

A kiszolgáló csak olvasható módból való lekéréséhez növelje a kiépített tárolót a kiszolgálón. Ezt a Azure Portal vagy az Azure CLI használatával teheti meg. A megnövelt érték után a kiszolgáló készen áll az írási tranzakciók újbóli fogadására.

Javasoljuk, hogy állítson be egy riasztást, amely értesíti Önt, ha a kiszolgáló tárterülete eléri a küszöbértéket, így elkerülhető a csak olvasási állapotba való beolvasás. A rendelkezésre álló metrikák megismeréséhez tekintse meg a [figyelési cikket](./concepts-monitoring.md) . 

Javasoljuk, hogy <!--turn on storage auto-grow or to--> riasztást állíthat be, amely értesíti, ha a kiszolgáló tárterülete eléri a küszöbértéket, így elkerülhető a csak olvasási állapotba való beolvasás. További információ a riasztások [beállítását](how-to-alert-on-metric.md)ismertető dokumentációban található.

### <a name="storage-auto-grow"></a>Tárterület automatikus növekedése

A Storage automatikus növekedése még nem érhető el Azure Database for MySQL rugalmas kiszolgáló számára.

## <a name="iops"></a>IOPS

Azure Database for MySQL – a rugalmas kiszolgáló támogatja a további IOPS üzembe helyezését. Ez a funkció lehetővé teszi, hogy további IOPS hozzon létre az ingyenes IOPS-korlát felett. Ezzel a szolgáltatással bármikor növelheti vagy csökkentheti a számítási feladatok követelményei alapján kiépített IOPS számát. 

A minimális IOPS az összes számítási méret 100, a maximális IOPS pedig a kiválasztott számítási méret határozza meg. Az előzetes verzióban a maximális támogatott IOPS 20 000 IOPS.

Ha többet szeretne megtudni a számítási mérettel kapcsolatos maximális IOPS, tekintse meg az alábbi ábrát: 

| Számítási méret         | Maximális IOPS        | 
|----------------------|---------------------|
| **Burstable**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Általános célú**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Memória optimalizálva** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

A maximális IOPS függ a maximális rendelkezésre álló IOPS, a számítási mérettől számítva. A nem *gyorsítótárazott lemez maximális átviteli sebességét* a következő oszlopban tekintheti meg: IOPS/Mbps a [B sorozat](../../virtual-machines/sizes-b-series-burstable.md), a [Ddsv4 sorozat](../../virtual-machines/ddv4-ddsv4-series.md)és a [Edsv4 sorozat](../../virtual-machines/edv4-edsv4-series.md) dokumentációjában.

> [!Important]
> Az **ingyenes IOPS** megegyeznek a minimális értékkel ("a gyorsítótár nélküli lemezek maximális átviteli sebessége: IOPS/Mbps" a számítási mérettől, a kiépített, GIB * 3-ban kiosztott tárterülettől)<br>
> A **minimális IOPS** az összes számítási méret 100.<br>
> A **maximális IOPS** a kiválasztott számítási méret határozza meg. Az előzetes verzióban a maximális támogatott IOPS 20 000 IOPS.

Az I/O-használatot a Azure Portalban (Azure Monitor) figyelheti az [IO százalék](./concepts-monitoring.md) metrika használatával. Ha további IOPS van szüksége, akkor a maximális IOPS a számítások alapján kell méreteznie a kiszolgáló számítási felskálázását.

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági másolatot készít a kiszolgálóról. A megőrzési időtartamot 1 és 35 nap közé is kiválaszthatja. További információ a biztonsági másolatokról a [biztonsági mentési és visszaállítási fogalmakról szóló cikkben](concepts-backup-restore.md).

## <a name="scale-resources"></a>Erőforrások skálázása

A kiszolgáló létrehozása után egymástól függetlenül módosíthatja a számítási szintet, a számítási méretet (virtuális mag és memóriát), valamint a tárterület mennyiségét és a biztonsági mentés megőrzési idejét. A számítási méret fel-és leskálázásra is méretezhető. A biztonsági másolatok megőrzési időszaka 1 és 35 nap között méretezhető. A tárterület mérete csak növelni lehet. Az erőforrások méretezése a portálon vagy az Azure CLI-n keresztül végezhető el.

> [!NOTE]
> A tárterület mérete csak növelni lehet. A növekedés után nem térhet vissza kisebb tárterület-méretre.

Ha megváltoztatja a számítási szintet vagy a számítási méretet, a kiszolgáló újraindul, hogy az új kiszolgáló típusa érvénybe lépjen. Az új kiszolgálóra való váltás pillanatában nem hozható létre új kapcsolat, és a nem véglegesített tranzakciók vissza lesznek állítva. Ez az ablak eltérő, de a legtöbb esetben 60-120 másodperc. 

A tárterület skálázása és a biztonsági mentés megőrzési időszakának módosítása online művelet, és nincs szükség a kiszolgáló újraindítására.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/MySQL/). A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) a kiválasztott lehetőségek alapján a **számítási és tárolási** lap havi költségét jeleníti meg. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és a beállítások testreszabásához válassza a **Azure Database for MySQL** és a **rugalmas kiszolgáló** lehetőséget.

Ha optimalizálni szeretné a kiszolgáló költségeit, akkor a következő tippeket kell figyelembe vennie:

- A számítási szintek vagy a számítási méret (virtuális mag) vertikális leskálázása, ha a számítási kapacitást kihasználják.
- Ha a számítási feladatnak nincs szüksége a teljes számítási kapacitásra a általános célú és a memória optimalizált szintjein, érdemes lehet átváltani.
- Állítsa le a kiszolgálót, ha nincs használatban.
- Csökkentse a biztonsági mentés megőrzési időtartamát, ha a biztonsági mentés hosszabb ideig nem szükséges.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre MySQL-kiszolgálót a portálon](quickstart-create-server-portal.md).
- További információ a [szolgáltatási korlátozásokról](concepts-limitations.md).
