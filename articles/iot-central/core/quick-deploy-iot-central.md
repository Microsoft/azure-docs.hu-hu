---
title: Rövid útmutató – Azure IoT Central-alkalmazás létrehozása | Microsoft Docs
description: Rövid útmutató – új Azure IoT Central-alkalmazás létrehozása. Hozza létre az alkalmazást az ingyenes díjszabási csomag vagy az egyik standard díjszabási csomag használatával.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c5e357824652077acab299e0e27678f528f23b9a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833898"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Rövid útmutató – Azure IoT Central-alkalmazás létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure IoT Central alkalmazást.


[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>Alkalmazás létrehozása

Navigáljon az [Azure IoT Central Build](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával.

Hozzon létre egy új alkalmazást az iparághoz kapcsolódó IoT Central sablonok listájából, amely segítséget nyújt a gyors kezdéshez, vagy az **egyéni alkalmazások** sablonjának használatával. Ebben a rövid útmutatóban az **egyéni alkalmazás** sablont használja.

Új Azure IoT Central-alkalmazás létrehozása az **egyéni alkalmazás** sablonból:

1. Navigáljon a **Build** oldalra:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="A IoT-alkalmazás oldalának összeállítása":::

1. Válassza az **egyéni alkalmazások** lehetőséget, és győződjön meg arról, hogy az **egyéni alkalmazás** sablon van kiválasztva.

1. Az Azure IoT Central automatikusan javaslatot tesz az **alkalmazás nevére** a kiválasztott alkalmazás sablonja alapján. Használhatja ezt a nevet, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét.

1. Az Azure IoT Central az alkalmazás neve alapján egyedi **URL-** előtagot is létrehoz az Ön számára. Ezt az URL-címet használja az alkalmazás eléréséhez. Módosítsa ezt az URL-előtagot valami emlékezetre, ha szeretné.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central alkalmazás létrehozása lap":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Azure IoT Central számlázási adatok":::

    > [!Tip]
    > Ha az előző oldalon az **egyéni alkalmazás** lehetőséget választotta, megjelenik egy alkalmazás- **sablon** legördülő lista. Előfordulhat, hogy a legördülő menü más sablonokat is tartalmaz, amelyeket a szervezete számára elérhetővé tettek.

1. Válassza ezt az alkalmazást a 7 napos ingyenes próbaverzió díjszabási csomagjának használatával, vagy a standard díjszabási csomagok valamelyikével:

    - Az *ingyenes* csomag használatával létrehozott alkalmazások hét napig ingyenesen használhatók, és legfeljebb öt eszközt támogatnak. Egy standard díjszabási csomag használatára a lejárat előtt bármikor átalakítható.
        > [!NOTE]
        > Az *ingyenes* csomag használatával létrehozott alkalmazások nem igényelnek Azure-előfizetést, ezért az Azure-előfizetésében nem találhatók meg az Azure Portal. Az ingyenes alkalmazásokat csak a IoT Central-portálról tekintheti meg és kezelheti.          
    - A *standard* csomag használatával létrehozott alkalmazások számlázása eszközönként történik, a **standard 0**, **standard 1** vagy standard **2** díjszabás közül választhat, amely az első két eszközzel ingyenes. Az ingyenes és standard díjszabási csomagokról az [Azure IoT Central díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/iot-central/)tájékozódhat. Ha standard díjszabási csomag használatával hoz létre alkalmazást, ki kell választania a *címtárat*, az *Azure-előfizetést* és a *helyet*:
        - A *könyvtár* az a Azure Active Directory, amelyben létrehozza az alkalmazást. A Azure Active Directory felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Ha nincs Azure Active Directory, akkor létrejön egy Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. IoT Central az előfizetéshez tartozó erőforrásokat. Ha nem rendelkezik Azure-előfizetéssel, az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription)ingyenesen létrehozhat egyet. Az Azure-előfizetés létrehozása után váltson vissza az **új alkalmazás** lapra. Az új előfizetés mostantól megjelenik az **Azure-előfizetés** legördülő menüjében.
        - A hely az a [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) *hely* , ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény érdekében általában ki kell választania az eszközökhöz legközelebb eső helyet. Ha kiválasztott egy helyet, később nem helyezheti át az alkalmazást egy másik helyre.

1. Tekintse át a használati feltételeket, majd válassza a **Létrehozás** elemet az oldal alján. Néhány perc elteltével IoT Central alkalmazás készen áll a használatra:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central-alkalmazás":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő lépés a IoT Central megismerésének folytatása:

> [!div class="nextstepaction"]
> [Szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](./quick-create-simulated-device.md)

Ha Ön egy eszköz fejlesztője, és szeretne belemerülni egy kódra, a javasolt következő lépés:
> [!div class="nextstepaction"]
> [Ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central-alkalmazással](./tutorial-connect-device.md)