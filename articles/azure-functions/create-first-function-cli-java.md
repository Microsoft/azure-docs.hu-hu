---
title: Java-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre Java-függvényt a parancssorból, majd hogyan tehet közzé egy helyi projektet a kiszolgáló nélküli üzemeltetéshez a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 1a1571e83e33ee0aec40df4cf68b3d01b6ef5f04
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787502"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Rövid útmutató: Java-függvény létrehozása az Azure-ban a parancssorból

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközökkel hoz létre egy Java-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe kell helyeznie a Azure Functions.

A rövid útmutató elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókban.

> [!NOTE]
> Ha nem a Maven az Ön által előnyben részesített fejlesztői eszköz, tekintse meg a [Gradle-](./functions-create-first-java-gradle.md), [IntelliJ IDEA-](/azure/developer/java/toolkit-for-intellij/quickstart-functions) és Visual Studio Code-et használó [Java-fejlesztőknek készült hasonló oktatóanyagokat.](create-first-function-vs-code-java.md)

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd kíváncsi:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#v2) 3.x. verzió.

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

+ A [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója. A környezeti változót a JDK megfelelő verziójának telepítési `JAVA_HOME` helyére kell beállítani.     

+ [Apache Maven,](https://maven.apache.org)3.0-s vagy újabb verzió.

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójú-e.

+ Az `az --version` futtatásával ellenőrizze, hogy az Azure CLI 2.4-es vagy újabb verziója van-e.

+ Az `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

A Azure Functions a függvényprojekt egy tároló egy vagy több különálló függvény számára, amelyek egy adott eseményindítóra válaszolnak. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + Használja a `-DjavaVersion=11` kapcsolót, ha a függvényeket Java 11-en szeretné futtatni. További információ: [Java-verziók.](functions-reference-java.md#java-versions) 
    > + A cikk befejezéséhez a környezeti változót a JDK megfelelő verziójának telepítési `JAVA_HOME` helyére kell állítani.

1. A Maven az üzembe helyezés során a projekt létrehozásához szükséges értékeket kéri.   
    Amikor a rendszer kéri, adja meg a következő értékeket:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | **csoportazonosító** | `com.fabrikam` | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java csomagelnevezési [szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. |
    | **artifactId (összetevő-azonosító)** | `fabrikam-functions` | Egy érték, amely a jar neve verziószám nélkül. |
    | **Változat** | `1.0-SNAPSHOT` | Válassza ki az alapértelmezett értéket. |
    | **Csomag** | `com.fabrikam` | Egy érték, amely a generált függvénykód Java-csomagja. Használja az alapértelmezettet. |

1. A `Y` megerősítéshez írja be vagy nyomja le az Enter billentyűt.

    A Maven létrehozza a projektfájlokat egy új mappában _artifactId_ néven, amely ebben a példában `fabrikam-functions` a . 

1. Lépjen a projektmappába:

    ```console
    cd fabrikam-functions
    ```

    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a fájlnevű konfigurációslocal.settings.js[és](functions-run-local.md#local-settings-file)host.js[a fájlban.](functions-host-json.md) Mivel *local.settings.jsa fájl* tartalmazhat az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*

### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szeretné, ugorjon a Függvény helyi [futtatása](#run-the-function-locally) részhez, és később vizsgálja meg a fájl tartalmát.

#### <a name="functionjava"></a>Function.java
*A Function.java* tartalmaz egy metódust, amely a változóban fogadja a kérésadatokat egy `run` `request` [HttpRequestMessage,](/java/api/com.microsoft.azure.functions.httprequestmessage) amely az eseményindító viselkedését meghatározó [HttpTrigger-jegyzetet](/java/api/com.microsoft.azure.functions.annotation.httptrigger) tartalmazza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A válaszüzenetet a [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API generálja.

#### <a name="pomxml"></a>pom.xml

Az alkalmazás gazdagépeként létrehozott Azure-erőforrások  beállításai **a** beépülő modul konfigurációs elemében vannak meghatározva, a generált fájlban pedig a `com.microsoft.azure` pom.xml meg. Az alábbi konfigurációs elem például arra utasít egy Maven-alapú üzemelő példányt, hogy hozzon létre egy függvényalkalmazást a régióban `java-functions-group` található `westus` erőforráscsoportban. Maga a függvényalkalmazás a csomagban üzemeltetett Windows rendszeren fut, amely alapértelmezés szerint egy kiszolgáló nélküli `java-functions-app-service-plan` használatra szánt csomag.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Ezeket a beállításokat módosíthatja, hogy szabályozni tudja az erőforrások Azure-beli létrehozási mikéntját, például a kezdeti üzembe helyezés előtt a következőre `runtime.os` `windows` `linux` váltva: . A Maven beépülő modul által támogatott beállítások teljes listájáért tekintse meg a [konfiguráció részleteit.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)

#### <a name="functiontestjava"></a>FunctionTest.java

Az archetípus egy egységtesztet is létrehoz a függvényhez. Ha úgy módosítja a függvényt, hogy kötéseket adjon hozzá, vagy új függvényeket adjon a projekthez, módosítania kell a *FunctionTest.java* fájlban található teszteket is.

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. Futtassa a függvényt a local Azure Functions a *LocalFunctionProj* mappából:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    A kimenet vége felé a következő soroknak kell megjelennie:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Ha a HttpExample nem a fent látható módon jelenik meg, valószínűleg a projekt gyökérmappán kívülről indította el a gazdagépet. Ebben az esetben a **Ctrl** C billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappához, és futtassa újra az +  előző parancsot.

1. Másolja a függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzza hozzá a lekérdezési sztringet, így a `HttpExample` `?name=<YOUR_NAME>` teljes URL-cím a következőhöz hasonló `http://localhost:7071/api/HttpExample?name=Functions` lesz: . A böngészőnek egy üzenetet kell megjelenítenie, amely megismétli a lekérdezési sztring értékét. A terminál, amelyen elindította a projektet, a naplókimenetet is megjeleníti a kérések igénylése során.

1. Ha végzett, a Ctrl C **billentyűkombinációval** állítsa +  le `y` a függvénygazdát.

## <a name="deploy-the-function-project-to-azure"></a>A függvényprojekt üzembe helyezése az Azure-ban

A függvényalkalmazások és a kapcsolódó erőforrások a függvényprojekt első üzembe helyezésekor vannak létrehozva az Azure-ban. Az alkalmazáshoz létrehozott Azure-erőforrások beállításait a következő [ fájlbanpom.xml meg:](#pomxml). Ebben a cikkben az alapértelmezett beállításokat fogja elfogadni.

> [!TIP]
> Ha Windows helyett Linuxon futó függvényalkalmazást hoz létre, módosítsa a fájl pom.xml `runtime.os` elemét a `windows` következőre: `linux` . A Linux használat alapú csomagon való futtatása ezekben a [régiókban támogatott.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Nem lehet linuxos és Windowson futó alkalmazás ugyanabban az erőforráscsoportban.

1. Az üzembe helyezés előtt jelentkezzen be az Azure-előfizetésbe az Azure CLI vagy a Azure PowerShell. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Az [az login](/cli/azure/reference-index#az_login) parancs bejelentkeztet az Azure-fiókjába.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    A [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkezik az Azure-fiókjába.

    ---

1. A következő paranccsal üzembe helyezheti a projektet egy új függvényalkalmazásban.

    ```console
    mvn azure-functions:deploy
    ```
    
    Ez a következő erőforrásokat hozza létre az Azure-ban:
    
    + Erőforráscsoport. Neve _java-functions-group._
    + Tárfiók. A Functions által megkövetelt. A nevet a rendszer véletlenszerűen generálja a Storage-fiók nevére vonatkozó követelmények alapján.
    + Üzemeltetési csomag. Kiszolgáló nélküli üzemeltetés a függvényalkalmazás számára a _westus_ régióban. A név _java-functions-app-service-plan._
    + Függvényalkalmazás. A függvényalkalmazás a függvények üzembe helyezési és végrehajtási egysége. A rendszer véletlenszerűen generálja a nevet az _artifactId_ alapján, és egy véletlenszerűen generált számmal egészül ki.
    
    Az üzembe helyezés csomagba tömöríti a projektfájlokat, és üzembe helyezheti őket az új függvényalkalmazásban [a zip-telepítés használatával.](functions-deployment-technologies.md#zip-deploy) A kód az Azure-beli üzembe helyezési csomagból fut.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatja [](#next-steps) a következő lépéssel, és hozzáad egy Azure Storage-üzenetsor kimeneti kötését, tartsa a helyén az összes erőforrást, miközben a már végzett munka alapján fog felépíteni.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását, hogy elkerülje a további költségeket.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
