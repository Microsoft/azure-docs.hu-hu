---
title: Java-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre Java-függvényt a parancssorból, majd hogyan tehet közzé egy helyi projektet a kiszolgáló nélküli üzemeltetéshez a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 910e19137873ed5c34584b3293ebd4248c5fe2f8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831905"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Rövid útmutató: Java-függvény létrehozása az Azure-ban a parancssorból

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Parancssori eszközökkel hozzon létre egy Java-függvényt, amely HTTP-kérésekre válaszol. Tesztelje helyileg a kódot, majd telepítse a kiszolgáló nélküli Azure Functions.

A rövid útmutató elvégzése néhány USD vagy annál kisebb költséggel jár a <abbr title="Az Azure-használat számlázási adatait fenntartó profil.">Azure-fiók</abbr>.

Ha nem a Maven az Ön által előnyben részesített fejlesztői eszköz, tekintse meg a [Gradle-](./functions-create-first-java-gradle.md), [IntelliJ IDEA-](/azure/developer/java/toolkit-for-intellij/quickstart-functions) és Visual Studio Code-et használó [Java-fejlesztőknek készült hasonló oktatóanyagokat.](create-first-function-vs-code-java.md)

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

Mielőtt hozzákezd, a következőkre lesz majd kíváncsi:

+ Aktív Azure-fiók <abbr title="Az alapvető szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, általában egy szervezeten belüli egyéni vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#v2) 3.x verzió.

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

+ A [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója. A környezeti változót a JDK megfelelő verziójának telepítési `JAVA_HOME` helyére kell beállítani.

+ [Apache Maven,](https://maven.apache.org)3.0-s vagy újabb verzió.

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

+ Egy terminálban vagy parancsablakban futtassa a parancsot `func --version` annak ellenőrzéshez, hogy a <abbr title="Parancssori eszközök a helyi számítógépen Azure Functions való munkavégzéshez.">Azure Functions Core Tools</abbr> A a 3.x verziójú.

+ A `az --version` futtatásával ellenőrizze, hogy az Azure CLI 2.4-es vagy újabb verziója van-e.

+ Az `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Helyi függvényprojekt létrehozása

A Azure Functions a függvényprojekt egy tároló egy vagy több különálló függvény számára, amelyek egy-egy adott függvényre válaszolnak <abbr title="A függvény kódját meghívó esemény típusa, például EGY HTTP-kérés, egy üzenetsor-üzenet vagy egy adott időpont.">Ravaszt</abbr>. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

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

    <br/>
    <details>
    <summary><strong>Függvények futtatása Java 11-ben</strong></summary>

    Használja a `-DjavaVersion=11` kapcsolót, ha a függvényeket Java 11-en szeretné futtatni. További információ: [Java-verziók.](functions-reference-java.md#java-versions)
    </details>

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

<br/>
<details>
<summary><strong>Mi jött létre a LocalFunctionProj mappában?</strong></summary>

Ez a mappa a projekt különböző fájljait tartalmazza, például *Function.java,* *FunctionTest.java* és *pom.xml.* A fájlnevű konfigurációs fájlok is [local.settings.jsa](functions-run-local.md#local-settings-file) és a [host.jstalálhatóak.](functions-host-json.md) Mivel *local.settings.js* fájl tartalmazhatja az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*
</details>

<br/>
<details>
<summary><strong>A Function.java kódja</strong></summary>

*A Function.java* tartalmaz egy metódust, amely a változóban fogadja a kérésadatokat egy `run` `request` [HttpRequestMessage,](/java/api/com.microsoft.azure.functions.httprequestmessage) amely az eseményindító viselkedését meghatározó [HttpTrigger-jegyzetet](/java/api/com.microsoft.azure.functions.annotation.httptrigger) tartalmazza. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A válaszüzenetet a [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API generálja.

Az archetípus egy egységtesztet is létrehoz a függvényhez. Ha úgy módosítja a függvényt, hogy kötéseket adjon hozzá, vagy új függvényeket adjon a projekthez, módosítania kell a *FunctionTest.java* fájlban található teszteket is.
</details>

<br/>
<details>
<summary><strong>Kód pom.xml</strong></summary>

Az alkalmazás gazdagépeként létrehozott Azure-erőforrások  beállításait **a** beépülő modul konfigurációs eleme határozza meg a generált fájlban található `com.microsoft.azure`pom.xml *csoportazonosítóval.* Az alábbi konfigurációs elem például arra utasít egy Maven-alapú üzemelő példányt, hogy hozzon létre egy függvényalkalmazást az `java-functions-group` erőforráscsoportban a `westus` <abbr title="Földrajzi hivatkozás arra az Azure-adatközpontra, amelyben az erőforrások le vannak osztva.">régió</abbr>. Maga a függvényalkalmazás a csomagban üzemeltetett Windows rendszeren fut, amely alapértelmezés szerint egy kiszolgáló nélküli `java-functions-app-service-plan` használatra szánt csomag.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Ezeket a beállításokat módosíthatja, hogy szabályozni tudja az erőforrások Azure-beli létrehozási mikéntját, például a kezdeti üzembe helyezés előtt a következőre `runtime.os` `windows` `linux` váltva: . A Maven beépülő modul által támogatott beállítások teljes listájáért tekintse meg a [konfiguráció részleteit.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. A függvény helyi futtatása

1. **Futtassa a függvényt** a helyi Azure Functions a *LocalFunctionProj* mappából:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    A kimenet vége felé a következő soroknak kell megjelennie:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Ha a HttpExample nem a fent látható módon jelenik meg, valószínűleg a projekt gyökérmappán kívülről indította el a gazdagépet. Ebben az esetben a <kbd>Ctrl+C</kbd> billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappához, és futtassa újra az előző parancsot.

1. **Másolja a függvény URL-címét** ebből a kimenetből egy böngészőbe, és fűzza hozzá a lekérdezési sztringet, így a teljes `HttpExample` `?name=<YOUR_NAME>` URL-cím, például `http://localhost:7071/api/HttpExample?name=Functions` : . A böngészőnek a következőhöz hasonló üzenetet kell `Hello Functions` megjelenítenie:

    ![A függvény eredménye helyileg fut a böngészőben](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    A terminál, amelyen elindította a projektet, a naplókimenetet is megjeleníti a kérések igénylése során.

1. Ha végzett, a <kbd>Ctrl+C</kbd> billentyűkombinációval állítsa le a függvénygazdát <kbd>az y</kbd> billentyűkombinációval.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. A függvényprojekt üzembe helyezése az Azure-ban

A függvényalkalmazások és a kapcsolódó erőforrások a függvényprojekt első üzembe helyezésekor vannak létrehozva az Azure-ban. Az alkalmazás gazdagépeként létrehozott Azure-erőforrások beállításait apom.xml *határozza* meg. Ebben a cikkben az alapértelmezett beállításokat fogja elfogadni.

<br/>
<details>
<summary><strong>Linuxon futó függvényalkalmazás létrehozása</strong></summary>

Ha Windows helyett Linuxon futó függvényalkalmazást hoz létre, módosítsa a fájl elemét `runtime.os` *pom.xml* helyett `windows` a következőre: `linux` . A Linux használat alapú csomagon való futtatása ezekben a [régiókban támogatott.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Nem lehet linuxos és Windowson futó alkalmazás ugyanabban az erőforráscsoportban.
</details>

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

    A [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkeztet az Azure-fiókjába.

    ---

1. A következő paranccsal helyezheti üzembe a projektet egy új függvényalkalmazásban.

    ```console
    mvn azure-functions:deploy
    ```

    Az üzembe helyezés csomagba tömöríti a projektfájlokat, és üzembe helyezheti őket az új függvényalkalmazásban [a zip-telepítés használatával.](functions-deployment-technologies.md#zip-deploy) A kód az Azure-beli üzembe helyezési csomagból fut.

<br/>
<details>
<summary><strong>Mi jött létre az Azure-ban?</strong></summary>

+ Erőforráscsoport. Neve _java-functions-group._
+ Tárfiók. A Functions által megkövetelt. A nevet a rendszer véletlenszerűen generálja a Storage-fióknévre vonatkozó követelmények alapján.
+ Üzemeltetési csomag. Kiszolgáló nélküli üzemeltetés a függvényalkalmazás számára a _westus_ régióban. A név _java-functions-app-service-plan._
+ Függvényalkalmazás. A függvényalkalmazás a függvények üzembe helyezési és végrehajtási egysége. A rendszer véletlenszerűen generálja a nevet az _artifactId_ alapján, és egy véletlenszerűen generált számmal egészül ki.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, a meghíváshoz egy HTTP-kérést kell a **böngészőben** vagy egy hasonló eszközzel meg kell hívnia az URL-címére <abbr title="Parancssori eszköz URL-címre vonatkozó HTTP-kérések létrehozásához; lásd: https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a parancs kimenetében látható teljes **meghívási URL-címet** egy böngésző `publish` címsorába a lekérdezési paraméter hozzáfűzése `&name=Functions` mellett. A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor helyileg futtatta a függvényt.

![A függvény kimenete egy böngészőben fut az Azure-ban](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Futtassa [`curl`](https://curl.haxx.se/) a metódust **a meghívási URL-címével,** és fűzi hozzá a paramétert. `&name=Functions` A parancs kimenetének a "Hello Functions" szövegnek kell lennie.

![A függvény kimenete a curl használatával fut az Azure-ban](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Erőforrások tisztítása

Ha folytatja a következő [lépéssel,](#next-steps) és hozzáad egy Azure Storage-tárolót <abbr title="Az Azure Storage-ban azt jelenti, hogy egy függvényt társít egy tárolási üzenetsorhoz, hogy üzeneteket hoz létre az üzenetsorban.">üzenetsor kimeneti kötése</abbr>, tartsa a helyén az összes erőforrást, mivel a már végzett munka alapján fog felépíteni.

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

<br>
<hr/>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
