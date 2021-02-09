---
title: A IoT kapcsolódó logisztikai útmutatója | Microsoft Docs
description: A IoT Central kapcsolódó logisztikai alkalmazás sablonjának oktatóanyaga
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 7c6c50b6fd8200726aa11f120d9748cc1b40aa22
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831620"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Oktatóanyag: üzembe helyezés és útmutató a csatlakoztatott logisztikai alkalmazási sablonhoz

Ez az oktatóanyag bemutatja, hogyan kezdheti meg a IoT Central *csatlakoztatott logisztikai* alkalmazás sablonját. Megtudhatja, hogyan helyezheti üzembe és használhatja a sablont.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy csatlakoztatott logisztikai alkalmazást.
> * Használja az alkalmazás fő funkcióit.
> * Az irányítópult használatával jelenítheti meg a kritikus logisztikai eszköz működési tevékenységeit.
> * Eszköz sablonjának használata
> * Szabályok követése
> * Feladatok használata

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazás telepítéséhez nincs szükség konkrét előfeltételekre.
* Használhatja az ingyenes díjszabási csomagot, vagy Azure-előfizetést is használhat.

## <a name="create-connected-logistics-application"></a>Csatlakoztatott logisztikai alkalmazás létrehozása

Hozza létre az alkalmazást az alábbi lépések segítségével:

1. Navigáljon az [Azure IoT Central Build](https://aka.ms/iotcentral) webhelyére. Ezután jelentkezzen be a Microsoft személyes, munkahelyi vagy iskolai fiókjával. Válassza a **Létrehozás** lehetőséget a bal oldali navigációs sávon, majd válassza a **kereskedelmi** lapot:

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Csatlakoztatott logisztikai sablon":::

1. Válassza az **alkalmazás létrehozása** a **csatlakoztatott logisztikai alkalmazásban** lehetőséget.

1. Az alkalmazás **létrehozása** megnyitja az **új alkalmazás** űrlapot. Adja meg a következő részleteket:


    * **Alkalmazás neve**: az alapértelmezett javasolt nevet használhatja, vagy megadhatja a felhasználóbarát alkalmazás nevét.
    * **URL**: használhatja a javasolt alapértelmezett URL-címet, vagy megadhatja a felhasználóbarát egyedi emlékezetes URL-címét. Ezt követően az alapértelmezett beállítás akkor ajánlott, ha már rendelkezik Azure-előfizetéssel. Elkezdheti a 7 napos ingyenes próbaverzió díjszabását, és úgy is dönthet, hogy az ingyenes nyomvonal lejárta előtt bármikor standard díjszabási csomagra vált.
    * **Számlázási információ**: a címtár, az Azure-előfizetés és a régió részletei szükségesek az erőforrások kiépítéséhez.
    * **Létrehozás**: válassza a létrehozás lehetőséget az oldal alján az alkalmazás üzembe helyezéséhez.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="Csatlakoztatott logisztikai alkalmazás sablonja":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Kapcsolódó logisztikai számlázási információk":::

## <a name="walk-through-the-application"></a>Az alkalmazás végigvezeti

Alább látható a csatlakoztatott logisztikai alkalmazás sablonjának kiválasztását bemutató képernyőkép.

> [!div class="mx-imgBorder"]
> ![A csatlakoztatott logisztikai alkalmazás sablonjának kijelölését bemutató képernyőkép](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

Az alábbi szakasz végigvezeti az alkalmazás főbb funkcióinak áttekintésén.

### <a name="dashboard"></a>Irányítópult

Az alkalmazás sablonjának üzembe helyezése után az alapértelmezett irányítópult egy kapcsolódó logisztikai szolgáltatói portál. A Northwind Trader egy fiktív logisztikai szolgáltató, amely egy árufuvarozási flottát kezel a tengeren és a szárazföldön. Ebben az irányítópultban két különböző átjáró jelenik meg, amelyek a szállítások telemetria biztosítják, valamint a hozzájuk tartozó parancsokat, feladatokat és műveleteket.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: alkalmazás létrehozása a csatlakoztatott logisztikai alkalmazás sablonból](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Az alkalmazás létrehozásakor a számlázási beállításokat ábrázoló képernyőkép](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Ez az irányítópult előre konfigurálva van a kritikus logisztikai eszköz működési tevékenységének megjelenítéséhez.

Az irányítópult két különböző átjáró-Eszközkezelő műveletet tesz lehetővé:

* Megtekintheti a tehergépkocsi-szállítmányok logisztikai útvonalait és a tengeri szállítások helyének részleteit.
* Tekintse meg az átjáró állapotát és az egyéb kapcsolódó információkat.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Csatlakoztatott logisztikai irányítópult":::

* Az átjárók, az aktív és az ismeretlen címkék teljes száma nyomon követhető.
* A következőket teheti: a belső vezérlőprogram frissítése, az érzékelők letiltása és engedélyezése, az érzékelő küszöbértékének frissítése, a telemetria intervallumok frissítése és az eszköz szolgáltatási szerződésének frissítése.
* Az eszköz akkumulátor-felhasználásának megtekintése.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Csatlakoztatott logisztikai irányítópult állapota":::

#### <a name="device-template"></a>Eszköz sablonja

Válassza ki az **eszközök sablonjait** az átjáró képességi modelljének megtekintéséhez. A képesség modell az átjáró telemetria van strukturálva **& tulajdonság** és az **átjáró parancsai** felületek.

**Átjáró telemetria & tulajdonsága** – ez a felület határozza meg az érzékelőkkel, a hellyel és az eszköz adataival kapcsolatos összes telemetria. Az illesztőfelület az eszközök Twin tulajdonságának képességeit is meghatározza, például az érzékelő küszöbértékeit és a frissítési intervallumokat.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Telemetria és tulajdonság felülete":::

**Átjáró parancsai** – ez a felület az összes átjáró-parancs funkcióját szervezi:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Átjáró parancsainak felülete":::

### <a name="rules"></a>Szabályok

Válassza a **szabályok** lapot az alkalmazási sablonban található szabályok lapon. Ezek a szabályok a kezelőknek szóló e-mail-értesítésekre vannak konfigurálva a további vizsgálatok érdekében:

**Átjáró-lopás riasztása**: Ez a szabály akkor aktiválódik, ha az érzékelők váratlan fényt észlelnek a folyamat során. A potenciális lopás kivizsgálásához azonnal értesíteni kell a kezelőket.

Nem **válaszoló átjáró**: Ez a szabály akkor aktiválódik, ha az átjáró hosszabb ideig nem jelent jelentést a felhőnek. Az átjáró nem válaszol, mert kevés az akkumulátor, a kapcsolat elvesztése vagy az eszköz károsodása.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Szabályok definíciói":::

### <a name="jobs"></a>Feladatok

Válassza a **feladatok** fület az alkalmazásban található feladatok megtekintéséhez:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Futtatandó feladatok":::

Az alkalmazásokra vonatkozó műveleteket feladatok végrehajtására is használhatja. Az alkalmazásban szereplő feladatok eszköz-és Twin-képességeket használnak olyan feladatok elvégzéséhez, mint például bizonyos érzékelők letiltása az összes átjárón, vagy az érzékelő küszöbértékének módosítása a szállítási módtól és útvonaltól függően:

* Ez egy standard művelet, amellyel letilthatja a sokk-érzékelőket az óceáni szállítás során, hogy az akkumulátor vagy az alacsonyabb hőmérsékleti küszöbértéket csökkentse a hideg láncú szállítás során.

* A feladatok lehetővé teszik a rendszerszintű műveletek, például a belső vezérlőprogram frissítését az átjárón vagy a szolgáltatási szerződés frissítését, hogy naprakészek maradjanak a karbantartási tevékenységekben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazás sablonját a **felügyeleti**  >  **alkalmazás beállításainak** meglátogatásával, és válassza a **Törlés** lehetőséget.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Sablon karbantartása":::

## <a name="next-steps"></a>Következő lépések

További információ:

> [!div class="nextstepaction"]
> [Kapcsolódó logisztikai fogalmak](./architecture-connected-logistics.md)
