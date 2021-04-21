---
title: Azure-függvények fejlesztése a Visual Studióval
description: Megtudhatja, hogyan fejleszthet és tesztelhet Azure Functions a Azure Functions Tools for Visual Studio 2019 használatával.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 2cba0a9ad63e319af0a5eaa1c1c018c3b285c28a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765574"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Azure-függvények fejlesztése a Visual Studióval  

Visual Studio C#-osztálytár függvényeket fejleszthet, tesztelhet és helyezhet üzembe az Azure-ban. Ha ez az első lépés a Azure Functions, tekintse meg [a bevezetést a Azure Functions.](functions-overview.md)

Visual Studio függvények fejlesztésekor a következő előnyöket biztosítja: 

* Függvények szerkesztése, összeállítása és futtatása a helyi fejlesztői számítógépen. 
* Tegye közzé Azure Functions projektjét közvetlenül az Azure-ban, és szükség szerint hozzon létre Azure-erőforrásokat. 
* C#-attribútumokkal deklarálhatja a függvénykötéseket közvetlenül a C#-kódban.
* Előre lefordított C#-függvények fejlesztése és üzembe helyezése. Az előre megfelelt függvények jobb hideg indítási teljesítményt biztosítanak, mint a C#-szkriptalapú függvények. 
* A függvények C#-ban való megkódlása a fejlesztés Visual Studio előnyeivel. 

Ez a cikk részletesen bemutatja, hogyan használhatók Visual Studio C#-osztálytári függvények fejlesztésére és az Azure-ban való közzétételére. Mielőtt elolvassa ezt a cikket, fontolja meg a Függvények rövid [útmutató elvégzését a Visual Studio.](functions-create-your-first-function-visual-studio.md) 

Hacsak másként nincs jelezve, a bemutatott eljárások és példák 2019 Visual Studio re szolgálnak. 

## <a name="prerequisites"></a>Előfeltételek

- Azure Functions Tools. Az Azure-függvényeszközök hozzáadásához foglalja bele az **Azure-fejlesztési** számítási feladatot a Visual Studio telepítésbe. Azure Functions Tools a 2017-es Visual Studio kezdve érhető el az Azure-fejlesztési számítási feladatban.

- A többi szükséges erőforrás, például egy Azure Storage-fiók, a közzétételi folyamat során jön létre az előfizetésében.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 2017 Visual Studio ban az Azure-fejlesztési számítási feladat külön Azure Functions telepíti a Azure Functions Toolst. Amikor frissíti a Visual Studio 2017-es verziójának telepítését, [](#check-your-tools-version) győződjön meg arról, hogy az Azure Functions legújabb verzióját használja. A következő szakaszok azt mutatják be, hogyan ellenőrizheti és frissítheti a Azure Functions Tools bővítményt a 2017-es Visual Studio frissítéséhez. 
>
> Ha a 2019-es Visual Studio használja, hagyja ki ezeket a szakaszokat.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Ellenőrizze az eszközök verzióját a 2017-es Visual Studio verzióban

1. Az Eszközök **menüben** válassza a **Bővítmények és frissítések elemet.** **Bontsa ki az Installed** Tools (Telepített eszközök) et, majd Azure Functions a Web Jobs Tools  >   **(Web jobs-eszközök) et.**

    ![A Functions-eszközök verziójának ellenőrzése](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Jegyezze fel a **telepített verziót,** és hasonlítsa össze ezt a verziót a kibocsátási [megjegyzésekben felsorolt legújabb verzióval.](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md) 

1. Ha a verziója régebbi, frissítse az eszközöket a Visual Studio a következő szakaszban látható módon.

### <a name="update-your-tools-in-visual-studio-2017"></a>Az eszközök frissítése Visual Studio 2017-ben

1. A Bővítmények **és frissítések párbeszédpanelen bontsa** ki a **Frissítések** Visual Studio Marketplace gombra, válassza a Azure Functions  >  és a Web Jobs Tools **gombra,** és válassza a **Frissítés lehetőséget.**

    ![A Functions-eszközök verziójának frissítése](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Az eszközök frissítésének letöltése után válassza a Bezárás lehetőséget, majd zárja be a Visual Studio az eszközfrissítést a VSIX-telepítővel.

1. Az eszközök frissítéséhez a VSIX-telepítőben válassza a **Módosítás** lehetőséget. 

1. A frissítés befejezése után válassza a **Bezárás** lehetőséget, majd indítsa újra a Visual Studio.

> [!NOTE]  
> A Visual Studio 2019-es és újabb Azure Functions az Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Miután létrehozott egy Azure Functions, a projektsablon létrehoz egy C#-projektet, telepíti a `Microsoft.NET.Sdk.Functions` NuGet-csomagot, és beállítja a célkeretrendszert. Az új projekt a következő fájlokat tartalmazza:

* **host.jsbe:** Lehetővé teszi a Függvénygazda konfigurálását. Ezek a beállítások a helyi és az Azure-ban való futtatáskor egyaránt érvényesek. További információt a referencia [host.jstartalmaz.](functions-host-json.md)

* **local.settings.js:** A függvények helyi futtatásakor használt beállításokat tartja fenn. Ezek a beállítások nem használhatók az Azure-ban való futtatáskor. További információ: Helyi [beállításfájl.](#local-settings-file)

    >[!IMPORTANT]
    >Mivel a local.settings.jsfájlban található adatok titkos adatokat is tartalmazhatnak, ki kell zárnia azt a projekt forráskezelőjéhez. Győződjön meg **arról, hogy** a fájl Másolás kimeneti könyvtárba beállítása **Másolás, ha újabb.** 

További információkért lásd a [Functions osztálytárprojektjét.](functions-dotnet-class-library.md#functions-class-library-project)

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio a rendszer nem tölti fel automatikusan a local.settings.jsa projekt közzétételekor. Ha meg kell győződnie arról, hogy ezek a beállítások az Azure-beli függvényalkalmazásban is léteznek, töltse fel őket a projekt közzététele után. További információ: [Függvényalkalmazás beállításai.](#function-app-settings) A gyűjtemények `ConnectionStrings` értékei soha nem vannak közzétéve.

A kód a függvényalkalmazás beállításainak értékeit is be tudja olvasni környezeti változókként. További információ: Környezeti [változók.](functions-dotnet-class-library.md#environment-variables)

## <a name="configure-your-build-output-settings"></a>A build kimeneti beállításainak konfigurálása

A projekt Azure Functions a buildeszközök optimalizálják a kimenetet, hogy a függvények futásidejű példányával megosztott szerelvények csak egy példánya megmarad. Az eredmény egy optimalizált build, amely a lehető legnagyobb helyet takarítja meg. Ha azonban a projekt szerelvények egy újabb verziójára lép, előfordulhat, hogy a buildeszközök nem tudják, hogy ezeket a szerelvényeket meg kell őrizni. Annak érdekében, hogy ezek a szerelvények megmaradnak az optimalizálási folyamat során, a projektfájl (.csproj) elemeivel adhatja meg `FunctionsPreservedDependencies` őket:

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>A projekt konfigurálása helyi fejlesztéshez

A Functions-futtatás belsőleg egy Azure Storage-fiókot használ. A HTTP- és webhookokon kívül minden eseményindító-típushoz állítson be egy érvényes Azure Storage-fiók kapcsolati `Values.AzureWebJobsStorage` sztringet a kulcshoz. A függvényalkalmazás az [Azure Storage Emulatort](../storage/common/storage-use-emulator.md) is használhatja a projekthez szükséges `AzureWebJobsStorage` kapcsolati beállításhoz. Az emulátor használata előtt állítsa a értékét `AzureWebJobsStorage` `UseDevelopmentStorage=true` értékre. Az üzembe helyezés előtt módosítsa ezt a beállítást egy tényleges tárfiók kapcsolati sztringre.

A tárfiók kapcsolati sztringének beállítása:

1. A Visual Studio a View Cloud Explorer **(Felhőkezelő**  >  **megtekintése) lehetőséget.**

2. A **Cloud Explorerben bontsa** ki a **Tárfiókok** területet, majd válassza ki a tárfiókját. A Tulajdonságok **lapon** másolja ki az Elsődleges kapcsolati **sztring értékét.**

2. A projektben nyissa meg local.settings.jsfájlt, és állítsa a kulcs értékét a kimásott kapcsolati `AzureWebJobsStorage` sztringre.

3. Ismételje meg az előző lépést, ha egyedi kulcsokat szeretne hozzáadni a tömbhöz a függvények által `Values` megkövetelt egyéb kapcsolatokhoz. 

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

A C#-osztálytár függvényeiben a függvény által használt kötések a kódban attribútumok alkalmazásával vannak definiálva. Amikor a függvény eseményindítóit a megadott sablonokból hozza létre, a rendszer alkalmazza az eseményindító-attribútumokat. 

1. A **Megoldáskezelő** kattintson a jobb gombbal a projektcsomópontra, és válassza **az Új elem** hozzáadása  >  **lehetőséget.** 

2. Válassza **az Azure-függvény** lehetőséget, **adjon** nevet az osztálynak, majd válassza a **Hozzáadás lehetőséget.**

3. Válassza ki az eseményindítót, állítsa be a kötési tulajdonságokat, majd kattintson az **OK gombra.** Az alábbi példa egy Queue Storage-eseményindító függvény létrehozásának beállításait mutatja be. 

    ![Queue Storage-eseményindító függvény létrehozása](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ez az eseményindító-példa egy nevű kulccsal használt kapcsolati sztringet `QueueStorage` használ. Adja meg ezt a kapcsolati sztringbeállítást [a fájllocal.settings.jsfájlban.](functions-run-local.md#local-settings-file)

4. Vizsgálja meg az újonnan hozzáadott osztályt. Egy statikus `Run()` metódust lát, amely a attribútummal van meg `FunctionName` rendelve. Ez az attribútum azt jelzi, hogy a metódus a függvény belépési pontja.

    A következő C#-osztály például egy alapszintű Queue Storage eseményindító-függvényt képvisel:

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

A rendszer kötésspecifikus attribútumot alkalmaz a belépésipont-metódushoz megadott összes kötési paraméterre. A attribútum paraméterként veszi fel a kötési információkat. Az előző példában az első paraméter egy attribútumot `QueueTrigger` alkalmazott, amely egy Queue Storage-eseményindító függvényt jelez. Az üzenetsor neve és a kapcsolati sztring beállításának neve paraméterekként lesz átkedve az `QueueTrigger` attribútumnak. További információ: [Azure Queue Storage-kötések Azure Functions.](functions-bindings-storage-queue-trigger.md)

A fenti eljárással adhat hozzá további függvényeket a függvényalkalmazás-projekthez. A projekt minden függvénye más eseményindítóval is rendelkezik, de egy függvénynek pontosan egy eseményindítóval kell lennie. További információ: Azure Functions [és kötési fogalmak.](functions-triggers-bindings.md)

## <a name="add-bindings"></a>Kötések hozzáadása

Ahogyan az eseményindítók, a bemeneti és kimeneti kötések is hozzá vannak adva a függvényhez kötési attribútumként. Kötések hozzáadása egy függvényhez a következőképpen:

1. Győződjön meg arról, hogy [a projektet helyi fejlesztésre konfigurálta.](#configure-the-project-for-local-development)

2. Adja hozzá a megfelelő NuGet-bővítménycsomagot az adott kötéshez. 

   További információkért lásd a [C# osztálytárat a Visual Studio.](./functions-bindings-register.md#local-csharp) A kötésre vonatkozó NuGet-csomagokra vonatkozó követelményeket a kötésre vonatkozó referenciacikkben találja. Az eseményindító csomagkövetelményei például a Event Hubs a Event Hubs [referenciacikkében találhatóak.](functions-bindings-event-hubs.md)

3. Ha vannak olyan alkalmazásbeállítások, amelyekre a kötésnek szüksége van, adja hozzá őket a gyűjteményhez a `Values` [helyi beállításfájlban.](functions-run-local.md#local-settings-file) 

   A függvény ezeket az értékeket használja helyi futtatáskor. Amikor a függvény az Azure-beli függvényalkalmazásban fut, a [függvényalkalmazás beállításait használja.](#function-app-settings)

4. Adja hozzá a megfelelő kötési attribútumot a metódus aláírásához. A következő példában egy üzenetsor-üzenet aktiválja a függvényt, a kimeneti kötés pedig létrehoz egy új üzenetsor-üzenetet ugyanazokkal a szöveggel egy másik üzenetsorban.

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
   A Queue Storage-kapcsolat a beállításból `AzureWebJobsStorage` szerezhető be. További információt az adott kötésre vonatkozó referenciacikkben talál. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Függvények tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. További információ: [A](functions-run-local.md)Azure Functions Core Tools. Amikor először indít el egy függvényt a függvényből, a rendszer felszólítja, hogy telepítse ezeket az Visual Studio. 

A függvény tesztelése a Visual Studio:

1. Nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Előfordulhat, hogy engedélyeznie kell egy tűzfal kivételt, hogy az eszközök kezelni tudjanak HTTP-kéréseket.

2. Ha a projekt fut, tesztelje a kódot úgy, mintha egy üzembe helyezett függvényt tesztelne. 

   További információ: [Strategies for testing your code in Azure Functions.](functions-test-a-function.md) Hibakeresési módban Visual Studio futtatja a töréspontokat.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Közzététel az Azure-ban

Amikor a közzétételt Visual Studio két üzembe helyezési módszer egyikét használja:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): Windows-alkalmazásokat telepít és telepít bármely IIS-kiszolgálón.
* [Tömörített üzembe helyezés engedélyezett run-From-package beállítással:](functions-deployment-technologies.md#zip-deploy)Ajánlott Azure Functions telepítéséhez.

Az alábbi lépésekkel közzéteheti a projektet egy függvényalkalmazásban az Azure-ban.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>A függvényalkalmazás beállításai

Mivel Visual Studio nem tölti fel automatikusan ezeket a beállításokat a projekt közzétételekor, a local.settings.js-ban megadott beállításokat az Azure-beli függvényalkalmazáshoz is hozzá kell adni.

A szükséges beállítások azure-beli függvényalkalmazásba való feltöltésének legegyszerűbb módja, ha kiválasztja a Manage **Azure App Service settings** (Beállítások kezelése) hivatkozást, amely a projekt sikeres közzététele után jelenik meg.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Beállítások a Közzététel ablakban":::

Erre a hivatkozásra kattintva **megjelenik** a függvényalkalmazás Alkalmazásbeállítások párbeszédpanelje, ahol új alkalmazásbeállításokat adhat hozzá, vagy módosíthatja a meglévőket.

![Alkalmazásbeállítások](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**A Helyi** érték egy beállításértéket jelenít meg  local.settings.jsfájlban, a Távoli pedig megjeleníti az aktuális beállításértéket az Azure-beli függvényalkalmazásban. Új **alkalmazásbeállítás létrehozásához** válassza a Beállítás hozzáadása lehetőséget. A Helyi **érték beszúrása** hivatkozással másoljon egy beállításértéket a **Távoli mezőbe.** A függőben lévő módosítások a helyi beállításfájlba és a függvényalkalmazásba vannak írva az **OK gombra való kiválasztásakor.**

> [!NOTE]
> Alapértelmezés szerint a fájl local.settings.jsnem kerül be a forráskezelőbe. Ez azt jelenti, hogy ha helyi Functions-projektet klónoz a forráskezelőből, a projektnek nincs local.settings.jsfájlban. Ebben az esetben manuálisan kell létrehoznia a local.settings.jsfájlt a projekt  gyökerében, hogy az Alkalmazásbeállítások párbeszédpanel a várt módon működik. 

Az alkalmazásbeállításokat a következő módokon is kezelheti:

* [Használja a Azure Portal.](functions-how-to-use-azure-function-app-settings.md#settings)
* [Használja `--publish-local-settings` a közzététel lehetőséget a Azure Functions Core Tools.](functions-run-local.md#publish)
* [Használja az Azure CLI-t.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)

## <a name="monitoring-functions"></a>Monitorozási függvények

A függvények végrehajtásának figyelése a függvényalkalmazás Azure-beli virtuális hálózatokkal való integrálásával Application Insights. Amikor függvényalkalmazást hoz létre a Azure Portal, ez az integráció alapértelmezés szerint meg lesz állítva. Amikor azonban a közzététel során létrehozza a függvényalkalmazást Visual Studio, a függvényalkalmazás azure-beli integrációja még nem történik meg. A függvényalkalmazáshoz való Application Insights lásd: Enable Application Insights integration (A [függvényalkalmazás Application Insights engedélyezése).](configure-monitoring.md#enable-application-insights-integration)

További információ a monitorozásról a Application Insights: [Monitor Azure Functions.](functions-monitoring.md)

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions Core Tools: Work [with Azure Functions Core Tools](functions-run-local.md).

További információ a függvények .NET-osztálytárakként való fejlesztésről: Azure Functions [C# fejlesztői referencia.](functions-dotnet-class-library.md) Ez a cikk olyan példákra is hivatkozásokat tartalmaz, amelyek bemutatják, hogyan lehet attribútumokkal deklarálni a Azure Functions.    
