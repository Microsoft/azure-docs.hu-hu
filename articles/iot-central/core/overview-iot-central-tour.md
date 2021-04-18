---
title: Ismerkedés az Azure IoT Central kezelőfelületével | Microsoft Docs
description: Ismerkedjen meg az IoT-megoldás létrehozásához, kezeléséhez és Azure IoT Central felhasználói felületének fő területeivel.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 564e7d3d2353661d0a69cbd15ad8b281defacf87
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600567"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Ismerkedés az Azure IoT Central kezelőfelületével

Ez a cikk bemutatja a Azure IoT Central felhasználói felületét. A felhasználói felületen létrehozhat, kezelhet és használhat egy IoT Central csatlakoztatott eszközeit.

## <a name="iot-central-homepage"></a>IoT Central kezdőlapja

A [IoT Central](https://aka.ms/iotcentral-get-started) kezdőlapján további információt talál a IoT Central elérhető legújabb hírekről és funkciókról, új alkalmazásokat hozhat létre, valamint meglévő alkalmazásokat láthat és elindítat.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central kezdőlapja":::

### <a name="create-an-application"></a>Alkalmazás létrehozása

A Build (Felépítés) szakaszban böngészhet az iparági IoT Central sablonok listájában, vagy egy egyéni alkalmazássablonnal kezdheti az egészet.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central buildoldal":::

További tudnivalókért tekintse meg [a Create an Azure IoT Central application (Új](quick-deploy-iot-central.md) alkalmazás létrehozása) rövid útmutatót.

### <a name="launch-your-application"></a>Az alkalmazás elindítása

Az alkalmazás IoT Central az alkalmazás létrehozásakor kiválasztott URL-címre navigálva indítja el. Az alkalmazáskezelőben az összes olyan alkalmazás listáját is láthatja, amelyekhez [hozzáféréssel IoT Central rendelkezik.](https://aka.ms/iotcentral-apps)

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central app manager":::

## <a name="navigate-your-application"></a>Navigálás az alkalmazásban

Miután belépett az IoT-alkalmazásba, a bal oldali panelen különböző funkciókhoz férhet hozzá. A bal oldali panelt kibonthatja vagy összecsukhatja a panel tetején található háromsoros ikonra kattintva:

> [!NOTE]
> A bal oldali panelen látható elemek a felhasználói szerepkörtől függenek. További információ a [felhasználók és szerepkörök kezeléséről.](howto-manage-users-roles.md) 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="bal oldali panel":::

  :::column-end:::
  :::column span="2":::
     **Az irányítópultok** az összes alkalmazás- és személyes irányítópultot megjelenítik. 
     
     **Az** eszközök lehetővé teszik az összes eszköz kezelését.

     **Az eszközcsoportok** segítségével megtekintheti és létrehozhatja a lekérdezés által megadott eszközgyűjteményeket. Az eszközcsoportok az alkalmazásban tömeges műveletek végrehajtásához használhatók.

     **A** szabályok segítségével szabályokat hozhat létre és szerkeszthet az eszközök figyelése érdekében. A szabályok az eszközadatok alapján vannak kiértékelve, és testre szabható műveleteket aktiválnak.

     **Az** elemzés gazdag képességeket biztosít az előzménytrendek elemzéséhez és az eszközök különböző telemetrikáihoz való összefüggésekhez.

     **A** feladatok tömeges műveletek futtatásával lehetővé teszik az eszközök nagy léptékű kezelését.

     **Az eszközsablonokkal** létrehozhatja és kezelheti az alkalmazáshoz csatlakozó eszközök jellemzőit.

     **Az adatexportokkal** konfigurálhatja a külső szolgáltatásokba, például tárolókba és üzenetsorokbe történő folyamatos exportálást.

     **Az** adminisztráció lehetővé teszi az alkalmazás beállításainak, testreszabásának, számlázásának, felhasználóinak és szerepkörének kezelését.

     **Saját alkalmazások** segítségével visszaugorhat a IoT Central alkalmazáskezelőhöz.
     
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

* Az irányítópult az első oldal, amely akkor látható, amikor bejelentkezik a IoT Central alkalmazásba. Több alkalmazás-irányítópultot is létrehozhat és testre szabhat. További információ [csempék irányítópulthoz való hozzáadásáról](howto-add-tiles-to-your-dashboard.md)

* Személyes irányítópultokat is létre lehet hozva az Ön számára fontos adatok figyeléseként. További tudnivalókért tekintse meg [a Személyes Azure IoT Central irányítópultok létrehozása útmutatót.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Eszközök

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Az Eszközök lap képernyőképe.":::

Ezen az oldalon az alkalmazáshoz IoT Central eszközsablon szerint _csoportosított eszközök megjelenik._ 

* Az eszközsablonok egy-egy olyan eszköztípust határoznak meg, amelyek csatlakozhatnak az alkalmazáshoz.
* Egy eszköz egy valós vagy szimulált eszközt jelöl az alkalmazásban.

További tudnivalókért lásd az eszközök [figyelése rövid](./quick-monitor-devices.md) útmutatót. 

### <a name="device-groups"></a>Device groups

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Eszközcsoport oldal":::

Ezen az oldalon eszközcsoportokat hozhat létre és IoT Central alkalmazásban. Eszközcsoportokat használhat tömeges műveletek végrehajtásához az alkalmazásban vagy az adatok elemzéséhez. További tudnivalókért tekintse meg az Eszközcsoportok használata a saját [Azure IoT Central alkalmazásban cikket.](tutorial-use-device-groups.md)

### <a name="rules"></a>Szabályok
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Képernyőkép a Szabályok lapról.":::

Ezen az oldalon eszközadatokon alapuló szabályokat lehet megtekinteni és létrehozni. Amikor egy szabály aktiválódik, elindíthat egy vagy több műveletet, például e-mailt küldhet vagy webhookot hívhat meg. További tudnivalókért lásd a szabályok [konfigurálását ismertető](tutorial-create-telemetry-rules.md) oktatóanyagot. 

### <a name="analytics"></a>Elemzés

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Az Elemzés oldal képernyőképe.":::

Az elemzés számos képességet biztosít az előzménytrendek elemzéséhez és az eszközök különböző telemetrikáihoz való korrelál. További tudnivalókért tekintse meg a Create analytics for your Azure IoT Central application (Elemzések létrehozása saját [Azure IoT Central alkalmazáshoz) cikket.](howto-create-analytics.md)

### <a name="jobs"></a>Feladatok

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Feladatok lap":::

Ezen az oldalon megtekintheti és létrehozhatja az eszközökön a tömeges eszközkezelési műveletekhez használható feladatokat. Frissítheti az eszköztulajdonságokat, a beállításokat, és parancsokat hajthat végre az eszközcsoportokon. További tudnivalókért tekintse meg a [Feladat futtatása](howto-run-a-job.md) cikket.

### <a name="device-templates"></a>Eszközsablonok

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Az Eszközsablonok képernyőképe.":::

Az eszközsablonok lapon megtekintheti és létrehozhatja az alkalmazásban az eszközsablonokat. További tudnivalókért lásd az [új eszköztípus az Azure IoT Central-alkalmazásokban történő definiálását](howto-set-up-template.md) ismertető szakaszt. 

### <a name="data-export"></a>Adatexportálás

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Adatexport":::

Az adatexportelés lehetővé teszi adatstreamek beállítását külső rendszerekbe. További tudnivalókért tekintse meg az [Adatok exportálása](./howto-export-data.md) a Azure IoT Central cikkben.

### <a name="administration"></a>Felügyelet

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Az IoT-felügyelet képernyőképe.":::

A felügyeleti lapon konfigurálhatja és testre szabhatja IoT Central alkalmazást. Itt módosíthatja az alkalmazás nevét, URL-címét, a címeket, kezelheti a felhasználókat és a szerepköröket, API-jogkivonatokat hozhat létre, és exportálhatja az alkalmazást. További tudnivalókért lásd az [Azure IoT Central-alkalmazások adminisztrációját](howto-administer.md) ismertető szakaszt.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure IoT Central jellemzőit, és megismerkedett a kezelőfelület elrendezésével, a javasolt következő lépés az [Azure IoT Central-alkalmazás létrehozását](quick-deploy-iot-central.md) ismertető rövid útmutató elvégzése.
