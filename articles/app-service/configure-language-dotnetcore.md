---
title: ASP.NET Core alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy ASP.NET Core alkalmazást a natív Windows-példányokban, vagy egy előre elkészített Linux-tárolóban, Azure App Service. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5e4351529fb7b6a66f554182a195bc26f79c0e2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889484"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>ASP.NET Core alkalmazás konfigurálása Azure App Servicehoz

> [!NOTE]
> A .NET-keretrendszer ASP.NET tekintse meg a [ASP.NET alkalmazás konfigurálása a Azure app Servicehoz](configure-language-dotnet-framework.md) című témakört.

ASP.NET Core alkalmazásokat lefordított bináris fájlként kell telepíteni Azure App Service. A Visual Studio közzétételi eszköze létrehozza a megoldást, majd közvetlenül telepíti a lefordított bináris fájlokat, míg a App Service üzembe helyezési motor először a kódrészletet telepíti, majd lefordítja a bináris fájlokat.

Ez az útmutató a ASP.NET Core fejlesztőknek szóló főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, először kövesse a [ASP.net Core](quickstart-dotnetcore.md) rövid útmutató és a [ASP.net Core SQL Database oktatóanyagot](tutorial-dotnetcore-sqldb-app.md) .

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Támogatott .NET Core Runtime-verziók megjelenítése

App Service a Windows-példányok már telepítve vannak az összes támogatott .NET Core-verzióval. A .NET Core futtatókörnyezet és az SDK elérhető verzióinak megjelenítéséhez navigáljon a `https://<app-name>.scm.azurewebsites.net/DebugConsole` következő parancsra a böngészőalapú konzolon:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>.NET Core verzió megjelenítése

Az aktuális .NET Core-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott .NET Core-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>A .NET Core verziójának beállítása

::: zone pivot="platform-windows"  

Állítsa be a célként megadott keretrendszert a ASP.NET Core projekthez tartozó Project-fájlban. További információ: [válassza ki a](/dotnet/core/versions/selection) .net Core-ban használandó verziót a .net Core dokumentációjában.

::: zone-end

::: zone pivot="platform-linux"

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a .net Core verzió 3,1-re való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>A Build Automation testreszabása

Ha a Build Automation használatával git vagy zip csomagok segítségével helyezi üzembe az alkalmazást, akkor a App Service az alábbi lépésekkel hozhat létre automatizálási lépéseket:

1. Futtassa az egyéni parancsfájlt, ha a meg van adva `PRE_BUILD_SCRIPT_PATH` .
1. Futtassa `dotnet restore` a parancsot a NuGet-függőségek visszaállításához.
1. A futtatásával `dotnet publish` hozzon létre egy bináris fájlt éles környezetben.
1. Futtassa az egyéni parancsfájlt, ha a meg van adva `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND``POST_BUILD_COMMAND`a és a környezeti változók, amelyek alapértelmezés szerint üresek. Az előkészítő parancsok futtatásához adja meg a következőt: `PRE_BUILD_COMMAND` . A létrehozás utáni parancsok futtatásához adja meg a következőt: `POST_BUILD_COMMAND` .

A következő példa a két változót adja meg egy több parancshoz, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

További környezeti változók a Build Automation testreszabásához: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

A ASP.NET Core alkalmazások Linux rendszeren való futtatásáról és App Serviceáról további információt a [Oryx dokumentációjában talál: a .net Core-alkalmazások észlelése és építése](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-szel az alkalmazás kódján kívül is [megadhatja az alkalmazások beállításait](configure-common.md#configure-app-settings). Ezt követően bármely osztályban elérheti őket a standard ASP.NET Core függőségi befecskendezési minta használatával:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Ha például a App Service és a *appsettings.js* megegyező nevű Alkalmazásbeállítás van konfigurálva, akkor az App Service érték elsőbbséget élvez az *appsettings.js* értéknél. Az érték helyi *appsettings.js* lehetővé teszi az alkalmazás helyi hibakeresését, de a app Service érték lehetővé teszi az alkalmazás éles környezetben való futtatását. A kapcsolatok karakterláncai ugyanúgy működnek. Így megtarthatja az alkalmazási titkokat a Code repositoryn kívül, és a kód módosítása nélkül is elérheti a megfelelő értékeket.

> [!NOTE]
> Figyelje meg, hogy a *appsettings.json* lévő [hierarchikus konfigurációs adatai](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) a `:` .net Core szabványnak megfelelő elválasztóval érhetők el. A App Service adott hierarchikus konfigurációs beállításainak felülbírálásához állítsa az Alkalmazásbeállítások nevét ugyanazzal a tagolt formátummal a kulcsban. a következő példát futtathatja a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Több projektből álló megoldások üzembe helyezése

Ha egy Visual Studio-megoldás több projektet is tartalmaz, a Visual Studio közzétételi folyamata már tartalmazza a telepítendő projekt kiválasztását. Ha üzembe helyezi a App Service üzembe helyezési motort, például a git vagy a ZIP üzembe helyezését, a Build Automation bekapcsolva állapotba kerül, akkor a App Service üzembe helyezési motor kiválasztja az App Service alkalmazásként megtalált első webhelyt vagy webalkalmazás-projektet. Megadhatja, hogy melyik projektet App Service kell használnia az `PROJECT` alkalmazás beállításainak megadásával. Futtassa például a következőt a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

A ASP.NET Core [beépített naplózási szolgáltatót biztosít a app Servicehoz](/aspnet/core/fundamentals/logging/#azure-app-service). A projekt *program. cs fájljában* adja hozzá a szolgáltatót az alkalmazáshoz a `ConfigureLogging` bővítmény módszerével, ahogy az az alábbi példában is látható:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Ezután a [standard .net Core mintával](/aspnet/core/fundamentals/logging)konfigurálhatja és létrehozhatja a naplókat.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

A App Service ASP.NET Core alkalmazások hibaelhárításával kapcsolatos további információkért lásd: [a Azure app Service-és IIS-ASP.net Core](/aspnet/core/test/troubleshoot-azure-iis) hibaelhárítása

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek oldalának beolvasása

Ha a ASP.NET Core-alkalmazás kivételt hoz létre a Visual Studio debuggerben, a böngésző egy részletes kivételt jelenít meg, de a App Service a lapot általános **HTTP 500** -hiba váltja fel, vagy **hiba történt a kérelem feldolgozása során.** üzenetet küldi vissza. A App Service részletes kivétel lapjának megjelenítéséhez adja hozzá az alkalmazás `ASPNETCORE_ENVIRONMENT` beállításait az alkalmazáshoz a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának tudnia kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, konfigurálja a továbbított fejlécek köztes middleware-t az *indítás során. cs*:

- Konfigurálja a middleware-t a [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , hogy továbbítsa a `X-Forwarded-For` és a `X-Forwarded-Proto` fejléceket a alkalmazásban `Startup.ConfigureServices` .
- Adjon hozzá magánhálózati IP-címtartományt az ismert hálózatokhoz, hogy a köztes kapcsolat megbízható legyen a App Service Load balancerben.
- A [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metódus meghívása a `Startup.Configure` többi middleware meghívása előtt.

A három elem együttes elhelyezésével a kód a következő példához hasonlóan néz ki:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        // These three subnets encapsulate the applicable Azure subnets. At the moment, it's not possible to narrow it down further.
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

További információkért lásd: [a ASP.net Core konfigurálása a proxykiszolgáló és a terheléselosztó működéséhez](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)

::: zone-end
