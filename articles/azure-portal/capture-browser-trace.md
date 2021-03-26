---
title: Böngésző-nyomkövetés rögzítése hibaelhárításhoz
description: Hálózati információk rögzítése böngésző-nyomkövetésből a Azure Portal problémáinak elhárítása érdekében.
ms.date: 03/25/2021
ms.topic: troubleshooting
ms.openlocfilehash: 63bc3e93187274db607b5f60216de461729801dc
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563487"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Böngésző-nyomkövetés rögzítése hibaelhárításhoz

Ha hibaelhárítást végez a Azure Portalával kapcsolatban, és fel kell vennie a kapcsolatot a Microsoft ügyfélszolgálatával, javasoljuk, hogy először rögzítsen egy böngésző-nyomkövetést és néhány további információt. Az összegyűjtött információk a probléma bekövetkezésekor fontos adatokat adhatnak meg a portálról. Kövesse az ebben a cikkben található lépéseket a böngészőben használt fejlesztői eszközökhöz: Google Chrome vagy Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML), Apple Safari vagy Firefox.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome és Microsoft Edge (Chromium)

A Google Chrome és a Microsoft Edge (Chromium) egyaránt a [Chromium nyílt forráskódú projekten](https://www.chromium.org/Home)alapul. A következő lépések bemutatják, hogyan használhatja a fejlesztői eszközöket, amelyek nagyon hasonlóak a két böngészőben. További információ: [Chrome devtools](https://developers.google.com/web/tools/chrome-devtools) és [Microsoft Edge (Chromium) fejlesztői eszközök](/microsoft-edge/devtools-guide-chromium).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A nyomkövetés elindítása _előtt_ fontos bejelentkezni, hogy a nyomkövetés ne tartalmazzon bizalmas adatokat a bejelentkezéshez. 

1. Kezdje el rögzíteni a portálon végrehajtott lépéseket a [Step Recording](https://support.microsoft.com/help/22878/windows-10-record-steps)használatával.

1. A portálon Navigáljon arra a lépésre, ahol a probléma bekövetkezik.

1. Nyomja meg az F12 billentyűt, vagy válassza ki ![ a böngésző beállításai ikont megjelenítő képernyőképet.](media/capture-browser-trace/chromium-icon-settings.png) > **További eszközök**  >  **Fejlesztői eszközök**.

1. Alapértelmezés szerint a böngésző csak az aktuálisan betöltött oldal nyomkövetési adatait őrzi meg. Állítsa be a következő beállításokat, hogy a böngésző megtartsa az összes nyomkövetési információt, még akkor is, ha a Reprodukálási több oldalra is szüksége lesz:

    1. Válassza a **hálózat** fület, majd válassza a **napló megőrzése** lehetőséget.

          ![Képernyőfelvétel: a hálózat lapon megjelenő napló megőrzése beállítás.](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Válassza a **konzol** fület, válassza a **konzol beállításai** lehetőséget, majd válassza a **napló megőrzése** lehetőséget. A beállítások ablaktábla bezárásához válassza újra a **konzol beállításait** .

          ![Képernyőkép, amely kiemeli a napló megőrzése beállítást a konzol lapon.](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Válassza a **hálózat** fület, majd válassza a **hálózati napló rögzítésének leállítása** és a **Törlés** lehetőséget.

    ![Képernyőkép a "hálózati napló rögzítésének leállítása" és a "Clear"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Válassza a **hálózati napló rögzítése** lehetőséget, majd hozza létre újra a problémát a portálon.

    ![A hálózati napló rögzítését bemutató képernyőkép.](media/capture-browser-trace/chromium-start-session.png)

    A munkamenet kimenete az alábbi képhez hasonlóan jelenik meg.

    ![A munkamenet kimenetét bemutató képernyőkép.](media/capture-browser-trace/chromium-browser-trace-results.png)

1. A váratlan portál viselkedésének újralétrehozása után válassza a **hálózati napló rögzítése** lehetőséget, majd válassza a **har exportálása** lehetőséget, és mentse a fájlt.

    ![Képernyőkép, amely bemutatja, hogyan exportálhatja a HAR-t a hálózat lapon.](media/capture-browser-trace/chromium-network-export-har.png)

1. Állítsa le a lépések rögzítőjét, és mentse a rögzítést.

1. A böngésző fejlesztői eszközök ablaktábláján kattintson a **konzol** fülre. Kattintson a jobb gombbal az egyik üzenetre, majd válassza a **Mentés másként...** lehetőséget, majd mentse a konzol kimenetét egy szövegfájlba.

    ![Képernyőkép, amely kiemeli a konzol fület és a Mentés másként... menüben](media/capture-browser-trace/chromium-console-select.png)

1. Csomagolja ki a HAR-fájlt, a konzol kimenetét és a képernyő rögzítését tömörített formátumban (például. zip), és ossza meg a Microsoft ügyfélszolgálatával.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

A következő lépések bemutatják, hogyan használhatja a fejlesztői eszközöket a Microsoft Edge-ben (EdgeHTML). További információ: [Microsoft Edge (EdgeHTML) fejlesztői eszközök](/microsoft-edge/devtools-guide).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A nyomkövetés elindítása _előtt_ fontos bejelentkezni, hogy a nyomkövetés ne tartalmazzon bizalmas adatokat a bejelentkezéshez. 

1. Kezdje el rögzíteni a portálon végrehajtott lépéseket a [Step Recording](https://support.microsoft.com/help/22878/windows-10-record-steps)használatával.

1. A portálon Navigáljon arra a lépésre, ahol a probléma bekövetkezik.

1. Nyomja meg az F12 billentyűt, vagy válassza ![ a böngésző beállításai ikon képernyőképét.](media/capture-browser-trace/edge-icon-settings.png) > **További eszközök**  >  **Fejlesztői eszközök**.

1. Alapértelmezés szerint a böngésző csak az aktuálisan betöltött oldal nyomkövetési adatait őrzi meg. Állítsa be a következő beállításokat, hogy a böngésző megtartsa az összes nyomkövetési információt, még akkor is, ha a Reprodukálási több oldalra is szüksége lesz:

    1. Válassza a **hálózat** fület, majd törölje a **jelet a bejegyzések törlése a navigációs** menüben.

          ![Képernyőkép: "a navigált bejegyzések törlése"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Válassza a **konzol** fület, majd a **napló megőrzése** lehetőséget.

          ![A "napló megőrzése" képernyőkép](media/capture-browser-trace/edge-console-preserve-log.png)

1. Válassza a **hálózat** fület, majd kattintson a **profilkészítési munkamenet leállítása** és a **munkamenet törlése** elemre.

    ![Képernyőfelvétel: "profilkészítés leállítása" és "munkamenet törlése"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Válassza a **profilkészítés elindítása munkamenet** lehetőséget, majd reprodukálja a problémát a portálon.

    ![Képernyőkép a "profilkészítés indítása munkamenetről"](media/capture-browser-trace/edge-start-session.png)

    A munkamenet kimenete az alábbi képhez hasonlóan jelenik meg.

    ![A profilkészítési munkamenet kimenetét bemutató képernyőkép.](media/capture-browser-trace/edge-browser-trace-results.png)

1. A váratlan portál viselkedésének újrakészítése után válassza a **profilkészítés leállítása** lehetőséget, majd válassza az **Exportálás har-ként** lehetőséget, és mentse a fájlt.

    ![Képernyőkép az "Exportálás HAR-ként"](media/capture-browser-trace/edge-network-export-har.png)

1. Állítsa le a lépések rögzítőjét, és mentse a rögzítést.

1. A böngésző fejlesztői eszközök ablaktábláján kattintson a **konzol** lapfülre, és bontsa ki az ablakot. Vigye a kurzort a konzol kimenetének elejére, majd húzza a kimenet teljes tartalmát. Kattintson a jobb gombbal, majd válassza a **Másolás** lehetőséget, és mentse a konzol kimenetét egy szövegfájlba.

    ![Képernyőfelvétel: a másolási menü kiemelése.](media/capture-browser-trace/edge-console-select.png)

1. Csomagolja ki a HAR-fájlt, a konzol kimenetét és a képernyő rögzítését tömörített formátumban (például. zip), és ossza meg a Microsoft ügyfélszolgálatával.

## <a name="apple-safari"></a>Apple Safari

A következő lépések bemutatják, hogyan használhatja a fejlesztői eszközöket az Apple Safariban. További információ: a [Safari fejlesztői eszközök áttekintése](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. A fejlesztői eszközök engedélyezése az Apple Safariban:

    1. Válassza a **Safari**, majd a **Beállítások** lehetőséget.

        ![A Safari beállításainak képernyőképe](media/capture-browser-trace/safari-preferences.png)

    1. Válassza a **speciális** fület, majd a **menüsávon válassza a fejlesztés menü megjelenítése** lehetőséget.

        ![Képernyőkép a Safari speciális beállításairól](media/capture-browser-trace/safari-show-develop-menu.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A nyomkövetés elindítása _előtt_ fontos bejelentkezni, hogy a nyomkövetés ne tartalmazzon bizalmas adatokat a bejelentkezéshez. 

1. A portálon végrehajtott lépések rögzítésének megkezdése. További információ: [a képernyő rögzítése a Mac](https://support.apple.com/HT208721)számítógépen.

1. A portálon Navigáljon arra a lépésre, ahol a probléma bekövetkezik.

1. Válassza a **fejlesztés**, majd a **webes ellenőr megjelenítése** lehetőséget.

    ![Képernyőkép a "webes ellenőr megjelenítése"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Alapértelmezés szerint a böngésző csak az aktuálisan betöltött oldal nyomkövetési adatait őrzi meg. Állítsa be a következő beállításokat, hogy a böngésző megtartsa az összes nyomkövetési információt, még akkor is, ha a Reprodukálási több oldalra is szüksége lesz:

    1. Válassza a **hálózat** fület, majd válassza a **napló megőrzése** lehetőséget.

          ![A napló megőrzése lehetőséget megjelenítő képernyőkép.](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Válassza a **konzol** fület, majd a **napló megőrzése** lehetőséget.

          ![A konzol lapon látható megőrzési naplót megjelenítő képernyőkép.](media/capture-browser-trace/safari-console-preserve-log.png)

1. Válassza a **hálózat** fület, majd a **hálózati elemek törlése** elemet.

    ![Képernyőkép a "hálózati elemek törlése"](media/capture-browser-trace/safari-clear-session.png)

1. A probléma újbóli előállítása a portálon. A munkamenet kimenete az alábbi képhez hasonlóan jelenik meg.

    ![A probléma reprodukálása utáni kimenetet bemutató képernyőkép.](media/capture-browser-trace/safari-browser-trace-results.png)

1. Miután újrakészítette a váratlan portál viselkedését, válassza az **Exportálás** lehetőséget, és mentse a fájlt.

    ![Képernyőkép az "exportálásról"](media/capture-browser-trace/safari-network-export-har.png)

1. Állítsa le a képernyő-rögzítőt, és mentse a rögzítést.

1. A böngésző fejlesztői eszközök ablaktábláján kattintson a **konzol** lapfülre, és bontsa ki az ablakot. Vigye a kurzort a konzol kimenetének elejére, majd húzza a kimenet teljes tartalmát. A kimenet másolásához és szövegfájlba mentéséhez használja a-C parancsot.

    ![Képernyőkép, amely kiemeli a kimenet megtekintését és másolását.](media/capture-browser-trace/safari-console-select.png)

1. Csomagolja ki a HAR-fájlt, a konzol kimenetét és a képernyő rögzítését tömörített formátumban (például. zip), és ossza meg a Microsoft ügyfélszolgálatával.

## <a name="firefox"></a>Firefox

A következő lépések bemutatják, hogyan használhatja a fejlesztői eszközöket a Firefoxban. További információ: [Firefox fejlesztői eszközök](https://developer.mozilla.org/docs/Tools).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A nyomkövetés elindítása _előtt_ fontos bejelentkezni, hogy a nyomkövetés ne tartalmazzon bizalmas adatokat a bejelentkezéshez. 

1. A portálon végrehajtott lépések rögzítésének megkezdése. Használja a Windows rendszerhez tartozó [Step Recorder](https://support.microsoft.com/help/22878/windows-10-record-steps) -t, vagy tekintse [meg, hogyan rögzítheti a képernyőt a Mac](https://support.apple.com/HT208721)számítógépen.

1. A portálon Navigáljon arra a lépésre, ahol a probléma bekövetkezik.

1. Nyomja meg az F12 billentyűt, vagy válassza ![ a böngésző beállításai ikon ](media/capture-browser-trace/firefox-icon-settings.png)  >  **webfejlesztő**  >  **váltógomb bekapcsolása eszközét**.

1. Alapértelmezés szerint a böngésző csak az aktuálisan betöltött oldal nyomkövetési adatait őrzi meg. Állítsa be a következő beállításokat, hogy a böngésző megtartsa az összes nyomkövetési információt, még akkor is, ha a Reprodukálási több oldalra is szüksége lesz:

    1. Válassza a **hálózat** fület, majd válassza a **naplófájlok** megőrzése lehetőséget.

          ![Képernyőkép, amely kiemeli a naplófájlok megőrzése lehetőséget.](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. Válassza a **konzol** fület, válassza a **konzol beállításai** lehetőséget, majd válassza a **naplók** megőrzése lehetőséget.

          ![Képernyőkép a "naplók megőrzése"](media/capture-browser-trace/firefox-console-persist-logs.png)

1. Válassza a **hálózat** fület, majd válassza a **Törlés** lehetőséget.

    ![Képernyőkép a "Clear"](media/capture-browser-trace/firefox-clear-session.png)

1. A probléma újbóli előállítása a portálon. A munkamenet kimenete az alábbi képhez hasonlóan jelenik meg.

    ![A böngésző nyomkövetési eredményeinek képernyőképe](media/capture-browser-trace/firefox-browser-trace-results.png)

1. A váratlan portál viselkedésének reprodukálása után válassza az **összes mentése a har-ként** lehetőséget.

    ![Képernyőkép a "HAR exportálása"](media/capture-browser-trace/firefox-network-export-har.png)

1. Állítsa le a Windows vagy a képernyő rögzítését a Mac számítógépen, és mentse a rögzítést.

1. A böngésző fejlesztői eszközök ablaktábláján kattintson a **konzol** fülre. Kattintson a jobb gombbal az egyik üzenetre, majd válassza a **látható üzenetek exportálása a** következőre lehetőséget, majd mentse a konzol kimenetét egy szövegfájlba.

    ![A konzol kimenetének képernyőképe](media/capture-browser-trace/firefox-console-select.png)

1. Csomagolja ki a HAR-fájlt, a konzol kimenetét és a képernyő rögzítését tömörített formátumban (például. zip), és ossza meg a Microsoft ügyfélszolgálatával.

## <a name="next-steps"></a>Következő lépések

[Az Azure Portal áttekintése](azure-portal-overview.md)