---
title: Kódtár telepítési hibáinak elhárítása
description: Ez az oktatóanyag áttekintést nyújt a kódtár telepítési hibáinak elhárításáról.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588309"
---
# <a name="troubleshoot-library-installation-errors"></a>Kódtár telepítési hibáinak elhárítása 
Ahhoz, hogy elérhetővé tegye a harmadik féltől származó vagy helyileg készült kódot az alkalmazások számára, telepíthet egy kódtárat az egyik kiszolgáló nélküli Apache Spark készletre. A rendszer a készlet indításakor letölti a requirements.txt fájlban felsorolt csomagokat a PyPi-fájlból. A rendszer ezt a követelményfájlt használja minden alkalommal, amikor spark-példányt hoz létre az abból a Spark-készletből. Miután telepített egy kódtárat egy Spark-készlethez, az elérhető lesz minden munkamenet számára, amely ugyanazt a készletet használja. 

Bizonyos esetekben előfordulhat, hogy egy kódtár nem jelenik meg a Apache Spark készletben. Ez az eset gyakran akkor fordul elő, ha hiba történik a megadott requirements.txt kódtárakban. Ha hiba történik a kódtár telepítési folyamatában, a Apache Spark-készlet visszaáll a Synapse-alap-futtatásban megadott kódtárakra.

A dokumentum célja, hogy gyakori problémákat biztosítson, és segítséget nyújtson a kódtár telepítési hibáinak hibakeresésében.

## <a name="force-update-your-apache-spark-pool"></a>A Apache Spark kényszerített frissítése
A készlet kódtárainak Apache Spark a készlet újraindítása után a rendszer ezeket a módosításokat fogja átveszni. Ha vannak aktív feladatok, ezek a feladatok továbbra is a Spark-készlet eredeti verzióján fognak futni.

A módosításokat kényszerítheti az alkalmazásra az **Új beállítások kényszerítés lehetőségének kiválasztásával.** Ez a beállítás a kiválasztott Spark-készlet összes aktuális munkamenetét be fogja vetni. A munkamenetek befejezését követően meg kell várnia, amíg a készlet újraindul. 

![Python-kódtárak hozzáadása](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-kódtárak hozzáadása")

## <a name="track-installation-progress"></a>Telepítési folyamat nyomon követése
A rendszer által fenntartott Spark-feladat minden alkalommal elindul, amikor egy készletet új kódtárakkal frissít. Ez a Spark-feladat segít figyelni a kódtár telepítésének állapotát. Ha a telepítés kódtárütközések vagy egyéb problémák miatt meghiúsul, a Spark-készlet visszaáll az előző vagy alapértelmezett állapotára. 

A felhasználók emellett a telepítési naplókat is megvizsgálva azonosíthatnak függőségi ütközéseket, vagy láthatják, hogy mely kódtárak voltak telepítve a készlet frissítése során.

A naplók megtekintése:
1. Lépjen a Spark-alkalmazások listájára a **Monitor lapon.** 
2. Válassza ki a készletfrissítésnek megfelelő Spark-alkalmazásrendszer-feladatot. Ezek a rendszerfeladatok *a SystemReservedJob-LibraryManagement cím alatt futnak.*
   ![Képernyőkép a rendszer számára fenntartott kódtár feladatról.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Rendszerkönyvtár-feladat megtekintése")
3. Váltson az illesztőprogram **és az** **stdout naplók megtekintéséhez.** 
4. Az eredmények között láthatja a csomagok telepítéséhez kapcsolódó naplókat.
    ![Képernyőkép a rendszer számára fenntartott kódtár feladatának eredményeiről.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Rendszerkönyvtár-feladat előrehaladásának megtekintése")

## <a name="validate-your-permissions"></a>Az engedélyek ellenőrzése
A kódtárak telepítéséhez és frissítéséhez a  **Storage-blobadatok** közreműködője vagy a Storage-blobadatok tulajdonosa engedélyekkel kell rendelkeznie az elsődleges Azure Data Lake Storage Gen2 Storage-fiókon, amely az Azure Synapse Analytics munkaterülethez van kapcsolva.

Az alábbi kód futtatásával ellenőrizheti, hogy rendelkezik-e ezekkel az engedélyekkel:

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
Ha hibaüzenetet kap, valószínűleg hiányoznak a szükséges engedélyek. A szükséges engedélyek beszerzéséről a következő dokumentumban olvashat: Storage-blobadatok közreműködője vagy [Storage-blobadatok tulajdonosának jogosultságai.](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)

Emellett ha folyamatot futtat, akkor a munkaterület MSI-nek Storage-blobadatok tulajdonosa vagy Storage-blobadatok közreműködője engedéllyel is rendelkeznie kell. Ha meg szeretne ismerkedni a munkaterület identitásának ezzel az engedéllyel, olvassa el a következőt: Engedélyek megadása a munkaterület [által felügyelt identitásnak.](../security/how-to-grant-workspace-managed-identity-permissions.md)

## <a name="check-the-environment-configuration-file"></a>A környezet konfigurációs fájljának ellenőrzése
A Conda-környezet frissítéséhez környezeti konfigurációs fájl használható. A Python-készletkezelés elfogadható fájlformátumai itt [vannak felsorolva.](./apache-spark-manage-python-packages.md)

Fontos megjegyezni a következő korlátozásokat:
   -  A követelményfájl tartalma nem tartalmazhat további üres sorokat vagy karaktereket. 
   -  A [Synapse Runtime](apache-spark-version-support.md) olyan kódtárakat tartalmaz, amelyek előre telepítve vannak minden kiszolgáló nélküli Apache Spark készleten. Az alap-futtatásban előre telepített csomagok nem távolíthatók el és nem távolíthatók el.
   -  A PySpark-, Python-, Scala-/Java-, .NET- vagy Spark-verziók megváltoztatása nem támogatott.
   -  A Python munkamenet-hatókörű kódtárak csak YML kiterjesztésű fájlokat fogadnak el.

## <a name="validate-wheel-files"></a>Kerekfájlok ellenőrzése
A kiszolgáló nélküli Synapse Apache Spark a Linux-disztribúción alapulnak. Amikor közvetlenül a PyPI-ről tölt le és telepít Wheel-fájlokat, mindenképpen azt a Linux-verziót válassza, amely a Spark-készletével azonos Python-verzión fut.

>[!IMPORTANT]
>Az egyéni csomagok munkamenetek között hozzáadhatóak vagy módosíthatók. A frissített csomag csak akkor látható, ha megvárja, amíg a készlet és a munkamenet újraindul.

## <a name="check-for-dependency-conflicts"></a>Függőségi ütközések ellenőrzése
 Általánosságban elmondható, hogy a Python-függőségek feloldási megoldása bonyolult lehet. A függőségi ütközések helyi hibakereséséhez létrehozhatja a saját virtuális környezetét a Synapse Runtime alapján, és ellenőrizheti a módosításokat.

A környezet újbóli létrehozása és a frissítések ellenőrzése:
 1. [Töltse le](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) a sablont a Synapse-futtatás helyi újbóli hozzuk létre. A sablon és a tényleges Synapse-környezet között apró különbségek lehetnek.
   
 2. Hozzon létre egy virtuális környezetet az [alábbi utasítások alapján.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment) Ezzel a környezettel elkülönített Python-telepítést hozhat létre a megadott kódtárak listájával. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. A ``pip install -r <provide your req.txt file>`` használatával frissítheti a virtuális környezetet a megadott csomagokkal. Ha a telepítés hibát ad vissza, akkor ütközés lehet a Synapse-alap-futtatásban előre telepített és a megadott követelményfájlban megadottak között. Ezeket a függőségi ütközéseket fel kell oldani ahhoz, hogy a frissített kódtárak a kiszolgáló nélküli Apache Spark le.

>[!IMPORTANT]
>A pip és a Conda együttes használata problémákat okozhat. A pip és a Conda kombinálása esetén ajánlott a következő ajánlott [eljárásokat követni:](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment).

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett kódtárak megtekintése: [Apache Spark támogatása](apache-spark-version-support.md)
