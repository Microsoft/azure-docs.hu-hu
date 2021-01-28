---
title: 'Oktatóanyag: modell betanítása automatizált gépi tanulás használatával'
description: Útmutató a gépi tanulási modellek kód nélküli betanításához az Azure szinapszis Analyticsben.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943524"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Oktatóanyag: gépi tanulási modell betanítása kód nélkül

A Spark-táblázatokban lévő adatait az [automatikus gépi tanulás](../../machine-learning/concept-automated-ml.md)használatával betanított új gépi tanulási modellekkel bővítheti. Az Azure szinapszis Analyticsben kiválaszthat egy Spark-táblázatot a munkaterületen, amely képzési adatkészletként használható a gépi tanulási modellek létrehozásához, és ezt kód nélküli felhasználói élményben teheti meg.

Ebből az oktatóanyagból megtudhatja, hogyan taníthatja be a gépi tanulási modelleket a szinapszis Studióban elérhető kód nélküli felhasználói felület használatával. A szinapszis Studio az Azure szinapszis Analytics egyik funkciója. 

A felhasználói felület manuális kódolása helyett a Azure Machine Learning automatikus gépi tanulást fogja használni. A betanított modell típusa a megoldani kívánt problémától függ.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md). Győződjön meg arról, hogy rendelkezik az alapértelmezett tárolóként konfigurált Azure Data Lake Storage Gen2 Storage-fiókkal. A Data Lake Storage Gen2 használt fájlrendszernél győződjön meg róla, hogy Ön a *Storage blob adatközreműködői*.
- Egy Apache Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: gyors útmutató [: DEDIKÁLT SQL-készlet létrehozása a szinapszis Studio használatával](../quickstart-create-sql-pool-studio.md).
- Egy Azure Machine Learning társított szolgáltatás az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: gyors útmutató [: új Azure Machine learning társított szolgáltatás létrehozása az Azure szinapszis Analyticsben](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-spark-table-for-the-training-dataset"></a>Spark-táblázat létrehozása a betanítási adatkészlethez

Ebben az oktatóanyagban egy Spark-táblára van szükség. A következő jegyzetfüzet létrehoz egyet:

1. Töltse le a notebook [create-Spark-Table-NYCTaxi-adat. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importálja a jegyzetfüzetet a szinapszis studióba.
![Képernyőkép az Azure szinapszis Analyticsről, az Importálás lehetőség kiemelve.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Válassza ki a használni kívánt Spark-készletet, majd válassza az **összes futtatása** lehetőséget. Ez a lépés beolvassa a New York-i taxi adatait a megnyitott adatkészletből, és az adatokat az alapértelmezett Spark-adatbázisba menti.
![Képernyőfelvétel az Azure szinapszis Analyticsről, az összes és a Spark-adatbázis kiemelésével.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. A jegyzetfüzet futtatásának befejeződése után egy új Spark-tábla jelenik meg az alapértelmezett Spark-adatbázisban. Az **adatok** területen keresse meg **nyc_taxi** nevű táblázatot.
![Képernyőkép az Azure szinapszis Analytics-adatlapról, az új táblázat kiemelve.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Az automatikus gépi tanulás varázsló megnyitása

A varázsló megnyitása:

1. Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblára. Ezután válassza   >  **a Machine learning az új modellel gazdagítva** lehetőséget.
![Képernyőfelvétel a Spark-táblázatról, Machine Learning és gazdagítva az új modell kiemelésével.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Adja meg a Azure Machine Learning-ben futtatott automatizált gépi tanulási kísérlet létrehozásához szükséges konfigurációs adatokat. Ez több modellt is futtat. A sikeres futtatásból származó legjobb modell regisztrálva van a Azure Machine Learning modell beállításjegyzékében.

   ![Képernyőkép a gépi tanulási modellek betanításához szükséges konfigurációs specifikációról.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine learning munkaterület**: az automatikus gépi tanulási kísérlet futtatásához egy Azure Machine learning munkaterület szükséges. Az Azure szinapszis Analytics-munkaterületet társított [szolgáltatás](quickstart-integrate-azure-machine-learning.md)használatával is össze kell kapcsolni az Azure Machine learning munkaterülettel. Miután teljesítette az összes előfeltételt, megadhatja az automatikus futtatáshoz használni kívánt Azure Machine Learning munkaterületet.

    - **Kísérlet neve**: adja meg a kísérlet nevét. Ha automatikus gépi tanulást végez, a kísérlet nevét adja meg. A futtatással kapcsolatos információkat a rendszer a Azure Machine Learning munkaterületen található kísérlet alatt tárolja. Ez a felület alapértelmezés szerint létrehoz egy új kísérletet, és létrehoz egy javasolt nevet, de megadhatja egy meglévő kísérlet nevét is.

    - **Legjobb modell neve**: adja meg az automatikus futtatásból származó legjobb modell nevét. A legjobb modell ezt a nevet adja meg, és a Futtatás után automatikusan a Azure Machine Learning modell beállításjegyzékében lesz mentve. A gépi tanulás automatizált futtatása számos gépi tanulási modellt hoz létre. Egy későbbi lépésben kiválasztott elsődleges metrika alapján ezek a modellek összehasonlíthatók, a legjobb modell pedig kiválasztható.

    - **Cél oszlop**: ez az a modell, amelyet előre kell tanítani. Válassza ki az előre jelezni kívánt oszlopot. (Ebben az oktatóanyagban a numerikus oszlopot válassza `fareAmount` a cél oszlopként.)

    - **Spark-készlet**: adja meg az automatikus kísérlet futtatásához használni kívánt Spark-készletet. A számítások a megadott készleten futnak.

    - **Spark-konfiguráció részletei**: a Spark-készleten kívül lehetősége van a munkamenet-konfiguráció részleteinek megadására.

1. Válassza a **Folytatás** lehetőséget.

## <a name="choose-a-task-type"></a>Válasszon feladattípust

Válassza ki a gépi tanulási modell típusát a kísérlethez a válaszolni kívánt kérdés alapján. Mivel `fareAmount` a cél oszlop, és numerikus érték, javasoljuk, hogy itt válassza a **regresszió** lehetőséget. Ezután válassza a **Folytatás** elemet.

![Képernyőkép az új modellel való bővítésről, regressziós kiemelve.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>További konfigurációk

Ha az előző szakaszban a modell típusaként a **regresszió** vagy a **besorolás** lehetőséget választotta, a következő konfigurációk érhetők el:

- **Elsődleges metrika**: adja meg azt a mérőszámot, amely a modell működésének mértékét méri. Ezzel a metrikával összehasonlíthatja az automatizált futtatásban létrehozott különböző modelleket, és meghatározhatja, hogy melyik modell legyen a legjobban elvégezve.

- **Betanítási idő (óra)**: adja meg, hogy legfeljebb hány óra alatt fusson a kísérlet, és hogyan tanítsa be a modelleket. Vegye figyelembe, hogy 1 értéknél kisebb értékeket is megadhat (például **0,5**).

- **Egyidejű ismétlések** maximális száma: válassza ki a párhuzamosan futó ismétlések maximális számát.

- **ONNX-modell kompatibilitása**: Ha engedélyezi ezt a beállítást, az automatizált gépi tanulás által betanított modellek a ONNX formátumba lesznek konvertálva. Ez különösen akkor fontos, ha a modellt szeretné használni az Azure szinapszis Analytics SQL-készletekben való pontozáshoz.

Ezek a beállítások mindegyike alapértelmezett értékkel rendelkezik, amelyet testreszabhat.
![Képernyőkép a regressziós modell konfigurálásához szükséges további konfigurációkról.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Miután az összes szükséges konfigurációt elvégezte, elkezdheti az automatikus futtatást. Válassza a **Futtatás létrehozása** lehetőséget, amely a futtatást közvetlenül, kód nélkül indítja el. Ha inkább a Code-ot részesíti előnyben, **a Megnyitás jegyzetfüzetben** lehetőség kiválasztásával. Ez a beállítás lehetővé teszi, hogy megtekintse a futtatást létrehozó kódot, majd futtassa a jegyzetfüzetet.

>[!NOTE]
>Ha a **Time Series-előrejelzést** az előző szakaszban szereplő modell típusaként választotta, további konfigurációkat kell megadnia. Az előrejelzés szintén nem támogatja a ONNX-modell kompatibilitását.

### <a name="create-a-run-directly"></a>Közvetlen Futtatás létrehozása

Az automatikus gépi tanulás közvetlen futtatásának megkezdéséhez válassza a **Futtatás indítása** lehetőséget. Ekkor megjelenik egy értesítés, amely jelzi, hogy a Futtatás elindul. Ezután megjelenik egy másik, a sikerességet jelző értesítés. A Azure Machine Learning állapotának ellenőrzéséhez kattintson az értesítésben található hivatkozásra.
![Képernyőkép a sikeres értesítésről.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Futtatás jegyzetfüzettel

Jegyzetfüzet létrehozásához válassza **a Megnyitás jegyzetfüzetben** lehetőséget. Ezután válassza **az összes futtatása** lehetőséget. Ez lehetőséget nyújt a beállítások automatikus gépi tanulási futtatáshoz való hozzáadására is.

![Képernyőkép egy jegyzetfüzetről, az összes Kiemelt futtatásával.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Miután sikeresen elküldte a futtatást, megjelenik egy hivatkozás a kísérlet futtatásához a Azure Machine Learning munkaterületen a jegyzetfüzet kimenetében. Válassza ki a hivatkozást a Azure Machine Learning automatikus futtatásának figyeléséhez.
![Képernyőkép az Azure szinapszis Analyticsről, amely egy hivatkozással van kiemelve. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: gépi tanulási modell pontozása varázsló (előzetes verzió) dedikált SQL-készletekhez](tutorial-sql-pool-model-scoring-wizard.md)
- [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása az Azure szinapszis Analyticsben](quickstart-integrate-azure-machine-learning.md)
- [Gépi tanulási képességek az Azure szinapszis Analyticsben](what-is-machine-learning.md)