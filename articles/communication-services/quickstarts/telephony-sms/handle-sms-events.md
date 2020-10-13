---
title: Gyors útmutató – SMS-események kezelése
titleSuffix: An Azure Communication Services quickstart
description: Ismerje meg, hogyan kezelheti az SMS-eseményeket az Azure kommunikációs szolgáltatásokkal.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e66d5c4dd4fc1c6c641da975b0ac2254f459642a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976929"
---
# <a name="quickstart-handle-sms-events"></a>Gyors útmutató: SMS-események kezelése

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások használatának első lépései a kommunikációs szolgáltatások SMS-események kezelésére Azure Event Grid használatával. 

## <a name="about-azure-event-grid"></a>Tudnivalók Azure Event Grid

A [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) egy felhőalapú esemény-szolgáltatás. Ebből a cikkből megtudhatja, hogyan fizethet elő a [kommunikációs szolgáltatás eseményeire](../../concepts/event-handling.md)vonatkozó eseményekre, és hogyan válthat ki egy eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Ebben a cikkben elküldjük az eseményeket egy olyan webalkalmazásnak, amely összegyűjti és megjeleníti az üzeneteket.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy Azure kommunikációs szolgáltatás erőforrása. További részletek az [Azure kommunikációs erőforrás létrehozása című](../create-communication-resource.md) útmutatóban olvashatók.
- SMS-kompatibilis telefonszám. [Telefonszám beolvasása](./get-phone-number.md).

## <a name="setting-up"></a>Beállítás

### <a name="enable-event-grid-resource-provider"></a>Event Grid erőforrás-szolgáltató engedélyezése

Ha korábban még nem használta Event Grid az Azure-előfizetésében, előfordulhat, hogy regisztrálnia kell a Event Grid erőforrás-szolgáltatót az alábbi lépésekkel:

Az Azure Portalon:

1. Válassza az **előfizetések** lehetőséget a bal oldali menüben.
2. Válassza ki az Event Gridhez használt előfizetést.
3. A bal oldali menüben, a **Beállítások**területen válassza az **erőforrás-szolgáltatók**elemet.
4. Keresse ki a **Microsoft.EventGrid** elemet.
5. Ha nincs regisztrálva, válassza a **Regisztrálás** lehetőséget. 

Lehetséges, hogy a regisztráció végrehajtása eltart néhány pillanatig. Válassza a **Frissítés** lehetőséget az állapotfrissítéshez. Ha az **Állapot** értéke **Regisztrált**, folytathatja.

### <a name="event-grid-viewer-deployment"></a>Event Grid Viewer üzembe helyezése

Ebben a rövid útmutatóban a [Azure Event Grid Viewer mintát](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) fogjuk használni az események közel valós idejű megtekintéséhez. Ez biztosítja a felhasználó számára a valós idejű hírcsatornák élményét. Emellett az egyes események hasznos adatait is elérhetővé kell tenniük a vizsgálathoz.  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Előfizetés az SMS-eseményekre webhookok használatával

A portálon navigáljon az Ön által létrehozott Azure Communication Services-erőforráshoz. A kommunikációs szolgáltatás erőforrásán belül válassza a **kommunikációs szolgáltatások** lap bal oldali menüjének **események** elemét.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Képernyőfelvétel: az esemény-előfizetések gombjának kiválasztása az erőforrás események lapján.":::

Kattintson az **esemény-előfizetés hozzáadása** elemre a létrehozási varázsló megadásához.

Az **esemény-előfizetés létrehozása** lapon adja meg az esemény-előfizetés **nevét** .

Előfizethet bizonyos eseményekre, hogy elmondja Event Grid a nyomon követni kívánt SMS-események, valamint az események küldésének helyét. Válassza ki azokat az eseményeket, amelyekre elő szeretné fizetni a legördülő menüből. SMS-ben lehetőség van a és a kiválasztására `SMS Received` `SMS Delivery Report Received` . 

Tekintse meg az [Azure kommunikációs szolgáltatások által támogatott események](../../concepts/event-handling.md)teljes listáját.

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Képernyőfelvétel: az esemény-előfizetések gombjának kiválasztása az erőforrás események lapján.":::

Válassza a **webes Hook** lehetőséget a **végpont típusához**. 

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Képernyőfelvétel: az esemény-előfizetések gombjának kiválasztása az erőforrás események lapján.":::

A **végpont**esetében kattintson a **válasszon egy végpontot**lehetőségre, és adja meg a webalkalmazás URL-címét.

Ebben az esetben a rövid útmutatóban korábban beállított [Azure Event Grid Viewer-minta](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) URL-címét fogjuk használni. A minta URL-címe a következő formátumban jelenik meg: `https://{{site-name}}.azurewebsites.net/api/updates`

Ezután válassza a **kijelölés megerősítése**lehetőséget.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Képernyőfelvétel: az esemény-előfizetések gombjának kiválasztása az erőforrás események lapján.":::

## <a name="viewing-sms-events"></a>SMS-események megtekintése

### <a name="triggering-sms-events"></a>SMS-események indítása

Az esemény-eseményindítók megtekintéséhez először elő kell állítani az eseményeket.

- `SMS Received` az események akkor jönnek létre, amikor a kommunikációs szolgáltatások telefonszáma szöveges üzenetet kap. Egy esemény elindításához csak küldjön egy üzenetet a telefonról a kommunikációs szolgáltatások erőforrásához csatolt telefonszámra.
- `SMS Delivery Report Received` az események akkor jönnek létre, amikor SMS-t küld egy felhasználónak a kommunikációs szolgáltatások telefonszáma segítségével. Egy esemény elindításához engedélyeznie kell az `Delivery Report` SMS-ben a [küldést](../telephony-sms/send.md). Próbáljon meg üzenetet küldeni a telefonjára a következővel: `Delivery Report` . Ennek a műveletnek a végrehajtásával az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

Tekintse meg az [Azure kommunikációs szolgáltatások által támogatott események](../../concepts/event-handling.md)teljes listáját.

### <a name="receiving-sms-events"></a>SMS-események fogadása

A fenti műveletek elvégzése után láthatja, hogy a rendszer `SMS Received` `SMS Delivery Report Received` elküldi az eseményeket a végpontnak. Ezek az események az elején beállított [Azure Event Grid Viewer-mintában](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) jelennek meg. Az esemény melletti szem ikon megnyomásával megtekintheti a teljes adattartalmat. Az események így fognak kinézni:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Képernyőfelvétel: az esemény-előfizetések gombjának kiválasztása az erőforrás események lapján.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Képernyőfelvétel: az esemény-előfizetések gombjának kiválasztása az erőforrás események lapján.":::

További információk az [esemény-sémákkal és egyéb eseményvezérelt fogalmakkal](../../concepts/event-handling.md)kapcsolatban.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhat SMS-eseményeket. SMS-üzeneteket a Event Grid előfizetés létrehozásával fogadhat.

> [!div class="nextstepaction"] 
> [SMS küldése](../telephony-sms/send.md)

A következőket is érdemes elvégezheti:

 - [További tudnivalók az események kezelésére vonatkozó fogalmakról](../../concepts/event-handling.md)
 - [Tudnivalók a Event Grid](https://docs.microsoft.com/azure/event-grid/overview)
