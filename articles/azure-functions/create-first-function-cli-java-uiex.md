---
title: Java-függvény létrehozása a parancssorból – Azure Functions
description: Ismerje meg, hogyan hozhat létre Java-függvényeket a parancssorból, majd hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050426"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Gyors útmutató: Java-függvény létrehozása az Azure-ban a parancssorból

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Parancssori eszközök használatával létrehozhat egy olyan Java-függvényt, amely válaszol a HTTP-kérelmekre. Tesztelje a kódot helyileg, majd telepítse a Azure Functions kiszolgáló nélküli környezetébe.

A rövid útmutató elvégzésével néhány USD értékű vagy annál kisebb költséggel jár a <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">Azure-fiók</abbr>.

Ha a Maven nem az Ön előnyben részesített fejlesztői eszköze, tekintse meg a Java-fejlesztőknek szóló hasonló oktatóanyagokat a [Gradle](./functions-create-first-java-gradle.md), a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) és a [Visual Studio Code](create-first-function-vs-code-java.md)használatával.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív Azure-fiók <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](functions-run-local.md#v2) 3. x verzió.

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója.

+ A [Java fejlesztői csomag](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója. A `JAVA_HOME` környezeti változót a JDK megfelelő verziójának telepítési helyére kell beállítani.

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

+ A terminál vagy a parancssorablakban futtassa a parancsot, `func --version` és győződjön meg arról, hogy a <abbr title="Parancssori eszközök készlete, amelyekkel a helyi számítógép Azure Functions dolgozhat.">Azure Functions Core Tools</abbr> 3. x verziójú.

+ Futtassa az parancsot az `az --version` Azure CLI 2,4-es vagy újabb verziójának megadásához.

+ A futtatásával `az login` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. helyi függvény létrehozása projekt

Azure Functions egy függvény projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik válaszol egy adott <abbr title="A függvény kódját meghívó esemény típusa, például egy HTTP-kérelem, egy üzenetsor-üzenet vagy egy adott időpont.">eseményindító</abbr>. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

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
    <summary><strong>Függvények futtatása Java 11 rendszeren</strong></summary>

    Használja a `-DjavaVersion=11` kapcsolót, ha a függvényeket Java 11-en szeretné futtatni. További információ: Java- [verziók](functions-reference-java.md#java-versions).
    </details>

1. A Maven megkéri, hogy a projektnek a telepítéskor való létrehozásának befejezéséhez szükséges értékeket is megkeresse.
    Ha a rendszer kéri, adja meg a következő értékeket:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | **csoportazonosító** | `com.fabrikam` | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java [csomag elnevezési szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. |
    | **artifactId** | `fabrikam-functions` | Egy érték, amely a jar neve, verziószám nélkül. |
    | **verziója** | `1.0-SNAPSHOT` | Válassza ki az alapértelmezett értéket. |
    | **csomag** | `com.fabrikam` | Egy érték, amely a generált függvény kódjához tartozó Java-csomag. Használja az alapértelmezettet. |

1. `Y`A megerősítéshez írja be vagy nyomja le az ENTER billentyűt.

    A Maven létrehoz egy új, _artifactId_ nevű mappában található projektfájlt, amely ebben a példában a `fabrikam-functions` . 

1. Navigáljon a projekt mappájába:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Mi jön létre a LocalFunctionProj mappában?</strong></summary>

Ez a mappa a projekthez különböző fájlokat tartalmaz, például a *function. Java*, a *FunctionTest. java* és a *pom.xml*. A (z) [local.settings.js](functions-run-local.md#local-settings-file) és [host.js](functions-host-json.md)nevű konfigurációs fájlok is elérhetők. Mivel a *local.settings.json* az Azure-ból letöltött titkos kódok is lehetnek, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.
</details>

<br/>
<details>
<summary><strong>A function. Java kódja</strong></summary>

*Function. Java* olyan `run` metódust tartalmaz, amely fogadja a kérelmeket a `request` változóban a [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) jegyzettel díszített [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) , amely meghatározza az trigger viselkedését. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

A válaszüzenetet a [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API hozza létre.

Az archetípus is létrehoz egy egység tesztet a függvényhez. Ha módosítja a függvényt kötések hozzáadására vagy új függvények hozzáadására a projekthez, a *FunctionTest. Java* fájlban is módosítania kell a teszteket.
</details>

<br/>
<details>
<summary><strong>pom.xmlkódja </strong></summary>

Az alkalmazás üzemeltetéséhez létrehozott Azure-erőforrások beállításai a beépülő modul **konfigurációs** elemében vannak meghatározva a  `com.microsoft.azure` generált *pom.xml* fájl GroupID. Az alábbi konfigurációs elem például arra utasítja a Maven-alapú telepítést, hogy hozzon létre egy Function alkalmazást az `java-functions-group` erőforráscsoporthoz a `westus` <abbr title="Földrajzi hivatkozás egy adott Azure-adatközpontra, amelyben az erőforrások le vannak foglalva.">régió</abbr>. A Function alkalmazás maga fut a csomagban lévő Windows rendszeren `java-functions-app-service-plan` , amely alapértelmezés szerint kiszolgáló nélküli fogyasztási csomag.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Ezen beállítások módosításával szabályozhatja, hogy az erőforrások hogyan jöjjenek létre az Azure-ban, például a verzióról a verzióra való váltással a `runtime.os` `windows` `linux` kezdeti üzembe helyezés előtt. A Maven beépülő modul által támogatott beállítások teljes listájáért tekintse meg a [konfiguráció részleteit](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. a függvény helyi futtatása

1. A **függvény futtatásához** indítsa el a helyi Azure functions Runtime-gazdagépet a *LocalFunctionProj* mappából:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    A kimenet vége felé a következő soroknak kell megjelenniük:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Ha a HttpExample nem jelenik meg a fentiekben látható módon, valószínűleg elindította a gazdagépet a projekt gyökérkönyvtárán kívülről. Ebben az esetben a <kbd>CTRL + C</kbd> billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappa mappájához, és futtassa újra az előző parancsot.

1. **Másolja a függvény URL-címét** `HttpExample` ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot `?name=<YOUR_NAME>` , így a teljes URL-címet, például: `http://localhost:7071/api/HttpExample?name=Functions` . A böngészőben a következőhöz hasonló üzenetnek kell megjelennie `Hello Functions` :

    ![A függvény helyi futtatásának eredménye a böngészőben](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    A terminál, amelyben elindította a projektet, a naplók kimenetét is megjeleníti a kérések elkészítésekor.

1. Ha elkészült, használja a <kbd>CTRL + C billentyűkombinációt</kbd> , és a functions gazdagép leállításához válassza az <kbd>y</kbd> lehetőséget.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. a Function projekt üzembe helyezése az Azure-ban

A functions-alkalmazás és a kapcsolódó erőforrások az Azure-ban jönnek létre, amikor először telepíti a functions-projektet. Az alkalmazás üzemeltetéséhez létrehozott Azure-erőforrások beállításai a *pom.xml* fájlban vannak meghatározva. Ebben a cikkben fogadja el az alapértelmezett értékeket.

<br/>
<details>
<summary><strong>Linuxon futó Function-alkalmazás létrehozása</strong></summary>

Ha a Windows helyett Linux rendszeren futó Function alkalmazást szeretne létrehozni, módosítsa a `runtime.os` *pom.xml* fájl elemét a verzióról a következőre: `windows` `linux` . [Ezekben a régiókban](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)támogatott a Linux futtatása a használati tervekben. Nem rendelkezhet olyan alkalmazásokkal, amelyek Linux rendszeren futnak, és ugyanazon az erőforráscsoporthoz futtatják a Windowson futó alkalmazásokat.
</details>

1. Az üzembe helyezés előtt jelentkezzen be az Azure-előfizetésbe az Azure CLI vagy a Azure PowerShell használatával. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Az az [login](/cli/azure/reference-index#az-login) parancs aláírja az Azure-fiókját.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    A [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkezik az Azure-fiókjába.

    ---

1. A következő parancs használatával telepítheti a projektet egy új Function alkalmazásba.

    ```console
    mvn azure-functions:deploy
    ```

    A központi telepítés a Project fájljait csomagolja és telepíti az új Function alkalmazásba a [zip-telepítés](functions-deployment-technologies.md#zip-deploy)használatával. A kód a központi telepítési csomagból fut az Azure-ban.

<br/>
<details>
<summary><strong>Mi jön létre az Azure-ban?</strong></summary>

+ Erőforráscsoport. Neve: _Java-functions-Group_.
+ Storage-fiók. A függvények igénylik. A név véletlenszerűen jön létre a Storage-fióknév követelményei alapján.
+ Üzemeltetési csomag. A _westus_ régióban lévő Function App kiszolgáló nélküli üzemeltetése. A név a _Java-functions-app-Service-Plan_.
+ Function alkalmazás. A functions alkalmazás a függvények üzembe helyezési és végrehajtási egysége. A név véletlenszerűen jön létre a _artifactId_ alapján, amely véletlenszerűen generált számmal van hozzáfűzve.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. a függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, **meghívja azt úgy, hogy HTTP-kérést küld az URL-címére** a böngészőben, vagy egy hasonló eszközzel <abbr title="Egy parancssori eszköz, amely HTTP-kéréseket hoz létre egy URL-címre; Lásd https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a parancs kimenetében látható teljes **Meghívási URL-** címet `publish` egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions` . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Futtassa a parancsot [`curl`](https://curl.haxx.se/) a **Meghívási URL-címmel**, és illessze be a paramétert `&name=Functions` . A parancs kimenetének a "Hello functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. erőforrások törlése

Ha folytatja a [következő lépéssel](#next-steps) , és hozzáadja az Azure Storage-t <abbr title="Az Azure Storage-ban egy függvényt társíthat egy tárolási sorhoz, hogy üzeneteket lehessen létrehozni a várólistán.">üzenetsor kimeneti kötése</abbr>megtarthatja az összes erőforrását, ahogy a már elvégzett szolgáltatásra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

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
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
