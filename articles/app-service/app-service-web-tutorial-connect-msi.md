---
title: 'Oktatóanyag: Adatok elérése felügyelt identitással'
description: Megtudhatja, hogyan tegye biztonságossá az adatbázis-kapcsolatot egy felügyelt identitás használatával, és hogyan alkalmazhatja azt más Azure-szolgáltatásokra.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 0012c3d208998786ef5aa34320f3bccc4e51ebe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782782"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Oktatóanyag: Az Azure SQL Database-kapcsolat biztonságossá tétele az App Service-ből felügyelt identitás segítségével

Az [App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás az Azure-ban. [Felügyelt identitást](overview-managed-identity.md) biztosít az alkalmazásához, vagyis egy kulcsrakész megoldást, amely biztosítja az [Azure SQL Database-hez](/azure/sql-database/) és egyéb Azure-szolgáltatásokhoz való hozzáférés védelmét. Az App Service-ben található felügyelt identitások biztonságosabbá teszik alkalmazását a titkos kódok, pl. a kapcsolati sztringekben lévő hitelesítő adatok szükségességének megszüntetésével. Ebben az oktatóanyagban felügyelt identitást fog hozzáadni a következő oktatóanyagok egyikében felépített minta-webalkalmazáshoz: 

- [Oktatóanyag: ASP.NET-alkalmazás összeállítása az Azure-ban Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Oktatóanyag: ASP.NET Core- és Azure SQL Database-alkalmazás Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Ha ezzel végzett, a mintaalkalmazása biztonságosan csatlakozhat az SQL Database-hez, felhasználónév és jelszó használata nélkül.

> [!NOTE]
> Az oktatóanyagban szereplő lépések a következő verziókat támogatják:
> 
> - .NET-keretrendszer 4.7.2-es vagy magasabb
> - .NET Core 2.2 és magasabb
>

Az elsajátított tudnivalók:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Az Entity Framework konfigurálása Azure AD-hitelesítés használatára SQL Database
> * Csatlakozás SQL Database Visual Studio Azure AD-hitelesítéssel

> [!NOTE]
>Az Azure AD-hitelesítés _különbözik_ az integrált [Windows-hitelesítéstől](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) a helyi Active Directory (AD DS). AD DS és az Azure AD teljesen eltérő hitelesítési protokollokat használ. További információért tekintse meg a [Azure AD Domain Services dokumentációját.](../active-directory-domain-services/index.yml)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk az [Oktatóanyag: ASP.NET-alkalmazás](app-service-web-tutorial-dotnet-sqldatabase.md) összeállítása az Azure-ban az SQL Database vagy az Oktatóanyag: ASP.NET Core- és SQL Database-alkalmazás buildel a [Azure App Service- Azure App Service.](tutorial-dotnetcore-sqldb-app.md) Ha még nem, először kövesse a két oktatóanyag valamelyikét. Másik lehetőségként a saját .NET-alkalmazás lépéseit is SQL Database.

Ha az alkalmazás hibakeresését SQL Database használatával, győződjön meg arról, hogy engedélyezett az ügyfélkapcsolat a számítógépről. Ha nem, adja hozzá az ügyfél IP-címét a Manage [server-level IP firewall rules using the Azure Portal.](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Adatbázis-hozzáférés megadása Az Azure AD-felhasználó számára

Először engedélyezze az Azure AD-SQL Database úgy, hogy hozzárendel egy Azure AD-felhasználót Active Directory kiszolgáló rendszergazdájaként. Ez a felhasználó nem Microsoft-fiók az Azure-előfizetésre való regisztrációhoz használttól. Olyan felhasználónak kell lennie, aki az Azure AD-be van létrehozva, importálva, szinkronizálva vagy meghívva. További információ az engedélyezett Azure AD-felhasználókról: [Az Azure AD](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)funkciói és korlátozásai a SQL Database.

Ha az Azure AD-bérlő még nem rendelkezik felhasználóval, hozzon létre egyet a Felhasználók hozzáadása vagy törlése a következővel: [Azure Active Directory.](../active-directory/fundamentals/add-users-azure-active-directory.md)

Keresse meg az Azure AD-felhasználó objektumazonosítóját a és [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) a *\<user-principal-name>* helyére. Az eredmény egy változóba lesz mentve.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Az Azure AD összes egyszerű felhasználónevének listájáért futtassa a következőt: `az ad user list --query [].userPrincipalName` .
>

Adja hozzá ezt az Azure AD-felhasználót rendszergazdaként Active Directory [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) paranccsal a Cloud Shell. A következő parancsban cserélje le *\<server-name>* a helyére a kiszolgáló nevét (az `.database.windows.net` utótag nélkül).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

További információ a rendszergazdai Active Directory hozzáadásáról: [Azure Active Directory-rendszergazdai fiók kiépítése a kiszolgálóhoz](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>A Visual Studio telepítése

### <a name="windows-client"></a>Windows-ügyfél
Visual Studio windowsos verzió integrálva van az Azure AD-hitelesítéssel. Ha engedélyezni szeretné a fejlesztést és a hibakeresést az Visual Studio-ban, adja hozzá az Azure AD-felhasználót az Visual Studio-ban. Ehhez válassza a Menü Fájlfiók beállításai menüpontját, majd kattintson  >   a Fiók **hozzáadása elemre.**

Az Azure AD-felhasználó Azure-szolgáltatáshitelesítéshez való beállítását a menüben válassza az Eszközök Lehetőségek, majd az   >   **Azure-szolgáltatáshitelesítési** fiók  >  **kiválasztása lehetőséget.** Válassza ki a hozzáadott Azure AD-felhasználót, majd kattintson az **OK gombra.**

Most már készen áll arra, hogy Azure AD-hitelesítéssel fejlessze és hibakeresést SQL Database a háttéralkalmazással.

### <a name="macos-client"></a>macOS-ügyfél

Visual Studio for Mac Azure AD-hitelesítés nincs integrálva. A [Microsoft.Azure.Services.AppAuthentication kódtár](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) azonban, amely később használatba lesz használhatja az Azure CLI-ről származó jogkivonatokat. Ha engedélyezni szeretné a fejlesztést és a Visual Studio, először telepítenie kell az [Azure CLI-t](/cli/azure/install-azure-cli) a helyi gépen.

Miután telepítette az Azure CLI-t a helyi gépen, jelentkezzen be az Azure CLI-be a következő paranccsal az Azure AD-felhasználó használatával:

```azurecli
az login --allow-no-subscriptions
```
Most már készen áll az alkalmazás fejlesztésére és hibakeresésre a SQL Database, Azure AD-hitelesítéssel.

## <a name="modify-your-project"></a>A projekt módosítása

A projekthez szükséges lépések attól függnek, hogy egy ASP.NET projektről van-e szó, ASP.NET Core-projektről van szó.

- [A ASP.NET](#modify-aspnet)
- [A core ASP.NET módosítása](#modify-aspnet-core)

### <a name="modify-aspnet"></a>A ASP.NET

A Visual Studio nyissa meg a Csomagkezelő Konzolt, és adja hozzá a [Microsoft.Azure.Services.AppAuthentication NuGet-csomagot:](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

A *Web.config* fájl tetején dolgozva a következő módosításokat kell követnie:

- A `<configSections>` -hez adja hozzá a következő szakaszdeklarációt:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- A záró címke `</configSections>` alá adja hozzá a következő XML-kódot. `<SqlAuthenticationProviders>`

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Keresse meg a nevű kapcsolati sztringet, `MyDbConnection` és cserélje le az értékét `connectionString` `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` értékre. Cserélje le _\<server-name>_ _\<db-name>_ a és a helyére a kiszolgáló és az adatbázis nevét.

> [!NOTE]
> Az előbb regisztrált SqlAuthenticationProvider a korábban telepített AppAuthentication kódtáron alapul. Alapértelmezés szerint rendszer által hozzárendelt identitást használ. A felhasználó által hozzárendelt identitások kihasználhoz meg kell adnia egy további konfigurációt. Tekintse meg [az](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) AppAuthentication kódtár kapcsolati sztringek támogatását.

Ez minden, amit csatlakoztatnia kell a SQL Database. A hibakeresési Visual Studio kód a Beállítás a következőben konfigurált Azure AD-felhasználót [Visual Studio.](#set-up-visual-studio) A későbbiekben be kell állítania SQL Database, hogy engedélyezze a kapcsolatot a App Service identitásával.

Írja `Ctrl+F5` be a parancsot az alkalmazás újrafuttassa. Ugyanaz a CRUD-alkalmazás a böngészőben mostantól közvetlenül csatlakozik a Azure SQL Database Azure AD-hitelesítéssel. Ezzel a beállítással adatbázis-áttelepítéseket futtathat Visual Studio.

### <a name="modify-aspnet-core"></a>A core ASP.NET módosítása

A Visual Studio nyissa meg a Csomagkezelő Konzolt, és adja hozzá a [Microsoft.Azure.Services.AppAuthentication NuGet-csomagot:](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

A [ASP.NET Core-](tutorial-dotnetcore-sqldb-app.md)és SQL Database-oktatóanyagban a kapcsolati sztring egyáltalán nincs használatban, mert a helyi fejlesztési környezet sqlite-adatbázisfájlt használ, az Éles Azure-környezet pedig egy kapcsolati sztringet a `MyDbConnection` App Service. A Active Directory esetén azt szeretné, hogy mindkét környezet ugyanazt a kapcsolati sztringet használja. A *appsettings.jsa kapcsolati* sztring értékét cserélje le `MyDbConnection` a következőre:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Ezután meg kell Entity Framework adatbázis környezetét a hozzáférési jogkivonattal a SQL Database. A *Data\MyDatabaseContext.cs* fájlban adja hozzá a következő kódot az üres `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktor kapcsos zárójelek között:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Ez a bemutató kód az átláthatóság és az egyszerűség kedvéért szinkron.

Ez minden dolog, amire szüksége van a SQL Database. Amikor hibakeresést Visual Studio, a kód a Beállítás a következőben konfigurált Azure [AD-felhasználót Visual Studio.](#set-up-visual-studio) A későbbiekben be kell állítania SQL Database, hogy engedélyezze a kapcsolatot a App Service identitásával. A osztály gyorsítótárazza a jogkivonatot a memóriában, és a lejárat előtt lekéri az `AzureServiceTokenProvider` Azure AD-ból. A jogkivonat frissítéshez nincs szükség egyéni kódra.

> [!TIP]
> Ha a konfigurált Azure AD-felhasználó több bérlőhöz is hozzáfér, a megfelelő hozzáférési jogkivonat lekéréséhez hívja meg a-t a kívánt `GetAccessTokenAsync("https://database.windows.net/", tenantid)` bérlőazonosítóval.

Írja `Ctrl+F5` be a parancsot az alkalmazás újrafuttassa. Ugyanaz a CRUD-alkalmazás a böngészőben mostantól közvetlenül csatlakozik a Azure SQL Database Azure AD-hitelesítéssel. Ezzel a beállítással adatbázis-áttelepítéseket futtathat Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Felügyelt identitás kapcsolatának használata

Ezután konfigurálja a App Service alkalmazást, hogy SQL Database egy rendszer által hozzárendelt felügyelt identitással csatlakozzon az alkalmazáshoz.

> [!NOTE]
> Bár az ebben a szakaszban található utasítások rendszer által hozzárendelt identitásra vonatkozóak, a felhasználó által hozzárendelt identitások is egyszerűen használhatók. Ehhez tegye meg. A módosítása szükséges a kívánt felhasználó által hozzárendelt `az webapp identity assign command` identitás hozzárendelése érdekében. Ezután az SQL-felhasználó létrehozásakor ügyeljen arra, hogy a felhasználó által hozzárendelt identitás erőforrásának nevét használja a webhely neve helyett.

### <a name="enable-managed-identity-on-app"></a>Felügyelt identitás engedélyezése az alkalmazásban

Ha engedélyezni szeretné a felügyelt identitást az Azure-alkalmazásához, használja az [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) parancsot a Cloud Shellben. A következő parancsban cserélje le a helyére *\<app-name>* a következőt: .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

A kimenet például a következő lehet:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Engedélyek megadása felügyelt identitáshoz

> [!NOTE]
> Ha szeretné, hozzáadhatja az identitást egy Azure AD-csoporthoz, majd SQL Database hozzáférést az Azure [AD-csoporthoz](../active-directory/fundamentals/active-directory-manage-groups.md)az identitás helyett. A következő parancsok például hozzáadják az előző lépésből származó felügyelt identitást egy _új, myAzureSQLDBAccessGroup_ nevű csoporthoz:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

A Cloud Shellben az SQLCMD parancsot használva jelentkezzen be az SQL Database-be. Cserélje le a helyére a kiszolgáló nevét, az alkalmazás által használt adatbázisnevet, a helyére pedig az _\<server-name>_ _\<db-name>_ Azure _\<aad-user-name>_ _\<aad-password>_ AD-felhasználó hitelesítő adatait.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

A kívánt adatbázis SQL-parancssorában futtassa a következő parancsokat az alkalmazás számára szükséges engedélyek megadásához. Példa: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* A a felügyelt identitás neve az Azure AD-ban. Ha az identitás rendszer által hozzárendelt, a név mindig ugyanaz lesz, mint a App Service neve. Egy Azure AD-csoport engedélyeinek megadásához használja inkább a csoport megjelenítendő nevét (például *myAzureSQLDBAccessGroup).*

Az `EXIT` parancs begépelésével térjen vissza a Cloud Shell-parancssorba.

> [!NOTE]
> A felügyelt identitások háttérszolgáltatása egy jogkivonat-gyorsítótárat is fenntart, amely csak akkor frissíti [a](overview-managed-identity.md#obtain-tokens-for-azure-resources) célerőforrás jogkivonatát, ha az lejár. Ha hibát vét az SQL Database-engedélyek konfigurálásakor, és megpróbálja  módosítani az engedélyeket, miután megpróbált jogkivonatot le kérni az alkalmazással, akkor addig nem kap új jogkivonatot a frissített engedélyekkel, amíg a gyorsítótárazott jogkivonat le nem jár.

> [!NOTE]
> Az AAD nem támogatott a SQL Server- és MSI-k esetén. 

### <a name="modify-connection-string"></a>Kapcsolati sztring módosítása

Ne feledje, hogy a *Web.config-ban* vagy a *appsettings.js-ban* végrehajtott módosítások a felügyelt identitással is működnek, ezért csak el kell távolítania a meglévő kapcsolati sztringet a App Service-ban, amelyet Visual Studio az alkalmazás első üzembe helyezésekor hozott létre. Használja a következő parancsot, de a *\<app-name>* helyére írja be az alkalmazás nevét.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>A módosítások közzététele

Már csak közzé kell tennie a módosításait az Azure-ban.

**Ha az [Oktatóanyag: Alkalmazás](app-service-web-tutorial-dotnet-sqldatabase.md)** ASP.NET az Azure-ban a SQL Database használatával, tegye közzé a módosításokat a Visual Studio. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza a **Publish** (Közzététel) elemet.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre. 

**Ha a [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](tutorial-dotnetcore-sqldb-app.md)**(Oktatóanyag: ASP.NET Core és SQL Database-alkalmazás Azure App Service) használatával, tegye közzé a módosításokat a Git használatával a következő parancsokkal:

```bash
git commit -am "configure managed identity"
git push azure main
```

Amikor az új weblapon megjelenik a feladatlista, az alkalmazása kapcsolódik az adatbázishoz a felügyelt identitás segítségével.

![Azure-alkalmazás a Code First migrálás után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Most már ugyanúgy szerkesztheti a feladatlistát, mint korábban.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Felügyelt identitások engedélyezése
> * SQL Database-hozzáférés engedélyezése a felügyelt identitáshoz
> * Az Entity Framework konfigurálása Azure AD-hitelesítés használatára SQL Database
> * Csatlakozás SQL Database Visual Studio Azure AD-hitelesítéssel

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése a Azure App Service](app-service-web-tutorial-custom-domain.md)