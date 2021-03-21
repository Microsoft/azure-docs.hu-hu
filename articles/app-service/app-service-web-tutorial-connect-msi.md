---
title: 'Oktatóanyag: hozzáférés az adatokhoz felügyelt identitással'
description: Ismerje meg, hogyan teheti biztonságossá az adatbázis-kapcsolatot egy felügyelt identitás használatával, és hogyan alkalmazhatja azt más Azure-szolgáltatásokra is.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 2c19ee2b8e7ec3c695b2c76c46402c118c559b40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736238"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Oktatóanyag: Az Azure SQL Database-kapcsolat biztonságossá tétele az App Service-ből felügyelt identitás segítségével

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. [Felügyelt identitást](overview-managed-identity.md) biztosít az alkalmazásához, vagyis egy kulcsrakész megoldást, amely biztosítja az [Azure SQL Database-hez](/azure/sql-database/) és egyéb Azure-szolgáltatásokhoz való hozzáférés védelmét. Az App Service-ben található felügyelt identitások biztonságosabbá teszik alkalmazását a titkos kódok, pl. a kapcsolati sztringekben lévő hitelesítő adatok szükségességének megszüntetésével. Ebben az oktatóanyagban a felügyelt identitást a következő oktatóanyagok egyikében létrehozott minta webalkalmazáshoz fogja hozzáadni: 

- [Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Oktatóanyag: ASP.NET Core és Azure SQL Database alkalmazás létrehozása Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Ha ezzel végzett, a mintaalkalmazása biztonságosan csatlakozhat az SQL Database-hez, felhasználónév és jelszó használata nélkül.

> [!NOTE]
> Az oktatóanyagban szereplő lépések a következő verziókat támogatják:
> 
> - .NET-keretrendszer 4.7.2 és újabb verziók
> - .NET Core 2,2 és újabb verziók
>

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework konfigurálása az Azure AD-hitelesítés használatára SQL Database
> * Kapcsolódás SQL Database a Visual studióból az Azure AD-hitelesítés használatával

> [!NOTE]
>Az Azure AD-hitelesítés _különbözik_ a helyszíni Active Directory (AD DS) [integrált Windows-hitelesítéstől](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) . AD DS és az Azure AD teljesen különböző hitelesítési protokollokat használ. További információ: [Azure ad Domain Services dokumentáció](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az [oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure](app-service-web-tutorial-dotnet-sqldatabase.md) -ban a SQL Database vagy [oktatóanyag: ASP.net Core és SQL Database alkalmazás létrehozása Azure app Service](tutorial-dotnetcore-sqldb-app.md). Ha még nem tette meg, kövesse az első két oktatóanyag egyikét. Azt is megteheti, hogy a saját .NET-alkalmazásának lépéseit a SQL Database használatával is módosíthatja.

Ha a háttérrendszer használatával szeretne hibakeresést végezni az alkalmazásban SQL Database, győződjön meg arról, hogy engedélyezte az ügyfélkapcsolatot a számítógépről. Ha nem, adja hozzá az ügyfél IP-címét a [kiszolgálói szintű IP-tűzfalszabályok kezelése a Azure Portal használatával](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)című témakör lépéseit követve.

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Adatbázis-hozzáférés biztosítása az Azure AD-felhasználóhoz

Először engedélyezze az Azure AD-hitelesítés használatát SQL Database egy Azure AD-felhasználó kiosztásával a kiszolgáló Active Directory rendszergazdájához. Ez a felhasználó különbözik az Azure-előfizetéshez való regisztrációhoz használt Microsoft-fióktól. Az Azure AD-ben létrehozott, importált, szinkronizált vagy meghívott felhasználónak kell lennie. További információ az engedélyezett Azure AD-felhasználókról: [Azure ad-szolgáltatások és-korlátozások a SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Ha az Azure AD-bérlő még nem rendelkezik felhasználóval, hozzon létre egyet a [felhasználók hozzáadása vagy törlése a Azure Active Directory használatával](../active-directory/fundamentals/add-users-azure-active-directory.md)című rész lépéseit követve.

Keresse meg az Azure AD-felhasználó objektum-AZONOSÍTÓját a [`az ad user list`](/cli/azure/ad/user#az-ad-user-list) és a csere használatával *\<user-principal-name>* . Az eredmény egy változóba lesz mentve.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Az Azure AD összes egyszerű felhasználóneve listájának megtekintéséhez futtassa a parancsot `az ad user list --query [].userPrincipalName` .
>

Adja hozzá ezt az Azure AD-felhasználót Active Directory rendszergazdaként [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) a Cloud Shell parancs használatával. A következő parancsban cserélje le a *\<server-name>* nevet a kiszolgáló nevére ( `.database.windows.net` utótag nélkül).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory-rendszergazda hozzáadásával kapcsolatos további információkért lásd: [Azure Active Directory-rendszergazda létesítése a kiszolgálóhoz](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>A Visual Studio telepítése

### <a name="windows-client"></a>Windows-ügyfél
A Windowshoz készült Visual Studio integrálva van az Azure AD-hitelesítéssel. A fejlesztés és a hibakeresés a Visual Studióban való engedélyezéséhez adja hozzá az Azure ad-felhasználót a Visual Studióban a Fiókbeállítások **kiválasztásával**  >   a menüben, majd kattintson a **fiók hozzáadása** lehetőségre.

Az Azure ad-felhasználó Azure-szolgáltatásbeli hitelesítéshez való beállításához válassza az **eszközök**  >  **lehetőséget** a menüben, majd válassza az **Azure szolgáltatás-hitelesítési**  >  **fiók kiválasztása** lehetőséget. Válassza ki a hozzáadott Azure AD-felhasználót, és kattintson **az OK** gombra.

Most már készen áll az alkalmazás fejlesztésére és hibakeresésére SQL Database a háttérben, az Azure AD-hitelesítés használatával.

### <a name="macos-client"></a>macOS-ügyfél

Visual Studio for Mac nincs integrálva az Azure AD-hitelesítéssel. Azonban a későbbiekben használni kívánt [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) függvénytár használhat jogkivonatokat az Azure CLI-ből. A fejlesztés és a hibakeresés a Visual Studióban való engedélyezéséhez először [telepítenie kell az Azure CLI](/cli/azure/install-azure-cli) -t a helyi gépre.

Miután telepítette az Azure CLI-t a helyi gépen, jelentkezzen be az Azure CLI-be az alábbi paranccsal az Azure AD-felhasználó használatával:

```azurecli
az login --allow-no-subscriptions
```
Most már készen áll az alkalmazás fejlesztésére és hibakeresésére SQL Database a háttérben, az Azure AD-hitelesítés használatával.

## <a name="modify-your-project"></a>A projekt módosítása

A projekthez követett lépések attól függnek, hogy ASP.NET-vagy ASP.NET Core-projektről van-e szó.

- [ASP.NET módosítása](#modify-aspnet)
- [ASP.NET Core módosítása](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet-csomagot:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

A *Web.config* a fájl elejéről dolgozik, és hajtsa végre a következő módosításokat:

- A alkalmazásban `<configSections>` adja hozzá a következő szakasz deklarációját:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- a záró `</configSections>` címke alatt adja hozzá a következő XML-kódot `<SqlAuthenticationProviders>` .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Keresse meg a nevű kapcsolati karakterláncot, `MyDbConnection` és cserélje le az `connectionString` értékét a értékre `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . Cserélje _\<server-name>_ le _\<db-name>_ a és a nevet a kiszolgáló nevére és az adatbázis nevére.

> [!NOTE]
> Az imént regisztrált SqlAuthenticationProvider a korábban telepített AppAuthentication-könyvtár tetején alapul. Alapértelmezés szerint a rendszer hozzárendelt identitást használ. A felhasználó által hozzárendelt identitás kihasználása érdekében további konfigurációt kell megadnia. Tekintse meg a AppAuthentication könyvtárának a [kapcsolatok karakterláncának támogatását](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) .

Ez minden dolog, amire szüksége van a SQL Databasehoz való kapcsolódáshoz. A Visual Studióban végzett hibakeresés során a kód a [Visual Studióban](#set-up-visual-studio)beállított Azure ad-felhasználót használja. A SQL Database később beállíthatja, hogy lehetővé tegye a App Service alkalmazás felügyelt identitásával való kapcsolódást.

Írja be `Ctrl+F5` újra az alkalmazás futtatását. A böngészőben megjelenő szifilisz-alkalmazás mostantól közvetlenül az Azure AD-hitelesítés használatával csatlakozik a Azure SQL Databasehoz. Ez a beállítás lehetővé teszi, hogy adatbázis-áttelepítést futtasson a Visual studióból.

### <a name="modify-aspnet-core"></a>ASP.NET Core módosítása

A Visual Studióban nyissa meg a Package Manager konzolt, és adja hozzá a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)NuGet-csomagot:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

A [ASP.net Core és SQL Database oktatóanyagban](tutorial-dotnetcore-sqldb-app.md) `MyDbConnection` nincs használatban a kapcsolatok karakterlánca, mert a helyi fejlesztési környezet egy SQLite-adatbázisfájlt használ, és az Azure éles környezet a app Service származó kapcsolatok karakterláncot használ. Active Directory hitelesítéssel mindkét környezet ugyanazt a kapcsolódási karakterláncot szeretné használni. A *appsettings.jsa*(z) elemnél cserélje le a következőt a `MyDbConnection` kapcsolatok karakterláncának értékére:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Ezután adja meg a Entity Framework adatbázis-környezetet a SQL Database hozzáférési jogkivonatával. A *Data\MyDatabaseContext.cs*-ben adja hozzá a következő kódot az üres konstruktor kapcsos zárójelében `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` :

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Ez a demonstrációs kód a világosság és az egyszerűség érdekében szinkronban van.

Ez minden dolog, amire szüksége van a SQL Databasehoz való kapcsolódáshoz. A Visual Studióban végzett hibakeresés során a kód a [Visual Studióban](#set-up-visual-studio)beállított Azure ad-felhasználót használja. A SQL Database később beállíthatja, hogy lehetővé tegye a App Service alkalmazás felügyelt identitásával való kapcsolódást. Az `AzureServiceTokenProvider` osztály gyorsítótárazza a memóriában lévő jogkivonatot, és lekéri azt az Azure ad-ből a lejárat előtt. Nincs szüksége egyéni kódra a jogkivonat frissítéséhez.

> [!TIP]
> Ha az Ön által konfigurált Azure AD-felhasználó több bérlőhöz fér hozzá, a `GetAccessTokenAsync("https://database.windows.net/", tenantid)` megfelelő hozzáférési jogkivonat lekéréséhez hívja meg a kívánt bérlői azonosítót.

Írja be `Ctrl+F5` újra az alkalmazás futtatását. A böngészőben megjelenő szifilisz-alkalmazás mostantól közvetlenül az Azure AD-hitelesítés használatával csatlakozik a Azure SQL Databasehoz. Ez a beállítás lehetővé teszi, hogy adatbázis-áttelepítést futtasson a Visual studióból.

## <a name="use-managed-identity-connectivity"></a>Felügyelt identitású kapcsolat használata

Ezután konfigurálja a App Service alkalmazást úgy, hogy az SQL Databasehoz kapcsolódjon egy rendszerhez rendelt felügyelt identitással.

> [!NOTE]
> Míg az ebben a szakaszban szereplő utasítások egy rendszerhez rendelt identitásra vonatkoznak, a felhasználó által hozzárendelt identitást egyszerűen használhatja. Ehhez tegye a következőt:. a `az webapp identity assign command` kívánt felhasználóhoz rendelt identitás hozzárendeléséhez a módosításra van szükség. Ezután az SQL-felhasználó létrehozásakor ügyeljen arra, hogy a hely neve helyett a felhasználó által hozzárendelt identitási erőforrás nevét használja.

### <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha engedélyezni szeretné a felügyelt identitást az Azure-alkalmazásához, használja az [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) parancsot a Cloud Shellben. Az alábbi parancsban cserélje le a következőt: *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Íme egy példa a kimenetre:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Engedélyek megadása a felügyelt identitásnak

> [!NOTE]
> Ha szeretné, felveheti az identitást egy [Azure ad-csoportba](../active-directory/fundamentals/active-directory-manage-groups.md), majd az identitás helyett SQL Database hozzáférést biztosíthat az Azure ad-csoportnak. Az alábbi parancsok például hozzáadják az előző lépés felügyelt identitását egy új, _myAzureSQLDBAccessGroup_ nevű csoporthoz:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

A Cloud Shellben az SQLCMD parancsot használva jelentkezzen be az SQL Database-be. Cserélje le a _\<server-name>_ nevet a kiszolgáló nevére, _\<db-name>_ az alkalmazás által használt adatbázis nevére, valamint az _\<aad-user-name>_ _\<aad-password>_ Azure ad-felhasználó hitelesítő adataival.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

A kívánt adatbázishoz tartozó SQL-parancssorban futtassa a következő parancsokat az alkalmazás igényeinek megfelelő engedélyek megadásához. Példa: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* a felügyelt identitás neve az Azure AD-ben. Ha az identitás rendszerhez van rendelve, a név mindig ugyanaz, mint a App Service alkalmazás neve. Az Azure AD-csoportok engedélyeinek megadásához használja helyette a csoport megjelenítendő nevét (például *myAzureSQLDBAccessGroup*).

Az `EXIT` parancs begépelésével térjen vissza a Cloud Shell-parancssorba.

> [!NOTE]
> A felügyelt identitások háttér-szolgáltatásai szintén [fenntartanak egy jogkivonat-gyorsítótárat](overview-managed-identity.md#obtain-tokens-for-azure-resources) , amely csak akkor frissíti a jogkivonatot a cél erőforráshoz, ha lejár. Ha hibásan konfigurálja a SQL Database engedélyeket, és megpróbálja módosítani az engedélyeket az alkalmazással kapott token beszerzése *után* , akkor valójában nem kap új jogkivonatot a frissített engedélyekkel, amíg a gyorsítótárazott jogkivonat le nem jár.

> [!NOTE]
> A HRE nem támogatott helyszíni SQL Server esetén, és ez magában foglalja a rendszercsomagok. 

### <a name="modify-connection-string"></a>Kapcsolati sztring módosítása

Ne feledje, hogy ugyanazokat a módosításokat hajtotta végre *Web.config* vagy *appsettings.jsa* felügyelt identitással, így az egyetlen teendő, hogy eltávolítsa a meglévő kapcsolódási karakterláncot a app Serviceban, amelyet a Visual Studio az alkalmazás első üzembe helyezésével hozott létre. Használja az alábbi parancsot, de a helyére írja *\<app-name>* be az alkalmazás nevét.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>A módosítások közzététele

Már csak közzé kell tennie a módosításait az Azure-ban.

**Ha az [oktatóanyagból származik: ASP.NET-alkalmazás létrehozása az Azure-ban a SQL Databaseval](app-service-web-tutorial-dotnet-sqldatabase.md)**, tegye közzé a módosításokat a Visual Studióban. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre. 

**Ha [oktatóanyag: hozzon létre egy ASP.NET Core és SQL Database alkalmazást a Azure app Serviceban](tutorial-dotnetcore-sqldb-app.md)**, tegye közzé a módosításokat a git használatával a következő parancsokkal:

```bash
git commit -am "configure managed identity"
git push azure main
```

Amikor az új weblapon megjelenik a feladatlista, az alkalmazása kapcsolódik az adatbázishoz a felügyelt identitás segítségével.

![Azure-alkalmazás a kód első áttelepítése után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Most már ugyanúgy szerkesztheti a feladatlistát, mint korábban.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Entity Framework konfigurálása az Azure AD-hitelesítés használatára SQL Database
> * Kapcsolódás SQL Database a Visual studióból az Azure AD-hitelesítés használatával

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése Azure App Service](app-service-web-tutorial-custom-domain.md)