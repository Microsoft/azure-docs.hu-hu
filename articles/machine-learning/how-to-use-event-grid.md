---
title: Események elindítása ML-munkafolyamatokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Eseményvezérelt alkalmazások, folyamatok vagy CI/CD gépi tanulási munkafolyamatok beállítása Azure Machine Learningban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 9dc7c0a476f57048c7caefb6001918d314c991ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209696"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Alkalmazások, folyamatok vagy CI/CD-munkafolyamatok elindítása Azure Machine Learning események alapján (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthat be eseményvezérelt alkalmazásokat, folyamatokat vagy CI/CD-munkafolyamatokat Azure Machine Learning események alapján, például a sikertelen értesítési e-mailek vagy a ML-folyamatok futtatását, ha a [Azure Event Grid](../event-grid/index.yml)bizonyos feltételeket észlel.

A Azure Machine Learning a gépi tanulási folyamat teljes életciklusát kezeli, beleértve a modellek betanítását, a modell üzembe helyezését és a figyelést. A Event Grid segítségével reagálhat Azure Machine Learning eseményekre, például a képzések befejezésére, a modellek regisztrálására és üzembe helyezésére, valamint az adateltolódás észlelésére a modern kiszolgáló nélküli architektúrák használatával. Ezután előfizethet és használhat olyan eseményeket, mint például a Futtatás állapota, a Futtatás befejezése, a modell regisztrálása, a modell üzembe helyezése és az adateltolódások észlelése egy munkaterületen belül.

Mikor kell Event Grid használni az eseményvezérelt műveletekhez:
* E-mailek küldése a futtatási hibákról és a Futtatás befejezéséről
* Azure-függvény használata a modell regisztrálása után
* Azure Machine Learning különböző végpontokra irányuló folyamatos átviteli események
* ML-folyamat elindítása a drift észlelésekor

## <a name="prerequisites"></a>Előfeltételek
A Event Grid használatához közreműködői vagy tulajdonosi hozzáféréssel kell rendelkeznie a Azure Machine Learning munkaterülethez, ahol eseményeket fog létrehozni.

## <a name="the-event-model--types"></a>Az Event Model & típusai

Azure Event Grid a forrásokból, például Azure Machine Learningokból és egyéb Azure-szolgáltatásokból származó eseményeket olvas be. Ezeket az eseményeket ezután az eseménykezelők küldik, például az Azure Event Hubs, a Azure Functions, a Logic Apps és egyebek. Az alábbi ábra azt mutatja be, hogy Event Grid hogyan kapcsolódik a forrásokhoz és a kezelőhöz, de nem a támogatott integrációk átfogó listája.

![Azure Event Grid funkcionális modell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

További információ az eseményforrás és az eseménykezelők használatáról: [Mi az Event Grid?](../event-grid/overview.md)

### <a name="event-types-for-azure-machine-learning"></a>Azure Machine Learningi események típusai

A Azure Machine Learning a gépi tanulási életciklus különböző pontjain biztosít eseményeket: 

| Eseménytípus | Leírás |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Gépi tanulási kísérlet futtatásának befejeződése után következik be |
| `Microsoft.MachineLearningServices.ModelRegistered` | Akkor következik be, amikor egy Machine learning-modell van regisztrálva a munkaterületen |
| `Microsoft.MachineLearningServices.ModelDeployed` | Akkor következik be, amikor egy vagy több modellel rendelkező következtetési szolgáltatás üzembe helyezése befejeződött |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Akkor következik be, amikor két adatkészlet adateltolódás-észlelési feladata befejeződött |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Futási állapot megváltozásakor következik be, amely jelenleg csak akkor jelenik meg, ha a futtatási állapot "sikertelen". |

### <a name="filter--subscribe-to-events"></a>& előfizetések szűrése eseményekre

Ezek az események Azure Event Gridon keresztül jelennek meg. A Azure Portal, a PowerShell vagy az Azure CLI használatával az ügyfelek egyszerűen előfizethetnek az eseményekre [egy vagy több eseménytípus megadásával, valamint a szűrési feltételek megadásával](../event-grid/event-filtering.md). 

Az események beállításakor szűrőket alkalmazhat, hogy csak adott esemény adatain aktiválja az eseményindítót. Az alábbi példában a Futtatás állapotának megváltozása események esetében a futtatási típusok alapján szűrhet. Az esemény csak akkor aktiválódik, ha a feltételek teljesülnek. Tekintse át az [Azure Machine learning Event Grid-sémát](../event-grid/event-schema-machine-learning.md) , ahol megismerheti azokat az események adatait, amelyeket szűrni tud. 

Az Azure Machine Learning eseményekre vonatkozó előfizetéseket az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) védi. A munkaterületek csak [közreműködői vagy tulajdonosai](how-to-assign-roles.md#default-roles) hozhatnak létre, frissíthetnek és törölhetnek esemény-előfizetéseket.  A szűrők az esemény-előfizetés [létrehozásakor](/cli/azure/eventgrid/event-subscription) vagy egy későbbi időpontban is alkalmazhatók az események előfizetésére. 


1. Nyissa meg a Azure Portal, válasszon ki egy új előfizetést, vagy egy meglévőt. 

1. Válassza a szűrők fület, és görgessen le a speciális szűrők elemre. A **kulcs** és az **Érték mezőben** adja meg a szűréshez használni kívánt tulajdonságokat. Itt láthatja, hogy az esemény csak akkor aktiválódik, ha a futtatási típus folyamat-futtatási vagy folyamati lépés fut.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Események szűrése":::


+ **Szűrés eseménytípus szerint:** Az esemény-előfizetés egy vagy több Azure Machine Learning eseménytípus megadására használható.

+ **Szűrés esemény tárgya szerint:** A Azure Event Grid a-től __kezdődően__ __a és a egyezéseknek megfelelően támogatja__ a tulajdonosi szűrőket, így a megfelelő tárgyú események az előfizetőnek lesznek továbbítva. A különböző gépi tanulási események formátuma eltérő.

  | Eseménytípus | Tulajdonos formátuma | Minta tárgya |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Speciális szűrés**: a Azure Event Grid a közzétett esemény sémája alapján is támogatja a speciális szűrést. Azure Machine Learning az esemény-séma részletei megtalálhatók a [Azure Machine Learning Azure Event Grid esemény sémájában](../event-grid/event-schema-machine-learning.md).  Néhány példaként használható speciális szűrés:

  `Microsoft.MachineLearningServices.ModelRegistered`Esemény esetén a modell címke értékének szűréséhez:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  A szűrők alkalmazásával kapcsolatos további információkért lásd: [Események szűrése Event Grid](../event-grid/how-to-filter-events.md).

## <a name="consume-machine-learning-events"></a>Machine Learning események felhasználása

Az Machine Learning eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való továbbításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg róla, hogy a várt Machine learning-munkaterületről származik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Mivel az üzenetek nem érkeznek meg a sorrendbe, és némi késés után a ETAG mezőkből megtudhatja, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e.  Emellett a Sequencer mezőket is használhatja az események sorrendjének megismeréséhez egy adott objektumra vonatkozóan.
> * Figyelmen kívül hagyhatja a nem értelmezhető mezőket. Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
> * Sikertelen vagy megszakított Azure Machine Learning művelet nem indít eseményt. Ha például a modell központi telepítése sikertelen, a Microsoft. MachineLearningServices. ModelDeployed nem aktiválódik. Az alkalmazások tervezésekor vegye figyelembe az ilyen meghibásodási módot. Azure Machine Learning SDK-val, parancssori felülettel vagy portálral bármikor megtekintheti egy művelet állapotát, és megismerheti a hibák részletes okát.

Azure Event Grid lehetővé teszi, hogy az ügyfelek olyan, de egymással összekapcsolt üzenetkezelőket hozzanak létre, amelyeket Azure Machine Learning események indíthatnak el. Az üzenetkezelő néhány jelentős példája a következő:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory folyamat
* Általános webhookok, amelyek az Azure platformon vagy máshol is üzemeltethető

## <a name="set-up-in-azure-portal"></a>Beállítás a Azure Portalban

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és lépjen a Azure Machine learning munkaterületére.

1. A bal oldali sávon válassza az __események__ lehetőséget, majd válassza az **esemény-előfizetések** lehetőséget. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Válassza ki a használni kívánt eseménytípus típusát. Az alábbi képernyőképen például a __regisztrált modell__ van kiválasztva, a __modell üzembe helyezése__, a __Futtatás befejeződött__ és az __adatkészlet-eltolódás észlelhető__:

    ![hozzáadási esemény típusa](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Válassza ki azt a végpontot, amelyen közzé kívánja tenni az eseményt. A következő képernyőképen az __Event hub__ a kiválasztott végpont:

    ![A képernyőképen az esemény-előfizetések létrehozása panel jelenik meg, az Event hub megnyitásával.](./media/how-to-use-event-grid/select-event-handler.png)

Miután megerősítette a kijelölést, kattintson a __Létrehozás__ gombra. A konfigurálás után ezeket az eseményeket a rendszer leküldi a végpontnak.


### <a name="set-up-with-the-cli"></a>Beállítás a parancssori felülettel

Telepítheti a legújabb [Azure CLI](/cli/azure/install-azure-cli)-t, vagy használhatja az Azure-előfizetésének részeként biztosított Azure Cloud Shell is.

A Event Grid bővítmény telepítéséhez használja az alábbi parancsot a parancssori felületről:

```azurecli-interactive
az add extension --name eventgrid
```

Az alábbi példa bemutatja, hogyan választhat ki egy Azure-előfizetést, és létrehoz egy új esemény-előfizetést a Azure Machine Learning számára:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Példák

### <a name="example-send-email-alerts"></a>Példa: e-mail értesítések küldése

A [Azure Logic apps](../logic-apps/index.yml) használatával konfigurálhatja az összes eseményhez tartozó e-maileket. Testre szabhatja a feltételeket, és megadhatja a címzetteket, hogy az együttműködés és a tájékoztatás lehetővé váljon a csapatok között.

1. A Azure Portal nyissa meg a Azure Machine Learning munkaterületet, és válassza a bal oldali sáv események lapját. Innen válassza a __Logic apps__ lehetőséget. 

    ![A képernyőképen egy Machine Learning munkaterület-események lap jelenik meg, Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Jelentkezzen be a logikai alkalmazás felhasználói felületére, és válassza a Machine Learning szolgáltatás lehetőséget a témakör típusaként. 

    ![A képernyőképen az erőforrás-esemény bekövetkezésekor párbeszédpanel jelenik meg, amelyen a Machine learning erőforrás-típus van kiválasztva.](./media/how-to-use-event-grid/select-topic-type.png)

1. Válassza ki, hogy mely esemény (ek) ra kell értesítést kapni. Például a következő képernyőkép- __RunCompleted__.

    ![Képernyőfelvétel: az esemény típusának kiválasztásakor a rendszer megjeleníti az erőforrás-esemény bekövetkezésekor megjelenő párbeszédpanelt.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. A fenti szakaszban található szűrési módszer használatával vagy szűrők hozzáadásával csak a logikai alkalmazást aktiválhatja az eseménytípus egy részhalmazán. A következő képernyőképen a __/datadriftID/Runs/__ __előtag-szűrőjét__ használja a rendszer.

    ![szűrés – események](./media/how-to-use-event-grid/filtering-events.png)

1. Ezután adjon hozzá egy lépést az esemény felhasználásához és az e-mailek kereséséhez. Az események fogadására több különböző levelezési fiók is használható. Megadhatja a feltételeket is, amikor e-mail riasztást küld.

    ![A képernyőképen a művelet választása párbeszédpanel jelenik meg, amely e-mailben szerepel a keresési sorban.](./media/how-to-use-event-grid/select-email-action.png)

1. Válassza az __E-mail küldése__ lehetőséget, és adja meg a paramétereket. A tárgyban megadhatja az __esemény típusát__ és a __témakört__ , hogy segítsen szűrni az eseményeket. A munkaterületre mutató hivatkozást is tartalmazhat, ha az üzenet törzsében fut. 

    ![Képernyőfelvétel: az e-mailek küldése párbeszédpanel, ahol a témakör és az esemény típusa a tárgy sorba kerül a listából a jobb oldalon.](./media/how-to-use-event-grid/configure-email-body.png)

1. A művelet mentéséhez válassza a **Mentés másként** lehetőséget az oldal bal oldali sarkában. A megjelenő jobb oldali sávon erősítse meg a művelet létrehozását.

    ![A képernyőképen látható a Mentés másként és a létrehozás gomb a Logic Apps Designerben.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Példa: az adateltolódás-eseményindítók újraképzése

A modellek elavultak az idő múlásával, és nem maradnak hasznosak abban a kontextusban, amelyben a fut. Az egyik módszer, ha meg szeretné tudni, hogy a modell újratanításához szükséges idő az adateltolódás észlelése. 

Ez a példa azt mutatja be, hogyan használható az Event Grid egy Azure Logic App-alkalmazással az újraképzés elindításához. A példa egy Azure Data Factory folyamatot indít el, amikor adateltolódás történik a modell betanítása és az adatkészletek kiszolgálása között.

Mielőtt elkezdené, hajtsa végre a következő műveleteket:

* Adatkészlet-figyelő beállítása a munkaterület [adateltolódásának észleléséhez](how-to-monitor-datasets.md)
* Közzétett [Azure Data Factory folyamat](../data-factory/index.yml)létrehozása.

Ebben a példában egy egyszerű Data Factory folyamatot használunk a fájlok blob-tárolóba történő másolásához és egy közzétett Machine Learning folyamat futtatásához. További információ erről a forgatókönyvről: [Machine learning lépés beállítása Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![A képernyőfelvételen a gyári erőforrások betanítási folyamata látható, a másolási Data1-t pedig az M L Execute Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Kezdje a logikai alkalmazás létrehozásával. Lépjen a [Azure Portalra](https://portal.azure.com), keresse meg a Logic apps, majd kattintson a Létrehozás gombra.

    ![Keresés – Logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Adja meg a kért információkat. A felhasználói élmény egyszerűsítése érdekében használja ugyanazt az előfizetést és erőforráscsoportot, mint a Azure Data Factory folyamat és a Azure Machine Learning munkaterület.

    ![Képernyőfelvétel: a logikai alkalmazás létrehozási paneljének megjelenítése.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Miután létrehozta a logikai alkalmazást, válassza ki, __hogy mikor történjen egy Event Grid erőforrás esemény__. 

    ![A képernyőképen a Logic Apps Designer látható, amely általános eseményindító-beállításokkal, többek között Event Grid erőforrás-esemény bekövetkezésekor jelenik meg.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Jelentkezzen be, és adja meg az esemény részleteit. Állítsa az __erőforrás nevét__ a munkaterület nevére. Állítsa az __DatasetDriftDetected__ az __esemény típusára__ .

    ![Képernyőfelvétel: az az eset, amikor egy erőforrás esemény van kiválasztva egy esemény típusú elemmel.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adjon hozzá egy új lépést, és keressen rá __Azure Data Factory__. Válassza __a folyamat futtatásának létrehozása__ lehetőséget. 

    ![Képernyőfelvétel: a művelet kiválasztása ablaktábla, ahol a folyamat létrehozása elem be van jelölve.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Jelentkezzen be, és adja meg a közzétett Azure Data Factory folyamat futtatását.

    ![Képernyőfelvétel: a folyamat futtatásának létrehozása panel különböző értékekkel.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Mentse és hozza létre a logikai alkalmazást a lap bal felső részén található **Mentés** gombbal. Az alkalmazás megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com) munkaterületére, majd kattintson az **események** elemre.

    ![A képernyőképen a logikai alkalmazás kiemelt eseményei láthatók.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Ekkor a rendszer a folyamatban lévő adatfeldolgozási folyamatot indítja el a drift bekövetkeztekor. Tekintse meg az adatdrift futtatásával és a Machine learning folyamatával kapcsolatos adatokat az [Új munkaterület portálon](https://ml.azure.com). 

![A képernyőfelvételen a folyamat végpontjai láthatók.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Példa: modell üzembe helyezése címkék alapján

Az Azure Machine Learning Model objektum olyan paramétereket tartalmaz, amelyekkel az üzemelő példányok elhelyezhetők, például a modell neve, verziója, címkéje és tulajdonsága. A modell regisztrációs eseménye elindíthat egy végpontot, és használhat egy Azure-függvényt egy modell üzembe helyezéséhez a paraméterek értéke alapján.

Példaként tekintse meg a [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) tárházat, és kövesse a **readme** fájlban található lépéseket.

## <a name="next-steps"></a>Következő lépések

További információ a Event Gridről és a Azure Machine Learning események megadásáról:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)

- [Azure Machine Learningi esemény sémája](../event-grid/event-schema-machine-learning.md)
