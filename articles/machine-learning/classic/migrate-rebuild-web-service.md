---
title: 'ML Studio (klasszikus): áttelepítés Azure Machine Learning – újjáépített webszolgáltatás'
description: A Studio (klasszikus) webszolgáltatások újraépítése folyamat-végpontként Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565250"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Studio (klasszikus) webszolgáltatás újraépítése Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan hozhat létre a Studio (klasszikus) webszolgáltatást **végpontként** a Azure Machine learning-ben.

Azure Machine Learning folyamat-végpontok használatával előrejelzéseket készíthet, Újrataníthatja a modelleket, vagy futtathat általános folyamatokat. A REST-végpont lehetővé teszi, hogy bármilyen platformról futtasson folyamatokat. 

Ez a cikk a Studio (klasszikus) Azure Machine Learning áttelepítési sorozat részét képezi. A Azure Machine Learningre való áttelepítéssel kapcsolatos további információkért tekintse meg az [áttelepítési áttekintést ismertető cikket](migrate-overview.md).

> [!NOTE]
> Ez az áttelepítési sorozat a fogd és vidd designerre fókuszál. A modellek programozott üzembe helyezésével kapcsolatos további információkért lásd: [gépi tanulási modellek üzembe helyezése az Azure-ban](../how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy Azure Machine Learning-munkaterület. [Hozzon létre egy Azure Machine learning munkaterületet](../how-to-manage-workspace.md#create-a-workspace).
- Egy Azure Machine Learning betanítási folyamat. További információ: [Studio (klasszikus) kísérlet újraépítése Azure Machine Learningban](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Valós idejű végpont vs folyamat végpontja

A Studio (klasszikus) webszolgáltatásokat a Azure Machine Learning **végpontok** váltották fel. A következő táblázat segítségével kiválaszthatja a használandó végpont típusát:

|Studio (klasszikus) webszolgáltatás| Azure Machine Learning cseréje
|---|---|
|Webszolgáltatások kérése/válasza (valós idejű előrejelzés)|Valós idejű végpont|
|Batch-webszolgáltatás (kötegelt előrejelzés)|Folyamat végpontja|
|Webszolgáltatás átképzése (átképzés)|Folyamat végpontja| 


## <a name="deploy-a-real-time-endpoint"></a>Valós idejű végpont üzembe helyezése

A Studio (klasszikus) esetében a **kérelem/válasz webszolgáltatás** használatával helyezheti üzembe a modellt a valós idejű előrejelzésekhez. A Azure Machine Learning **valós idejű végpontot** használ.

A modellek több módon is üzembe helyezhetők Azure Machine Learningban. Az egyik legegyszerűbb módszer a tervező használata az üzembe helyezési folyamat automatizálására. A modell valós idejű végpontként történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Futtassa legalább egyszer a befejezett betanítási folyamatát.
1. A Futtatás befejezése után a vászon tetején válassza a következtetési **folyamat**  >  **valós idejű következtetésének** létrehozása lehetőséget.

    ![valós idejű következtetési folyamat létrehozása](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    A tervező egy valós idejű következtetési folyamatba alakítja át a betanítási folyamatot. Hasonló konverzió is a Studióban (klasszikus) történik.

    A tervezőben a konverziós lépés a [betanított modellt is regisztrálja a Azure Machine learning munkaterületen](../how-to-deploy-and-where.md#registermodel).

1. Válassza a **Submit (Küldés** ) lehetőséget a valós idejű következtetési folyamat futtatásához, és ellenőrizze, hogy sikeresen fut-e.

1. A következtetési folyamat ellenőrzése után válassza a **telepítés** lehetőséget.

1. Adja meg a végpont nevét és a számítási típust.

    A következő táblázat ismerteti az üzembe helyezés számítási lehetőségeit a tervezőben:

    | Számítási cél | Alkalmazási cél | Leírás | Létrehozás |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Valós idejű következtetés|Nagyméretű, éles üzemben üzemelő példányok. Gyors válaszidő és a szolgáltatás automatikus skálázása.| Felhasználó által létrehozott. További információt a [számítási célok létrehozása](../how-to-create-attach-compute-studio.md#inference-clusters)című témakörben talál. |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|Tesztelés vagy fejlesztés | Kisméretű, CPU-alapú számítási feladatok, amelyek kevesebb mint 48 GB RAM memóriát igényelnek.| A Azure Machine Learning automatikusan hozza létre.

### <a name="test-the-real-time-endpoint"></a>A valós idejű végpont tesztelése

Az üzembe helyezés befejezése után további részleteket és a végpont tesztelését végezheti el:

1. Lépjen a **végpontok** lapra.
1. Válassza ki a végpontot.
1. Kattintson a **Teszt** fülre.
    
    ![Képernyőfelvétel a végpontok lap és a tesztelési végpont gombjának megjelenítéséről](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Folyamat végpontjának közzététele a kötegelt előrejelzéshez vagy az átképzéshez

A betanítási folyamat használatával a **folyamat végpontját** nem valós idejű végpont helyett is létrehozhatja. A **folyamat-végpontok** használatával végezheti el a kötegelt előrejelzést vagy az átképzést.

A folyamat végpontjai a Studio (klasszikus) **Batch-végrehajtási végpontokat**  és a **webszolgáltatások átképzését** cserélik le.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Folyamat végpontjának közzététele a kötegelt előrejelzéshez

A Batch-előrejelzési végpontok közzététele hasonló a valós idejű végponthoz.

A következő lépések végrehajtásával tehet közzé egy folyamat-végpontot a Batch-előrejelzéshez:

1. Futtassa legalább egyszer a befejezett betanítási folyamatát.

1. A Futtatás befejezése után a vászon tetején válassza a **következtetési folyamat létrehozása** elemre vonatkozó  >  **következtetési folyamat** elemet.

    ![Képernyőfelvétel a betanítási folyamaton a következtetési folyamat létrehozása gombról](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    A tervező átalakítja a betanítási folyamatot egy batch következtetési folyamatba. Hasonló konverzió is a Studióban (klasszikus) történik.

    A tervezőben ez a lépés a [betanított modellt is regisztrálja a Azure Machine learning munkaterületen](../how-to-deploy-and-where.md#registermodel).

1. Válassza a **Submit (Küldés** ) lehetőséget a Batch-következtetési folyamat futtatásához, és ellenőrizze, hogy sikeresen befejeződött-e.

1. A következtetési folyamat ellenőrzése után válassza a **Közzététel** lehetőséget.
 
1. Hozzon létre egy új folyamat-végpontot, vagy válasszon ki egy meglévőt.
    
    Az új folyamat végpontja létrehoz egy új REST-végpontot a folyamathoz. 

    Ha meglévő folyamat-végpontot választ, nem írja felül a meglévő folyamatot. Ehelyett Azure Machine Learning a végpont összes folyamatának verzióját. Megadhatja, hogy melyik verziót szeretné futtatni a REST-hívásban. Az alapértelmezett folyamatot is be kell állítania, ha a REST-hívás nem ad meg verziót.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Folyamat-végpont közzététele az átképzéshez

Egy folyamat-végpont átképzéshez való közzétételéhez már van olyan folyamat-piszkozata, amely egy modellt vezet be. A képzési folyamatok létrehozásával kapcsolatos további információkért lásd: [Studio (klasszikus) kísérlet](migrate-rebuild-experiment.md)újjáépítése.

Ha újra szeretné használni a folyamat-végpontot a betanításhoz, létre kell hoznia egy **folyamat paramétert** a bemeneti adatkészlethez. Így dinamikusan állíthatja be a betanítási adatkészletet, így a modell újratanítása is lehetővé válik.

A következő lépések végrehajtásával teheti közzé az átképzési folyamat végpontját:

1. Legalább egyszer futtassa a betanítási folyamatát.
1. A Futtatás befejezése után válassza ki az adatkészlet modult.
1. A modul részletei ablaktáblán válassza a **beállítás folyamatként paramétert**.
1. Adjon meg egy leíró nevet, például "InputDataset".    

    ![Képernyőfelvétel a folyamat paramétereinek létrehozásáról](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Ezzel létrehoz egy folyamat paramétert a bemeneti adatkészlethez. Amikor betanítja a folyamat végpontját a betanításhoz, megadhat egy új adatkészletet a modell újratanításához.

1. Válassza a **Közzététel** lehetőséget.

    ![Képernyőkép a közzétételi gomb kiemeléséről egy képzési folyamatban](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>A folyamat végpontjának meghívása a studióból

Miután létrehozta a Batch-következtetést vagy az átképzési folyamat végpontját, közvetlenül a böngészőből hívhatja meg a végpontot.

1. Lépjen a **folyamatok** lapra, és válassza a **folyamat-végpontok** lehetőséget.
1. Válassza ki a futtatni kívánt folyamat-végpontot.
1. Válassza a **Küldés** lehetőséget.

    A **Küldés** lehetőség kiválasztását követően bármelyik folyamat paraméterét megadhatja.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan lehet újraépíteni a Studio (klasszikus) webszolgáltatást Azure Machine Learning. A következő lépés a [webszolgáltatás integrálása az ügyfélalkalmazások](migrate-rebuild-integrate-with-client-app.md)használatával.


Tekintse meg a Studio (klasszikus) áttelepítési sorozat további cikkeit:

1. [Áttelepítési áttekintés](migrate-overview.md).
1. [Adatkészlet Átmigrálása](migrate-register-dataset.md).
1. [Építse újra a Studio (klasszikus) betanítási folyamatát](migrate-rebuild-experiment.md).
1. **Építse újra a Studio (klasszikus) webszolgáltatást**.
1. [Azure Machine learning webszolgáltatások integrálása az ügyfélalkalmazások alkalmazásával](migrate-rebuild-integrate-with-client-app.md).
1. [Telepítse át az R-szkriptet](migrate-execute-r-script.md).
