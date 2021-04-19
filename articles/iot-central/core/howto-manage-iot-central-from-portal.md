---
title: Az IoT Central kezelése a Azure Portal | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet IoT Central alkalmazásokat a Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: 3e5e126815d0171a6c1627a08419b05b9a3c0c23
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719204"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Az IoT Central kezelése a Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

A Azure Portal [hozhat](https://portal.azure.com) létre és kezelhet IoT Central alkalmazásokat.

## <a name="create-iot-central-applications"></a>Új IoT Central létrehozása

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Alkalmazás létrehozásához lépjen a IoT Central [Alkalmazás lapjára](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) a Azure Portal:

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

Az összes mező kitöltése után válassza a **Létrehozás lehetőséget.** További tudnivalókért tekintse meg [a Create an IoT Central application (Új](quick-deploy-iot-central.md) alkalmazás létrehozása) rövid útmutatót.

## <a name="manage-existing-iot-central-applications"></a>Meglévő alkalmazás IoT Central kezelése

Ha már rendelkezik Azure IoT Central alkalmazással, törölheti, vagy áthelyezheti egy másik előfizetésbe vagy erőforráscsoportba a Azure Portal.

> [!NOTE]
> Az ingyenes csomag használatával létrehozott *alkalmazásokhoz* nincs szükség Azure-előfizetésre, ezért nem fogja megtalálni őket az Azure-előfizetésében a Azure Portal. Az ingyenes alkalmazásokat csak a portálon IoT Central kezelheti.

Első lépésekként keressen rá az alkalmazásra a képernyő tetején található Azure Portal. Az összes alkalmazást megtekintheti úgy is, hogy rákeres az _IoT Central,_ és kiválasztja a szolgáltatást:

![Képernyőkép a "IoT Central alkalmazások" keresési eredményeiről, az első kijelölt szolgáltatással.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Amikor kiválaszt egy alkalmazást a keresési eredmények között, a Azure Portal megjeleníti annak áttekintését. Az alkalmazás url-címének kiválasztásával IoT Central **az alkalmazást:**

![Képernyőkép az "Áttekintés" oldalról, az "alkalmazás URL-IoT Central" kiemelésével.](media/howto-manage-iot-central-from-portal/image3.png)

Az alkalmazás másik erőforráscsoportba való áthelyezéshez válassza az **erőforráscsoport melletti** módosítás lehetőséget. Az Erőforrások **áthelyezése lapon** válassza ki azt az erőforráscsoportot, amelybe át szeretné áthelyezni az alkalmazást:

![Képernyőkép az "Áttekintés" oldalról, az "Erőforráscsoport (módosítás)" kiemeléssel.](media/howto-manage-iot-central-from-portal/image4a.png)

Az alkalmazás másik előfizetésbe való áthelyezéséhez válassza az  **előfizetés melletti** módosítás lehetőséget. Az Erőforrások **áthelyezése lapon** válassza ki azt az előfizetést, amelybe át szeretné áthelyezni az alkalmazást:

![Felügyeleti portál: erőforrás-kezelés](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan kezelheti Azure IoT Central alkalmazásokat a Azure Portal, a következő javasolt lépés:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)