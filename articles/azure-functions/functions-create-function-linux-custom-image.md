---
title: Linux Azure Functions létrehozása egyéni rendszerkép használatával
description: Megismerheti, hogyan hozhat létre egyéni Linux-rendszerképeken futó Azure Functions-függvényeket.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 7950bfb4a57db812da87f4e5f76f3075d50a8293
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782271"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Függvény létrehozása Linux rendszerben egyéni tárolóval

Ebben az oktatóanyagban egyéni Docker-tárolóként hozza létre és helyezheti üzembe a kódot Azure Functions linuxos alapként szolgáló rendszerkép használatával. Egyéni rendszerképet általában akkor használ, ha a függvények egy adott nyelvi verziót igényelnek, vagy olyan függőséggel vagy konfigurációval rendelkezik, amelyet nem a beépített rendszerkép biztosít.

::: zone pivot="programming-language-other"
Azure Functions nyelvet és futásidőt támogat egyéni [kezelők használatával.](functions-custom-handlers.md) Egyes nyelvek, például az oktatóanyagban használt R programozási nyelv esetében egyéni tároló használatát igénylő függőségként kell telepítenie a futásidejű vagy további kódtárakat.
::: zone-end

A függvénykód egyéni Linux-tárolóban való üzembe helyezéséhez [Prémium](functions-premium-plan.md) vagy Dedikált [(App Service) csomag üzemeltetése](dedicated-plan.md) szükséges. Az oktatóanyag elvégzése néhány amerikai dollárral jár az [Azure-fiókjában,](#clean-up-resources) ami az erőforrások feleslegessé tevésével csökkenthető, amikor végzett.

Használhat egy alapértelmezett Azure App Service is, amely a Linux rendszeren üzemeltetett első [függvényének létrehozása oldalon található.](./create-first-function-cli-csharp.md?pivots=programming-language-python) A támogatott alapként Azure Functions rendszerképek a Azure Functions [rendszerkép-Azure Functions találhatók.](https://hub.docker.com/_/microsoft-azure-functions-base)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Hozzon létre egy függvényalkalmazást és egy Docker-Azure Functions Core Tools.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Támogató erőforrások létrehozása az Azure-ban a függvényalkalmazáshoz
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.
> * Folyamatos üzembe helyezés engedélyezése.
> * Engedélyezze az SSH-kapcsolatokat a tárolóhoz.
> * Adjon hozzá egy kimeneti Queue Storage-kötést. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Hozzon létre egy függvényalkalmazást és egy Docker-Azure Functions Core Tools.
> * Egyéni rendszerkép készítése a Docker használatával.
> * Egyéni rendszerkép közzététele egy tárolójegyzékben.
> * Támogató erőforrások létrehozása az Azure-ban a függvényalkalmazáshoz
> * Függvényalkalmazás üzembe helyezése a Docker Hubból.
> * Alkalmazásbeállítások hozzáadása a függvényalkalmazáshoz.
> * Folyamatos üzembe helyezés engedélyezése.
> * Engedélyezze az SSH-kapcsolatokat a tárolóhoz.
::: zone-end

Ezt az oktatóanyagot bármely, Windows, macOS vagy Linux rendszert futtató számítógépen követheti. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Egy [Docker-azonosító](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ A `docker login` Dockerbe való bejelentkezéshez futtassa a következőt: . Ez a parancs meghiúsul, ha a Docker nem fut, ebben az esetben indítsa el a Docker-t, és próbálja újra a parancsot.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>A helyi függvényprojekt létrehozása és tesztelése

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Egy terminálban vagy parancssorban futtassa az alábbi parancsot a választott nyelvhez egy nevű mappában található függvényalkalmazás-projekt `LocalFunctionsProject` létrehozásához.  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Egy üres mappában futtassa a következő parancsot a Functions-projekt [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)-ból való létrehozásához.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

A `-DjavaVersion` paraméter jelzi a Functions-runtime-nak, hogy a Java melyik verzióját használja. Használja a `-DjavaVersion=11` kapcsolót, ha a függvényeket Java 11-en szeretné futtatni. Ha nem adja meg a értéket, a Maven alapértelmezés szerint `-DjavaVersion` a Java 8-at használja. További információ: [Java-verziók.](functions-reference-java.md#java-versions)

> [!IMPORTANT]
> A cikk befejezéséhez a környezeti változót a JDK megfelelő verziójának telepítési `JAVA_HOME` helyére kell állítani.

A Maven az üzembe helyezés során a projekt létrehozásához szükséges értékeket kéri.   
Amikor a rendszer kéri, adja meg a következő értékeket:

| Adatkérés | Érték | Leírás |
| ------ | ----- | ----------- |
| **csoportazonosító** | `com.fabrikam` | Egy érték, amely egyedileg azonosítja a projektet az összes projektben, a Java csomagelnevezési [szabályait](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) követve. |
| **artifactId (összetevő-azonosító)** | `fabrikam-functions` | Egy érték, amely a jar neve verziószám nélkül. |
| **Változat** | `1.0-SNAPSHOT` | Válassza ki az alapértelmezett értéket. |
| **Csomag** | `com.fabrikam.functions` | Egy érték, amely a generált függvénykód Java-csomagja. Használja az alapértelmezettet. |

A `Y` megerősítéshez írja be vagy nyomja le az Enter billentyűt.

A Maven létrehozza a projektfájlokat egy új mappában _artifactId_ néven, amely ebben a példában `fabrikam-functions` a . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

A beállítás létrehoz egy et a projekthez, amely definiál egy megfelelő egyéni tárolót a Azure Functions és a `--docker` `Dockerfile` kiválasztott futásidővel való használathoz.

Lépjen a projektmappába:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény egyedi neve, az argumentum pedig a függvény `--name` `--template` eseményindítóját határozza meg. `func new` létrehoz egy almappát, amely megegyezik a függvény nevével, amely tartalmazza a projekt választott nyelvének megfelelő kódfájlt, valamint egy nevű konfigurációs *function.jsa következőn:*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény egyedi neve, az argumentum pedig a függvény `--name` `--template` eseményindítóját határozza meg. `func new` létrehoz egy almappát, amely megegyezik a függvény nevével, amely tartalmaz egy nevű *konfigurációsfunction.jsa következőn:*.

```console
func new --name HttpExample --template "HTTP trigger"
```

Egy szövegszerkesztőben hozzon létre egy fájlt a kezelő nevű *projektmappában. R*. Adja hozzá a következőt tartalomként.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

A *host.jsmódosítsa* a szakaszt úgy, hogy konfigurálja az egyéni kezelő `customHandler` indítási parancsát.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

A függvény helyi teszteléséhez indítsa el a helyi Azure Functions a projektmappában a gyökérkönyvtárban található futásidejű gazdagépen: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Ha látja, hogy a végpont megjelenik a `HttpExample` kimenetben, lépjen a következőre: `http://localhost:7071/api/HttpExample?name=Functions` . A böngészőnek egy "hello" üzenetet kell megjelenítenie, amely a értéket jeleníti meg a lekérdezési `Functions` `name` paraméterhez megadott értékkel.

Állítsa **le** a - **gazdagépet** a Ctrl C billentyűkombinációval.

## <a name="build-the-container-image-and-test-locally"></a>A tároló rendszerképének összeállítása és helyi tesztelése

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Nem kötelező) Vizsgálja meg *a dockerfile-t* a projektmappa gyökerében. A Dockerfile ismerteti a függvényalkalmazás Linuxon való futtatásához szükséges környezetet.  A támogatott alapként használható rendszerképek teljes Azure Functions az alapként Azure Functions [oldalon található.](https://hub.docker.com/_/microsoft-azure-functions-base)
::: zone-end

::: zone pivot="programming-language-other"
Vizsgálja meg *a dockerfile-t* a projektmappa gyökerében. A Dockerfile ismerteti a függvényalkalmazás Linuxon való futtatásához szükséges környezetet. Az egyéni kezelőalkalmazások a `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` rendszerképet használják alapként.

Módosítsa a *Dockerfile-t* az R telepítéséhez. Cserélje le a *Dockerfile* tartalmát a következőre.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

A projekt gyökérmappában futtassa a [docker build](https://docs.docker.com/engine/reference/commandline/build/) parancsot, és adjon meg egy nevet () `azurefunctionsimage` és egy címkét `v1.0.0` (). A `<DOCKER_ID>` helyére a Docker Hub-fiók azonosítóját írja. Ez a parancs létrehozza a tároló Docker-rendszerképét.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Amikor a parancs befejeződik, helyileg futtathatja az új tárolót.
    
A build tesztelése érdekében futtassa a rendszerképet egy helyi tárolóban a [docker run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal, és cserélje le újra a et a Docker-azonosítóra, és adja hozzá a `<DOCKER_ID` port `-p 8080:80` argumentumot:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Ha a rendszerkép egy helyi tárolóban fut, nyisson meg egy böngészőt a helyre, ahol meg kell jelenni az alább látható `http://localhost:8080` helyőrző képnek. A rendszerkép ezen a ponton jelenik meg, mert a függvény a helyi tárolóban fut, ahogyan az Azure-ban tenné, ami azt jelenti, hogy egy hozzáférési kulcs védi afunction.jsa  tulajdonsággal. `"authLevel": "function"` A tároló még nem lett közzétéve egy függvényalkalmazásban az Azure-ban, ezért a kulcs még nem érhető el. Ha a helyi tárolón szeretne tesztet tesztelni, állítsa le a Docker-t, módosítsa az engedélyezési tulajdonságot a következőre: , építse újra a rendszerképet, és indítsa újra a `"authLevel": "anonymous"` Docker-t. Ezután állítsa vissza `"authLevel": "function"` a *function.jsa következőn:*. További információkért lásd az engedélyezési [kulcsokat.](functions-bindings-http-webhook-trigger.md#authorization-keys)

![Helyőrző kép, amely azt jelzi, hogy a tároló helyileg fut](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Ha a rendszerkép egy helyi tárolóban fut, lépjen a webhelyre, ahol a korábban is látható "hello" üzenetnek `http://localhost:8080/api/HttpExample?name=Functions` kell lennie. Mivel a Maven-archetípus egy HTTP által aktivált függvényt hoz létre, amely névtelen hitelesítést használ, a függvényt akkor is meg lehet hívni, ha az a tárolóban fut. 
::: zone-end  

Miután ellenőrizte a függvényalkalmazást a tárolóban, állítsa le a docker alkalmazást **a Ctrl** C + **billentyűkombinációval.**

## <a name="push-the-image-to-docker-hub"></a>A rendszerkép leküldése a Docker Hub

Docker Hub egy tároló-beállításjegyzék, amely rendszerképeket tartalmaz, valamint rendszerkép- és tárolószolgáltatásokat biztosít. Ahhoz, hogy megosztja a rendszerképet, beleértve az Azure-beli üzembe helyezést is, le kell azt helyeznie egy regisztrációs adatbázisba.

1. Ha még nem jelentkezett be a Dockerbe, tegye meg a [docker login](https://docs.docker.com/engine/reference/commandline/login/) paranccsal, és cserélje le a adatokat a `<docker_id>` Docker-azonosítójára. Ez a parancs a felhasználónevét és jelszavát kéri. A "Bejelentkezés sikeres" üzenet megerősíti, hogy bejelentkezett.

    ```console
    docker login
    ```
    
1. Miután bejelentkezett, a docker push paranccsal Docker Hub le a rendszerképet a [docker push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal, és cserélje le a et a `<docker_id>` Docker-azonosítójára.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. A hálózati sebességtől függően a rendszerkép első lekért lekért része néhány percet is igénybe vehet (a későbbi módosítások lekért ideje sokkal gyorsabb). Amíg várakozik, továbbléphet a következő szakaszra, és létrehozhat Azure-erőforrásokat egy másik terminálban.

## <a name="create-supporting-azure-resources-for-your-function"></a>Támogató Azure-erőforrások létrehozása a függvényhez

A függvénykód Azure-ban való üzembe helyezéséhez három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Azure Storage-fiók, amely fenntartja a projektek állapotát és egyéb adatait.
- Egy függvényalkalmazást, amely biztosítja a függvénykód futtatásának környezetét. A függvényalkalmazás a helyi függvényprojekthez van leképezve, és lehetővé teszi a függvények logikai egységként való csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében.

Ezeket az elemeket Azure CLI-parancsokkal hozhatja létre. Minden parancs JSON-kimenetet biztosít a befejezéskor.

1. Jelentkezzen be az Azure-ba [az az login paranccsal:](/cli/azure/reference-index#az_login)

    ```azurecli
    az login
    ```
    
1. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa egy nevű erőforráscsoportot hoz létre a `AzureFunctionsContainers-rg` `westeurope` régióban. (Az erőforráscsoportot és az erőforrásokat általában egy, a közelben található régióban hozza létre, a parancsban elérhető régió `az account list-locations` használatával.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux- és Windows-alkalmazások nem használhatók ugyanabban az erőforráscsoportban. Ha rendelkezik egy nevű meglévő erőforráscsoporttal egy Windows-függvényalkalmazással vagy `AzureFunctionsContainers-rg` -webalkalmazással, másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú tárfiókot az erőforráscsoportban és régióban [az az storage account create paranccsal.](/cli/azure/storage/account#az_storage_account_create) A következő példában cserélje le a helyére az Önnek `<storage_name>` megfelelő globálisan egyedi nevet. A neveknek három–24 karakter hosszúságú számokat és csak kisbetűket tartalmazhatnak. `Standard_LRS` A egy tipikus általános célú fiókot ad meg.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Ebben az oktatóanyagban a tárfiók csak néhány USD centet jelent.
    
1. Az paranccsal hozzon létre egy Prémium szintű tervet Azure Functions Elastic Premium 1 tarifacsomagban ( ), a nyugat-európai régióban ( , vagy használjon egy megfelelő régiót a közelében) és `myPremiumPlan`  `--sku EP1` egy Linux-tárolóban `-location westeurope` ( `--is-linux` ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Itt a Prémium szintű csomag van használatban, amely szükség szerint skálázhat. További információk az üzemeltetésről: [Azure Functions szolgáltatási csomagok összehasonlítása](functions-scale.md). A költségek kiszámításához tekintse meg a [Functions díjszabási oldalát.](https://azure.microsoft.com/pricing/details/functions/)

    A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Függvényalkalmazás létrehozása és konfigurálása az Azure-ban a rendszerkép használatával

Az Azure-ban egy függvényalkalmazás kezeli a függvények végrehajtását az üzemeltetési csomagban. Ebben a szakaszban az előző szakaszban található Azure-erőforrások használatával fog függvényalkalmazást létrehozni egy Docker Hub egy azure storage-hoz való kapcsolati sztring használatával.

1. Hozza létre a Függvényalkalmazást [az az functionapp create paranccsal.](/cli/azure/functionapp#az_functionapp_create) A következő példában cserélje le a helyére a tárfiók előző szakaszában `<storage_name>` használt nevet. Cserélje le a helyére az Önnek megfelelő globálisan egyedi nevet, a helyére pedig a `<app_name>` `<docker_id>` Docker-azonosítóját.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    A *deployment-container-image-name* paraméter határozza meg a függvényalkalmazáshoz használni szükséges rendszerképet. Az az [functionapp config container show paranccsal](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) megtekintheti az üzembe helyezéshez használt rendszerkép információit. Másik rendszerképből való üzembe helyezéshez használhatja az [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) parancsot is.

1. Jelenítse meg a létrehozott tárfiók kapcsolati sztringét [az az storage account show-connection-string paranccsal.](/cli/azure/storage/account) Cserélje `<storage-name>` le a helyére a fent létrehozott tárfiók nevét:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Adja hozzá ezt a beállítást a függvényalkalmazáshoz [az az functionapp config appsettings set paranccsal.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) A következő parancsban cserélje le a helyére a függvényalkalmazás nevét, a helyére pedig az előző lépésből származó kapcsolati sztringet (egy hosszú kódolt sztringet, amely `<app_name>` `<connection_string>` a "DefaultEndpointProtocol=" karakterláncmal kezdődik):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > A Bashben héjváltozóval rögzítheti a kapcsolati sztringet a vágólap használata helyett. Először a következő paranccsal hozzon létre egy változót a kapcsolati sztring használatával:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Ezután tekintse meg a változót a második parancsban:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. A függvény most már használhatja ezt a kapcsolati sztringet a tárfiók eléréséhez.

> [!NOTE]    
> Ha az egyéni rendszerképet egy privát tárolófiókban teszi közzé, használjon inkább környezeti változókat a Kapcsolati sztringhez a Docker-fájlban. További információ: [ENV utasítás.](https://docs.docker.com/engine/reference/builder/#env) A és a változót is be kell `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD` állítania. Az értékek használatához újra létre kell hoznia a rendszerképet, le kell pusholnia a rendszerképet a regisztrációs adatbázisba, majd újra kell indítania a függvényalkalmazást az Azure-ban.

## <a name="verify-your-functions-on-azure"></a>A függvények ellenőrzése az Azure-ban

Az Azure-beli függvényalkalmazásban üzembe helyezett rendszerkép most már HTTP-kérésekkel hívhatja meg a függvényt. Mivel a *function.js* tartalmazza a tulajdonságot, először be kell szereznie a hozzáférési kulcsot (más néven "függvénykulcsot"), és bele kell foglalnia azt URL-paraméterként a végpontra vonatkozó `"authLevel": "function"` kérésekbe.

1. A függvény URL-címének lekérése a hozzáférési (function) kulccsal a Azure Portal vagy az Azure CLI használatával az `az rest` paranccsal.)

    # <a name="portal"></a>[Portál](#tab/portal)

    1. Jelentkezzen be a Azure Portal, majd keresse meg és válassza a **Függvényalkalmazás lehetőséget.**

    1. Válassza ki az ellenőrizni kívánt függvényt.

    1. A bal oldali navigációs panelen válassza a **Függvények** lehetőséget, majd válassza ki az ellenőrizni kívánt függvényt.

        ![Válassza ki a függvényt a Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Válassza a **Függvény URL-címének bekérte lehetőséget.**

        ![A függvény URL-címének lekért Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Az előugró ablakban válassza az **alapértelmezett (függvénykulcs)** lehetőséget, majd másolja az URL-címet a vágólapra. A kulcs a következő sztringet `?code=` követi: .

        ![Az alapértelmezett függvényelérési kulcs kiválasztása](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Mivel a függvényalkalmazás tárolóként van üzembe helyezni, a függvénykód nem változtatható meg a portálon. Ehelyett frissítenie kell a projektet a helyi rendszerképben, újra le kell lekulpelnie a rendszerképet a regisztrációs adatbázisba, majd újra üzembe kell azt üzembe kellnie az Azure-ban. A folyamatos üzembe helyezést egy későbbi szakaszban állíthatja be.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Szerkesszen egy URL-sztringet a következő formátumban, lecserélve a , és értékeket az Azure-előfizetés azonosítójára, a függvényalkalmazás erőforráscsoportjára, illetve a függvényalkalmazás `<subscription_id>` `<resource_group>` `<app_name>` nevére:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Az URL-cím például a következő címre mutathat:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Az egyszerűség kedvéért hozzárendelheti az URL-címet egy környezeti változóhoz, és használhatja a `az rest` parancsban.
    
    1. Futtassa a következő parancsot (amely az `az rest` Azure CLI 2.0.77-es és újabb verzióiban érhető el), és cserélje le a et az előző lépésben kiadott URI-sztringre, beleértve az `<uri>` idézőjeleket is:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A parancs kimenete a függvénykulcs. A függvény teljes URL-címe `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` lesz, lecserélve `<app_name>` a , és `<function_name>` `<key>` értékeket a megadott értékekre.
    
        > [!NOTE]
        > Az itt lekért  kulcs a függvényalkalmazás összes függvényéhez használható gazdakulcs; A portálon látható metódus csak az egyetlen függvény kulcsát olvassa be.

    ---

1. Illessze be a függvény URL-címét a böngésző címsorába, és adja hozzá a paramétert `&name=Azure` az URL-cím végéhez. A böngészőben meg kell jelenni a "Hello, Azure" szövegnek.

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Az engedélyezés teszteléséhez távolítsa el a paramétert az URL-címből, és ellenőrizze, hogy nem `code=` kap-e választ a függvénytől.


## <a name="enable-continuous-deployment-to-azure"></a>Folyamatos üzembe helyezés engedélyezése az Azure-ban

Engedélyezheti, Azure Functions automatikusan frissítse a rendszerkép üzembe helyezését, amikor frissíti a rendszerképet a beállításjegyzékben.

1. Engedélyezze a folyamatos üzembe helyezést [az az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) paranccsal, és cserélje le a et `<app_name>` a függvényalkalmazás nevére:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Ez a parancs engedélyezi a folyamatos üzembe helyezést, és visszaadja az üzembe helyezési webhook URL-címét. (Ezt az URL-címet később is lekérheti az [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url) paranccsal.)

1. Másolja az üzembe helyezési webhook URL-címét a vágólapra.

1. Nyissa [Docker Hub,](https://hub.docker.com/)jelentkezzen be, és **válassza a** tárház lehetőséget a navigációs sávon. Keresse meg és válassza ki a képet, válassza a **Webhookok** lapot, adjon meg egy **webhooknevet,** illessze be az URL-címét **a Webhook URL-címbe,** majd válassza a **Létrehozás lehetőséget:**

    ![A webhook hozzáadása a DockerHub-adattárhoz](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Ha a webhook be van állítva, Azure Functions a rendszerképet minden alkalommal újra üzembe Docker Hub.

## <a name="enable-ssh-connections"></a>SSH-kapcsolatok engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ha az SSH engedélyezve van, a speciális eszközök (Kudu) App Service csatlakozhat a tárolóhoz. Annak érdekében, hogy SSH-kapcsolaton keresztül könnyedén kapcsolódhat a tárolóhoz, Azure Functions biztosít egy alapként szolgáló rendszerképet, amely már engedélyezve van az SSH-hoz. Csak a Dockerfile-t kell szerkesztenie, majd újraépítenie és újra üzembe kell hoznia a rendszerképet. Ezután csatlakozhat a tárolóhoz a Speciális eszközök (Kudu) használatával

1. A Docker-fájlban fűzza hozzá a `-appservice` sztringet az alapként megadott rendszerképhez az `FROM` utasításban:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Építse újra a rendszerképet a paranccsal, és cserélje le a `docker build` `<docker_id>` et a Docker-azonosítójára:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. A frissített rendszerkép leküldése Docker Hub, amely jelentősen kevesebb időt fog venni, mint az első leküldés, csak a kép frissített szegmensei lesznek feltöltve.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions a rendszerképet automatikusan újra üzembe a függvényalkalmazásban; A folyamat kevesebb mint egy perc alatt le zajlik.

1. Egy böngészőben nyissa meg a et, és cserélje le a `https://<app_name>.scm.azurewebsites.net/` `<app_name>` et az egyedi nevére. Ez az URL-cím a függvényalkalmazás tárolójának Speciális eszközök (Kudu) végpontja.

1. Jelentkezzen be az Azure-fiókjába, majd válassza ki az **SSH-t,** hogy kapcsolatot létesítsen a tárolóval. Ha az Azure még frissíti a tároló rendszerképét, a csatlakozás néhány percig is eltelhet.

1. Miután létrejött a kapcsolat a tárolóval, futtassa az parancsot `top` az aktuálisan futó folyamatok megtekintéséhez. 

    ![SSH-munkamenetben futó legfelső szintű Linux-parancs](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Írás Azure Storage-üzenetsorba

Azure Functions lehetővé teszi a függvények más Azure-szolgáltatásokhoz és -erőforrásokhoz való csatlakoztatását anélkül, hogy saját integrációs kódot kellene írnia. Ezek *a kötések,* amelyek a bemenetet és a kimenetet is képviselik, a függvénydefiníción belül vannak deklarálva. A kötések adatai a függvények számára paraméterekként vannak megadva. Az *eseményindítók* speciális bemeneti kötések. Bár egy függvény csak egy eseményindítóval rendelkezik, több bemeneti és kimeneti kötéssel is rendelkezik. További tudnivalókért lásd [a Azure Functions és kötési fogalmakat.](functions-triggers-bindings.md)

Ez a szakasz bemutatja, hogyan integrálhatja a függvényt egy Azure Storage-üzenetsokkal. A függvényhez adott kimeneti kötés adatokat ír egy HTTP-kérésből az üzenetsorban lévő üzenetbe.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Kód hozzáadása a kimeneti kötéshez

Az üzenetsor kötésének definiálása után frissítheti a függvényt, hogy fogadja a kimeneti paramétert, és üzeneteket írjon `msg` az üzenetsorba.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>A rendszerkép frissítése a beállításjegyzékben

1. A gyökérmappában futtassa újra a parancsot, és ezúttal frissítse a címkében lévő `docker build` verziót a következőre: `v1.0.1` . Ahogy korábban, cserélje `<docker_id>` le a helyére a Docker Hub-fiókjának azonosítóját:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. A frissített rendszerkép visszaküldése az adattárba a `docker push` következővel:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Mivel konfigurálta a folyamatos teljesítést, a regisztrációs adatbázis rendszerképének frissítése automatikusan frissíti a függvényalkalmazást az Azure-ban.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Az üzenet megtekintése az Azure Storage-üzenetsorban

A függvény meghíváshoz használja ugyanazt az URL-címet egy böngészőben, mint korábban. A böngészőnek ugyanazt a választ kell megjelenítenie, mint korábban, mert a függvénykódnak ezt a részét nem módosítja. A hozzáadott kód azonban egy üzenetet írt a tárolási üzenetsor `name` `outqueue` URL-paraméterével.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbbra is használni szeretné az Azure-függvényt az oktatóanyagban létrehozott erőforrások használatával, az összes erőforrást a helyén hagyhatja. Mivel prémium szintű csomagokat hozott létre a Azure Functions, a folyamatos költségek napi egy-két USD-t fognak fizetni.

A folyamatos költségek elkerülése érdekében törölje az erőforráscsoportot a csoportban lévő összes erőforrás `AzureFunctionsContainer-rg` törléséhez: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Következő lépések

+ [Monitorozási függvények](functions-monitoring.md)
+ [Méretezési és üzemeltetési lehetőségek](functions-scale.md)
+ [Kubernetes-alapú kiszolgáló nélküli üzemeltetés](functions-kubernetes-keda.md)
