---
title: 'Rövid útmutató: az első függvény létrehozása az Azure-ban a Visual Studióval'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és tehet közzé egy HTTP-trigger Azure-függvényt a Visual Studio használatával.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050120"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rövid útmutató: az első függvény létrehozása az Azure-ban a Visual Studióval

Ebben a cikkben a Visual Studióval hozzon létre egy C# szintű függvénytár-alapú függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően telepítse azt a <abbr title="Egy futásidejű számítástechnikai környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazások fejlesztői számára, ami leegyszerűsíti a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezet <abbr title="Egy Azure-szolgáltatás, amely alacsony költséghatékonyságú, kiszolgáló nélküli számítási környezetet biztosít az alkalmazásokhoz.">Azure Functions</abbr>.

A rövid útmutató elvégzésével néhány USD értékű vagy annál kisebb költséggel jár a <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">Azure-fiók</abbr>.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

+ Azure létrehozása <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">account</abbr> aktív <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Telepítse a [Visual Studio 2019](https://azure.microsoft.com/downloads/) alkalmazást, és válassza ki az **Azure-fejlesztési** számítási feladatot a telepítés során. 

![A Visual Studio telepítése az Azure-fejlesztési számítási feladattal](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>Ehelyett használjon Azure Functions projektet</strong></summary>
Ha létre szeretne hozni egy <abbr title="Egy vagy több, együtt telepíthető és felügyelhető különálló függvény logikai tárolója.">Azure Functions projekt</abbr> a Visual Studio 2017 használatával Ehelyett először telepítenie kell a [legújabb Azure functions eszközöket](functions-develop-vs.md#check-your-tools-version).
</details>

## <a name="2-create-a-function-app-project"></a>2. Function alkalmazás-projekt létrehozása

1. A Visual Studio menüjében válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

1. A **create a New Project (új projekt létrehozása**) mezőbe írja be a *functions* kifejezést a keresőmezőbe, válassza ki a **Azure functions** sablont, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** területen adja meg a **<abbr title=" Function app nevét C#-névtérként, ezért ne használjon aláhúzást, kötőjelet vagy más nem alfanumerikus karaktereket. "> </abbr>** A projekthez tartozó projekt neve, majd válassza a **Létrehozás** lehetőséget. 

1. Adja meg a következő információkat az **új Azure functions alkalmazás-beállítások létrehozásához** :

    + Válassza **<abbr title=" ezt az értéket olyan Function projekt létrehozása, amely az Azure functions 3. x futtatókörnyezetét használja, amely támogatja a .net Core 3. x. Azure functions 1. x verziója támogatja a .NET-keretrendszert. "> Azure Functions v3 (.NET Core) </abbr>** a functions Runtime legördülő menüből. (További információ: [Azure functions Runtime-verziók áttekintése](functions-versions.md).)
    
    + Válassza **<abbr title=" ezt az értéket, ha egy HTTP-kérelem által aktivált függvényt hoz létre. "> HTTP- </abbr> trigger** a függvény sablonja.
    
    + Válassza ki, hogy egy **<abbr title=" Azure-függvényhez szükség van egy Storage-fiókra, az egyiket a rendszer a projekt az Azure-ba való közzétételekor rendeli hozzá A HTTP-triggerek nem használnak Azure Storage-fiókhoz tartozó kapcsolatok karakterláncát; minden más trigger-típushoz érvényes Azure Storage-fiókhoz tartozó összekapcsolási karakterlánc szükséges. "> Storage- </abbr> emulátor** a Storage-fiók legördülő listából.
        
    + Válassza a **Névtelen** lehetőséget a <abbr title="A létrehozott függvényt bármely ügyfél elindíthatja, kulcs megadása nélkül. Ez az engedélyezési beállítás megkönnyíti az új függvény tesztelését.">Engedélyszint</abbr> legördülő. (A kulcsokkal és az engedélyezéssel kapcsolatos további információkért lásd: [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys) és [http-és webhook-kötések](functions-bindings-http-webhook.md).)

    + Kattintson a **Létrehozás** elemre.
        
## <a name="3-rename-the-function"></a>3. nevezze át a függvényt

A `FunctionName` Method attribútum a függvény nevét állítja be, amely alapértelmezés szerint a következőképpen jön létre: `Function1` . Mivel az eszközök nem teszik lehetővé, hogy a projekt létrehozásakor felülbírálja az alapértelmezett függvény nevét, szánjon egy percet a Function osztály, a fájl és a metaadatok jobb nevének létrehozásához.

1. A **fájlkezelőben** kattintson a jobb gombbal a Function1.cs fájlra, és nevezze át a *HttpExample.cs*.

1. A kódban nevezze át a Function1 osztályt a következőre: "HttpExample".

1. A `HttpTrigger` nevű metódusban `Run` nevezze át a `FunctionName` Method attribútumot a következőre: `HttpExample` .


## <a name="4-run-the-function-locally"></a>4. a függvény helyi futtatása

1. A függvény futtatásához nyomja le az <kbd>F5</kbd> billentyűt a Visual Studióban.

1. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába. Fűzze hozzá a lekérdezési karakterláncot **? név =<YOUR_NAME>** ehhez az URL-címhez, majd futtassa a kérelmet. 

    ![A függvény által visszaadott localhost válasz a böngészőben](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. A hibakeresés leállításához nyomja le az F5 <kbd>billentyűt</kbd> + <kbd></kbd> a Visual Studióban.

<br/>
<details>
<summary><strong>Hibaelhárítás</strong></summary>
 Előfordulhat, hogy engedélyeznie kell egy tűzfal-kivételt, hogy az eszközök kezelni tudják a HTTP-kérelmeket. A függvény helyi futtatásakor a rendszer soha nem kényszeríti ki az engedélyezési szinteket.
</details>

## <a name="5-publish-the-project-to-azure"></a>5. a projekt közzététele az Azure-ban

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **cél** lapon válassza az **Azure** lehetőséget.

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Azure-cél kiválasztása":::

1. **Adott cél** esetében válassza az **Azure függvényalkalmazás (Windows) lehetőséget.**

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Azure-függvényalkalmazás kiválasztása":::

1. A **Function példányban** válassza az **új Azure-függvény létrehozása...** lehetőséget, majd használja az alábbi értékeket:

    + A **név** a következőt adja meg: <abbr title="Használjon olyan nevet, amely egyedileg azonosítja az új Function alkalmazást. Fogadja el ezt a nevet, vagy adjon meg egy új nevet. Érvényes karakterek: `a-z` , `0-9` és `-` ..">Globálisan egyedi név</abbr>
    
    + **Válasszon** egy előfizetést a legördülő listából.
    
    + **Válasszon ki** egy meglévőt <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyeket egységként lehet kezelni.">erőforráscsoport</abbr> a legördülő listából, vagy az **új** elemre kattintva hozzon létre egy új erőforráscsoportot.
    
    + **Kiválasztás** <abbr title="Ha a projektet egy felhasználási csomagban futó Function alkalmazásban teszi közzé, csak a functions-alkalmazás végrehajtásához kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak.">Használat</abbr> a lejátszás típusa legördülő menüből. (További információ: [felhasználási terv](consumption-plan.md).)
    
    + **Válasszon ki** egy  <abbr title="Földrajzi hivatkozás egy adott Azure-adatközpontra, amelyben az erőforrások le vannak foglalva. Az elérhető régiók listájáért lásd: [régiók](https://azure.microsoft.com/regions/) .">location</abbr> a legördülő menüből.
    
    + **Válasszon ki** egy <abbr = "az Azure Storage-fiókra a functions Runtime szükséges. Válassza az új lehetőséget az általános célú Storage-fiók konfigurálásához. Kiválaszthat egy meglévő fiókot is, amely megfelel a Storage-fiókra vonatkozó követelményeknek. " Azure Storage ></abbr> fiók a legördülő menüből

    ![A Create App Service (App Service létrehozása) párbeszédpanel](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Kattintson a **Létrehozás** elemre. 

1. Győződjön meg arról, hogy a **functions példányban** a **Futtatás a csomagból fájl** van bejelölve. 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Profil létrehozásának befejezése":::

    <br/>
    <details>
    <summary><strong>Mit tesz ez a beállítás?</strong></summary>
    A **csomag fájlból való futtatásakor** a Function alkalmazás a [zip-telepítéssel](functions-deployment-technologies.md#zip-deploy) van telepítve, és a [csomagon](run-functions-from-deployment-package.md) belüli mód engedélyezve van. Ez az ajánlott üzembe helyezési módszer a functions-projekthez, mert jobb teljesítményt eredményez.    
    </details>   

1. Válassza a **Befejezés** gombot.

1. A Közzététel lapon válassza a **Közzététel** elemet.

1. A közzétételi lapon tekintse át a Function alkalmazás gyökerének URL-címét.

1. A közzététel lapon válassza a **kezelés a <abbr title=" Cloud Explorerben lehetőséget, hogy a Visual Studio használatával megtekintheti a hely tartalmát, elindíthatja és leállíthatja a Function alkalmazást, és közvetlenül böngészhet az Azure-beli és a Azure Portalban található alkalmazás-erőforrások működéséhez. "> Cloud Explorer </abbr>**.
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Sikeres közzétételt jelző üzenet":::
    

## <a name="6-test-your-function-in-azure"></a>6. a függvény tesztelése az Azure-ban

1. A Cloud Explorerben ki kell választania az új Function alkalmazást. Ha nem, bontsa ki az előfizetést, bontsa ki **app Services** és válassza ki az új Function alkalmazást.

1. Kattintson a jobb gombbal a Function alkalmazásra, és válassza a **Megnyitás böngészőben** lehetőséget. Ekkor megnyílik a Function alkalmazás gyökere az alapértelmezett böngészőben, és megjeleníti azt a lapot, amely azt jelzi, hogy a Function alkalmazás fut. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Futó Function alkalmazás":::

1. A böngészőben a címsorban fűzze hozzá a **/API/HttpExample? Name = functions** karakterláncot az alap URL-címhez, és futtassa a kérést.

    A HTTP-trigger függvényt meghívó URL-cím formátuma a következő:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Nyissa meg ezt az URL-címet, és megjelenik a függvény által visszaadott távoli GET kérelemre adott válasz, amely a következő példához hasonlóan néz ki:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="A függvény által visszaadott válasz a böngészőben":::

## <a name="7-clean-up-resources"></a>7. erőforrások tisztítása

Törölje a Function alkalmazást és annak erőforrásait, hogy elkerülje a további költségek felmerülését.

1. A Cloud Explorerben bontsa ki az előfizetést, bontsa ki a **app Services** elemet, kattintson a jobb gombbal a Function alkalmazásra, majd válassza a **Megnyitás a portálon** lehetőséget. 

1. A Function alkalmazás lapon válassza az **Áttekintés** lapot, majd válassza ki az **erőforráscsoport** területen található hivatkozást.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Válassza ki a függvény alkalmazás oldaláról törölni kívánt erőforráscsoportot.":::

1. Az **erőforráscsoport** lapon tekintse át a befoglalt erőforrások listáját, és ellenőrizze, hogy a törölni kívántak-e.
 
1. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

    A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan adhat hozzá egy <abbr title="Azt jelenti, hogy a függvényt egy tárolási várólistához kell rendelni, hogy üzeneteket lehessen létrehozni a várólistán. A kötések a függvények és más erőforrások közötti deklaratív kapcsolatok. A bemeneti kötés adatokat biztosít a függvénynek; a kimeneti kötések adatokat biztosítanak a függvénytől más erőforrásoknak.">Azure Storage-üzenetsor kimeneti kötése</abbr> a függvényhez:

> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs.md)
