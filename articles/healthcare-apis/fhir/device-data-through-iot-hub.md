---
title: 'Oktatóanyag: Eszközadatok fogadása Azure IoT Hub'
description: Ez az oktatóanyag bemutatja, hogyan engedélyezheti az eszközök adatirányítását a IoT Hub-Azure API for FHIR az Azure IoT Connector for FHIR segítségével.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 77d54ef0c9bef40af47c2fc48b9d4b5d6315b119
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780394"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Oktatóanyag: Eszközadatok fogadása Azure IoT Hub

Az Azure IoT Connector for Fast Healthcare Interoperability Resources (FHIR&#174;)* lehetővé teszi, hogy adatokat kérjen le az Internet of Medical Things (IoMT) eszközökről a Azure API for FHIR. Az [Azure IoT Connector for FHIR (előzetes verzió)](iot-fhir-portal-quickstart.md) üzembe helyezése Azure Portal rövid útmutatóban egy [](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) olyan eszközre mutattunk példát, amelyet telemetria az FHIR-hez Azure IoT Central azure IoT Connector küldésével kezelt. Az Azure IoT Connector for FHIR a szolgáltatáson keresztül kiépített és felügyelt eszközökkel is Azure IoT Hub. Ez az oktatóanyag az eszközadatoknak az FHIR-hez Azure IoT Hub Azure IoT Connector való csatlakoztatását és útválasztását biztosítja.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR Azure IoT Connector for FHIR-lel – [Az Azure IoT Connector for FHIR (előzetes verzió)](iot-fhir-portal-quickstart.md) üzembe helyezése az Azure Portal
- Azure IoT Hub vagy szimulált eszköz(ekkel) csatlakoztatott erőforrás – [IoT Hub](../../iot-hub/quickstart-send-telemetry-dotnet.md) létrehozása a Azure Portal

> [!TIP]
> Ha egy szimulált Azure IoT Hub alkalmazást használ, szabadon választhatja ki a választott alkalmazást a különböző támogatott nyelvek és rendszerek között.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Az Azure IoT Connector for FHIR kapcsolati sztringje (előzetes verzió)

Azure IoT Hub kapcsolati sztringre van szükség az Azure IoT Connector for FHIR-hez való biztonságos csatlakozáshoz. Hozzon létre egy új kapcsolati sztringet az Azure IoT Connector for FHIR számára [a Kapcsolati](iot-fhir-portal-quickstart.md#generate-a-connection-string)sztring létrehozása. Őrizze meg ezt a kapcsolati sztringet, hogy a következő lépésben használni fog.

Az Azure IoT Connector for FHIR egy Azure Event Hub-példányt használ a háttérben az eszközüzenetek fogadásához. A fent létrehozott kapcsolati sztring alapvetően az alapul szolgáló eseményközpont kapcsolati sztringe.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Csatlakozás Azure IoT Hub Azure IoT Connector for FHIR-hez (előzetes verzió)

Azure IoT Hub támogatja az üzenet-útválasztás nevű funkciót, amely lehetővé teszi eszközadatok elküldését különböző Azure-szolgáltatásoknak, például az Event Hubnak, a Storage-fióknak és a Service Bus. [](../../iot-hub/iot-hub-devguide-messages-d2c.md) Az Azure IoT Connector for FHIR ezzel a funkcióval csatlakozik, és eszközadatokat küld Azure IoT Hub az eseményközpont végpontjára.

> [!NOTE] 
> Jelenleg csak PowerShell- vagy CLI-paranccsal hozhat létre üzenet-útválasztást, mert az Azure IoT Connector for FHIR eseményközpontja nem az ügyfél-előfizetésen van üzemeltetve, ezért az nem jelenik meg az Azure Portal. [](../../iot-hub/tutorial-routing.md) Azonban miután hozzáadta az üzenetútvonal-objektumokat a PowerShell vagy a parancssori felület használatával, azok láthatók a Azure Portal és onnan kezelhetők.

Az üzenetek útválasztásának beállítása két lépésből áll.

### <a name="add-an-endpoint"></a>Végpont hozzáadása
Ez a lépés egy végpontot határoz meg, amelyre a IoT Hub az adatokat irányítják. Hozza létre ezt a végpontot az [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell-paranccsal vagy [az az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create) CLI-paranccsal, az Ön igényeinek megfelelő alapján.

Itt található a végpont létrehozásához a paranccsal használható paraméterek listája:

|PowerShell-paraméter|CLI-paraméter|Description|
|---|---|---|
|ResourceGroupName|resource-group|A IoT Hub erőforráscsoport neve.|
|Name|hub-name|A IoT Hub neve.|
|EndpointName (Végpont neve)|végpont neve|Adjon meg egy nevet, amit hozzá szeretne rendelni a létrehozott végponthoz.|
|EndpointType (Végponttípus)|végpont-típus|A végpont típusa, IoT Hub csatlakoznia kell. Használja az "EventHub" literális értékét a PowerShellhez, az "eventhub" értéket pedig a parancssori felülethez.|
|EndpointResourceGroup|endpoint-resource-group|Az Azure-beli virtuális IoT Connector FHIR-erőforrás erőforráscsoport-Azure API for FHIR neve. Ezt az értéket a webhely Áttekintés Azure API for FHIR.|
|EndpointSubscriptionId (EndpointSubscriptionId)|endpoint-subscription-id|Az FHIR IoT Connector Azure-előfizetésének Azure API for FHIR azonosítója. Ezt az értéket a webhely Áttekintés Azure API for FHIR.|
|ConnectionString (Kapcsolati karakterlánc)|connection-string|Kapcsolati sztring az Azure IoT Connector FHIR-hez. Használja az előző lépésben lekért értéket.|

### <a name="add-a-message-route"></a>Üzenetútvonal hozzáadása
Ez a lépés egy üzenetútvonalat határoz meg a fent létrehozott végpont használatával. Hozzon létre egy útvonalat az [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell-paranccsal vagy [az az iot hub route create](/cli/azure/iot/hub/route#az_iot_hub_route_create) CLI-paranccsal az Ön igényeinek megfelelő alapján.

Az üzenetútvonal hozzáadásához az paranccsal használható paraméterek listája a következő:

|PowerShell-paraméter|CLI-paraméter|Description|
|---|---|---|
|ResourceGroupName|g|A IoT Hub erőforráscsoport neve.|
|Name|hub-name|A IoT Hub neve.|
|EndpointName (Végpont neve)|végpont neve|A fent létrehozott végpont neve.|
|RouteName (Útvonal neve)|útvonalnév|A létrehozott üzenetútvonalhoz hozzárendelni kívánt név.|
|Forrás|forrástípus|A végpontnak küldenünk szükséges adatok típusa. Használja a "DeviceMessages" konstansértéket a PowerShellhez, és a "devicemessages" konstansértéket a parancssori felülethez.|

## <a name="send-device-message-to-iot-hub"></a>Eszközüzenet küldése a IoT Hub

Az eszköz (valós vagy szimulált) használatával küldje el az alább látható szívverési mintaüzenetet a Azure IoT Hub. Ez az üzenet az Azure IoT Connector for FHIR-be lesz irányítva, ahol az üzenet FHIR-megfigyelési erőforrásvá lesz átalakítva, és a rendszer a Azure API for FHIR.

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
> Mindenképpen olyan eszközüzenetet küldjön, [](iot-mapping-templates.md) amely megfelel az Azure IoT Connector for FHIR-hez konfigurált leképezési sablonoknak.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Eszközadatok megtekintése a Azure API for FHIR

A Postman használatával megtekintheti az Azure IoT Connector for FHIR által létrehozott FHIR-Azure API for FHIR erőforrás(okat). Állítsa be a [Postmant,](access-fhir-postman-tutorial.md) hogy hozzáférjen Azure API for FHIR, és kérést küld a megfigyelés FHIR-erőforrásainak megtekintésére a fenti mintaüzenetben elküldött `GET` `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` pulzusértékkel.

> [!TIP]
> Győződjön meg arról, hogy a felhasználó megfelelő hozzáféréssel rendelkezik Azure API for FHIR adatsíkhoz. A [szükséges adatsík-szerepkörök hozzárendeléséhez használja az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC).](configure-azure-rbac.md)


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban eszközadatokat Azure IoT Hub az Azure IoT Connector az FHIR-hez. Válasszon az alábbi következő lépések közül, ha többet szeretne megtudni az Azure IoT Connector for FHIR-ről:

Az Azure-ban az adatfolyam különböző szakaszainak IoT Connector FHIR-hez.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR-adatfolyam](iot-data-flow.md)

Megtudhatja, hogyan konfigurálhatja a IoT Connector eszköz- és FHIR-leképezési sablonokkal.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR-leképezési sablonok](iot-mapping-templates.md)

*A Azure Portal az Azure IoT Connector for FHIR a IoT Connector (előzetes verzió). Az FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével használható.