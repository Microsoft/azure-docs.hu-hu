---
title: 'Rövid útmutató: dedikált SQL-készlet létrehozása a Azure Portal használatával'
description: Az útmutató lépéseinek követésével hozzon létre egy új dedikált SQL-készletet a Azure Portal használatával.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3537b126adf082c8b00a02d999789d8a32b3f51d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462012"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-the-azure-portal"></a>Rövid útmutató: dedikált SQL-készlet létrehozása a Azure Portal használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez és elemzéséhez. A dedikált SQL-készlet A T-SQL-alapú számítási és tárolási funkciókat kínál. Miután létrehozott egy dedikált SQL-készletet a szinapszis munkaterületen, az adat betölthető, modellezhető, feldolgozható és leküldhető a gyorsabb elemzési elemzéshez.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy dedikált SQL-készletet egy szinapszis-munkaterületen a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis-munkaterület](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigáljon a szinapszis munkaterületre

1. Navigáljon ahhoz a szinapszis-munkaterülethez, ahol a dedikált SQL-készlet létre lesz hozva. Ehhez írja be a szolgáltatás nevét (vagy az erőforrás nevét közvetlenül) a keresősávba.
![Azure Portal keresési sáv, amely szinapszis-munkaterületeket írt ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) be. 
1. A munkaterületek listájából írja be a megnyitni kívánt munkaterület nevét (vagy annak egy részét). Ebben a példában egy **contosoanalytics** nevű munkaterületet fogunk használni.
![A contoso nevet tartalmazó szinapszis-munkaterületek listázása.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-dedicated-sql-pool"></a>Új dedikált SQL-készlet létrehozása

1. Azon a szinapszis-munkaterületen, ahol létre szeretné hozni a dedikált SQL-készletet, válassza a felső sávban az **új DEDIKÁLT SQL Pool** parancsot.
![A szinapszis munkaterület áttekintése, amely egy új dedikált SQL-készletet hoz létre a parancs körüli piros mezővel.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Az **alapok** lapon adja meg a következő adatokat:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **Dedikált SQL-készlet neve** | Bármely érvényes név | A dedikált SQL-készlet neve. |
    | **Teljesítményszint** | DW100c | Állítsa a legkisebb méretre a gyors üzembe helyezés költségeinek csökkentése érdekében |

  
    ![Dedikált SQL-készlet létrehozási folyamat – alapbeállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)

    > [!IMPORTANT]
    > Vegye figyelembe, hogy a dedikált SQL-készletek által használható nevekre vonatkozó korlátozások érvényesek. A nevek nem tartalmazhatnak speciális karaktereket, csak 15 karakterből állhatnak, és nem tartalmazhatnak fenntartott szavakat, és egyedinek kell lenniük a munkaterületen.

3. Válassza a **Tovább: További beállítások** lehetőséget.
4. Válassza a **nincs** lehetőséget a dedikált SQL-készlet adat nélküli kiépítéséhez. Hagyja bejelölve az alapértelmezett rendezést.

    Ha vissza szeretné állítani a dedikált SQL-készletet egy visszaállítási pontról, válassza a **visszaállítási pont** lehetőséget. További információ a visszaállítás végrehajtásáról [: útmutató: meglévő DEDIKÁLT SQL-készlet visszaállítása](backuprestore/restore-sql-pool.md)

![dedikált SQL-készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Válassza az **Áttekintés + létrehozás** lehetőséget.
6. Győződjön meg arról, hogy a korábban megadott adatok alapján a részletek helyesek. Válassza a **Létrehozás** lehetőséget.
![dedikált SQL-készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. Ekkor elindul az erőforrás-kiépítési folyamat.
 ![Képernyőkép, amely az "üzembe helyezés befejezése" lapot mutatja.](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. A kiépítés befejezése után lépjen vissza a munkaterületre, majd az újonnan létrehozott dedikált SQL-készlet új bejegyzését jeleníti meg.
 ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)


A dedikált SQL-készlet létrehozása után elérhető lesz a munkaterületen az adatok betöltéséhez, a streamek feldolgozásához, a tótól való olvasáshoz stb.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi lépések végrehajtásával törölje a dedikált SQL-készletet a munkaterületről.
> [!WARNING]
> Egy dedikált SQL-készlet törlésével a rendszer eltávolítja az elemzési motort és a törölt dedikált SQL-készlet adatbázisában tárolt adatok a munkaterületről. A továbbiakban nem lesz lehetséges a dedikált SQL-készlethez való kapcsolódás, és a dedikált SQL-készletbe beolvasott vagy írt összes lekérdezés, folyamat és jegyzetfüzet többé nem fog működni.

Ha törölni szeretné a dedikált SQL-készletet, hajtsa végre a következő lépéseket:

1. Navigáljon az SQL-készletek panelre a munkaterület panelen
1. Válassza ki a törölni kívánt dedikált SQL-készletet (ebben az esetben **contosowdw**)
1. A kijelölés után nyomja le a **delete** billentyűt.
1. Erősítse meg a törlést, **majd nyomja le a Delete (** törlés ![ ) gombra dedikált SQL-készlet – áttekintés – a törlés megerősítésének kijelölését.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. Ha a folyamat sikeresen befejeződik, a dedikált SQL-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

## <a name="next-steps"></a>Következő lépések

- Lásd [: gyors útmutató: kiszolgáló nélküli Apache Spark készlet létrehozása a szinapszis Studióban webes eszközök használatával](quickstart-apache-spark-notebook.md).
- Lásd [: gyors útmutató: kiszolgáló nélküli Apache Spark készlet létrehozása a Azure Portal használatával](quickstart-create-apache-spark-pool-portal.md).
