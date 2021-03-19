---
title: Python Function-alkalmazások hibakeresése Azure Functions
description: Útmutató a Python-függvények hibakereséséhez.
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 9b9f5d389eda5d74e7e78cfcfa9a46fba7276cbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846037"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Az Azure Functions Python-hibáinak elhárítása

A következő lista a Python-függvények gyakori problémáira vonatkozó hibaelhárítási útmutatókat tartalmazza:

* [ModuleNotFoundError és ImportError](#troubleshoot-modulenotfounderror)
* [A "cygrpc" nem importálható](#troubleshoot-cannot-import-cygrpc)

## <a name="troubleshoot-modulenotfounderror"></a>A ModuleNotFoundError hibáinak megoldása

Ez a szakasz segítséget nyújt a modulokkal kapcsolatos hibák elhárításához a Python-függvény alkalmazásában. Ezek a hibák általában a következő Azure Functions hibaüzenetet eredményezik:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Ez a hiba akkor fordul elő, ha egy Python-függvény alkalmazás nem tud betölteni egy Python-modult. A hiba alapvető oka az alábbi problémák egyike:

- [A csomag nem található](#the-package-cant-be-found)
- [A csomag nem oldható meg a megfelelő Linux-kerékkel](#the-package-isnt-resolved-with-proper-linux-wheel)
- [A csomag nem kompatibilis a Python-értelmező verziójával](#the-package-is-incompatible-with-the-python-interpreter-version)
- [A csomag ütközik más csomagokkal](#the-package-conflicts-with-other-packages)
- [A csomag csak a Windows vagy macOS platformokat támogatja](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>Projektfájlok megtekintése

A probléma tényleges okának azonosításához le kell kérnie a Function alkalmazásban futó Python-projektfájlok fájljait. Ha nem rendelkezik a Project-fájlokkal a helyi számítógépen, a következő módokon kérheti le őket:

- Ha a Function alkalmazásnak van `WEBSITE_RUN_FROM_PACKAGE` alkalmazás-beállítása, és az értéke egy URL-cím, töltse le az URL-címet a böngészőjébe, és másolja be a fájlt.
- Ha a Function alkalmazás `WEBSITE_RUN_FROM_PACKAGE` be van állítva, és a értéke `1` , akkor navigáljon a `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` fájlhoz, és töltse le a legújabb `href` URL-címről.
- Ha a Function alkalmazás nem rendelkezik a fent említett alkalmazás-beállítással, keresse meg `https://<app-name>.scm.azurewebsites.net/api/settings` az URL-címet, és keresse meg a következőt: `SCM_RUN_FROM_PACKAGE` . A fájl letöltéséhez másolja és illessze be az URL-címet a böngészőjébe.
- Ha ezek egyike sem működik az Ön számára, navigáljon `https://<app-name>.scm.azurewebsites.net/DebugConsole` a tartalomhoz, és fedje fel a következőt: `/home/site/wwwroot` .

A cikk további részében a függvény alkalmazás tartalmának vizsgálatával, a kiváltó ok azonosításával és az adott probléma megoldásával kapcsolatos esetleges hibák elhárítása segíti a hiba lehetséges okait.

### <a name="diagnose-modulenotfounderror"></a>ModuleNotFoundError diagnosztizálása

Ez a szakasz a modulokkal kapcsolatos hibák lehetséges kiváltó okait részletezi. Miután kitalálta, hogy melyik a legvalószínűbb kiváltó ok, lépjen a kapcsolódó enyhítésre.

#### <a name="the-package-cant-be-found"></a>A csomag nem található

Tallózással keresse meg a következőt: `.python_packages/lib/python3.6/site-packages/<package-name>` vagy `.python_packages/lib/site-packages/<package-name>` . Ha a fájl elérési útja nem létezik, akkor ez a hiányzó elérési út valószínűleg a kiváltó ok.

Ha az üzembe helyezés során harmadik féltől származó vagy elavult eszközöket használ, a probléma oka lehet.

Lásd: [távoli buildek engedélyezése](#enable-remote-build) vagy [natív függőségek létrehozása](#build-native-dependencies) a mérsékléshez.

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>A csomag nem oldható meg a megfelelő Linux-kerékkel

Ugrás a következőre: `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` vagy `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . A kedvenc szövegszerkesztővel nyissa meg a **görgős** fájlt, és keresse meg a **címke:** szakaszt. Ha a címke értéke nem tartalmaz **Linux**-t, ez lehet a probléma.

A Python-függvények csak Linux rendszeren futnak az Azure-ban: a functions Runtime v2. x verziója a Debian stretch-on és az v3. x futtatókörnyezeten fut a Debian Buster-on. Az összetevőnek szerepelnie kell a megfelelő Linux bináris fájlokban. Ha `--build local` a jelölőt a Core Tools, a harmadik féltől származó vagy az elavult eszközök használatával használja, a régebbi bináris fájlok is használhatók.

Lásd: [távoli buildek engedélyezése](#enable-remote-build) vagy [natív függőségek létrehozása](#build-native-dependencies) a mérsékléshez.

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>A csomag nem kompatibilis a Python-értelmező verziójával

Ugrás a következőre: `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` vagy `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Egy szövegszerkesztővel nyissa meg a METAADAT-fájlt, és keresse meg az **osztályozók:** szakaszt. Ha a szakasz nem tartalmazza `Python :: 3` , `Python :: 3.6` , `Python :: 3.7` vagy, `Python :: 3.8` Ez azt jelenti, hogy a csomag verziószáma vagy túl régi, vagy valószínűleg a csomag már karbantartás alatt áll.

A Function alkalmazás Python-verzióját a [Azure Portal](https://portal.azure.com)is megtekintheti. Navigáljon a Function alkalmazáshoz, válassza az **erőforrás-kezelő** lehetőséget, és válassza az **Indítás** lehetőséget.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Nyissa meg a erőforrás-kezelő a Function alkalmazáshoz a portálon":::

Az Explorer betöltése után keresse meg a **LinuxFxVersion**, amely a Python-verziót mutatja.

Lásd: [a csomag frissítése a legújabb verzióra](#update-your-package-to-the-latest-version) , vagy [cserélje le a csomagot megfelelő](#replace-the-package-with-equivalents) értékre a mérsékléshez.

#### <a name="the-package-conflicts-with-other-packages"></a>A csomag ütközik más csomagokkal

Ha meggyőződött arról, hogy a csomag megfelelően van feloldva a megfelelő Linux-kerekekkel, akkor lehet, hogy ütközik más csomagokkal. Bizonyos csomagok esetében a PyPi-dokumentumok tisztázzák a nem kompatibilis modulokat. A (z) például a [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) következő utasítást tartalmazza:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

A csomag verziójának dokumentációját itt találja: `https://pypi.org/project/<package-name>/<package-version>` .

Lásd: [a csomag frissítése a legújabb verzióra](#update-your-package-to-the-latest-version) , vagy [cserélje le a csomagot megfelelő](#replace-the-package-with-equivalents) értékre a mérsékléshez.

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>A csomag csak a Windows vagy macOS platformokat támogatja

Nyissa meg a `requirements.txt` szövegszerkesztőben, és keresse meg a csomagot a alkalmazásban `https://pypi.org/project/<package-name>` . Egyes csomagok csak Windows vagy macOS platformokon futnak. Például a pywin32 csak Windows rendszeren fut.

A `Module Not Found` hiba akkor fordulhat elő, ha Windows vagy MacOS rendszert használ a helyi fejlesztéshez. A csomagot azonban nem lehet importálni a Azure Functionson, amely Linux-t használ futásidőben. Ezt valószínűleg a használatával lehet a `pip freeze` virtuális környezetnek a Windows vagy MacOS rendszerű gépről requirements.txtba exportálni a projekt inicializálásakor.

Lásd: [a csomag helyettesítése az egyenértékű](#replace-the-package-with-equivalents) vagy a [kézműves requirements.txtával ](#handcraft-requirementstxt) .

### <a name="mitigate-modulenotfounderror"></a>ModuleNotFoundError enyhítése

Az alábbiakban a modulokkal kapcsolatos problémák lehetséges enyhítését ismertetjük. A [fenti diagnosztika](#diagnose-modulenotfounderror) segítségével meghatározhatja, hogy mely megoldások közül próbálkozzon.

#### <a name="enable-remote-build"></a>Távoli Build engedélyezése

Győződjön meg arról, hogy a távoli Build engedélyezve van. Ennek módja a telepítési módszertől függ.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Győződjön meg arról, hogy a [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) -hoz készült Azure functions bővítmény legújabb verziója van telepítve. Ellenőrizze, hogy `.vscode/settings.json` létezik-e, és tartalmazza-e a beállítást `"azureFunctions.scmDoBuildDuringDeployment": true` . Ha nem, akkor hozza létre ezt a fájlt a `azureFunctions.scmDoBuildDuringDeployment` beállítás engedélyezésével és a projekt újbóli üzembe helyezésével.

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Győződjön meg arról, hogy a [Azure functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) legújabb verziója van telepítve. Nyissa meg a helyi függvény projekt mappáját, és használja `func azure functionapp publish <app-name>` a t a telepítéshez.

## <a name="manual-publishing"></a>[Manuális közzététel](#tab/manual)

Ha a csomagot manuálisan teszi közzé a `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` végponton, győződjön meg arról, hogy **a SCM_DO_BUILD_DURING_DEPLOYMENT** és a **ENABLE_ORYX_BUILD** is **igaz** értékre van állítva. További információ: az [Alkalmazásbeállítások használata](functions-how-to-use-azure-function-app-settings.md#settings).

---

#### <a name="build-native-dependencies"></a>Natív függőségek létrehozása

Győződjön meg arról, hogy a **Docker** és a [Azure functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases) legújabb verziója van telepítve. Nyissa meg a helyi függvény projekt mappáját, és használja `func azure functionapp publish <app-name> --build-native-deps` a t a telepítéshez.

#### <a name="update-your-package-to-the-latest-version"></a>A csomag frissítése a legújabb verzióra

Keresse meg a csomag legújabb verzióját, `https://pypi.org/project/<package-name>` és tekintse meg az **osztályozók:** szakaszt. A csomagnak vagy `OS Independent` `POSIX` `POSIX :: Linux` **operációs rendszerrel** kompatibilisnek kell lennie. Emellett a programozási nyelvnek tartalmaznia kell a, a, a `Python :: 3` `Python :: 3.6` vagy a `Python :: 3.7` `Python :: 3.8` .

Ha ezek helyesek, a csomagot a legújabb verzióra is frissítheti, ha módosítja a `<package-name>~=<latest-version>` requirements.txt lévő sort.

#### <a name="handcraft-requirementstxt"></a>Kézműves requirements.txt

Egyes fejlesztők a `pip freeze > requirements.txt` Python-csomagok listáját használják a fejlesztői környezetekhez. Habár ez a kényelem a legtöbb esetben működik, a platformok közötti üzembe helyezési forgatókönyvek során problémák merülhetnek fel, például a függvények helyi fejlesztése Windows vagy macOS rendszeren, de a Linuxon futó Function alkalmazásban való közzététel is lehetséges. Ebben az esetben a `pip freeze` helyi fejlesztési környezet nem várt operációsrendszer-specifikus függőségeket vagy függőségeket tud bevezetni. Ezek a függőségek megszakítják a Python Function alkalmazást Linux rendszeren való futtatáskor.

Az ajánlott eljárás az, ha az importálási utasítást a projekt forráskódjában lévő minden. a. a fájlból berequirements.txt jelöli. Ez garantálja, hogy a csomagok feloldása megfelelően kezelhető a különböző operációs rendszereken.

#### <a name="replace-the-package-with-equivalents"></a>A csomag helyettesítése a megfelelővel

Először is tekintse át a csomag legújabb verzióját a alkalmazásban `https://pypi.org/project/<package-name>` . Ennek a csomagnak általában saját GitHub-lapja van, a **problémák** szakasz a githubon és a keresés, ha a probléma kijavítva. Ha igen, frissítse a csomagot a legújabb verzióra.

Előfordulhat, hogy a csomag integrálva lett a [Python standard Library](https://docs.python.org/3/library/) -ba (például pathlib). Ha igen, mivel egy bizonyos Python-disztribúciót biztosítunk Azure Functions (Python 3,6, Python 3,7 és Python 3,8), el kell távolítani a csomagot a requirements.txt.

Ha azonban egy olyan problémával szembesül, amelyet nem rögzítettek, és Ön határidővel rendelkezik. Azt javasoljuk, hogy végezzen kutatást, és találjon egy hasonló csomagot a projekthez. A Python-Közösség általában a hasonló könyvtárak széles választékát kínálja.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>A hibakeresés nem tudja importálni a "cygrpc"

Ez a szakasz segítséget nyújt a Python-cygrpc kapcsolatos hibák elhárításában. Ezek a hibák általában a következő Azure Functions hibaüzenetet eredményezik:

> `Cannot import name 'cygrpc' from 'grpc._cython'`

Ez a hiba akkor fordul elő, ha egy Python-függvény alkalmazás nem indul el megfelelő Python-értelmező használatával. A hiba alapvető oka az alábbi problémák egyike:

- [A Python-értelmező nem egyezik az operációsrendszer-architektúrával](#the-python-interpreter-mismatches-os-architecture)
- [Azure Functions Python-feldolgozók nem támogatják a Python-tolmácsot](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>A "cygrpc" hivatkozási hiba diagnosztizálása

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>A Python-értelmező nem egyezik az operációsrendszer-architektúrával

Ezt valószínűleg a 32 bites Python-tolmács okozza a 64 bites operációs rendszerre.

Ha x64-es operációs rendszert használ, győződjön meg róla, hogy a Python 3,6, 3,7 vagy 3,8 tolmács is a 64 bites verzióban van.

A Python-értelmező bitszáma az alábbi parancsokkal tekintheti meg:

Windows PowerShellben: `py -c 'import platform; print(platform.architecture()[0])'`

UNIX-szerű rendszerhéj esetén: `python3 -c 'import platform; print(platform.architecture()[0])'`

Ha nem egyeznek a Python tolmács bitszáma és az operációs rendszer architektúrája között, töltsön le egy megfelelő Python-tolmácsot a [Python Software Foundation](https://python.org/downloads/release)szolgáltatásból.

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>Azure Functions Python-feldolgozók nem támogatják a Python-tolmácsot

A Azure Functions Python-feldolgozó csak a Python 3,6, 3,7 és 3,8 nyelveket támogatja.
Ellenőrizze, hogy a Python-értelmező megfelel-e a várt verziónak `py --version` a Windowsban vagy `python3 --version` a UNIX-szerű rendszerekben. Győződjön meg arról, hogy a visszatérési eredmény a Python 3.6. x, a Python 3.7. x vagy a Python 3.8. x.

Ha a Python-értelmező verziója nem felel meg az elvárásoknak, töltse le a Python 3,6, 3,7 vagy 3,8 tolmácsot a [Python Software Foundation](https://python.org/downloads/release)szolgáltatásból.

## <a name="next-steps"></a>Következő lépések

Ha nem tudja feloldani a problémát, jelentse a következőt a functions csapatnak:

> [!div class="nextstepaction"]
> [Megoldatlan probléma jelentése](https://github.com/Azure/azure-functions-python-worker/issues)
