---
title: Rövid útmutató – Alkalmazás Azure IoT Central létrehozása | Microsoft Docs
description: Rövid útmutató – Új Azure IoT Central létrehozása. Hozza létre az alkalmazást az ingyenes vagy a standard tarifacsomagok egyikével.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 7b33beaad580e64a4760b0557f04f266ecfc1b4d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718808"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Rövid útmutató – Azure IoT Central létrehozása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure IoT Central alkalmazást.

## <a name="prerequisite"></a>Előfeltétel 

 - Aktív előfizetéssel rendelkezik egy Azure-fiók. Hozzon létre egy ingyenes [fiókot.](https://aka.ms/createazuresubscription)
 - Az Azure-előfizetésnek Közreműködői hozzáféréssel kell lennie

## <a name="create-an-application"></a>Alkalmazás létrehozása

Lépjen az [Azure IoT Central Build webhelyre.](https://aka.ms/iotcentral) Ezután jelentkezzen be egy személyes, munkahelyi vagy iskolai Microsoft-fiókkal.

Létrehozhat egy új alkalmazást az iparág szempontjából releváns IoT Central-sablonok listájából, így gyorsan elkezdheti az első lépésekben, vagy kezdheti az új alkalmazást egy **egyéni alkalmazássablonnal.** Ebben a rövid útmutatóban az Egyéni alkalmazás **sablont használja.**

Új alkalmazás Azure IoT Central egyéni  alkalmazássablonból:

1. Lépjen a **Build (Build)** lapra:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="IoT-alkalmazásoldal összeállítása":::

1. Válassza az **Egyéni alkalmazás lehetőséget**

1. Az Új **alkalmazás lapon** győződjön meg arról, hogy az **Alkalmazássablon** alatt az Egyéni alkalmazás **van kiválasztva.**

1. Azure IoT Central automatikusan javaslatott ad egy **alkalmazásnévre** a kiválasztott alkalmazássablon alapján. Használhatja ezt a nevet, vagy megadhatja a saját rövid alkalmazásnevét.

1. Azure IoT Central létrehoz egy egyedi **URL-előtagot** is az alkalmazás neve alapján. Ezt az URL-címet használhatja az alkalmazás eléréséhez. Ha szeretné, módosítsa ezt az URL-előtagot egy könnyen megjegyezhetőre.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central Alkalmazás létrehozása lap létrehozása":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Azure IoT Central számlázási adatok":::

    > [!Tip]
    > Ha az előző **oldalon az Egyéni alkalmazás** et választotta, egy Alkalmazássablon **legördülő** menüt fog látni. Előfordulhat, hogy a legördülő menüben a szervezet által elérhetővé tett egyéb sablonok is érhetők el.

1. Ezt az alkalmazást a 7 napos ingyenes próba-díjszabási csomag vagy a standard tarifacsomagok egyikének használatával hozza létre:

    - Az ingyenes csomag  használatával létrehozott alkalmazások hét napig ingyenesek, és legfeljebb öt eszközt támogatnak. A lejáratuk előtt bármikor átalakíthatja őket standard díjszabási csomag használatára.
        > [!NOTE]
        > Az ingyenes csomag használatával létrehozott *alkalmazásokhoz* nincs szükség Azure-előfizetésre, ezért nem fogja megtalálni őket az Azure-előfizetésében a Azure Portal. Az ingyenes alkalmazásokat csak a portálon IoT Central kezelheti.          
    - A standard csomaggal létrehozott alkalmazások számlázása eszközönként történik. Választhatja *a* **Standard 0,** **Standard 1** vagy **Standard 2** tarifacsomagot, ha az első két eszköz ingyenes. További információt az ingyenes és a standard díjszabásról a Azure IoT Central [oldalon olvashat.](https://azure.microsoft.com/pricing/details/iot-central/) Ha egy standard díjszabási csomag használatával hoz létre egy alkalmazást, ki kell választania a *címtárat,* *az Azure-előfizetést* és a *Helyet:*
        - *A* címtár az Azure Active Directory, amelyben létrehozza az alkalmazást. A Azure Active Directory felhasználói identitásokat, hitelesítő adatokat és egyéb szervezeti adatokat tartalmaz. Ha nem rendelkezik előfizetéssel, Azure Active Directory létre egyet az Azure-előfizetés létrehozásakor.
        - Az *Azure-előfizetéssel* Azure-szolgáltatások példányait hozhatja létre. IoT Central erőforrásokat az előfizetésben. Ha nem rendelkezik Azure-előfizetéssel, ingyenesen létrehozhat egyet az [Azure bejelentkezési oldalán.](https://aka.ms/createazuresubscription) Az Azure-előfizetés létrehozása után lépjen vissza az Új **alkalmazás oldalra.** Az új előfizetés most megjelenik az **Azure-előfizetés** legördülő menüben.
        - *A* Hely az [a földrajzi hely,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol létre szeretné hozni az alkalmazást. Az optimális teljesítmény elérése érdekében általában azt a helyet érdemes választani, amely fizikailag a legközelebb van az eszközeihez. Miután választott egy helyet, később nem tudja áthelyezni az alkalmazást egy másik helyre.

1. Tekintse át a használati feltételeket, és **válassza** a Létrehozás lehetőséget a lap alján. Néhány perc múlva a IoT Central készen áll a használatra:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central alkalmazás":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy IoT Central-alkalmazást. A következő javasolt lépés a további IoT Central:

> [!div class="nextstepaction"]
> [Szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](./quick-create-simulated-device.md)

Ha Ön eszközfejlesztő, és szeretne belemerülni néhány kódba, a következő javasolt lépés a következő:
> [!div class="nextstepaction"]
> [Ügyfélalkalmazás létrehozása és csatlakoztatása a Azure IoT Central alkalmazáshoz](./tutorial-connect-device.md)
