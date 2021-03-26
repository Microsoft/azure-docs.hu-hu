---
title: 'Gyors útmutató: az Azure IoT-összekötő üzembe helyezése a FHIR-ben (előzetes verzió) a Azure Portal használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe, konfigurálhatja és használhatja az Azure IoT-összekötőt a FHIR készült Azure API FHIR szolgáltatásához a Azure Portal használatával.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 91b3097e465458181074d1e450e69f267d0fe556
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026785"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Gyors útmutató: az Azure IoT-összekötő üzembe helyezése a FHIR-ben (előzetes verzió) a Azure Portal használatával

Az Azure IoT-összekötő a gyors egészségügyi együttműködési erőforrásokhoz (FHIR&#174;) * az Azure API opcionális funkciója a FHIR-hez, amely lehetővé teszi az orvosi eszközök internetes hálózatáról (IoMT) származó adatok betöltését. Az előzetes verzió ideje alatt az Azure IoT Connector for FHIR funkció ingyenesen elérhető. Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:
- Az Azure IoT Connector üzembe helyezése és konfigurálása a FHIR-hez a Azure Portal használatával
- Egy szimulált eszköz használata az Azure IoT Connectorba való adatküldéshez FHIR
- Az Azure IoT Connector által létrehozott erőforrások megtekintése az Azure API for FHIR FHIR

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API FHIR-erőforráshoz – [Az Azure API üzembe helyezése a FHIR-hez a Azure Portal használatával](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Ugrás az Azure API FHIR-erőforráshoz

Nyissa meg a [Azure Portalt](https://portal.azure.com) , és lépjen az **Azure API for FHIR** erőforráshoz, amelyhez létre szeretné hozni az Azure IoT-összekötőt a FHIR szolgáltatáshoz.

[![Azure API FHIR-erőforráshoz](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

A bal oldali navigációs menüben kattintson a **IoT-összekötő (előzetes verzió)** elemre a **bővítmények** szakaszban a IoT- **Összekötők** lap megnyitásához.

[![IoT-összekötő funkció](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Új Azure IoT-összekötő létrehozása a FHIR (előzetes verzió)

Kattintson a **Hozzáadás** gombra az **IoT-összekötő létrehozása** lap megnyitásához.

[![IoT-összekötő hozzáadása](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Adja meg a FHIR új Azure IoT-összekötő beállításait. Kattintson a **Létrehozás** gombra, és várja meg az Azure IoT-összekötőt a FHIR telepítéséhez.

> [!NOTE]
> Válassza a **Létrehozás** lehetőséget a **megoldás típusa** legördülő listából a telepítéshez. 

[![IoT-összekötő létrehozása](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Beállítás|Érték|Leírás |
|---|---|---|
|Összekötő neve|Egyedi név|Adjon meg egy nevet az Azure IoT-összekötő azonosításához a FHIR ennek a névnek egyedinek kell lennie a FHIR-erőforráshoz készült Azure API-n belül. A név csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Betűvel vagy számmal kell kezdődnie és végződnie, és a hosszának 3-24 karakter közöttinek kell lennie.|
|Megoldás típusa|Keresés vagy létrehozás|Válassza a **lookup (keresés** ) lehetőséget, ha sávon kívüli folyamattal hozza létre az [eszköz](https://www.hl7.org/fhir/device.html) és a [PÁCIENS](https://www.hl7.org/fhir/patient.html) FHIR erőforrásait a FHIR készült Azure API-ban. A FHIR készült Azure IoT-összekötő ezeket az erőforrásokat fogja használni, amikor egy [megfigyelési](https://www.hl7.org/fhir/observation.html) FHIR-erőforrást hoz létre az eszközre vonatkozó információk megjelenítéséhez. Válassza a **Létrehozás** lehetőséget, ha azt szeretné, hogy az Azure IoT CONNECTOR a FHIR-hoz hozzon létre operációs rendszer nélküli eszközt és a beteg erőforrásait az Azure API-ban az FHIR lévő megfelelő azonosító értékek használatával.|

A telepítés befejezése után az újonnan létrehozott Azure IoT-összekötő a FHIR-hoz megjelenik a **IoT-összekötők** lapon.

[![IoT-összekötő létrehozva](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Az Azure IoT Connector konfigurálása a FHIR (előzetes verzió)

A FHIR készült Azure IoT Connectornak két leképezési sablonra van szüksége az eszköz üzeneteinek FHIR-alapú megfigyelési erőforrás (ok) ra való átalakításához: **eszköz-hozzárendelés** és **FHIR-hozzárendelés**. A FHIR készült Azure IoT-összekötő nem teljesen működőképes, amíg fel nem töltődik a leképezések.

[![IoT-összekötő hiányzó leképezései](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

A leképezési sablonok feltöltéséhez kattintson az újonnan telepített Azure IoT-összekötőre a FHIR, és lépjen a **IoT-összekötő** lapra.

[![IoT-összekötő kattintson](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Eszköz-hozzárendelés

Az eszköz-hozzárendelési sablon egy normalizált sémába alakítja át az eszközöket. Az **IoT-összekötő** lapon kattintson az **eszköz-hozzárendelés konfigurálása** gombra az **eszköz-hozzárendelés** lapra való ugráshoz. 

[![IoT-összekötő kattintson az eszköz-hozzárendelés konfigurálása elemre.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Az **eszköz-hozzárendelés** lapon adja hozzá a következő parancsfájlt a JSON-szerkesztőhöz, és kattintson a **Mentés** gombra.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT-összekötő eszközének leképezése](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>FHIR leképezése

A FHIR-leképezési sablon normalizált üzenetet alakít át egy FHIR-alapú megfigyelési erőforrásra. Az **IoT-összekötő** lapon kattintson a **FHIR-leképezés konfigurálása** gombra, hogy a **FHIR-leképezési** lapra ugorjon.  

[![IoT-összekötő kattintson a FHIR-leképezés konfigurálása elemre.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

A **FHIR-leképezés** lapon adja hozzá a következő parancsfájlt a JSON-szerkesztőhöz, és kattintson a **Mentés** gombra.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT-összekötő FHIR leképezése](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Kapcsolati sztring létrehozása

A IoMT-eszköznek kapcsolati sztringre van szüksége az Azure IoT Connectorhoz való csatlakozáshoz és az FHIR-hez való üzenetküldéshez. A FHIR újonnan telepített Azure IoT-összekötő **IoT-összekötő** lapján válassza az **Ügyfélkapcsolatok kezelése** gombot. 

[![IoT-összekötő kattintson az Ügyfélkapcsolatok kezelése elemre.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

A **kapcsolatok** lapon kattintson a **Hozzáadás** gombra egy új kapcsolat létrehozásához. 

[![IoT-összekötő kapcsolatai](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Adja meg a kapcsolatok rövid nevét az átfedések ablakban, és kattintson a **Létrehozás** gombra.

[![IoT-összekötő új kapcsolatai](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Válassza ki az újonnan létrehozott kapcsolatot a **kapcsolatok** lapon, és másolja az **elsődleges kapcsolati sztring** mező értékét a jobb oldali átfedési ablakból.

[![IoT-összekötő csatlakozási karakterlánca](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

A kapcsolódási karakterlánc megőrzése egy későbbi lépésben való használatra. 

## <a name="connect-your-devices-to-iot"></a>Eszközök csatlakoztatása a IoT-hez

Az Azure a IoT-termékek széles választékát kínálja a IoT-eszközök csatlakoztatásához és kezeléséhez. Az Azure IoT Hub használatával saját megoldását is létrehozhatja, vagy elindíthatja a IoT-alkalmazások kezelése platformot az Azure IoT Central segítségével. Ebben az oktatóanyagban kihasználjuk az Azure IoT Central-t, amely az iparági környezettel foglalkozó megoldási sablonokkal segíti az első lépéseket.

Telepítse a [folyamatos beteg monitorozási alkalmazás sablonját](../../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template). Ez a sablon két szimulált eszközt tartalmaz, amelyek valós idejű adatfeldolgozást biztosítanak az első lépések megtételéhez: **intelligens vitális javítás** és **intelligens térd zárójel**.

> [!NOTE]
> Ha a valódi eszközök készen állnak, ugyanazt a IoT Central alkalmazást használhatja az [eszközök](../../iot-central/core/howto-set-up-template.md) bevezetéséhez és az eszköz-szimulátorok cseréjéhez. Az eszköz adatai automatikusan FHIR is áramlanak. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Csatlakoztassa a IoT-adatait az Azure IoT-összekötővel a FHIR (előzetes verzió) szolgáltatáshoz

Miután telepítette a IoT Central alkalmazást, a két beépített szimulált eszköz elkezdi a telemetria generálását. Ebben az oktatóanyagban betöltjük a telemetria a *Smart vitals patch* Simulator-ből a FHIR-be a FHIR-hez készült Azure IoT-összekötőn keresztül. Ha a IoT-adatait az Azure IoT-Összekötőbe szeretné exportálni a FHIR-hez, [be kell állítania egy folyamatos adatexportálást IoT Centralon belül](../../iot-central/core/howto-export-data.md). Először létre kell hoznia egy kapcsolódást a célhelyhez, majd egy adatexportálási feladatot hozunk létre a folyamatos futtatáshoz: 

Hozzon létre egy új célhelyet:
- Lépjen a **Célhelyek** lapra, és hozzon létre egy új célhelyet.
- Első lépésként adjon egyedi nevet a célhelynek.
- Válassza az *Azure Event Hubs* a célhely típusaként.
- Adja meg az Azure IoT-összekötőt a FHIR azon csatlakozási karakterláncához, amely egy előző lépésben a **csatlakozási sztring** mezőhöz lett beszerzett.

Új adatexportálás létrehozása:
- Miután létrehozta a célhelyet, ugorjon a **Exportálás** lapra, és hozzon létre egy új adatexportálást. 
- Először adja meg, hogy az adatok egyedi nevet exportálnak.
- Az **adat** kiválasztása területen válassza az *telemetria* lehetőséget az *exportálandó adat típusaként*.
- A **célhely** területen válassza ki az előző névben létrehozott célhely nevét.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Eszközbeállítások megtekintése a FHIR készült Azure API-ban

Az Azure IoT Connector által létrehozott FHIR-alapú megfigyelési erőforrás (oka) t a Poster használatával tekintheti meg az Azure API FHIR-hez készült FHIR. Állítsa be a [Poster-t, hogy hozzáférjen az Azure API-hoz a FHIR-hez](access-fhir-postman-tutorial.md) , és `GET` kérjen kérelmet a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` megfigyelési FHIR erőforrásainak a pulzusszám értékkel való megtekintéséhez. 

> [!TIP]
> Győződjön meg arról, hogy a felhasználó megfelelő hozzáféréssel rendelkezik az Azure API-hoz a FHIR adatsíkon. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](configure-azure-rbac.md) használatával rendelje hozzá a szükséges adatsíkok-szerepköröket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az Azure IoT Connector egy példányát a FHIR, ha eltávolítja a társított erőforráscsoportot, vagy a társított Azure API-t a FHIR szolgáltatáshoz, vagy maga az Azure IoT-összekötőt a FHIR-példányhoz. 

Ha közvetlenül el szeretné távolítani egy Azure IoT-összekötőt a FHIR-példányhoz, válassza ki a példányt a **IoT-összekötők** lapról, és lépjen a **IoT-összekötő** lapra, és kattintson a **Törlés** gombra. Ha a rendszer megerősítést kér, válassza az **Igen** lehetőséget. 

[![IoT-összekötő példányának törlése](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezte az Azure IoT Connectort a FHIR szolgáltatáshoz a FHIR-erőforráshoz készült Azure API-ban. Válasszon az alábbi lépések közül, ha többet szeretne megtudni a FHIR készült Azure IoT Connectorról:

Megismerheti az Azure IoT-összekötőn belüli adatáramlás különböző szakaszait az FHIR-ben.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő a FHIR-adatfolyamhoz](iot-data-flow.md)

Ismerje meg, hogyan konfigurálhatja az IoT-összekötőt eszköz-és FHIR-leképezési sablonok használatával.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR-leképezési sablonokhoz](iot-mapping-templates.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban.
