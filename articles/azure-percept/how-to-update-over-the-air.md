---
title: Azure Percept-alapú DK frissítése over-the-Air (OTA)
description: Ismerje meg, hogyan fogadhat over-the Air (OTA) frissítéseket az Azure Percept DK-be
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063117"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Azure Percept-alapú DK frissítése over-the-Air (OTA)

Ebből az útmutatóból megtudhatja, hogyan frissítheti az Azure-beli Percept DK (OTA) szolgáltatói Tanácsának operációs rendszerét és belső vezérlőprogramot a IoT Hub eszköz frissítésével.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): összekapcsolta a fejlesztői csomagot egy Wi-Fi hálózattal, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készletet a IoT hub
- [A IoT Hub eszköz frissítése sikeresen konfigurálva](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>A frissítési fájl és a jegyzékfájl importálása

> [!NOTE]
> Ha már importálta a frissítést, közvetlenül is kihagyhatja az **eszköz frissítési csoportjának létrehozását**.

1. [Töltse le a megfelelő jegyzékfájlt (. JSON) és a frissítési fájlt (. SWU) az Azure Percept-eszközhöz](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Navigáljon ahhoz az Azure-IoT Hubhoz, amelyet az Azure Percept-eszközhöz használ. A bal oldali menüben válassza az **eszközök frissítése** lehetőséget az **automatikus eszközkezelés** területen.

1. A képernyő felső részén több lap jelenik meg. Válassza a **frissítések** lapot.

1. Válassza az **+ új frissítés importálása** elemet a **készen áll a telepítéshez** fejléc alatt.

1. Kattintson a **manifest-fájl importálása** elemre, és **válassza a fájlok frissítése** elemet a jegyzékfájl (. JSON) és a frissítési fájl (. SWU) kiválasztásához.

1. Válassza a mappa ikont vagy a szövegmezőt a **tároló kiválasztása** területen, és válassza ki a megfelelő Storage-fiókot. Ha már létrehozott egy tárolót, újra használhatja azt. Ellenkező esetben a **+ tároló** elemre kattintva hozzon létre egy új Storage-tárolót az OTA-frissítésekhez. Válassza ki a használni kívánt tárolót, és kattintson a **kiválasztás** gombra.

1. Az importálási folyamat elindításához válassza a **Submit (Küldés** ) lehetőséget. A képméret miatt a beküldési folyamat akár 5 percet is igénybe vehet.

    > [!NOTE]
    > Előfordulhat, hogy a rendszer megkéri, hogy adjon hozzá egy Cross Origin Request (CORS) szabályt a kiválasztott tároló eléréséhez. Válassza a **szabály hozzáadása lehetőséget, majd** a folytatáshoz próbálkozzon újra.

1. Az importálási folyamat megkezdése után a rendszer átirányítja az **eszköz frissítései** lap **importálási előzmények** lapjára. Kattintson a **frissítés** gombra a folyamat figyeléséhez, miközben az importálási folyamat befejeződött. A frissítés méretétől függően ez eltarthat néhány percig, vagy hosszabb időt is igénybe vehet (csúcsidőben az importálási szolgáltatás akár 1 órát is igénybe vehet).

1. Ha az **állapot** oszlop azt jelzi, hogy az importálás sikeres volt, jelölje be a **telepítésre kész** lapra, majd kattintson a **frissítés** gombra. Ekkor megjelenik az importált frissítés a listában.

## <a name="create-a-device-update-group"></a>Eszköz frissítési csoportjának létrehozása

A IoT Hub eszköz frissítése lehetővé teszi az Azure Percept-DKs adott csoportjaira vonatkozó frissítések megcélzását. Csoport létrehozásához hozzá kell adnia egy címkét az Azure IoT Hubban lévő eszközök számára.

> [!NOTE]
> Ha már létrehozott egy csoportot, ugorjon a következő szakaszra.

A csoport címkére vonatkozó követelmények:

- A címkéhez bármilyen értéket hozzáadhat, kivéve a "Kategorizálatlan" értéket, amely egy foglalt érték.
- A címke értéke nem lehet hosszabb 255 karakternél.
- A címke értéke csak a következő speciális karaktereket tartalmazza: ".", "-", "_", "~".
- A címkék és csoportok nevei megkülönböztetik a kis-és nagybetűket.
- Egy eszközhöz csak egy címke tartozhat. Az eszközhöz hozzáadott minden további címke felülbírálja az előző címkét.
- Egy eszköz csak egy csoporthoz tartozhat.

1. Címke hozzáadása az eszköz (ök) hoz:

    1. A bal oldali navigációs ablaktáblán lévő **IoT Edge** keresse meg az Azure Percept DK-t, és navigáljon az **eszközéhez**.

    1. Adja hozzá a IoT Hub címke értékének új **eszköz frissítését** az alább látható módon ( ```<CustomTagValue>``` a címke értéke/neve, például AzurePerceptGroup1). További információ az eszköz Twin [JSON-dokumentum címkéről](../iot-hub/iot-hub-devguide-device-twins.md#device-twins).

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Kattintson a **Mentés** gombra, és oldja meg a formázási problémákat.

1. Hozzon létre egy csoportot egy meglévő Azure IoT Hub címke kiválasztásával:

    1. Térjen vissza az Azure IoT Hub oldalára.

    1. Válassza az **eszközök frissítései** lehetőséget a bal oldali menü panel **automatikus eszközkezelés** területén.

    1. Válassza a **csoportok** lapot. Ezen a lapon az eszköz frissítéséhez csatlakoztatott nem csoportosított eszközök száma jelenik meg.

    1. Új csoport létrehozásához válassza a **+ Hozzáadás** lehetőséget.

    1. Válasszon ki egy IoT Hub címkét a listából, majd kattintson a **Submit (Küldés**) gombra.

    1. A csoport létrehozása után a rendszer frissíti a frissítési megfelelőségi diagramot és a csoportok listáját. A diagram a megfelelőség különböző állapotában lévő eszközök számát jeleníti **meg: a legújabb frissítés**, az **új frissítések**, **a folyamatban lévő frissítések** és **még nincs csoportosítva**.

## <a name="deploy-an-update"></a>Frissítés központi telepítése

1. Az újonnan létrehozott csoportot új frissítéssel kell látnia az **elérhető frissítések** területen (Előfordulhat, hogy egyszer kell frissítenie). Válassza ki a frissítést.

1. Ellenőrizze, hogy a megfelelő eszközcsoport van-e kiválasztva célként kiválasztott eszközként. Válassza ki a központi telepítés **kezdési dátumát** és **kezdési idejét** , majd kattintson a **központi telepítés létrehozása** elemre.

    > [!CAUTION]
    > Ha a múltbeli kezdési időt állítja be, a rendszer azonnal elindítja az üzembe helyezést.

1. A megfelelőségi diagram ellenőrzése. Ekkor már folyamatban van a frissítés.

1. A frissítés befejezését követően a megfelelőségi diagramon megjelenik az új frissítési állapot.

1. Válassza a **központi telepítések** fület az **eszköz frissítései** oldal tetején.

1. Válassza ki az üzemelő példányt a központi telepítés részleteinek megtekintéséhez. Előfordulhat, hogy a **frissítés** elemre kell kattintania, amíg az **állapot** **sikeresen** be nem változik.

## <a name="next-steps"></a>Következő lépések

A fejlesztői csomag frissítése sikeresen megtörtént. Folytathatja a fejlesztést és a műveletet a fejlesztői csomaggal.