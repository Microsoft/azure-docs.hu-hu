---
title: MVC-projekt módosításai az Azure AD-hez való csatlakozáskor
description: Leírja, hogy mi történik az MVC-projekttel, amikor az Azure AD-hoz csatlakozik a Visual Studio csatlakoztatott szolgáltatásaival
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: ab72512b2e5d915dbd4b8a3d7d8446a5932eef9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165447"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Mi történt az MVC-projekttel (Visual Studio Azure Active Directory csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-dotnet-getting-started.md)
> - [mi történt](vs-active-directory-dotnet-what-happened.md)

Ez a cikk egy ASP.NET MVC-projekt pontos módosításait azonosítja, amikor a [Azure Active Directory csatlakoztatott szolgáltatást a Visual Studióval](vs-active-directory-add-connected-service.md)adja hozzá.

További információ a csatlakoztatott szolgáltatás használatáról: [első lépések](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Hozzáadott referenciák

Hatással van a projektfájl (*. NET-referenciák) és a `packages.config` (NuGet-hivatkozások) elemre.

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft. IdentityModel. Protocol. Extensions |
| NET NuGet | Microsoft. Owin |
| NET NuGet | Microsoft.Owin.Host.SystemWeb |
| NET NuGet | Microsoft. Owin. Security |
| NET NuGet | Microsoft.Owin.Security.Cookies |
| NET NuGet | Microsoft.Owin.Security.OpenIdConnect |
| NET NuGet | Owin |
| .NET        | System. IdentityModel |
| NET NuGet | System. IdentityModel. tokens. JWT |
| .NET        | System.Runtime.Serialization |

További hivatkozások ha a címtár- **adatok olvasása** lehetőséget választotta:

| Típus | Referencia |
| --- | --- |
| NET NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (csak Visual Studio 2015) |
| NET NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| NET NuGet | Microsoft. EDM |
| NET NuGet | Microsoft. OData |
| NET NuGet | Microsoft. Services. Services. Client |
| NET NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (csak Visual Studio 2015) |
| NET NuGet | System. térbeli |

A következő hivatkozások törlődnek (csak ASP.NET 4 projekt, a Visual Studio 2015-as verziójában):

| Típus | Referencia |
| --- | --- |
| NET NuGet | Microsoft. AspNet. Identity. Core |
| NET NuGet | Microsoft. AspNet. Identity. EntityFramework |
| NET NuGet | Microsoft. AspNet. Identity. Owin |

## <a name="project-file-changes"></a>Projektfájl módosításai

- Állítsa a tulajdonságot `IISExpressSSLPort` egy külön számra.
- Állítsa a tulajdonságot `WebProject_DirectoryAccessLevelKey` 0 vagy 1 értékre, ha a **Címtáradatok olvasása** lehetőséget választotta.
- Állítsa be a tulajdonságot `IISUrl` , hogy a `https://localhost:<port>/` hol `<port>` egyezzen az `IISExpressSSLPort` értékkel.

## <a name="webconfig-or-appconfig-changes"></a>web.config vagy app.config módosítása

- A következő konfigurációs bejegyzések lettek hozzáadva:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- `<dependentAssembly>`A és a csomópont alatt hozzáadott elemek `<runtime><assemblyBinding>` `System.IdentityModel.Tokens.Jwt` `Microsoft.IdentityModel.Protocol.Extensions` .

További változtatások a **Címtáradatok olvasása** lehetőség bejelölésével:

- Az alábbi konfigurációs bejegyzést adta hozzá a következőhöz `<appSettings>` :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- A következő elemek lettek hozzáadva a `<configuration>` projekthez – az MDF-fájl és a Project-Catalog-ID értéke:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Hozzáadott `<dependentAssembly>` elemek a `<runtime><assemblyBinding>` csomópont alatt a `Microsoft.Data.Services.Client` , `Microsoft.Data.Edm` és rendszerhez `Microsoft.Data.OData` .

## <a name="code-changes-and-additions"></a>Kód módosításai és kiegészítései

- Az attribútum hozzá lett adva a `[Authorize]` `Controllers/HomeController.cs` és más meglévő vezérlőkhöz.

- Egy hitelesítési indítási osztály lett hozzáadva, `App_Start/Startup.Auth.cs` amely az Azure ad-hitelesítés indítási logikáját tartalmazza. Ha a **Címtáradatok olvasása** lehetőséget választotta, ez a fájl a OAuth-kód fogadására és a hozzáférési tokenek cseréjére szolgáló kódot is tartalmaz.

- Hozzá lett adva egy vezérlő osztály, `Controllers/AccountController.cs` amely tartalmazza a `SignIn` és a `SignOut` metódusokat.

- Egy részleges nézet lett hozzáadva, `Views/Shared/_LoginPartial.cshtml` amely a és a műveleti hivatkozását tartalmazza `SignIn` `SignOut` .

- A rendszer részleges nézetet adott hozzá, `Views/Account/SignoutCallback.cshtml` amely HTML-kódot tartalmaz a kijelentkezési kezelőfelülethez.

- Frissítette a metódust, amely azt a `Startup.Configuration` hívást tartalmazza, hogy `ConfigureAuth(app)` az osztály már létezik; egyéb esetben olyan `Startup` osztályt adott hozzá, amely a metódus meghívását is tartalmazza.

- Hozzáadva `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) vagy `Service References/Azure AD/ConnectedService.json` (visual Studio 2015), amely azokat az információkat tartalmazza, amelyeket a Visual Studio használ a csatlakoztatott szolgáltatás hozzáadásának nyomon követésére.

- Ha a **Címtáradatok olvasása** lehetőséget választotta, a rendszer hozzáadta `Models/ADALTokenCache.cs` és `Models/ApplicationDbContext.cs` a jogkivonat-gyorsítótárazást is támogatja. További vezérlőt is hozzáadhat, és megtekintheti a felhasználói profil adatainak az Azure Graph API-kkal való elérésének szemléltetését:, `Controllers/UserProfileController.cs` `Views/UserProfile/Index.cshtml` és `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Fájl biztonsági mentése (Visual Studio 2015)

A csatlakoztatott szolgáltatás hozzáadásakor a Visual Studio 2015 biztonsági mentést készít a módosított és eltávolított fájlokról. A rendszer az összes érintett fájlt menti a mappába `Backup/AzureAD` . A Visual Studio 2017-es és újabb verziói nem hoznak létre biztonsági másolatokat.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy Azure AD-alkalmazást abban a tartományban, amelyet a csatlakoztatott szolgáltatás hozzáadásakor kiválasztott.
- Frissítette az alkalmazást, hogy tartalmazza a **címtár-adatolvasási** engedélyt, ha ez a beállítás be van jelölve.

[További információ a Azure Active Directoryról](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](./authentication-vs-authorization.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)