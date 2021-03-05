---
title: Gyors útmutató – alkalmazások létrehozása és üzembe helyezése az Azure Spring Cloud-ban
description: Az Azure Spring Cloud alkalmazás üzembe helyezését ismerteti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 727354593488cd5af77408eaa7c8d9e747a0b071
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210308"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Gyors útmutató: alkalmazások létrehozása és üzembe helyezése az Azure Spring Cloud-ban

::: zone pivot="programming-language-csharp"
Ebben a rövid útmutatóban az Azure CLI használatával hozhat létre és telepíthet Service-alkalmazásokat az Azure Spring Cloud szolgáltatásban.

## <a name="prerequisites"></a>Előfeltételek

* A sorozat előző rövid útmutatóinak elvégzése:

  * [Azure Spring Cloud-szolgáltatás kiépítése](spring-cloud-quickstart-provision-service-instance.md).
  * Az [Azure Spring Cloud konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>A mintaalkalmazás letöltése

Ha már használta a Azure Cloud Shellt, váltson át egy helyi parancssorba a következő lépésekhez.

1. Hozzon létre egy új mappát, és a minta alkalmazás-tárház klónozásával.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Navigáljon az adattár könyvtárába.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>PlanetWeatherProvider üzembe helyezése

1. Hozzon létre egy alkalmazást a PlanetWeatherProvider projekthez az Azure Spring Cloud-példányban.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Az automatikus szolgáltatás-regisztráció engedélyezéséhez a `spring.application.name` projekt *appsettings.js* fájljában megegyező nevet kapott az alkalmazásnak:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   A parancs futtatása több percet is igénybe vehet.

1. Módosítsa a könyvtárat a `PlanetWeatherProvider` projekt mappájába.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Hozza létre a telepítendő bináris fájlokat és a *. zip* fájlt.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > A projektfájl a következő XML-fájlt tartalmazza, amely a. *zip* fájlban lévő bináris fájlokat csomagolja a *./publish* mappába való írás után:
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Üzembe helyezés az Azure-ban.

   A következő parancs futtatása előtt győződjön meg arról, hogy a parancssor a Project mappában található.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   A `--main-entry` beállítás megadja a *. zip* fájl gyökérkönyvtárának relatív elérési útját az alkalmazás belépési pontját tartalmazó *. dll* fájlhoz. Miután a szolgáltatás feltölti a *. zip* fájlt, kibontja az összes fájlt és mappát, és megkísérli végrehajtani a belépési pontot a megadott *. dll* fájlban.

   A parancs futtatása több percet is igénybe vehet.

## <a name="deploy-solarsystemweather"></a>SolarSystemWeather üzembe helyezése

1. Hozzon létre egy másik alkalmazást az Azure Spring Cloud-példányában, ezúttal a SolarSystemWeather projekthez:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` a a `SolarSystemWeather` projekt *appsettings.js* fájljában megadott név.

   A parancs futtatása több percet is igénybe vehet.

1. Módosítsa a könyvtárat a `SolarSystemWeather` projektre.

   ```console
   cd ../solar-system-weather
   ```

1. Hozza létre a telepítendő bináris fájlokat és *. zip* fájlt.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Üzembe helyezés az Azure-ban.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   A parancs futtatása több percet is igénybe vehet.

## <a name="assign-public-endpoint"></a>Nyilvános végpont kiosztása

Az alkalmazás teszteléséhez küldjön egy böngészőből egy HTTP GET-kérést az `solar-system-weather` alkalmazásnak.  Ehhez a kérelemhez nyilvános végpont szükséges.

1. A végpont hozzárendeléséhez futtassa a következő parancsot.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --assign-endpoint true
   ```

1. A végpont URL-címének lekéréséhez futtassa a következő parancsot.

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Küldjön egy GET kérelmet az `solar-system-weather` alkalmazásnak. Egy böngészőben nyissa meg a nyilvános URL-címet, amely a `/weatherforecast` végéhez van hozzáfűzve. Például:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

A kimenet JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

Ez a válasz azt mutatja, hogy mind a Service-alkalmazások működnek. Az `SolarSystemWeather` alkalmazás az alkalmazásból beolvasott adatok visszaadása `PlanetWeatherProvider` .
::: zone-end

::: zone pivot="programming-language-java"
Ebből a dokumentumból megtudhatja, hogyan hozhat létre és helyezhet üzembe Service-alkalmazásokat az Azure Spring Cloud használatával:
* Azure CLI
* Maven beépülő modul
* IntelliJ

Az Azure CLI vagy Maven használatával történő üzembe helyezés előtt végezze [el az Azure Spring Cloud példányát kiépítő](spring-cloud-quickstart-provision-service-instance.md) példákat, és [állítsa be a konfigurációs kiszolgálót](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](/java/azure/jdk/)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](/cli/azure/install-azure-cli) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ telepítése](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és [Bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Üzembe helyezési eljárások

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>A Service-alkalmazások helyi létrehozása

1. A minta alkalmazás adattárának klónozása az Azure Cloud-fiókba.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Módosítsa a könyvtárat, és hozza létre a projektet.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

A projekt fordítása körülbelül 5 percet vesz igénybe. Ha elkészült, egyedi JAR-fájlokkal kell rendelkeznie az egyes szolgáltatásokhoz a megfelelő mappákban.

### <a name="create-and-deploy-the-apps"></a>Alkalmazások létrehozása és központi telepítése

1. Állítsa be az alapértelmezett erőforráscsoport nevét és a fürt nevét az alábbi parancsokkal:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Hozzon létre Azure Spring Cloud-szolgáltatásokat az előző lépésben létrehozott JAR-fájlokkal. Három alkalmazást fog létrehozni: **átjáró**, **Auth-szolgáltatás** és **fiók-szolgáltatás**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Az előző lépésben létrehozott alkalmazásokat az Azure-ba kell telepíteni. A következő parancsokkal telepítheti mindhárom alkalmazást:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Nyilvános végpont kiosztása az átjáróhoz

Egy webböngészőn keresztül elérhetővé kell tennie az alkalmazást. Az átjáró alkalmazásnak nyilvános végpontra van szüksége.

1. Rendelje hozzá a végpontot a következő parancs használatával:

    ```azurecli
    az spring-cloud app update -n gateway --assign-endpoint true
    ```

2. A nyilvános IP-címhez tartozó **átjáró** alkalmazás lekérdezése, hogy ellenőrizze, hogy fut-e az alkalmazás:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>A minta alkalmazás-adattár klónozása és összeállítása

1. A git-tárház klónozásához futtassa a következő parancsot:

    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Módosítsa a könyvtárat, és hozza létre a projektet a következő parancs futtatásával:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Konfigurációk készítése és üzembe helyezése az Azure Spring Cloud-ban

1. Konfigurációk előállításához futtassa a következő parancsot a szülő POM-t tartalmazó PiggyMetrics gyökérkönyvtárában. Ha már bejelentkezett az Azure CLI-vel, akkor a parancs automatikusan felveszi a hitelesítő adatokat. Ellenkező esetben a rendszer útmutatást ad a bejelentkezéshez. További információ: [wiki-oldal](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
    ```
    
    A rendszer a következőket kéri:
    * **Modulok:** Válassza `gateway` a, a és a lehetőséget `auth-service` `account-service` .
    * **Előfizetés:** Ez az Azure Spring Cloud-példány létrehozásához használt előfizetés.
    * **Szolgáltatási példány:** Ez az Azure Spring Cloud-példány neve.
    * **Nyilvános végpont:** A megadott projektek listájában adja meg a értéknek megfelelő számot `gateway` .  Ez nyilvános hozzáférést biztosít.

1. A POM mostantól tartalmazza a beépülő modul függőségeit és konfigurációit. Telepítse az alkalmazásokat a következő parancs használatával. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Mintavételezési projekt importálása a IntelliJ-ben

1. Töltse le és csomagolja ki az oktatóanyag forrás-tárházát, vagy klónozott a git használatával: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Nyissa meg az IntelliJ **üdvözlő** párbeszédpanelt, és válassza a **Projekt importálása** elemet az Importálás varázsló megnyitásához.

1. Válassza a `piggymetric` mappa lehetőséget.

    ![Projekt importálása](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Gateway-alkalmazás üzembe helyezése az Azure Spring Cloud-on
Az Azure-ba való üzembe helyezéshez be kell jelentkeznie az Azure-fiókjával Azure Toolkit for IntelliJval, és ki kell választania az előfizetését. A bejelentkezés részleteiért lásd: [telepítés és bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, és válassza az **Azure**-  ->  **üzembe helyezés az Azure Spring Cloud**-ban lehetőséget.

    ![Üzembe helyezés az Azure 1-ben](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. A **név** mezőben adja hozzá a következőt *: átjáró* a meglévő **névhez**.
1. Az összetevő **szövegmezőben** válassza a *com. piggymetrics: Gateway: 1.0-pillanatkép* elemet.
1. Az **előfizetés** szövegmezőben ellenőrizze az előfizetését.
1. A **Spring Cloud** szövegmezőben válassza ki azt az Azure Spring Cloud-példányt, amelyet az [Azure Spring Cloud-példány kiépítése](./spring-cloud-quickstart-provision-service-instance.md)című részben hozott létre.
1. Az *engedélyezéshez* állítsa be a **nyilvános végpontot** .
1. Az **alkalmazás:** szövegmezőben válassza az **alkalmazás létrehozása...** lehetőséget.
1. Adja meg az *átjárót*, majd kattintson **az OK** gombra.

    ![Üzembe helyezés az Azure-ban OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. A párbeszédpanel **Indítás előtt** szakaszában kattintson duplán a *Maven cél futtatása* elemre.
1. A **Working Directory** szövegmezőben navigáljon a *piggymetrics/Gateway* mappára.
1. A **parancssori** szövegmezőbe írja be a *Package-DskipTests* értéket. Kattintson az **OK** gombra.
1. A telepítés elindításához kattintson a **Futtatás** gombra az **Azure Spring Cloud app üzembe helyezése** párbeszédpanel alján. A beépülő modul futtatja a parancsot `mvn package` az `gateway` alkalmazáson, és telepíti a parancs által generált jar-t `package` .

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Auth-szolgáltatás és fiók-szolgáltatásbeli alkalmazások üzembe helyezése az Azure Spring Cloud-ban
A fenti lépéseket megismételve üzembe helyezheti és futtathatja `auth-service` az `account-service` alkalmazásokat az Azure Spring Cloud szolgáltatásban:

1. Módosítsa a **nevet** és **az összetevőt az** alkalmazás azonosításához `auth-service` .
1. Az **alkalmazás:** szövegmezőben válassza az **alkalmazás létrehozása...** lehetőséget az alkalmazások létrehozásához. `auth-service`
1. Győződjön meg arról, hogy a **nyilvános végpont** beállítás *Letiltva* értékre van állítva.
1. A párbeszédpanel **Indítás előtt** szakaszában váltson a **munkakönyvtárra** a *piggymetrics/Auth-Service* mappára.
1. A telepítés elindításához kattintson a **Futtatás** gombra az **Azure Spring Cloud app üzembe helyezése** párbeszédpanel alján. 
1. Ismételje meg ezeket az eljárásokat a konfigurálásához és telepítéséhez `account-service` .
---

A PiggyMetrics alkalmazás eléréséhez navigáljon az előző lépések kimenetében megadott URL-címhez. Például: `https://<service instance name>-gateway.azuremicroservices.io`

![PiggyMetrics elérése](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Az URL-cím megkereséséhez navigáljon a Azure Portal is. 
1. Navigáljon a szolgáltatáshoz
2. **Alkalmazások** kiválasztása
3. **Átjáró** kiválasztása

    ![Alkalmazás navigálása](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Az URL-cím megkeresése az **átjárón | Áttekintő** lap

    ![Alkalmazás második navigálása](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban olyan Azure-erőforrásokat hozott létre, amelyek továbbra is felhalmozzák a díjakat, ha az előfizetésben maradnak. Ha nem szeretne továbblépni a következő rövid útmutatóra, tekintse meg az [erőforrások tisztítása](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources)című témakört. Ellenkező esetben folytassa a következő rövid útmutatóval:

> [!div class="nextstepaction"]
> [Naplók, metrikák és nyomkövetés](spring-cloud-quickstart-logs-metrics-tracing.md)
