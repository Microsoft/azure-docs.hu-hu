---
title: Rövid útmutató – szimulált eszköz hozzáadása az Azure IoT Central
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy eszköz-sablont, és hogyan adhat hozzá szimulált eszközt a IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c7925c1bc397e15d8b6e0f2f21d5e9147109b83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064851"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Gyors útmutató: szimulált eszköz hozzáadása a IoT Central alkalmazáshoz

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az eszköz sablonja meghatározza a IoT Central-alkalmazáshoz csatlakozó eszközök képességeit. A képességek közé tartozik az eszköz telemetria, az eszköz tulajdonságai, valamint az eszköz által válaszoló parancsok. Egy eszköz sablonjában egy építő vagy egy operátor valós és szimulált eszközöket is hozzáadhat egy alkalmazáshoz. A szimulált eszközök hasznosak lehetnek a IoT Central alkalmazás működésének teszteléséhez a valódi eszközök csatlakoztatása előtt.

Ebben a rövid útmutatóban hozzá kell adnia egy ESP32-Azure IoT Kit fejlesztői táblához tartozó sablont, és létre kell hoznia egy szimulált eszközt. A rövid útmutató elvégzéséhez nincs szükség valódi eszközre, és az eszköz szimulációja is működik. Egy ESP32-eszköz:

* Telemetria, például hőmérsékletet küld.
* Az eszközre jellemző tulajdonságokat, például az eszköz újraindítása óta a maximális hőmérsékletet jelenti.
* Válaszol a parancsokra, például az újraindításra.
* Az általános eszköz tulajdonságait, például a belső vezérlőprogram verzióját és a sorozatszámot jelenti.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central-alkalmazás létrehozásához az **egyéni alkalmazás > egyéni alkalmazásspecifikus** sablon használatával.

## <a name="create-a-device-template"></a>Eszközsablon létrehozása

Építőként az IoT Central alkalmazásban létrehozhat és szerkesztheti az eszközök sablonjait. Miután közzétett egy sablont, szimulált eszközt hozhat, vagy a valódi eszközöket összekapcsolhatja az eszköz sablonjában. A szimulált eszközök lehetővé teszik az alkalmazás működésének tesztelését a valódi eszköz csatlakoztatása előtt.

Ha új sablont szeretne felvenni az alkalmazásba, válassza a bal oldali ablaktábla **eszközök sablonok** lapját.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Az eszközök sablonjainak üres listáját bemutató képernyőkép":::

Az eszköz sablonja az alábbiakat meghatározó eszköz modellt tartalmaz:

* Az eszköz által küldött telemetria.
* Eszköz tulajdonságai
* Azok a parancsok, amelyekre az eszköz válaszol.

### <a name="add-a-device-template"></a>Eszközsablon hozzáadása

Az eszköz modelljét több lehetőség is felveszi a IoT Central alkalmazásba. Létrehozhat egy új modellt, egy modellt importálhat egy fájlból, vagy kiválaszthat egy eszközt az eszköz katalógusból. A IoT Central támogatja az *eszköz első* megközelítését is, amelyben az alkalmazás automatikusan importál egy modellt egy adattárból, amikor az első alkalommal csatlakozik a valódi eszközhöz.

Ebben a rövid útmutatóban egy eszközt választ ki az eszköz-katalógusból egy eszköz sablon létrehozásához.

A következő lépések bemutatják, hogyan importálhatja a modellt egy **ESP32** -eszközhöz az eszköz-katalógus használatával. Ezek az eszközök telemetria, például hőmérsékletet küldenek az alkalmazásnak:

1. Új sablon hozzáadásához válassza az **+ új** lehetőséget az **eszközök sablonjai** lapon.

1. A **típus kiválasztása** lapon görgessen lefelé, amíg meg nem találja a **ESP32-Azure IoT Kit** csempét az előre **konfigurált eszköz használata** szakaszban.

1. Válassza ki a **ESP32 – Azure IoT Kit** csempét, majd kattintson a **Next (tovább) gombra: Review (áttekintés**).

1. Az **Áttekintés** lapon válassza a **Létrehozás** lehetőséget.

1. Néhány másodperc elteltével megtekintheti az új eszköz sablonját:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="A ESP32 eszközhöz tartozó eszköz sablonját ábrázoló képernyőfelvétel":::

    A sablon neve **érzékelő vezérlő**. A modell olyan összetevőket tartalmaz, mint például az **érzékelő vezérlő**, a **SensorTemp** és az **eszköz információi felülete**. Az összetevők határozzák meg egy ESP32-eszköz képességeit. A képességek közé tartoznak a telemetria, a tulajdonságok és a parancsok.

### <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

Az eszközök tartalmazhatnak Felhőbeli tulajdonságokat is. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt. Két felhő tulajdonságainak hozzáadása:

1. Válassza a **felhő tulajdonságai** lehetőséget, majd a **+ felhő tulajdonságot**. A következő táblázatban található információk segítségével két Felhőbeli tulajdonságot adhat hozzá az eszköz sablonhoz:

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | Nincsenek          | Date   |
    | Ügyfél neve     | Nincsenek          | Sztring |

1. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Képernyőfelvétel – két felhő tulajdonságai":::

## <a name="views"></a>Megjelenítések

Építőként testreszabhatja az alkalmazást, hogy az eszközre vonatkozó releváns információkat jelenítsen meg az operátorral. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz csatlakoztatott eszközöket. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez, beleértve az általuk küldött telemetria is.

### <a name="default-views"></a>Alapértelmezett nézetek

Az alapértelmezett nézetek gyors módszert biztosítanak a fontos eszközök megjelenítésének megkezdéséhez. Az eszköz sablonjaihoz legfeljebb három alapértelmezett nézet hozható létre:

* A **parancsok** nézet lehetővé teszi, hogy az operátor parancsokat küldjön az eszközre.
* Az **Áttekintés** nézet diagramokat és mérőszámokat használ az eszközök telemetria megjelenítéséhez.
* A **Névjegy** nézet megjeleníti az eszköz tulajdonságait.

Válassza ki a **nézetek** csomópontot az eszköz sablonjában. Láthatja, hogy IoT Central a sablon hozzáadásakor **áttekintést** és a **Névjegy** nézetét hozta létre.

Új **felügyelt eszköz** űrlap hozzáadása, amelyet az operátor használhat az eszköz felügyeletére:

1. Válassza ki a **nézetek** csomópontot, majd válassza az **eszköz és a Felhőbeli** adatcsempe szerkesztése lehetőséget egy új nézet hozzáadásához.

1. Módosítsa az űrlap nevét az **eszköz kezelésére**.

1. Válassza ki az **ügyfél nevét** és az **utolsó szolgáltatás dátumának** Felhőbeli tulajdonságait, valamint a **cél hőmérséklet** tulajdonságot. Ezután válassza a **Hozzáadás szakaszt**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Az eszköz sablonja által hozzáadott új űrlapot bemutató képernyőfelvétel":::

1. Az új űrlap mentéséhez válassza a **Mentés** lehetőséget.

## <a name="publish-device-template"></a>Eszköz sablonjának közzététele

Szimulált eszköz létrehozása vagy valódi eszköz csatlakoztatása előtt közzé kell tennie az eszköz sablonját. Habár IoT Central közzétette a sablont, amikor először hozta létre, közzé kell tennie a frissített verziót.

Eszköz sablonjának közzététele:

1. Navigáljon az **érzékelő vezérlő** eszköz sablonhoz az **eszközök sablonjai** lapról.

1. **Közzététel** kiválasztása:

    :::image type="content" source="media/quick-create-simulated-device/published-model.png" alt-text="A közzétételi ikon helyét bemutató képernyőfelvétel":::

1. Az **eszköz sablonjának közzététele az alkalmazás** párbeszédpanelen válassza a **Közzététel** lehetőséget.

Miután közzétett egy sablont, az az **eszközök** lapon látható. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz modelljét új verzió létrehozása nélkül. A közzétett eszközökön azonban verziószámozás nélkül módosíthatók a felhő tulajdonságai, testreszabásai és nézetei. A módosítások elvégzése után válassza a **Közzététel**  lehetőséget a módosítások elküldéséhez az operátornak.

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Szimulált eszköz az alkalmazáshoz való hozzáadásához használja a létrehozott **ESP32** -sablont.

1. Új eszköz hozzáadása operátorként a bal oldali ablaktáblán válassza az **eszközök** lehetőséget. A **Devices (eszközök** ) lapon a ESP32-eszközhöz tartozó **összes eszköz** és az **érzékelő-vezérlő** eszköz sablonja látható. Válassza ki az **érzékelő vezérlőt**.

1. Szimulált fejlesztői készlet-eszköz hozzáadásához válassza az **+ új** lehetőséget. Használja a javasolt **eszköz azonosítóját** , vagy adja meg a sajátját. Az eszköz AZONOSÍTÓi betűket, számokat és karaktert tartalmazhatnak `-` . Megadhatja az új eszköz nevét is. Győződjön meg arról, hogy az **eszköz szimulálása** **Igen** értékre van állítva, majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="A szimulált érzékelő vezérlő eszközét bemutató képernyőkép":::

Mostantól a Builder által az eszköz sablonja által létrehozott nézeteket szimulált adatként használhatja:

1. Válassza ki a szimulált eszközt az **eszközök** lapon

    * Az **Áttekintés** nézetben látható a szimulált telemetria ábrázolása:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="A szimulált eszköz áttekintő lapját ábrázoló képernyőfelvétel":::

    * A **Névjegy** nézet a tulajdonságok értékeit jeleníti meg.

    * A **parancsok** nézet lehetővé teszi a parancsok futtatását, például az eszközön való **újraindítást** .

    * Az **eszközök kezelése** nézet a kezelő számára az eszköz kezeléséhez létrehozott űrlap.

    * A **nyers** adatnézet lehetővé teszi az eszköz által elküldhető nyers telemetria és tulajdonságértékek megtekintését. Ez a nézet az eszközök hibakereséséhez használható.

## <a name="use-a-simulated-device-to-improve-views"></a>A nézetek fejlesztése szimulált eszköz használatával

Miután létrehozta az új szimulált eszközt, a szerkesztő ezzel az eszközzel folytathatja a fejlesztést és az eszköz sablonra épülő nézeteit.

1. A bal oldali ablaktáblában válassza az **eszközök sablonok** lehetőséget, majd válassza ki az **érzékelő vezérlő** sablonját.

1. Válassza ki a szerkeszteni kívánt nézeteket, például az **áttekintést**, vagy hozzon létre egy új nézetet. Válassza az **előnézet eszköz konfigurálása** lehetőséget, majd **válasszon egy futó eszközről**. Itt dönthet úgy, hogy nem rendelkezik előnézeti eszközzel, a teszteléshez konfigurált valódi eszközzel vagy a IoT Centralba felvett meglévő eszközzel.

1. Válassza ki a szimulált eszközt a listában. Ezután válassza az **Alkalmaz** lehetőséget. Most már megtekintheti ugyanazt a szimulált eszközt az eszköz sablon nézetének létrehozási felületén. Ez a nézet diagramok és egyéb vizualizációk esetén hasznos.

    :::image type="content" source="media/quick-create-simulated-device/configure-preview.png" alt-text="A konfigurált előnézeti eszközt ábrázoló képernyőfelvétel":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy ESP32-eszközhöz egy **érzékelő-vezérlőt** , és hogyan adhat hozzá szimulált eszközt az alkalmazáshoz.

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök monitorozásáról, folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> [Szabályok és műveletek konfigurálása](./quick-configure-rules.md)
