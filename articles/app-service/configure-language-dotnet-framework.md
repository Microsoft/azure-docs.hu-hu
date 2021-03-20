---
title: ASP.NET-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat ASP.NET alkalmazást Azure App Serviceban. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92744166"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>ASP.NET-alkalmazás konfigurálása Azure App Servicehoz

> [!NOTE]
> ASP.NET Core esetében tekintse meg a [ASP.net Core alkalmazás konfigurálása a Azure app Servicehoz](configure-language-dotnetcore.md) című témakört.

A ASP.NET alkalmazásokat lefordított bináris fájlként kell telepíteni Azure App Service. A Visual Studio közzétételi eszköze létrehozza a megoldást, majd közvetlenül telepíti a lefordított bináris fájlokat, míg a App Service üzembe helyezési motor először a kódrészletet telepíti, majd lefordítja a bináris fájlokat.

Ez az útmutató a ASP.NET-fejlesztők számára biztosít kulcsfontosságú fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [ASP.net](quickstart-dotnet-framework.md) rövid útmutató és a [ASP.net SQL Database oktatóanyagot](app-service-web-tutorial-dotnet-sqldatabase.md) .

## <a name="show-supported-net-framework-runtime-versions"></a>Támogatott .NET-keretrendszer futtatókörnyezet-verzióinak megjelenítése

App Service a Windows-példányok már telepítve vannak a .NET-keretrendszer összes támogatott verziójával. A .NET-keretrendszer futtatókörnyezetének és SDK-verzióinak megjelenítéséhez nyissa meg a `https://<app-name>.scm.azurewebsites.net/DebugConsole` megfelelő parancsot a böngésző alapú konzolon:

CLR 4 futtatókörnyezeti verziók esetén (.NET-keretrendszer 4-es vagy újabb verzió):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

Lehetséges, hogy a .NET-keretrendszer legújabb verziója nem érhető el azonnal.

CLR 2 futtatókörnyezeti verziók esetén (.NET-keretrendszer 3,5-es és újabb verziói):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Az aktuális .NET-keretrendszer futtatókörnyezet-verziójának megjelenítése

Futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

A érték `v4.0` azt jelenti, hogy a legújabb CLR 4 verzió (.NET-keretrendszer 4. x) van használatban. Az érték `v2.0` azt jelenti, hogy a CLR 2 verziója (.NET-keretrendszer 3,5) van használatban.

## <a name="set-net-framework-runtime-version"></a>A .NET-keretrendszer futtatókörnyezet-verziójának beállítása

Alapértelmezés szerint a App Service a .NET-keretrendszer legújabb verzióját használja a ASP.NET-alkalmazás futtatásához. Ha az alkalmazást a .NET-keretrendszer 3,5-es verziójával szeretné futtatni, futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com) (v 2.0 a CLR 2-et jelenti):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](configure-common.md#configure-app-settings) és a kapcsolati karakterláncok az alkalmazás kódján kívül is megadhatók. Ezt követően bármely osztályban elérheti őket a standard ASP.NET minta használatával:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Ha a App Service és a *web.configban* azonos nevű alkalmazás-beállítást állít be, akkor a app Service érték elsőbbséget élvez a *web.config* értékével szemben. A helyi *web.config* érték lehetővé teszi az alkalmazás helyi hibakeresését, de a app Service érték lehetővé teszi az alkalmazás futtatását a termékben az éles környezetben. A kapcsolatok karakterláncai ugyanúgy működnek. Így megtarthatja az alkalmazási titkokat a Code repositoryn kívül, és a kód módosítása nélkül is elérheti a megfelelő értékeket.

## <a name="deploy-multi-project-solutions"></a>Több projektből álló megoldások üzembe helyezése

Ha egy Visual Studio-megoldás több projektet is tartalmaz, a Visual Studio közzétételi folyamata már tartalmazza a telepítendő projekt kiválasztását. Ha üzembe helyezi a App Service üzembe helyezési motort, például a git vagy a ZIP üzembe helyezését, a Build Automation bekapcsolva állapotba kerül, akkor a App Service üzembe helyezési motor kiválasztja az App Service alkalmazásként megtalált első webhelyt vagy webalkalmazás-projektet. Megadhatja, hogy melyik projektet App Service kell használnia az `PROJECT` alkalmazás beállításainak megadásával. Futtassa például a következőt a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek oldalának beolvasása

Ha a ASP.NET-alkalmazás kivételt hoz létre a Visual Studio debuggerben, a böngésző egy részletes kivétel lapot jelenít meg, de App Service a lapot egy általános hibaüzenet váltja fel. A App Service részletes kivétel lapjának megjelenítéséhez nyissa meg a *Web.config* fájlt, és adja hozzá a elemet `<customErrors mode="Off"/>` a `<system.web>` elemhez. Például:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Telepítse újra az alkalmazást a frissített *Web.config*. Ekkor ugyanazt a részletes kivételt ismertető oldalt kell látnia.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

A diagnosztikai üzeneteket a [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace)használatával adhatja hozzá az alkalmazás kódjához. Például: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database használatával](app-service-web-tutorial-dotnet-sqldatabase.md)
