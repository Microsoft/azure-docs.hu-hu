---
title: Üzembe helyezési technológiák a Azure Functions
description: Ismerje meg, milyen különböző módokon helyezhet üzembe kódokat Azure Functions.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: ca81067fa60836d77c4d8af121ebf415c772a1d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789212"
---
# <a name="deployment-technologies-in-azure-functions"></a>Üzembe helyezési technológiák a Azure Functions

A projektkód Azure-ban való üzembe helyezéséhez Azure Functions különböző technológiákat is használhat. Ez a cikk áttekintést nyújt az Elérhető üzembe helyezési módszerekről, és javaslatokat ad a különböző forgatókönyvekben használható legjobb módszerre. Emellett a mögöttes telepítési technológiák teljes listáját és fő részleteit is tartalmazza. 

## <a name="deployment-methods"></a>Üzembe helyezési módszerek

A kód Azure-ban való közzétételéhez használt üzembe helyezési technológiát általában az alkalmazás közzétételi módja határozza meg. A megfelelő üzembe helyezési módszert az adott igények és a fejlesztési ciklus pontjának határozzák meg. A fejlesztés és a tesztelés során például közvetlenül a fejlesztői eszközből, például a Visual Studio használatával helyezheti üzembe. Ha az alkalmazás éles környezetben van, nagyobb a valószínűsége annak, hogy folyamatosan közzétesz egy forrásvezérlőt vagy egy automatizált közzétételi folyamatot, amely további ellenőrzést és tesztelést is tartalmaz.  

Az alábbi táblázat a függvényprojekthez elérhető üzembe helyezési módszereket ismerteti.

| Központi &nbsp; telepítési típus | Metódusok | A legjobb... |
| -- | -- | -- |
| Eszközalapú | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; &nbsp; Code-közzététel](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio közzététele](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[A Core Tools közzététele](functions-run-local.md#publish) | Üzembe helyezés a fejlesztés és más alkalmi telepítések során. Az üzembe helyezéseket helyileg, az eszközök kezelik. | 
| App Service felügyelt| &bull;&nbsp;[Üzembe &nbsp; helyezési &nbsp; központ (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Tárolók &nbsp; üzembe helyezése](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Folyamatos üzembe helyezés (CI/CD) a forrásvezérlőből vagy egy tároló-beállításjegyzékből. Az üzembe helyezéseket a App Service (Kudu) kezeli.|
| Külső folyamatok|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub-műveletek](functions-how-to-github-actions.md) | Az éles és DevOps-folyamatok, amelyek további ellenőrzést, tesztelést és más műveleteket tartalmaznak, az automatikus üzembe helyezés részeként futnak. Az üzembe helyezéseket a folyamat kezeli. |

Bár egyes Functions-üzemelő példányok a környezetük alapján a legjobb technológiát használják, a legtöbb üzembe helyezési módszer a [zip-alapú üzembe helyezésen alapul.](#zip-deploy)

## <a name="deployment-technology-availability"></a>Üzembe helyezési technológia rendelkezésre állása

Azure Functions támogatja a platformfüggetlen helyi fejlesztést és üzemeltetést Windows és Linux rendszeren. Jelenleg három üzemeltetési csomag érhető el:

+ [Használat](consumption-plan.md)
+ [Prémium](functions-premium-plan.md)
+ [Dedikált (App Service)](dedicated-plan.md)

Minden tervnek más a viselkedése. Nem minden üzembe helyezési technológia érhető el az egyes Azure Functions. Az alábbi diagramon látható, hogy mely telepítési technológiák támogatottak az operációs rendszer és a üzemeltetési csomag egyes kombinációihoz:

| Üzembe helyezési technológia | Windows-használat | Windows Premium | Windows Dedicated  | Linux-használat | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Külső csomag<sup>URL-címe 1</sup> |✔|✔|✔|✔|✔|✔|
| Zip üzembe helyezése |✔|✔|✔|✔|✔|✔|
| Docker-tároló | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Verziókövetés |✔|✔|✔| |✔|✔|
| Helyi Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Felhőalapú szinkronizálás<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portálszerkesztés |✔|✔|✔| |✔<sup>2.</sup>|✔<sup>2.</sup>|

<sup>1.</sup> Központi telepítési technológia, amely manuális [eseményindító-szinkronizálást igényel.](#trigger-syncing)
<sup>2</sup> A portálszerkesztés csak a Prémium és Dedikált csomagokat használó Linux-függvények HTTP- és időzítő eseményindítóihoz engedélyezett.

## <a name="key-concepts"></a>Fő fogalmak

Egyes alapvető fogalmak kritikus fontosságúak az üzemelő példányok Azure Functions.

### <a name="trigger-syncing"></a>Eseményindító szinkronizálása

Az eseményindítók módosításakor a Functions-infrastruktúrának értesülnie kell a változásokról. Számos központi telepítési technológia esetében a szinkronizálás automatikusan megtörténik. Bizonyos esetekben azonban manuálisan kell szinkronizálnia az eseményindítókat. Ha külső csomag URL-címére, helyi Gitre, felhőszinkronizálásra vagy FTP-re hivatkozva telepíti a frissítéseket, manuálisan kell szinkronizálnia az eseményindítókat. Az eseményindítók háromféleképpen szinkronizálhatóak:

* Indítsa újra a függvényalkalmazást a Azure Portal
* Küldjön egy HTTP POST kérést a következő címre `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` a [főkulcs használatával:](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Küldjön egy HTTP POST kérést a következőnek: `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Cserélje le a helyőrzőket az előfizetés azonosítójára, az erőforráscsoport nevére és a függvényalkalmazás nevére.

### <a name="remote-build"></a>Távoli build

Azure Functions automatikusan végrehajthat buildeket a zip-telepítés után kapott kódra. Ezek a buildek némileg eltérően viselkednek attól függően, hogy az alkalmazás Windows vagy Linux rendszeren fut-e. A távoli buildek nem hajthatóak végre, ha egy alkalmazás korábban Futtatás csomagból módban [való futtatásra lett beállítva.](run-functions-from-deployment-package.md) Ha meg szeretne ismerkedni a távoli build használatával, lépjen a [zip deploy (zip üzembe helyezése) lapra.](#zip-deploy)

> [!NOTE]
> Ha problémái vannak a távoli buildeltel, annak az lehet az oka, hogy az alkalmazás a funkció elérhetővé válik (2019. augusztus 1.) előtt lett létrehozva. Próbáljon meg létrehozni egy új függvényalkalmazást, vagy a futtatásával frissíteni a `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` függvényalkalmazást. Ez a parancs két próbálkozásra lehet sikeres.

#### <a name="remote-build-on-windows"></a>Távoli build Windows rendszeren

A Windows rendszeren futó összes függvényalkalmazás rendelkezik egy kis méretű felügyeleti alkalmazással, az SCM (vagy [Kudu) webhelyével.](https://github.com/projectkudu/kudu) Ez a hely kezeli az üzembe helyezési és buildlogikát a Azure Functions.

Amikor telepít egy alkalmazást a Windowsban, nyelvspecifikus parancsokat futtat, például `dotnet restore` a (C#) vagy `npm install` a (JavaScript) parancsokat.

#### <a name="remote-build-on-linux"></a>Távoli build Linuxon

A linuxos távoli build [](functions-how-to-use-azure-function-app-settings.md#settings) engedélyezéséhez a következő alkalmazásbeállításokat kell beállítani:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Alapértelmezés szerint a [Azure Functions Core Tools](functions-run-local.md) és a [Azure Functions Code-hoz](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) Visual Studio bővítmény is távoli buildeket hajt végre a Linuxon való üzembe helyezéskor. Emiatt mindkét eszköz automatikusan létrehozza ezeket a beállításokat az Azure-ban.

Ha az alkalmazások távolról, Linux rendszeren vannak felépítve, [a telepítőcsomagból futnak.](run-functions-from-deployment-package.md)

##### <a name="consumption-plan"></a>Használatalapú csomag

A használatban álló csomagon futó Linux-függvényalkalmazások nem tartalmaznak SCM/Kudu-helyet, ami korlátozza az üzembe helyezési lehetőségeket. A használat alapján csomagként futó Linuxon futó függvényalkalmazások azonban támogatják a távoli buildeket.

##### <a name="dedicated-and-premium-plans"></a>Dedikált és Prémium csomagok

A Linuxon futó függvényalkalmazások a [Dedikált (App Service)](dedicated-plan.md) csomag és a [Prémium](functions-premium-plan.md) csomag esetében szintén korlátozott SCM/Kudu-webhelyet kínálnak.

## <a name="deployment-technology-details"></a>Üzembe helyezési technológia részletei

A következő üzembe helyezési módszerek érhetők el a Azure Functions.

### <a name="external-package-url"></a>Külső csomag URL-címe

Külső csomag URL-címével hivatkozhat a függvényalkalmazást tartalmazó távoli csomagfájlra (.zip). A fájl a megadott URL-címről lesz letöltve, és az alkalmazás Futtatás [csomagból módban](run-functions-from-deployment-package.md) fut.

>__Hogyan használható:__ Adja [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) hozzá az et az alkalmazásbeállításokhoz. Ennek a beállításnak egy URL-címnek kell lennie (a futtatni kívánt csomagfájl helye). A beállításokat a portálon [vagy](functions-how-to-use-azure-function-app-settings.md#settings) az Azure CLI használatával [használhatja.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)
>
>Ha az Azure Blob Storage-et használja, használjon egy közös hozzáférésű [jogosultságjelet (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) tartalmazó privát tárolót, amely hozzáférést biztosít a Functions számára a csomaghoz. Amikor az alkalmazás újraindul, lekéri a tartalom egy másolatát. A hivatkozásnak az alkalmazás teljes élettartama alatt érvényesnek kell lennie.

>__Mikor használható:__ A külső csomag URL-címe az egyetlen támogatott üzembe helyezési módszer Azure Functions, amely Linuxon fut a használat alapján, ha a felhasználó nem szeretné, hogy távoli [build](#remote-build) útjára ússunk. Amikor frissíti a függvényalkalmazás által hivatkozó [](#trigger-syncing) csomagfájlt, manuálisan kell szinkronizálnia az eseményindítókat, hogy az Azure-nak meg tudja mondani az alkalmazás változását.

### <a name="zip-deploy"></a>Zip üzembe helyezése

A zip-alapú üzembe helyezés használatával a függvényalkalmazást tartalmazó .zip-fájl leküldése az Azure-ba. Ha szükséges, beállíthatja, hogy [](run-functions-from-deployment-package.md)az alkalmazás elindul a csomagból, vagy megadhatja, hogy távoli [buildre kerül](#remote-build) sor.

>__Hogyan használható:__ Üzembe helyezés kedvenc ügyféleszközével: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), vagy a parancssorból a [Azure Functions Core Tools.](functions-run-local.md#project-file-deployment) Alapértelmezés szerint ezek az eszközök zip-telepítést használnak, és [a csomagból futtatják a következőt:](run-functions-from-deployment-package.md). A Core Tools és Visual Studio Code bővítmény egyaránt lehetővé teszi a [távoli build](#remote-build) használatát a Linuxon való üzembe helyezéskor. .zip-fájl a függvényalkalmazásban való manuális üzembe helyezéséhez kövesse a Deploy from a .zip file or URL (Üzembe helyezés .zip-fájlból vagy [URL-címről) útmutatását.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

>A zip deploy használatával való üzembe helyezéskor beállíthatja, hogy az alkalmazás [a csomagból fusson.](run-functions-from-deployment-package.md) A csomagból való futtatáshoz állítsa az [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) alkalmazásbeállítás értékét `1` értékre. Javasoljuk a zip-fájl üzembe helyezését. Gyorsabb betöltési időket eredményez az alkalmazásoknál, és ez az alapértelmezett érték a VS Code, a Visual Studio és az Azure CLI esetében.

>__Mikor használható:__ A zip üzembe helyezés az ajánlott üzembe helyezési technológia Azure Functions.

### <a name="docker-container"></a>Docker-tároló

Üzembe helyezhet egy Linux-tároló rendszerképet, amely tartalmazza a függvényalkalmazást.

>__Hogyan használható:__ Hozzon létre egy Linux-függvényalkalmazást a prémium vagy dedikált csomagban, és adja meg, hogy melyik tároló rendszerképből fusson. Ezt két módon teheti meg:
>
>* Hozzon létre egy Linux-függvényalkalmazást egy Azure App Service csomagon a Azure Portal. A **Közzététel beállításhoz** válassza a **Docker-rendszerkép** lehetőséget, majd konfigurálja a tárolót. Adja meg a rendszerkép helyének helyét.
>* Hozzon létre egy Linux-függvényalkalmazást egy App Service az Azure CLI használatával. További információ: [Függvény létrehozása Linux rendszeren egyéni rendszerkép használatával.](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)
>
>Egy meglévő alkalmazás egyéni tárolóval való [](functions-run-local.md)üzembe helyezéséhez használja a Azure Functions Core Tools [`func deploy`](functions-run-local.md#publish) paranccsal.

>__Mikor használható:__ Akkor használja a Docker-tároló lehetőséget, ha nagyobb irányításra van szüksége a függvényalkalmazást tartalmazó Linux-környezet felett. Ez az üzembe helyezési mechanizmus csak a Linuxon futó függvények számára érhető el.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy packages and deploys your Windows applications to any IIS server, including your function apps running on Windows in Azure.

>__Hogyan használható:__ A [Visual Studio eszközök használatával Azure Functions.](functions-create-your-first-function-visual-studio.md) Törölje a **Futtatás csomagfájlból (ajánlott) jelölőnégyzet** jelölését.
>
>A Web [Deploy 3.6-ot](https://www.iis.net/downloads/microsoft/web-deploy) is letöltheti, és közvetlenül `MSDeploy.exe` hívhatja a-t.

>__Mikor használható:__ A Web Deploy támogatott, és nem tartalmaz problémákat, de az előnyben részesített mechanizmus a zip üzembe helyezése, ha a [Futtatás csomagból engedélyezve van.](#zip-deploy) További tudnivalókért lásd a Visual Studio [útmutatót.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Verziókövetés

A verzióvezérlővel csatlakoztassa a függvényalkalmazást egy Git-adattárhoz. Az adattár kódfrissítése elindítja az üzembe helyezést. További információkért lásd a [Kudu wikit.](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)

>__Hogyan használható:__ A portál Függvények területén használja az Üzembe helyezési központot a forrásvezérlőből való közzététel beállításához. További információ: Folyamatos üzembe [helyezés a Azure Functions.](functions-continuous-deployment.md)

>__Mikor használható:__ A forrásvezérlés használata az ajánlott eljárás a függvényalkalmazásaikon együttműködő csapatok számára. A forrásvezérlés jó üzembe helyezési lehetőség, amely kifinomultabb üzembe helyezési folyamatokat tesz lehetővé.

### <a name="local-git"></a>Helyi Git

A helyi Git használatával kódot is lekérhetők a helyi gépről Azure Functions Git használatával.

>__Hogyan használható:__ Kövesse a [Local Git deployment (Helyi Git üzemelő példány) útmutatását a Azure App Service.](../app-service/deploy-local-git.md)

>__Mikor használható:__ Általában egy másik üzembe helyezési módszer használatát javasoljuk. Amikor a helyi Gitből tesz közzé adatokat, manuálisan kell szinkronizálnia [az eseményindítókat.](#trigger-syncing)

### <a name="cloud-sync"></a>Felhőalapú szinkronizálás

A Cloud Sync használatával szinkronizálhatja a Dropboxból és a OneDrive-ból származó tartalmakat az Azure Functions.

>__Hogyan használható:__ Kövesse a Tartalom [szinkronizálása felhőmappában útmutatását.](../app-service/deploy-content-sync.md)

>__Mikor használható:__ Általánosságban más üzembe helyezési módszerek használatát javasoljuk. Ha felhőalapú szinkronizálással teszi közzé a közzétételt, manuálisan kell [szinkronizálnia az eseményindítókat.](#trigger-syncing)

### <a name="ftp"></a>FTP

Az FTP használatával közvetlenül továbbíthat fájlokat a Azure Functions.

>__Hogyan használható:__ Kövesse a Tartalom üzembe [helyezése FTP/s használatával rész utasításait.](../app-service/deploy-ftp.md)

>__Mikor használható:__ Általánosságban más üzembe helyezési módszerek használatát javasoljuk. Ftp használatával való közzétételkor manuálisan kell szinkronizálnia az [eseményindítókat.](#trigger-syncing)

### <a name="portal-editing"></a>Portálszerkesztés

A portálalapú szerkesztőben közvetlenül szerkesztheti a függvényalkalmazásban található fájlokat (lényegében minden alkalommal üzembe helyezheti őket, amikor menti a módosításokat).

>__Hogyan használható:__ A függvények szerkesztéséhez a Azure Portal kell létrehoznia a függvényeket a [portálon.](./functions-get-started.md) Egyetlen igazságforrás megőrzése érdekében bármely más üzembe helyezési módszer használata csak olvashatóvé teszi a függvényt, és megakadályozza a portál folyamatos szerkesztését. Ha vissza szeretne térni egy olyan állapotba, amelyben szerkesztheti a fájlokat a Azure Portal, manuálisan visszatérhet a szerkesztési módhoz, és eltávolíthatja a központi telepítéssel kapcsolatos alkalmazásbeállításokat `Read/Write` [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) (például: ).

>__Mikor használható:__ A portál jó módszer a Azure Functions. Az intenzívebb fejlesztési munka érdekében javasoljuk, hogy használja az alábbi ügyféleszközök egyikét:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (parancssor)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

Az alábbi táblázat a portál szerkesztését támogató operációs rendszereket és nyelveket mutatja be:

| Nyelv | Windows-használat | Windows Premium | Windows Dedicated | Linux-használat | Linux Premium | Dedikált Linux |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#-szkript |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (előzetes verzió) | | | | | | |
| PowerShell (előzetes verzió) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> A portálszerkesztés csak HTTP- és időzítő eseményindítókhoz engedélyezett Linux-függvények esetén, prémium és dedikált csomagokkal.

## <a name="deployment-behaviors"></a>Üzembe helyezési viselkedések

Az üzembe helyezés során az összes meglévő végrehajtás befejeződhet vagy időkorrektálhat, ami után a rendszer betölti az új kódot a kérések feldolgozásának megkezdéséhez.

Ha nagyobb kontrollra van szüksége az átállás felett, használjon üzembehelyeket.

## <a name="deployment-slots"></a>Üzembe helyezési pontok

Amikor üzembe helyez egy függvényalkalmazást az Azure-ban, üzembe helyezhet egy különálló üzembe helyezési üzembe helyezési helyet, nem pedig közvetlenül az éles környezetben. Az üzembe helyezési tárolóhelyekkel kapcsolatos további információkért tekintse meg az [üzembehely Azure Functions üzembehely helyezési](functions-deployment-slots.md) tárolóhelyek dokumentációját.

## <a name="next-steps"></a>Következő lépések

A függvényalkalmazások üzembe helyezéséről az alábbi cikkekben talál további információt:

+ [Folyamatos üzembe helyezés Azure Functions](functions-continuous-deployment.md)
+ [Folyamatos teljesítés az Azure DevOps használatával](functions-how-to-azure-devops.md)
+ [Zip-példányok Azure Functions](deployment-zip-push.md)
+ [A Azure Functions futtatása csomagfájlból](run-functions-from-deployment-package.md)
+ [A függvényalkalmazás erőforrás-üzembe helyezésének automatizálása a Azure Functions](functions-infrastructure-as-code.md)
