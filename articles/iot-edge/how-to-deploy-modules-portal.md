---
title: Modulok üzembe helyezése Azure Portal-Azure IoT Edge
description: A Azure Portal IoT Hub segítségével leküldheti a IoT Edge modult a IoT Hubról a IoT Edge eszközre, amelyet a telepítési jegyzék konfigurál.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9248c9578d94b000c04c82b33eeeb089e55a26ef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200313"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure IoT Edge modulok üzembe helyezése a Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Miután létrehozta IoT Edge modulokat az üzleti logikával, üzembe helyezheti azokat az eszközökön a peremhálózat működéséhez. Ha több modullal is együttműködik az adatok gyűjtéséhez és feldolgozásához, egyszerre telepítheti őket, és deklarálhatja az azokat összekötő útválasztási szabályokat.

Ez a cikk bemutatja, hogyan végigvezeti a Azure Portal az üzembe helyezési jegyzék létrehozásán és az üzembe helyezés egy IoT Edge eszközön való továbbításán. További információ a megosztott címkék alapján több eszközt célzó központi telepítés létrehozásáról: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
* Egy IoT Edge eszköz.

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. A [virtuális Linux-eszköz létrehozásához](quickstart-linux.md) vagy [virtuális Windows-eszköz létrehozásához](quickstart.md)kövesse az egyik rövid útmutató lépéseit.

## <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.

A Azure Portal tartalmaz egy varázslót, amely végigvezeti az üzembe helyezési jegyzék létrehozásán, a JSON-dokumentum manuális létrehozása helyett. Három lépésből áll: **modulok hozzáadása**, **útvonalak megadása** és az **üzembe helyezés áttekintése**.

>[!NOTE]
>A cikkben ismertetett lépések a IoT Edge-ügynök és a központ legújabb séma-verzióját tükrözik. A 1,1-es verziójú séma a IoT Edge verzió 1.0.10 együtt lett közzétéve, és lehetővé teszi a modul indítási sorrendjének és útvonal-rangsorolási funkcióinak használatát.
>
>Ha a 1.0.9 vagy korábbi verzióját futtató eszközre telepít központilag, akkor a varázsló **modulok** lépésében szerkessze a **futásidejű beállításokat** a séma 1,0-es verziójának használatához.

### <a name="select-device-and-add-modules"></a>Eszköz kiválasztása és modulok hozzáadása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
1. A bal oldali panelen válassza a **IoT Edge** lehetőséget a menüből.
1. Kattintson a céleszköz AZONOSÍTÓJÁRA az eszközök listájából.
1. A felső sávon válassza a **modulok beállítása** lehetőséget.
1. A lap **Container Registry beállítások** szakaszában adja meg a modul lemezképeit tartalmazó privát tároló-nyilvántartók eléréséhez szükséges hitelesítő adatokat.
1. A lap **IoT Edge modulok** szakaszában válassza a **Hozzáadás** lehetőséget.
1. A legördülő menüből válasszon a következő három típusú modul közül:

   * **IoT Edge modul** – megadja a modul nevét és a tároló rendszerképének URI-ját. A minta SimulatedTemperatureSensor-modul képuri-ja például a következő: `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Ha a modul rendszerképét egy privát tároló beállításjegyzékében tárolja, adja hozzá a hitelesítő adatokat ezen a lapon a rendszerkép eléréséhez.
   * **Piactér modul** – az Azure piactéren üzemeltetett modulok. Egyes piactér-modulok további konfigurálást igényelnek, ezért tekintse át a modul részleteit az [Azure marketplace IoT Edge modulok](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) listájában.
   * **Azure stream Analytics modul** – Azure stream Analytics munkaterhelés által generált modulok.

1. A modul hozzáadása után válassza ki a modul nevét a listából a modul beállításainak megnyitásához. Szükség esetén töltse ki az opcionális mezőket.

   További információ az elérhető modul beállításairól: [modulok konfigurálása és kezelése](module-composition.md#module-configuration-and-management).

   További információ a modul Twin-ről: a [kívánt tulajdonságok meghatározása vagy frissítése](module-composition.md#define-or-update-desired-properties).

1. Ismételje meg a 6 – 8. lépést további modulok hozzáadásához a központi telepítéshez.
1. Válassza a **Tovább: útvonalak** szakaszt az útvonalak szakaszhoz.

### <a name="specify-routes"></a>Útvonalak meghatározása

Az **útvonalak** lapon megadhatja, hogyan adja át az üzeneteket a modulok és a IoT hub között. Az üzenetek név/érték párokkal vannak kiépítve. Alapértelmezés szerint az új eszköz első üzembe helyezése egy **Route** nevű útvonalat tartalmaz, amely a **/messages/-ből a \* $upstreamba kerül**, ami azt jelenti, hogy az összes modul kimenetét az IoT hubhoz küldi a rendszer.  

Az élő paraméterek **prioritása** és **ideje** választható paraméterek, amelyeket hozzáadhat egy útvonal-definícióhoz. A Priority paraméterrel kiválaszthatja, hogy mely útvonalakon legyenek feldolgozva az üzenetek, vagy hogy mely útvonalakat kell feldolgozni utoljára. A prioritás meghatározása a 0-9 szám megadásával történik, ahol a 0 a legfontosabb prioritás. Az élettartam paraméter lehetővé teszi annak bejelentését, hogy mennyi ideig kell tárolni az adott útvonalon lévő üzeneteket, amíg azokat fel nem dolgozzák vagy el nem távolítják a várólistából.

Az útvonalak létrehozásával kapcsolatos további információkért lásd: [útvonalak deklarálása](module-composition.md#declare-routes).

Az útvonalak beállítása után kattintson a **Tovább gombra: felülvizsgálat + létrehozás** gombra, és folytassa a varázsló következő lépésével.

### <a name="review-deployment"></a>Központi telepítés áttekintése

A felülvizsgálati szakasz megjeleníti a JSON üzembe helyezési jegyzéket, amelyet az előző két szakaszban megadott beállítások alapján hoztak létre. Vegye figyelembe, hogy két modul deklarálta azt, hogy nem adta hozzá a következőket: **$edgeAgent** és **$edgeHub**. Ez a két modul hozza létre a [IoT Edge futtatókörnyezetet](iot-edge-runtime.md) , és minden központi telepítés esetében kötelező alapértelmezett érték.

Tekintse át az üzembe helyezési adatokat, majd kattintson a **Létrehozás** gombra.

## <a name="view-modules-on-your-device"></a>Az eszközön található modulok megtekintése

Miután telepítette a modulokat az eszközön, megtekintheti az összeset a IoT Hub eszköz részletei lapján. Ez a lap megjeleníti az egyes telepített modulok nevét, valamint a hasznos információkat, például a telepítési állapotot és a kilépési kódot.

## <a name="deploy-modules-from-azure-marketplace"></a>Modulok üzembe helyezése az Azure Marketplace-en

Az [Azure Marketplace](https://azuremarketplace.microsoft.com/) egy online alkalmazások és szolgáltatások piaca, ahol az Azure-on futó Nagyvállalati alkalmazások és megoldások széles körét böngészheti, beleértve a [IoT Edge modulokat](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)is.

IoT Edge modult az Azure piactéren és a IoT Hub is üzembe helyezhet.

### <a name="deploy-from-azure-marketplace"></a>Üzembe helyezés az Azure piactéren

Beolvassa a IoT Edge modulokat a piactéren, és ha megtalálta, hogy melyikre van szüksége, a **Létrehozás** **vagy a letöltés lehetőség** kiválasztásával telepítheti. Folytassa a központi telepítési varázsló lépéseit, amelyek a kiválasztott IoT Edge modultól függően változhatnak:

1. A **Folytatás** gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát. Előfordulhat, hogy először meg kell adnia a kapcsolattartási adatokat.
1. Válassza ki az előfizetését és azt a IoT Hub, amelyhez a céleszköz csatolva van.
1. Válassza **a telepítés eszközre** lehetőséget.
1. Adja meg az eszköz nevét, vagy válassza az **eszköz keresése** lehetőséget a hub-ban regisztrált eszközök között a tallózáshoz.
1. Válassza a **Létrehozás** lehetőséget a telepítési jegyzék konfigurálásának szabványos folyamatának folytatásához, beleértve a további modulok hozzáadását, ha szükséges. Az új modul adatai, például a rendszerkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

Ellenőrizze, hogy a modul telepítve van-e a IoT Hub a Azure Portalban. Válassza ki az eszközt, válassza a **modulok beállítása** elemet, és a modult a **IoT Edge modulok** szakaszban kell megadnia.

### <a name="deploy-from-azure-iot-hub"></a>Üzembe helyezés az Azure IoT Hub

Az Azure Marketplace-en gyorsan üzembe helyezhet egy modult az eszközön a Azure Portal IoT Hub.

1. A Azure Portal navigáljon a IoT Hub.
1. A bal oldali ablaktábla **automatikus eszközkezelés** területén válassza a **IoT Edge** lehetőséget.
1. Válassza ki azt a IoT Edge eszközt, amely az üzemelő példány fogadására szolgál.
1. A felső sávon válassza a **modulok beállítása** lehetőséget.
1. A **IoT Edge-modulok** szakaszban kattintson a **Hozzáadás** gombra, és válassza ki a **piactér modult** a legördülő menüből.

![Modul hozzáadása a IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Válasszon egy modult a **IoT Edge modul Marketplace** oldaláról. A kiválasztott modul automatikusan konfigurálva van az előfizetéshez, az erőforráscsoporthoz és az eszközhöz. Ezután megjelenik a IoT Edge-modulok listájában. Egyes modulok további konfigurálást igényelhetnek.

> [!TIP]
> Az Azure IoT Hub IoT Edge moduljaival kapcsolatos információk korlátozottak. Először tájékozódhat az Azure Marketplace [IoT Edge moduljairól](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .

Válassza a Next (tovább) lehetőséget **: útvonalakat** , és folytassa az üzembe helyezést a jelen cikk [útvonalak megadása](#specify-routes) és [üzembe helyezés áttekintése](#review-deployment) című szakaszában leírtak szerint.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezhet üzembe és figyelheti IoT Edge modulokat a skálán](how-to-deploy-at-scale.md)
