---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Ismerkedjen meg az IoT-megoldás létrehozásához, kezeléséhez és Azure IoT Central felhasználói felületének fő területeivel.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: ff795281b3471d5c6f57397edf5a52e0df9bbd32
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589119"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ismerkedés az Azure IoT Central kezelőfelületével

Ez a cikk a Microsoft Azure IoT Central kezelőfelületét mutatja be. A kezelőfelületet használhatja Azure IoT Central-megoldások és az azokhoz csatlakozó eszközök létrehozásához, felügyeletéhez és használatához.

## <a name="iot-central-homepage"></a>IoT Central kezdőlapja

A [IoT Central](https://aka.ms/iotcentral-get-started) kezdőlapján további információt talál a IoT Central elérhető legújabb hírekről és funkciókról, új alkalmazásokat hozhat létre, valamint meglévő alkalmazásokat láthat és elindítat.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central kezdőlapja":::

### <a name="create-an-application"></a>Alkalmazás létrehozása

A Build (Felépítés) szakaszban böngészhet az iparági IoT Central sablonok listájában, vagy egy egyéni alkalmazássablonnal kezdhet az nulláról.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central buildoldal":::

További tudnivalókért tekintse meg [a Create an Azure IoT Central application (Új](quick-deploy-iot-central.md) alkalmazás létrehozása) rövid útmutatót.

### <a name="launch-your-application"></a>Az alkalmazás elindítása

Az alkalmazás IoT Central az alkalmazás létrehozásakor kiválasztott URL-címre navigálva indítsa el a saját alkalmazását. Az alkalmazáskezelőben az összes olyan alkalmazás listáját is láthatja, amelyekhez [hozzáféréssel IoT Central rendelkezik.](https://aka.ms/iotcentral-apps)

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central app manager":::

## <a name="navigate-your-application"></a>Navigálás az alkalmazásban

Az IoT-alkalmazáson belül a bal oldali panelen férhet hozzá a különböző területekhez. A bal oldali panelt kibonthatja vagy összecsukhatja a panel tetején található háromsoros ikonra kattintva:

> [!NOTE]
> A bal oldali panelen látható elemek a felhasználói szerepkörtől függenek. További információ a [felhasználók és szerepkörök kezeléséről.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="bal oldali panel":::

  :::column-end:::
  :::column span="2":::
     **Az irányítópultok** az összes alkalmazás- és személyes irányítópultot megjelenítik. 
     
     **Az** eszközök lehetővé teszik a csatlakoztatott eszközök valós és szimulált kezelését.

     **Az eszközcsoportok** segítségével megtekintheti és létrehozhatja a lekérdezés által megadott eszközgyűjteményeket. Az eszközcsoportok az alkalmazásban tömeges műveletek végrehajtásához használhatók.

     **A** szabályok segítségével szabályokat hozhat létre és szerkeszthet az eszközök figyelése érdekében. A szabályok kiértékelése az eszköz telemetrián alapul, és testre szabható műveleteket vált ki.

     **Az elemzés** lehetővé teszi az eszközök telemetriai adatainak grafikus megjelenítését.

     **A** feladatok tömeges műveletek futtatásával lehetővé teszik az eszközök nagy léptékű kezelését.

     **Az eszközsablonokkal** hozhatja létre és kezelheti az alkalmazáshoz csatlakozó eszközök jellemzőit.

     **Az adatexportokkal** konfigurálhatja a külső szolgáltatásokba ( például tárolókba és üzenetsorokbe) történő folyamatos exportálást.

     **Az** adminisztrációval kezelheti az alkalmazás beállításait, testreszabását, számlázását, felhasználóit és szerepköreit.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Keresés, súgó, téma és támogatás

Minden oldalon megjelenik a felső menü:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central eszköztár":::

* Az eszközök kereséséhez adjon meg egy **Keresési** értéket.
* A felhasználói felület nyelvének vagy témájának módosításához válassza a **Beállítások ikont.** További információ az [alkalmazásbeállítások kezeléséről](howto-manage-preferences.md)
* Ha segítségre és támogatásra van szüksége, kattintson a **Súgó** elemre, amely megnyitja az erőforrások legördülő listáját. Az [alkalmazással kapcsolatos információkat az](./howto-get-app-info.md) About your app (Tudnivalók az **alkalmazásról) hivatkozáson keresztül kaphatja** meg. Az ingyenes díjcsomagban elérhető alkalmazások támogatási erőforrásai közé tartozik az élő [csevegéshez való hozzáférés.](howto-show-hide-chat.md)
* Az alkalmazásból való kijelentkezhez válassza a **Fiók ikont.**

A kezelőfelületen egy világos és egy sötét téma közül választhat:

> [!NOTE]
> A világos és a sötét témák közötti választás nem érhető el, ha a rendszergazda egyéni témát konfigurált az alkalmazáshoz.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="A Téma IoT Central képernyőképe.":::

### <a name="dashboard"></a>Irányítópult

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Az irányítópult IoT Central képernyőképe.":::

* Az irányítópult az első oldal, amely akkor látható, amikor bejelentkezik a Azure IoT Central alkalmazásba. Több alkalmazás-irányítópultot is létrehozhat és testre szabhat. További információ [csempék irányítópulthoz való hozzáadásáról](howto-add-tiles-to-your-dashboard.md)

* Személyes irányítópultokat is létre lehet hozva az Ön számára fontos adatok figyeléseként. További tudnivalókért tekintse meg [a Személyes Azure IoT Central irányítópultok létrehozása útmutatót.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Eszközök

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Az Eszközök lap képernyőképe.":::

Az Explorer lapon az _alkalmazáshoz_ Azure IoT Central eszközsablon szerint _csoportosított eszközök megjelenik._ 

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban.

További tudnivalókért lásd az eszközök [figyelése rövid](./quick-monitor-devices.md) útmutatót. 

### <a name="device-groups"></a>Device groups

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Eszközcsoport oldal":::

Az eszközcsoport kapcsolódó eszközök gyűjteménye. Eszközcsoportok használatával tömeges műveleteket hajt végre az alkalmazásban. További tudnivalókért tekintse meg az Eszközcsoportok használata a saját [Azure IoT Central alkalmazásban cikket.](tutorial-use-device-groups.md)

### <a name="rules"></a>Szabályok
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Képernyőkép a Szabályok lapról.":::

A Szabályok lapon szabályokat határozhat meg az eszközök telemetriára, állapotára vagy eseményeire alapozva. Amikor egy szabály aktiválódik, elindíthat egy vagy több műveletet, például e-mailt küld, webhook-riasztásokon keresztül értesíthet egy külső rendszert stb. További tudnivalókért lásd a szabályok [konfigurálását ismertető](tutorial-create-telemetry-rules.md) oktatóanyagot. 

### <a name="analytics"></a>Elemzés

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Az Elemzés oldal képernyőképe.":::

Az elemzési oldal lehetővé teszi az eszközök telemetriai adatainak grafikus megjelenítését egy idősorozatban. További tudnivalókért tekintse meg a Create analytics for your Azure IoT Central application (Elemzések létrehozása saját [Azure IoT Central alkalmazáshoz) cikket.](howto-create-analytics.md)

### <a name="jobs"></a>Feladatok

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Feladatok lap":::

A Feladatok lapon tömeges műveleteket futtathat eszközein. Frissítheti az eszköztulajdonságokat, a beállításokat, és parancsokat hajthat végre az eszközcsoportokon. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md) cikket.

### <a name="device-templates"></a>Eszközsablonok

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Az Eszközsablonok képernyőképe.":::

Az eszközsablonok lapon hozhatja létre és kezelheti az alkalmazásban található eszközsablonokat. Az eszközsablonok az eszközök jellemzőinek meghatározása, például:

* Telemetria-, állapot- és eseménymérések
* Tulajdonságok
* Parancsok
* Megjelenítések

További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](howto-set-up-template.md) ismertető szakaszt. 

### <a name="data-export"></a>Adatexportálás

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Adatexport":::

Az adatexportelés lehetővé teszi adatstreamek beállítását külső rendszerekbe. További tudnivalókért tekintse meg az [Adatok exportálása](./howto-export-data.md) a Azure IoT Central cikkben.

### <a name="administration"></a>Felügyelet

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Az IoT-felügyelet képernyőképe.":::

A felügyeleti lapon konfigurálhatja és testre szabhatja IoT Central alkalmazást. Itt módosíthatja az alkalmazás nevét, URL-címét, a címeket, kezelheti a felhasználókat és a szerepköröket, API-jogkivonatokat hozhat létre, és exportálhatja az alkalmazást. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md) ismertető szakaszt.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md) ismertető rövid útmutató elvégzése.
