---
title: Gyorsútmutató – Az első Azure Spring Cloud-alkalmazás üzembe helyezése
description: Ebben a rövid útmutatóban egy Spring Cloud-alkalmazást helyezünk üzembe az Azure Spring Cloud-on.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 520a36a0896cc2b4b14366350f66d365ea04a557
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612796"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése

::: zone pivot="programming-language-csharp"
Ez a rövid útmutató ismerteti, hogyan helyezhet üzembe egy egyszerű Azure Spring Cloud Service-alkalmazást az Azure-ban való futtatáshoz.

>[!NOTE]
> Az Azure Spring Cloud Steeltoe-támogatása jelenleg nyilvános előzetes verzióként érhető el. A nyilvános előzetes ajánlatok lehetővé teszik, hogy az ügyfelek a hivatalos kiadásuk előtt új funkciókkal kísérletezzenek.  A nyilvános előzetes verzió funkcióit és szolgáltatásait nem éles használatra szánták.  További információ az előzetes verziók támogatásáról: [Gyakori kérdések](https://azure.microsoft.com/support/faq/) vagy a [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md)fájl.

Ennek a rövid útmutatónak a követésével megismerheti a következőket:

> [!div class="checklist"]
> * Alapszintű Steeltoe .NET Core-projekt létrehozása
> * Azure Spring Cloud Service-példány kiépítése
> * Az alkalmazás létrehozása és üzembe helyezése nyilvános végponttal
> * Naplók streamelése valós időben

Az ebben a rövid útmutatóban használt alkalmazás kódja egy .NET Core web API-projekt sablonnal létrehozott egyszerű alkalmazás. A példa elvégzése után az alkalmazás elérhető lesz az interneten, és a Azure Portal és az Azure CLI használatával felügyelhető.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Az Azure Spring Cloud Service a .NET Core 3,1-as és újabb verzióit támogatja.
* Az [Azure CLI verziója 2.0.67 vagy újabb verzió](/cli/azure/install-azure-cli).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Győződjön meg arról, hogy az Azure CLI verziója 2.0.67 vagy újabb:

```azurecli
az --version
```

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

1. Bejelentkezés az Azure CLI-be

    ```azurecli
    az login
    ```

1. Ha egynél több előfizetéssel rendelkezik, válassza ki azt, amelyet ehhez a rövid útmutatóhoz szeretne használni.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Steeltoe .NET Core-projekt létrehozása

A Visual Studióban hozzon létre egy "Hello-World" nevű ASP.NET Core webalkalmazást API-projekt sablonnal. Figyelje meg, hogy egy automatikusan generált WeatherForecastController lesz, amely később a tesztelési végpont lesz.

1. Hozzon létre egy mappát a projekt forráskódjának kódjához, és hozza létre a projektet.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Navigáljon a projekt könyvtárába.

   ```console
   cd hello-world
   ```

1. A fájl *appSettings.js* szerkesztésével adja hozzá a következő beállításokat:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. *appsettings.json* is megváltoztathatja a kategória naplózási szintjét `Microsoft` `Warning` `Information` . Ez a változás biztosítja, hogy a naplók akkor legyenek előkészítve, amikor egy későbbi lépésben megtekinti a folyamatos átviteli naplókat.

   A fájl *appsettings.js* most az alábbi példához hasonlóan néz ki:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Függőségek és `Zip` feladatok hozzáadása a *. csproj* fájlhoz:

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   A csomagok a Steeltoe Service Discovery és az Azure Spring Cloud ügyféloldali kódtár számára készültek. A `Zip` feladat az Azure-ba való üzembe helyezés. A parancs futtatásakor a `dotnet publish` bináris fájlokat a *közzétételi* mappában hozza létre, és ez a feladat a *közzétételi* mappát egy *. zip* fájlba helyezi, amelyet feltölt az Azure-ba.

3. A *program. cs* fájlban adjon hozzá egy, `using` Az Azure Spring Cloud ügyféloldali kódtárat használó irányelvet és kódot:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. A *Startup. cs* fájlban adjon hozzá egy olyan `using` irányelvet és kódot, amely a `ConfigureServices` és metódusok végén a Steeltoe szolgáltatás felderítését használja `Configure` :

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Hozza létre a projektet, és győződjön meg arról, hogy nincsenek fordítási hibák.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Szolgáltatási példány kiépítése

Az alábbi eljárás az Azure Spring Cloud egy példányát hozza létre a Azure Portal használatával.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com/). 

1. A felső keresőmezőbe keressen az *Azure Spring Cloud* kifejezésre.

1. Válassza az *Azure Spring Cloud* lehetőséget az eredmények közül.

   ![ASC ikon kezdete](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Az Azure Spring Cloud oldalon válassza a **+ Hozzáadás** lehetőséget.

   ![ASC ikon hozzáadása](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Tekintse meg a következő útmutatót:

   * **Előfizetés**: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.
   * **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot. Az itt megadott nevet a későbbi lépésekben fogjuk használni **\<resource group name\>** .
   * **Szolgáltatás adatai/neve**: adja meg a **\<service instance name\>** .  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
   * **Régió**: válassza ki a szolgáltatási példány régióját.

   ![ASC-portál indítása](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Válassza **a felülvizsgálat és létrehozás** lehetőséget.

## <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése

Az alábbi eljárás a korábban létrehozott projektet hozza létre és telepíti.

1. Győződjön meg arról, hogy a parancssor továbbra is a Project mappában van.

1. Futtassa a következő parancsot a projekt létrehozásához, a bináris fájlok közzétételéhez, és tárolja a bináris fájlokat egy *. zip* -fájlban a Project mappában.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Hozzon létre egy alkalmazást az Azure Spring Cloud-példányban egy hozzárendelt nyilvános végponttal. Használja ugyanazt a "Hello-World" nevű nevet, amelyet a *appsettings.jsban adott meg*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --assign-endpoint --runtime-version NetCore_31
   ```

1. Telepítse a *. zip* fájlt az alkalmazásba.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   `--main-entry`Ez a beállítás azonosítja az alkalmazás belépési pontját tartalmazó *. dll* fájlt. Miután a szolgáltatás feltölti a *. zip* fájlt, kibontja az összes fájlt és mappát, és megpróbálja végrehajtani a belépési pontot a által megadott *. dll* fájlban `--main-entry` .

   Az alkalmazás üzembe helyezésének befejezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy telepítve van-e, nyissa meg az **alkalmazások** panelt a Azure Portal.

## <a name="test-the-app"></a>Az alkalmazás tesztelése

Az üzembe helyezés befejezése után az alábbi URL-címen érheti el az alkalmazást:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

Az alkalmazás a következő példához hasonló JSON-adatokkal tér vissza:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Naplók streamelése valós időben

A következő parancs használatával valós idejű naplókat kaphat az alkalmazásból.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

A naplók megjelennek a kimenetben:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> `az spring-cloud app logs -h`A használatával több paramétert és a log stream funkcióit is megismerheti.

A speciális log Analytics-funkciókhoz látogasson el a **naplók** lapra a [Azure Portal](https://portal.azure.com/)menüjében. A naplók itt néhány perc késéssel rendelkeznek.
[![Naplók Analytics ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Ez a rövid útmutató ismerteti, hogyan helyezhet üzembe egy egyszerű Azure Spring Cloud Service-alkalmazást az Azure-ban való futtatáshoz. 

Az oktatóanyagban használt alkalmazás kódja egy egyszerű, rugós Inicializálással készített alkalmazás. A példa elvégzése után az alkalmazás online állapotba kerül, és a Azure Portal használatával felügyelhető.

Ez a rövid útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Alapszintű Spring Cloud-projekt létrehozása
> * Szolgáltatási példány kiépítése
> * Az alkalmazás létrehozása és üzembe helyezése nyilvános végponttal
> * Naplók streamelése valós időben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A JDK 8 telepítése](/java/azure/jdk/)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](/cli/azure/install-azure-cli) , és az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="generate-a-spring-cloud-project"></a>Spring Cloud-projekt létrehozása

Kezdje a [Spring inicializáló](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) használatával egy olyan minta projekt létrehozásához, amely az Azure Spring Cloud ajánlott függőségeivel rendelkezik. Az alábbi képen a projekt inicializálási beállítása látható.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```
Vegye figyelembe, hogy ez a példa a Java 8-as verzióját használja.  Ha a Java 11-es verzióját szeretné használni, módosítsa a beállítást a **projekt metaadatai** területen.

  ![Inicializálás lap](media/spring-cloud-quickstart-java/initializr-page.png)

1. Kattintson a **generált** elemre, ha az összes függőség be van állítva. Töltse le és csomagolja ki a csomagot, majd hozzon létre egy webes vezérlőt egy egyszerű webalkalmazáshoz a `src/main/java/com/example/hellospring/HelloController.java` következőképpen:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud-példány kiépítése

Az alábbi eljárás az Azure Spring Cloud egy példányát hozza létre a Azure Portal használatával.

1. Az új lapon nyissa meg a [Azure Portal](https://ms.portal.azure.com/). 

2. A felső keresőmezőbe keressen az *Azure Spring Cloud* kifejezésre.

3. Válassza az *Azure Spring Cloud* lehetőséget az eredmények közül.

    ![ASC ikon kezdete](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Az Azure Spring Cloud oldalon kattintson a **+ Hozzáadás** gombra.

    ![ASC ikon hozzáadása](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Tekintse meg a következő útmutatót:
    - **Előfizetés**: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.
    - **Erőforráscsoport**: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás. Ezt a későbbi lépések során fogjuk használni **\<resource group name\>** .
    - **Szolgáltatás adatai/neve**: adja meg a **\<service instance name\>** .  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - **Hely**: válassza ki a szolgáltatási példány régióját.

    ![ASC-portál indítása](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kattintson az **Áttekintés és létrehozás** elemre.

## <a name="build-and-deploy-the-app"></a>Az alkalmazás létrehozása és üzembe helyezése
    
#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)
Az alábbi eljárás az Azure CLI használatával hozza létre és telepíti az alkalmazást. Hajtsa végre a következő parancsot a projekt gyökerében.

1. A projekt összeállítása a Maven használatával:

    ```console
    mvn clean package -DskipTests
    ```

1. (Ha még nem telepítette) Telepítse az Azure Spring Cloud-bővítményt az Azure CLI-hez:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Hozza létre az alkalmazást a hozzárendelt nyilvános végponttal. Ha a Java 11-et használja, adja meg a `--runtime-version=Java_11` kapcsolót.

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --assign-endpoint true
    ```

1. Telepítse az alkalmazás jar-fájlját ( `target\hellospring-0.0.1-SNAPSHOT.jar` Windows rendszeren):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Az alkalmazás üzembe helyezésének befejezése néhány percet vesz igénybe. Annak ellenőrzéséhez, hogy telepítve van-e, nyissa meg az **alkalmazások** panelt a Azure Portal. Ekkor meg kell jelennie az alkalmazás állapotának.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Az alábbi eljárás az Azure Spring Cloud IntelliJ beépülő moduljának használatával helyezi üzembe a minta alkalmazást a IntelliJ-ÖTLETben.  

### <a name="import-project"></a>Projekt importálása

1. Nyissa meg az IntelliJ **üdvözlő** párbeszédpanelt, és válassza a **Projekt importálása** elemet az Importálás varázsló megnyitásához.
1. Válassza a `hellospring` mappa lehetőséget.

    ![Projekt importálása](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése
Az Azure-ba való üzembe helyezéshez be kell jelentkeznie az Azure-fiókjával, és ki kell választania az előfizetését.  A bejelentkezés részleteiért lásd: [telepítés és bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, és válassza az **Azure**-  ->  **üzembe helyezés az Azure Spring Cloud**-ban lehetőséget.

    [![Üzembe helyezés az Azure ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) 1-ben](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Fogadja el az alkalmazás nevét a **név** mezőben. A **név** a konfigurációra hivatkozik, nem az alkalmazás nevére. A felhasználóknak általában nem kell módosítaniuk.
1. Az összetevő **szövegmezőben** válassza a *hellospring-0.0.1-Snapshot. jar* elemet.
1. Az **előfizetés** szövegmezőben ellenőrizze az előfizetését.
1. A **Spring Cloud** szövegmezőben válassza ki azt az Azure Spring Cloud-példányt, amelyet az [Azure Spring Cloud-példány kiépítése](./spring-cloud-quickstart-provision-service-instance.md)című részben hozott létre.
1. Az *engedélyezéshez* állítsa be a **nyilvános végpontot** .
1. Az **alkalmazás:** szövegmezőben válassza az **alkalmazás létrehozása...** lehetőséget.
1. Adja meg a *hellospring*, majd kattintson **az OK** gombra.

    [![Üzembe helyezés az Azure-ban ok ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. A telepítés elindításához kattintson a **Futtatás** gombra az **Azure Spring Cloud app üzembe helyezése** párbeszédpanel alján. A beépülő modul futtatja a parancsot `mvn package` az `hellospring` alkalmazáson, és telepíti a parancs által generált jar-t `package` .
---

Az üzembe helyezés befejezése után az alkalmazás a következő címen érhető el: `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Alkalmazás elérése böngészőből ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Valós idejű folyamatos átviteli naplók

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

A következő parancs használatával valós idejű naplókat kaphat az alkalmazásból.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
A naplók az eredmények között jelennek meg:

[![Folyamatos átviteli naplók ](media/spring-cloud-quickstart-java/streaming-logs.png)](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> `az spring-cloud app logs -h`A használatával több paramétert és a log stream funkcióit is megismerheti.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Válassza az **Azure Explorer**, majd a **Spring Cloud** elemet.
1. Kattintson a jobb gombbal a futó alkalmazásra.
1. Válassza a **folyamatos átviteli naplók** lehetőséget a legördülő listából.
1. Válassza a példány lehetőséget.

    [![Adatfolyam-naplók ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) kiválasztása](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. A folyamatos átviteli napló a kimeneti ablakban jelenik meg.

    [![Adatfolyam-napló kimenete ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

A speciális naplók elemzési szolgáltatásaihoz látogasson el a **naplók** lapra a [Azure Portal](https://portal.azure.com/)menüjében. A naplók itt néhány perc késéssel rendelkeznek.

[![Naplók Analytics ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben olyan Azure-erőforrásokat hozott létre, amelyek továbbra is felhalmozzák a díjakat, amíg az előfizetésben maradnak. Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölje az erőforráscsoportot a portálról, vagy futtassa a következő parancsot az Azure CLI-ben:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Alapszintű Azure Spring Cloud-projekt létrehozása
> * Szolgáltatási példány kiépítése
> * Az alkalmazás létrehozása és üzembe helyezése nyilvános végponttal
> * Naplók streamelése valós időben

Ha többet szeretne megtudni arról, hogyan használhatók az Azure Spring-funkciók, folytassa a gyors üzembe helyezési sorozattal, amely egy minta alkalmazást telepít az Azure Spring Cloud-ba:

> [!div class="nextstepaction"]
> [A szolgáltatás létrehozása és futtatása](spring-cloud-quickstart-sample-app-introduction.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
