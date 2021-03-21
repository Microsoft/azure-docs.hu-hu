---
title: ASP.NET Core MVC-webalkalmazási oktatóanyag a Azure Cosmos DB használatával
description: ASP.NET Core MVC-oktatóanyag az MVC-alapú webalkalmazások Azure Cosmos DB használatával történő létrehozásához. A JSON-t és az adatok elérését egy Azure App Service-ASP NET Core MVC oktatóanyag lépésről lépésre futtatott Todo-alkalmazásból fogja tárolni.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010357"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Oktatóanyag: ASP.NET Core MVC-webalkalmazás fejlesztése a Azure Cosmos DB a .NET SDK használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Ez az oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure-ban üzemeltetett ASP.NET MVC-alkalmazás adatait a Azure Cosmos DB használatával. Ebben az oktatóanyagban a .NET SDK v3-t használja. Az alábbi képen látható, hogy milyen weboldalt fog létrehozni a cikkben szereplő minta használatával:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Képernyőkép az oktatóanyag által létrehozott teendők listája MVC-webalkalmazásról – ASP NET Core MVC oktatóanyag lépésről lépésre":::

Ha nincs ideje az oktatóanyag elvégzésére, letöltheti a teljes minta projektet a [githubról][GitHub].

Ez az oktatóanyag az alábbiakkal foglalkozik:

> [!div class="checklist"]
>
> * Azure Cosmos-fiók létrehozása
> * ASP.NET Core MVC-alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása a Azure Cosmos DBhoz
> * Létrehozási, olvasási, frissítési és törlési (szifilisz) műveletek végrehajtása az adatokon

> [!TIP]
> Ez az oktatóanyag feltételezi, hogy már rendelkezik korábbi tapasztalattal a ASP.NET Core MVC és a Azure App Service használatával. Ha új ASP.NET Core vagy az [előfeltételként szükséges eszközök](#prerequisites), javasoljuk, hogy töltse le a teljes minta projektet a [githubról][GitHub], adja hozzá a szükséges NuGet-csomagokat, és futtassa azt. A projekt létrehozása után áttekintheti ezt a cikket, hogy betekintést kapjon a programkódba a projekt kontextusában.

## <a name="prerequisites"></a><a name="prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások követése előtt győződjön meg arról, hogy rendelkezik a következő erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

A cikkben szereplő összes képernyőkép a Microsoft Visual Studio Community 2019. Ha más verziót használ, előfordulhat, hogy a képernyők és a beállítások nem egyeznek teljesen. A megoldásnak működnie kell, ha megfelel az előfeltételeknek.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>1. lépés: Azure Cosmos-fiók létrehozása

Kezdjük egy Azure Cosmos-fiók létrehozásával. Ha már rendelkezik Azure Cosmos DB SQL API-fiókkal, vagy ha a Azure Cosmos DB emulátort használja, ugorjon a [2. lépés: új ASP.net MVC-alkalmazás létrehozása](#create-a-new-mvc-application)elemre.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A következő szakaszban új ASP.NET Core MVC-alkalmazást hoz létre.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>2. lépés: új ASP.NET Core MVC-alkalmazás létrehozása

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.

1. A **create a New Project (új projekt létrehozása**) területen keresse meg és válassza ki **ASP.net Core webalkalmazást** a C# nyelvhez. A folytatáshoz válassza a **Tovább** gombot.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Új ASP.NET Core webalkalmazás-projekt létrehozása":::

1. Az **új projekt konfigurálása** lapon nevezze el a Project *Todo* nevet, és válassza a **Létrehozás** lehetőséget.

1. Az **új ASP.net Core Webalkalmazás létrehozása lapon** válassza a **webalkalmazás (Model-View-Controller)** lehetőséget. A folytatáshoz válassza a **Létrehozás** lehetőséget.

   A Visual Studio egy üres MVC-alkalmazást hoz létre.

1.   >  A ASP.NET-alkalmazás helyi futtatásához válassza a hibakeresés **indítása** vagy az F5 billentyűt.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>3. lépés: Azure Cosmos DB NuGet-csomag hozzáadása a projekthez

Most, hogy rendelkezünk a megoldáshoz szükséges ASP.NET Core MVC Framework-kóddal, adjuk hozzá a Azure Cosmos DBhoz való kapcsolódáshoz szükséges NuGet-csomagokat.

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget.

1. A **NuGet csomagkezelő eszközben** keresse meg és válassza ki a **Microsoft. Azure. Cosmos** elemet. Válassza a **Telepítés** gombot.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="NuGet-csomag telepítése":::

   A Visual Studio letölti és telepíti a Azure Cosmos DB csomagot és annak függőségeit.

   A NuGet-csomag telepítéséhez a **Package Manager-konzolt** is használhatja. Ehhez válassza az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt**. A parancssorba írja be a következő parancsot:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>4. lépés: az ASP.NET Core MVC-alkalmazás beállítása

Most adjuk hozzá a modelleket, a nézeteket és a vezérlőket ehhez az MVC-alkalmazáshoz.

### <a name="add-a-model"></a><a name="add-a-model"></a> Modell hozzáadása

1. A **megoldáskezelő** kattintson a jobb gombbal a **modellek** mappára, majd válassza az  >  **osztály** hozzáadása elemet.

1. Az **új elem hozzáadása** lapon nevezze el az új Class *. cs nevű elemet* , majd válassza a **Hozzáadás** lehetőséget.

1. Cserélje le a *Item. cs* osztály tartalmát a következő kódra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

A Azure Cosmos DB JSON használatával helyezi át és tárolja az adattárolást. Az `JsonProperty` attribútum segítségével szabályozhatja, hogy a JSON hogyan szerializálja és deszerializálja az objektumokat. Az `Item` osztály az `JsonProperty` attribútumot mutatja be. Ez a kód vezérli a JSON-ba kerülő tulajdonságnév formátumát. Átnevezi a .NET-tulajdonságot is `Completed` .

### <a name="add-views"></a><a name="add-views"></a>Nézetek hozzáadása

Ezután adja hozzá a következő nézeteket.

* Elemek létrehozása nézet
* Egy elemek törlése nézet
* Az elemek részleteit megjelenítő nézet
* Elemek szerkesztése nézet
* Az összes elemet listázó nézet

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Elemek nézet létrehozása

1. A **megoldáskezelő** kattintson a jobb gombbal a **nézetek** mappára, és válassza az  >  **új mappa** hozzáadása lehetőséget. Adja meg a mappa *tétel* nevét.

1. Kattintson a jobb gombbal az üres elem mappára, majd válassza a nézet **hozzáadása** **elemet**  >  .

1. Az **MVC-nézet hozzáadása** párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **nézet neve** mezőbe írja be a *create (létrehozás*) nevet.
   * A **sablon** lapon válassza a **Létrehozás** lehetőséget.
   * A **modell osztályban** válassza az **elem (teendők) elemet. Modellek)**.
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout. cshtml* értéket.
   * Válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="Az MVC nézet hozzáadása párbeszédpanelt ábrázoló képernyőkép":::

1. Ezután válassza a **Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio új sablon nézetet hozzon létre. Cserélje le a generált fájlban lévő kódot a következő tartalomra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Elemek nézetének törlése

1. A **megoldáskezelő** kattintson ismét a jobb gombbal az **elem** mappájára, majd válassza a  >  **nézet** hozzáadása elemet.

1. Az **MVC-nézet hozzáadása** párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **nézet neve** mezőbe írja be a *delete* értéket.
   * A **sablon** mezőben válassza a **Törlés** lehetőséget.
   * A **Model class** (Modellosztály) mezőben válassza ki az **Item (todo.Models)** elemet.
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout. cshtml* értéket.
   * Válassza a **Hozzáadás** lehetőséget.

1. Ezután válassza a **Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio új sablon nézetet hozzon létre. Cserélje le a generált fájlban lévő kódot a következő tartalomra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Nézet hozzáadása egy elem részleteinek beszerzéséhez

1. A **megoldáskezelő** kattintson ismét a jobb gombbal az **elem** mappájára, majd válassza a   >  **nézet** hozzáadása elemet.

1. Az **MVC-nézet hozzáadása** párbeszédpanelen adja meg a következő értékeket:

   * A **nézet neve** mezőben adja meg a *részleteket*.
   * A **sablon** területen válassza a **részletek** lehetőséget.
   * A **modell osztályban** válassza az **elem (teendők) elemet. Modellek)**.
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout. cshtml* értéket.

1. Ezután válassza a **Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio új sablon nézetet hozzon létre. Cserélje le a generált fájlban lévő kódot a következő tartalomra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Elem szerkesztési nézetének hozzáadása

1. A **megoldáskezelő** kattintson ismét a jobb gombbal az **elem** mappájára, majd válassza a  >  **nézet** hozzáadása elemet.

1. Az **MVC-nézet hozzáadása** párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **View name** (Nézet neve) mezőbe írja be az *Edit* (Szerkesztés) nevet.
   * A **Template** (Sablon) mezőben válassza az **Edit** (Szerkesztés) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az **Item (todo.Models)** elemet.
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout. cshtml* értéket.
   * Válassza a **Hozzáadás** lehetőséget.

1. Ezután válassza a **Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio új sablon nézetet hozzon létre. Cserélje le a generált fájlban lévő kódot a következő tartalomra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Nézet hozzáadása az összes elem listázásához

Végül pedig vegyen fel egy nézetet az összes elem beolvasásához a következő lépésekkel:

1. A **megoldáskezelő** kattintson ismét a jobb gombbal az **elem** mappájára, majd válassza a  >  **nézet** hozzáadása elemet.

1. Az **MVC-nézet hozzáadása** párbeszédpanelen hajtsa végre a következő módosításokat:

   * A **View name** (Nézet neve) mezőbe írja be az *Index* nevet.
   * A **Template** (Sablon) mezőben válassza a **List** (Lista) elemet.
   * A **Model class** (Modellosztály) mezőben válassza ki az **Item (todo.Models)** elemet.
   * Válassza **az elrendezés használata lapot** , és írja be a *~/views/Shared/_Layout. cshtml* értéket.
   * Válassza a **Hozzáadás** lehetőséget.

1. Ezután válassza a **Hozzáadás** lehetőséget, és hagyja, hogy a Visual Studio új sablon nézetet hozzon létre. Cserélje le a generált fájlban lévő kódot a következő tartalomra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

A lépések elvégzése után zárjunk be minden *cshtml* -dokumentumot a Visual Studióban.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Szolgáltatások deklarálása és inicializálása

Először egy olyan osztályt fogunk felvenni, amely tartalmazza a Azure Cosmos DBhoz való kapcsolódáshoz és használatához szükséges logikát. Ebben az oktatóanyagban ezt a logikát egy nevű osztályba `CosmosDbService` és egy nevű illesztőfelületbe ágyazjuk be `ICosmosDbService` . Ez a szolgáltatás a szifilisz műveleteit végzi. Emellett olyan olvasási hírcsatorna-műveleteket is végez, mint például a hiányos elemek listázása, az elemek létrehozása, szerkesztése és törlése.

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza az  >  **új mappa** hozzáadása lehetőséget. Nevezze el a mappa- *szolgáltatásokat*.

1. Kattintson a jobb gombbal a **szolgáltatások** mappára, válassza az  >  **osztály** hozzáadása elemet. Nevezze el az új osztály *CosmosDbService* , és válassza a **Hozzáadás** lehetőséget.

1. Cserélje le a *CosmosDbService. cs* fájl tartalmát a következő kódra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Kattintson a jobb gombbal a **szolgáltatások** mappára, válassza az  >  **osztály** hozzáadása elemet. Nevezze el az új osztály *ICosmosDbService* , és válassza a **Hozzáadás** lehetőséget.

1. Adja hozzá a következő kódot a *ICosmosDbService* osztályhoz:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Nyissa meg az *indítási. cs* fájlt a megoldásban, és adja hozzá a következő metódus **InitializeCosmosClientInstanceAsync**, amely beolvassa a konfigurációt, és inicializálja az ügyfelet.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. Ugyanezen a fájlon cserélje le a metódust a következőre `ConfigureServices` :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   Az ebben a lépésben szereplő kód a konfiguráció alapján inicializálja az ügyfelet egy egyedi példányként, amelyet a rendszer a [függőségek befecskendezésével ASP.net Core](/aspnet/core/fundamentals/dependency-injection).

   Győződjön meg arról, hogy az alapértelmezett MVC-vezérlőt úgy módosítja, hogy az `Item` `Configure` azonos fájl metódusában lévő útvonalakat szerkeszti:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Adja meg a konfigurációt a projekt *appsettings.js* fájljában, ahogy az a következő kódrészletben látható:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Vezérlő hozzáadása

1. **Megoldáskezelő** kattintson a jobb gombbal a **vezérlők** mappára, majd válassza a  >  **vezérlő** hozzáadása elemet.

1. Az **állvány hozzáadása** területen válassza az **MVC vezérlő – üres** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="MVC-vezérlő kiválasztása – üres a hozzáadási állványban":::

1. Nevezze el az új vezérlő *ItemController*.

1. Cserélje le a *ItemController. cs* fájl tartalmát a következő kódra:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

A **ValidateAntiForgeryToken** attribútum itt található, amely segít megvédeni ezt az alkalmazást a helyek közötti kérelmek hamisításának elleni támadásokkal szemben. A nézeteknek ezzel a hamisítási jogkivonattal is működniük kell. További információkért és Példákért lásd: [a helyek közötti kérelmek hamisításának (CSRF) megelőzése a ASP.net MVC alkalmazásban][Preventing Cross-Site Request Forgery]. A [GitHub][GitHub] közzétett forráskódban szerepel a teljes megvalósítás.

A metódus paraméterében a **kötési** attribútumot is használjuk a túlzott közzétételi támadásokkal szembeni védelem érdekében. További információ: [oktatóanyag: a szifilisz funkcióinak megvalósítása a Entity Framework ASP.net MVC-ben][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>5. lépés: az alkalmazás helyi futtatása

Az alkalmazás helyi számítógépen való teszteléséhez kövesse az alábbi lépéseket:

1. Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban való létrehozásához. Ennek fel kell építenie az alkalmazást és el kell indítania egy böngészőt a korábban látott üres rácsoldallal:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Az oktatóanyag által létrehozott teendők listázása webalkalmazás képernyőképe":::
   
   Ha az alkalmazás Ehelyett megnyílik a kezdőlapra, fűzze hozzá `/Item` az URL-címhez.

1. Válassza az **új létrehozása** hivatkozást, és adja hozzá az értékeket a **név** és a **Leírás** mezőkhöz. Hagyja üresen a **Befejezve** jelölőnégyzet jelölését. Ha kijelöli, az alkalmazás kiegészített állapotban adja hozzá az új elemet. Az elem már nem jelenik meg a kezdeti listán.

1. Válassza a **Létrehozás** lehetőséget. Az alkalmazás visszaküldi az **index** nézetet, és az elem megjelenik a listában. Több elemet is hozzáadhat a **Tennivalók** listájához.

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Képernyőfelvétel az index nézetről":::
  
1. Válassza a lista **elem** melletti **Szerkesztés** lehetőséget. Az alkalmazás megnyitja a **szerkesztési** nézetet, ahol frissítheti az objektum bármely tulajdonságát, beleértve a **befejezett** jelzőt is. Ha a **kész** lehetőséget választja, és a **Mentés** gombra kattint, az alkalmazás a listában befejezettként jeleníti meg az **elemet** .

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Képernyőfelvétel az index nézetről a befejezett négyzet bejelölve":::

1. Ellenőrizze a Azure Cosmos DB szolgáltatásban található adatállapotot a [Cosmos Explorer](https://cosmos.azure.com) vagy a Azure Cosmos DB Emulator adatkezelő használatával.

1. Miután tesztelte az alkalmazást, válassza a CTRL + F5 billentyűkombinációt az alkalmazás hibakeresésének leállításához. Készen áll a telepítésre!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>6. lépés: az alkalmazás üzembe helyezése

Most, hogy a teljes alkalmazás megfelelően működik az Azure Cosmos DB-adatbázissal, az Azure App Service-be fogjuk telepíteni ezt a webalkalmazást.  

1. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre **megoldáskezelő** , majd válassza a **Közzététel** lehetőséget.

1. A **közzétételi cél** kiválasztása lapon válassza a **app Service** lehetőséget.

1. Meglévő App Service-profil használatához válassza a **meglévő kiválasztása**, majd a **Közzététel** lehetőséget.

1. A **app Service** válassza ki az **előfizetést**. A **View** szűrő használatával csoportosíthatja az erőforráscsoport vagy az erőforrástípus alapján.

1. Keresse meg a profilt, majd kattintson **az OK gombra**. Ezután keresse meg a szükséges Azure App Service, és kattintson **az OK gombra**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="A Visual Studio App Service párbeszédpanelje":::

Egy másik lehetőség egy új profil létrehozása:

1. Ahogy az előző eljárásban, kattintson a jobb gombbal a projektre **megoldáskezelő** és válassza a **Közzététel** lehetőséget.
  
1. A **közzétételi cél** kiválasztása lapon válassza a **app Service** lehetőséget.

1. A **közzétételi cél** kiválasztása lapon válassza az **új létrehozása** elemet, és válassza a **Közzététel** lehetőséget.

1. A **app Service** mezőben adja meg a webalkalmazás nevét és a megfelelő előfizetést, erőforráscsoportot és üzemeltetési csomagot, majd válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="A Visual Studio App Service létrehozása párbeszédpanelje":::

Néhány másodpercen belül a Visual Studio közzéteszi a webalkalmazást, és elindít egy böngészőt, amelyen megtekintheti az Azure-ban futó projektet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy ASP.NET Core MVC-webalkalmazást. Az alkalmazás elérheti Azure Cosmos DB tárolt adatait. Most már folytathatja ezeket az erőforrásokat:

* [Particionálás az Azure Cosmos DB-ben](./partitioning-overview.md)
* [Bevezetés az SQL-lekérdezések használatába](./sql-query-getting-started.md)
* [Adatok modellezése és particionálása az Azure Cosmos DB-ben való életből vett példa használatával](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
