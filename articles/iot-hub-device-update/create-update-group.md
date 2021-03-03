---
title: Eszközcsoport létrehozása az Azure IoT Hub eszköz frissítésében | Microsoft Docs
description: Eszközcsoport létrehozása az Azure IoT Hub eszköz frissítése során
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679517"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Eszközcsoport létrehozása a IoT Hub eszköz frissítésében
A IoT Hub eszköz frissítése lehetővé teszi egy frissítés telepítését IoT-eszközök egy csoportjára.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzáférés egy IoT hubhoz az eszköz frissítésével IoT hub engedélyezve](create-device-update-account.md). Javasoljuk, hogy a IoT Hub S1 (standard) vagy újabb verzióját használja. 
* Az eszköz frissítéséhez kiépített IoT-eszköz (vagy szimulátor) IoT Hubon belül.
* [A kiépített eszközhöz legalább egy frissítés importálása sikeres volt.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Címke hozzáadása az eszközökhöz  

A IoT Hub eszköz frissítése lehetővé teszi egy frissítés telepítését IoT-eszközök egy csoportjára. Egy csoport létrehozásához az első lépés egy címke hozzáadása a IoT Hub lévő eszközök céljához. A címkék csak az eszköz frissítéséhez való csatlakozás után adhatók hozzá sikeresen az eszközhöz.

Az alábbi dokumentáció a címkék hozzáadását és frissítését ismerteti.

### <a name="programmatically-update-device-twin"></a>Eszközön lévő Twin-eszközök programozott frissítése

A RegistryManager használatával a megfelelő címkével frissítheti az eszközt, miután regisztrálta az eszközt az eszköz frissítésével. 
[Megtudhatja, hogyan adhat hozzá címkéket egy minta .NET-alkalmazás használatával.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[A címke tulajdonságainak megismerése](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Eszköz frissítési címke formátuma

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Feladatok használata

A feladatokat több eszközön is ütemezheti, [Ha az alábbi példákat](../iot-hub/iot-hub-devguide-jobs.md) követve hozzáad vagy frissít egy eszköz-frissítési címkét. [További információk](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Ez a művelet az aktuális IOT hub-üzenetek kvótájának változására vonatkozik, és javasoljuk, hogy egyszerre csak akár 50 000-es eszköz-dupla címkét kelljen megadnia, ellenkező esetben előfordulhat, hogy további IoT Hub egységeket kell vásárolnia, ha túllépi a napi IoT Hub az üzenet kvótáját. A részletek a [kvóták és a szabályozás](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)során találhatók meg.

### <a name="direct-twin-updates"></a>Közvetlen dupla frissítések

A címkék közvetlenül is hozzáadhatók vagy frissíthetők az eszközökhöz.

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.

2. A bal oldali navigációs ablaktáblán található "IoT-eszközök" vagy "IoT Edge" közül keresse meg a IoT-eszközt, és keresse meg az eszköz Ikerét.

3. A Twin (eszközök) készletben törölje a meglévő eszköz frissítési címke értékét a NULL értékre állításával.

4. Vegyen fel egy új eszköz frissítési címke értékét az alább látható módon. [Példa az eszköz kettős JSON-dokumentumára címkékkel.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Korlátozások

* A címkéhez bármilyen értéket hozzáadhat, kivéve a "Kategorizálatlan" értéket, amely foglalt érték.
* A címke értéke nem lehet hosszabb 255 karakternél.
* A címke értéke alfanumerikus karaktereket és a következő speciális karaktereket tartalmazhatja: ".", "-", "_", "~".
* A címkék és a csoportok nevei megkülönböztetik a kis-és nagybetűket.
* Egy eszköz csak egy címkével rendelkezhet a ADUGroup nevével, az adott névvel ellátott címke további kiegészítései pedig felülbírálják a ADUGroup-címke meglévő értékét.
* Egy eszköz csak egy csoporthoz tartozhat.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Eszközcsoport létrehozása meglévő IoT Hub címke kiválasztásával

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Válassza ki az eszköz frissítési példányához korábban csatlakoztatott IoT Hub.

3. Válassza ki az eszközök frissítései lehetőséget a bal oldali navigációs sávon az automatikus eszközkezelés lehetőség alatt.

4. Válassza a csoportok fület az oldal tetején. Ekkor megtekintheti az eszköz frissítéséhez csatlakoztatott eszközök számát, amelyek még nincsenek csoportosítva.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Képernyőkép a nem csoportosított eszközökről." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Új csoport létrehozásához kattintson a Hozzáadás gombra.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Képernyőkép az eszközcsoport hozzáadásáról." lightbox="media/create-update-group/add-group.png":::

6. Válasszon ki egy IoT Hub címkét a listából, majd válassza a frissítési csoport létrehozása lehetőséget.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="A címke kijelölésének képernyőképe" lightbox="media/create-update-group/select-tag.png":::

7. A csoport létrehozása után látni fogja, hogy a frissítés megfelelőségi diagram és csoportok listája frissül.  A megfelelőségi diagram frissítése a megfelelőség különböző állapotában lévő eszközök számát jeleníti meg: a legújabb frissítés, az új frissítések, a folyamatban lévő frissítések és az eszközök még nincsenek csoportosítva. [A frissítés megfelelőségének megismerése.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Képernyőkép a frissítés megfelelőségi nézetéről." lightbox="media/create-update-group/updated-view.png":::

8. Ekkor meg kell jelennie az újonnan létrehozott csoportnak és az eszközökre vonatkozó összes elérhető frissítésnek az új csoportban. Ezt a nézetet a frissítés nevére kattintva telepítheti az új csoportba. További részletekért tekintse meg a következő lépést: frissítés telepítése.

## <a name="view-device-details-for-the-group-you-created"></a>Az eszköz adatainak megtekintése a létrehozott csoporthoz

1. Navigáljon az újonnan létrehozott csoportra, és kattintson a csoport nevére.

2. A csoport részét képező eszközök listája az eszköz frissítési tulajdonságaival együtt jelenik meg. Ebben a nézetben az összes olyan eszköz megfelelőségi információit is láthatja, amelyek tagjai a csoportnak. A megfelelőségi diagram frissítése megjeleníti az eszközök számát a megfelelőség különböző állapotában: a legújabb frissítés, a rendelkezésre álló új frissítések és a folyamatban lévő frissítések.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Képernyőkép az eszközcsoport részletei nézetről." lightbox="media/create-update-group/group-details.png":::

3. A csoporton belül minden egyes eszközre rákattinthat, hogy átirányítja a IoT Hub eszköz részletek lapjára.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Képernyőkép az eszköz részletei nézetről." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Következő lépések 

[Frissítés telepítése](deploy-update.md)

[További információ az eszközök csoportjairól](device-update-groups.md)

[A frissítés megfelelőségének megismerése.](device-update-compliance.md)
