---
title: Java-függvény létrehozása Azure Functions a IntelliJ használatával
description: Ebből a témakörből megtudhatja, hogyan hozhat létre egy egyszerű HTTP-triggert használó Java-függvényt a IntelliJ használatával, amelyet aztán az Azure-beli kiszolgáló nélküli környezetben való futtatásra tehet közzé.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 1edfbca2f2a16aeebc1244b298426dc46d72c6d5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708815"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Az első Java-függvény létrehozása az Azure-ban a IntelliJ használatával

Ez a cikk:
- HTTP-alapú Java-függvények létrehozása egy IntelliJ IDEA projektben.
- A projekt tesztelésének és hibakeresésének lépései a saját számítógép integrált fejlesztői környezetében (IDE).
- Útmutatás a függvény projekt Azure Functions való üzembe helyezéséhez

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Java-függvények az Azure-ban való létrehozásához és közzétételéhez a IntelliJ használatával telepítse a következő szoftvereket:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Egy [Azure által támogatott Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) a Java 8 rendszerhez
+ Telepített [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition vagy Community Edition
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ A legújabb [Function Core-eszközök](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Telepítés és bejelentkezés

1. Az IntelliJ IDEA beállításainak párbeszédpanelén (Ctrl+Alt+S) válassza a **Beépülő modulok** lehetőséget. Ezután keresse meg **az Azure Toolkit for IntelliJ-t** a **Marketplace**-en és kattintson a **Telepítés** elemre. A telepítést követően kattintson az **Újraindítás** elemre a beépülő modul aktiválásához. 

    ![Azure Toolkit for IntelliJ beépülő modul a Marketplace-en][marketplace]

2. Az Azure-fiókjába való bejelentkezéshez nyissa meg az **Azure Explorer** oldalsávot, majd kattintson az **Azure-bejelentkezés** ikonra a felső sávban (vagy az IDEA-menüben: **Eszközök/Azure/Azure-bejelentkezés**).
    ![Az IntelliJ Azure Sign In (Azure-bejelentkezés) parancsa][intellij-azure-login]

3. Az **Azure-bejelentkezés** ablakban válassza a **Bejelentkezés az eszközön** lehetőséget, majd kattintson a **Bejelentkezés** elemre ([egyéb bejelentkezési lehetőségek](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Az Azure bejelentkezési ablaka a kiválasztott Bejelentkezés az eszközön lehetőséggel][intellij-azure-popup]

4. Kattintson a **Copy&Open** (Másolás és megnyitás) elemre az **Azure Device Login** (Azure-bejelentkezés az eszközön) párbeszédpanelen.

   ![Az Azure bejelentkezési párbeszédpanele][intellij-azure-copycode]

5. A böngészőben illessze be az eszköz kódját (amelyet a rendszer az utolsó lépésben **Copy&Open** (Másolás és megnyitás) lehetőségre kattintáskor kimásolt), majd kattintson a **Next** (Tovább) gombra.

   ![Az eszközről történő bejelentkezéshez használt böngésző][intellij-azure-link-ms-account]

6. Az **előfizetések kiválasztása** párbeszédpanelen válassza ki a használni kívánt előfizetéseket, majd kattintson a **kiválasztás** gombra.

   ![Az Előfizetések kiválasztása párbeszédpanel][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Azure Toolkit for IntelliJ segítségével létrehoz egy helyi Azure Functions projektet. A cikk későbbi részében közzéteszi a függvény kódját az Azure-ban. 

1. Nyissa meg a IntelliJ üdvözlő párbeszédpaneljét, válassza az *új projekt létrehozása* elemet új projekt varázsló megnyitásához, majd válassza a *Azure functions* lehetőséget.

    ![Függvény létrehozása projekt](media/functions-create-first-java-intellij/create-functions-project.png)

1. Válassza a *http-trigger* lehetőséget, majd kattintson a *tovább* gombra, és kövesse a varázslót a következő lapokon található összes konfiguráció átadásához. erősítse meg a projekt helyét, majd kattintson a *Befejezés* gombra. A Intellj ÖTLETE ekkor megnyitja az új projektet.

    ![Function projekt létrehozása – Befejezés](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>A projekt helyi futtatása

1. A kód megjelenítéséhez keresse meg `src/main/java/org/example/functions/HttpTriggerFunction.java` a generált kódot. A *17*. sorban láthatja, hogy van egy zöld *Futtatás* gomb, kattintson rá, és válassza az *"Azure-Function-vizsga..." futtatása* lehetőséget, majd láthatja, hogy a Function alkalmazás helyileg fut néhány naplóval.

    ![Helyi futtatási projekt](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Helyi futtatási projekt kimenete](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. A függvényt úgy is kipróbálhatja, hogy a nyomtatott végpontot a böngészőből éri el, például: `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Helyi futtatási függvény teszt eredménye](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. A napló kinyomtatása is megtörténik a GONDOLATában, most a *Leállítás* gombra kattintva állítsa le a Function alkalmazást.

    ![Helyi futtatási függvény teszt naplója](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>A projekt helyi hibakeresése

1. Ha helyileg szeretné felvenni a funkciót a projektben, kattintson a *hibakeresés* gombra az eszköztáron. Ha nem látja az eszköztárat, engedélyezze azt a   >  **megjelenés**  >  **eszköztár** megtekintése lehetőség kiválasztásával.

    ![Helyi hibakeresési funkció alkalmazás gombja](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. A Töréspont hozzáadásához kattintson a fájl *20* . sorára `src/main/java/org/example/functions/HttpTriggerFunction.java` , majd nyissa meg újra a végpontot, és `http://localhost:7071/api/HttpTrigger-Java?name=Azure` Keresse meg a töréspontot, és próbálja ki a további hibakeresési funkciókat, például a *Step*, a *Watch*, a *kiértékelés*. A hibakeresési munkamenet leállításához kattintson a Leállítás gombra.

    ![Helyi hibakeresési funkció alkalmazásának megszakítása](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>A projekt üzembe helyezése az Azure-ban

1. Kattintson a jobb gombbal a projektre a IntelliJ Project Explorerben, válassza az *Azure-> üzembe helyezés lehetőséget Azure functions*

    ![Projekt üzembe helyezése az Azure-ban](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Ha még nincs függvényalkalmazás, kattintson *a nem elérhető függvény lehetőségre, és kattintson az új létrehozása* elemre.

    ![Function-alkalmazás létrehozása az Azure-ban](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Írja be a Function alkalmazás nevét, és válassza a megfelelő előfizetés/platform/erőforráscsoport/App Service terv lehetőséget, itt létrehozhat egy erőforráscsoport/App Service tervet is. Ezután az Alkalmazásbeállítások változatlanok maradnak, kattintson az *OK* gombra, és várjon néhány percet az új Function alkalmazás létrehozásához. Az *új Függvényalkalmazás létrehozása* után a folyamatjelző sáv eltűnik.

    ![Function alkalmazás üzembe helyezése az Azure-alkalmazás létrehozása varázsló](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Válassza ki azt a Function alkalmazást, amelyet központilag telepíteni szeretne, (az imént létrehozott új Function alkalmazás automatikusan ki lesz választva). A függvények telepítéséhez kattintson a *Futtatás* gombra.

    ![A képernyőképen a Azure Functions központi telepítése párbeszédpanel látható.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Function alkalmazás üzembe helyezése az Azure log-ben](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Function apps kezelése ÖTLETből

1. Az Ön ÖTLETE szerint kezelheti a Function-alkalmazásait az *Azure Explorerrel* , és a *függvényalkalmazás* gombra kattintva Itt láthatja az összes Function-alkalmazást.

    ![Function apps megtekintése az Explorerben](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Kattintással válassza ki az egyik Function-alkalmazást, majd kattintson a jobb gombbal a *Tulajdonságok megjelenítése* elemre a Részletek lap megnyitásához. 

    ![Function app-tulajdonságok megjelenítése](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Kattintson a jobb gombbal a *HttpTrigger-Java* Function alkalmazásra, és válassza az *trigger függvény* lehetőséget, majd láthatja, hogy a böngésző megnyílik az trigger URL-címével.

    ![A képernyőképen a U R L-vel rendelkező böngésző látható.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>További függvények hozzáadása a projekthez

1. Kattintson a jobb gombbal a csomag *org. example. functions* elemre, és válassza a *New-> Azure functions osztály* elemet. 

    ![Függvények hozzáadása a projekt bejegyzéséhez](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Töltse ki az osztály neve *HttpTest* , és válassza a *HttpTrigger* elemet a Function osztály létrehozása varázslóban, kattintson az *OK* gombra a létrehozáshoz, így új függvényeket hozhat létre.

    ![Képernyőfelvétel: a függvény osztályának létrehozása párbeszédpanel.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Függvények hozzáadása a projekt kimenetéhez](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Függvények tisztítása

1. Függvények törlése az Azure Explorerben
      
      ![A képernyőképen a helyi menüből kiválasztott törlés látható.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Következő lépések

Létrehozott egy Java-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
