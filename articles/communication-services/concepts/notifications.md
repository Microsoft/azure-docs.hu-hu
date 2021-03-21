---
title: Értesítések az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Értesítéseket küldhet az Azure kommunikációs szolgáltatásokra épülő alkalmazások felhasználói számára.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 21c247c2fb9e1532c4580eea894c97e2bff84f92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657042"
---
# <a name="communication-services-notifications"></a>Kommunikációs szolgáltatások értesítései

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Az Azure kommunikációs szolgáltatások csevegési és Meghívási ügyfélszoftvere olyan valós idejű üzenetkezelési csatornát hoz létre, amely lehetővé teszi, hogy a rendszer hatékony, megbízható módon továbbítsa az üzeneteket a csatlakoztatott ügyfeleknek. Ez lehetővé teszi, hogy a bonyolult HTTP-lekérdezési logika megvalósítása nélkül hozzon létre gazdag és valós idejű kommunikációs funkciókat az alkalmazásaiba. A mobil alkalmazásokban azonban ez a jelző csatorna csak akkor marad csatlakoztatva, ha az alkalmazás az előtérben van aktív. Ha azt szeretné, hogy a felhasználók fogadják a bejövő hívásokat vagy csevegési üzeneteket, amíg az alkalmazása a háttérben van, akkor leküldéses értesítéseket kell használnia.

A leküldéses értesítések segítségével adatokat küldhet az alkalmazásból a felhasználók mobileszközökön. Leküldéses értesítések segítségével megjelenítheti a párbeszédpanelt, hanglejátszást készíthet, vagy megjelenítheti a bejövő hívás felhasználói felületét. Az Azure kommunikációs szolgáltatásai [Azure Event Grid](../../event-grid/overview.md) -és Azure- [Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) integrációs szolgáltatásokat biztosítanak, amelyek lehetővé teszik leküldéses értesítések hozzáadását az alkalmazásokba.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Leküldéses értesítések indítása Azure Event Grid használatával

Az Azure kommunikációs szolgáltatásai megbízható, méretezhető és biztonságos módon biztosítják a valós idejű események bejelentésének [Azure Event Gridét](https://azure.microsoft.com/services/event-grid/) . Ezt az integrációt kihasználva olyan értesítési szolgáltatást hozhat létre, amely mobil leküldéses értesítéseket továbbít a felhasználóknak egy Azure- [függvényt](../../azure-functions/functions-overview.md) vagy webhookot indító Event Grid-előfizetés létrehozásával.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Ábra, amely bemutatja, hogyan integrálódik a kommunikációs szolgáltatások a Event Grid.":::

További információ az [Azure kommunikációs szolgáltatásokban zajló események kezelésére](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Leküldéses értesítések kézbesítése az Azure Notification Hubs használatával

Csatlakoztathat egy Azure Notification hub-t a kommunikációs szolgáltatások erőforrásához, hogy automatikusan küldjön leküldéses értesítéseket a felhasználó mobileszközön, amikor bejövő hívást kapnak. Ezeknek a leküldéses értesítéseknek a használatával felébresztheti az alkalmazást a háttérben, és megjelenítheti a felhasználói FELÜLETET, amely lehetővé teszi a felhasználó számára a hívás elfogadását vagy elutasítását.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Ábra, amely bemutatja, hogyan integrálódik a kommunikációs szolgáltatások az Azure Notification Hubs.":::

A kommunikációs szolgáltatások az Azure Notification hub-t áteresztő szolgáltatásként használják a platform-specifikus leküldéses értesítési szolgáltatásokkal való kommunikációhoz a [Direct Send](/rest/api/notificationhubs/direct-send) API használatával. Ez lehetővé teszi a meglévő Azure Notification hub-erőforrások és-konfigurációk újrafelhasználását, hogy alacsony késésű, megbízható hívási értesítéseket nyújtson az alkalmazásaihoz.

> [!NOTE]
> Jelenleg csak a leküldéses értesítések hívása támogatott.

### <a name="notification-hub-provisioning"></a>Értesítési központ kiépítés

Ha Notification Hubs használatával szeretne leküldéses értesítéseket küldeni az ügyféleszközök számára, [hozzon létre egy értesítési](../../notification-hubs/create-notification-hub-portal.md) központot a kommunikációs szolgáltatások erőforrásával megegyező előfizetésen belül. Konfigurálnia kell az Azure Notification hub-t a használni kívánt Platform Notification Systemhoz. Ha meg szeretné tudni, hogyan kérhet le leküldéses értesítéseket az ügyfélalkalmazás Notification Hubsről, tekintse meg a [Notification Hubs első lépéseivel foglalkozó](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) témakört, és válassza ki a cél ügyféloldali platformot a lap tetején található legördülő listából.

> [!NOTE]
> Jelenleg a APNs és az FCM platform támogatott.
A APNs platformot jogkivonat-hitelesítési móddal kell konfigurálni. A tanúsítvány-hitelesítési mód jelenleg nem támogatott.

Miután konfigurálta az értesítési központot, hozzárendelheti a kommunikációs szolgáltatások erőforrásához úgy, hogy a Azure Resource Manager ügyfelet vagy a Azure Portalon keresztül a hub kapcsolati karakterláncát adja meg. A kapcsolatok karakterláncának engedélyeket kell tartalmaznia `Send` . Javasoljuk, hogy hozzon létre egy másik hozzáférési szabályzatot, amely csak az Ön központjának `Send` megfelelő engedélyekkel rendelkezik. További információ a [Notification Hubs biztonsági és hozzáférési szabályzatokról](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Az értesítési központ csatolása az Azure Resource Manager-ügyféllel

Az Azure Resource Managerba való bejelentkezéshez hajtsa végre a következőket, és jelentkezzen be a hitelesítő adataival.

```console
armclient login
```

 Miután sikeresen bejelentkezett, futtassa a következőt az értesítési központ kiépítéséhez:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Az értesítési központ csatolása a Azure Portal használatával

A portálon navigáljon az Azure kommunikációs szolgáltatások erőforrásaihoz. A kommunikációs szolgáltatások erőforráson belül válassza a kommunikációs szolgáltatások lap bal oldali menüjének leküldéses értesítések elemét, és kapcsolja össze a korábban kiépített értesítési központot. Itt meg kell adnia a kapcsolódási karakterláncot és a resourceId:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="A leküldéses értesítések beállításait ábrázoló képernyőkép a Azure Portalon belül.":::

> [!NOTE]
> Ha az Azure Notification hub kapcsolati karakterláncát frissíti, a kommunikációs szolgáltatások erőforrását is frissíteni kell.
A hub összekapcsolásának minden változása az adatsíkon (azaz egy értesítés küldésekor) jelenik meg, legfeljebb ``10`` percen belül. Ez akkor is érvényes, ha a hub első alkalommal van társítva, **Ha** az értesítéseket korábban küldték.

### <a name="device-registration"></a>Eszközregisztráció

Tekintse át a [hanghívási](../quickstarts/voice-video-calling/getting-started-with-calling.md) rövid útmutatót, amelyből megtudhatja, hogyan regisztrálja az eszközt a kommunikációs szolgáltatásokkal.

### <a name="troubleshooting-guide-for-push-notifications"></a>Hibaelhárítási útmutató leküldéses értesítésekhez

Ha nem látja a leküldéses értesítéseket az eszközön, három helyen lehet eldobni az értesítéseket:

- Az Azure Notification Hubs nem fogadta el az Azure kommunikációs szolgáltatások értesítését
- A Platform Notification System (például APNs és FCM) nem fogadta el az Azure-beli értesítést Notification Hubs
- A Platform Notification System nem kézbesítte az értesítést az eszköznek.

Az értesítés eldobásának első helye (az Azure Notification Hubs nem fogadta el az Azure kommunikációs szolgáltatásokból érkező értesítéseket) alább látható. A másik két helyen az [eldobott értesítések diagnosztizálása az Azure Notification Hubs-ban](../../notification-hubs/notification-hubs-push-notification-fixer.md)című témakörben talál további információt.

Az egyik lehetőség, hogy megnézze, hogy a kommunikációs szolgáltatások erőforrása küld-e értesítéseket az Azure Notification Hubs számára, hogy megtekinti a `incoming messages` kapcsolódó [Azure Notification hub-metrikák](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs)metrikáját.

Az alábbiakban néhány gyakori helytelen konfigurációt lehet okozni, amelyek miatt az Azure Notification hub nem fogadja el a kommunikációs szolgáltatások erőforrásaitól érkező értesítéseket.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Az Azure Notification hub nem kapcsolódik a kommunikációs szolgáltatások erőforrásához

Előfordulhat, hogy nem kapcsolta össze az Azure Notification hub-t a kommunikációs szolgáltatások erőforrásával. Tekintse meg az [értesítési központ kiépítési szakaszát](#notification-hub-provisioning) , ahol megtudhatja, hogyan kapcsolhatja össze őket.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>Nincs konfigurálva a csatolt Azure Notification hub

A társított értesítési központot a használni kívánt platform (például iOS vagy Android) Platform Notification System hitelesítő adataival kell konfigurálni. További információt a [leküldéses értesítések beállítása egy értesítési központban](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)című témakörben talál.

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>A csatolt Azure Notification hub nem létezik

A kommunikációs szolgáltatások erőforrásához kapcsolódó Azure Notification hub már nem létezik. Győződjön meg arról, hogy a csatolt értesítési központ továbbra is létezik.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Az Azure Notification hub APNs platform tanúsítvány-hitelesítési móddal van konfigurálva

Ha a APNs platformot tanúsítvány-hitelesítési móddal szeretné használni, az jelenleg nem támogatott. A APNs platformot a jogkivonat-hitelesítési móddal kell konfigurálnia az [értesítési központ leküldéses értesítéseinek beállítása](../../notification-hubs/configure-notification-hub-portal-pns-settings.md)beállításban megadott módon.

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>A csatolt kapcsolati sztringnek nincs `Send` engedélye

Az értesítési központ és a kommunikációs szolgáltatások erőforrásának összekapcsolásához használt kapcsolati karakterláncnak rendelkeznie kell `Send` engedéllyel. Ha további információra van szüksége arról, hogyan hozhat létre új kapcsolati karakterláncot, vagy hogyan tekintheti meg az Azure Notification hub aktuális kapcsolati karakterláncát, tekintse meg [Notification Hubs biztonsági és hozzáférési házirendeket](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>A csatolt kapcsolati sztring vagy az Azure Notification hub resourceId érvénytelenek

Győződjön meg arról, hogy a kommunikációs szolgáltatások erőforrását a megfelelő kapcsolati karakterlánccal és az Azure Notification hub resourceId konfigurálja

#### <a name="the-linked-connection-string-is-regenerated"></a>A csatolt kapcsolati sztring újragenerált

Ha újragenerálta a csatolt Azure Notification hub kapcsolati karakterláncát, a kapcsolati karakterláncot a kommunikációs szolgáltatások erőforrásában lévő új értékkel kell frissítenie az [értesítési központ újracsatolásával](#notification-hub-provisioning).

## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](../../event-grid/overview.md)
* Ha többet szeretne megtudni az Azure Notification hub-fogalmakról, tekintse meg az [azure Notification Hubs dokumentációját](../../notification-hubs/index.yml) .
