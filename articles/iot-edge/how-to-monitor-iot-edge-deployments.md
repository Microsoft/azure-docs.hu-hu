---
title: A IoT Edge figyelése – Azure IoT Edge
description: Magas szintű figyelés, beleértve az edgeHub és az edgeAgent jelentett tulajdonságokat és az automatikus üzembe helyezési metrikákat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 39e7bb5c151d490e79ef111589f52f260c3e6c7a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483163"
---
# <a name="monitor-iot-edge-deployments"></a>Üzemelő IoT Edge-példányok monitorozása

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge olyan jelentéseket biztosít, amelyek lehetővé teszi a saját eszközein üzembe helyezett modulok valós idejű információinak IoT Edge figyelése. A IoT Hub szolgáltatás lekéri az állapotadatokat az eszközökről, és elérhetővé teszi őket az operátor számára. A monitorozás olyan nagy léptékű [telepítések](module-deployment-monitoring.md) esetén is fontos, amelyek közé tartoznak az automatikus és a többrétegű üzembe helyezések.

Az eszközök és a modulok is hasonló adatokkal, például kapcsolattal rendelkezik, ezért az értékek az eszközazonosító vagy a modul azonosítója alapján vannak lekért adatok.

A IoT Hub szolgáltatás gyűjti az eszközök és modulok ikereszközei által jelentett adatokat, és megszabadja az eszközök által jelentett különböző államok számát. A IoT Hub szolgáltatás négy metrikacsoportba rendezi az adatokat:

| Típus | Leírás |
| --- | ---|
| Megcélzott | Megjeleníti a IoT Edge célcsoport-telepítési feltételnek megfelelő összes eszközt. |
| Alkalmazva | Azokat a céleszközöket IoT Edge, amelyekre nem irányul egy másik magasabb prioritású telepítés. |
| Sikeres jelentéskészítés | Megjeleníti azokat IoT Edge eszközöket, amelyek a modulok sikeres üzembe helyezését jelentették. |
| Jelentéskészítési hiba | Megjeleníti azokat IoT Edge eszközöket, amelyek azt jelentették, hogy egy vagy több modul üzembe helyezése nem sikerült. A hiba további kivizsgálásához távolról csatlakozzon ezekhez az eszközökhöz, és tekintse meg a naplófájlokat. |

A IoT Hub szolgáltatás elérhetővé teszi az adatokat, hogy megfigyelje a Azure Portal Azure CLI-ban.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Üzemelő példány figyelése a Azure Portal

A telepítés részleteinek megtekintéséhez és az azt futtató eszközök figyeléhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és lépjen a IoT Hub.
1. Válassza **IoT Edge** panel bal oldali menüjének Új elemre.
1. Válassza a **IoT Edge központi telepítések lapot.**
1. Vizsgálja meg az üzembe helyezési listát. Az egyes üzemelő példányok részletei a következők:

    | Oszlop | Leírás |
    | --- | --- |
    | ID (Azonosító) | Az üzemelő példány neve. |
    | Típus | Az üzemelő példány típusa( **Üzembe helyezés vagy** **Réteges üzembe helyezés).** |
    | Cél feltétel | A megcélzott eszközök meghatározásához használt címke. |
    | Prioritás | Az üzemelő példányhoz rendelt prioritási szám. |
    | Rendszermetrikák | A célcsoporthoz IoT Hub ikereszközök száma. **Az** Alkalmazott beállítás azon eszközök számát adja meg, amelyeken a modulikre alkalmazott telepítési tartalom a IoT Hub. |
    | Eszközmetrikák | A sikeres IoT Edge vagy hibákat jelentő eszközök száma IoT Edge ügyfél-futtatásból. |
    | Egyéni metrikák | A IoT Edge megadott metrikák adatait jelentéseket jelentéseket szolgáltató eszközök száma. |
    | Létrehozás ideje | Az üzembe helyezés létrehozási időbélyege. Ez az időbélyeg arra használható, hogy megszakítsa a két üzemelő példány azonos prioritású környezeteket. |

1. Válassza ki a figyelni kívánt központi telepítést.  
1. Az Üzembe **helyezés részletei lapon** görgessen le az alsó szakaszhoz, és válassza a Cél **feltétele** lapot. Válassza **a Nézet** lehetőséget a cél feltételnek megfelelő eszközök listához. Módosíthatja a feltételt és a **Prioritást is.** Ha **módosításokat is végrehajtott,** válassza a Mentés lehetőséget.

   ![Központi telepítés megcélzott eszközeinek megtekintése](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Válassza a **Metrikák** lapot. Ha metrikát választ a **Metrika** kiválasztása legördülő menüből, megjelenik **egy** Nézet gomb, amely megjeleníti az eredményeket. A Metrikák szerkesztése **lehetőséget** választva módosíthatja a megadott egyéni metrikák kritériumait. Ha **módosításokat is végrehajtott,** válassza a Mentés lehetőséget.

   ![Üzemelő példány metrikainak megtekintése](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Az üzemelő példány módosításához lásd: [Üzemelő példány módosítása.](how-to-deploy-at-scale.md#modify-a-deployment)

## <a name="monitor-a-deployment-with-azure-cli"></a>Üzemelő példány monitor helyezése az Azure CLI-val

Egyetlen [üzemelő példány részleteinek](/cli/azure/iot/edge/deployment) megjelenítéséhez használja az az iot edge deployment show parancsot:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

A deployment show parancs a következő paramétereket veszi fel:

* **--deployment-id** – Az IoT Hubban található üzemelő példány neve. Szükséges paraméter.
* **--hub-name** – Annak az IoT Hubnak a neve, amelyben az üzembe helyezés található. A központnak az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre az paranccsal `az account set -s [subscription name]`

Vizsgálja meg az üzembe helyezést a parancsablakban. A **metrics tulajdonság** listázza az egyes központok által kiértékelt metrikák számát:

* **targetedCount** – Rendszermetrika, amely meghatározza a célcsoportban található iker IoT Hub amelyek megfelelnek a célcsoport-beállítási feltételnek.
* **appliedCount** – A rendszermetrika határozza meg azon eszközök számát, amelyeken a modulikre alkalmazott telepítési tartalom a IoT Hub.
* **reportedSuccessfulCount** – Eszközmetrika, amely meghatározza a IoT Edge-eszközök számát, amelyek sikerességről jelentéseket IoT Edge ügyfél-IoT Edge során.
* **reportedFailedCount** – Eszközmetrika, amely meghatározza a IoT Edge-eszközök számát a központi telepítési jelentéskészítési IoT Edge az ügyfél-IoT Edge között.

Az [az iot edge deployment show-metric](/cli/azure/iot/edge/deployment) paranccsal az egyes metrikákhoz az eszköz-idek vagy -objektumok listáját is meg lehet mutatni:

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

A deployment show-metric parancs a következő paramétereket veszi fel:

* **--deployment-id** – Az IoT Hubban található üzemelő példány neve.
* **--metric-id** – Annak a metrikának a neve, amelynek meg szeretné látni az eszközazonosítók listáját, `reportedFailedCount` például: .
* **--hub-name** – Annak az IoT Hubnak a neve, amelyben az üzembe helyezés található. A központnak az aktuális előfizetésben kell lennie. Váltson a kívánt előfizetésre a `az account set -s [subscription name]` paranccsal.

Az üzemelő példány módosításához lásd: [Üzemelő példány módosítása.](how-to-deploy-cli-at-scale.md#modify-a-deployment)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan figyelheti az ikermodulokat [,](how-to-monitor-module-twins.md)elsősorban a IoT Edge Agent és IoT Edge Hub-modulokat az üzemelő példányok kapcsolatának és állapotának IoT Edge érdekében.
