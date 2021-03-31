---
title: IoT Hub létrehozása a Azure Portal használatával | Microsoft Docs
description: Azure IoT-hubok létrehozása, kezelése és törlése az Azure Portalon keresztül. Az árképzési szintekkel, a skálázással, a biztonsággal és az üzenetkezelési konfigurációval kapcsolatos információkat tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536045"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>IoT hub létrehozása a Azure Portal használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet IoT-hubokat a [Azure Portal](https://portal.azure.com)használatával.

Az oktatóanyag lépéseinek használatához Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>Az IoT hub beállításainak módosítása

A meglévő IoT hub beállításait a IoT Hub panelből való létrehozása után módosíthatja.

![Az IoT hub beállításait ábrázoló képernyőfelvétel](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Íme néhány, a IoT hub számára beállítható tulajdonság:

**Díjszabás és méretezés**: ezt a tulajdonságot használhatja egy másik szintjére való Migrálás vagy a IoT hub egységek számának megadására. 

**Műveletek figyelése**: a különböző figyelési kategóriákat be-vagy kikapcsolhatja, például az eszközről a felhőbe irányuló üzenetekkel vagy a felhőből az eszközre irányuló üzenetekkel kapcsolatos események naplózása.

**IP-szűrő**: olyan IP-címek tartományát határozza meg, amelyeket az IoT hub elfogad vagy visszautasít.

**Tulajdonságok**: megjeleníti a máshol másolható és használható tulajdonságok listáját, például az erőforrás-azonosítót, az erőforráscsoportot, a helyet és így tovább.

### <a name="shared-access-policies"></a>Megosztott elérési szabályzatok

A megosztott hozzáférési házirendek listáját a **Beállítások** szakaszban található **megosztott hozzáférési házirendek** lehetőségre kattintva is megtekintheti vagy módosíthatja. Ezek a szabályzatok határozzák meg az eszközök és szolgáltatások IoT Hubhoz való kapcsolódásának engedélyeit. 

Kattintson a **Hozzáadás** gombra a **közös hozzáférési szabályzat hozzáadása** panel megnyitásához.  Megadhatja az új szabályzat nevét és az ehhez a Szabályzathoz társítandó engedélyeket az alábbi ábrán látható módon:

![Megosztott hozzáférési szabályzat hozzáadását bemutató képernyőkép](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* A **beállításjegyzék olvasási** és **beállításjegyzék-írási** házirendjei olvasási és írási hozzáférési jogokat biztosítanak az identitás-nyilvántartónak. Ezeket az engedélyeket a háttérbeli felhőalapú szolgáltatások használják az eszközök identitásának kezeléséhez. Az írási beállítás választása esetén a rendszer automatikusan kiválasztja az olvasási lehetőséget.

* A **szolgáltatás-csatlakozási** házirend engedélyezi a szolgáltatási végpontok elérését. Ezt az engedélyt a háttérbeli felhőalapú szolgáltatások használják az eszközökről érkező üzenetek küldésére és fogadására, valamint az eszköz Twin és a modul Twin adatainak frissítésére és olvasására.

* Az **eszköz csatlakoztatási** házirendje engedélyeket biztosít az üzenetek küldéséhez és fogadásához az IoT hub az eszköz-oldalsó végpontok használatával. Ezt az engedélyt az eszközök használják az IoT hub üzeneteinek küldésére és fogadására, az eszközök Twin-és modul-különálló adatainak frissítésére és olvasására, valamint a fájlfeltöltés végrehajtására.

Kattintson a **Létrehozás** gombra az újonnan létrehozott szabályzat meglévő listához való hozzáadásához.

További információ az adott engedélyek által biztosított hozzáférésről: [IoT hub engedélyek](./iot-hub-devguide-security.md#iot-hub-permissions).

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>IoT hub üzenet-útválasztása

Kattintson az üzenetküldés lehetőségre az **üzenetkezelés** **területen az üzenet-** útválasztási ablaktábla megjelenítéséhez, ahol útvonalakat és egyéni végpontokat határozhat meg a hubhoz. Az [üzenet-útválasztás](iot-hub-devguide-messages-d2c.md) segítségével kezelheti az eszközökről a végpontokra küldött adatok küldésének módját. Az első lépés egy új útvonal hozzáadása. Ezután hozzáadhat egy meglévő végpontot az útvonalhoz, vagy létrehozhat egy új támogatott típust, például a blob Storage-ot. 

![Üzenet-útválasztási ablaktábla](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Útvonalak

Az útvonalak az üzenet-útválasztási ablaktábla első lapja. Új útvonal hozzáadásához kattintson a +**Hozzáadás** gombra. A következő képernyő jelenik meg. 

![Új útvonal hozzáadását bemutató képernyőkép](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Nevezze el az útvonalat. Az útvonal nevének egyedinek kell lennie az adott hubhoz tartozó útvonalak listáján belül. 

A **végpont** esetében választhat egyet a legördülő listából, vagy hozzáadhat egy újat. Ebben a példában a Storage-fiók és a tároló már elérhető. Ha végpontként szeretné hozzáadni őket, kattintson a +**Hozzáadás** elemre a végpont legördülő menüben, majd válassza a **blob Storage** lehetőséget. Az alábbi képernyőn látható, hol vannak megadva a Storage-fiók és a tároló.

![A tárolási végpontok útválasztási szabályhoz való hozzáadását bemutató képernyőkép](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

A Storage-fiók és a tároló kiválasztásához kattintson **a tároló** kiválasztása elemre. Ha kiválasztotta ezeket a mezőket, a végpont ablaktáblára tér vissza. Használja az alapértelmezett értékeket a többi mezőhöz, és **hozzon** létre egy végpontot a Storage-fiókhoz, és adja hozzá az útválasztási szabályokhoz.

Az **adatforrás** területen válassza az eszközök telemetria-üzenetek lehetőséget. 

Ezután adjon hozzá egy útválasztási lekérdezést. Ebben a példában azokat az üzeneteket, amelyekben a nevű alkalmazás tulajdonsága `level` megegyezik a Storage-fiókkal, egy értékkel kell elnevezni `critical` .

![Új útválasztási szabály mentését bemutató képernyőkép](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Az útválasztási szabály mentéséhez kattintson a **Save (Mentés** ) gombra. Visszatér az üzenet-útválasztási panelre, és megjelenik az új útválasztási szabály.

### <a name="custom-endpoints"></a>Egyéni végpontok

Kattintson az **Egyéni végpontok** fülre. Megjelenik a már létrehozott egyéni végpontok. Innen új végpontokat adhat hozzá, vagy törölheti a meglévő végpontokat. 

> [!NOTE]
> Ha töröl egy útvonalat, nem törli az adott útvonalhoz rendelt végpontokat. Végpont törléséhez kattintson az egyéni végpontok lapra, válassza ki a törölni kívánt végpontot, és kattintson a Törlés gombra.
>

További információt az egyéni végpontokról a [IoT hub-végpontok](iot-hub-devguide-endpoints.md)című cikk tartalmaz.

Egy IoT hubhoz legfeljebb 10 egyéni végpontot adhat meg. 

Ha szeretné megtekinteni, hogyan használhatók az egyéni végpontok az útválasztással, tekintse meg a következőt: [üzenetküldés az IoT hub](tutorial-routing.md)használatával.

## <a name="find-a-specific-iot-hub"></a>Egy adott IoT hub keresése

Az alábbi két módon találhat egy adott IoT hubot az előfizetésében:

1. Ha ismeri azt az erőforráscsoportot, amelyhez az IoT hub tartozik, kattintson az **erőforráscsoportok** elemre, majd válassza ki az erőforráscsoportot a listából. Az erőforráscsoport képernyő megjeleníti a csoport összes erőforrását, beleértve a IoT hubokat is. Kattintson arra a hubhoz, amelyre keres.

2. Kattintson a **Minden erőforrás** elemre. A **minden erőforrás** panelen található egy legördülő lista, amely alapértelmezés szerint a következőt tartalmazza: `All types` . Kattintson a legördülő listára, és törölje a jelölést `Select all` . Keresse `IoT Hub` meg és ellenőrizze. Kattintson a legördülő listára a bezárásához, és a bejegyzések szűrve lesznek, és csak a IoT hubok láthatók.

## <a name="delete-the-iot-hub"></a>Az IoT hub törlése

IOT hub törléséhez keresse meg a törölni kívánt IoT hub-t, majd kattintson a IoT hub neve alatti **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve további információkat tudhat meg az Azure IoT Hub kezeléséről:

* [Üzenet-útválasztás IoT Hub](tutorial-routing.md)
* [Az IoT hub figyelése](monitor-iot-hub.md)
