---
title: A Scala & Java-kódtárainak kezelése Apache Spark
description: Ismerje meg, hogyan adhat hozzá és kezelhet Scala-és Java-kódtárakat az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098706"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>A Scala és a Java-csomagok kezelése az Azure szinapszis Analytics szolgáltatásban Apache Sparkhoz

A tárak újrafelhasználható kódot biztosítanak, amelyet a programok vagy projektek számára érdemes felvenni. 

Előfordulhat, hogy számos okból frissítenie kell a kiszolgáló nélküli Apache Spark Pool-környezetet. Előfordulhat például, hogy a következőket találja:
- a legfontosabb függőségei egy új verziót adtak ki.
- További csomagra van szükség a gépi tanulási modell betanításához vagy az adatelőkészítéshez.
- jobb csomagot talált, és már nincs szüksége a régebbi csomagra.

Ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódot elérhetővé tegye az alkalmazásai számára, telepíthet egy függvénytárat az egyik kiszolgáló nélküli Apache Spark készletére vagy notebook-munkamenetére. Ebből a cikkből megtudhatja, hogyan kezelheti a Scala és a Java-csomagokat.

## <a name="default-installation"></a>Alapértelmezett telepítés
Az Azure szinapszis Analytics Apache Spark a közös adattervezéshez, az adatok előkészítéséhez, a gépi tanuláshoz és az adatvizualizációs feladatokhoz tartozó könyvtárak teljes készletét nyújtja. A teljes kódtárak listája a következő címen érhető el: [Apache Spark Version support](apache-spark-version-support.md). 

A Spark-példány indításakor ezek a kódtárak automatikusan szerepelni fognak. Az extra Scala/Java-csomagok a Spark-készletben és a munkamenet-szinten is hozzáadhatók.

## <a name="workspace-packages"></a>Munkaterület-csomagok
A munkaterület-csomagok lehetnek egyéni vagy privát JAR-fájlok. Ezeket a csomagokat feltöltheti a munkaterületre, majd később hozzárendelheti őket egy adott Spark-készlethez.

Munkaterület-csomagok hozzáadása:
1. Navigáljon a   >  **munkaterület-csomagok** kezelése lapra.
2. Töltse fel a jar-fájljait a fájl választójának használatával.
3. Miután feltöltötte a fájlokat az Azure szinapszis munkaterületre, felveheti ezeket a jar-fájlokat egy adott Apache Spark készletbe.

![A munkaterület-csomagokat kiemelő képernyőkép.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Munkaterület-csomagok megtekintése")

## <a name="pool-libraries"></a>Készlet kódtárai
Miután azonosította a Spark-alkalmazáshoz használni kívánt Scala-és Java-csomagokat, telepítheti őket egy Spark-készletbe. A készleten futó összes jegyzetfüzet és feladat számára a készlet szintű kódtárak érhetők el.

A Spark Pool-kódtárak frissítéséhez navigáljon az Azure szinapszis studióhoz vagy Azure Portal. Itt kiválaszthatja a telepítendő munkaterület-kódtárakat. 

A módosítások mentése után a Spark-feladatok futtatják majd a telepítést, és gyorsítótárazzák az eredményül kapott környezetet a későbbi újrafelhasználáshoz. A feladat befejezése után új Spark-feladatok vagy jegyzetfüzet-munkamenetek fogják használni a frissített készlet kódtárait. 

> [!IMPORTANT]
> - Ha a telepített csomag nagy méretű, vagy hosszú ideig tart a telepítés, ez hatással van a Spark-példány indítási idejére.
> - A PySpark, a Python, a Scala/Java, a .NET vagy a Spark verzió módosítása nem támogatott.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Csomagok kezelése az Azure szinapszis studióból vagy Azure Portal
A Spark Pool-kódtárak az Azure szinapszis studióból vagy Azure Portal is kezelhetők. 

Tárak frissítése vagy hozzáadása Spark-készlethez:
1. Navigáljon az Azure szinapszis Analytics-munkaterületre a Azure Portal.

    Ha a **Azure Portalról** frissít:

    - A **szinapszis-erőforrások** szakaszban válassza a **Apache Spark készletek** fület, és válasszon ki egy Spark-készletet a listából.
     
    - Válassza ki a **csomagokat** a Spark-készlet **Beállítások** szakaszában.
  
    ![Képernyőfelvétel: a környezeti konfigurációs fájl feltöltése gomb.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python-kódtárak hozzáadása")
   
    Ha a következőt frissíti a **szinapszis studióból**:
    - Válassza a **felügyelet** elemet a fő navigációs panelen, majd válassza ki **Apache Spark készleteket**.

    - Válassza a **csomagok** szakaszt egy adott Spark-készlethez.
    ![Képernyőkép, amely kiemeli a Studio alkalmazásban a feltöltési környezet konfigurációs beállítását.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Python-kódtárak hozzáadása a studióból")
   
2. JAR-fájlok hozzáadásához navigáljon a **munkaterület-csomagok** szakaszhoz, és adja hozzá a készlethez. 
3. A módosítások mentése után a rendszer elindítja a rendszerfeladatot a megadott kódtárak telepítéséhez és gyorsítótárazásához. Ez a folyamat segít csökkenteni a munkamenetek általános indítási idejét. 
4. Miután a feladatot sikeresen elvégezte, minden új munkamenet fogja felvenni a frissített készlet-kódtárakat.

> [!IMPORTANT]
> Ha az **új beállítások kényszerítése** lehetőséget választja, akkor a kiválasztott Spark-készlet összes aktuális munkamenete megszűnik. A munkamenetek befejezése után meg kell várnia, amíg a készlet újra fog indulni. 
>
> Ha a beállítás nincs bejelölve, meg kell várnia, amíg az aktuális Spark-munkamenet véget ér, vagy manuálisan le is állíthatja. Ha a munkamenet véget ért, engedélyeznie kell a készlet újraindítását.

#### <a name="track-installation-progress-preview"></a>A telepítési folyamat nyomon követése (előzetes verzió)
A rendszer számára fenntartott Spark-feladatok minden alkalommal megindulnak, amikor egy készlet frissül egy új kódtárak készletével. Ez a Spark-feladatokban segít figyelni a könyvtár telepítésének állapotát. Ha a telepítés könyvtári ütközések vagy más problémák miatt meghiúsul, a Spark-készlet visszaáll az előző vagy az alapértelmezett állapotba. 

Emellett a felhasználók is ellenőrizhetik a telepítési naplókat a függőségi ütközések azonosítása érdekében, vagy megtekinthetik, hogy mely könyvtárak lettek telepítve a készlet frissítése során.

A naplók megtekintése:
1. Navigáljon a Spark-alkalmazások listához a **figyelés** lapon. 
2. Válassza ki a rendszer Spark-alkalmazási feladatot, amely megfelel a készlet frissítésének. Ezek a rendszerfeladatok a *SystemReservedJob-LibraryManagement* cím alatt futnak.
   ![Képernyőfelvétel: a rendszer számára fenntartott könyvtár feladatainak kiemelése.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Rendszerkönyvtár-feladat megtekintése")
3. Váltson át az **illesztőprogram** és az **StdOut** naplók megtekintéséhez. 
4. Az eredmények között megjelennek a csomagok telepítésével kapcsolatos naplók.
    ![Képernyőfelvétel: a rendszer számára fenntartott függvénytár-feladatok eredményeinek kiemelése.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Rendszerkönyvtári feladat állapotának megtekintése")

## <a name="session-scoped-libraries"></a>Munkamenet-hatókörű kódtárak 
A készlet szintjének könyvtárain kívül a munkamenet-hatókörű kódtárakat is megadhatja egy jegyzetfüzet-munkamenet elején.  A munkamenet-hatókörű kódtárak lehetővé teszik jar-csomagok megadását és használatát kizárólag notebook-munkameneten belül. 

Munkamenet-hatókörű kódtárak használata esetén fontos szem előtt tartani a következő szempontokat:
   - A munkamenet-hatókörű kódtárak telepítésekor csak az aktuális jegyzetfüzet fér hozzá a megadott könyvtárakhoz. 
   - Ezek a kódtárak nem befolyásolják más munkameneteket vagy feladatokat ugyanazzal a Spark-készlettel. 
   - Ezek a kódtárak az alap futtatókörnyezetre és a készlet szintjének kódtárara vannak telepítve. 
   - A notebook-kódtárak a legmagasabb prioritást fogják igénybe vételre.

A munkamenet-hatókörű Java-vagy Scala-csomagok megadásához a következő ```%%configure``` lehetőséget használhatja:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

Javasoljuk, hogy a (z)%% configure a jegyzetfüzet elején fusson. Az érvényes paraméterek teljes listájáért tekintse meg ezt a [dokumentumot](https://github.com/cloudera/livy#request-body) .

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)
- A kódtár telepítési hibáinak elhárítása: [függvénytár-hibák elhárítása](apache-spark-troubleshoot-library-errors.md)
