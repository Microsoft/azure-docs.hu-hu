---
title: IoT Edge üzemelő példányok figyelése – Azure IoT Edge
description: Magas szintű monitorozás, beleértve a edgeHub és a edgeAgent jelentett tulajdonságokat és az automatikus üzembe helyezési metrikákat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: f4f2f8522f6d3d1039673803d946323deb485db9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200254"
---
# <a name="monitor-iot-edge-deployments"></a>Üzemelő IoT Edge-példányok monitorozása

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge olyan jelentéskészítést biztosít, amely lehetővé teszi a valós idejű információk figyelését a IoT Edge-eszközökön üzembe helyezett modulokon. A IoT Hub szolgáltatás lekérdezi az állapotot az eszközökről, és elérhetővé teszi őket a kezelő számára. A monitorozás a nagy [léptékű központi telepítések](module-deployment-monitoring.md) esetében is fontos, beleértve az automatikus telepítéseket és a rétegzett központi telepítéseket.

Mindkét eszközhöz és modulhoz hasonló adatok tartoznak, például a kapcsolat, így az értékek az eszköz azonosítója vagy a modul azonosítója alapján szerezhetők be.

A IoT Hub szolgáltatás adatokat gyűjt az eszköz és a modul ikrek által jelentett adatokról, és az eszközök különböző állapotait is tartalmazza. A IoT Hub szolgáltatás az adatokat négy metrikai csoportba rendezi:

| Típus | Description |
| --- | ---|
| Megcélzott | Megjeleníti a központi telepítésre vonatkozó megcélzott feltételnek megfelelő IoT Edge eszközöket. |
| Alkalmazva | Azokat a célként IoT Edge eszközöket jeleníti meg, amelyeket a magasabb prioritású központi telepítés nem céloz meg. |
| Sikeres jelentéskészítés | Azokat a IoT Edge eszközöket jeleníti meg, amelyek jelentették, hogy a modulok telepítése sikeresen megtörtént. |
| Jelentéskészítési hiba | Megjeleníti azokat a IoT Edge eszközöket, amelyek azt jelentették, hogy egy vagy több modul telepítése nem sikerült. A hiba további kivizsgálásához kapcsolódjon távolról az eszközökhöz, és tekintse meg a naplófájlokat. |

A IoT Hub szolgáltatás elérhetővé teszi ezeket az adatvédelmet a Azure Portal és az Azure CLI-ben.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Központi telepítés figyelése a Azure Portal

A központi telepítés részleteinek megtekintéséhez és az azt futtató eszközök figyeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.
1. A bal oldali ablaktábla menüjében válassza a **IoT Edge** lehetőséget.
1. Válassza a **IoT Edge központi telepítések** fület.
1. Vizsgálja meg a központi telepítés listáját. Az egyes központi telepítések esetében az alábbi adatokat tekintheti meg:

    | Oszlop | Leírás |
    | --- | --- |
    | ID (Azonosító) | A központi telepítés neve. |
    | Típus | A központi telepítés típusa, vagy a **telepítés** vagy a **rétegzett telepítés**. |
    | Cél feltétel | A célként megadott eszközök definiálásához használt címke. |
    | Prioritás | A központi telepítéshez rendelt prioritási szám. |
    | Rendszermetrikák | A célcsoport-kezelési feltételnek megfelelő, IoT Hubban található Twins-eszközök száma. **Alkalmazva** értékkel adható meg, hogy hány eszközön lett alkalmazva a központi telepítési tartalom a modulban IoT hub. |
    | Eszköz Metrikái | A IoT Edge ügyfél futtatókörnyezetének sikerességét vagy hibáit jelentő IoT Edge eszközök száma. |
    | Egyéni metrikák | A központi telepítéshez megadott metrikák adatait jelentő IoT Edge eszközök száma. |
    | Létrehozás ideje | A központi telepítés létrehozásának időbélyegzője. Ez az időbélyeg a kapcsolatok megszakítására szolgál, ha két üzemelő példány azonos prioritással rendelkezik. |

1. Válassza ki a figyelni kívánt központi telepítést.  
1. A **központi telepítés részletei** lapon görgessen le az alsó szakaszhoz, és válassza a **cél feltétel** lapot. Válassza a **nézet** lehetőséget a célként megadott feltételnek megfelelő eszközök listázásához. Megváltoztathatja a feltételt és a **prioritást** is. Ha módosításokat végzett, válassza a **Mentés** lehetőséget.

   ![Központi telepítéshez tartozó megtekintett eszközök megtekintése](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Válassza a **metrikák** fület. Ha a **metrika kiválasztása** legördülő menüből választ egy mérőszámot, megjelenik egy **nézet** gomb, amely megjeleníti az eredményeket. Kiválaszthatja a **metrikák szerkesztése** lehetőséget is, ha módosítani szeretné a definiált egyéni metrikák feltételeit. Ha módosításokat végzett, válassza a **Mentés** lehetőséget.

   ![Központi telepítés metrikáinak megtekintése](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Az üzembe helyezés módosításához lásd: [központi telepítés módosítása](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Üzemelő példány figyelése az Azure CLI-vel

Egy központi telepítés részleteinek megjelenítéséhez használja az az [IoT Edge Deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) parancsot:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Az üzembe helyezési show parancs a következő paramétereket veszi figyelembe:

* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve. Szükséges paraméter.
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]`

Vizsgálja meg a központi telepítést a parancsablakban. A **metrikák** tulajdonság felsorolja az egyes hubok által kiértékelt metrikák darabszámát:

* **targetedCount** – a rendszer mérőszáma, amely meghatározza, hogy hány eszköz található az IoT Hubban, amely megfelel a célcsoport-kezelési feltételnek.
* **appliedCount** – a rendszermetrika határozza meg, hogy hány eszközön lett alkalmazva a központi telepítési tartalom az IoT hub-ben a modulba tartozó ikrekre.
* **reportedSuccessfulCount** – az eszköz metrikája, amely meghatározza, hogy hány IoT Edge eszköz szerepel a központi telepítési jelentéskészítés sikerességében a IoT Edge ügyfél futtatókörnyezetében.
* **reportedFailedCount** – az eszköz metrikája, amely meghatározza, hogy hány IoT Edge eszköz szerepel a központi telepítés jelentéskészítési hibájában a IoT Edge ügyfél futtatókörnyezetből.

Az az [IoT Edge Deployment show-metrikus](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric) paranccsal megjelenítheti az egyes mérőszámokhoz tartozó eszközök azonosítóit vagy objektumait.

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Az üzembe helyezés megjelenítése-metrika parancs a következő paramétereket veszi figyelembe:

* **--Deployment-ID** – az IoT hub-ban található központi telepítés neve.
* **--metrikus azonosító** – annak a metrikának a neve, amelyre vonatkozóan meg szeretné jeleníteni az eszközök azonosítóinak listáját, például: `reportedFailedCount` .
* **--hub-Name** -annak a IoT-hubhoz a neve, amelyben a központi telepítés létezik. A hubhoz a jelenlegi előfizetésben kell lennie. Váltson a kívánt előfizetésre a paranccsal `az account set -s [subscription name]` .

Az üzembe helyezés módosításához lásd: [központi telepítés módosítása](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [figyelheti az ikrek modult](how-to-monitor-module-twins.md), elsősorban a IoT Edge Agent és IoT Edge hub Runtime-modulokat a IoT Edge üzemelő példányok kapcsolatára és állapotára vonatkozóan.
