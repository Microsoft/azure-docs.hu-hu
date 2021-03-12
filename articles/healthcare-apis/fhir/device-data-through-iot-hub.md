---
title: 'Oktatóanyag: eszközre vonatkozó adatfogadás az Azure IoT Hub'
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti az eszköz adattovábbítását IoT Hubból az Azure API-ba az Azure IoT-összekötővel a FHIR-hez való FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 36d6d8b4d4435b1db99f2721c817c9c06ca60945
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019183"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Oktatóanyag: eszközre vonatkozó adatfogadás az Azure IoT Hub

Az Azure IoT-összekötő a gyors egészségügyi együttműködési erőforrásokhoz (FHIR&#174;) * lehetőséget nyújt az orvosi eszközök internetes hálózatáról (IoMT) származó adatoknak az Azure API-ba való betöltésére a FHIR. Az [Azure IoT-összekötő üzembe helyezése a FHIR-ben (előzetes verzió) a Azure Portal](iot-fhir-portal-quickstart.md) rövid útmutatóban az Azure IoT Central által felügyelt eszközre mutat példát, amely [telemetria küld](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) az Azure IoT Connectornak FHIR. A FHIR készült Azure IoT-összekötő az Azure IoT Hub használatával kiépített és felügyelt eszközökkel is használható. Ez az oktatóanyag azt az eljárást ismerteti, amellyel az Azure IoT Hub az Azure IoT-összekötőhöz csatlakoztathatja és irányíthatja az eszközöket az FHIR-hez.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API FHIR-erőforráshoz legalább egy Azure IoT-összekötővel a FHIR – [Azure IoT-összekötő üzembe helyezése a FHIR-ben (előzetes verzió) a Azure Portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub-erőforrás valós vagy szimulált eszközhöz csatlakoztatva – [IoT hub létrehozása a Azure Portal használatával](../../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Ha Azure IoT Hub szimulált eszköz alkalmazást használ, a különböző támogatott nyelvek és rendszerek közül választhat.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>A FHIR Azure IoT-összekötőhöz tartozó csatlakozási karakterlánc beolvasása (előzetes verzió)

Az Azure IoT Hub kapcsolati karakterláncot igényel az Azure IoT-összekötő FHIR való biztonságos csatlakoztatásához. Hozzon létre egy új csatlakozási karakterláncot a FHIR készült Azure IoT-összekötőhöz a következő témakörben leírtak szerint: Create a Reporting [String](iot-fhir-portal-quickstart.md#generate-a-connection-string) Őrizze meg ezt a kapcsolódási karakterláncot a következő lépésben való használatra.

A FHIR készült Azure IoT-összekötő egy Azure Event hub-példányt használ a motorháztető alatt az eszköz üzeneteinek fogadásához. A fent létrehozott kapcsolati sztring alapvetően a kapcsolati sztring ehhez a mögöttes Event hub-hoz.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Az Azure IoT Hub összekötése a FHIR készült Azure IoT-összekötővel (előzetes verzió)

Az Azure IoT Hub támogatja az [üzenet-útválasztás](../../iot-hub/iot-hub-devguide-messages-d2c.md) nevű szolgáltatást, amely lehetővé teszi az eszközre vonatkozó adatküldés lehetőségét különböző Azure-szolgáltatások, például az Event hub, a Storage-fiók és a Service Bus számára. A FHIR készült Azure IoT Connector ezt a funkciót használja az Azure-IoT Hub az Event hub-végpontba való kapcsolódáshoz és az eszköz adatainak küldéséhez.

> [!NOTE] 
> Jelenleg csak a PowerShell vagy a CLI parancs használható az üzenet- [Útválasztás létrehozásához](../../iot-hub/tutorial-routing.md) , mert az FHIR Event hub-hoz készült Azure IoT Connector nem az ügyfél-előfizetésen fut, ezért nem lesz látható a Azure Portalon keresztül. Bár ha a PowerShell vagy a parancssori felület használatával hozzáadja az üzenet-útválasztási objektumokat, azok láthatók lesznek a Azure Portalon, és ott is kezelhetők.

Az üzenetek útválasztásának beállítása két lépésből áll.

### <a name="add-an-endpoint"></a>Végpont hozzáadása
Ez a lépés egy olyan végpontot határoz meg, amelybe a IoT Hub átirányítja az adattovábbítást. Hozza létre ezt a végpontot az [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell [-paranccsal vagy az IOT hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create) CLI parancs használatával a beállítás alapján.

Itt látható a végpont létrehozásához a paranccsal használandó paraméterek listája:

|PowerShell-paraméter|CLI-paraméter|Leírás|
|---|---|---|
|ResourceGroupName|resource-group|Az IoT Hub erőforrás erőforráscsoport-neve.|
|Name|hub neve|A IoT Hub erőforrás neve.|
|Végpontneve|végpont neve|Használjon olyan nevet, amelyet hozzá szeretne rendelni a létrehozandó végponthoz.|
|EndpointType|végpont típusa|Annak a végpontnak a típusa, amelyhez IoT Hub csatlakoznia kell. Használja a "EventHub" konstans értékét a PowerShell és a "EventHub" számára a CLI-hez.|
|EndpointResourceGroup|végpont – erőforrás-csoport|Erőforráscsoport neve az Azure IoT-összekötőhöz a FHIR Azure API-hoz a FHIR-erőforráshoz. Ezt az értéket a FHIR készült Azure API áttekintés lapján érheti el.|
|EndpointSubscriptionId|végpont – előfizetés-azonosító|Az Azure IoT-összekötő előfizetés-azonosítója a FHIR Azure API-hoz a FHIR-erőforráshoz. Ezt az értéket a FHIR készült Azure API áttekintés lapján érheti el.|
|ConnectionString|connection-string|A FHIR készült Azure IoT-összekötőhöz tartozó kapcsolódási karakterlánc. Használja az előző lépésben beszerzett értéket.|

### <a name="add-a-message-route"></a>Üzenet útvonalának hozzáadása
Ez a lépés egy üzenet útvonalát határozza meg a fent létrehozott végpont használatával. Hozzon létre egy útvonalat az [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell-paranccsal vagy [az IOT hub Route Create](/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI parancs használatával a beállítás alapján.

Az üzenet útvonalának hozzáadásához a paranccsal használható paramétereket a következő lista tartalmazza:

|PowerShell-paraméter|CLI-paraméter|Leírás|
|---|---|---|
|ResourceGroupName|g|Az IoT Hub erőforrás erőforráscsoport-neve.|
|Name|hub neve|A IoT Hub erőforrás neve.|
|Végpontneve|végpont neve|A fent létrehozott végpont neve.|
|Z|útvonal neve|A létrehozandó üzenet-útvonalhoz hozzárendelni kívánt név.|
|Forrás|forrás típusa|A végpontnak küldendő adattípusok. Használja a "DeviceMessages" konstans értékét a PowerShell és a "DeviceMessages" számára a CLI-hez.|

## <a name="send-device-message-to-iot-hub"></a>Eszköz üzenet küldése IoT Hub

Az eszköz (valós vagy szimulált) használatával elküldheti az alábbi minta szívfrekvencia-üzenetet az Azure IoT Hubnak. Ez az üzenet a FHIR Azure IoT-összekötőhöz lesz irányítva, ahol az üzenet egy FHIR-megfigyelési erőforrásba lesz átalakítva, és a FHIR Azure API-ba kerül.

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
> [!IMPORTANT]
> Ügyeljen arra, hogy a FHIR Azure IoT-összekötővel konfigurált [leképezési sablonoknak](iot-mapping-templates.md) megfelelő üzenetet küldjön.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Eszközbeállítások megtekintése a FHIR készült Azure API-ban

Az Azure IoT Connector által létrehozott FHIR-megfigyelési erőforrás (oka) t megtekintheti a FHIR-hez készült Azure API-ban, a Poster használatával. Állítsa be a [Poster-t, hogy hozzáférjen az Azure API-hoz a FHIR-hez](access-fhir-postman-tutorial.md) , és `GET` kérést küldjön a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` megfigyelési FHIR-erőforrások megtekintésére a fenti mintában elküldött pulzusszám értékkel.

> [!TIP]
> Győződjön meg arról, hogy a felhasználó megfelelő hozzáféréssel rendelkezik az Azure API-hoz a FHIR adatsíkon. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](configure-azure-rbac.md) használatával rendelje hozzá a szükséges adatsíkok-szerepköröket.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban beállíthatja, hogy az Azure IoT Hub átirányítsa az eszközöket az Azure IoT Connectorba a FHIR számára. Válasszon az alábbi lépések közül, ha többet szeretne megtudni a FHIR készült Azure IoT Connectorról:

Megismerheti az Azure IoT-összekötőn belüli adatáramlás különböző szakaszait az FHIR-ben.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő a FHIR-adatfolyamhoz](iot-data-flow.md)

Ismerje meg, hogyan konfigurálhatja az IoT-összekötőt eszköz-és FHIR-leképezési sablonok használatával.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR-leképezési sablonokhoz](iot-mapping-templates.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban.