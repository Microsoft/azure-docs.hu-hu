---
title: A kódtár telepítési hibáinak elhárítása
description: Ez az oktatóanyag áttekintést nyújt a függvénytár-telepítési hibák elhárításáról.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694920"
---
# <a name="troubleshoot-library-installation-errors"></a>A kódtár telepítési hibáinak elhárítása 
Ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódot elérhetővé tegye az alkalmazásai számára, telepíthet egy függvénytárat az egyik kiszolgáló nélküli Apache Spark-készletre. A requirements.txt fájlban felsorolt csomagok letöltése a PyPi a készlet indításakor történik. Ezt a követelményt a rendszer minden alkalommal felhasználja, amikor egy Spark-példányt létrehoznak a Spark-készletből. Miután telepített egy tárat egy Spark-készlethez, az minden munkamenet számára elérhető, amely ugyanazt a készletet használja. 

Bizonyos esetekben előfordulhat, hogy egy könyvtár nem jelenik meg a Apache Spark-készletben. Ez az eset gyakran fordul elő, ha a megadott requirements.txt vagy könyvtárakban hiba történt. Ha a könyvtár telepítési folyamata során hiba lép fel, a Apache Spark készlet vissza fog térni a szinapszis alap futtatókörnyezetében megadott könyvtárakba.

A dokumentum célja a gyakori problémák biztosítása, valamint a kódtár telepítési hibáinak elhárítása.

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark-készlet frissítésének kényszerítése
Amikor frissíti a tárakat a Apache Spark készletben, ezek a módosítások a készlet újraindítása után lesznek felveszve. Ha aktív feladatokkal rendelkezik, ezek a feladatok továbbra is a Spark-készlet eredeti verzióján futnak.

A módosítások érvénybe lépéséhez válassza az **új beállítások kényszerítése** lehetőséget. Ezzel a beállítással megszűnik a kiválasztott Spark-készlet összes aktuális munkamenete. A munkamenetek befejezése után meg kell várnia a készlet újraindítását. 

![Python-kódtárak hozzáadása](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-kódtárak hozzáadása")

## <a name="track-installation-progress"></a>A telepítési folyamat nyomon követése
A rendszer által fenntartott Spark-feladatok minden alkalommal elindulnak, amikor egy készlet frissül a tárak új készletével. Ez a Spark-feladatokban segít figyelni a könyvtár telepítésének állapotát. Ha a telepítés könyvtárbeli ütközések vagy más problémák miatt meghiúsul, a Spark-készlet visszaáll az előző vagy az alapértelmezett állapotba. 

Emellett a felhasználók is ellenőrizhetik a telepítési naplókat a függőségi ütközések azonosítása érdekében, vagy megtekinthetik, hogy mely könyvtárak lettek telepítve a készlet frissítése során.

A naplók megtekintése:
1. Navigáljon a Spark-alkalmazások listához a **figyelés** lapon. 
2. Válassza ki a rendszer Spark-alkalmazási feladatot, amely megfelel a készlet frissítésének. Ezek a rendszerfeladatok a *SystemReservedJob-LibraryManagement* cím alatt futnak.
   ![Képernyőfelvétel: a rendszer számára fenntartott könyvtár feladatainak kiemelése.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Rendszerkönyvtár-feladat megtekintése")
3. Váltson át az **illesztőprogram** és az **StdOut** naplók megtekintéséhez. 
4. Az eredmények között megjelennek a csomagok telepítésével kapcsolatos naplók.
    ![Képernyőfelvétel: a rendszer számára fenntartott függvénytár-feladatok eredményeinek kiemelése.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Rendszerkönyvtári feladat állapotának megtekintése")

## <a name="validate-your-permissions"></a>Engedélyek ellenőrzése
A kódtárak telepítéséhez és frissítéséhez a **Storage blob Adatközreműködőinek** vagy a **Storage blob-adattulajdonosi** engedélyekkel kell rendelkeznie az Azure szinapszis Analytics-munkaterülethez kapcsolódó elsődleges Azure Data Lake Storage Gen2 Storage-fiókban.

A következő kód futtatásával ellenőrizheti, hogy rendelkezik-e ezekkel az engedélyekkel:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Ha hibaüzenetet kap, valószínűleg hiányzik a szükséges engedélyek. A szükséges engedélyek beszerzésének megismeréséhez tekintse meg a következő dokumentumot: [Storage blob-adatközreműködő vagy tárolási blob-adattulajdonosi engedélyek kiosztása](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role).

Emellett, ha egy folyamatot futtat, akkor a munkaterület MSI-fájljának a Storage blob-adattulajdonosi vagy a Storage blob adatközreműködői engedélyekkel is rendelkeznie kell. Ha meg szeretné tudni, hogyan adja meg a munkaterület identitását, keresse fel a következőt: [engedélyek megadása a munkaterület által felügyelt identitáshoz](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>A környezet konfigurációs fájljának keresése
A Conda-környezet frissítésére szolgáló környezeti konfigurációs fájl használható. A Python-készlet felügyeletének elfogadható fájlformátumait [itt](./apache-spark-manage-python-packages.md)találja.

Fontos megjegyezni a következő korlátozásokat:
   -  A követelmények fájljának tartalma nem tartalmazhat extra üres sorokat vagy karaktereket. 
   -  A [szinapszis futtatókörnyezete](apache-spark-version-support.md) olyan kódtárakat tartalmaz, amelyek előre telepítve vannak minden kiszolgáló nélküli Apache Spark-készletre. Az alapszintű futtatókörnyezetre előre telepített csomagok nem távolíthatók el és nem távolíthatók el.
   -  A PySpark, a Python, a Scala/Java, a .NET vagy a Spark verzió módosítása nem támogatott.
   -  A Python munkamenet-hatókörű kódtárak csak a YML kiterjesztésű fájlokat fogadják el.

## <a name="validate-wheel-files"></a>A Wheel Files ellenőrzése
A szinapszis kiszolgáló nélküli Apache Spark készletek a Linux-disztribúción alapulnak. Ha közvetlenül a PyPI tölti le és telepíti a felni fájlokat, ügyeljen arra, hogy a Linux rendszerre épülő verziót válassza, és a Spark-készlettel megegyező Python-verzión fusson.

>[!IMPORTANT]
>Egyéni csomagok is hozzáadhatók és módosíthatók a munkamenetek között. A frissített csomag megtekintéséhez azonban várnia kell, hogy a készlet és a munkamenet újrainduljon.

## <a name="check-for-dependency-conflicts"></a>Függőségi ütközések keresése
 Általánosságban elmondható, hogy a Python függőségi feloldás trükkös megoldás. A függőségi ütközések helyi hibakereséséhez létrehozhatja saját virtuális környezetét a szinapszis futtatókörnyezet alapján, és ellenőrizheti a módosításokat.

A környezet újbóli létrehozása és a frissítések ellenőrzése:
 1. [Töltse le](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) a sablont a szinapszis futtatókörnyezet helyi újbóli létrehozásához. A sablon és a tényleges szinapszis-környezet között enyhe különbségek lehetnek.
   
 2. Hozzon létre egy virtuális környezetet az [alábbi utasítások](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html)alapján. Ez a környezet lehetővé teszi egy elkülönített Python-telepítés létrehozását a könyvtárak megadott listájával. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. A használatával ``pip install -r <provide your req.txt file>`` frissítheti a virtuális környezetet a megadott csomagokkal. Ha a telepítés hibát jelez, akkor ütközés lehet a szinapszis alapszintű futtatókörnyezetben előre telepített, illetve a megadott követelmények fájlban meghatározott adatok között. Ezeket a függőségi ütközéseket fel kell oldani ahhoz, hogy a frissített kódtárak a kiszolgáló nélküli Apache Spark készleten legyenek lekérdezve.

>[!IMPORTANT]
>A problémák a pip és a Conda együttes használatakor is származhat belőle. A pip és a Conda összevonásakor a legjobb megoldás a [javasolt ajánlott eljárások](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)követése.

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)