---
title: Oktatóanyag – szabályok létrehozása és kezelése az Azure IoT Central-alkalmazásban
description: Ez az oktatóanyag azt mutatja be, hogy az Azure IoT Central-szabályok hogyan teszik lehetővé az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését a szabály indításakor.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b0b5aafd85fe6d992afa9d879f73ef0ec43e00d3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834374"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Oktatóanyag: Szabály létrehozása és értesítések beállítása az Azure IoT Central-alkalmazásban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az Azure IoT Central használatával távolról is figyelheti a csatlakoztatott eszközöket. Az Azure IoT Central-szabályok lehetővé teszik az eszközök közel valós idejű figyelését és a műveletek automatikus meghívását, például e-mailek küldését. Ez a cikk azt ismerteti, hogyan hozhatók létre szabályok az eszközök által küldött telemetria figyeléséhez.

Az eszközök a telemetria használatával numerikus adatok küldését az eszközről. Egy szabály akkor aktiválódik, ha a kiválasztott telemetria egy megadott küszöbértéket keresztez.

Ebben az oktatóanyagban létrehoz egy szabályt, amely e-mailt küld, ha egy szimulált érzékelő eszköz hőmérséklete meghaladja a 70 F-ot &deg; .

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Szabály létrehozása
> * E-mail művelet hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) és [egy szimulált eszköz hozzáadása a IoT Central alkalmazás](./quick-create-simulated-device.md) -gyors útmutatóhoz az **érzékelő vezérlő** -eszköz sablonjának létrehozásához.

## <a name="create-a-rule"></a>Szabály létrehozása

Telemetria szabály létrehozásához az eszköz sablonjának tartalmaznia kell legalább egy telemetria értéket. Ez az oktatóanyag egy szimulált **érzékelő vezérlő** eszközt használ, amely hőmérséklet-és páratartalom-telemetria küld. Hozzáadta ezt az eszközt, és létrehozott egy szimulált eszközt a [szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](./quick-create-simulated-device.md) című rövid útmutatóban. A szabály figyeli az eszköz által jelentett hőmérsékletet, és e-mailt küld, ha 70 fok fölé kerül.

> [!NOTE]
> Felhasználónként legfeljebb 50 szabályt lehet megszabni.

1. A bal oldali ablaktáblán válassza a **szabályok** lehetőséget.

1. Ha még nem hozott létre szabályokat, a következő képernyő jelenik meg:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="A szabályok üres listáját megjelenítő képernyőkép":::

1. Új szabály hozzáadásához válassza az **+ új** lehetőséget.

1. A szabály azonosításához adja meg a name _hőmérséklet-figyelőt_ , majd nyomja le az ENTER billentyűt.

1. Válassza ki az **érzékelő vezérlő** eszköz sablonját. Alapértelmezés szerint a szabály automatikusan az eszköz sablonhoz társított összes eszközre vonatkozik. Az eszközök egy részhalmazának szűréséhez válassza a **+ szűrés** lehetőséget, és használja az eszköz tulajdonságait az eszközök azonosításához. A szabály letiltásához kapcsolja be az **engedélyezett/letiltott** gombot:

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="A szabály definíciójában az eszköz sablonjának kijelölését bemutató képernyőkép":::

### <a name="configure-the-rule-conditions"></a>A szabály feltételeinek konfigurálása

A feltételek határozzák meg a szabály által figyelt feltételeket. Ebben az oktatóanyagban a szabályt úgy konfigurálja, hogy a hőmérséklet meghaladja a 70 &deg; F-ot.

1. Válassza a **hőmérséklet** lehetőséget a **telemetria** legördülő menüben.

1. Ezután válassza a **nagyobb, mint** a **kezelőt** , és adja meg a _70_ **értéket**.

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="A szabály hőmérsékleti feltételét bemutató képernyőkép":::

1. Igény szerint **időösszesítést** is beállíthat. Amikor kiválaszt egy időösszesítést, ki kell választania egy összesítési típust is, például az összesítési legördülő listából az átlagot vagy az összeget.

    * Összesítés nélkül a szabály minden olyan telemetria-adatponthoz aktiválódik, amely megfelel a feltételnek. Ha például úgy konfigurálja a szabályt, hogy a hőmérséklet 70-nál nagyobb legyen, akkor a szabály szinte azonnal elindul, amikor az eszköz hőmérséklete meghaladja ezt az értéket.
    * Az összesítéssel a szabály akkor aktiválódik, ha az időablakban található telemetria-adatpontok összesített értéke megfelel a feltételnek. Ha például úgy konfigurálja a szabályt, hogy az a hőmérséklet 70-as és az átlagos időösszesítést követően 10 percet vesz igénybe, akkor a szabály akkor aktiválódik, ha az eszköz a 70-nél nagyobb átlagos hőmérsékletet jelez, a 10 perces intervallumot számítva.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="A kitöltött összesítő feltételt megjelenítő képernyőkép":::

A **+ feltétel** lehetőség kiválasztásával több feltételt is hozzáadhat egy szabályhoz. Ha több feltétel van megadva, az összes feltételnek teljesülnie kell a szabály működésének elindításához. Minden feltétel egy implicit `AND` záradékhoz van csatlakoztatva. Ha több feltételt használó időösszesítést használ, a telemetria összes értékét összesíteni kell.

### <a name="configure-actions"></a>Műveletek konfigurálása

Miután definiálta a feltételt, beállíthatja, hogy a szabály milyen műveleteket végezhet el. A rendszer meghívja a műveleteket, ha a szabályban megadott összes feltétel igaz értékre van kiértékelve.

1. Válassza a **+ e-mail** lehetőséget a **műveletek** szakaszban.

1. Adja meg a _hőmérsékleti figyelmeztetést_ a művelet megjelenítendő neveként, az e-mail-címét a **to** mezőben, és _tekintse meg az eszközt!_ az e-mailek törzsében megjelenő Megjegyzés.

    > [!NOTE]
    > Az e-maileket csak az alkalmazáshoz hozzáadott felhasználóknak küldi el a rendszer, és legalább egyszer bejelentkezett. További információ az Azure IoT Central [felhasználói kezeléséről](howto-administer.md) .

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="A szabályhoz tartozó e-mail-műveletet megjelenítő képernyőkép":::

1. A művelet mentéséhez válassza a **kész** lehetőséget. Egy szabályhoz több műveletet is hozzáadhat.

1. A szabály mentéséhez válassza a **Mentés** lehetőséget. A szabály néhány percen belül életbe lép, és megkezdi az alkalmazásnak küldött telemetria figyelését. Ha a szabályban megadott feltétel teljesül, a szabály elindítja a konfigurált e-mail műveletet.

Egy idő után egy e-mail-üzenet jelenik meg, amikor a szabály a következőt jeleníti meg:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Az értesítő e-mailt megjelenítő képernyőkép":::

## <a name="delete-a-rule"></a>Szabály törlése

Ha már nincs szüksége egy szabályra, törölje azt a szabály megnyitásával, és válassza a **Törlés** lehetőséget.

## <a name="enable-or-disable-a-rule"></a>Szabály engedélyezése vagy letiltása

Válassza ki az engedélyezni vagy letiltani kívánt szabályt. A szabályban lévő összes eszközre vonatkozó szabály engedélyezéséhez vagy letiltásához kapcsolja be a szabályban **engedélyezett/letiltott** gombot.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Adott eszközökhöz tartozó szabályok engedélyezése vagy letiltása

Válassza ki a testre szabni kívánt szabályt. Használjon egy vagy több szűrőt a **cél eszközök** szakaszban, hogy szűkítse a szabály hatókörét a figyelni kívánt eszközökre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Most, hogy meghatározta a küszöbérték-alapú szabályt, a javasolt következő lépés az alábbiak megismerése:

> [!div class="nextstepaction"]
> [Webhookok létrehozása a szabályokban](./howto-create-webhooks.md).
