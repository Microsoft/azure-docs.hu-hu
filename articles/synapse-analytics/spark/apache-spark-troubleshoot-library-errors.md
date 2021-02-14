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
ms.openlocfilehash: 60ea97ea2df271f867febec3fa0f0826a18dbbbf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417459"
---
# <a name="troubleshoot-library-installation-errors"></a>A kódtár telepítési hibáinak elhárítása 
Ahhoz, hogy a harmadik féltől származó vagy helyileg létrehozott kódot elérhetővé tegye az alkalmazásai számára, telepíthet egy függvénytárat az egyik kiszolgáló nélküli Apache Spark-készletre. A requirements.txt fájlban felsorolt csomagok letöltése a PyPi a készlet indításakor történik. Ezt a követelményt a rendszer minden alkalommal felhasználja, amikor egy Spark-példányt létrehoznak a Spark-készletből. Miután telepítette a tárat egy Spark-készlethez, az minden munkamenet számára elérhető lesz, amely ugyanazt a készletet használja. 

Bizonyos esetekben előfordulhat, hogy a telepíteni kívánt könyvtár nem jelenik meg a Apache Spark-készletben. Ez az eset gyakran fordul elő, ha a megadott requirements.txt vagy könyvtárakban hiba történt. Ha a könyvtár telepítési folyamata során hiba történik, a Apache Spark készlet vissza fog térni a szinapszis alap futtatókörnyezetében megadott könyvtárakba.

A dokumentum célja a gyakori problémák biztosítása, valamint a kódtár telepítési hibáinak elhárítása.

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark-készlet frissítésének kényszerítése
Amikor frissíti a tárakat a Apache Spark készletben, ezek a módosítások a készlet újraindítása után lesznek felveszve. Ha aktív feladatokkal rendelkezik, ezek a feladatok továbbra is a Spark-készlet eredeti verzióján futnak.

A módosítások érvénybe lépéséhez válassza az **új beállítások kényszerítése** lehetőséget. Ezzel a beállítással megszűnik a kiválasztott Spark-készlet összes aktuális munkamenete. A munkamenetek befejezése után meg kell várnia, amíg a készlet újra fog indulni. 

![Python-kódtárak hozzáadása](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-kódtárak hozzáadása")

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
Ha hibaüzenetet kap, valószínűleg hiányzik a szükséges engedélyek. A szükséges engedélyek beszerzésének megismeréséhez tekintse meg a következő dokumentumot: [Storage blob-adatközreműködő vagy tárolási blob-adattulajdonosi engedélyek kiosztása](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-an-azure-built-in-role).

Emellett, ha egy folyamatot futtat, akkor a munkaterület MSI-fájljának a Storage blob-adattulajdonosi vagy a Storage blob adatközreműködői engedélyekkel is rendelkeznie kell. Ha meg szeretné tudni, hogyan adja meg a munkaterület identitását, keresse fel a következőt: [engedélyek megadása a munkaterület által felügyelt identitáshoz](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-requirements-file"></a>A követelmények fájljának bejelölése
A virtuális környezet frissítéséhez egy ***requirements.txt*** -fájl (a pip Freeze parancs kimenete) is használható. Ez a fájl a [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) Reference dokumentációjában ismertetett formátumot követi.

Fontos megjegyezni a következő korlátozásokat:
   -  A PyPI-csomag nevének a pontos verziószámmal együtt kell szerepelnie. 
   -  A követelmények fájljának tartalma nem tartalmazhat extra üres sorokat vagy karaktereket. 
   -  A [szinapszis futtatókörnyezete](apache-spark-version-support.md) olyan kódtárakat tartalmaz, amelyek előre telepítve vannak minden kiszolgáló nélküli Apache Spark-készletre. Az alapszintű futtatókörnyezetre előre telepített csomagok nem állíthatók vissza. A csomagokat csak hozzá lehet adni vagy frissíteni kell.
   -  A PySpark, a Python, a Scala/Java, a .NET vagy a Spark verzió módosítása nem támogatott.

A következő kódrészlet a követelmények fájl szükséges formátumát mutatja.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>A Wheel Files ellenőrzése
A szinapszis kiszolgáló nélküli Apache Spark készletek a Linux-disztribúción alapulnak. Ha közvetlenül a PyPI tölti le és telepíti a felni fájlokat, ügyeljen arra, hogy a Linux rendszerre épülő verziót válassza, és a Spark-készlettel megegyező Python-verzión fusson.

>[!IMPORTANT]
>Egyéni csomagok is hozzáadhatók és módosíthatók a munkamenetek között. A frissített csomag megjelenítéséhez azonban várnia kell, amíg a készlet és a munkamenet újra fog indulni.

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

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)

