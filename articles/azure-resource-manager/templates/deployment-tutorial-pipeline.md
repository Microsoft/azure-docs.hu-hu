---
title: Folyamatos integráció az Azure Pipelinesszal
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe Azure Resource Manager sablonokat (ARM-sablonokat) folyamatosan.
ms.date: 03/02/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3ff98c1c033c6da4b6bdf40c3b8ecb3347601741
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722822"
---
# <a name="tutorial-continuous-integration-of-arm-templates-with-azure-pipelines"></a>Oktatóanyag: ARM-sablonok folyamatos integrálása az Azure-folyamatokkal

Az [előző oktatóanyagban](./deployment-tutorial-linked-template.md)egy csatolt sablont helyez üzembe.  Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure-folyamatokat Azure Resource Manager sablon (ARM-sablon) projektjeinek folyamatos létrehozására és üzembe helyezésére.

Az Azure DevOps fejlesztői szolgáltatásokat biztosít a csapatok számára a munka megtervezéséhez, a programkódok fejlesztéséhez való együttműködéshez, valamint alkalmazások létrehozásához és üzembe helyezéséhez. A fejlesztők az Azure DevOps Services használatával dolgozhatnak a felhőben. Az Azure DevOps a webböngészőn vagy az IDE-ügyfélen keresztül elérő szolgáltatások integrált készletét nyújtja. Az Azure-folyamat ezen funkciók egyike. Az Azure-folyamatok egy teljes funkcionalitású folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás. Az előnyben részesített git-szolgáltatóval működik, és a legtöbb jelentős felhőalapú szolgáltatásban üzembe helyezhető. Ezután automatizálhatja a kód kiépítését, tesztelését és üzembe helyezését Microsoft Azure, Google Cloud Platform vagy Amazon Web Services.

> [!NOTE]
> Válasszon egy projekt nevét. Ha átugorja az oktatóanyagot, cserélje le bármelyik **AzureRmPipeline** a projekt nevével.
> A projekt neve az erőforrásnevek létrehozásához használatos.  Az egyik erőforrás egy Storage-fiók. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon. A névnek egyedinek kell lennie. A sablonban a Storage-fiók neve a projekt neve, amely a **tároló** hozzáfűzésével van ellátva, a projekt nevének pedig 3 – 11 karakterből kell állnia. Így a projekt nevének meg kell felelnie a Storage-fiók nevének, és kevesebb, mint 11 karakterből áll.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * GitHub-adattár előkészítése
> * Azure DevOps-projekt létrehozása
> * Azure-folyamat létrehozása
> * A folyamat központi telepítésének ellenőrzése
> * A sablon frissítése és újbóli üzembe helyezése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* **Egy GitHub-fiók**, amelyben felhasználhatja a sablonok tárházának létrehozását. Ha még nem rendelkezik fiókkal, [ingyen létrehozhat egyet](https://github.com). További információ a GitHub-adattárak használatáról: [GitHub-adattárak létrehozása](/azure/devops/pipelines/repos/github).
* **Telepítse a git**-t. Ez az oktatóanyag-utasítás a *git bash* vagy a *git Shell* használatát ismerteti. Útmutatásért lásd: a [git telepítése](https://www.atlassian.com/git/tutorials/install-git).
* **Egy Azure DevOps-szervezet**. Ha még nem rendelkezik fiókkal, ingyen létrehozhat egyet. Lásd: [szervezet vagy projekt gyűjtemény létrehozása](/azure/devops/organizations/accounts/create-organization).
* választható **Visual Studio Code a Resource Manager-eszközök bővítménnyel**. Lásd [a gyors útmutató: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.

## <a name="prepare-a-github-repository"></a>GitHub-adattár előkészítése

A GitHub a projekt forráskódjának tárolására szolgál, beleértve a Resource Manager-sablonokat is. Más támogatott adattárak esetében lásd: az [Azure DevOps által támogatott adattárak](/azure/devops/pipelines/repos/).

### <a name="create-a-github-repository"></a>GitHub-adattár létrehozása

Ha nem rendelkezik GitHub-fiókkal, tekintse meg az [Előfeltételek](#prerequisites)című témakört.

1. Jelentkezzen be a [githubba](https://github.com).
1. Válassza ki a fiókját a jobb felső sarokban, majd válassza ki **a tárházat**.

    ![Azure-DevOps Azure-folyamatok Azure Resource Manager létrehozása GitHub-tárház létrehozásához](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Válassza az **új**, zöld gomb lehetőséget.
1. A **tárház neve** mezőbe írja be az adattár nevét.  Például: **AzureRmPipeline-repo**. Ne felejtse el lecserélni bármelyik **AzureRmPipeline** a projekt nevével. Az oktatóanyagban a **nyilvános** vagy a **magánjellegű** lehetőség közül választhat. Majd válassza a **Tárház létrehozása** lehetőséget.
1. Írja le az URL-címet. Az adattár URL-címe a következő: `https://github.com/[YourAccountName]/[YourRepositoryName]` .

Ezt a tárházat *távoli tárháznak* nevezzük. Ugyanannak a projektnek minden fejlesztője megoszthatja saját *helyi tárházát*, és egyesítheti a módosításokat a távoli tárházban.

### <a name="clone-the-remote-repository"></a>A távoli tárház klónozása

1. Nyissa meg a git-rendszerhéj vagy a git bash felületet. Lásd: [Előfeltételek](#prerequisites).
1. Ellenőrizze, hogy az aktuális mappa a **GitHub**-e.
1. Futtassa az alábbi parancsot:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Cserélje le a `[YourAccountName]` nevet a GitHub-fiók nevére, és cserélje le az `[YourGitHubRepositoryName]` előző eljárás során létrehozott adattár nevére.

A _CreateWebApp_ mappa a sablon tárolására szolgáló mappa. A `pwd` parancs a mappa elérési útját jeleníti meg. Az elérési út a sablon mentése a következő eljárásba.

### <a name="download-a-quickstart-template"></a>Gyorsindítás sablon letöltése

A sablonok létrehozása helyett letöltheti a sablonokat, és mentheti azokat a _CreateWebApp_ mappába.

* A fő sablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* A csatolt sablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

A rendszer a mappa nevét és a fájlneveket is használja, mivel azok a folyamatban vannak. Ha megváltoztatja ezeket a neveket, frissítenie kell a folyamatban használt neveket.

### <a name="push-the-template-to-the-remote-repository"></a>A sablon leküldése a távoli tárházba

A _azuredeploy.js_ bekerült a helyi tárházba. Ezután töltse fel a sablont a távoli tárházba.

1. Ha nincs megnyitva, nyissa meg a *git-rendszerhéj* vagy a *git bash* eszközt.
1. Módosítsa a könyvtárat a helyi tárház _CreateWebApp_ mappájába.
1. Ellenőrizze, hogy a fájl _azuredeploy.js_ a mappában van-e.
1. Futtassa az alábbi parancsot:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin main
    ```

    Előfordulhat, hogy figyelmeztetést kap a LF-ről. Figyelmen kívül hagyhatja a figyelmeztetést. a **Main a** fő ág.  Általában létre kell hoznia egy ágat az egyes frissítésekhez. Az oktatóanyag leegyszerűsítése érdekében közvetlenül a fő ágat használja.

1. Tallózással keresse meg a GitHub-tárházat egy böngészőben. Az URL-cím: `https://github.com/[YourAccountName]/[YourGitHubRepository]` . A mappában a _CreateWebApp_ mappát és a két fájlt kell megtekinteni.
1. A sablon megnyitásához válassza _aazuredeploy.json_ lehetőséget.
1. Kattintson a **RAW** gombra. Az URL-cím kezdete `https://raw.githubusercontent.com` .
1. Másolja az URL-címet. Ezt az értéket kell megadnia, amikor az oktatóanyag későbbi részében konfigurálja a folyamatot.

Eddig létrehozott egy GitHub-tárházat, és feltöltötte a sablonokat a tárházba.

## <a name="create-a-devops-project"></a>DevOps-projekt létrehozása

A következő eljárás folytatásához szükség van egy DevOps-szervezetre. Ha még nem rendelkezik ilyennel, tekintse meg az [Előfeltételek](#prerequisites)című témakört.

1. Jelentkezzen be az [Azure DevOps](https://dev.azure.com)-ba.
1. Válasszon ki egy DevOps-szervezetet a bal oldalon.

    ![Azure DevOps-projekt létrehozása Azure-DevOps Azure-folyamatokkal Azure Resource Manager](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Válassza az **Új projekt** lehetőséget. Ha nem rendelkezik projektekkel, a projekt létrehozása lap automatikusan megnyílik.
1. Írja be a következő értékeket:

    * **Projekt neve**: adja meg a projekt nevét. Az oktatóanyag legelején a projekt nevét is használhatja.
    * **Verziókövetés**: válassza a **git** lehetőséget. Előfordulhat, hogy a **speciális** elemre kell bontania a **verziókövetés** megjelenítéséhez.

    Használja az alapértelmezett értéket a többi tulajdonsághoz.
1. Válassza a **Létrehozás** lehetőséget.

Hozzon létre egy olyan szolgáltatási kapcsolódást, amely a projektek Azure-ba való telepítésére szolgál.

1. A bal oldali menü alján válassza a **projekt beállításai** lehetőséget.
1. Válassza a **szolgáltatási kapcsolatok** lehetőséget a **folyamatok** alatt.
1. Válassza a **szolgáltatási kapcsolatok létrehozása** lehetőséget, válassza a **Azure Resource Manager** lehetőséget, majd kattintson a **tovább** gombra.
1. Válassza ki az **egyszerű szolgáltatásnév** elemet, majd kattintson a **tovább** gombra.
1. Írja be a következő értékeket:

    * **Hatóköri szint**: válassza az **előfizetés** lehetőséget.
    * **Előfizetés**: Válassza ki előfizetését.
    * **Erőforráscsoport**: hagyja üresen.
    * **Kapcsolatok neve**: adjon meg egy nevet. Például: **AzureRmPipeline-Conn**. Jegyezze fel ezt a nevet, a folyamat létrehozásakor szüksége lesz a névre.
    * **Hozzáférési engedély biztosítása minden folyamathoz**. kiválasztott
1. Kattintson a **Mentés** gombra.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Eddig a következő feladatokat végezte el.  Ha kihagyja az előző szakaszt, mert már ismeri a GitHubot és a DevOps-t, a folytatás előtt végre kell hajtania a feladatokat.

* Hozzon létre egy GitHub-tárházat, és mentse a sablonokat az adattár _CreateWebApp_ mappájába.
* Hozzon létre egy DevOps-projektet, és hozzon létre egy Azure Resource Manager szolgáltatás-összekötőt.

Folyamat létrehozása lépéssel a sablon üzembe helyezéséhez:

1. Válassza ki a **folyamatokat** a bal oldali menüben.
1. Válassza a **folyamat létrehozása** lehetőséget.
1. Válassza a **GitHub** lehetőséget a **Connect** (Csatlakozás) lapon. Ha a rendszer kéri, adja meg a GitHub hitelesítő adatait, majd kövesse az utasításokat. Ha a következő képernyő jelenik meg, válassza a **csak adattárak lehetőséget**, és ellenőrizze, hogy az adattár szerepel-e a listában, mielőtt kiválasztja a **jóváhagyás & a telepítés** gombra.

    ![Azure Resource Manager Azure DevOps Azure-folyamatok csak adattárakat választanak](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. A **Select (kiválasztás** ) lapon válassza ki a tárházat. Az alapértelmezett név: `[YourAccountName]/[YourGitHubRepositoryName]` .
1. A **configure (Konfigurálás** ) lapon válassza a **kezdő folyamat** elemet. Megjeleníti a _Azure-pipelines. YML_ folyamat fájlját két parancsfájl lépéssel.
1. Törölje a két parancsfájl lépéseit a _. YML_ fájlból.
1. Vigye a kurzort a sorba a **lépések után:**.
1. Válassza a képernyő jobb oldalán található **Segéd megjelenítése** lehetőséget a **feladatok** ablaktábla megnyitásához.
1. Válassza az **ARM-sablon központi telepítése** lehetőséget.
1. Írja be a következő értékeket:

    * **deploymentScope**: válassza az **erőforráscsoport** elemet. További információ a hatókörökről: [telepítési hatókörök](deploy-rest.md#deployment-scope).
    * **Azure Resource Manager-kapcsolatok**: válassza ki a korábban létrehozott szolgáltatás-összekötő nevét.
    * **Előfizetés**: a cél ELŐfizetési azonosítójának meghatározása.
    * **Művelet**: válassza ki az **erőforráscsoport létrehozása vagy frissítése** műveletet 2 művelet – 1. hozzon létre egy erőforráscsoportot, ha új erőforráscsoport-nevet ad meg; 2. a megadott sablon üzembe helyezése.
    * **Erőforráscsoport**: adjon meg egy új erőforráscsoport-nevet. Például: **AzureRmPipeline-RG**.
    * **Hely**: válassza ki az erőforráscsoport helyét, például az **USA középső** régióját.
    * **Sablon helye**: válassza ki **a fájl URL-címét**, ami azt jelenti, hogy a feladat az URL-cím használatával keresi a sablonfájlt. Mivel a _relativePath_ a fősablonban van használatban, és a _relativePath_ csak URI-alapú telepítések esetén támogatott, itt az URL-címet kell használnia.
    * **Sablon hivatkozása**: adja meg a [GitHub-tárház előkészítése](#prepare-a-github-repository) szakasz végén található URL-címet. Ezzel kezdődik `https://raw.githubusercontent.com` .
    * **Sablon paraméterei hivatkozás**: hagyja üresen ezt a mezőt. A paraméter értékeit a **felülbírálási sablon paraméterei** között kell megadni.
    * **Felülbírálja a sablon paramétereit**: ENTER `-projectName [EnterAProjectName]` .
    * **Üzembe helyezési mód**: válassza a **növekményes** lehetőséget.
    * **Központi telepítés neve**: adja meg a **DeployPipelineTemplate**. A **központi telepítés nevének** megtekintéséhez válassza a **speciális** lehetőséget.

    ![Képernyőfelvétel: az ARM-sablon üzembe helyezési lapja, ahol a szükséges értékek szerepelnek.](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Válassza a **Hozzáadás** lehetőséget.

    További információ a feladatról: az [Azure-erőforráscsoport üzembe helyezési feladata](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)és [Azure Resource Manager sablon központi telepítési feladata](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    A _. YML_ fájl a következőhöz hasonló:

    ![Képernyőfelvétel: a felülvizsgálati oldal a folyamat YAML áttekintése című új folyamattal.](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Kattintson a **Mentés és futtatás** elemre.
1. A **Mentés és Futtatás** ablaktáblán válassza a **Mentés és Futtatás** újra lehetőséget. A rendszer menti a YAML fájl egy példányát a csatlakoztatott adattárba. A YAML-fájlt a tárház tallózásával érheti el.
1. Ellenőrizze, hogy a folyamat végrehajtása sikeresen megtörtént-e.

    ![Azure DevOps Azure-YAML Azure Resource Manager](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. A nevet a folyamat YAML fájlban megadott név határozza meg. Ekkor létrejön egy Storage-fiók. A Storage-fiók neve a **tárolóval** kezdődik.
1. Válassza ki a Storage-fiók nevét a megnyitásához.
1. Válassza ki a **Tulajdonságok** elemet. Figyelje meg, hogy a **replikáció** **helyileg redundáns tárolást (LRS)**.

## <a name="update-and-redeploy"></a>Frissítés és újbóli üzembe helyezés

Amikor frissíti a sablont, és leküldi a módosításokat a távoli tárházba, a folyamat automatikusan frissíti az erőforrásokat, a Storage-fiókot ebben az esetben.

1. Nyissa meg _linkedStorageAccount.jsa_ helyi adattárból a Visual Studio Code-ban vagy bármely szövegszerkesztőben.
1. Frissítse a **Tárfióktípus** **defaultValue** értékét **Standard_GRSra**. Tekintse meg a következő képernyőképet:

    ![Azure-DevOps Azure-YAML frissítésének Azure Resource Manager](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Mentse a módosításokat.
1. A következő parancsok a git bash/shellből való futtatásával küldje el a módosításokat a távoli adattárba.

    ```bash
    git pull origin main
    git add .
    git commit -m "Update the storage account type."
    git push origin main
    ```

    Az első parancs ( `pull` ) szinkronizálja a helyi tárházat a távoli adattárral. A folyamat YAML-fájlja csak a távoli tárházhoz lett hozzáadva. A `pull` parancs futtatása letölti a YAML fájl egy példányát a helyi ágra.

    A negyedik parancs ( `push` ) feltölti a módosított _linkedStorageAccount.js_ fájlt a távoli tárházba. A távoli tárház fő ága frissítve lett, a folyamat újra aktiválódik.

A módosítások ellenőrzéséhez ellenőrizze a Storage-fiók replikáció tulajdonságát. Lásd: [a központi telepítés ellenőrzése](#verify-the-deployment).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

Előfordulhat, hogy törölni szeretné a GitHub-tárházat és az Azure DevOps-projektet is.

## <a name="next-steps"></a>Következő lépések

Gratulálunk, befejezte ezt a Resource Manager-sablon üzembe helyezését ismertető oktatóanyagot. Tudassa velünk, ha megjegyzésekkel és javaslatokkal rendelkezik a visszajelzések szakaszban. Köszönjük!
Készen áll a sablonokkal kapcsolatos speciális fogalmak beugrására. A következő oktatóanyag részletesen ismerteti a sablon-referenciák dokumentációjának használatát, amely segítséget nyújt a telepítendő erőforrások definiálásához.

> [!div class="nextstepaction"]
> [A sablonreferencia felhasználása](./template-tutorial-use-template-reference.md)
