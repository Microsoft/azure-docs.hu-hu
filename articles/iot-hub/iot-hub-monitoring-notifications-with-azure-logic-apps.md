---
title: IoT távoli monitorozás és értesítések Azure Logic Apps | Microsoft Docs
description: Használja Azure Logic Apps IoT-hőmérséklet monitorozásához az IoT Hubon, és automatikusan küldjön e-mail-értesítéseket a postaládába az észlelt anomáliákról.
author: robinsh
keywords: iot-monitorozás, iot-értesítések, iot temperature monitoring
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564938"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT távoli monitorozás és értesítések Azure Logic Apps IoT Hub és postaláda csatlakoztatásával

![Végpontok között diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) a munkafolyamatok vezénylése a helyszíni és a felhőszolgáltatások, egy vagy több vállalat, valamint a különböző protokollok között. A logikai alkalmazás egy eseményindítóval kezdődik, amelyet egy vagy több olyan művelet követ, amely beépített vezérlőkkel, például feltételekkel és iterátorokkal indítható el. Ez a rugalmasság teszi Logic Apps IoT-megoldássá ioT-monitorozási forgatókönyvekhez. Ha például telemetriai adatokat kap egy eszközről egy IoT Hub-végpontra, az elindíthatja a logikai alkalmazás munkafolyamatait az adatok Azure Storage-blobban való tárolásához, e-mail-riasztásokat küldhet az adatanomáliákra való figyelmeztetéshez, ütemezhet egy technikust, ha egy eszköz hibát észlel stb.

Ebből a cikkből megtudhatja, hogyan hozhat létre olyan logikai alkalmazást, amely csatlakoztatja az IoT Hubot és a postaládát a hőmérséklet monitorozásához és az értesítésekhez. Az eszközön futó ügyfélkód egy alkalmazástulajdonság () beállítását állítja be az IoT Hubnak küldött összes `temperatureAlert` telemetriai üzenethez. Ha az ügyfélkód 30 C feletti hőmérsékletet észlel, ezt a tulajdonságot a következőre állítja: ; ellenkező esetben a tulajdonságot a következőre `true` állítja: `false` .

Az IoT Hubra érkező üzenetek a következő módon néznek ki: a telemetriai adatok a törzsben és az alkalmazás tulajdonságaiban található tulajdonsággal (a rendszertulajdonságok nem `temperatureAlert` jelennek meg):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Az üzenetformátummal kapcsolatos IoT Hub lásd: Create and read IoT Hub messages (Üzenetek [létrehozása IoT Hub olvasása).](iot-hub-devguide-messages-construct.md)

Ebben a témakörben útválasztást állíthat be az IoT Hubon olyan üzenetek küldése érdekében, amelyekben a tulajdonság egy Service Bus `temperatureAlert` `true` végpontra. Ezután beállít egy logikai alkalmazást, amely a végpontra érkező Service Bus aktiválódik, és e-mailes értesítést küld Önnek.

## <a name="prerequisites"></a>Előfeltételek

* Töltse ki a [Raspberry Pi online szimulátor oktatóanyagát](iot-hub-raspberry-pi-web-simulator-get-started.md) vagy az eszköz egyik oktatóanyagát. A [Raspberry Pi-hoz ](iot-hub-raspberry-pi-kit-node-get-started.md) például használhatja a node.jsvagy a [Telemetria](quickstart-send-telemetry-dotnet.md) küldése gyorsútmutatók valamelyikét. Ezek a cikkek a következő követelményeket fedik le:

  * Aktív Azure-előfizetés.
  * Egy Azure IoT Hub az előfizetés alatt.
  * Az eszközön futó ügyfélalkalmazás, amely telemetriai üzeneteket küld az Azure IoT Hubnak.

## <a name="create-service-bus-namespace-and-queue"></a>Névtér Service Bus üzenetsor létrehozása

Hozzon létre egy Service Bus-névteret és üzenetsort. A témakör későbbi felében létre fog hozni egy útválasztási szabályt az IoT Hubban, amely a hőmérsékleti riasztást tartalmazó üzeneteket az Service Bus-üzenetsorba irányítja, ahol egy logikai alkalmazás fogja fogadni őket, és elindítja azt egy értesítő e-mail küldésére.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

1. A [Azure Portal](https://portal.azure.com/)válassza **a + Erőforrás-integráció létrehozása lehetőséget**  >    >  **Service Bus.**

1. A **Névtér létrehozása panelen** adja meg a következő információkat:

   **Név:** A Service Bus-névtér neve. A névtérnek egyedinek kell lennie az Azure-ban.

   **Tarifacsomag:** Válassza az **Alapszintű** lehetőséget a legördülő listából. Az alapszintű csomag elegendő ehhez az oktatóanyaghoz.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, mint az IoT Hub.

   **Hely:** Használja ugyanazt a helyet, mint az IoT Hub.

   ![Service Bus-névtér létrehozása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Válassza a **Létrehozás** lehetőséget. Várja meg, amíg az üzembe helyezés befejeződik, mielőtt továbblép a következő lépésre.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Új Service Bus hozzáadása a névtérhez

1. Nyissa meg Service Bus névteret. A Service Bus úgy jut el a legegyszerűbben,  ha kiválasztja az Erőforráscsoportok lehetőséget az erőforráspanelen, kiválasztja az erőforráscsoportot, majd Service Bus a névteret az erőforrások listájából.

1. A **Névtér Service Bus** válassza a **+ Üzenetsor lehetőséget.**

1. Adja meg az üzenetsor nevét, majd válassza a **Létrehozás lehetőséget.** Az üzenetsor sikeres létrehozása után az **Üzenetsor** létrehozása panel bezárul.

   ![Service Bus-üzenetsor hozzáadása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. A Névtér **Service Bus az** **Entitások** alatt válassza az **Üzenetsorok lehetőséget.** Nyissa meg Service Bus üzenetsort a listából, majd válassza a **Megosztott hozzáférési**  >  **szabályzatok + Hozzáadás lehetőséget.**

1. Adja meg a szabályzat nevét, jelölje be a **Kezelés jelölőnégyzetet,** majd válassza a **Létrehozás lehetőséget.**

   ![Service Bus-üzenetsor-szabályzat hozzáadása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Egyéni végpont és útválasztási szabály hozzáadása az IoT Hubhoz

Adjon hozzá egy egyéni végpontot az Service Bus-üzenetsorhoz az IoT Hubhoz, és hozzon létre egy üzenet-útválasztási szabályt, amely az adott végpontra irányítja a hőmérsékleti riasztást tartalmazó üzeneteket, ahol a logikai alkalmazás fogja fogadni őket. Az útválasztási szabály egy útválasztási lekérdezést () használ az üzenetek az eszközön futó ügyfélkód által beállított alkalmazástulajdonság értéke alapján `temperatureAlert = "true"` `temperatureAlert` való továbbításhoz. További információ: [Üzenet-útválasztási lekérdezés üzenettulajdonságok alapján.](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)

### <a name="add-a-custom-endpoint"></a>Egyéni végpont hozzáadása

1. Nyissa meg az IoT Hubot. Az IoT Hubhoz úgy jut el  a legegyszerűbben, ha kiválasztja az Erőforráscsoportok lehetőséget az erőforráspanelen, kiválasztja az erőforráscsoportot, majd kiválasztja az IoT Hubot az erőforrások listájából.

1. Az **Üzenetkezelés alatt** válassza az **Üzenetirányítás lehetőséget.** Az **Üzenetirányítás panelen** válassza az Egyéni **végpontok** lapot, majd a **+ Hozzáadás lehetőséget.** A legördülő listában válassza a **Service Bus-üzenetsor lehetőséget.**

   ![Képernyőkép a Service Bus-üzenetsor beállításról.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. A **Service Bus-végpont hozzáadása panelen** adja meg a következő adatokat:

   **Végpont neve:** A végpont neve.

   **Service Bus-névtér:** Válassza ki a létrehozott névteret.

   **Service Bus-üzenetsor:** Válassza ki a létrehozott üzenetsort.

   ![Adjon hozzá egy végpontot az IoT Hubhoz a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Válassza a **Létrehozás** lehetőséget. A végpont sikeres létrehozása után folytassa a következő lépéssel.

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

1. Az Üzenetirányítás **panelre** visszatérve válassza az **Útvonalak lapot,** majd a **+ Hozzáadás lehetőséget.**

1. Az Útvonal **hozzáadása panelen** adja meg a következő adatokat:

   **Név:** Az útválasztási szabály neve.

   **Végpont:** Válassza ki a létrehozott végpontot.

   **Adatforrás:** Válassza az **Eszköz telemetriai üzenetei lehetőséget.**

   **Útválasztási lekérdezés:** Írja be a következőt: `temperatureAlert = "true"` .

   ![Útválasztási szabály hozzáadása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Kattintson a **Mentés** gombra. Bezárhatja az **Üzenetirányítás panelt.**

## <a name="create-and-configure-a-logic-app"></a>Logikai alkalmazás létrehozása és konfigurálása

Az előző szakaszban úgy beállította az IoT Hubot, hogy egy hőmérsékleti riasztást tartalmazó üzeneteket irányítson a Service Bus üzenetsorába. Most be kell állítania egy logikai alkalmazást, amely figyeli a Service Bus üzenetsort, és e-mailes értesítést küld, amikor üzenetet adnak hozzá az üzenetsorhoz.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Válassza **az Erőforrás-integrációs logikai**  >  **alkalmazás** létrehozása  >  **lehetőséget.**

1. Adja meg a következő információkat:

   **Név:** A logikai alkalmazás neve.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, mint az IoT Hub.

   **Hely:** Használja ugyanazt a helyet, mint az IoT Hub.

   ![Logikai alkalmazás létrehozása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-the-logic-app-trigger"></a>A logikai alkalmazás eseményindítójának konfigurálása

1. Nyissa meg a logikai alkalmazást. A logikai alkalmazás legegyszerűbben úgy megjelenik, ha kiválasztja az Erőforráscsoportok lehetőséget az erőforráspanelen, kiválasztja az erőforráscsoportot, majd kiválasztja a logikai alkalmazást az erőforrások listájából.  Amikor kiválasztja a logikai alkalmazást, megnyílik Logic Apps Designer.

1. A Logic Apps Designerben görgessen le a **Sablonok részhez,** és válassza az **Üres logikai alkalmazás lehetőséget.**

   ![Kezdjen egy üres logikai alkalmazással a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Válassza az **Összes lapot,** majd a **Service Bus.**

   ![Válassza Service Bus lehetőséget a logikai alkalmazás létrehozásához a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. A **Triggers (Eseményindítók)** alatt válassza az **When one or more messages arrive in a queue (auto-complete) (Egy** vagy több üzenet üzenetsorba érkezésekor (automatikus kitöltés) lehetőséget.

   ![Válassza ki a logikai alkalmazás eseményindítóját a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus-kapcsolat létrehozása.
   1. Adja meg a kapcsolat nevét, és Service Bus a listából a névteret. Megnyílik a következő képernyő.

      ![Képernyőkép az Üzenetsorba érkező egy vagy több üzenet érkezésekor (automatikus kitöltés) lehetőségről.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Válassza ki a Service Bus-szabályzatot (RootManageSharedAccessKey). Ezután válassza a **Létrehozás lehetőséget.**

      ![Service Bus-kapcsolat létrehozása a logikai alkalmazáshoz a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Az utolsó képernyőn az Üzenetsor **neve mezőben** válassza ki a létrehozott üzenetsort a legördülő menüből. A Maximális `175` **üzenetszámhoz adja meg a következőt:**.

      ![A Service Bus-kapcsolat maximális üzenetszámának megadása a logikai alkalmazásban](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. A **módosítások** mentéshez válassza a Mentés lehetőséget a Logic Apps tetején található menüben.

### <a name="configure-the-logic-app-action"></a>A logikai alkalmazás műveletének konfigurálása

1. SMTP-szolgáltatáskapcsolat létrehozása.

   1. Válassza az **Új lépés** lehetőséget. A **Művelet kiválasztása lapon** válassza az Összes **lapot.**

   1. Írja be a keresőmezőbe a következőt: , válassza ki az SMTP szolgáltatást a `smtp` keresési eredményben, majd válassza az **E-mail küldése lehetőséget.** 

      ![SMTP-kapcsolat létrehozása a logikai alkalmazásban a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Adja meg a postaláda SMTP-adatait, majd válassza a **Létrehozás lehetőséget.**

      ![Adja meg az SMTP-kapcsolat adatait a logikai alkalmazásban a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Szerezze be a [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)és [Yahoo Mail SMTP-adatait.](https://help.yahoo.com/kb/SLN4075.html)

      > [!NOTE]
      > Előfordulhat, hogy le kell tiltania a TLS/SSL-t a kapcsolat létesítéshez. Ha ez a helyzet, és újra engedélyezni szeretné a TLS-t a kapcsolat létrejötte után, tekintse meg a szakasz végén található választható lépést.

   1. Az **E-mail** küldése lépés Új paraméter hozzáadása legördülő menüjéből **válassza** a **From**, **To**, **Subject** és **Body lehetőséget.** Kattintson vagy koppintson a képernyőn bárhol a kijelölési mezőhöz.

      ![Smtp-kapcsolat e-mail-mezőinek kiválasztása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Adja meg a From és **a** To e-mail-címét, valamint a Tárgy és a Törzs  `High temperature detected` **e-mail-címét.**  Ha **megnyílik az Add dynamic content from the apps and connectors** used in this flow (Dinamikus tartalom hozzáadása a folyamathoz használt alkalmazásokból és összekötőkből) párbeszédpanel, válassza az **Elrejtés** lehetőséget a bezárásához. Ebben az oktatóanyagban nem használ dinamikus tartalmat.

      ![Az SMTP-kapcsolat e-mail-mezőinek kitöltése](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Az **SMTP-kapcsolat** mentéshez válassza a Mentés lehetőséget.

1. (Nem kötelező) Ha le kellett tiltania a TLS-t, hogy kapcsolatot létesítsen az e-mail-szolgáltatóval, és újra engedélyezni szeretné, kövesse az alábbi lépéseket:

   1. A Logikai **alkalmazás panel fejlesztői** eszközök területén **válassza** az **API-kapcsolatok lehetőséget.**

   1. Az API-kapcsolatok listájából válassza ki az SMTP-kapcsolatot.

   1. Az **smtp API Connection (SMTP API-kapcsolat) panel** **General (Általános) alatt válassza** az Edit API connection **(API-kapcsolat szerkesztése) lehetőséget.**

   1. Az **API-kapcsolat szerkesztése panelen** válassza az SSL engedélyezése **lehetőséget,** írja be újra az e-mail-fiók jelszavát, majd válassza a **Mentés lehetőséget.**

      ![Szerkessze az SMTP API-kapcsolatot a logikai alkalmazásban a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A logikai alkalmazás most már készen áll a hőmérsékleti riasztások feldolgozására a Service Bus üzenetsorból, és értesítéseket küld az e-mail-fiókjába.

## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése

1. Indítsa el az ügyfélalkalmazást az eszközön.

1. Fizikai eszköz használata esetén körültekintően vigyen egy hőforrást az hőérzékelőhöz, amíg a hőmérséklet meghaladja a 30 C fokot. Ha az online szimulátort használja, az ügyfélkód véletlenszerűen a 30 C-t meghaladó telemetriai üzeneteket fog kiadni.

1. Meg kell kezdenie a logikai alkalmazás által küldött e-mail-értesítések fogadását.

   > [!NOTE]
   > Előfordulhat, hogy az e-mail-szolgáltatónak ellenőriznie kell a küldő identitását, hogy biztosan Ön küldje el az e-mailt.

## <a name="next-steps"></a>Következő lépések

Sikeresen létrehozott egy logikai alkalmazást, amely összekapcsolja az IoT Hubot és a postaládát a hőmérséklet monitorozása és az értesítések érdekében.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]