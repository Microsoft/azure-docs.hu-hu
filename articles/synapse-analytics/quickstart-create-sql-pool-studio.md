---
title: 'Rövid útmutató: dedikált SQL-készlet létrehozása a szinapszis Studio használatával'
description: Hozzon létre egy dedikált SQL-készletet a szinapszis Studio használatával az útmutató lépéseinek követésével.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3644891f12a6475ec9cfec51f572df4742481e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541844"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Rövid útmutató: dedikált SQL-készlet létrehozása a szinapszis Studio használatával

Az Azure szinapszis Analytics számos analitikai motort kínál az adatai betöltéséhez, átalakításához, modellezéséhez és elemzéséhez. A dedikált SQL-készlet A T-SQL-alapú számítási és tárolási funkciókat kínál. Miután létrehozott egy dedikált SQL-készletet a szinapszis munkaterületen, az adat betölthető, modellezhető, feldolgozható és leküldhető a gyorsabb elemzési elemzéshez.

Ez a rövid útmutató ismerteti, hogyan hozhat létre dedikált SQL-készletet egy szinapszis-munkaterületen a szinapszis Studio használatával.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Szinapszis-munkaterület](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigáljon a szinapszis munkaterületre

1. Navigáljon ahhoz a szinapszis-munkaterülethez, ahol a dedikált SQL-készlet létre lesz hozva. Ehhez írja be a szolgáltatás nevét (vagy az erőforrás nevét közvetlenül) a keresősávba.

    ![Azure Portal keresési sáv, amelyben szinapszis-munkaterületek vannak beírva.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A munkaterületek listájából írja be a megnyitni kívánt munkaterület nevét (vagy annak egy részét). Ebben a példában egy **contosoanalytics** nevű munkaterületet fogunk használni.

    ![A contoso nevet tartalmazó szinapszis-munkaterületek listázása.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>A Synapse Studio indítása

1. A munkaterület-Áttekintés lapon válassza ki a **munkaterület webes URL-címét** a szinapszis Studio elindításához.

    ![Azure Portal szinapszis-munkaterület áttekintése a munkaterület webes URL-címével kiemelve.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Dedikált SQL-készlet létrehozása a szinapszis Studióban

1. A szinapszis Studio kezdőlapján navigáljon a **felügyeleti hubhoz** a bal oldali navigációs sávon a **kezelés** ikonra kattintva.

    ![Szinapszis Studio kezdőlapja kiemelve a felügyeleti központ szakaszával.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. A felügyeleti központban navigáljon az **SQL-készletek** szakaszhoz, és tekintse meg a munkaterületen elérhető SQL-készletek aktuális listáját.

    ![Szinapszis Studio Management hub és SQL-készletek navigáció kiválasztva](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Válassza az **+ új** parancs lehetőséget, az új SQL-készlet létrehozása varázsló pedig megjelenik. 

    ![A szinapszis Studio Management hub SQL-készletek listázása.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Az **alapok** lapon adja meg a következő adatokat:

    | Beállítás | Ajánlott érték | Leírás |
    | :------ | :-------------- | :---------- |
    | **SQL-készlet neve** | contosoedw | Ez a név, amelyet a dedikált SQL-készlet fog tartalmazni. |
    | **Teljesítményszint** | DW100c | Állítsa be ezt a legkisebb méretre a rövid útmutató költségeinek csökkentése érdekében |

    ![SQL-készletek létrehozási folyamat – alapbeállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Vegye figyelembe, hogy a dedikált SQL-készletek által használható nevekre vonatkozó korlátozások érvényesek. A nevek nem tartalmazhatnak speciális karaktereket, csak 15 karakterből állhatnak, és nem tartalmazhatnak fenntartott szavakat, és egyedinek kell lenniük a munkaterületen.

4. A következő lapon a **További beállítások** területen válassza a **nincs** lehetőséget, ha az SQL-készletet szeretné kiépíteni az információk nélkül. Az alapértelmezett rendezést hagyja kiválasztva.

    Ha vissza szeretné állítani a dedikált SQL-készletet egy visszaállítási pontról, válassza a **visszaállítási pont** lehetőséget. További információ a visszaállítás végrehajtásáról [: útmutató: meglévő DEDIKÁLT SQL-készlet visszaállítása](backuprestore/restore-sql-pool.md)

    ![SQL-készlet folyamat létrehozása – további beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Most nem adunk hozzá címkét, ezért a következő elemnél válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. A **felülvizsgálat + létrehozás** lapon győződjön meg arról, hogy a korábban beírt adatok alapján a részletek helyesek, majd kattintson a **Létrehozás** gombra. 

    ![SQL-készlet létrehozása folyamat – felülvizsgálati beállítások lap.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Ekkor elindul az erőforrás-kiépítési folyamat.

1. A kiépítés befejezése után lépjen vissza a munkaterületre, majd egy új bejegyzést fog látni az újonnan létrehozott SQL-készlethez.

    ![SQL-készlet folyamat létrehozása – erőforrás-kiépítés.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. A dedikált SQL-készlet létrehozása után elérhető lesz a munkaterületen az adatok betöltéséhez, a streamek feldolgozásához, a tótól való olvasáshoz stb.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Dedikált SQL-készlet tisztítása a szinapszis Studio használatával    

Az alábbi lépések végrehajtásával törölje a dedikált SQL-készletet a munkaterületről a szinapszis Studio használatával.
> [!WARNING]
> Ha egy dedikált SQL-készletet töröl, a rendszer eltávolítja az elemzési motort a munkaterületről. Többé nem lesz lehetséges a készlethez való kapcsolódás, és a dedikált SQL-készletet használó összes lekérdezés, folyamat és szkript többé nem fog működni.

Ha törölni szeretné a dedikált SQL-készletet, tegye a következőket:

1. Navigáljon az SQL-készletekhez a szinapszis Studióban, a felügyeleti központban.
1. Válassza ki a törölni kívánt dedikált SQL-készlet három pontját (ebben az esetben **contosoedw**) a dedikált SQL-készlet parancsainak megjelenítéséhez:

    ![Az SQL-készletek listázása a közelmúltban létrehozott készlettel.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Nyomja le a **delete** billentyűt.
1. Erősítse meg a törlést, majd nyomja le a **delete (Törlés** ) gombot.
1. Ha a folyamat sikeresen befejeződik, a dedikált SQL-készlet többé nem jelenik meg a munkaterület erőforrásaiban.

## <a name="next-steps"></a>Következő lépések
 
- Tekintse meg a rövid útmutató [: Apache Spark jegyzetfüzet létrehozása](quickstart-apache-spark-notebook.md)című témakört.
- Lásd [: gyors útmutató: DEDIKÁLT SQL-készlet létrehozása a Azure Portal használatával](quickstart-create-sql-pool-portal.md).
