---
title: Események aktiválása a gépi tanulási munkafolyamatokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Eseményvezérelt alkalmazások, folyamatok vagy CI/CD gépi tanulási munkafolyamatok beállítása a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 09a541e784167251e0833f6fd3f6130a450e07d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478845"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Alkalmazások, folyamatok vagy CI/CD-munkafolyamatok aktiválása Azure Machine Learning események alapján (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthat be eseményvezérelt alkalmazásokat, folyamatokat vagy CI-/CD-munkafolyamatokat Azure Machine Learning-események, például hibaértesítési e-mailek vagy gépi tanulási folyamatok futtatása alapján, ha a rendszer bizonyos feltételeket [Azure Event Grid.](../event-grid/index.yml)

Azure Machine Learning a gépi tanulási folyamat teljes életciklusát kezeli, beleértve a modell betanítása, a modell üzembe helyezése és figyelése. A Event Grid olyan Azure Machine Learning-eseményekre reagálhat, mint a betanításfutációk befejezése, a modellek regisztrációja és üzembe helyezése, valamint az adatelszelés észlelése modern kiszolgáló nélküli architektúrák használatával. Ezután feliratkozhat és felhasználhatja az olyan eseményeket, mint a futtatás állapotának módosítása, a futtatás befejezése, a modellregisztráció, a modell üzembe helyezése és az adateltávolítés észlelése a munkaterületen.

Mikor használhatók Event Grid eseményvezérelt műveletekhez:
* E-mailek küldése futási hiba esetén és futtatás befejezésekor
* Azure-függvény használata modell regisztrációja után
* Események streamelése Azure Machine Learning végpontokra
* Gépi tanulási folyamat aktiválása eltérés észlelése esetén

## <a name="prerequisites"></a>Előfeltételek
A Event Grid közreműködői vagy tulajdonosi hozzáférésre van szüksége ahhoz a Azure Machine Learning munkaterülethez, amely számára eseményeket fog létrehozni.

## <a name="the-event-model--types"></a>Az eseménymodell & típusokat

Azure Event Grid eseményeket olvas be olyan forrásokból, mint Azure Machine Learning azure-szolgáltatások. Ezeket az eseményeket ezután eseménykezelőknek ( például Azure Event Hubs, Azure Functions, Logic Apps stb. küldi el a program. Az alábbi ábra bemutatja, Event Grid a rendszer hogyan csatlakoztatja a forrásokat és a kezelőket, de ez nem a támogatott integrációk átfogó listája.

![Azure Event Grid modell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

További információ az eseményforrásokról és eseménykezelőkről: [Mi a Event Grid?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Eseménytípusok a Azure Machine Learning

Azure Machine Learning a gépi tanulási életciklus különböző pontjain biztosít eseményeket: 

| Eseménytípus | Leírás |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | A gépi tanulási kísérlet futtatásakor áll elő |
| `Microsoft.MachineLearningServices.ModelRegistered` | Akkor merül fel, ha egy gépi tanulási modell regisztrálva van a munkaterületen |
| `Microsoft.MachineLearningServices.ModelDeployed` | Akkor merül fel, ha egy következtetési szolgáltatás üzembe helyezése egy vagy több modellel fejeződik be |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Akkor merül fel, ha két adatkészlet sodródásészlelési feladata befejeződött |
| `Microsoft.MachineLearningServices.RunStatusChanged` | A futtatás állapotának módosult állapota esetén a jelenleg csak akkor áll elő, ha a futtatás állapota "sikertelen" |

### <a name="filter--subscribe-to-events"></a>Eseményekre való & szűrése

Ezeket az eseményeket a Azure Event Grid. A Azure Portal, a PowerShell vagy az Azure CLI használatával az ügyfelek könnyedén előfizetnek az eseményekre egy vagy több eseménytípus megadásával és szűrési [feltételekkel.](../event-grid/event-filtering.md) 

Az események beállításakor szűrőket alkalmazhat csak adott eseményadatok aktiválására. Az alábbi példában a futtatás állapotának módosult eseményeire futtatástípusok szerint szűrhet. Az esemény csak akkor aktiválódik, ha a feltételek teljesülnek. A szűrhető [eseményadatokról Azure Machine Learning Event](../event-grid/event-schema-machine-learning.md) Grid-sémában olvashat. 

Az előfizetések Azure Machine Learning azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) védik. [Esemény-előfizetéseket](how-to-assign-roles.md#default-roles) csak egy munkaterület közreműködője vagy tulajdonosa hozhat létre, frissíthet és törölhet.  A szűrők az esemény-előfizetések [](/cli/azure/eventgrid/event-subscription) létrehozásakor vagy egy későbbi időpontban alkalmazhatók az esemény-előfizetésre. 


1. A Azure Portal válasszon ki egy új előfizetést vagy egy meglévőt. 

1. Válassza a Szűrők lapot, és görgessen le a Speciális szűrőkig. A Kulcs **és** **érték mezőben** adja meg a szűrni kívánt tulajdonságtípusokat. Itt látható, hogy az esemény csak akkor aktiválódik, ha a futtatás típusa folyamatfuttatás vagy folyamatlépés-futtatás.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="események szűrése":::


+ **Szűrés eseménytípus szerint:** Az esemény-előfizetések megadhatnak egy vagy több Azure Machine Learning eseménytípust.

+ **Szűrés esemény tárgya alapján:** Azure Event Grid támogatja a tárgyszűrőket  az  alapján, hogy egyezéssel kezdődik és végződik, így az egyező tárgyú eseményeket a rendszer kézbesíti az előfizetőnek. A különböző gépi tanulási események különböző tárgyformátummal is fel vannak téve.

  | Eseménytípus | Tárgy formátuma | Minta tárgya |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Speciális szűrés:** Azure Event Grid a közzétett eseménysémán alapuló speciális szűrést is támogatja. Azure Machine Learning eseménysémára vonatkozó részleteket a(Azure Event Grid) eseménysémában [Azure Machine Learning.](../event-grid/event-schema-machine-learning.md)  Néhány példa speciális szűrésre:

  Esemény `Microsoft.MachineLearningServices.ModelRegistered` esetén a modell címkeértékének szűréséhez:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  A szűrők alkalmazásával kapcsolatos további információkért lásd: [Események szűrése](../event-grid/how-to-filter-events.md)a Event Grid.

## <a name="consume-machine-learning-events"></a>Események Machine Learning felhasználva

A Machine Learning eseményeket kezelő alkalmazásoknak követnie kell néhány ajánlott gyakorlatot:

> [!div class="checklist"]
> * Mivel több előfizetés is konfigurálható úgy, hogy az eseményeket ugyanabba az eseménykezelőbe irányítsa, fontos, hogy ne feltételezz egy adott forrásból származó eseményeket, hanem ellenőrizze az üzenet témakörét, hogy az a várt gépi tanulási munkaterületről érkezik-e.
> * Hasonlóképpen ellenőrizze, hogy az eventType az, amelyet fel tud-e készíteni, és ne feltételezzék, hogy minden kapott esemény a várt típusú lesz.
> * Mivel az üzenetek nem sorrendben érkeznek meg, és némi késleltetés után az etag mezőivel tudhatja meg, hogy az objektumokra vonatkozó információk még mindig naprakészek-e.  Emellett a sorrendberendozó mezőivel megértheti az események sorrendjét egy adott objektumon.
> * Hagyja figyelmen kívül a nem érthető mezőket. Ez a gyakorlat segít rugalmasan tartani az új funkciókkal szemben, amelyek a jövőben hozzáadhatóak.
> * Sikertelen vagy megszakított Azure Machine Learning műveletek nem aktiválnak eseményt. Ha például egy modell üzembe helyezése sikertelen, a Microsoft.MachineLearningServices.ModelDeployed nem aktiválódik. Az alkalmazások tervezésekor vegye figyelembe az ilyen hiba üzemmódot. Az SDK Azure Machine Learning, a CLI vagy a portál használatával mindig ellenőrizheti egy művelet állapotát, és megértheti a hiba részletes okait.

Azure Event Grid lehetővé teszi az ügyfelek számára, hogy nem egyező üzenetkezelőket építsenek ki, amelyeket az események Azure Machine Learning aktiválnak. Néhány észrevehető példa az üzenetkezelőkre:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory folyamat
* Általános webhookok, amelyek az Azure platformon vagy máshol is üzemeltethetőek

## <a name="set-up-in-azure-portal"></a>Beállítás a Azure Portal

1. Nyissa meg [Azure Portal,](https://portal.azure.com) és nyissa meg a Azure Machine Learning munkaterületét.

1. A bal oldali sávon válassza az __Események,__ majd az **Esemény-előfizetések lehetőséget.** 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Válassza ki a kívánt eseménytípust. Az alábbi képernyőképen például a __modell__ regisztrálva van, __a Modell__ telepítve, a __Futtatás befejezve__, és __az Adatkészlet__ eltérése észlelhető:

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Válassza ki azt a végpontot, amely számára közzéteheti az eseményt. Az alábbi képernyőképen a __kiválasztott végpont az Eseményközpont:__

    ![Képernyőkép az Esemény-előfizetés létrehozása panelről, megnyitott Eseményközpont kiválasztása gombra kattintva.](./media/how-to-use-event-grid/select-event-handler.png)

Miután megerősítette a választást, kattintson a __Létrehozás gombra.__ A konfigurálás után ezek az események le lesznek kértve a végpontra.


### <a name="set-up-with-the-cli"></a>Beállítás a CLI-val

Telepítheti a legújabb [Azure CLI-t,](/cli/azure/install-azure-cli)vagy használhatja Azure Cloud Shell azure-előfizetés részeként biztosított alkalmazásokat.

A Event Grid telepítéséhez használja a parancssori felület alábbi parancsát:

```azurecli-interactive
az add extension --name eventgrid
```

Az alábbi példa bemutatja, hogyan választhat ki egy Azure-előfizetést, és hogyan lehet új esemény-előfizetést Azure Machine Learning:

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

### <a name="example-send-email-alerts"></a>Példa: E-mailes riasztások küldése

A [Azure Logic Apps](../logic-apps/index.yml) konfigurálhatja az e-maileket az összes eseményhez. Testre szabhatja a feltételeket, és meghatározhatja a címzetteket, hogy lehetővé tegye a különböző csapatok együttműködését és tudatosságát.

1. A Azure Portal a munkaterületen Azure Machine Learning bal oldali sávon válassza az Események lapot. Itt válassza a __Logic Apps lehetőséget.__ 

    ![Képernyőkép a Machine Learning Események lapról a Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Jelentkezzen be a logikai alkalmazás felhasználói felületére, és válassza Machine Learning szolgáltatás témakörtípust. 

    ![Az Erőforrás-esemény bekövetkezténekkor párbeszédpanel képernyőképe, erőforrástípusként a Gépi tanulás beállítással.](./media/how-to-use-event-grid/select-topic-type.png)

1. Válassza ki, hogy mely eseményekről legyen értesítve. Az alábbi képernyőképen például a __FuttatásCompleted parancs látható.__

    ![Képernyőkép az Erőforrás-esemény bekövetkeztének esetén párbeszédpanelről, kiválasztott eseménytípussal.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Használhatja a fenti szakaszban található szűrési metódust, vagy hozzáadhat szűrőket, hogy a logikai alkalmazás csak az eseménytípusok egy részkészletén aktiválódik. Az alábbi képernyőképen __a__ __/datadriftID/runs/__ előtagszűrőt használjuk.

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Ezután adjon hozzá egy lépést, amely felhasználja ezt az eseményt, és e-mailt keres. Az események fogadására számos különböző e-mail-fiók használható. Azt is beállíthatja, hogy mikor küldjön e-mailes riasztást.

    ![Képernyőkép a Művelet kiválasztása párbeszédpanelről, a keresőmezőbe beírt e-mail-címmel.](./media/how-to-use-event-grid/select-email-action.png)

1. Válassza __az E-mail küldése lehetőséget,__ és adja meg a paramétereket. A tárgyban az események szűréséhez  használhatja az __Eseménytípust__ és a Témakört. Az üzenet törzsében a futtatásokat a munkaterület oldalára mutató hivatkozással is meg lehet jelenni. 

    ![Képernyőkép az E-mail küldése párbeszédpanelről, a jobb oldali lista tárgysorába beszúrt Témakör és Eseménytípus értékekkel.](./media/how-to-use-event-grid/configure-email-body.png)

1. A művelet mentéshez válassza a **Mentés** másként lehetőséget a lap bal felső sarkában. A megjelenő jobb sávon erősítse meg a művelet létrehozását.

    ![Képernyőkép a Mentés másként és a Létrehozás gombról a Logic Apps Designerben.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Példa: Sodródás eseményindítók újraképezése

A modellek idővel elavultak, és nem maradnak hasznosak abban a környezetben, amiben fut. Az egyik módszer annak kimutatására, hogy itt az ideje a modell újraképezésének, a sodródás észlelése. 

Ez a példa bemutatja, hogyan használható az Event Grid egy Azure Logic App-alkalmazással az újraképzés aktiválásához. A példa elindít egy Azure Data Factory, amikor sodródik a modell betanítása és kiszolgálása között.

Mielőtt hozzákezd, hajtsa végre a következő műveleteket:

* Adathalmaz-figyelő beállítása a [sodródás](how-to-monitor-datasets.md) észleléséhez egy munkaterületen
* Hozzon létre egy [közzétett Azure Data Factory folyamatot.](../data-factory/index.yml)

Ebben a példában egy egyszerű Data Factory egy blobtárolóba másolunk fájlokat, és futtatunk egy közzétett Machine Learning folyamatot. További információ erről a forgatókönyvről: Útmutató Machine Learning [a](../data-factory/transform-data-machine-learning-service.md) Azure Data Factory

![Képernyőkép a Betanítás folyamatról a gyári erőforrásokban, az Adatok másolása1 kimásoló M L végrehajtási folyamat1 mezővel.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Kezdje a logikai alkalmazás létrehozásával. Keresse meg a [Azure Portal,](https://portal.azure.com)keressen rá a Logic Apps, és válassza a létrehozás lehetőséget.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Adja meg a kért adatokat. A folyamat egyszerűsítése érdekében használja ugyanazt az előfizetést és erőforráscsoportot, mint Azure Data Factory a Azure Machine Learning munkaterületen.

    ![A Logikai alkalmazás Létrehozása panel képernyőképe.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. A logikai alkalmazás létrehozása után válassza az __Erőforrás-esemény Event Grid lehetőséget.__ 

    ![Képernyőkép a Logic Apps Designerről, a Kezdés közös eseményindítóval lehetőséggel, beleértve az Erőforrás Event Grid esemény bekövetkeztét.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Jelentkezzen be, és adja meg az esemény részleteit. Az Erőforrás __neve beállításban adja__ meg a munkaterület nevét. Állítsa az __Eseménytípust__ __DatasetDriftDetected beállításra.__

    ![Képernyőkép az Erőforrás-esemény bekövetkeztének esete eseménytípus-elem kijelölve lehetőségről.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Adjon hozzá egy új lépést, és keressen rá a __Azure Data Factory.__ Válassza __a Folyamatfuttassanak létrehozása lehetőséget.__ 

    ![Képernyőkép a Művelet kiválasztása panelről, a Folyamatfuttassanak létrehozása lehetőség választásával.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Jelentkezzen be, és adja meg a Azure Data Factory futtatni kívánt folyamatot.

    ![Képernyőkép a Folyamatfutatás létrehozása panelről különböző értékekkel.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Mentse és hozza létre  a logikai alkalmazást az oldal bal felső részen található Mentés gombbal. Az alkalmazás megtekintéséhez a munkaterületet a Azure Portal [kattintson](https://portal.azure.com) az **Események elemre.**

    ![Az eseményeket a logikai alkalmazás kiemelését bemutató képernyőkép.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Az adat-előállító folyamat mostantól eltolt eltérés esetén aktiválódik. Az adatsodródási futtatás és a gépi tanulási folyamat részleteinek megtekintése az új [munkaterületi portálon.](https://ml.azure.com) 

![Folyamatvégpontokat bemutató képernyőkép.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Példa: Modell üzembe helyezése címkék alapján

A Azure Machine Learning-objektum olyan paramétereket tartalmaz, amelyek alapján el tudja fordni az üzembe helyezéseket, például a modell nevét, verzióját, címkéit és tulajdonságát. A modellregisztrációs esemény elindíthat egy végpontot, és egy Azure-függvénnyel üzembe helyezhet egy modellt ezen paraméterek értéke alapján.

Példaként tekintse meg az adattárat, és [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) kövesse a **readme fájlban található** lépéseket.

## <a name="next-steps"></a>Következő lépések

További információ a Event Grid és Azure Machine Learning eseményekről:

- [Bevezetés az Event Grid használatába](../event-grid/overview.md)

- [Eseményséma a Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
