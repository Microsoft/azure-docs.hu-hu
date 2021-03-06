---
title: Távoli modellek központi telepítésének hibaelhárítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként lehet megkerülni, megoldani és elhárítani néhány gyakori Docker-telepítési hibát az Azure Kubernetes Service és a Azure Container Instances használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperf-fy20q4, devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 1a7116f0edbed8270a3345bc924bf50872615b04
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217159"
---
# <a name="troubleshooting-remote-model-deployment"></a>Távoli modellek központi telepítésének hibaelhárítása 

Ismerje meg, hogy miként lehet elhárítani és megoldani a modelleket a Azure Container Instances (ACI) és az Azure Kubernetes Service (ak) szolgáltatással való üzembe helyezése során felmerülő gyakori hibák elhárításához és megoldásához Azure Machine Learning használatával.

> [!NOTE]
> Ha modellt helyez üzembe az Azure Kubernetes Service (ak) szolgáltatásban, javasoljuk, hogy engedélyezze [Azure monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) a fürt számára. Ez segítséget nyújt a fürt állapotának és erőforrás-használatának általános megismerésében. A következő erőforrások is hasznosak lehetnek:
>
> * [Az AK-fürtöt befolyásoló Resource Health események keresése](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service Diagnostics](../aks/concepts-diagnostics.md)
>
> Ha egy modellt nem kifogástalan állapotú vagy túlterhelt fürtre próbál telepíteni, a rendszer problémát tapasztal. Ha segítségre van szüksége az AK-fürtökkel kapcsolatos problémák megoldásához, forduljon az AK-támogatáshoz.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Az [Azure CLI](/cli/azure/install-azure-cli)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>A Machine learning-modellek Docker-telepítésének lépései

Ha Azure Machine Learningon nem helyi számítási feladatokra helyezi üzembe a modellt, a következő dolgok történnek:

1. A InferenceConfig lévő környezetek objektumban megadott Docker a rendszer elküldi a felhőbe, a forrás könyvtára tartalmával együtt.
1. Ha egy korábban létrehozott rendszerkép nem érhető el a tároló beállításjegyzékében, egy új Docker-rendszerkép jön a felhőben, és a munkaterület alapértelmezett tárolójának beállításjegyzékében tárolódik.
1. A rendszer letölti a Docker-rendszerképet a tároló-beállításjegyzékből a számítási célra.
1. A munkaterület alapértelmezett blob-tárolója csatlakoztatva van a számítási célhoz, így hozzáférést biztosít a regisztrált modellekhez
1. A webkiszolgáló inicializálása a bejegyzési parancsfájl függvényének futtatásával történik `init()`
1. Ha a telepített modell megkapja a kérelmet, a `run()` függvény kezeli ezt a kérelmet.

Helyi központi telepítés használata esetén a fő különbség az, hogy a tároló rendszerképe a helyi gépre épül, ezért a helyi telepítéshez szükséges Docker-nek kell lennie.

Ezeknek a magas szintű lépéseknek a megértése segít megérteni, hogy hol történnek a hibák.

## <a name="get-deployment-logs"></a>Üzembe helyezési naplók beolvasása

A hibák hibakeresésének első lépése a telepítési naplók beszerzése. Először kövesse az [itt található utasításokat](how-to-deploy-and-where.md#connect-to-your-workspace) a munkaterülethez való kapcsolódáshoz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Az üzembe helyezett webszolgáltatásból származó naplók beszerzéséhez tegye a következőket:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Feltételezve, hogy van egy nevű objektuma `azureml.core.Workspace` `ws` , a következőket teheti:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Helyi hibakeresés

Ha a modell ACI-vagy AK-beli üzembe helyezése során problémák merülnek fel, telepítse helyi webszolgáltatásként. Helyi webszolgáltatás használatával könnyebben háríthatók el a problémák. Ha helyileg szeretné elhárítani a központi telepítést, tekintse meg a [helyi hibaelhárítást ismertető cikket](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>A tároló nem ütemezhető

Amikor szolgáltatást helyez üzembe az Azure Kubernetes Service egyik számítási céljára, az Azure Machine Learning megkísérli a kért mennyiségű erőforrással ütemezni a szolgáltatást. Ha a fürtben nincsenek elérhető csomópontok a megfelelő mennyiségű erőforrás 5 perc után, akkor a telepítés sikertelen lesz. A hibaüzenet a következő: `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Ezt a hibát a további csomópontok hozzáadásával, a csomópontok SKU-jának módosításával vagy a szolgáltatás erőforrás-követelményeinek módosításával kezelheti. 

A hibaüzenet általában azt jelzi, hogy melyik erőforrásra van szüksége – például ha megjelenik egy hibaüzenet, amely azt jelzi, hogy `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` a szolgáltatás a GPU-t igényli, és a fürtben három olyan csomópont van, amely nem rendelkezik elérhető GPU-val. Ez több csomópont hozzáadásával oldható meg, ha GPU SKU-t használ, váltson GPU-t támogató SKU-ra, ha nem a GPU-k használatára van szüksége, és nem módosítja a környezetet.  

## <a name="service-launch-fails"></a>A szolgáltatás indítása sikertelen

A rendszerkép sikeres felépítése után a rendszer megkísérli a tároló indítását a telepítési konfiguráció alapján. A tároló indítási folyamatának részeként a `init()` rendszer meghívja a pontozási parancsfájl függvényét. Ha a függvény nem észlelt kivételeket `init()` tartalmaz, előfordulhat, hogy a **CrashLoopBackOff** hiba jelenik meg a hibaüzenetben.

Használja az információ a [Docker-napló vizsgálata](how-to-troubleshoot-deployment-local.md#dockerlog) című cikket.

## <a name="function-fails-get_model_path"></a>A függvény sikertelen: get_model_path ()

Gyakran előfordul, `init()` hogy a pontozási parancsfájl függvényében a [Model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) függvényt a rendszer megkeresi a modell fájlját vagy a tárolóban a modell fájljainak mappáját. Ha a modellfájl vagy -mappa nem található, a függvény meghiúsul. A hiba hibakeresésének legegyszerűbb módja a következő Python-kód futtatása a Container shellben:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ez a példa megjeleníti a helyi elérési utat (ahhoz képest `/var/azureml-app` ) a tárolóban, ahol a pontozási parancsfájl a modell fájljának vagy mappájának megkeresésére vár. Ezt követően ellenőrizheti, hogy a fájl vagy mappa valóban a várt-e.

Ha a naplózási szint HIBAKERESÉSét állítja be, akkor további információk is naplózhatók, ami hasznos lehet a hiba azonosításához.

## <a name="function-fails-runinput_data"></a>A függvény sikertelen: Futtatás (input_data)

Ha a szolgáltatás sikeresen telepítve van, de összeomlik a pontozási végpontba való adatküldés során, a függvényben hozzáadhat egy Error befogási utasítást, `run(input_data)` hogy a részletes hibaüzenetet adja vissza helyette. Például:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Megjegyzés**: Ha a hívásból hibaüzeneteket ad vissza, `run(input_data)` csak hibakeresési célokat kell végrehajtania. Biztonsági okokból az éles környezetben nem adhat vissza hibaüzeneteket.

## <a name="http-status-code-502"></a>502-es HTTP-állapotkód

A 502 állapotkód azt jelzi, hogy a szolgáltatás kivételt okozott, vagy összeomlott a `run()` score.py fájl metódusában. A cikkben található információk segítségével a fájl hibakeresését folytathatja.

## <a name="http-status-code-503"></a>503-as HTTP-állapotkód

Az Azure Kubernetes szolgáltatás központi telepítései támogatják az automatikus skálázást, amely lehetővé teszi a replikák hozzáadását a további terhelések támogatásához. Az automatikus skálázás a terhelés **fokozatos** változásainak kezelésére szolgál. Ha a kérések másodpercenként nagy számú tüskéket kapnak, az ügyfelek a 503-as HTTP-állapotkódot kapják meg. Annak ellenére, hogy az autoskálázás gyorsan működik, a további tárolók létrehozásához nagy mennyiségű időt vesz igénybe.

A vertikális felskálázásra vagy lefelé irányuló döntések az aktuális tároló-replikák kihasználtságán alapulnak. A foglalt replikák (egy kérelem feldolgozása) száma osztva az aktuális replikák teljes számával. Ha ez a szám meghaladja a számot `autoscale_target_utilization` , a rendszer több replikát hoz létre. Ha alacsonyabb, a replikák csökkennek. A replikák hozzáadására vonatkozó döntések lelkesek és gyorsak (körülbelül 1 másodperc). A replikák eltávolítására vonatkozó döntések konzervatívak (körülbelül 1 perc). Alapértelmezés szerint az automatikus skálázási cél kihasználtsága **70%-ra** van állítva, ami azt jelenti, hogy a szolgáltatás **legfeljebb 30%**-os terhelést képes kezelni a másodpercenkénti kérésekben (RPS).

Két olyan dolog van, amely segíthet megelőzni a 503-es állapotkódot:

> [!TIP]
> Ezt a két megközelítést külön vagy kombinálva lehet használni.

* Módosítsa a kihasználtsági szintet, amelynél az automatikus skálázás új replikákat hoz létre. A kihasználtsági célt úgy állíthatja be, `autoscale_target_utilization` hogy alacsonyabb értéket állít be.

    > [!IMPORTANT]
    > Ez a változás nem eredményezi a replikák *gyorsabb* létrehozását. Ehelyett alacsonyabb kihasználtsági küszöbértékben jönnek létre. Ahelyett, hogy megvárná, amíg a szolgáltatás 70%-ot nem használ, az érték 30%-ra való módosítása a replikákat a 30%-os kihasználtság esetén hozza létre.
    
    Ha a webszolgáltatás már használja a jelenlegi maximális replikákat, és továbbra is a 503-es állapotkódot látja, növelje az `autoscale_max_replicas` értéket a replikák maximális számának növeléséhez.

* Módosítsa a replikák minimális számát. A minimális replikák növelése nagyobb készletet biztosít a bejövő tüskék kezeléséhez.

    A replikák minimális számának növeléséhez állítsa `autoscale_min_replicas` magasabb értékre. A szükséges replikák kiszámításához használja a következő kódot, és cserélje le az értékeket a projekthez tartozó értékekre:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Ha az új minimális replikánál nagyobb kérelmeket kap, akkor a 503s újra megjelenhet. Ha például a szolgáltatás felé irányuló forgalom növekszik, akkor előfordulhat, hogy növelnie kell a minimális replikákat.

A és a for beállításával kapcsolatos további információkért `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` tekintse meg a [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) -modul referenciáját.

## <a name="http-status-code-504"></a>504-es HTTP-állapotkód

A 504 állapotkód azt jelzi, hogy a kérelem túllépte az időkorlátot. Az alapértelmezett időkorlát 1 perc.

A felesleges hívások eltávolításához módosítsa a score.py, vagy próbálja meg felgyorsítani a szolgáltatást. Ha ezek a műveletek nem orvosolják a problémát, a jelen cikkben található információk segítségével a score.py-fájl hibakeresését végezheti el. A kód nem válaszoló állapotban vagy végtelen hurokban is lehet.

## <a name="other-error-messages"></a>Egyéb hibaüzenetek

Tegye a következő hibákat a műveletekhez:

|Hiba  | Feloldás  |
|---------|---------|
|Rendszerkép-létrehozási hiba a webszolgáltatás telepítésekor     |  A "pynacl = = 1.2.1" hozzáadása pip-függőségként a Conda-fájlhoz a rendszerkép-konfigurációhoz       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   A központi telepítésben használt virtuális gépek SKU-jának módosítása több memóriával. |
|FPGA hiba     |  A modelleket nem fogja tudni telepíteni a FPGA, amíg nem kérelmezi és nem hagyta jóvá a FPGA-kvótát. A hozzáférés kéréséhez töltse ki a kvóta kérése űrlapot: https://aka.ms/aml-real-time-ai       |

## <a name="advanced-debugging"></a>Speciális hibakeresés

Előfordulhat, hogy interaktív hibakeresést kell végeznie a modell üzemelő példányában található Python-kódon. Ha például a bejegyzési parancsfájl meghibásodik, és az ok nem határozható meg további naplózással. A Visual Studio Code és a debugpy használatával a Docker-tárolón belül futó kód is csatolható.

További információkért tekintse meg az [interaktív hibakeresést a vs Code útmutatóban](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Modell üzembe helyezésének felhasználói fóruma](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Következő lépések

További információk az üzembe helyezésről:

* [Üzembe helyezés és hol](how-to-deploy-and-where.md)
* [Oktatóanyag: a betanítási & modellek üzembe helyezése](tutorial-train-models-with-aml.md)
* [Kísérletek helyi futtatása és hibakeresése](./how-to-debug-visual-studio-code.md)