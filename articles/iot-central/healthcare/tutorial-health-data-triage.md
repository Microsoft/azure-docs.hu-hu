---
title: Oktatóanyag – Health adatosztályozási irányítópult létrehozása az Azure IoT Central | Microsoft Docs
description: Oktatóanyag – Ismerje meg, hogyan hozhat létre Health adatosztályozási irányítópultot az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: d227d934eedd31342ce419576fffe7cea17efb1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748074"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Oktatóanyag: Power BI-szolgáltató irányítópultjának összeállítása

A folyamatos beteg-figyelési megoldás létrehozásakor létrehozhat egy irányítópultot a kórházi ápolási csapat számára a páciensek adatainak megjelenítéséhez. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Power BI valós idejű adatfolyam-irányítópultot a IoT Central folyamatos beteg-figyelési alkalmazás sablonból. Ha a használati eset nem igényli a valós idejű adatelérést, használhatja a [IoT Central Power bi irányítópultot](../core/howto-connect-powerbi.md), amely egyszerűsített telepítési folyamattal rendelkezik. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="Irányítópult GIF":::

Az alapszintű architektúra a következő struktúrát fogja követni:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Szolgáltató osztályozása irányítópult":::

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok exportálása az Azure IoT Centralból az Azure-ba Event Hubs
> * Power BI folyamatos átviteli adatkészlet beállítása
> * A logikai alkalmazás összekötése az Azure Event Hubs
> * Stream-adatok Power BI a logikai alkalmazásból
> * Valós idejű irányítópult létrehozása a páciensek számára – létfontosságú


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure IoT Central folyamatos beteg monitorozási alkalmazás sablonja. Ha még nem rendelkezik ilyennel, kövesse az [alkalmazás-sablonok üzembe helyezésének](overview-iot-central-healthcare.md)lépéseit.

* Egy Azure [Event Hubs névtér és Event hub](../../event-hubs/event-hubs-create.md).

* Az Event hub eléréséhez használni kívánt logikai alkalmazás. A logikai alkalmazás Azure Event Hubs triggerrel való indításához [üres logikai alkalmazásra](../../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

* Egy Power BI szolgáltatás-fiók. Ha még nem rendelkezik ilyennel, [létrehozhat egy ingyenes próbaverziós fiókot Power bi szolgáltatáshoz](https://app.powerbi.com/). Ha még nem használta Power BI korábban, hasznos lehet a [Power bi](/power-bi/service-get-started)használatának megkezdéséhez.


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Folyamatos adatexportálás beállítása az Azure-ba Event Hubs
Először be kell állítania egy folyamatos adatexportálást az Azure IoT Central-alkalmazás sablonból az előfizetésében található Azure Event hub-ba. Ezt az Azure IoT Central oktatóanyag lépéseit követve végezheti el [Event Hubs exportálásához](../core/howto-export-data.md). Ehhez az oktatóanyaghoz csak a telemetria kell exportálni.


## <a name="create-a-power-bi-streaming-dataset"></a>Power BI folyamatos átviteli adatkészlet létrehozása

1. Jelentkezzen be a Power BI-fiókjába.

1. Az előnyben részesített munkaterületen hozzon létre egy új folyamatos átviteli adatkészletet az eszköztár jobb felső sarkában található **+ Létrehozás** gombra kattintva. Létre kell hoznia egy külön adatkészletet minden olyan beteg számára, amelyet szeretne az irányítópulton.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Folyamatos átviteli adatkészlet létrehozása":::


1. Válassza az **API** lehetőséget az adatkészlet forrásához.

1. Adjon meg egy **nevet** (például a beteg nevét) az adatkészlethez, majd töltse ki a streamben található értékeket. Az alábbi példát láthatja a folyamatos beteg monitorozási alkalmazás sablonjában a szimulált eszközökről érkező értékek alapján. A példa két betegből áll:

   * Teddy Silvers, akik a Smart térd Zárójelből származó adatokkal rendelkeznek.
   * Yesenia Sanford, aki az intelligens vitális javításból származó adatokkal rendelkezik.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Adatkészlet értékeinek megadása":::

Ha többet szeretne megtudni a Power BI lévő adatfolyam-adatkészletekről, olvassa el a következő dokumentumot [valós idejű adatfolyamként Power BIban:](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>A logikai alkalmazás összekötése az Azure Event Hubs

A logikai alkalmazás Azure Event Hubshoz való összekapcsolásához kövesse a jelen dokumentumban ismertetett utasításokat az [események az azure Event Hubs és a Azure Logic Apps használatával való elküldéséhez](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action). Néhány javasolt paraméter:

|Paraméter|Érték|
|---|---|
|Tartalomtípus|application/json|
|Időköz|3|
|Gyakoriság|Second|

Ennek a lépésnek a végén a Logic app designernek így kell kinéznie:

>[!div class="mx-imgBorder"] 
>![Logic Apps csatlakozik Event Hubs](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Adatkészlet értékeinek megadása":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Stream-adatok Power BI a logikai alkalmazásból

A következő lépés az, hogy elemezze az Event hub-ról érkező adatokat a korábban létrehozott Power BI adatkészletekben.

A művelet megkezdése előtt meg kell ismernie az eszközről az Event hub-ba küldött JSON-adattartalmat. Ehhez tekintse meg ezt a [minta sémát](../core/howto-export-data.md#telemetry-format) , és módosítsa úgy, hogy az megfeleljen a sémának, vagy a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) használatával vizsgálja meg az üzeneteket. Ha a folyamatos beteg monitorozási alkalmazásokat használja, az üzenetek a következőképpen fognak kinézni:

**Smart Vitals – javítási telemetria**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Intelligens térd kapcsos zárójel telemetria**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Tulajdonságok**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. Most, hogy megtekintette a JSON-adattartalmakat, térjen vissza a Logic app Designerre, és válassza az **+ új lépés** lehetőséget. A következő lépésként keresse meg és adja hozzá az **inicializálás változót** , és adja meg a következő paramétereket:

   |Paraméter|Érték|
   |---|---|
   |Név|Csatoló neve|
   |Típus|Sztring|

   Kattintson a **Mentés** gombra. 

1. Adjon hozzá egy " **Body** " nevű másik változót **karakterláncként**. A logikai alkalmazás a következő műveleteket fogja hozzáadni:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Változók inicializálása":::
    
1. Válassza az **+ új lépés** lehetőséget, és adjon hozzá egy **elemzési JSON** -műveletet. Nevezze át ezt az **elemzési tulajdonságokra**. A tartalomhoz válassza az Event hub-ból származó **Tulajdonságok** elemet. Válassza a minta hasznos adatok használata lehetőséget a séma alján **való létrehozásához** , majd illessze be a minta adattartalmat a fenti tulajdonságok szakaszból.

1. Ezután válassza a **változó beállítása** műveletet, és frissítse a **csatoló neve** változót a **iothub-Interface-Name** elemre az elemzett JSON-tulajdonságok közül.

1. Vegyen fel egy **felosztott** vezérlőelemet a következő műveletként, és válassza ki a **csatoló neve** változót az on paraméterrel. Ezzel az adatok a megfelelő adatkészletbe lesznek tölcsérben.

1. Az Azure IoT Central alkalmazásban keresse meg az intelligens létfontosságú adatok javítási állapotának adatait, valamint az intelligens térd zárójelek állapotának adatait az **eszköz sablonok** nézetből. Hozzon létre két különböző esetet az egyes interfészekhez tartozó **kapcsolók** vezérléséhez, és nevezze át a vezérlőt megfelelően. Beállíthatja az alapértelmezett esetet **a leállítási vezérlő használatára** , és kiválaszthatja, hogy melyik állapotot szeretné megjeleníteni.

   :::image type="content" source="media/split-by-interface.png" alt-text="Felosztás vezérlése":::

1. A **Smart vitals patch** esetében adjon hozzá egy **elemzési JSON** -műveletet. A tartalomhoz válassza az Event hub-ból származó **tartalmat** . Másolja ki és illessze be a fenti Smart Vitals-javításhoz tartozó minta hasznos adatokat a séma létrehozásához.

1. Adjon hozzá egy **set változót** , és frissítse a **Body** változót az elemzett JSON **törzsével** a 7. lépésben.

1. Vegyen fel egy **feltétel** vezérlőelemet a következő műveletként, és állítsa be a feltételt a **törzs**, a **tartalmaz**, a **HeartRate**. Ezzel a beállítással megadhatja, hogy az intelligens létfontosságú javításokból származó adatok megfelelő készlete legyen az Power BI adatkészlet feltöltése előtt. A 7-9. lépés a következőképpen fog kinézni:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Intelligens Vitals – feltétel hozzáadása":::

1. A feltétel **valódi** esetéhez adjon hozzá egy műveletet, amely meghívja a **sorok hozzáadása egy adatkészlethez** Power bi funkciót. Ehhez be kell jelentkeznie Power BI. A **hamis** eset újra használhatja a **megszakítási** vezérlőt.

1. Válassza ki a megfelelő **munkaterületet**, **adatkészletet** és **táblát**. Képezze le a Power BI adatfolyam-adatkészletének létrehozásakor megadott paramétereket az Event hub-ból származó elemzett JSON-értékekre. A kitöltött műveleteknek így kell kinéznie:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Sorok hozzáadása a Power BIhoz":::

1. Az **intelligens térd zárójeles** kapcsoló esetében adjon hozzá egy **elemzési JSON** -műveletet a tartalom elemzéséhez, hasonlóan a 7. lépéshez. Ezután **sorokat adhat hozzá egy adatkészlethez** , hogy frissítse a Teddy Silvers-adatkészletet a Power BIban.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Az adatkészletekhez tartozó sorok hozzáadását bemutató képernyőkép":::

1. Kattintson a **Save (Mentés** ) gombra, majd futtassa a logikai alkalmazást.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Valós idejű irányítópult létrehozása a páciensek számára – létfontosságú

Most lépjen vissza a Power BIra, és válassza a **+ Létrehozás** lehetőséget egy új **irányítópult** létrehozásához. Adja meg az irányítópult nevét, és kattintson a **create (létrehozás**).

Válassza ki a három pontot a felső navigációs sávon, majd válassza a **+ csempe hozzáadása** lehetőséget.

:::image type="content" source="media/add-tile.png" alt-text="Csempe hozzáadása az irányítópulthoz":::

Válassza ki, hogy milyen típusú csempét szeretne felvenni, és testreszabni szeretné az alkalmazást.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforrásokat a következő lépésekkel:

1. A Azure Portal törölheti az Event hub-t és Logic Apps a létrehozott erőforrásokat.

1. IoT Central alkalmazásához lépjen az adminisztráció lapra, és válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Folyamatos kórházi monitorozási architektúra – útmutató](concept-continuous-patient-monitoring-architecture.md)