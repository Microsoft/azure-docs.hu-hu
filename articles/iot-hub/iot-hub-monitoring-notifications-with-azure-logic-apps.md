---
title: IoT távoli figyelés és értesítések a Azure Logic Appskal | Microsoft Docs
description: A IoT hub IoT hőmérséklet-figyeléséhez használja a Azure Logic Appst, és a rendszer automatikusan e-mail-értesítéseket küld a postaládának az észlelt rendellenességekről.
author: robinsh
keywords: IOT-figyelés, IOT értesítések, IOT hőmérséklet-figyelés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: cd14ff0688f4230aeedac748ca4b32609bdd2938
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92490322"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT távoli figyelés és értesítések Azure Logic Apps az IoT hub és a postaláda csatlakoztatásával

![Végpontok közötti diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

A [Azure Logic apps](../logic-apps/index.yml) segítséget nyújt a munkafolyamatok a helyszíni és a felhőalapú szolgáltatások, egy vagy több vállalat és különböző protokollok közötti összehangolása során. A logikai alkalmazások egy triggerrel kezdődnek, amelyet aztán egy vagy több olyan művelet követ, amely a beépített vezérlőkkel, például a feltételekkel és az iterációkkal végezhető el. Ez a rugalmasság a IoT-figyelési forgatókönyvek ideális IoT-megoldását teszi Logic Apps. Például egy IoT Hub végponton lévő eszközről érkező telemetria adatok beérkezése esetén a Logic app-munkafolyamatok elindíthatják az Azure Storage-blobokban tárolt adatok tárházát, e-mailes riasztásokat küldhetnek az adatrendellenességek figyelmeztetésére, ha az eszköz hibát jelez, és így tovább.

## <a name="what-you-learn"></a>Ismertetett témák

Megtudhatja, hogyan hozhat létre egy logikai alkalmazást, amely összekapcsolja az IoT hubot és a postaládáját a hőmérséklet-figyeléshez és az értesítésekhez.

Az eszközön futó-ügyfél kódja egy Application tulajdonságot állít be `temperatureAlert` , amely minden telemetria üzenetet küld az IoT hubhoz. Ha az ügyfél kódja 30 °C feletti hőmérsékletet észlel, a tulajdonságot a következőre állítja be: `true` . egyéb esetben a tulajdonságot a értékre állítja be `false` .

Az IoT hub-ra érkező üzenetek a következőhöz hasonlóan néz ki: a törzsben található telemetria-és az `temperatureAlert` alkalmazás tulajdonságaiban található tulajdonság (a Rendszertulajdonságok nem jelennek meg):

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

Az IoT Hub üzenet formátumával kapcsolatos további tudnivalókért tekintse meg [IoT hub üzenetek létrehozása és olvasása](iot-hub-devguide-messages-construct.md)című témakört.

Ebben a témakörben az IoT hub útválasztását úgy állíthatja be, hogy olyan üzeneteket küldjön, amelyekben a `temperatureAlert` tulajdonság `true` egy Service Bus végpont. Ezután beállíthat egy logikai alkalmazást, amely elindítja a Service Bus végpontra érkező üzeneteket, és e-mailben értesítést küld Önnek.

## <a name="what-you-do"></a>Teendők

* Hozzon létre egy Service Bus névteret, és adjon hozzá egy Service Bus-várólistát.
* Vegyen fel egy egyéni végpontot és egy útválasztási szabályt az IoT hubhoz, hogy átirányítsa a hőmérsékleti riasztást tartalmazó üzeneteket az Service Bus üzenetsor felé.
* Logikai alkalmazás létrehozása, konfigurálása és tesztelése a Service Bus üzenetsor üzeneteinek felhasználásához, valamint értesítési e-mailek küldése a kívánt címzettnek.

## <a name="what-you-need"></a>Amire szükség lesz

* Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például a [málna PI és a node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek az alábbi követelményekre vonatkoznak:

  * Aktív Azure-előfizetés.
  * Az előfizetéshez tartozó Azure IoT hub.
  * Az eszközön futó ügyfélalkalmazás, amely telemetria üzeneteket küld az Azure IoT hub-nak.

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus névtér és üzenetsor létrehozása

Hozzon létre egy Service Bus-névteret és üzenetsort. A jelen témakör későbbi részében egy útválasztási szabályt hoz létre az IoT hub-ben, hogy olyan üzeneteket küldjön, amelyek hőmérsékleti riasztást tartalmaznak a Service Bus várólistára, ahol egy logikai alkalmazás felveszi őket, és elindít egy értesítő e-mailt.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

1. A [Azure Portal](https://portal.azure.com/)válassza az **+ erőforrás**-  >  **integrációs**  >  **Service Bus** létrehozása lehetőséget.

1. A **névtér létrehozása** panelen adja meg a következő adatokat:

   **Name (név**): a Service Bus-névtér neve. A névtérnek egyedinek kell lennie az Azure-ban.

   **Árképzési szint**: válassza az **alapszintű** lehetőséget a legördülő listából. Az alapszintű csomag elegendő ehhez az oktatóanyaghoz.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely**: használja ugyanazt a helyet, amelyet az IoT hub használ.

   ![Service Bus-névtér létrehozása a Azure Portalban](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Válassza a **Létrehozás** lehetőséget. Várjon, amíg a telepítés befejeződik, mielőtt továbblép a következő lépésre.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Service Bus üzenetsor hozzáadása a névtérhez

1. Nyissa meg a Service Bus névteret. A Service Bus névtér elérésének legegyszerűbb módja az **erőforráscsoport kiválasztása az erőforrás-** ablaktáblában, válassza ki az erőforráscsoportot, majd válassza ki a Service Bus névteret az erőforrások listájából.

1. A **Service Bus névtér** ablaktáblán válassza a **+ üzenetsor** lehetőséget.

1. Adja meg a várólista nevét, majd válassza a **Létrehozás** lehetőséget. A várólista sikeres létrehozása után a **várólista létrehozása** panel bezárul.

   ![Service Bus-várólista hozzáadása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Vissza a **Service Bus névtér** panel **entitások** területén válassza a **várólisták** elemet. Nyissa meg a Service Bus várólistát a listából, majd válassza a **megosztott elérési házirendek**  >  **+ Hozzáadás** lehetőséget.

1. Adja meg a házirend nevét, jelölje be a **kezelés**, majd a **Létrehozás** lehetőséget.

   ![Service Bus-várólista házirendjének hozzáadása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Egyéni végpont és útválasztási szabály hozzáadása az IoT hub-hoz

Vegyen fel egy egyéni végpontot a Service Bus üzenetsor számára az IoT hubhoz, és hozzon létre egy üzenet-útválasztási szabályt, amely az adott végpontra vonatkozó hőmérséklet-riasztást tartalmazó üzeneteket irányítja, ahol azokat a logikai alkalmazás fogja felvenni. Az útválasztási szabály útválasztási lekérdezést használ, az `temperatureAlert = "true"` üzenetek továbbításához az `temperatureAlert` eszközön futó ügyfélalkalmazás által beállított Application tulajdonság értéke alapján. További információ: üzenet- [útválasztási lekérdezés üzenet tulajdonságai alapján](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Egyéni végpont hozzáadása

1. Nyissa meg az IoT hubot. Az IoT hub beszerzésének legegyszerűbb módja az **erőforráscsoport kiválasztása az erőforrás-** ablaktáblában, kiválaszthatja az erőforráscsoportot, majd kiválaszthatja az IoT hubot az erőforrások listájából.

1. Az **üzenetkezelés** területen válassza az **üzenet-útválasztás** elemet. Az **üzenet-útválasztás** ablaktáblán válassza az **Egyéni végpontok** fület, majd válassza a **+ Hozzáadás** lehetőséget. A legördülő listában válassza ki a **Service Bus-várólista** elemet.

   ![Képernyőfelvétel: a Service Bus-üzenetsor kiemelése.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. A **Service Bus-végpont hozzáadása** panelen adja meg a következő adatokat:

   **Végpont neve**: a végpont neve.

   **Service Bus-névtér**: válassza ki a létrehozott névteret.

   **Service Bus-üzenetsor**: válassza ki a létrehozott várólistát.

   ![Adjon hozzá egy végpontot az IoT hub-hoz a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Válassza a **Létrehozás** lehetőséget. A végpont sikeres létrehozása után folytassa a következő lépéssel.

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

1. Vissza az **üzenet-útválasztási** ablaktáblán válassza az **útvonalak** fület, majd válassza a **+ Hozzáadás** lehetőséget.

1. Az **útvonal hozzáadása** panelen adja meg a következő adatokat:

   **Name (név**): az útválasztási szabály neve.

   **Endpoint (végpont**): válassza ki a létrehozott végpontot.

   **Adatforrás**: válassza az **eszköz telemetria üzenetei** lehetőséget.

   **Útválasztási lekérdezés**: írja be a értéket `temperatureAlert = "true"` .

   ![Útválasztási szabály hozzáadása a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Kattintson a **Mentés** gombra. Lezárhatja az **üzenet-útválasztási** ablaktáblát.

## <a name="create-and-configure-a-logic-app"></a>Logikai alkalmazás létrehozása és konfigurálása

Az előző szakaszban beállíthatja, hogy az IoT hub hőmérséklet-riasztást tartalmazó üzeneteket továbbítson az Service Bus-várólistára. Most beállíthatja, hogy egy logikai alkalmazás figyelje a Service Bus várólistát, és e-mail értesítést küldjön, amikor egy üzenet bekerül a várólistába.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Válassza **az erőforrás létrehozása**  >  **integrációs**  >  **logikai alkalmazás** lehetőséget.

1. Adja meg a következő információkat:

   **Name (név**): a logikai alkalmazás neve.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely**: használja ugyanazt a helyet, amelyet az IoT hub használ.

   ![Logikai alkalmazás létrehozása a Azure Portalban](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-the-logic-app-trigger"></a>A logikai alkalmazás triggerének konfigurálása

1. Nyissa meg a logikai alkalmazást. A logikai alkalmazás elérésének legegyszerűbb módja az **erőforráscsoport kiválasztása az erőforrás-** ablaktáblában, kiválaszthatja az erőforráscsoportot, majd kiválaszthatja a logikai alkalmazást az erőforrások listájából. Amikor kiválasztja a logikai alkalmazást, megnyílik a Logic Apps Designer.

1. A Logic Apps Designerben görgessen le a **sablonok** elemre, és válassza az **üres logikai alkalmazás** lehetőséget.

   ![Kezdje egy üres logikai alkalmazással a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Válassza az **összes** fület, majd válassza a **Service Bus** lehetőséget.

   ![Válassza a Service Bus lehetőséget a logikai alkalmazás létrehozásának megkezdéséhez a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Az **Eseményindítók** területen válassza ki, hogy **Mikor érkeznek meg egy vagy több üzenet egy várólistában (automatikusan befejeződött)**.

   ![Válassza ki a logikai alkalmazás triggerét a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Hozzon létre egy Service Bus-kapcsolatokat.
   1. Adja meg a kapcsolatok nevét, és válassza ki a Service Bus névteret a listából. Megnyílik a következő képernyő.

      ![Képernyőkép, amely kiemeli, ha egy vagy több üzenet érkezik egy üzenetsor (automatikus Befejezés) beállítással.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Válassza ki a Service Bus-szabályzatot (RootManageSharedAccessKey). Ezután válassza a  **Létrehozás** lehetőséget.

      ![Service Bus-kapcsolatok létrehozása a logikai alkalmazáshoz a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Az utolsó képernyőn, a **várólista neve** mezőben válassza ki a legördülő listából létrehozott várólistát. Adja meg `175` az **üzenetek maximális száma** értéket.

      ![Adja meg a logikai alkalmazásban található Service Bus-kapcsolatok maximális üzeneteinek darabszámát](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. A módosítások mentéséhez kattintson a Logic Apps Designer tetején található menü **Mentés** elemére.

### <a name="configure-the-logic-app-action"></a>A logikai alkalmazás műveletének konfigurálása

1. Hozzon létre egy SMTP-szolgáltatási kapcsolatokat.

   1. Válassza az **Új lépés** lehetőséget. A **válasszon műveletet** területen válassza az **összes** fület.

   1. Írja be a keresőmezőbe a keresést `smtp` , válassza ki az **SMTP** szolgáltatást a keresési eredmények között, majd válassza az **e-mail küldése** lehetőséget.

      ![Hozzon létre egy SMTP-kapcsolatokat a logikai alkalmazásban a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Adja meg a postaláda SMTP-adatait, majd válassza a **Létrehozás** lehetőséget.

      ![Adja meg az SMTP-kapcsolatok adatait a logikai alkalmazásban a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Az SMTP-információk letöltése a [Hotmail/Outlook. com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), a [Gmail](https://support.google.com/a/answer/176600?hl=en)és a [Yahoo mail](https://help.yahoo.com/kb/SLN4075.html)szolgáltatáshoz.

      > [!NOTE]
      > Előfordulhat, hogy le kell tiltania a TLS/SSL-t a kapcsolat létrehozásához. Ha ez a helyzet áll fenn, és újra engedélyezni szeretné a TLS-t a kapcsolódás létrejötte után, tekintse meg a szakasz végén található opcionális lépést.

   1. Az **E-mail küldése** lépés **új paraméter hozzáadása** legördülő menüjéből válassza a **Feladó**, a, **a** **Tárgy** és a **törzs** lehetőséget. Kattintson vagy koppintson bárhová a képernyőn a kijelölési mező bezárásához.

      ![SMTP-kapcsolatok e-mail-mezőinek kiválasztása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Adja **meg az e** -mail-címét a és **a**, valamint `High temperature detected` a **tulajdonos** és a **törzs** számára. Ha megnyílik a **dinamikus tartalom hozzáadása az ebben a folyamatban használt alkalmazásokból és összekötőből** párbeszédpanel, válassza az **Elrejtés** elemet a bezáráshoz. Ebben az oktatóanyagban nem használ dinamikus tartalmat.

      ![Kitöltési SMTP-kapcsolatok e-mail-mezői](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Az SMTP-kapcsolatok mentéséhez válassza a **Mentés** lehetőséget.

1. Választható Ha le kellett tiltania a TLS-t, hogy kapcsolatot létesítsen az e-mail-szolgáltatóval, és újra engedélyezni szeretné, kövesse az alábbi lépéseket:

   1. A **logikai alkalmazás** ablaktábla **fejlesztői eszközök** területén válassza az API- **kapcsolatok** elemet.

   1. Az API-kapcsolatok listájából válassza ki az SMTP-kapcsolatot.

   1. Az **SMTP API-kapcsolatok** ablaktábla **általános** területén válassza az **API-kapcsolatok szerkesztése** lehetőséget.

   1. Az **API-kapcsolat szerkesztése** ablaktáblán válassza az **SSL engedélyezése** lehetőséget, majd írja be újra az e-mail fiókja jelszavát, és kattintson a **Mentés** gombra.

      ![Az SMTP API-kapcsolatok szerkesztése a logikai alkalmazásban a Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A logikai alkalmazás most már készen áll arra, hogy feldolgozza a hőmérsékleti riasztásokat a Service Busi sorból, és küldjön értesítéseket az e-mail fiókjába.

## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése

1. Indítsa el az ügyfélalkalmazás alkalmazást az eszközön.

1. Ha fizikai eszközt használ, körültekintően vigyen fel egy hőt a hő-érzékelő közelébe, amíg a hőmérséklet meghaladja a 30 °c-ot. Ha az online szimulátort használja, az ügyfél kódja véletlenszerűen kiírja a 30 C-ot meghaladó telemetria üzeneteket.

1. Kezdje el fogadni a logikai alkalmazás által küldött e-mail-értesítéseket.

   > [!NOTE]
   > Előfordulhat, hogy az e-mail-szolgáltatónak ellenőriznie kell a küldő identitását, hogy az Ön, aki elküldi az e-mailt.

## <a name="next-steps"></a>Következő lépések

Sikeresen létrehozott egy logikai alkalmazást, amely összekapcsolja az IoT hubot és a postaládáját a hőmérséklet-figyeléshez és az értesítésekhez.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]