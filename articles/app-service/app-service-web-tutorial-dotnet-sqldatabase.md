---
title: 'Oktatóanyag: ASP.NET alkalmazás Azure SQL Database'
description: Ismerje meg, hogyan helyezhet üzembe egy C# ASP.NET-alkalmazást az Azure-ban és Azure SQL Database
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 533bd817b704db9976624b356a9950a9c48b8339
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104605977"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>Oktatóanyag: ASP.NET-alkalmazás üzembe helyezése az Azure-ban Azure SQL Database

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy adatvezérelt ASP.NET-alkalmazást a App Serviceban, és hogyan csatlakoztatható a [Azure SQL Databasehoz](../azure-sql/database/sql-database-paas-overview.md). Ha elkészült, az Azure-ban futó ASP.NET-alkalmazás és a SQL Database csatlakozik.

![Közzétett ASP.NET-alkalmazás Azure App Service](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Adatbázis létrehozása Azure SQL Database
> * ASP.NET-alkalmazás csatlakoztatása SQL Database-adatbázishoz
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

Telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

Ha már telepítette a Visual studiót, adja hozzá a számítási feladatokat a Visual studióhoz, **Ehhez kattintson az eszközök**  >  **beolvasása eszközök és funkciók** elemre.

## <a name="download-the-sample"></a>A minta letöltése

1. [Töltse le a mintaprojektet](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

1. Bontsa ki (csomagolja ki) a  *dotnet-sqldb-tutorial-master.zip* fájlt.

A mintaprojekt az [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) szolgáltatást használó, alapszintű [ASP.NET MVC](https://www.asp.net/mvc) CRUD (létrehoz-olvas-frissít-töröl) alkalmazást tartalmaz.

### <a name="run-the-app"></a>Az alkalmazás futtatása

1. Nyissa meg a *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* fájlt a Visual Studióban.

1. Az alkalmazás hibakeresés nélküli futtatásához nyomja le a `Ctrl+F5` billentyűkombinációt. Az alkalmazás megjelenik az alapértelmezett böngészőben.

1. Kattintson az **Új létrehozása** hivatkozásra, és hozzon létre néhány *teendőt*.

    ![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

1. Tesztelje az **Edit** (Szerkesztés), a **Details** (Részletek) és a **Delete** (Törlés) hivatkozást.

Az alkalmazás adatbázis-környezetet használ az adatbázishoz való kapcsolódáshoz. Ebben a példában az adatbázis-környezet a `MyDbConnection` nevű kapcsolati sztringet használja. A kapcsolati sztring a *Web.config* fájlban adható meg, és a *Models/MyDatabaseContext.cs* fájl hivatkozik rá. A kapcsolati karakterlánc nevét az oktatóanyag későbbi részében használva csatlakoztathatja az Azure-alkalmazást egy Azure SQL Databasehoz.

## <a name="publish-aspnet-application-to-azure"></a>ASP.NET-alkalmazás közzététele az Azure-ban

1. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

    ![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. Válassza ki az **Azure** -t célként, és kattintson a **tovább** gombra.

1. Győződjön meg arról, hogy a **Azure app Service (Windows)** lehetőség van kiválasztva, és kattintson a **tovább** gombra.

#### <a name="sign-in-and-add-an-app"></a>Jelentkezzen be, és adjon hozzá egy alkalmazást

1. A **Közzététel** párbeszédpanelen kattintson a **fiók hozzáadása** elemre a fiókkezelő legördülő menüből.

1. Jelentkezzen be az Azure-előfizetésbe. Ha már be van jelentkezve egy Microsoft-fiókba, győződjön meg arról, hogy abban a fiókban található az előfizetése. Ha nem abban a Microsoft-fiókban van az Azure-előfizetése, amelyikbe be van jelentkezve, kattintással adja hozzá a helyes fiókot.

1. A **app Service példányok** ablaktáblán kattintson a elemre **+** .

    ![Bejelentkezés az Azure-ba](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

#### <a name="configure-the-web-app-name"></a>A webalkalmazás nevének konfigurálása

Megtarthatja a webalkalmazás létrehozott nevét, vagy módosíthatja egy másik egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). A webalkalmazás neve az alkalmazás alapértelmezett URL-címének a részeként használatos (`<app_name>.azurewebsites.net`, amelyben az `<app_name>` a webalkalmazás neve). A webalkalmazás nevének egyedinek kell lennie az Azure összes alkalmazása tekintetében.

> [!NOTE]
> Még ne válassza a **create (létrehozás** ) lehetőséget.

![A Create App Service (App Service létrehozása) párbeszédpanel](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

#### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. A **Resource Group** (Erőforráscsoport) mellett kattintson a **New** (Új) elemre.

   ![A Resource Group (Erőforráscsoport) mellett kattintson a New (Új) elemre.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

1. Adja a **myResourceGroup** nevet az erőforráscsoportnak.

#### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Az **üzemeltetési csomag** mellett kattintson az **új** elemre.

1. Az **app Service terv konfigurálása** párbeszédpanelen konfigurálja az új App Service tervet a következő beállításokkal, majd kattintson **az OK** gombra:

   | Beállítás  | Ajánlott érték | További tudnivalók |
   | ----------------- | ------------ | ----|
   |**App Service-csomag**| myAppServicePlan | [App Service-csomagok](../app-service/overview-hosting-plans.md) |
   |**Hely**| Nyugat-Európa | [Azure-régiók](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**Méret**| Ingyenes | [Árképzési szintek](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

   ![App Service-csomag létrehozása](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

1. Kattintson a **Létrehozás** gombra, és várja meg az Azure-erőforrások létrehozását.

1. A **Közzététel** párbeszédpanel a konfigurált erőforrásokat jeleníti meg. Kattintson a **Finish** (Befejezés) gombra.

   ![a létrehozott erőforrások](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)


#### <a name="create-a-server-and-database"></a>Kiszolgáló és adatbázis létrehozása

Adatbázis létrehozása előtt [logikai SQL Serverre](../azure-sql/database/logical-servers.md)van szükség. A logikai SQL-kiszolgáló olyan logikai szerkezet, amely csoportként kezelt adatbázisok egy csoportját tartalmazza.

1. A **Közzététel** párbeszédpanelen görgessen le a **szolgáltatás függőségei** szakaszhoz. **SQL Server adatbázis** mellett kattintson a **Konfigurálás** elemre.

   ![SQL Database függőség konfigurálása](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sqldb-dependency.png)

1. Válassza a **Azure SQL Database** lehetőséget, majd kattintson a **tovább** gombra.

1. A **Azure SQL Database konfigurálása** párbeszédpanelen kattintson a elemre **+** .

1. Az **adatbázis-kiszolgáló** mellett kattintson az **új** elemre.

   A rendszer létrehoz egy kiszolgálónevet. A rendszer ezt a nevet használja a kiszolgáló alapértelmezett URL-címének részeként `<server_name>.database.windows.net` . Egyedinek kell lennie az összes Azure SQL-kiszolgáló között. Módosíthatja a kiszolgáló nevét, ezen oktatóanyag esetén azonban tartsa meg a létrehozott értéket.

1. Adjon meg egy rendszergazdai felhasználónevet és jelszót. A jelszó „összetettségére” vonatkozó követelményeket a [jelszavakra vonatkozó szabályzattal](/sql/relational-databases/security/password-policy) foglalkozó cikkben tekintheti meg.

   Jegyezze meg ezt a felhasználónevet és jelszót. A kiszolgálót később kell kezelnie.

   ![Kiszolgáló létrehozása](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

   > [!IMPORTANT]
   > Bár a kapcsolati sztringekban lévő jelszava maszkolva van (a Visual Studióban és az App Service szolgáltatásban is), a tény, hogy valamilyen rendszer tárolja, növeli az alkalmazás támadható felületét. Az App Service megszüntetheti ezt a kockázatot [felügyelt szolgáltatásidentitások](overview-managed-identity.md) használatával, feleslegessé téve a titkos kódokat az alkalmazáskonfigurációban. További információ: [Következő lépések](#next-steps).

1. Kattintson az **OK** gombra.

1. A **Azure SQL Database** párbeszédpanelen tartsa meg az alapértelmezett generált **adatbázis nevét**. Válassza a **Létrehozás** lehetőséget, és várja meg az adatbázis-erőforrások létrehozását.

    ![Adatbázis konfigurálása](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

#### <a name="configure-database-connection"></a>Adatbázis-kapcsolatok konfigurálása

1. Amikor a varázsló befejezi az adatbázis-erőforrások létrehozását, kattintson a **tovább** gombra.

1. Az **adatbázis-kapcsolatok karakterláncának neve** mezőbe írja be a következőt: _MyDbConnection_. Ennek a névnek meg kell egyeznie a _Models/MyDatabaseContext.cs_ fájlban hivatkozott kapcsolati sztringgel.

1. Az **adatbázis-kapcsolatok felhasználóneve** és az **adatbázis-kapcsolatok jelszava** mezőben adja meg a [kiszolgáló létrehozásakor](#create-a-server-and-database)használt rendszergazdai felhasználónevet és jelszót.

1. Győződjön meg arról, hogy az **Azure** -Alkalmazásbeállítások **be** van jelölve, majd kattintson a Befejezés gombra.

    ![Adatbázis-kapcsolatok karakterláncának konfigurálása](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-connection.png)

1. Várjon, amíg a konfigurációs varázsló befejeződik, és kattintson a **Bezárás** gombra.

#### <a name="deploy-your-aspnet-app"></a>A ASP.NET-alkalmazás üzembe helyezése

1. A **Közzététel** lapon görgessen vissza a tetejére, és kattintson a **Közzététel** elemre. Miután a ASP.NET alkalmazást üzembe helyezte az Azure-ban. Az alapértelmezett böngésző az üzembe helyezett alkalmazásra mutató URL-címmel indul el.

1. Adjon hozzá néhány teendőt.

    ![Közzétett ASP.NET-alkalmazás az Azure-alkalmazásban](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

    Gratulálunk! Az adatvezérelt ASP.NET-alkalmazás fut és elérhető az Azure App Service-ben.

## <a name="access-the-database-locally"></a>Az adatbázis elérése helyileg

A Visual Studio segítségével egyszerűen feltárhatja és felügyelheti az új adatbázist az Azure-ban a **SQL Server Object Explorerban**. Az új adatbázis már megnyitotta tűzfallal a létrehozott App Service alkalmazást, de a helyi számítógépről (például a Visual studióból) való hozzáféréshez meg kell nyitnia egy tűzfalat a helyi számítógép nyilvános IP-címéhez. Ha az internetszolgáltató megváltoztatja a nyilvános IP-címét, újra kell konfigurálnia a tűzfalat, hogy újra hozzáférhessen az Azure-adatbázishoz.

#### <a name="create-a-database-connection"></a>Adatbázis-kapcsolat létrehozása

1. A **View** (Nézet) menüben válassza az **SQL Server Object Explorer** lehetőséget.

1. Az **SQL Server Object Explorer** felső részében kattintson az **Add SQL Server** (SQL Server hozzáadása) gombra.

#### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

1. A **Connect** (Csatlakozás) párbeszédpanelen bontsa ki az **Azure** csomópontot. Az Azure-ban található összes SQL Database-példány látható itt.

1. Válassza ki a korábban létrehozott adatbázist. A korábban létrehozott kapcsolatot a rendszer automatikusan megadja a lap alján.

1. Írja be a korábban létrehozott adatbázis-rendszergazdai jelszót, és kattintson a **Connect** (Csatlakozás) elemre.

    ![Az adatbázis-kapcsolat konfigurálása a Visual Studióban](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

#### <a name="allow-client-connection-from-your-computer"></a>Ügyfélkapcsolat engedélyezése a számítógépen

Megnyílik a **Create a new firewall rule** (Új tűzfalszabály létrehozása) párbeszédpanel. Alapértelmezés szerint a kiszolgáló csak az Azure-szolgáltatások (például az Azure-alkalmazások) adatbázisaihoz való kapcsolódást teszi lehetővé. Ha az Azure-on kívülről szeretne csatlakozni az adatbázishoz, hozzon létre egy tűzfalszabály-kiszolgálót a kiszolgáló szintjén. A tűzfalszabály engedélyezi a helyi számítógép nyilvános IP-címét.

A párbeszédpanel már ki van töltve a számítógép nyilvános IP-címével.

1. Jelölje be az **Add my client IP** (Ügyfél IP-címének hozzáadása) választógombot, és kattintson az **OK** gombra.

    ![Tűzfalszabály létrehozása](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

    Miután a Visual Studio befejezte a tűzfal beállítását az SQL Database-példány számára, a kapcsolat megjelenik az **SQL Server Object Explorerben**.

    Itt elvégezheti a leggyakoribb adatbázis-műveleteket, például lekérdezések futtatását, nézetek és tárolt eljárások létrehozását és sok minden mást.

1. Bontsa ki a > **adatbázisok**  >  **&lt; adatbázis->**  >  **táblázatait**. Kattintson a jobb gombbal a `Todoes` táblára, és válassza a **View Data** (Adatok megtekintése) lehetőséget.

    ![SQL Database-objektumok megismerése](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Az alkalmazás frissítése Code First migrálásokkal

A Visual Studióban található ismerős eszközök segítségével frissítheti az adatbázist és az alkalmazást az Azure-ban. Ebben a lépésben Code First migrálásokat használ az Entity Frameworkben az adatbázisséma módosításához, majd annak közzétételéhez az Azure-ban.

Az Entity Framework Code First migrálásainak a használatával kapcsolatos további információkért tekintse meg a [Getting Started with Entity Framework 6 Code First using MVC 5](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (Ismerkedés az Entity Framework 6 Code First használatával az MVC 5 segítségével) című cikket.

#### <a name="update-your-data-model"></a>Adatmodell frissítése

A kódszerkesztőben nyissa meg a _Models\Todo.cs_ fájlt. Adja hozzá a következő tulajdonságot a `ToDo` osztályhoz:

```csharp
public bool Done { get; set; }
```
    
#### <a name="run-code-first-migrations-locally"></a>Code First migrálás helyi futtatása

Futtasson néhány parancsot a helyi adatbázis frissítéséhez.

1. Kattintson a **Tools** (Eszközök) menü **NuGet Package Manager** > **Package Manager Console** (NuGet-csomagkezelő > Csomagkezelő konzol) elemére.

1. A Package Manager Console (Csomagkezelő konzol) ablakban engedélyezze a Code First migrálásokat:

    ```powershell
    Enable-Migrations
    ```
    
1. Migrálás felvétele:

    ```powershell
    Add-Migration AddProperty
    ```
    
1. Frissítse a helyi adatbázist:

    ```powershell
    Update-Database
    ```
    
1. Az alkalmazás futtatásához nyomja le a `Ctrl+F5` billentyűkombinációt. Tesztelje a szerkesztést, a részletek megtekintését és a létrehozást lehetővé tevő hivatkozást.

Ha az alkalmazás betöltése hibák nélkül fejeződik be, a Code First migrálások sikeresek voltak. A lap azonban továbbra is ugyanúgy néz ki, mert az alkalmazáslogika még nem használja az új tulajdonságot.

#### <a name="use-the-new-property"></a>Az új tulajdonság használata

Hajtson végre néhány módosítást a kódban a `Done` tulajdonság használatához. Ebben az oktatóanyagban az egyszerűség kedvéért csak az `Index` és a `Create` nézetet módosítja, így láthatja a tulajdonságot működés közben.

1. Nyissa meg a _Controllers\TodosController.cs_ fájlt.

1. Keresse meg a `Create()` metódust az 52. sorban, és adja hozzá a `Done` kifejezést a `Bind` attribútum tulajdonságlistájához. Amikor végzett, a `Create()` metódus aláírása a következő kódhoz hasonló:

    ```csharp
    public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
    ```
    
1. Nyissa meg a _Views\Todos\Create.cshtml_ fájlt.

1. A Razor-kódban látnia kell a `<div class="form-group">` elemet, amely a `model.Description` elemet használja, majd egy másik `<div class="form-group">` elemet, amely a `model.CreatedDate` elemet használja. Közvetlenül ezután a két elem után adjon hozzá egy másik `<div class="form-group">` elemet, amely a `model.Done` elemet használja:

    ```csharp
    <div class="form-group">
        @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
        <div class="col-md-10">
            <div class="checkbox">
                @Html.EditorFor(model => model.Done)
                @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
            </div>
        </div>
    </div>
    ```
    
1. Nyissa meg a _Views\Todos\Index.cshtml_ fájlt.

1. Keresse meg az üres `<th></th>` elemet. Az elem felett adja hozzá a következő Razor-kódot:

    ```csharp
    <th>
        @Html.DisplayNameFor(model => model.Done)
    </th>
    ```
    
1. Keresse meg a `Html.ActionLink()` segédmetódusokat tartalmazó `<td>` elemet. Ezen `<td>`_felett_ adjon hozzá egy másik `<td>` elemet a következő Razor-kóddal:

    ```csharp
    <td>
        @Html.DisplayFor(modelItem => item.Done)
    </td>
    ```
    
    Mást nem kell tennie a módosítások megtekintéséhez az `Index` és a `Create` nézetben.

1. Az alkalmazás futtatásához nyomja le a `Ctrl+F5` billentyűkombinációt.

Most hozzáadhat teendőket, és bejelölheti a **Kész** jelölőnégyzetet. Ezután ennek meg kell jelennie a főoldalon befejezett elemként. Ne feledje, hogy az `Edit` nézetben nem jelenik meg a `Done` mező, mivel az `Edit` nézetet nem módosította.

#### <a name="enable-code-first-migrations-in-azure"></a>Code First migrálások engedélyezése az Azure-ban

Most, hogy a kód módosítása működik, beleértve az adatbázis-áttelepítést, közzéteszi az Azure-alkalmazásban, és frissíti a SQL Database a Code First Migrálás szolgáltatással.

1. Fentiekhez hasonlóan kattintson jobb gombbal a projektre, majd válassza a **Publish** (Közzététel) lehetőséget.

1.   >  A közzétételi beállítások megnyitásához kattintson a további műveletek **Szerkesztés** lehetőségre.

    ![Közzétételi beállítások megnyitása](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

1. A **MyDatabaseContext** legördülő menüben válassza ki a Azure SQL Database adatbázis-összekapcsolását.

1. Válassza az **Execute Code First Migrations (runs on application start)** (Code First migrálások végrehajtása (alkalmazásindításakor fut)) elemet, majd kattintson a **Save** (Mentés) gombra.

    ![Kód első áttelepítésének engedélyezése az Azure-alkalmazásban](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

#### <a name="publish-your-changes"></a>A módosítások közzététele

Most, hogy engedélyezte a kód első áttelepítését az Azure-alkalmazásban, tegye közzé a kód módosításait.

1. A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre.

1. Próbálkozzon újabb teendő elemek hozzáadásával, kattintson a **Done** (Kész) gombra, ami után az elemeknek befejezett elemekként kell megjelenniük a kezdőlapon.

    ![Azure-alkalmazás a kód első áttelepítése után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

A meglévő teendők továbbra is megjelennek. Ha ismét közzéteszi az ASP.NET-alkalmazást, az SQL Database-ben meglévő adatok nem vesznek el. A Code First migrálások emellett csak az adatsémát módosítják, a meglévő adatokat érintetlenül hagyják.

## <a name="stream-application-logs"></a>Alkalmazásnaplók streamelése

A nyomkövetési üzeneteket közvetlenül az Azure-alkalmazásból a Visual studióba is továbbíthatja.

Nyissa meg a _Controllers\TodosController.cs_ fájlt.

Minden művelet egy `Trace.WriteLine()` metódussal indul. Ez a kód azt mutatja be, hogyan lehet nyomkövetési üzeneteket hozzáadni az Azure-alkalmazáshoz.

#### <a name="enable-log-streaming"></a>Naplóstreamelés engedélyezése

1. A **nézet** menüben válassza a **Cloud Explorer** lehetőséget.

1. A **Cloud Explorerben** bontsa ki az alkalmazással rendelkező Azure-előfizetést, és bontsa ki a **app Service**.

1. Kattintson a jobb gombbal az Azure-alkalmazásra, és válassza a **folyamatos átviteli naplók megtekintése** lehetőséget.

    ![Naplóstreamelés engedélyezése](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

    A naplókat mostantól az **Output** (Kimenet) ablakba streameli a rendszer.

    ![Az Output (Kimenet) ablakban streamelt napló](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

    Nem jelennek még azonban meg a nyomkövetési üzenetek. Ennek oka az, hogy amikor először kiválasztja a **folyamatos átviteli naplók megtekintése** lehetőséget, az Azure-alkalmazás a nyomkövetési szintet állítja be `Error` , amely csak a hibák eseményeit naplózza (a `Trace.TraceError()` metódussal).

#### <a name="change-trace-levels"></a>Nyomkövetési szintek módosítása

1. Ha módosítani szeretné a nyomkövetési szinteket más nyomkövetési üzenetek kinyomtatásához, térjen vissza a **Cloud Explorer** szolgáltatáshoz.

1. Kattintson ismét a jobb gombbal az alkalmazásra, és válassza **a Megnyitás a portálon** lehetőséget.

1. Az alkalmazás portál-kezelés lapján a bal oldali menüben válassza ki **app Service naplókat**.

1. Az **alkalmazás naplózása (fájlrendszer)** területen válassza a **részletes** **szint** lehetőséget. Kattintson a **Mentés** gombra.

    ![Nyomkövetési szint módosítása Verbose (Részletes) értékűre](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

    > [!TIP]
    > Kísérletezhet a különböző szintű nyomkövetésekkel, hogy lássa, milyen típusú üzenetek jelennek meg az egyes szintek esetén. Az **információs** szint például magában foglalja a, a és a által létrehozott összes naplót, `Trace.TraceInformation()` de a `Trace.TraceWarning()` `Trace.TraceError()` által létrehozott naplók nem `Trace.WriteLine()` .

1. A böngészőben nyissa meg újra az alkalmazást az *http:// &lt;>. azurewebsites.net nevű* alkalmazásban, majd próbálja meg az Azure-ban a Feladatlista-alkalmazás körét. A nyomkövetési üzeneteket mostantól a Visual Studio **Output** (Kimenet) ablakába streameli a rendszer.

    ```console
    Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
    Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
    Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
    Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
    ```
    
#### <a name="stop-log-streaming"></a>Naplóstreamelés leállítása

A naplóstreamelési szolgáltatás leállításához kattintson az **Output** (Kimenet) ablak **Stop monitoring** (Megfigyelés leállítása) gombjára.

![Naplóstreamelés leállítása](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Az Azure-alkalmazás kezelése

Ugorjon az [Azure Portalra](https://portal.azure.com), és felügyelje a létrehozott webalkalmazást. Keresse meg és válassza ki a **app Services**.

![Azure-App Services keresése](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Az alkalmazás oldalán landolt.

Alapértelmezés szerint a portál megjeleníti az **Áttekintés** oldalt. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Adatbázis létrehozása Azure SQL Database
> * ASP.NET-alkalmazás csatlakoztatása SQL Database-adatbázishoz
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

A következő oktatóanyag azt mutatja be, hogyan növelheti könnyedén az Azure SQL Database szolgáltatással létrehozott kapcsolat biztonságát.

> [!div class="nextstepaction"]
> [Az SQL Database biztonságos elérése az Azure-erőforrások felügyelt identitásaival](app-service-web-tutorial-connect-msi.md)

További források:

> [!div class="nextstepaction"]
> [ASP.NET-alkalmazás konfigurálása](configure-language-dotnet-framework.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)