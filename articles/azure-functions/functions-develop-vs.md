---
title: Azure-függvények fejlesztése a Visual Studióval
description: Megtudhatja, hogyan fejlesztheti és tesztelheti Azure Functionseket a Visual Studio 2019 Azure Functions Tools használatával.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 877c82e375b0ea469071402b83fadbd634177f3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97655815"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Azure-függvények fejlesztése a Visual Studióval  

A Visual Studio lehetővé teszi a C# Class Library-függvények fejlesztését, tesztelését és üzembe helyezését az Azure-ban. Ha ez a felhasználói élmény Azure Functions, tekintse meg a [Azure functions bemutatása](functions-overview.md)című témakört.

A Visual Studio a következő előnyöket biztosítja a függvények fejlesztésekor: 

* Függvények szerkesztése, létrehozása és futtatása a helyi fejlesztői számítógépen. 
* Tegye közzé a Azure Functions projektet közvetlenül az Azure-ban, és szükség szerint hozzon létre Azure-erőforrásokat. 
* C#-attribútumok használatával deklarálhatja a függvény kötéseit közvetlenül a C#-kódban.
* Előre lefordított C# függvények fejlesztése és üzembe helyezése. Az előzetesen teljesített függvények jobb hidegindító teljesítményt nyújtanak, mint a C# parancsfájl-alapú függvények. 
* A függvények C# nyelven való kódolása a Visual Studio-fejlesztés összes előnyének kihasználásával. 

Ez a cikk részletesen ismerteti, hogyan használható a Visual Studio a C# Class Library-függvények fejlesztéséhez és az Azure-ban való közzétételéhez. A cikk elolvasása előtt érdemes lehet befejezni a [Visual Studio functions](functions-create-your-first-function-visual-studio.md)rövid útmutatóját. 

Hacsak másként nincs jelezve, az eljárások és a példák a Visual Studio 2019-re vonatkoznak. 

## <a name="prerequisites"></a>Előfeltételek

- Azure Functions eszközök. Az Azure Function Tools hozzáadásához vegye fel az **Azure-fejlesztési** számítási feladatokat a Visual Studio-telepítésbe. Azure Functions eszközök a Visual Studio 2017-től kezdődően az Azure-fejlesztési számítási feladatban érhetők el.

- A közzétételi folyamat során a szükséges egyéb erőforrások, például az Azure Storage-fiókok az előfizetésben jönnek létre.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> A Visual Studio 2017-es verziójában az Azure-fejlesztési munkaterhelés külön bővítményként telepíti Azure Functions eszközöket. A Visual Studio 2017 telepítésének frissítésekor ellenőrizze, hogy a Azure Functions eszközök [legújabb verzióját](#check-your-tools-version) használja-e. A következő részben bemutatjuk, hogyan lehet megtekinteni és (ha szükséges) frissíteni a Azure Functions Tools bővítményt a Visual Studio 2017-ben. 
>
> Ha a Visual Studio 2019-et használja, ugorja át ezeket a szakaszt.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Tekintse meg az eszközök verzióját a Visual Studio 2017-ben

1. A **Tools (eszközök** ) menüben válassza a **bővítmények és frissítések** lehetőséget. Bontsa ki a **telepített**  >  **eszközök** csomópontot, majd válassza a **Azure functions és a web Jobs-eszközök** elemet.

    ![A functions Tools verziójának ellenőrzése](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Jegyezze fel a telepített **verziót** , és hasonlítsa össze ezt a verziót a [kibocsátási megjegyzésekben](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)felsorolt legújabb verzióval. 

1. Ha régebbi verziójú, frissítse az eszközöket a Visual Studióban, ahogy az a következő szakaszban is látható.

### <a name="update-your-tools-in-visual-studio-2017"></a>Eszközök frissítése a Visual Studio 2017-ban

1. A **bővítmények és frissítések** párbeszédpanelen bontsa ki a **frissítések**  >  **Visual Studio Marketplace**, majd a **Azure functions és a webjobs-eszközök** elemet, és válassza a **frissítés** lehetőséget.

    ![A functions Tools verziójának frissítése](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Miután letöltötte az eszközök frissítését, válassza a **Bezárás** lehetőséget, majd a Visual Studió bezárásával aktiválja az eszközök frissítését a VSIX Installer használatával.

1. A VSIX-telepítőben válassza a **módosítás** lehetőséget az eszközök frissítéséhez. 

1. A frissítés befejezése után kattintson a **Bezárás** gombra, majd indítsa újra a Visual studiót.

> [!NOTE]  
> A Visual Studio 2019-es és újabb verzióiban a Azure Functions Tools bővítmény a Visual Studio részeként frissül.  

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Miután létrehozta a Azure Functions projektet, a Project sablon létrehoz egy C# projektet, telepíti a `Microsoft.NET.Sdk.Functions` NuGet csomagot, és beállítja a célként megadott keretrendszert. Az új projekt a következő fájlokat tartalmazhatja:

* **host.json**: lehetővé teszi a functions gazdagép konfigurálását. Ezek a beállítások mind a helyi, mind az Azure-beli futtatáskor érvényesek. További információ: [host.json Reference](functions-host-json.md).

* **local.settings.json**: megtartja a függvények helyi futtatásakor használt beállításokat. Ezek a beállítások nem használhatók az Azure-ban való futtatáskor. További információ: [Local Settings fájl](#local-settings-file).

    >[!IMPORTANT]
    >Mivel a local.settings.jsfájl tartalmazhat titkokat, ki kell zárnia azt a projekt forrása vezérlőelemből. Győződjön meg arról, hogy a fájl **Másolás a kimeneti könyvtárba** beállítás értéke a **Másolás, ha újabb**. 

További információ: [functions Class Library Project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

A Visual Studio nem tölti fel automatikusan a local.settings.jsbeállításait a projekt közzétételekor. Ha meg szeretné győződni arról, hogy ezek a beállítások az Azure-beli Function alkalmazásban is léteznek, töltse fel őket a projekt közzététele után. További információ: [Function app Settings](#function-app-settings). A `ConnectionStrings` gyűjteményben lévő értékek soha nem lesznek közzétéve.

A kód emellett környezeti változókként is beolvashatja a Function app Settings értékeit. További információ: [környezeti változók](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-your-build-output-settings"></a>A Build kimeneti beállításainak konfigurálása

Azure Functions projektek létrehozásakor a build-eszközök optimalizálja a kimenetet, így a függvények futtatókörnyezetének egyetlen példánya is megmarad. Az eredmény egy optimalizált Build, amely annyi helyet takarít meg, amennyire csak lehetséges. Ha azonban a projekt egyik szerelvényének újabb verziójára helyez át, előfordulhat, hogy a build-eszközök nem tudják, hogy ezeket a szerelvényeket meg kell őrizni. Annak ellenőrzéséhez, hogy ezek a szerelvények megmaradnak-e az optimalizálási folyamat során, megadhatja őket `FunctionsPreservedDependencies` a projekt (. csproj) fájljában található elemek használatával:

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>A projekt konfigurálása helyi fejlesztéshez

A functions futtatókörnyezet belsőleg használ egy Azure Storage-fiókot. A HTTP és webhookok kivételével az összes trigger típushoz állítsa a `Values.AzureWebJobsStorage` kulcsot egy érvényes Azure Storage-fiók kapcsolati karakterláncára. A Function alkalmazás az [Azure Storage emulatort](../storage/common/storage-use-emulator.md) is használhatja a `AzureWebJobsStorage` projekthez szükséges kapcsolatbeállításokat. Az emulátor használatához állítsa a értékét a következőre `AzureWebJobsStorage` : `UseDevelopmentStorage=true` . Az üzembe helyezés előtt módosítsa a beállítást egy tényleges Storage-fiók kapcsolódási karakterláncára.

A Storage-fiók kapcsolódási karakterláncának beállítása:

1. A Visual Studióban válassza a  >  **Cloud Explorer** megtekintése lehetőséget.

2. A **Cloud Explorerben** bontsa ki a **Storage-fiókok** csomópontot, majd válassza ki a Storage-fiókját. A **Tulajdonságok** lapon másolja az **elsődleges kapcsolatok karakterláncának** értékét.

2. A projektben nyissa meg a local.settings.jsfájlt, és állítsa be a `AzureWebJobsStorage` kulcs értékét a másolt kapcsolódási karakterláncra.

3. Az előző lépés megismétlésével egyedi kulcsokat adhat hozzá a `Values` tömbhöz a függvények által igényelt egyéb kapcsolatokhoz. 

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

A C# Class Library függvények esetében a függvény által használt kötések a kódban szereplő attribútumok alkalmazásával definiálhatók. Amikor létrehozza a függvény eseményindítóit a megadott sablonokból, az eseményindító attribútumai lesznek alkalmazva. 

1. **Megoldáskezelő** kattintson a jobb gombbal a projekt csomópontjára, és válassza az  >  **új elem hozzáadása elemet**. 

2. Válassza az **Azure-függvény** lehetőséget, adja meg az osztály **nevét** , majd válassza a **Hozzáadás** lehetőséget.

3. Válassza ki az triggert, állítsa be a kötési tulajdonságokat, majd kattintson **az OK gombra**. A következő példa a várólista-tároló trigger funkciójának létrehozási beállításait mutatja be. 

    ![Üzenetsor-tárolási trigger függvény létrehozása](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ez az trigger-példa egy nevű kulccsal rendelkező kapcsolatok karakterláncot használ `QueueStorage` . Adja meg ezt a kapcsolattípus-beállítást a [local.settings.jsfájljában](functions-run-local.md#local-settings-file).

4. Vizsgálja meg az újonnan hozzáadott osztályt. Ekkor megjelenik egy statikus `Run()` metódus, amely az attribútummal van társítva `FunctionName` . Ez az attribútum azt jelzi, hogy a metódus a függvény belépési pontja.

    A következő C# osztály például egy alapszintű üzenetsor-tárolási trigger függvényt jelöl:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

A rendszer a belépési pont metódusához megadott kötési paraméterekre alkalmazza a kötés-specifikus attribútumot. Az attribútum paraméterként a kötési adatokat veszi fel. Az előző példában az első paraméter egy `QueueTrigger` attribútummal van alkalmazva, amely egy üzenetsor-tárolási trigger függvényt jelez. A várólista neve és a kapcsolódási karakterlánc beállításának neve paraméterként lesz átadva az `QueueTrigger` attribútumnak. További információ: [Azure üzenetsor-tárolási kötések Azure Functionshoz](functions-bindings-storage-queue-trigger.md).

A fenti eljárás segítségével további függvényeket adhat hozzá a Function app-projekthez. A projekt egyes függvényei eltérő triggerrel rendelkezhetnek, de a függvénynek pontosan egy triggerrel kell rendelkeznie. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Kötések hozzáadása

Ahogy az eseményindítók esetében, a bemeneti és kimeneti kötések kötési attribútumokként lesznek hozzáadva a függvényhez. Adja hozzá a kötéseket egy függvényhez a következő módon:

1. Győződjön meg arról, hogy [konfigurálta a projektet a helyi fejlesztéshez](#configure-the-project-for-local-development).

2. Adja hozzá a megfelelő NuGet-kiterjesztési csomagot az adott kötéshez. 

   További információ: [C# Class Library a Visual Studióval](./functions-bindings-register.md#local-csharp). Keresse meg a kötési NuGet-csomagra vonatkozó követelményeket a kötésre vonatkozó cikkben. Megkeresheti például a Event Hubs trigger csomagra vonatkozó követelményeit a [Event Hubs kötési útmutató cikkben](functions-bindings-event-hubs.md).

3. Ha vannak olyan Alkalmazásbeállítások, amelyekre a kötés szükséges, adja hozzá őket a `Values` gyűjteményhez a [helyi beállítási fájlban](functions-run-local.md#local-settings-file). 

   A függvény ezeket az értékeket használja, amikor helyileg fut. Ha a függvény az Azure-beli Function alkalmazásban fut, akkor a [Function app beállításait](#function-app-settings)használja.

4. Adja hozzá a megfelelő kötési attribútumot a metódus aláírásához. A következő példában egy üzenetsor-üzenet elindítja a függvényt, és a kimeneti kötés egy új üzenetsor-üzenetet hoz létre, amely egy másik várólistában található azonos szöveggel rendelkezik.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   A várólista-tárolóhoz való kapcsolódás a `AzureWebJobsStorage` beállításból származik. További információkért tekintse meg az adott kötésre vonatkozó hivatkozási cikket. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. További információ: [a Azure functions Core Tools használata](functions-run-local.md). Ezeket az eszközöket akkor kell telepíteni, amikor először elindítja a függvényt a Visual studióból. 

A függvény tesztelése a Visual Studióban:

1. Nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Előfordulhat, hogy egy tűzfal-kivételt is engedélyeznie kell, hogy az eszközök kezelni tudják a HTTP-kérelmeket.

2. Ha a projekt fut, tesztelje a kódot úgy, ahogy az üzembe helyezett függvény tesztelését végzi. 

   További információ: stratégiák a [kód teszteléséhez Azure functions](functions-test-a-function.md). Ha hibakeresési módban futtatja a Visual studiót, a töréspontok a várt módon jelennek meg.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Közzététel az Azure-ban

A Visual studióból való közzétételkor a két telepítési módszer egyikét használja:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): csomagok és Windows-alkalmazások telepítése bármely IIS-kiszolgálóra.
* A [zip üzembe helyezése a-csomaggal engedélyezve: Azure functions üzemelő](functions-deployment-technologies.md#zip-deploy)példányokhoz ajánlott.

A következő lépésekkel teheti közzé a projektjét egy Azure-beli Function alkalmazásban.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>A függvényalkalmazás beállításai

Mivel a Visual Studio nem tölti fel automatikusan ezeket a beállításokat a projekt közzétételekor, a local.settings.jsban hozzáadott beállításokat is fel kell vennie az Azure-beli Function alkalmazásba.

A szükséges beállításoknak az Azure-beli Function alkalmazásba való feltöltésének legegyszerűbb módja, ha kiválasztja a projekt sikeres közzététele után megjelenő **Azure app Service-beállítások kezelése** hivatkozást.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Beállítások a közzétételi ablakban":::

A hivatkozás kiválasztásakor megjelenik a Function **alkalmazás Alkalmazásbeállítások** párbeszédpanele, ahol új Alkalmazásbeállítások adhatók hozzá, vagy módosíthatók a meglévő beállítások.

![Alkalmazásbeállítások](./media/functions-develop-vs/functions-vstools-app-settings2.png)

A **helyi** érték jelenik meg a local.settings.jsfájlon, a **távoli** pedig az Azure-beli Function alkalmazás aktuális beállítás értékét jeleníti meg. Új Alkalmazásbeállítás létrehozásához válassza a **beállítás hozzáadása** lehetőséget. A beállítás értékének a **helyi** hivatkozásból történő másolásához használja a Setting értéket a **távoli** mezőre. A függőben lévő módosítások a helyi beállítások fájlba és a Function alkalmazásba íródnak, amikor az **OK gombra** kattint.

> [!NOTE]
> Alapértelmezés szerint a fájl local.settings.jsnincs bejelölve a verziókövetésba. Ez azt jelenti, hogy ha a helyi functions-projektet a verziókövetés alapján klónozott, a projekthez nem tartozik local.settings.jsa fájlhoz. Ebben az esetben manuálisan kell létrehoznia a local.settings.jsfájlt a projekt gyökerében, hogy az **Alkalmazásbeállítások** párbeszédpanel a várt módon működjön. 

Az alkalmazás beállításait az alábbi módokon is kezelheti:

* [Használja a Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Használja a `--publish-local-settings` Azure functions Core Tools közzététel lehetőségét](functions-run-local.md#publish).
* [Használja az Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)-t.

## <a name="monitoring-functions"></a>Figyelési függvények

A függvények végrehajtásának ajánlott figyelése a Function alkalmazás Azure Application Insights-nal való integrálásával. Ha a Azure Portalban hoz létre egy Function alkalmazást, a rendszer alapértelmezés szerint ezt az integrációt végzi. Ha azonban a Visual Studio Publishing szolgáltatásban hozza létre a Function alkalmazást, az Azure-beli Function alkalmazásban való integráció nem történik meg. A Application Insightsnak a Function alkalmazáshoz való kapcsolódásával kapcsolatos további információkért lásd: [Application Insights integrációjának engedélyezése](configure-monitoring.md#enable-application-insights-integration).

Ha többet szeretne megtudni a Application Insights használatával végzett figyelésről, tekintse meg a [Azure functions figyelése](functions-monitoring.md)című témakört.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions Core Toolsről: a [Azure functions Core Tools használata](functions-run-local.md).

További információ a függvények .NET-es kódtárakkal való fejlesztéséről: [Azure functions C# fejlesztői dokumentáció](functions-dotnet-class-library.md). A cikk azt is bemutatja, hogyan használhatók az attribútumok a Azure Functions által támogatott különböző típusú kötések deklarálása céljából.    
