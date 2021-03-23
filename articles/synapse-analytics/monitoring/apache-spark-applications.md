---
title: Apache Spark alkalmazások figyelése a szinapszis Studio használatával
description: A szinapszis Studio használatával figyelheti Apache Spark alkalmazásait.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ea08c82ed5772be7e3a6094f5477c4450975c1fa
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775868"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>A szinapszis Studio használata a Apache Spark-alkalmazások figyelésére

Az Azure szinapszis Analytics segítségével a munkaterületen lévő Apache Spark-készleteken notebookok, feladatok és más típusú alkalmazások futtatására Apache Spark használható.

Ez a cikk ismerteti, hogyan figyelheti meg Apache Spark-alkalmazásait, így nyomon követheti a legújabb állapotot, problémákat és előrehaladást.

Ez az oktatóanyag a következő feladatokat mutatja be:

* Futó Apache Spark-alkalmazás figyelése
* Befejezett Apache Spark alkalmazás megtekintése
* Megszakított Apache Spark alkalmazás megtekintése
* A hibakeresés nem sikerült Apache Spark alkalmazás

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilát az oktatóanyagnak, ellenőrizze, hogy megfelel-e a következő feltételeknek:

- Egy szinapszis Studio-munkaterület. Útmutatásért lásd: [a szinapszis Studio-munkaterület létrehozása](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Egy Apache Spark készlet.

## <a name="view-apache-spark-applications"></a>Apache Spark alkalmazások megtekintése 
Megtekintheti az összes Apache Spark alkalmazást a **figyelő**  ->  **Apache Spark alkalmazásokban**.
   ![Apache Spark-alkalmazások](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Befejezett Apache Spark alkalmazás megtekintése

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások** elemet. A befejezett Apache Spark alkalmazások részleteinek megtekintéséhez válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket.

  ![Befejezett feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Keresse meg a **Befejezett feladatokat**, az **állapotot** és az **összes időtartamot**.

2. A napló lekérdezésének frissítése.

3. Nyissa meg Apache Spark History Server hivatkozást a **Spark History Server** elemre kattintva.

4. Tekintse át az **összegző** információkat.

5. Keresse meg a **naplókat**. A legördülő listából különböző típusú naplókat választhat, és a **naplók letöltése** lehetőségre kattintva letöltheti a napló adatait, és a hibák **és figyelmeztetések szűrése** jelölőnégyzet bejelölésével szűrheti a szükséges hibákat és figyelmeztetéseket.

6. A feladatokról a generált feladatok diagramján tekinthet meg áttekintést. Alapértelmezés szerint a Graph megjeleníti az összes feladatot. Ezt a nézetet **feladattípus** alapján szűrheti.

7. Alapértelmezés szerint a **folyamatjelző** megjelenítés van kiválasztva. Az adatfolyamatot úgy tekintheti meg, ha kiválasztja a **folyamatjelző** / **olvasása írási** /  / **időtartamot** a **megjelenítési** legördülő listában.

8. A feladatok lejátszásához kattintson a **Lejátszás** gombra. A **Leállítás gombra kattintva bármikor leállíthatja** a szolgáltatást.

9. Az egér görgetősávjának vagy görgetősávjának használatával nagyíthatja és kicsinyítheti a feladatok gráfját, és a **nagyításhoz** is kiválaszthatja, hogy illeszkedjen a képernyőhöz.

10. A feladatütemezés csomópont a következő információkat jeleníti meg az egyes szakaszok esetében:

    * ID.

    * Név vagy leírás.

    * A feladat teljes száma.

    * Olvasott adatok: a bemeneti méret és a shuffle olvasási méret összege.

    * Adatírás: a kimeneti méret és a shuffle írási méret összege.

    * Végrehajtási idő: az első kísérlet kezdési időpontja és a legutóbbi kísérlet befejezési időpontja közötti idő.

    * Sorok száma: a bemeneti rekordok, a kimeneti rekordok, a shuffle olvasási rekordok és a shuffle írási rekordok összege.

    * Haladás.

     ![Befejezett feladatok megtekintése](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Kattintson a **részletek megtekintése** elemre a gráfban, a szakasz részletei megjelennek.

    ![a fázis részletei](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Futó Apache Spark-alkalmazás figyelése

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások** elemet. A Apache Spark futó alkalmazások részleteinek megtekintéséhez válassza a beküldés Apache Spark alkalmazást, és tekintse meg a részleteket. Ha a Apache Spark alkalmazás még fut, nyomon követheti a folyamatot.

   ![futó feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Keresse meg a **Befejezett feladatokat**, az **állapotot** és az **összes időtartamot**.

2. A Apache Spark alkalmazás **megszakítása** .

3. **Frissítés** Napló lekérdezése

4. Kattintson a **Spark felhasználói felület** gombra a Spark-feladatokhoz való ugráshoz.

5. Tekintse meg a diagramot. A feladatokról a generált feladatok diagramján tekinthet meg áttekintést. Tekintse meg a 6., 7., 8., 9., 10. lépés [Apache Spark alkalmazást](#view-completed-apache-spark-application).

6. Tekintse át az **összegző** információkat.

7. Keresse **meg a diagnosztikát a diagnosztika** lapon.

8. Keresse meg a **naplókat** ezen a lapon. A legördülő listából különböző típusú naplókat választhat, és a **naplók letöltése** lehetőségre kattintva letöltheti a napló adatait, és a hibák **és figyelmeztetések szűrése** jelölőnégyzet bejelölésével szűrheti a szükséges hibákat és figyelmeztetéseket.

    ![futó feladatok megtekintése](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Megszakított Apache Spark alkalmazás megtekintése

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások** elemet. A megszakított Apache Spark alkalmazások részleteinek megtekintéséhez válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket.

 ![megszakított feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Keresse meg a **Befejezett feladatokat**, az **állapotot** és az **összes időtartamot**.

2. Frissítse a napló lekérdezését.

3. Nyissa meg az Apache History Server hivatkozást a **Spark History Server** elemre kattintva.

4. Tekintse meg a diagramot. A feladatokról a generált feladatok diagramján tekinthet meg áttekintést. Tekintse meg a 6., 7., 8., 9., 10. lépés [Apache Spark alkalmazást](#view-completed-apache-spark-application).

5. Tekintse át az **összegző** információkat.

6. Keresse meg a **naplókat**. A legördülő listából különböző típusú naplókat választhat, és a naplók **letöltése** lehetőségre kattintva letöltheti a napló adatait, és a hibák **és figyelmeztetések szűrése** jelölőnégyzet bejelölésével szűrheti a szükséges hibákat és figyelmeztetéseket.

7. Kattintson a **részletek megtekintése** elemre a gráfban, a szakasz részletei megjelennek.

   ![megszakított feladatok megtekintése](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>A hibakeresés nem sikerült Apache Spark alkalmazás

Nyissa meg a **monitort**, majd válassza **Apache Spark alkalmazások** elemet. A sikertelen Apache Spark alkalmazások részleteinek megtekintéséhez válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket.

![Sikertelen feladatok kiválasztása](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Keresse meg a **Befejezett feladatokat**, az **állapotot** és az **összes időtartamot**.

2. A napló lekérdezésének frissítése.

3. Nyissa meg Apache Spark History Server hivatkozást a **Spark History Server** elemre kattintva.

4. Tekintse meg a diagramot. A feladatokról a generált feladatok diagramján tekinthet meg áttekintést. Tekintse meg a 6., 7., 8., 9., 10. lépést [Apache Spark alkalmazás befejezése](#view-completed-apache-spark-application)

5. Tekintse át az **összegző** információkat.

6. Keresse meg a hiba adatait.

   ![Sikertelen feladatok adatai](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark alkalmazások összehasonlítása

Az alkalmazások összehasonlítására kétféleképpen van lehetőség. Az összehasonlításhoz válasszon egy **alkalmazást**, vagy kattintson az **összehasonlítás jegyzetfüzetbe** gombra a jegyzetfüzetben való megjelenítéshez.

### <a name="compare-by-choose-an-application"></a>Összehasonlítás egy alkalmazás kiválasztásával

Kattintson az **alkalmazások összehasonlítása** gombra, és válasszon egy alkalmazást a teljesítmény összehasonlításához, intuitív módon megtekintheti a két alkalmazás közötti különbséget.

![alkalmazások összehasonlítása](./media/how-to-monitor-spark-applications/compare-applications.png)

![az alkalmazások összehasonlításának részletei](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. Az egér használatával vigye az egérmutatót egy alkalmazásra, majd jelenítse meg az **alkalmazások összehasonlítása** ikont.

2. Kattintson az **alkalmazások összehasonlítása** ikonra, és megjelenik az alkalmazások összehasonlítása oldal.

3. Kattintson az **alkalmazás kiválasztása** gombra az **összehasonlító alkalmazás kiválasztása** lap megnyitásához.

4. Az összehasonlító alkalmazás kiválasztásakor be kell írnia az alkalmazás URL-címét, vagy az ismétlődő listából kell választania. Ezután kattintson **az OK** gombra. 

   ![összehasonlító alkalmazás kiválasztása](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Az összehasonlítási eredmény az alkalmazások összehasonlítása oldalon jelenik meg.

   ![összehasonlítás eredménye](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Összehasonlítás a jegyzetfüzetben való összehasonlítással

Kattintson az **összehasonlítás a jegyzetfüzetbe** gombra az **alkalmazások összehasonlítása** lapon a jegyzetfüzet megnyitásához. A. ipynb fájl alapértelmezett neve az alkalmazás- **elemzések ismétlődő** változata.

![összehasonlítás jegyzetfüzetben](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

A jegyzetfüzetben: ismétlődő alkalmazás-elemzési fájl, közvetlenül a Spark-készlet és a nyelv beállítása után is futtatható.

![ismétlődő alkalmazás-elemzés](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studio használatával](how-to-monitor-pipeline-runs.md) című cikket.
