---
title: 'Gyors útmutató: Java-alkalmazás létrehozása Azure App Service'
description: Percek alatt üzembe helyezheti az első Java-Hello World Azure App Service. A Mavenhez készült Azure Web App beépülő modul lehetővé teszi a Java-alkalmazások üzembe helyezését.
keywords: Azure, app Service, Web App, Windows, Linux, Java, Maven, gyors útmutató
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050375"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Gyors útmutató: Java-alkalmazás létrehozása Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](/cli/azure/get-started-with-azure-cli) -t a [Mavenhez készült Azure Web App beépülő modullal](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) egy. jar-fájl vagy. War-fájl üzembe helyezéséhez. 

Ehhez a cikkhez is tartoznak IDE-verziók. Ismerkedjen meg [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) rövid útmutatóval vagy [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)gyors útmutatóval.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Egy <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">Azure-fiók</abbr> aktív <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Az [Azure CLI](/cli/azure/install-azure-cli)-vel.

+ A [Java fejlesztői csomag](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója.

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

A Maven beépülő modul a fiók hitelesítő adatait használja az Azure CLI-ből a App Services üzembe helyezéséhez. [A folytatás előtt jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli) . További információ: [hitelesítés a Maven beépülő](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)modulokkal.

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Java-alkalmazás létrehozása

# <a name="java-se"></a>[Java SE](#tab/javase)

A [Spring Boot első lépések](https://github.com/spring-guides/gs-spring-boot) minta projekt klónozása.

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Váltsa a könyvtárat a befejezett projektre.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

A következő Maven-parancs végrehajtásával hozzon létre egy nevű új alkalmazást a Cloud Shell promptban `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ezután módosítsa a munkakönyvtárat a projekt mappájába:

```bash
cd helloworld
```

---

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. a Maven beépülő modul konfigurálása

Futtassa az alábbi Maven-parancsot az üzembe helyezés konfigurálásához. Ez a parancs segítséget nyújt a App Service operációs rendszer, a Java-verzió és a Tomcat-verzió beállításához.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Amikor a rendszer a **Subscription** (előfizetés) értékre kérdez rá, adja meg a helyes `Subscription` értéket a sor elején álló szám beírásával.
1. Ha a rendszer a **webalkalmazás** beállítását kéri, fogadja el az alapértelmezett beállítást az `<create>` ENTER billentyű lenyomásával vagy egy meglévő alkalmazás kiválasztásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, válassza a **Windows** lehetőséget a beírásával `3` .
1. Ha a rendszer a **díjszabási** csomaggal kéri, válassza a **B2** lehetőséget a beírásával `2` .
1. Használja az alapértelmezett Java-verziót ( **Java 8**) az ENTER billentyű lenyomásával.
1. Végül az utolsó kérdésnél hagyja jóvá a beállításokat az Enter billentyűvel.

    Az összefoglalás kimenete az alább látható kódrészlethez hasonlóan fog kinézni.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Amikor a rendszer a **Subscription** (előfizetés) értékre kérdez rá, adja meg a helyes `Subscription` értéket a sor elején álló szám beírásával.
1. Ha a rendszer a **webalkalmazás** beállítását kéri, fogadja el az alapértelmezett beállítást az `<create>` ENTER billentyű lenyomásával vagy egy meglévő alkalmazás kiválasztásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, válassza a **Windows** lehetőséget a beírásával `3` .
1. Ha a rendszer a **díjszabási** csomaggal kéri, válassza a **B2** lehetőséget a beírásával `2` .
1. Használja az alapértelmezett Java-verziót ( **Java 8**) az ENTER billentyű lenyomásával.
1. A **Tomcat 8,5** alapértelmezett webes tárolót az ENTER billentyű lenyomásával használhatja.
1. Végül az utolsó kérdésnél hagyja jóvá a beállításokat az Enter billentyűvel.

    Az összefoglalás kimenete az alább látható kódrészlethez hasonlóan fog kinézni.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Amikor a rendszer a **Subscription** (előfizetés) értékre kérdez rá, adja meg a helyes `Subscription` értéket a sor elején álló szám beírásával.
1. Ha a rendszer a **webalkalmazás** beállítását kéri, fogadja el az alapértelmezett beállítást az `<create>` ENTER billentyű lenyomásával vagy egy meglévő alkalmazás kiválasztásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, az ENTER billentyű lenyomásával válassza a **Linux** lehetőséget.
1. Ha a rendszer a **díjszabási** csomaggal kéri, válassza a **B2** lehetőséget a beírásával `2` .
1. Használja az alapértelmezett Java-verziót ( **Java 8**) az ENTER billentyű lenyomásával.
1. Végül az utolsó kérdésnél hagyja jóvá a beállításokat az Enter billentyűvel.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Amikor a rendszer a **Subscription** (előfizetés) értékre kérdez rá, adja meg a helyes `Subscription` értéket a sor elején álló szám beírásával.
1. Ha a rendszer a **webalkalmazás** beállítását kéri, fogadja el az alapértelmezett beállítást az `<create>` ENTER billentyű lenyomásával vagy egy meglévő alkalmazás kiválasztásával.
1. Ha a rendszer az **operációs rendszer** beállítását kéri, az ENTER billentyű lenyomásával válassza a **Linux** lehetőséget.
1. Ha a rendszer a **díjszabási** csomaggal kéri, válassza a **B2** lehetőséget a beírásával `2` .
1. Használja az alapértelmezett Java-verziót ( **Java 8**) az ENTER billentyű lenyomásával.
1. A **Tomcat 8,5** alapértelmezett webes tárolót az ENTER billentyű lenyomásával használhatja.
1. Végül az utolsó kérdésnél hagyja jóvá a beállításokat az Enter billentyűvel.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end

Szükség esetén közvetlenül is módosíthatja App Service konfigurációit `pom.xml` . Néhány gyakori lista alább látható:

Tulajdonság | Kötelező | Leírás | Verzió
---|---|---|---
`<schemaVersion>` | hamis | Határozza meg a konfigurációs séma verzióját. A támogatott értékek a következők: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | hamis | Határozza meg az előfizetés AZONOSÍTÓját. | 0.1.0 +
`<resourceGroup>` | true | Azure <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyeket egységként lehet kezelni.">erőforráscsoport</abbr> a webalkalmazáshoz. | 0.1.0 +
`<appName>` | true | A webalkalmazás neve. | 0.1.0 +
`<region>` | true | Meghatározza azt a régiót, ahol a webalkalmazás üzemeltetve lesz; az alapértelmezett érték a **westeurope**. A [támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) szakaszban található összes érvényes régió. | 0.1.0 +
`<pricingTier>` | hamis | A webalkalmazás díjszabási szintje. Az alapértelmezett érték az éles számítási feladatokhoz **P1V2** , a **B2** pedig a javasolt minimum a Java dev/testhez. [További információ](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | A futásidejű környezet konfigurációja a részleteket [itt](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)tekintheti meg. | 0.1.0 +
`<deployment>` | true | A központi telepítés konfigurálásával [itt](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)láthatja a részleteket. | 0.1.0 +

Ügyeljen rá, hogy a `<appName>` és a `<resourceGroup>` ( `helloworld-1590394316693` és `helloworld-1590394316693-rg` ennek megfelelően a példában szereplő kimenet) értékeit később is használni fogjuk.

[Probléma bejelentése](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. az alkalmazás üzembe helyezése

A Java-alkalmazást az alábbi paranccsal helyezheti üzembe az Azure-ban.

```bash
mvn package azure-webapp:deploy
```

Az üzembe helyezés befejezése után az alkalmazás készen áll a következőre: `http://<appName>.azurewebsites.net/` . Nyissa meg az URL-címet a helyi webböngészővel. A következő megjelenítésnek kell megjelennie:

![Azure App Service futó minta alkalmazás](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulálunk!** Az első Java-alkalmazás üzembe helyezése App Service.

[Probléma bejelentése](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. erőforrások tisztítása

A Java-alkalmazás és a kapcsolódó erőforrások törlésével elkerülhető a további költségek felmerülése.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

A parancs futtatása egy percig is eltarthat.

<br>
<hr/>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kapcsolódás a PostgreSQL-hez készült Azure-ADATBÁZIShoz Java használatával](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [CI/CD beállítása](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Díjszabási információk](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Összesítő naplók és mérőszámok](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Vertikális felskálázás](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Azure Java-fejlesztőknek – erőforrások](/java/azure/)

> [!div class="nextstepaction"]
> [A Java-alkalmazás konfigurálása](configure-language-java.md)
