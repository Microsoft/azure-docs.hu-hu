---
title: Azure File Sync Felhőbeli rétegek figyelése | Microsoft Docs
description: A felhőre vonatkozó szabályzatok figyeléséhez használt mérőszámok részletei.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c27916afb0d199bcb32db9d43202e552a4a04f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593135"
---
# <a name="monitor-cloud-tiering"></a>Felhőbeli rétegek figyelése
A felhő-előállítók házirendjét kétféleképpen figyelheti: a kiszolgálói végpont tulajdonságai panel és a Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Figyelés kiszolgálói végponton keresztül

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/), majd keresse meg a figyelni kívánt kiszolgáló-végponthoz tartozó **kiszolgálói végpont tulajdonságai** panelt, és görgessen le a Felhőbeli rétegek szakaszhoz. 

![A kiszolgálói végpont tulajdonságai között található Cloud rétegű szakasz képernyőképe.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

A **lemezterület-megtakarítás** a Felhőbeli rétegek engedélyezésével megtakarított terület mennyisége. Ha a helyi kötet mérete elég nagy az összes adattároláshoz, akkor 0%-os megtakarítást érhet el. Ha 0%-os vagy alacsony lemezterület-megtakarítás van, akkor azt jelezheti, hogy a felhő-rétegek nem hasznosak az aktuális helyi gyorsítótár-mérettel. 

A **gyorsítótár-találatok aránya** a helyi gyorsítótárban megnyitott fájlok százalékos aránya. A gyorsítótár találati arányának kiszámítása a gyorsítótárból vagy a megnyitott fájlok teljes száma alapján megnyitott fájlok alapján történik. Az 100%-os gyorsítótár-találati arány azt jelenti, hogy az elmúlt órában megnyitott összes fájl már szerepel a helyi gyorsítótárban. Ha a cél a kimenő forgalom csökkentése, ez azt jelzi, hogy az aktuális szabályzat jól működik.

> [!NOTE]
> A véletlenszerű hozzáférési mintákkal rendelkező munkaterhelések általában alacsonyabb gyorsítótár-találati aránysal rendelkeznek. 

A **teljes méret (felhő)** a felhőbe szinkronizált fájlok mérete. 

A **gyorsítótárazott méret (kiszolgáló)** a kiszolgálón tárolt fájlok teljes mérete, a letöltött és a lépcsőzetesen is. Előfordulhat, hogy a gyorsítótárazott méret nagyobb, mint a felhőben lévő fájlok teljes mérete. A-kiszolgálón lévő kötetek (például a fürt mérete) miatt ez a változó lehet. Ha a gyorsítótárazott méret nagyobb a kívántnál, érdemes lehet növelni a kötetek szabad területére vonatkozó házirendet. 

A Azure File Sync **hatékony kötet-házirendet** használ annak meghatározásához, hogy mennyi szabad lemezterületet kell hagyni a kiszolgálói végpontot futtató köteten. Ha ugyanazon a köteten több kiszolgálói végpont található, akkor a kiszolgálói végpontok közül a legnagyobb mennyiségű szabad terület házirend lesz az adott köteten lévő összes kiszolgálói végpontra érvényes kötet-házirend. Ha például két kiszolgálói végpont van ugyanazon a köteten, az egyik 30%-os szabad területtel, a másik pedig a 50%-os mennyiségű szabad területtel, akkor a kiszolgálói végpontok érvényes szabad lemezterület-házirendje 50% lesz.

Az **aktuális kötet szabad területe** a helyszíni kiszolgálón jelenleg elérhető szabad terület. Ha magas a kimenő forgalom, de nagyobb mennyiségű szabad terület áll rendelkezésre, mielőtt a kötet szabad területére vonatkozó házirend beindul, érdemes lehet letiltani a dátum-házirendet. Egy másik probléma lehet az, hogy az aktuálisan létrehozott fájlok közül a legutóbb elért fájl nagyobb, mint a házirend berúgása előtt megmaradt kötet szabad területe. Ebben az esetben érdemes megfontolni a helyi kötet méretének növelését. 

## <a name="monitoring-via-azure-monitor"></a>Figyelés Azure Monitoron keresztül

Lépjen a **Storage Sync szolgáltatáshoz** , és válassza a **mérőszámok** lehetőséget az oldalsó Navigálás oldalon. 

Három különböző mérőszám használható a kimenő forgalom figyelésére a felhőalapú rétegekből:

- Felhőbeli rétegek felidézésének mérete
    - A visszaadott adat teljes mérete bájtban kifejezve, és annak azonosítására szolgál, hogy mennyi adat kerül visszahívásra.
- Felhőbeli rétegek felidézésének mérete alkalmazás szerint
    - Ez az alkalmazás által bájtban visszaadott adatmennyiség teljes mérete, amellyel azonosíthatók az adat-visszahívások.
- Felhőbeli rétegek felidézésének átviteli sebessége
    - Ez azt méri, hogy milyen gyorsan történik az adat visszahívása bájtban, és akkor kell használni, ha a teljesítményre vonatkozó probléma merül fel. 

Ahhoz, hogy pontosabb legyen a gráfok megjelenítésére vonatkozó információk, érdemes lehet a **szűrő hozzáadása** és a **felosztás alkalmazása**.
 
A Azure File Sync metrikáinak különböző típusaival és azok használatának módjával kapcsolatos részletekért lásd: [Azure file Sync figyelése](storage-sync-files-monitoring.md).

A metrikák használatáról további információt az [Azure Metrikaböngésző használatának első lépéseivel](../../azure-monitor/essentials/metrics-getting-started.md)foglalkozó témakörben talál.

Ha módosítani szeretné a Felhőbeli adatkezelési szabályzatot, tekintse meg a [felhőalapú rétegek kiválasztására](storage-sync-choose-cloud-tiering-policies.md)vonatkozó házirendeket.

## <a name="next-steps"></a>Következő lépések
* [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)