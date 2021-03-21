---
title: 'Gyors útmutató: kiszolgáló nélküli Apache Spark készlet létrehozása a Azure Portal használatával'
description: Az útmutató lépéseinek követésével hozzon létre egy kiszolgáló nélküli Apache Spark készletet az Azure Portal használatával.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fc7a7f137701590e4bddd791a3fdd2d1eaeca4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461611"
---
# <a name="quickstart-create-a-new-serverless-apache-spark-pool-using-the-azure-portal"></a>Gyors útmutató: új kiszolgáló nélküli Apache Spark készlet létrehozása a Azure Portal használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez, elemzéséhez és terjesztéséhez. A Apache Spark-készlet nyílt forráskódú big data számítási képességeket biztosít. Miután létrehozott egy Apache Spark-készletet a szinapszis munkaterületen, az adatai betölthetők, modellezhető, feldolgozhatók és terjeszthetők a gyorsabb elemzési elemzéshez.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Apache Spark készletet egy szinapszis-munkaterületen a Azure Portal használatával.

> [!IMPORTANT]
> A Spark-példányok számlázása percenként történik, függetlenül attól, hogy használja-e őket. A használata után állítsa le a Spark-példányt, vagy állítson be egy rövid időkorlátot. További információkért lásd a cikk **Az erőforrások eltávolítása** című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis Analytics-munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigáljon a szinapszis munkaterületre 
1. Navigáljon ahhoz a szinapszis-munkaterülethez, ahol a Apache Spark-készletet hozza létre, majd írja be a szolgáltatás nevét (vagy az erőforrás nevét közvetlenül) a keresősávba.
![Azure Portal keresési sáv, amelyben szinapszis-munkaterületek vannak beírva.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A munkaterületek listájából írja be a megnyitni kívánt munkaterület nevét (vagy annak egy részét). Ebben a példában egy **contosoanalytics** nevű munkaterületet fogunk használni.
![A contoso nevet tartalmazó szinapszis-munkaterületek listázása.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Új Apache Spark-készlet létrehozása

1. Válassza ki az **új Apache Spark készlet** elemet abban a szinapszis munkaterületen, ahol létre szeretné hozni a Apache Spark készletet.
    ![A szinapszis munkaterület áttekintése, amely egy új Apache Spark készlet létrehozásához szükséges vörös mezővel rendelkezik a parancs körül](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Az **alapok** lapon adja meg a következő adatokat:

    |Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Apache Spark készlet neve** | Egy érvényes készlet neve | A Apache Spark-készlet neve. |
    | **Csomópont mérete** | Kicsi (4 vCPU/32 GB) | Állítsa be ezt a legkisebb méretre a rövid útmutató költségeinek csökkentése érdekében |
    | **Automatikus méretezés** | Disabled (Letiltva) | Ehhez a rövid útmutatóhoz nem szükséges az autoscale méretezés |
    | **Csomópontok száma** | 5 | Kis méret használata a rövid útmutató költségeinek korlátozásához |


    ![Apache Spark készlet létrehozása folyamat – alapismeretek lap.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy a Apache Spark-készletek által használható nevekre vonatkozó korlátozások érvényesek. A név csak betűket vagy számokat tartalmazhat, legfeljebb 15 karakterből állhat, betűvel kell kezdődnie, nem tartalmazhat fenntartott szavakat, és egyedinek kell lennie a munkaterületen.

3. Válassza a **Tovább: további beállítások** elemet, és tekintse át az alapértelmezett beállításokat. Ne módosítsa az alapértelmezett beállításokat.
    ![Képernyőkép: a "További beállítások" lapon megjelenik a "Apache Spark készlet létrehozása" oldal.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Kattintson a **Tovább gombra: címkék**. Ne adjon hozzá címkéket.
    ![Apache Spark készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Győződjön meg arról, hogy a korábban megadott adatok alapján a részletek helyesek, majd válassza a **Létrehozás** lehetőséget.
    ![Apache Spark készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. Ezen a ponton az erőforrás-kiépítési folyamat elindul, ami azt jelzi, hogy a művelet befejeződött.
    ![Az "az üzembe helyezés befejeződött" üzenet jelenik meg az "áttekintés" oldalon.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. A kiépítés befejezése után lépjen vissza a munkaterületre, majd megjelenik egy új bejegyzés az újonnan létrehozott Apache Spark-készlethez.
    ![Apache Spark készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. Ezen a ponton nincsenek futó erőforrások, a Spark díjszabása nélkül létrehozott metaadatokat a létrehozni kívánt Spark-példányokkal kapcsolatban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépések végrehajtásával törölje a Apache Spark-készletet a munkaterületről.
> [!WARNING]
> Apache Spark-készlet törlése eltávolítja az elemzési motort a munkaterületről. Többé nem lesz lehetséges a készlethez való kapcsolódás, és a Apache Spark készletet használó összes lekérdezés, folyamat és jegyzetfüzet többé nem fog működni.

Ha törölni szeretné a Apache Spark-készletet, tegye a következőket:

1. Navigáljon a munkaterületen a Apache Spark készletek panelre.
2. Válassza ki a törölni kívánt Apache Spark-készletet (ebben az esetben a **contosospark**).
3. Nyomja le a **delete** billentyűt.
 ![Apache Spark készletek felsorolása a legutóbb létrehozott készlettel.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
 ![Megerősítő párbeszédpanel a kiválasztott Apache Spark-készlet törléséhez.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Ha a folyamat sikeresen befejeződik, a Apache Spark-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a rövid útmutató [: Apache Spark jegyzetfüzet létrehozása](quickstart-apache-spark-notebook.md)című témakört.
- Lásd [: gyors útmutató: DEDIKÁLT SQL-készlet létrehozása a Azure Portal használatával](quickstart-create-sql-pool-portal.md).
