---
title: Az IoT Central kezelése a Azure Portal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet IoT Central alkalmazásokat a Azure Portal.
services: iot-central
ms.service: iot-central
author: vishwam
ms.author: vishwams
ms.date: 04/17/2021
ms.topic: how-to
manager: philmea
ms.openlocfilehash: ed65e85c7428bf59fe770534e97afdd53564086a
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107601974"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Az IoT Central kezelése a Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Az alkalmazáskezelő [Azure Portal](https://portal.azure.com) hasonló IoT Central alkalmazásokat hozhat létre és kezelhet a IoT Central [alkalmazáskezelőben.](https://apps.azureiotcentral.com/myapps)

## <a name="create-iot-central-applications"></a>Új IoT Central létrehozása

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Alkalmazás létrehozásához nyissa meg a create IoT Central Application (Alkalmazás létrehozása [IoT Central)](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) lapot a Azure Portal és töltse ki az űrlapot.

![Űrlap IoT Central létrehozása](media/howto-manage-iot-central-from-portal/image6a.png)

* **Az erőforrásnév** egy egyedi név, amely a IoT Central az Azure-erőforráscsoportban.

* **Az alkalmazás URL-címe** az alkalmazás eléréséhez használható URL-cím.

* **A** Hely az [a földrajzi hely,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény elérése érdekében általában azt a helyet érdemes választani, amely fizikailag a legközelebb van az eszközeihez. Azure IoT Central jelenleg a következő helyeken érhető el:
    * Ázsia és a Csendes-óceáni térség
    * Ausztrália
    * Európa
    * Japán
    * Egyesült Királyság
    * Egyesült Államok

  Miután választott egy helyet, később nem tudja áthelyezni az alkalmazást egy másik helyre.

Az összes mező kitöltése után válassza a **Létrehozás lehetőséget.** További információért tekintse meg [a Create an IoT Central application (Alkalmazás IoT Central létrehozása)](quick-deploy-iot-central.md) rövid útmutatót.

## <a name="manage-existing-iot-central-applications"></a>Meglévő alkalmazás IoT Central kezelése

Ha már rendelkezik Azure IoT Central alkalmazással, törölheti, vagy áthelyezheti egy másik előfizetésbe vagy erőforráscsoportba a Azure Portal.

> [!NOTE]
> Az ingyenes csomag használatával létrehozott *alkalmazásokhoz* nincs szükség Azure-előfizetésre, ezért nem fogja megtalálni őket az Azure-előfizetésében a Azure Portal. Az ingyenes alkalmazásokat csak a portálon IoT Central kezelheti.

Első lépésekként keressen rá az alkalmazásra a képernyő tetején található Azure Portal. Az összes alkalmazást megtekintheti úgy is, hogy rákeres a "IoT Central Alkalmazások" szóra, és kiválasztja a szolgáltatást:

![Képernyőkép a "IoT Central alkalmazások" keresési eredményeiről, az első kijelölt szolgáltatással.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Miután kiválasztott egy alkalmazást a keresési eredmények között, a Azure Portal megjeleníti annak áttekintését. A tényleges alkalmazáshoz az alkalmazás URL-címének kiválasztásával **IoT Central meg:**

![Képernyőkép az "Áttekintés" oldalról, kiemelt "IoT Central URL-címével.](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás másik erőforráscsoportba való áthelyezéshez válassza az **erőforráscsoport melletti** módosítás lehetőséget. Az Erőforrások **áthelyezése lapon** válassza ki azt az erőforráscsoportot, amelybe át szeretné áthelyezni az alkalmazást:

![Képernyőkép az "Áttekintés" oldalról, az "Erőforráscsoport (módosítás)" kiemeléssel.](media/howto-manage-iot-central-from-portal/image4a.png)

Az alkalmazás másik előfizetésbe való áthelyezéséhez válassza az  **előfizetés melletti** módosítás lehetőséget. Az Erőforrások **áthelyezése lapon** válassza ki azt az előfizetést, amelybe át szeretné áthelyezni az alkalmazást:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezelheti Azure IoT Central alkalmazásokat a Azure Portal, a következő javasolt lépés:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)