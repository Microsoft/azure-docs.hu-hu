---
title: Kotlin-függvény létrehozása Azure Functions a IntelliJ használatával
description: Ebből a témakörből megtudhatja, hogyan hozhat létre egy egyszerű HTTP-triggert Kotlin-függvényt a IntelliJ használatával, amelyet aztán egy kiszolgáló nélküli környezetben futtathat az Azure-ban.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f02643ee28d76d4f90206a1aa2879b4672da2a38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179445"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Az első Kotlin-függvény létrehozása az Azure-ban a IntelliJ használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre egy HTTP-alapú Java-függvényt egy IntelliJ IDEA-projektben, hogyan futtathatja és hibakeresést végezhet a projektben az integrált fejlesztési környezetben (IDE), majd végül üzembe helyezheti a Function projektet az Azure-beli Function alkalmazásban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Kotlin-függvények az Azure-ban való létrehozásához és közzétételéhez a IntelliJ használatával telepítse a következő szoftvereket:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), 8-as verzió
- [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió
- [INTELLIJ Idea](https://www.jetbrains.com/idea/download), közösségi vagy Ultimate verziók a Maven használatával
- [Azure CLI](/cli/azure)
- A Azure Functions Core Tools [2. x verziója](functions-run-local.md#v2) . Helyi fejlesztési környezetet biztosít a Azure Functions írásához, futtatásához és hibakereséséhez.

> [!IMPORTANT]
> A jelen cikkben ismertetett lépések végrehajtásához a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="create-a-function-project"></a>Függvényprojekt létrehozása

1. A IntelliJ ÖTLETben válassza az **új projekt létrehozása** lehetőséget.  
1. Az **új projekt** ablakban válassza a **Maven** lehetőséget a bal oldali ablaktáblán.
1. Jelölje be a **Létrehozás az archetípus alapján** jelölőnégyzetet, majd válassza az **archetípus hozzáadása** lehetőséget az [Azure-functions-Kotlin-archetípus](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)elemnél.
1. Az **archetípus hozzáadása** ablakban végezze el a következő mezőket:
    - _GroupID_: com. microsoft. Azure
    - _ArtifactId_: Azure-functions-Kotlin-archetípus
    - _Verzió_: a [központi tárház](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)legújabb verziójának használata a 
     ![ Maven-projekt létrehozása az archetípusból a IntelliJ IDEA-ban](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Válassza **az OK**, majd a **tovább** lehetőséget.
1. Adja meg az aktuális projekt adatait, majd kattintson a **Befejezés gombra**.

A Maven egy új mappában hozza létre a projektfájlt, amelynek a neve megegyezik a _ArtifactId_ értékével. A projekt generált kódja egy egyszerű [http-triggert](./functions-bindings-http-webhook.md) használó függvény, amely a kiváltó HTTP-kérelem törzsét visszhangzik.

## <a name="run-project-locally-in-the-ide"></a>Projekt helyi futtatása az IDE-ben

> [!NOTE]
> A projekt helyi futtatásához és hibakereséséhez győződjön meg arról, hogy telepítette a [2. verziót Azure functions Core Tools](functions-run-local.md#v2).

1. Manuálisan importálhatja a módosításokat, vagy engedélyezheti az [automatikus importálást](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Nyissa meg a **Maven-projektek** eszköztárat.
1. Bontsa ki az **életciklus** csomópontot, majd nyissa meg a **csomagot**. A megoldás egy újonnan létrehozott célkönyvtár keretében lett létrehozva és csomagolva.
1. Bontsa ki a **plugins**  >  **Azure-functions** és az **Azure-functions megnyitása: Futtatás** parancsot a Azure functions helyi futtatókörnyezet elindításához.  
  ![Maven-eszköztár a Azure Functionshoz](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. A függvény tesztelésének befejezése után zárjuk be a Futtatás párbeszédpanelt. Egyszerre csak egy Function Host lehet aktív és helyileg futni.

## <a name="debug-the-project-in-intellij"></a>A projekt hibakeresése a IntelliJ-ben

1. A Function Host hibakeresési módban való elindításához adja meg a **-DenableDebug** paramétert a függvény futtatásakor. Módosíthatja a [Maven-célok](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) konfigurációját, vagy futtathatja a következő parancsot egy terminál-ablakban:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   A parancs hatására a Function Host egy hibakeresési portot nyit meg a 5005-es időpontban.

1. A **Futtatás** menüben válassza a **konfigurációk szerkesztése** lehetőséget.
1. **Távoli** hozzáadásához válassza a **(+)** lehetőséget.
1. Fejezze be a _név_ és a _Beállítások_ mezőt, majd kattintson az **OK gombra** a konfiguráció mentéséhez.
1. A telepítés után válassza a **hibakeresés < a távoli konfiguráció neve >** vagy a billentyűzeten a SHIFT + F9 billentyűkombinációval indítsa el a hibakeresést.

   ![Hibakeresési projekt a IntelliJ-ben](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Ha elkészült, állítsa le a hibakeresőt és a futó folyamatot. Egyszerre csak egy Function Host lehet aktív és helyileg futni.

## <a name="deploy-the-project-to-azure"></a>A projekt központi telepítése az Azure-ban

1. Mielőtt üzembe helyezi a projektet egy Azure-beli Function alkalmazásban, be kell [jelentkeznie az Azure CLI használatával](/cli/azure/authenticate-azure-cli).

   ``` azurecli
   az login
   ```

1. Telepítse a kódot egy új Function-alkalmazásba a `azure-functions:deploy` Maven-cél használatával. A Maven-projektek ablakban az **Azure-functions: Deploy (üzembe helyezés** ) lehetőséget is kiválaszthatja.

   ```
   mvn azure-functions:deploy
   ```

1. Keresse meg a HTTP-trigger függvény URL-címét az Azure CLI kimenetében a Function app sikeres telepítése után.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Következő lépések

Most, hogy telepítette az első Kotlin-függvényt az Azure-ba, tekintse át a Java-és Kotlin-függvények fejlesztésével kapcsolatos további információkat a [Java fejlesztői útmutató Azure functions](functions-reference-java.md) .
- Vegyen fel további functions-alkalmazásokat különböző eseményindítókkal a projekthez a `azure-functions:add` Maven-cél használatával.
