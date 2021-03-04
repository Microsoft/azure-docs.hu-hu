---
title: Python-kódtárak kezelése Apache Sparkhoz
description: Megtudhatja, hogyan veheti fel és kezelheti a Apache Spark által használt Python-kódtárakat az Azure szinapszis Analyticsben.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4bb323e0e8f72456b6a522ede9a98d193e1c3c7e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098774"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Python-kódtárak kezelése Apache Sparkhoz az Azure szinapszis Analyticsben

A tárak újrafelhasználható kódot biztosítanak, amelyet a programok vagy projektek számára érdemes felvenni. 

Előfordulhat, hogy különböző okokból frissítenie kell a kiszolgáló nélküli Apache Spark Pool-környezetet. Előfordulhat például, hogy a következőket találja:
- az egyik alapvető függőség csak új verziót adott ki.
- További csomagra van szükség a gépi tanulási modell betanításához vagy az adatelőkészítéshez.
- jobb csomagot talált, és már nincs szüksége a régebbi csomagra.

Ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódot elérhetővé tegye az alkalmazásai számára, telepíthet egy függvénytárat az egyik kiszolgáló nélküli Apache Spark készletére vagy notebook-munkamenetére. Ebből a cikkből megtudhatja, hogyan kezelheti a Python-kódtárakat a kiszolgáló nélküli Apache Spark készletben.

## <a name="default-installation"></a>Alapértelmezett telepítés
Az Azure szinapszis Analytics Apache Spark a közös adattervezéshez, az adatok előkészítéséhez, a gépi tanuláshoz és az adatvizualizációs feladatokhoz tartozó könyvtárak teljes készletét nyújtja. A teljes kódtárak listája a következő címen érhető el: [Apache Spark Version support](apache-spark-version-support.md). 

A Spark-példány indításakor ezek a kódtárak automatikusan szerepelni fognak. A Spark-készletben és a munkamenet-szinten további Python-és egyéni kialakítású csomagok is hozzáadhatók.

## <a name="pool-libraries"></a>Készlet kódtárai
Miután azonosította a Spark-alkalmazáshoz használni kívánt Python-kódtárakat, egy Spark-készletbe is telepítheti őket. A készleten futó összes jegyzetfüzet és feladat számára a készlet szintű kódtárak érhetők el.

A tárak fürtön való telepítésének két fő módja van:
-  Olyan munkaterület-függvénytárat telepítsen, amely munkaterület-csomagként lett feltöltve.
-  Adjon meg egy *requirements.txt* vagy *Conda környezetet. a YML* környezeti specifikációja a csomagok olyan adattárakból való telepítésére szolgál, mint a PyPI, a Conda-Forge és sok más.

> [!IMPORTANT]
> - Ha a telepített csomag nagy méretű, vagy hosszú ideig tart a telepítés, ez hatással van a Spark-példány indítási idejére.
> - A PySpark, a Python, a Scala/Java, a .NET vagy a Spark verzió módosítása nem támogatott.
> - A csomagokat olyan külső adattárakból kell telepíteni, mint a PyPI, a Conda-Forge, vagy az alapértelmezett Conda-csatornák nem támogatottak a DEP-kompatibilis munkaterületeken.

### <a name="install-python-packages"></a>Python-csomagok telepítése
A Python-csomagok olyan adattárakból telepíthetők, mint a PyPI és a Conda-Forge egy környezeti specifikációs fájl megadásával. 

#### <a name="environment-specification-formats"></a>Környezeti specifikáció formátumai

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
A környezet frissítéséhez egy *requirements.txt* fájl (kimenet a `pip freeze` parancsból) használható. Egy készlet frissítésekor a fájlban felsorolt csomagok letöltődnek a PyPI. A rendszer ezután gyorsítótárazza és menti a teljes függőségeket a készlet későbbi újrafelhasználásához. 

A következő kódrészlet a követelmények fájl formátumát mutatja. A PyPI-csomag neve a pontos verzióval együtt jelenik meg. Ez a fájl a [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) Reference dokumentációjában ismertetett formátumot követi. 

Ez a példa egy adott verziót PIN-kódra mutat. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML formátuma (előzetes verzió)
Emellett a *környezeti. YML* fájlt is megadhatja a készlet környezetének frissítéséhez. Az ebben a fájlban felsorolt csomagok az alapértelmezett Conda csatornákon, a Conda-Forge és a PyPI tölthetők le. A konfigurációs beállítások használatával más csatornákat is megadhat, vagy eltávolíthatja az alapértelmezett csatornákat.

Ez a példa a csatornákat és a Conda/PyPI függőségeket határozza meg. 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
A környezet. YML fájl létrehozásával kapcsolatos további információkért lásd: [környezet létrehozása környezetből. YML fájl](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Python-csomagok frissítése
Miután azonosította a Spark-készletre telepíteni kívánt környezeti specifikációs fájlt vagy kódtárakat, a Spark-készlet kódtárait az Azure szinapszis studióhoz vagy Azure Portalhoz való navigálással is frissítheti. Itt megadhatja a környezeti specifikációt, és kiválaszthatja a telepítendő munkaterület-kódtárakat. 

A módosítások mentése után a Spark-feladatok futtatják majd a telepítést, és gyorsítótárazzák az eredményül kapott környezetet a későbbi újrafelhasználáshoz. A feladat befejezése után új Spark-feladatok vagy jegyzetfüzet-munkamenetek fogják használni a frissített készlet kódtárait. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Csomagok kezelése az Azure szinapszis studióból vagy Azure Portal
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
   
2. Töltse fel a környezet konfigurációs fájlját a lap  **csomagok** területén található fájl-választó használatával.
3. A készlethez hozzáadandó további **munkaterület-csomagokat** is kiválaszthatja. 
4. A módosítások mentése után a rendszer elindítja a rendszerfeladatot a megadott kódtárak telepítéséhez és gyorsítótárazásához. Ez a folyamat segít csökkenteni a munkamenetek általános indítási idejét. 
5. Miután a feladatot sikeresen elvégezte, minden új munkamenet fogja felvenni a frissített készlet-kódtárakat.

> [!IMPORTANT]
> Ha az **új beállítások kényszerítése** lehetőséget választja, akkor a kiválasztott Spark-készlet összes aktuális munkamenete megszűnik. A munkamenetek befejezése után meg kell várnia, amíg a készlet újra fog indulni. 
>
> Ha a beállítás nincs bejelölve, meg kell várnia, amíg az aktuális Spark-munkamenet véget ér, vagy manuálisan le is állíthatja. Ha a munkamenet véget ért, engedélyeznie kell a készlet újraindítását.


##### <a name="track-installation-progress-preview"></a>A telepítési folyamat nyomon követése (előzetes verzió)
A rendszer számára fenntartott Spark-feladatok minden alkalommal megindulnak, amikor egy készlet frissül egy új kódtárak készletével. Ez a Spark-feladatokban segít figyelni a könyvtár telepítésének állapotát. Ha a telepítés könyvtári ütközések vagy más problémák miatt meghiúsul, a Spark-készlet visszaáll az előző vagy az alapértelmezett állapotba. 

Emellett a felhasználók is ellenőrizhetik a telepítési naplókat a függőségi ütközések azonosítása érdekében, vagy megtekinthetik, hogy mely könyvtárak lettek telepítve a készlet frissítése során.

A naplók megtekintése:
1. Navigáljon a Spark-alkalmazások listához a **figyelés** lapon. 
2. Válassza ki a rendszer Spark-alkalmazási feladatot, amely megfelel a készlet frissítésének. Ezek a rendszerfeladatok a *SystemReservedJob-LibraryManagement* cím alatt futnak.
   ![Képernyőfelvétel: a rendszer számára fenntartott könyvtár feladatainak kiemelése.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Rendszerkönyvtár-feladat megtekintése")
3. Váltson át az **illesztőprogram** és az **StdOut** naplók megtekintéséhez. 
4. Az eredmények között megjelennek a függőségek telepítésével kapcsolatos naplók.
    ![Képernyőfelvétel: a rendszer számára fenntartott függvénytár-feladatok eredményeinek kiemelése.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Rendszerkönyvtári feladat állapotának megtekintése")

## <a name="install-wheel-files"></a>A Wheel Files telepítése
A Python Wheels-fájlok a Python-kódtárak csomagolásának gyakori módja. Az Azure szinapszis Analyticsben a felhasználók az Azure szinapszis munkaterület-csomagok felületén tölthetik fel a felni fájljaikat egy jól ismert helyre a Azure Data Lake Storage fiók vagy a feltöltés használatával.

### <a name="workspace-packages-preview"></a>Munkaterület-csomagok (előzetes verzió)
A munkaterület-csomagok lehetnek egyéni vagy privát felni fájlok. Ezeket a csomagokat feltöltheti a munkaterületre, majd később hozzárendelheti őket egy adott Spark-készlethez.

Munkaterület-csomagok hozzáadása:
1. Navigáljon a   >  **munkaterület-csomagok** kezelése lapra.
2. Töltse fel a görgetőkerék fájljait a fájl választójának használatával.
3. Miután feltöltötte a fájlokat az Azure szinapszis munkaterületre, felveheti ezeket a csomagokat egy adott Apache Spark készletbe.

![A munkaterület-csomagokat kiemelő képernyőkép.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Munkaterület-csomagok megtekintése")

### <a name="storage-account"></a>A(z)
Az egyéni Builder-csomagok telepíthetők a Apache Spark készletbe úgy, hogy az összes kerék fájlt feltölti a szinapszis munkaterülethez csatolt Azure Data Lake Storage (Gen2) fiókba. 

A fájlokat a Storage-fiók alapértelmezett tárolójában fel kell tölteni a következő elérési útra: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Előfordulhat, hogy a mappán belül fel kell vennie a ```python``` mappát, ```libraries``` Ha még nem létezik.

> [!IMPORTANT]
> Ha az Azure DataLake Storage metódussal szeretne egyéni kódtárakat telepíteni, a **Storage blob-Adatközreműködőinek** vagy a **Storage blob-adattulajdonosi** engedélyekkel kell rendelkeznie az Azure szinapszis Analytics-munkaterülethez kapcsolódó elsődleges Gen2 Storage-fiókban.

>[!WARNING]
> Egyéni kerék-fájlok megadásakor a felhasználók a Storage-fiókban és a munkaterület-függvénytár felületén sem tudnak kerék-fájlokat biztosítani. Ha mindkettő meg van adva, a rendszer csak a munkaterület-csomagok listájában megadott felni fájlokat telepíti. 

## <a name="session-scoped-packages-preview"></a>Munkamenet-hatókörű csomagok (előzetes verzió)
A készlet szintjének csomagokon kívül a munkamenet-hatókörű kódtárakat is megadhatja egy jegyzetfüzet-munkamenet elején.  A munkamenet-hatókörű kódtárak segítségével egyéni Python-környezeteket adhat meg és használhat egy jegyzetfüzet-munkamenetben. 

Munkamenet-hatókörű kódtárak használata esetén fontos szem előtt tartani a következő szempontokat:
   - A munkamenet-hatókörű kódtárak telepítésekor csak az aktuális jegyzetfüzet fér hozzá a megadott könyvtárakhoz. 
   - Ezek a kódtárak nem befolyásolják más munkameneteket vagy feladatokat ugyanazzal a Spark-készlettel. 
   - Ezek a kódtárak az alap futtatókörnyezetre és a készlet szintjének kódtárara vannak telepítve. 
   - A notebook-kódtárak a legmagasabb prioritást fogják igénybe vételre.

Munkamenet-hatókörű csomagok meghatározása:
1.  Navigáljon a kiválasztott Spark-készlethez, és győződjön meg arról, hogy engedélyezte a munkamenet-szintű kódtárakat.  Ha ezt a beállítást szeretné engedélyezni, lépjen a   >  **Apache Spark készlet** kezelése lapra, és  >   engedélyezze a ![munkamenet-csomagokat.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Munkamenet-csomagok engedélyezése")
2.  A beállítás alkalmazása után nyisson meg egy jegyzetfüzetet, és válassza a **munkamenet**- >  **csomagok** konfigurálása lehetőséget.
  ![Munkamenet-csomagok meghatározása.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Munkamenet-konfiguráció frissítése")
3.  Itt feltöltheti a Conda *Environment. YML* fájlt egy munkameneten belüli csomagok telepítéséhez vagy frissítéséhez. A munkamenet elindítása után a rendszer telepíti a megadott kódtárakat. Ha a munkamenet véget ér, ezek a kódtárak többé nem lesznek elérhetők, mert a munkamenetre jellemzőek.

## <a name="verify-installed-libraries"></a>Telepített kódtárak ellenőrzése
A következő kód futtatásával ellenőrizheti, hogy a megfelelő kódtárak megfelelő verziója van-e telepítve a PyPI-ből:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Bizonyos esetekben előfordulhat, hogy meg kell vizsgálnia a csomag verziószámát a Conda-ből.

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)
- A kódtár telepítési hibáinak elhárítása: [függvénytár-hibák elhárítása](apache-spark-troubleshoot-library-errors.md)
- Hozzon létre egy privát Conda-csatornát a Azure Data Lake Storage fiók használatával: [privát csatornák Conda](./spark/../apache-spark-custom-conda-channel.md)
