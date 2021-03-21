---
title: Node.js-modulok használata
description: Megtudhatja, hogyan dolgozhat Node.js-modulokkal Azure App Service vagy Cloud Services használatakor.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: f61f08f32ebb2b721846d3c3017405af99421104
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95542218"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>A Node.js modulok használata az Azure alkalmazásokkal
Ez a dokumentum útmutatást nyújt Node.js modulok használatáról az Azure-ban üzemeltetett alkalmazásokkal. Útmutatást nyújt annak biztosításához, hogy az alkalmazás a modul egy adott verzióját használja, valamint natív modulokat használjon az Azure-ban.

Ha már ismeri a Node.js-modulok használatát, **package.jsbe** -és **npm-shrinkwrap.jsa** fájlokon, a következő információk gyors összefoglalást biztosítanak a cikkben tárgyalt témakörökből:

* Azure App Service a fájlok **package.jsének** megismerésére és **npm-shrinkwrap.jsére** , és a fájlok bejegyzései alapján is telepíthet modulokat.

* Az Azure Cloud Services a fejlesztői környezetre telepítendő összes modult, valamint a központi telepítési csomag részét képező **csomópont- \_ modulok** könyvtárat várja. Lehetővé teheti a modulok telepítésének támogatását a Cloud Services-on lévő fájlok **package.js** vagy **npm-shrinkwrap.js** használatával. Ehhez a konfigurációhoz azonban a Cloud Service-projektek által használt alapértelmezett parancsfájlok testreszabására van szükség. A környezet konfigurálásának példáját lásd: az [Azure indítási feladata a NPM-telepítés futtatásához a Node-modulok üzembe helyezésének elkerülése](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) érdekében

> [!NOTE]
> Az Azure Virtual Machines ebben a cikkben nem tárgyaljuk, mivel a virtuális gépek üzembe helyezési élménye függ a virtuális gép által üzemeltetett operációs rendszertől.
>
>

## <a name="nodejs-modules"></a>Node.js modulok
A modulok olyan betölthető JavaScript-csomagok, amelyek konkrét funkciókat biztosítanak az alkalmazás számára. A modulokat általában a **NPM** parancssori eszközzel kell telepíteni, azonban egyes modulok (például a HTTP-modul) a Core Node.js csomag részeként vannak megadva.

A modulok telepítésekor a rendszer a Node modules ( **csomópont- \_ modulok** ) könyvtárban tárolja azokat az alkalmazási könyvtár struktúrájának gyökerében. A **csomópont \_ moduljainak** könyvtára minden modulja saját könyvtárat tart fenn, amely az általa függő modulokat tartalmazza, és ez a viselkedés minden modul esetében ismétlődik, egészen a függőségi láncig. Ez a környezet lehetővé teszi, hogy a telepített modulok saját verzióra vonatkozó követelményekkel rendelkezzenek a modulokra vonatkozóan, amelyektől függnek, azonban nagy méretű címtár-struktúrát eredményezhet.

Ha az alkalmazás részeként helyezi üzembe a **Node \_** modules könyvtárat, a **package.js** vagy **npm-shrinkwrap.js** fájlon való összevetésével növeli a központi telepítés méretét, azonban garantálja, hogy az éles környezetben használt modulok verziói megegyeznek a fejlesztés során használt modulokkal.

### <a name="native-modules"></a>Natív modulok
Habár a legtöbb modul egyszerűen egyszerű szöveges JavaScript-fájlok, egyes modulok platform-specifikus bináris lemezképek. Ezek a modulok a telepítéskor, általában a Python és a Node-GYP használatával vannak lefordítva. Mivel az Azure Cloud Services az alkalmazás részeként üzembe helyezett **Node \_** modules mappa használatára támaszkodik, a telepített modulok részét képező natív moduloknak működniük kell egy felhőalapú szolgáltatásban, feltéve, hogy a telepítése és lefordítása egy Windows-fejlesztési rendszeren történt.

A Azure App Service nem támogatja az összes natív modult, és a modulok adott előfeltételekkel való fordításakor sikertelen lehet. Míg egyes népszerű modulok, például a MongoDB opcionális natív függőségekkel rendelkeznek, és ezek nélkül is működnek, két megkerülő megoldás sikeresnek bizonyult szinte minden jelenleg elérhető natív modul esetében:

* Futtassa a **NPM-telepítést** olyan Windows rendszerű gépen, amelyen telepítve van az összes natív modul előfeltétele. Ezután telepítse az alkalmazás részeként a létrehozott **Node \_** modules mappát Azure app Service.

  * A fordítás előtt győződjön meg arról, hogy a helyi Node.js telepítése megfelelő architektúrával rendelkezik, és a verzió a lehető leghamarabb elérhető az Azure-ban (az aktuális értékek a tulajdonságok **folyamat. Arch** és **Process. Version**).

* A Azure App Service konfigurálható egyéni bash-vagy rendszerhéj-parancsfájlok futtatására az üzembe helyezés során, így egyéni parancsok hajthatók végre, és pontosan konfigurálhatók a **NPM telepítésének** módja. A környezet konfigurálását bemutató videó: [Egyéni webhely-telepítési parancsfájlok a kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>package.jshasználata fájlon

A fájl **package.js** az alkalmazás által igényelt legfelső szintű függőségek megadására van szükség, hogy az üzemeltetési platform telepíteni tudja a függőségeket, ahelyett, hogy a telepítés részeként bele kellene foglalni a Node modules mappát. **\_** Az alkalmazás telepítése után a **NPM install** paranccsal elemezheti a **package.jsa** fájlban, és telepítheti az összes felsorolt függőséget.

A fejlesztés során a **--Save**, **--Save-dev** vagy a **--Save-opcionális** paramétereket használhatja a modulok telepítésekor, ha a modulhoz bejegyzést szeretne hozzáadni a **package.js** fájlból automatikusan. További információ: [NPM-install](https://docs.npmjs.com/cli/install).

A fájl **package.js** egyik lehetséges problémája, hogy csak a legfelső szintű függőségek verzióját adja meg. Az egyes telepített modulok esetleg nem határozzák meg, hogy milyen verziójú modulokra van szükség, és így lehetséges, hogy a fejlesztés során használttól eltérő függőségi láncot használ.

> [!NOTE]
> Ha a Azure App Servicere való központi telepítéskor a fájl <b>package.js</b> natív modulra hivatkozik, az alkalmazás a git használatával történő közzétételekor az alábbi példához hasonló hibaüzenet jelenhet meg:
>
> NPM ERR! module-name@0.6.0 telepítés: "Node-GYP configure build"
>
> NPM ERR! a "CMD"/c "" Node-GYP configure build "" nem sikerült 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>npm-shrinkwrap.jshasználata fájlon
A fájl **npm-shrinkwrap.js** a fájlpackage.jsa modul verziószámozási korlátozásait próbálja **meg** kezelni. Míg a fájl **package.js** csak a legfelső szintű modulok verzióit tartalmazza, a fájl **npm-shrinkwrap.js** a teljes modul függőségi láncának verziójának követelményeit tartalmazza.

Ha az alkalmazás készen áll az éles környezetben való használatra, zárolhatja a verziókra vonatkozó követelményeket, és létrehozhat egy **npm-shrinkwrap.js** fájlt a **NPM Shrinkwrap** parancs használatával. Ez a parancs a **Node \_** modules mappában jelenleg telepített verziókat fogja használni, és rögzíti ezeket a verziókat a fájl **npm-shrinkwrap.js** . Miután az alkalmazást telepítette az üzemeltetési környezetbe, a **NPM install** paranccsal elemezheti a **npm-shrinkwrap.jsa** fájlban, és telepítheti az összes felsorolt függőséget. További információ: [NPM-Shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ha a Azure App Servicere való központi telepítéskor a fájl <b>npm-shrinkwrap.js</b> natív modulra hivatkozik, az alkalmazás a git használatával történő közzétételekor az alábbi példához hasonló hibaüzenet jelenhet meg:
>
> NPM ERR! module-name@0.6.0 telepítés: "Node-GYP configure build"
>
> NPM ERR! a "CMD"/c "" Node-GYP configure build "" nem sikerült 1
>
>

## <a name="next-steps"></a>Következő lépések
Most, hogy megértette, hogyan használhatja Node.js modulokat az Azure-ban, megtudhatja, hogyan [adhatja meg a Node.js verzióját](https://github.com/squillace/nodejs-microservice), hogyan [hozhat létre és helyezhet üzembe egy Node.js-webalkalmazást](app-service/quickstart-nodejs.md), és [hogyan használhatja a Mac és Linux rendszerhez készült Azure Command-Line interfészt](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

További információk: [Node.js fejlesztői központ](/azure/developer/javascript/).

[specify the Node.js version]: ./app-service/overview.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo