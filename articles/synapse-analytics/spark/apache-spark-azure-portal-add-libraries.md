---
title: Apache Spark kódtárainak kezelése
description: Megtudhatja, hogyan veheti fel és kezelheti a Apache Spark által használt könyvtárakat az Azure szinapszis Analyticsben.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 0458fb8b140166b7bdf0fc0df41dbb207fdce3c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518521"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Apache Spark kódtárainak kezelése az Azure szinapszis Analyticsben

A tárak újrafelhasználható kódot biztosítanak, amelyet a programok vagy projektek számára érdemes felvenni. Ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódot elérhetővé tegye az alkalmazásai számára, telepíthet egy függvénytárat az egyik kiszolgáló nélküli Apache Spark-készletre. Miután telepítette a tárat egy Spark-készlethez, az minden munkamenet számára elérhető lesz, amely ugyanazt a készletet használja. 

## <a name="before-you-begin"></a>Előkészületek
- A kódtárak telepítéséhez és frissítéséhez a **Storage blob Adatközreműködőinek** vagy a **Storage blob-adattulajdonosi** engedélyekkel kell rendelkeznie az Azure szinapszis Analytics-munkaterülethez csatolt elsődleges Gen2 Storage-fiókban.
  
## <a name="default-installation"></a>Alapértelmezett telepítés
Apache Spark az Azure szinapszis Analyticsben a teljes anacondas telepítése plusz további könyvtárakat tartalmaz. A teljes kódtárak listája a következő címen érhető el: [Apache Spark Version support](apache-spark-version-support.md). 

A Spark-példány indításakor ezek a kódtárak automatikusan szerepelni fognak. A Spark-készlet szintjén további Python-és egyéni kialakítású csomagok is hozzáadhatók.


## <a name="manage-python-packages"></a>Python-csomagok kezelése
Miután azonosította a Spark-alkalmazáshoz használni kívánt kódtárakat, egy Spark-készletbe is telepítheti őket. 

 A virtuális környezet frissítéséhez *requirements.txt* fájl (a parancs kimenete `pip freeze` ) használható. A telepítéshez vagy frissítéshez a fájlban felsorolt csomagok letöltése a PyPI a készlet indításakor történik. Ezt a követelményt a rendszer minden alkalommal felhasználja, amikor egy Spark-példányt létrehoznak a Spark-készletből.

> [!IMPORTANT]
> - Ha a telepített csomag nagy méretű, vagy hosszú ideig tart a telepítés, ez hatással van a Spark-példány indítási idejére.
> - A telepítéskor fordítási támogatást igénylő csomagok, például a GCC, nem támogatottak.
> - A csomagokat nem lehet leértékelni, csak a Hozzáadás vagy a frissítés lehetőségre.
> - A PySpark, a Python, a Scala/Java, a .NET vagy a Spark verzió módosítása nem támogatott.
> - A csomagok telepítése a PyPI-ből nem támogatott a DEP-kompatibilis munkaterületeken.


### <a name="requirements-format"></a>Követelmények formátuma

A következő kódrészlet a követelmények fájl formátumát mutatja. A PyPi-csomag neve a pontos verzióval együtt jelenik meg. Ez a fájl a [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) Reference dokumentációjában ismertetett formátumot követi. Ez a példa egy adott verziót PIN-kódra mutat. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Python-csomagok telepítése
A Spark-alkalmazás fejlesztése során előfordulhat, hogy frissítenie kell a meglévőt, vagy új kódtárakat kell telepítenie. A tárak a készlet létrehozásakor vagy azt követően is frissíthetők.

> [!IMPORTANT]
> A kódtárak telepítéséhez a tárolási blob adatközreműködőinek vagy a Storage blob-adattulajdonosi engedélyekkel kell rendelkeznie a szinapszis munkaterülethez csatolt elsődleges Gen2 Storage-fiókban.

#### <a name="install-packages-during-pool-creation"></a>Csomagok telepítése a készlet létrehozása során
Tárak telepítése Spark-készletre a készlet létrehozása során:
   
1. Navigáljon az Azure szinapszis Analytics-munkaterületre a Azure Portal.
   
2. Válassza a **létrehozás Apache Spark készlet létrehozása** lehetőséget, majd a **További beállítások** lapot. 
   
3. Töltse fel a környezet konfigurációs fájlját a lap **csomagok** területén található fájl-választó használatával. 
   
    ![Python-kódtárak hozzáadása a készlet létrehozása során](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Python-kódtárak hozzáadása")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Csomagok telepítése a szinapszis munkaterületről
További kódtárak frissítése vagy hozzáadása Spark-készlethez az Azure szinapszis Analytics-portálon:

1.  Navigáljon az Azure szinapszis Analytics-munkaterületre a Azure Portal.
   
2.  Indítsa el az Azure szinapszis Analytics-munkaterületet a Azure Portal.

3.  Válassza a **felügyelet** elemet a fő navigációs panelen, majd válassza ki **Apache Spark készleteket**.
   
4. Válasszon egyetlen Spark-készletet, és töltse fel a környezet konfigurációs fájlját a lap  **csomagok** területén található fájl-választóval.

    ![Python-kódtárak hozzáadása a szinapszisban](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Csomagok telepítése a Azure Portal
Függvénytár telepítése Spark-készletre közvetlenül a Azure Portal:
   
 1. Navigáljon az Azure szinapszis Analytics-munkaterületre a Azure Portal.
   
 2. A **szinapszis-erőforrások** szakaszban válassza a **Apache Spark készletek** fület, és válasszon ki egy Spark-készletet a listából.
   
 3. Válassza a **csomagok** lehetőséget a Spark-készlet **Beállítások** szakaszában. 

 4. Töltse fel a környezet konfigurációs fájlját a fájl-választó használatával.

    ![Képernyőfelvétel: a környezeti konfigurációs fájl feltöltése gomb.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python-kódtárak hozzáadása")

### <a name="verify-installed-libraries"></a>Telepített kódtárak ellenőrzése

A következő kód futtatásával ellenőrizheti, hogy telepítve vannak-e a megfelelő kódtárak megfelelő verziói

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Python-csomagok frissítése
A csomagok bármikor hozzáadhatók vagy módosíthatók a munkamenetek között. Az új csomag-konfigurációs fájl felülírja a meglévő csomagokat és verziókat.  

Könyvtár frissítése vagy eltávolítása:
1. Navigáljon az Azure szinapszis Analytics-munkaterületre. 

2. A Azure Portal vagy az Azure szinapszis munkaterületének használatával válassza ki a frissíteni kívánt **Apache Spark-készletet** .

3. Navigáljon a **csomagok** szakaszhoz, és töltsön fel egy új környezeti konfigurációs fájlt
   
4. A módosítások mentése után le kell állítania az aktív munkameneteket, és újra kell indítania a készletet. Lehetőség van arra is, hogy az aktív munkamenetek befejezéséhez a jelölőnégyzet bejelölésével **kényszerítse az új beállítások** megadását.

    ![Python-kódtárak hozzáadása](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-kódtárak hozzáadása")
   

> [!IMPORTANT]
> Ha az **új beállítások kényszerítése** lehetőséget választja, akkor a kiválasztott Spark-készlet összes aktuális munkamenete megszűnik. A munkamenetek befejezése után meg kell várnia, amíg a készlet újra fog indulni. 
>
> Ha a beállítás nincs bejelölve, meg kell várnia, amíg az aktuális Spark-munkamenet véget ér, vagy manuálisan le is állíthatja. Ha a munkamenet véget ért, engedélyeznie kell a készlet újraindítását. 


## <a name="manage-a-python-wheel"></a>Python-kerék kezelése

### <a name="install-a-custom-wheel-file"></a>Egyéni kör fájljának telepítése
Az egyéni Builder-csomagok telepíthetők a Apache Spark készletbe úgy, hogy az összes kerék fájlt feltölti a szinapszis munkaterülethez csatolt Azure Data Lake Storage (Gen2) fiókba. 

A fájlokat a Storage-fiók alapértelmezett tárolójában fel kell tölteni a következő elérési útra: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Előfordulhat, hogy a mappán belül fel kell vennie a ```python``` mappát, ```libraries``` Ha még nem létezik.

>[!IMPORTANT]
>Egyéni – a-csomagok hozzáadhatók és módosíthatók a munkamenetek között. A frissített csomag megjelenítéséhez azonban várnia kell, amíg a készlet és a munkamenet újra fog indulni.

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)
