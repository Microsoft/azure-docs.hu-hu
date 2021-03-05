---
title: Azure Function-alkalmazás létrehozása a Java és az Eclipse használatával
description: Útmutató egy egyszerű, HTTP által aktivált kiszolgáló nélküli alkalmazás létrehozásához és közzétételéhez a Java és az Eclipse használatával Azure Functions.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 7dd881d130b9df19335ac64be501553af99d58d8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179544"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Az első függvény létrehozása a Java és az Eclipse használatával 

Ebből a cikkből megtudhatja, hogyan hozhat létre [kiszolgáló](https://azure.microsoft.com/solutions/serverless/) nélküli függvény projektet az Eclipse ide és az Apache Maven használatával, tesztelheti és hibakeresést végezhet, majd üzembe helyezheti Azure functions. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Ha Java-és Eclipse-alapú functions-alkalmazást szeretne létrehozni, a következőknek kell telepítve lennie:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), 8-as verzió.
-  [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.
-  [Eclipse](https://www.eclipse.org/downloads/packages/), Java és Maven támogatással.
-  [Azure CLI](/cli/azure)

> [!IMPORTANT] 
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

Javasoljuk, hogy a [Azure functions Core Tools, 2-es verziójú verziót](functions-run-local.md#v2)is telepítse, amely helyi környezetet biztosít a Azure functions futtatásához és hibakereséséhez. 

## <a name="create-a-functions-project"></a>Functions-projekt létrehozása

1. Az Eclipse-ben válassza a **fájl** menüt, majd az **új- &gt; Maven projekt** elemet. 
1. Fogadja el az alapértelmezett értékeket az **új Maven-projekt** dialógusban, és kattintson a **Tovább gombra**.
1. Keresse meg és válassza ki az [Azure-functions-archetípus](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype) elemet, és kattintson a **tovább** gombra.
1. Ügyeljen arra, hogy az összes mező értékeit kitöltse, például:, `resourceGroup` `appName` és `appRegion` (használjon más appName, mint a **fabrikam-Function-20170920120101928**), és végül **fejezze** be a műveletet.
    ![Az Eclipse Maven create2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

A Maven egy _artifactId_ nevű új mappában hozza létre a projektfájlokat. A projektben létrehozott kód egy egyszerű http- [triggert](./functions-bindings-http-webhook.md) használó függvény, amely a kiváltó HTTP-kérelem törzsét visszhangzik.

## <a name="run-functions-locally-in-the-ide"></a>Függvények helyi futtatása az IDE-ben

> [!NOTE]
> A ( [Azure functions Core Tools) 2. verziójának](functions-run-local.md#v2) telepítve kell lennie a függvények helyi futtatásához és hibakereséséhez.

1. Kattintson a jobb gombbal a generált projektre, majd válassza a **Futtatás as** és **Maven Build** lehetőséget.
1. A **konfiguráció szerkesztése** párbeszédpanelen adja meg `package` a **célok** és **név** mezőket, majd válassza a **Futtatás** lehetőséget. Ezzel létrehozza és becsomagolja a függvény kódját.
1. A Build befejezése után hozzon létre egy másik futtatási konfigurációt a fentieknek megfelelően, `azure-functions:run` a célt és a nevet használva. Válassza a **Futtatás** lehetőséget a függvény futtatásához az ide-ben.

Ha végzett a függvény tesztelésével, szakítsa meg a futtatókörnyezetet a konzol ablakban. Egyszerre csak egy Function Host lehet aktív és helyileg futni.

### <a name="debug-the-function-in-eclipse"></a>A függvény hibakeresése az Eclipse-ben

Az előző lépésben beállított **futtató** konfigurációban váltson `azure-functions:run` át, `azure-functions:run -DenableDebug` és futtassa a frissített konfigurációt a Function alkalmazás hibakeresési módban történő elindításához.

Válassza a **Futtatás** menüt, és nyissa meg a **hibakeresési konfigurációkat**. Válassza ki a **távoli Java-alkalmazást** , és hozzon létre egy újat. Adja meg a konfiguráció nevét, és adja meg a beállításokat. A portnak konzisztensnek kell lennie a Function Host által megnyitott hibakeresési porttal, amely alapértelmezés szerint a `5005` . A telepítés után kattintson a be gombra a `Debug` hibakeresés megkezdéséhez.

![Hibakeresési függvények az Eclipse-ben](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Az IDE használatával töréspontokat állíthat be, és megvizsgálhatja a függvényben lévő objektumokat. Ha elkészült, állítsa le a hibakeresőt és a futó függvény gazdagépét. Egyszerre csak egy Function Host lehet aktív és helyileg futni.

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure Functions üzembehelyezési folyamata az Azure parancssori felületről származó fiókhitelesítő-adatokat használja. A számítógép parancssorának folytatása előtt [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli) .

```azurecli
az login
```

A kód üzembe helyezése egy új Function alkalmazásban a `azure-functions:deploy` Maven céljával egy új **futtató** konfigurációban.

Az üzembe helyezés végén megjelenik az URL-cím, amellyel bejelentkezhet az Azure-függvényalkalmazásba:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Következő lépések

- Ha további információra van szüksége a Java-függvények létrehozásával kapcsolatban, tekintse át a [Java-függvények fejlesztői útmutatóját](functions-reference-java.md).
- Adjon hozzá különböző triggereket használó további funkciókat a projekthez az `azure-functions:add` Maven-cél használatával.
