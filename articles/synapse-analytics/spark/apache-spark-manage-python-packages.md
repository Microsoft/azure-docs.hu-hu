---
title: Python-kódtárak kezelése Apache Spark
description: Ismerje meg, hogyan adhat hozzá és kezelhet a Apache Spark által használt Python-Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 1d64233fc477ec25f91bb73c744b10210571df41
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588343"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Python-kódtárak kezelése Apache Spark a Azure Synapse Analytics

A kódtárak olyan újrahasználható kódot biztosítanak, amely a programokba vagy projektekbe is belevehető. 

Különböző okokból szükség lehet a kiszolgáló nélküli Apache Spark készletkörnyezet frissítésére. Például a következőt találhatja:
- az egyik alapvető függősége kiadott egy új verziót.
- egy további csomagra van szüksége a gépi tanulási modell betanítása vagy az adatok előkészítése során.
- jobb csomagot talált, és már nincs szüksége a régebbi csomagra.

Ahhoz, hogy elérhetővé tegye a harmadik féltől származó vagy helyileg készült kódot az alkalmazások számára, telepíthet egy kódtárat az egyik kiszolgáló nélküli Apache Spark-készletre vagy jegyzetfüzet-munkamenetre. Ez a cikk bemutatja, hogyan kezelheti a Python-kódtárakat a kiszolgáló nélküli Apache Spark készletben.

## <a name="default-installation"></a>Alapértelmezett telepítés
Apache Spark a Azure Synapse Analytics általános adattervezési, adat-előkészítési, gépi tanulási és adatvizualizációs feladatokhoz használható kódtárakat tartalmaz. A teljes kódtárak listája a következő Apache Spark [található:](apache-spark-version-support.md). 

A Spark-példányok indításakor ezek a kódtárak automatikusan megjelennek. A Spark-készlet és a munkamenet szintjén további Python- és egyénileg létrehozott csomagok is hozzáadhatóak.

## <a name="pool-libraries"></a>Készletkódtárak
Miután azonosította azokat a Python-kódtárakat, amelyek a Spark-alkalmazáshoz használni szeretnék, telepítheti őket egy Spark-készletbe. A készletszintű kódtárak a készleten futó összes jegyzetfüzet és feladat számára elérhetők.

A fürtön a kódtárak telepítésének két elsődleges módja van:
-  Telepítsen egy munkaterületi kódtárat, amely munkaterület-csomagként lett feltöltve.
-  Adjon meg *egyrequirements.txt* vagy *Conda environment.yml környezeti* specifikációt az olyan adattárakból származó csomagok telepítéséhez, mint a PyPI, a Conda-Forge stb.

> [!IMPORTANT]
> - Ha a telepített csomag nagy méretű, vagy a telepítése hosszú időt vesz igénybe, az hatással van a Spark-példány indítási idejét.
> - A PySpark-, Python-, Scala-/Java-, .NET- vagy Spark-verziók megváltoztatása nem támogatott.
> - A DEP-kompatibilis munkaterületeken nem támogatott csomagok telepítése olyan külső adattárakból, mint a PyPI, a Conda-Forge vagy az alapértelmezett Conda-csatornák.

### <a name="install-python-packages"></a>Python-csomagok telepítése
A Python-csomagok olyan adattárakból telepíthetők, mint a PyPI Conda-Forge egy környezet specifikációs fájlja. 

#### <a name="environment-specification-formats"></a>Környezeti specifikációs formátumok

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
Egy *requirements.txt* fájl (a parancs kimenete) `pip freeze` használatával frissítheti a környezetet. A készlet frissítésekor az ebben a fájlban felsorolt csomagok a PyPI-ból tölthetők le. Ezután a rendszer gyorsítótárazza és menti a teljes függőségeket a készlet későbbi felhasználásához. 

Az alábbi kódrészlet a követelményfájl formátumát mutatja be. A PyPI-csomag neve és pontos verziója van felsorolva. Ez a fájl a pip-fagypont [referenciadokumentációban](https://pip.pypa.io/en/stable/reference/pip_freeze/) leírt formátumot követi. 

Ez a példa egy adott verziót rögzít. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML formátum (előzetes verzió)
Emellett egy *environment.yml* fájlt is meg tud adni a készletkörnyezet frissítéséhez. A fájlban felsorolt csomagok az alapértelmezett Conda-csatornákról, a Conda-Forge-ból és a PyPI-ból tölthetők le. A konfigurációs beállításokkal más csatornákat is megadhat, vagy eltávolíthatja az alapértelmezett csatornákat.

Ez a példa a csatornákat és a Conda/PyPI-függőségeket határozza meg. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
A környezet environment.yml fájlból való létrehozásával kapcsolatos részletekért lásd: Környezet létrehozása [egy environment.yml fájlból.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
)

#### <a name="update-python-packages"></a>Python-csomagok frissítése
Miután azonosította a környezet specifikációs fájlját vagy a Spark-készletre telepíteni kívánt kódtárakat, a Spark-készlet kódtárait a Azure Synapse Studióba vagy a Azure Portal. Itt meg kell adnia a környezet specifikációját, és kiválaszthatja a telepíteni kívánt munkaterület-kódtárakat. 

A módosítások mentése után egy Spark-feladat futtatja a telepítést, és gyorsítótárazza az eredményül kapott környezetet későbbi felhasználás céljából. A feladat befejezése után az új Spark-feladatok vagy jegyzetfüzet-munkamenetek a frissített készletkódtárakat fogják használni. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Csomagok kezelése a Azure Synapse Studióból vagy Azure Portal
A Spark-készlet kódtárai a Azure Synapse Studióból vagy a Azure Portal. 

Kódtárak frissítése vagy hozzáadása Spark-készlethez:
1. Lépjen a Azure Synapse Analytics munkaterületére a Azure Portal.

    Ha az alkalmazásból **frissít Azure Portal:**

    - A **Synapse-erőforrások szakaszban** válassza a **Apache Spark készletek** lapot, és válasszon ki egy Spark-készletet a listából.
     
    - Válassza a **Csomagok** lehetőséget **a** Spark-készlet Beállítások szakaszában.
  
    ![Képernyőkép a környezeti konfigurációs fájl feltöltése gomb kiemelésével.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python-kódtárak hozzáadása")
   
    Ha az alkalmazásból **frissít, Synapse Studio:**
    - Válassza **a kezelés** lehetőséget a fő navigációs panelen, majd válassza a Apache Spark **lehetőséget.**

    - Válassza ki **a Csomagok** szakaszt egy adott Spark-készlethez.
    ![Képernyőkép a Környezet feltöltése konfigurációs lehetőség kiemelésével a Studióból.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Python-kódtárak hozzáadása a Studióból")
   
2. Töltse fel a környezeti konfigurációs fájlt a lap  **Csomagok** szakaszában található fájlválasztóval.
3. További munkaterület-csomagokat **is kiválaszthat,** amelyek hozzáadva vannak a készlethez. 
4. A módosítások mentése után a rendszer elindítja a megadott kódtárak telepítését és gyorsítótárazása érdekében. Ez a folyamat csökkenti a munkamenet indításának teljes idejét. 
5. A feladat sikeres befejezése után minden új munkamenet felveszi a frissített készletkódtárakat.

> [!IMPORTANT]
> Az Új beállítások **kényszerítés lehetőségének kiválasztásával** a kiválasztott Spark-készlet összes aktuális munkamenetét be fogja vetni. A munkamenetek befejezését követően meg kell várnia a készlet újraindítását. 
>
> Ha ez a beállítás nincs bejelölve, meg kell várnia az aktuális Spark-munkamenet végét, vagy le kell állítania manuálisan. A munkamenet befejezése után újra kell indítania a készletet.


##### <a name="track-installation-progress-preview"></a>Telepítési folyamat nyomon követése (előzetes verzió)
A rendszer minden alkalommal elindít egy rendszer által fenntartott Spark-feladatot, amikor egy készletet új kódtárakkal frissít. Ez a Spark-feladat segít figyelni a kódtár telepítésének állapotát. Ha a telepítés kódtárütközések vagy egyéb problémák miatt meghiúsul, a Spark-készlet visszaáll korábbi vagy alapértelmezett állapotára. 

Emellett a felhasználók a telepítési naplókat is megvizsgálva azonosíthatnak függőségi ütközéseket, vagy láthatják, hogy mely kódtárak voltak telepítve a készlet frissítése során.

A naplók megtekintése:
1. Lépjen a Spark-alkalmazások listájára a **Monitor lapon.** 
2. Válassza ki a készletfrissítésnek megfelelő Spark-alkalmazásrendszer-feladatot. Ezek a rendszerfeladatok a *SystemReservedJob-LibraryManagement cím alatt futnak.*
   ![Képernyőkép a rendszer számára fenntartott kódtár feladatról.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Rendszerkönyvtár-feladat megtekintése")
3. Váltson az illesztőprogram **és az** **stdout naplók megtekintéséhez.** 
4. Az eredmények között látni fogja a függőségek telepítéséhez kapcsolódó naplókat.
    ![Képernyőkép a rendszer számára fenntartott kódtár feladatának eredményeiről.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Rendszerkönyvtár-feladat előrehaladásának megtekintése")

## <a name="install-wheel-files"></a>Kerekfájlok telepítése
A Python-wheel-fájlok gyakori módja a Python-kódtárak csomagolásának. A Azure Synapse Analytics a felhasználók feltöltheti a wheel-fájljaikat egy jól ismert helyre a Azure Data Lake Storage-fiókba, vagy feltöltheti őket az Azure Synapse-munkaterület csomagjának felületén.

### <a name="workspace-packages-preview"></a>Munkaterület-csomagok (előzetes verzió)
A munkaterület-csomagok egyéni vagy privát wheel-fájlok is lehetek. Ezeket a csomagokat feltöltheti a munkaterületre, majd később hozzárendelheti őket egy adott Spark-készlethez.

Munkaterület-csomagok hozzáadása:
1. Lépjen a **Munkaterület-csomagok**  >  **kezelése lapra.**
2. Töltse fel a wheel-fájlokat a fájlválasztóval.
3. Miután feltöltötte a fájlokat a Azure Synapse munkaterületre, hozzáadhatja ezeket a csomagokat egy adott Apache Spark készlethez.

![Képernyőkép a munkaterület-csomagok kiemeléséhez.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Munkaterület-csomagok megtekintése")

>[!WARNING]
>- A Azure Synapse-Apache Spark egyéni kódtárakat is fel lehet használni, amelyek vagy munkaterület-csomagként vannak feltöltve, vagy egy jól ismert Azure Data Lake Storage belül. Ezek a lehetőségek azonban nem használhatók egyidejűleg ugyanabban a Apache Spark készletben. Ha a csomagok mindkét módszerrel vannak megadva, csak a Munkaterület-csomagok listában megadott wheel-fájlok lesznek telepítve. 
>
>- Miután a munkaterületi csomagok (előzetes verzió) használatával csomagokat telepített egy adott Apache Spark-készletre, van egy korlátozás, amely szerint nem lehet csomagokat megadni ugyanazon készlet Storage-fiók elérési útján.  

### <a name="storage-account"></a>Tárfiók
Az egyénileg létrehozott wheel-csomagok úgy telepíthetők a Apache Spark-készletre, hogy az összes kerekfájlt feltöltik a Synapse-munkaterülethez kapcsolt Azure Data Lake Storage-fiókba (Gen2). 

A fájlokat a tárfiók alapértelmezett tárolója a következő elérési útra kell feltölteni: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> Bizonyos esetekben előfordulhat, hogy létre kell hoznia a fájl elérési útját a fenti struktúra alapján, ha még nem létezik. Előfordulhat például, hogy hozzá kell adni a mappát a mappához, ```python``` ```libraries``` ha még nem létezik.

> [!IMPORTANT]
> Az egyéni kódtárak Azure DataLake Storage módszerrel való telepítéséhez a **Storage-blobadatok** közreműködője vagy a **Storage-blobadatok** tulajdonosa engedélyekkel kell rendelkeznie az elsődleges Gen2 Storage-fiókon, amely az Azure Synapse Analytics munkaterülethez van kapcsolva.


## <a name="session-scoped-packages-preview"></a>Munkamenet-hatókörű csomagok (előzetes verzió)
A készletszintű csomagok mellett munkamenet-hatókörű kódtárakat is megadhat a jegyzetfüzet-munkamenet elején.  A munkamenet-hatókörrel kapcsolatos kódtárak lehetővéják egyéni Python-környezetek megadását és használatát egy jegyzetfüzet-munkamenetben. 

Munkamenet-hatókörű kódtárak használata esetén fontos szem előtt tartani a következőket:
   - Munkamenet-hatókörű kódtárak telepítésekor csak az aktuális jegyzetfüzet fér hozzá a megadott kódtárakhoz. 
   - Ezek a kódtárak nem befolyásolják az azonos Spark-készletet használó más munkameneteket vagy feladatokat. 
   - Ezek a kódtárak az alapszintű futásidejű és készletszintű kódtárakra vannak telepítve. 
   - A jegyzetfüzet-kódtárak élveznek elsőbbséget.

Munkamenet-hatókörű csomagok megadása:
1.  Lépjen a kiválasztott Spark-készletre, és győződjön meg arról, hogy engedélyezte a munkamenetszintű kódtárakat.  Ezt a beállítást a Manage Apache Spark pool Packages (Készletcsomagok  >  **kezelése) lapra** navigálva  >   ![engedélyezheti. Munkamenetcsomagok engedélyezése.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Munkamenet-csomagok engedélyezése")
2.  A beállítás alkalmazása után megnyithat egy jegyzetfüzetet, és kiválaszthatja a **Munkamenet-csomagok** >  **konfigurálása lehetőséget.**
  ![Munkamenetcsomagok megadása.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Munkamenet-konfiguráció frissítése")
3.  Itt feltölthet egy Conda *environment.yml fájlt* csomagok telepítéséhez vagy frissítéséhez egy munkameneten belül. Miután elindítja a munkamenetet, a rendszer telepíti a megadott kódtárakat. A munkamenet befejezése után ezek a kódtárak többé nem lesznek elérhetők, mivel a munkamenetre jellemzőek.

## <a name="verify-installed-libraries"></a>Telepített kódtárak ellenőrzése
Annak ellenőrzéséhez, hogy a megfelelő kódtárak megfelelő verziói telepítve vannak-e a PyPI-ból, futtassa a következő kódot:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
Bizonyos esetekben a Condából származó csomagverziók megtekintéséhez előfordulhat, hogy külön-külön kell megvizsgálnia a csomagverziót.

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett kódtárak megtekintése: [Apache Spark támogatása](apache-spark-version-support.md)
- Kódtár telepítési hibáinak elhárítása: [Kódtárhibák elhárítása](apache-spark-troubleshoot-library-errors.md)
- Privát Conda-csatorna létrehozása saját fiókkal Azure Data Lake Storage: [Conda privát csatornák](./spark/../apache-spark-custom-conda-channel.md)
