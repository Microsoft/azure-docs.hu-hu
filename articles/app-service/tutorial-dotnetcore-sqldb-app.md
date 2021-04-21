---
title: 'Oktatóanyag: ASP.NET Core és a Azure SQL Database'
description: Ismerje meg, hogyan lehet működő .NET Core-alkalmazást Azure App Service és kapcsolatot létesíteni egy Azure SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 52a5b127312ef979791d17b27ca67b21a779e310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765746"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Oktatóanyag: ASP.NET Core- és Azure SQL Database-alkalmazás Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) egy nagymértékben skálázható, önjavítást javító webes üzemeltetési szolgáltatást nyújt az Azure-ban. Ez az oktatóanyag bemutatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztathatja SQL Database. Ha végzett, egy Windows rendszeren futó, a windowsos App Service .NET Core MVC-alkalmazással fog futni.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) egy nagymértékben skálázható, önjavítást használó webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztathatja egy SQL Database. Az oktatóanyag eredménye egy, a Linux App Service-ben futó .NET Core MVC-alkalmazás lesz.

::: zone-end

![App Service-ben futó alkalmazás](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * SQL Database-adatbázis létrehozása az Azure-ban
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">A legújabb .NET Core 3.1 SDK telepítése</a>

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Helyi .NET Core-alkalmazás létrehozása

Ebben a lépésben a helyi .NET Core-projektet állíthatja be.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa az alábbi parancsokat a mintaadattár klónozásához és a gyökerére való módosításhoz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

A mintaprojekt egy, az [Entity Framework Core](/ef/core/) szolgáltatást használó, alapszintű CRUD (létrehoz-olvas-frissít-töröl) alkalmazást tartalmaz.

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alábbi parancsokat a szükséges csomagok telepítéséhez, adatbázisok migrálásához és az alkalmazás elindításához.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson az **Új létrehozása** hivatkozásra, és hozzon létre néhány _teendőt_.

![sikeres csatlakozás az SQL Database-hez](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Ha bármikor le szeretné állítani a .NET Core-t, nyomja meg a `Ctrl+C` billentyűkombinációt a terminálon.

## <a name="create-production-sql-database"></a>Éles SQL Database létrehozása

Ebben a lépésben egy SQL Database-adatbázist hozhat létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

Ez az oktatóanyag az SQL-adatbázisokhoz az [Azure SQL Database-t](/azure/sql-database/) használja.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database logikai kiszolgáló létrehozása

A Cloud Shell hozzon létre egy logikai SQL Database a [`az sql server create`](/cli/azure/sql/server#az_sql_server_create) paranccsal.

Cserélje le *\<server-name>* a helyőrzőt egy *egyedi SQL Database* névre. Ez a név a globálisan egyedi végpont () SQL Database `<server-name>.database.windows.net` része. Érvényes karakterek: `a` - `z` `0` - `9` , `-` , . Továbbá cserélje le *\<db-username>* a és *\<db-password>* a helyére a választott felhasználónevet és jelszót. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Az SQL Database logikai kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfiguráljon egy kiszolgálói tűzfalszabályt

Hozzon létre egy [kiszolgálószintű Azure SQL Database-tűzfalszabályt](../azure-sql/database/firewall-configure.md) az [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) parancs használatával. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](overview-inbound-outbound-ips.md#find-outbound-ips).
>

A Cloud Shell futtassa újra a parancsot, hogy engedélyezze a helyi számítógépről való hozzáférést a helyett a *\<your-ip-address>* [helyi IPv4 IP-címével.](https://www.whatsmyip.org/)

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy [S0 teljesítményszintű](../azure-sql/database/service-tiers-dtu.md) adatbázist a kiszolgálón az [`az sql db create`](/cli/azure/sql/db#az_sql_db_create) parancs használatával.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Kapcsolati sztring létrehozása

Az paranccsal szerezze be a kapcsolati [`az sql db show-connection-string`](/cli/azure/sql/db#az_sql_db_show_connection_string) sztringet.

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

A parancs kimenetében cserélje le a és a helyére a korábban használt adatbázis-rendszergazdai *\<username>* *\<password>* hitelesítő adatokat.

Ez a .NET Core-alkalmazás kapcsolati sztringje. Másolja későbbi felhasználás céljára.

### <a name="configure-app-to-connect-to-production-database"></a>Alkalmazás konfigurálása az éles adatbázishoz való csatlakozáshoz

A helyi adattárban nyissa meg a Startup.cs fájlt, és keresse meg a következő kódot:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Cserélje le az alábbi kódra.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> A horizontálisan felskálásos éles alkalmazások esetén kövesse az áttelepítések éles környezetben való alkalmazásával [kapcsolatos ajánlott eljárásokat.](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)
> 

### <a name="run-database-migrations-to-the-production-database"></a>Adatbázis-migrálások futtatása az éles adatbázisba

Az alkalmazás jelenleg egy helyi Sqlite-adatbázishoz csatlakozik. Most, hogy konfigurált egy Azure SQL Database, hozza létre újra a kezdeti migrálást a célként való beállításhoz. 

Futtassa a következő parancsokat az adattár gyökérkönyvtárában. Cserélje *\<connection-string>* le a helyére a korábban létrehozott kapcsolati sztringet.

```
# Delete old migrations
rm -r Migrations
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Alkalmazás futtatása új konfigurációval

Most, hogy az adatbázis-migrálásokat futtatta az éles adatbázisban, tesztelje az alkalmazást a következő futtatásával:

```
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson az **Új létrehozása** hivatkozásra, és hozzon létre néhány _teendőt_. Az alkalmazás most adatokat olvas és ír az éles adatbázisba.

Véglegesítjük a helyi módosításokat, majd véglegesítjük a Git-adattárban. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Most már készen áll a kód üzembe helyezésére.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben az SQL Database-hez csatlakoztatott .NET Core-alkalmazást helyezi üzembe az App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Kapcsolati sztring konfigurálása

Az Azure-alkalmazás kapcsolati sztringek beállítását a parancs [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) használatával Cloud Shell. A következő parancsban cserélje le az paramétert a korábban létrehozott kapcsolati *\<app-name>* *\<connection-string>* sztringre.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

A ASP.NET Core-ban használhatja ezt a nevű kapcsolati sztringet ( ) a szokásos mintával, mint a fájlbanappsettings.js`MyDbConnection` *kapcsolati sztring.* Ebben az esetben a `MyDbConnection` is meg van határozva a(appsettings.js *a következőn:*. A App Service futtatásakor a App Service kapcsolati sztring elsőbbséget élvez a -banappsettings.js *kapcsolati sztringtel.* A kód a *appsettings.jsaz értéket* használja a helyi fejlesztés során, és ugyanez a kód a App Service értéket használja az üzembe helyezéskor.

A kapcsolati sztring kódban való hivatkozásának mikéntjére az Alkalmazás konfigurálása éles [adatbázishoz való csatlakozáshoz.](#configure-app-to-connect-to-production-database)

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      main -> main
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Keresse meg az Azure-alkalmazást

Keresse meg az üzembe helyezett alkalmazást a webböngészővel.

```bash
http://<app-name>.azurewebsites.net
```

Adjon hozzá néhány teendőt.

![App Service-ben futó alkalmazás](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gratulálunk!** Egy adatvezérelt .NET Core-alkalmazást futtat az App Service-ben.

## <a name="update-locally-and-redeploy"></a>Frissítés helyileg és ismételt üzembe helyezés

Ebben a lépésben módosíthatja az adatbázissémát, és közzéteheti azt az Azure-ban.

### <a name="update-your-data-model"></a>Adatmodell frissítése

Nyissa _meg a Models/Todo.cs kódot_ a kódszerkesztőben. Adja hozzá a következő tulajdonságot a `ToDo` osztályhoz:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Adatbázis-migrálások újrafuttatása

Futtatassan néhány parancsot az éles adatbázis frissítéséhez.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Ha új terminálablakot nyit meg, a kapcsolati sztringet a terminálon kell beállítania az éles adatbázishoz, ahogy azt az Adatbázis-áttelepítések futtatása az éles adatbázisba lehetőségnél [is tette.](#run-database-migrations-to-the-production-database)
>

### <a name="use-the-new-property"></a>Az új tulajdonság használata

Hajtson végre néhány módosítást a kódban a `Done` tulajdonság használatához. Ebben az oktatóanyagban az egyszerűség kedvéért csak az `Index` és a `Create` nézetet módosítja, így láthatja a tulajdonságot működés közben.

Nyissa _meg a Controllers/TodosController.cs tartományvezérlőt._

Keresse meg a `Create([Bind("ID,Description,CreatedDate")] Todo todo)` metódust, és adja hozzá a `Done` kifejezést a `Bind` attribútum tulajdonságok listájához. Amikor végzett, a `Create()` metódus aláírása a következő kódhoz hasonló:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Nyissa _meg a Views/Todos/Create.cshtml webhelyet._

A Razor-kódban látnia kell a `Description` alá tartozó `<div class="form-group">` elemet és egy másik, `<div class="form-group">` elemet a `CreatedDate` részhez. Közvetlenül ezután a két elem után adjon hozzá egy `<div class="form-group">` elemet a `Done` részhez:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Nyissa _meg a Views/Todos/Index.cshtml et._

Keresse meg az üres `<th></th>` elemet. Az elem felett adja hozzá a következő Razor-kódot:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Keresse meg a `asp-action` címke-segítőket tartalmazó `<td>` elemet. Az elem felett adja hozzá a következő Razor-kódot:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Mást nem kell tennie a módosítások megtekintéséhez az `Index` és a `Create` nézetben.

### <a name="test-your-changes-locally"></a>Módosítások helyi tesztelése

Futtassa helyben az alkalmazást.

```bash
dotnet run
```

> [!NOTE]
> Ha új terminálablakot nyit meg, a kapcsolati sztringet a terminálon kell beállítania az éles adatbázishoz, ahogy azt az Adatbázis-áttelepítések futtatása az éles adatbázisba lehetőségnél [is tette.](#run-database-migrations-to-the-production-database)
>

A böngészőjében lépjen az `http://localhost:5000/` helyre. Most hozzáadhat teendőket, és bejelölheti a **Kész** jelölőnégyzetet. Ezután ennek meg kell jelennie a főoldalon befejezett elemként. Ne feledje, hogy az `Edit` nézetben nem jelenik meg a `Done` mező, mivel az `Edit` nézetet nem módosította.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

```bash
git add .
git commit -m "added done field"
git push azure main
```

Miután a befejeződött, lépjen a App Service alkalmazásba, próbáljon meg hozzáadni egy feladat elemet, és jelölje be `git push` a **Kész jelölőnégyzetet.**

![Azure-alkalmazás a Code First migrálás után](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

A meglévő teendők továbbra is megjelennek. Amikor újra közzéten egy ASP.NET Core-alkalmazást, a SQL Database meglévő adatai nem elvesznek. Emellett az Entity Framework Core Migrations csak az adatsémát módosítja, a meglévő adatokat érintetlenül hagyja.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

Bár a ASP.NET Core alkalmazás a Azure App Service fut, a konzolnaplókat a rendszer a Cloud Shell. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A mintaprojekt már követi az Azure ASP.NET Core Logging in Azure (Alapvető naplózás [az Azure-ban)](/aspnet/core/fundamentals/logging#azure-app-service-provider) dokumentum útmutatását két konfigurációs módosítással:

- Tartalmaz egy hivatkozást a `Microsoft.Extensions.Logging.AzureAppServices` *DotNetCoreSqlDb.csproj fájlban.*
- A `loggerFactory.AddAzureWebAppDiagnostics()` *Program.cs fájlban* hívja meg a hívásokat.

Ha a ASP.NET [alapszintű](/aspnet/core/fundamentals/logging#log-level) naplózási szintjét App Service-ről az alapértelmezett szintre, használja az parancsot a `Information` `Error` [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) Cloud Shell.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging filesystem --level information
```

> [!NOTE]
> A projekt naplószintje már be van állítva a `Information` *fájlban,appsettings.jsa következőn:*.

A naplóstreamelést a következő [`az webapp log tail`](/cli/azure/webapp/log#az_webapp_log_tail) paranccsal kezdheti meg: Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

A naplóstreamelés elindulás után frissítse az Azure-alkalmazást a böngészőben, hogy némi webes forgalmat generálni tud. Ekkor láthatja, hogy a rendszer átadja a konzolnaplófájlokat a terminálnak. Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

A `Ctrl`+`C` billentyűparanccsal bármikor leállíthatja a naplóstreamelést.

Az alapvető naplók testreszabásával kapcsolatos további ASP.NET [a Naplózás a ASP.NET Core-ban.](/aspnet/core/fundamentals/logging)

## <a name="manage-your-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás a Azure Portal [keresse](https://portal.azure.com)meg és válassza **a** App Services.

![Válassza App Services a Azure Portal](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

A **App Services** válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Alapértelmezés szerint a portálon megjelenik az alkalmazás **Áttekintés** lapja. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * SQL Database-adatbázis létrehozása az Azure-ban
> * .NET Core-alkalmazás csatlakoztatása az SQL Database-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

A következő oktatóanyag azt is bemutatja, hogyan lehet leképezni egy egyéni DNS-nevet az alkalmazásra.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazásra](app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást is:

> [!div class="nextstepaction"]
> [Az ASP.NET Core-alkalmazás konfigurálása](configure-language-dotnetcore.md)
