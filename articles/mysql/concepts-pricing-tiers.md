---
title: Az Azure Database for MySQL tarifacsomagok
description: Ez a cikk ismerteti a tarifacsomagok az Azure Database for MySQL-hez.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: d84e3f834bd598bbd2cf84dfc46f6cd5507f6d8b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587123"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database for MySQL-hez tarifacsomagok

Létrehozhat egy Azure Database for MySQL-kiszolgáló a másik három tarifacsomag közül: Alapszintű, általános célú és memóriahasználatra optimalizált. A tarifacsomag, amelyek kioszthatóak virtuális magok, memória / virtuális mag és a tárolási technológiát, az adatok tárolására használt számítási mennyisége alapján különbözteti meg. Az összes erőforrás a MySQL-kiszolgáló szintjén vannak üzembe helyezve. A kiszolgáló egy vagy több adatbázissal rendelkezhet.

|    | **Basic** | **Általános célú** | **Memóriahasználatra optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Számítási generáció | A gen 4, 5 általános | A gen 4, 5 általános | Gen 5 |
| Virtuális magok | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória / virtuális mag | 2 GB | 5 GB | 10 GB |
| Tároló mérete | 5 GB – 1 TB | 5 GB-tól 4 TB-ig | 5 GB-tól 4 TB-ig |
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Adatbázis biztonsági másolatainak megőrzési ideje | 7 – 35 nap | 7 – 35 nap | 7 – 35 nap |

Válasszon egy tarifacsomagot, használja az alábbi táblázat kiindulási pontként.

| Tarifacsomag | Kívánt teljesítményprofilok |
|:-------------|:-----------------|
| Alapszintű | Kis számítási és i/o-teljesítményt igénylő munkaterhelések. Ilyenek például fejlesztési vagy tesztelési célokra használt kiszolgálók vagy kisméretű, ritkán használt alkalmazásokhoz. |
| Általános rendeltetés | A legtöbb üzleti számítási feladathoz, amely kiegyensúlyozott számítást és memóriát, valamint méretezhető i/o-teljesítményt igényelnek. Ilyenek például a kiszolgálók üzemeltetésére, webes és mobilalkalmazások és egyéb nagyvállalati alkalmazások.|
| Memóriára optimalizált | Kiemelkedő teljesítményű adatbázisokat igénylő számítási feladatok memórián belüli teljesítmény szükséges a gyorsabb tranzakciófeldolgozás és a magasabb szintű egyidejűség érdekében. Ilyenek például a kiszolgálók feldolgozásához, valós idejű és nagy teljesítményű tranzakciós vagy elemző alkalmazások.|

Miután létrehozott egy kiszolgálót, a virtuális magok, a hardver generációja és a díjszabás számát réteg (kivéve, és az alapszintű) módosíthatja felfelé vagy lefelé másodpercen belül. Is egymástól függetlenül módosíthatja felfelé storage és a biztonsági másolat megőrzési idejének felfelé vagy lefelé az alkalmazás leállása nélkül. A biztonsági mentési tár írja be a kiszolgáló létrehozása után nem módosítható. További információkért lásd: a [méretezni az erőforrásokat](#scale-resources) szakaszban.

## <a name="compute-generations-and-vcores"></a>Számítási generáció és a virtuális magok

A számítási erőforrások szolgálnak, amelyek jelölik az alapul szolgáló hardver logikai CPU, a virtuális magok. Jelenleg két számítási generáció, 4. generációs és a Gen 5 közül választhat. Gen 4 logikai CPU k Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorral. Gen 5 logikai CPU k Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorral. A gen 4 és a Gen 5 ("X" azt jelzi, hogy elérhető), a következő régiókban érhetők el.

> [!IMPORTANT]
> 2018. December 12., kezdve új ügyfeleket nem helyezhetik üzembe a számítási generáció 4 kiszolgálók Dél-Brazília, közép-Kanada, kelet-Kanada, Kelet-Ázsia, USA 2. keleti régiója, közép-India, Nyugat-India, Nyugat-japán, USA északi középső Régiója, USA nyugati RÉGIÓJA. Korábban létrehozott számítási generáció 4 kiszolgáló számítási generáció 5-től 2019. február 1. ezekben a régiókban migrálása.
>
> [!IMPORTANT]
> 2019. február 15., kezdve új ügyfelek nem tudják üzembe a számítási generáció 4 kiszolgálókat az USA középső RÉGIÓJA, USA keleti RÉGIÓJA, kelet-japán, Észak-Európa, USA déli középső Régiója, Délkelet-Ázsia, Nyugat-Európa. Korábban létrehozott számítási generáció 4 kiszolgáló számítási generáció 5-től 2019. április 1. ezekben a régiókban migrálása.

| **Az Azure-régió** | **A gen 4** | **A gen 5** |
|:---|:----------:|:--------------------:|
| USA középső régiója |  | X |
| USA keleti régiója |  | X |
| USA 2. keleti régiója | X | X |
| USA északi középső régiója | X | X |
| USA déli középső régiója | X | X |
| USA nyugati régiója | X | X |
| USA nyugati régiója, 2. |  | X |
| Dél-Brazília | X | X |
| Közép-Kanada | X | X |
| Kelet-Kanada | X | X |
| Észak-Európa | X | X |
| Nyugat-Európa |  | X |
| Közép-Franciaország |  | X |
| Az Egyesült Királyság déli régiója |  | X |
| Az Egyesült Királyság nyugati régiója |  | X |
| Kelet-Ázsia | X | X |
| Délkelet-Ázsia | X | X |
| Kelet-Ausztrália |  | X |
| Ausztrália középső régiója |  | X |
| Ausztrália 2. középső régiója |  | X |
| Délkelet-Ausztrália |  | X |
| Közép-India | X | X |
| Dél-India |  | X |
| Nyugat-India | X | X |
| Kelet-Japán | X | X |
| Nyugat-Japán | X | X |
| Korea középső régiója |  | X |
| Korea déli régiója |  | X |
| Kelet-Kína 1 | X |  |
| Kelet-Kína 2 |  | X |
| Észak-Kína 1 | X |  |
| Észak-Kína 2 |  | X |
| Közép-Németország |  | X |
| US DoD – Középső régió  | X |  |
| US DoD – Kelet  | X |  |
| USA-beli államigazgatás – Arizona |  | X |
| USA-beli államigazgatás – Texas |  | X |
| USA-beli államigazgatás – Virginia |  | X |

## <a name="storage"></a>Storage

A tároló üzembe helyezi az érték a tárolási kapacitás érhető el az Azure database for MySQL-kiszolgálót. A storage szolgál az adatbázisfájlokat, az ideiglenes fájlok, tranzakciós naplókhoz és a MySQL-kiszolgáló naplóit. Tároló üzembe helyezésekor teljes mennyisége is meghatározza a rendelkezésre álló i/o-kapacitás a kiszolgálóhoz.

|    | **Basic** | **Általános célú** | **Memóriahasználatra optimalizált** |
|:---|:----------|:--------------------|:---------------------|
| Tárolási típus | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Tároló mérete | 5 GB – 1 TB | 5 GB-tól 4 TB-ig | 5 GB-tól 4 TB-ig |
| Növekmény tárméret | 1 GB | 1 GB | 1 GB |
| IO | Változó |3 IOPS/GB<br/>Minimum 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Minimum 100 IOPS<br/>Max 6000 IOPS |

Hozzáadhat további tárolókapacitás alatt és a kiszolgáló létrehozása után. Az alapszintű csomag nem biztosítanak az iops-t. Az általános célú és memóriahasználatra optimalizált tarifacsomagok az iops-t a kiépített tárhely méretére vonatkozik egy 3:1 arányt a skálázás.

Az i/o-használat az Azure Portalon vagy Azure CLI-parancsok használatával követheti nyomon. A releváns metrikákat kíván monitorozni vannak [tárhelykorlátozás, tárolási százalékos aránya, felhasznált tárterület és i/o-százalék](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Skálázhatósági méretkorlátot

A kiszolgáló csak olvashatóként lesz megjelölve, amikor a szabad tárterület 5 GB alá vagy a kiépített tárterület 5%-a alá csökken (amelyik kisebb). Ha például 100 GB tárhelyet kiépítése, és a tényleges használat halad 95 GB, a kiszolgáló van megjelölve, csak olvasható. Ha 5 GB tárterületet osztott ki, a kiszolgáló akkor lesz megjelölve csak olvashatóként, amikor a szabad tárterület 250 MB alá csökken.  

Mialatt a szolgáltatás csak olvashatóvá próbálja tenni a kiszolgálót, minden új írási tranzakció kérését blokkolja a rendszer, és a meglévő aktív tranzakciók végrehajtása folytatódik. A kiszolgáló csak olvashatóként való beállításakor minden későbbi írási művelet és tranzakció meghiúsul. Az olvasási lekérdezések továbbra is zavartalanul működnek. A kiépített tárterület növelése után a kiszolgáló ismét készen fog állni az írási tranzakciók elfogadására.

Azt javasoljuk, hogy beállította egy riasztás arra az esetre, ha a kiszolgáló tárhelyét hamarosan eléri a küszöbértéket a csak olvasható állapotba első elkerülése érdekében. További információkért lásd a dokumentációt [riasztást beállítása](howto-alert-on-metric.md).

## <a name="backup"></a>Backup

A szolgáltatás automatikusan biztonsági másolatokat a kiszolgálóról vesz igénybe. A minimális megőrzési idő a biztonsági másolatok hét napon. Beállíthatja, hogy egy legfeljebb 35 napos megőrzési idővel. A megőrzési bármikor módosítható a kiszolgáló élettartama során. Helyileg redundáns és georedundáns biztonsági mentések közül választhat. Georedundáns biztonsági mentéseket is vannak tárolva a [földrajzilag párosított régióba](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) a régió, ahol a kiszolgáló akkor jön létre. Ezt a redundanciát biztosít védelmet egy esetleges vészhelyzet esetén. Bármely más Azure-régió, amelyben a szolgáltatás georedundáns biztonsági másolat érhető el, a kiszolgáló helyreállítására is kaphat. Nem alkalmas a kiszolgáló létrehozása után két biztonsági másolati tárhely beállításai között módosíthatja.

## <a name="scale-resources"></a>Erőforrások skálázása

Miután létrehozta a kiszolgáló, egymástól függetlenül módosíthatja a virtuális magok, a hardver generációja, a tarifacsomag (kivéve a, illetve onnan alapszintű), a storage és a biztonsági másolat megőrzési idejének összege. A biztonsági mentési tár írja be a kiszolgáló létrehozása után nem módosítható. A virtuális magok számát felfelé és lefelé skálázhatók. A biztonsági másolat megőrzési idejének méretezhetők felfelé vagy lefelé 7-ről 35 napon belül. A tároló mérete pedig csak növelni. Az erőforrások méretezése elvégezhető a portál vagy az Azure CLI-vel felületén keresztül. A méretezés az Azure CLI-vel egy példa: [figyelés és méret egy Azure Database for MySQL-kiszolgálóhoz az Azure CLI-vel](scripts/sample-scale-server.md).

Amikor módosítja a virtuális magok száma, a hardver generációja, vagy a tarifacsomagot, az eredeti kiszolgáló egy példányát az új számítási felosztás jön létre. Miután az új kiszolgáló üzembe helyezéséig, kapcsolatok szálról az új kiszolgálóra. Során a pillanatban, amikor a rendszer átvált az új kiszolgáló új kapcsolat nem hozható létre, és az összes nem véglegesített tranzakció vissza legyen állítva. Ebben az ablakban változik, de a legtöbb esetben kevesebb mint egy percet.

Tároló méretezése és a biztonsági másolat megőrzési idejének módosítása is igaz online. Nem jár, és a folyamatot nem befolyásolja az alkalmazás. IOPS és a felhasznált tárterület mérete logikákat tárolási vertikális felskálázásával növelheti a rendelkezésre álló IOPS a kiszolgáló.

## <a name="pricing"></a>Díjszabás

A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/mysql/). A költség, a konfiguráció azt szeretné, hogy a [az Azure portal](https://portal.azure.com/#create/Microsoft.MySQLServer) havi költségét jeleníti meg a **tarifacsomag** lapon a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátorát segítségével a becsült ár beolvasása. A a [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhely, válassza **elemek hozzáadása**, bontsa ki a **adatbázisok** kategória, és válassza **, Azure Database for MySQL**testre szabhatja beállítások.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [MySQL-kiszolgáló létrehozása a portálon](howto-create-manage-server-portal.md).
- Ismerje meg [szolgáltatási korlátozásaival](concepts-limits.md).
- Ismerje meg, hogyan [horizontális felskálázás a olvasható replikák](howto-read-replicas-portal.md).
