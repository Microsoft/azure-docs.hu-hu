---
title: Az Azure Kubernetes üzembe helyezési központja
description: Az Azure DevOps üzembe helyezési központja leegyszerűsíti a robusztus Azure DevOps-folyamat beállítását az alkalmazáshoz
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932217"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Az Azure Kubernetes üzembe helyezési központja

Az Azure DevOps üzembe helyezési központja leegyszerűsíti a robusztus Azure DevOps-folyamat beállítását az alkalmazáshoz. Alapértelmezés szerint a központi telepítési központ konfigurálja az Azure DevOps folyamatot az alkalmazás frissítéseinek a Kubernetes-fürtön való üzembe helyezéséhez. Kiterjesztheti az alapértelmezett konfigurált Azure DevOps folyamatot, és gazdagabb képességeket is hozzáadhat: a jóváhagyás megkezdése előtt, további Azure-erőforrások üzembe helyezéséhez, parancsfájlok futtatásához, az alkalmazás frissítéséhez, és még több ellenőrzési teszt futtatásához.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Konfiguráljon egy Azure DevOps-folyamatot az alkalmazás frissítéseinek a Kubernetes-fürtre történő telepítéséhez.
> * Vizsgálja meg a folyamatos integráció (CI) folyamatát.
> * Vizsgálja meg a folyamatos teljesítés (CD) folyamatát.
> * Törölje az erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

* Egy Azure Kubernetes-szolgáltatás (ak) fürtje.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).

1. A Azure Portal menüsávjának jobb oldalán válassza a [Cloud Shell](../cloud-shell/overview.md) lehetőséget.

1. Az AK-fürt létrehozásához futtassa a következő parancsokat:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Alkalmazások frissítéseinek központi telepítése Kubernetes-fürtre

1. Nyissa meg az előző szakaszban létrehozott erőforráscsoportot.

1. Válassza ki az AK-fürtöt, majd a bal oldali panelen válassza a **Deployment Center (előzetes verzió)** lehetőséget. Válassza az első **lépések** lehetőséget.

   ![A képernyőfelvételen a központi telepítési központra mutató nyíl látható Azure Portal.](media/deployment-center-launcher/settings.png)

1. Válassza ki a kód helyét, és kattintson a **Tovább gombra**. Ezután válassza ki az egyik jelenleg támogatott tárházat: **[Azure Repos](/azure/devops/repos/index)** vagy **GitHub**.

    Az Azure Repos olyan verziókövetés-eszközök összessége, amelyek segítenek a kód kezelésében. Azt jelzi, hogy a szoftveres projekt mérete nagy vagy kicsi, jó ötlet a verziókövetés a lehető leghamarabb.

    - **Azure Repos**: válasszon egy tárházat a meglévő projektből és szervezetből.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: engedélyezze és válassza ki a GitHub-fiók adattárát.

        ![Az animáció egy olyan folyamatot mutat be a GitHubon, amely a GitHubot kiválasztja forrásként, majd kiválasztja a tárházat.](media/deployment-center-launcher/github.gif)


1. A központi telepítési központ elemzi a tárházat, és észleli a Docker. Ha frissíteni szeretné a Docker, módosíthatja az azonosított portszámot.

    ![Alkalmazásbeállítások](media/deployment-center-launcher/application-settings.png)

    Ha a tárház nem tartalmazza a Docker, a rendszer megjelenít egy üzenetet, amelyet véglegesíteni kell.

    ![Képernyőfelvétel: a központi telepítési központ egy üzenettel nem található Docker a tárházban.](media/deployment-center-launcher/dockerfile.png)

1. Válasszon ki egy meglévő tároló-beállításjegyzéket, vagy hozzon létre egyet, majd kattintson a **Befejezés gombra**. A folyamat automatikusan létrejön, és egy Build az [Azure-folyamatokban](/azure/devops/pipelines/index).

    Az Azure-folyamatok egy felhőalapú szolgáltatás, amellyel automatikusan felépítheti és tesztelheti a kód projektjét, és elérhetővé teheti azokat más felhasználók számára. Az Azure-folyamatok folyamatosan és következetesen tesztelik és felépítik a folyamatos integrációt és a folyamatos teljesítést, és minden célra elszállítják a kódot.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Kattintson a hivatkozásra a folyamatban lévő folyamat megtekintéséhez.

1. Az üzembe helyezés befejezése után megjelenik a sikeres naplók.

    ![Képernyőfelvétel: az 1-es kiadású, zöld pipa ikonnal jelölt üzembehelyezési központ.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A Deployment Center automatikusan konfigurálja az Azure DevOps-szervezet CI/CD-folyamatát. A folyamat megvizsgálható és testreszabható.

1. Nyissa meg a Deployment Center irányítópultját.  

1. Válassza ki a létrehozási számot a sikeres naplók listájáról a projekt összeállítási folyamatának megtekintéséhez.

1. Kattintson a jobb felső sarokban lévő három pontra (...). A menü számos lehetőséget mutat be, például az új buildek kiépítését, a buildek megőrzését és a build folyamat szerkesztését. Válassza a **folyamat szerkesztése** lehetőséget. 

1. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különböző feladatait. A build különböző feladatokat hajt végre, például forrásokat gyűjt a git-tárházból, létrehoz egy rendszerképet, leküldi a rendszerképet a tároló-beállításjegyzékbe, és közzéteszi az üzembe helyezésekhez használt kimeneteket.

1. Válassza ki az összeállítási folyamat nevét a folyamat tetején.

1. Módosítsa a felépítési folyamat nevét egy ennél részletesebb leírásra, válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra.

1. A létrehozási folyamat alatt válassza az **Előzmények** lehetőséget. Ez a panel a legutóbbi fordítási változások naplózási nyomvonalát mutatja be. Az Azure DevOps figyeli a felépítési folyamat során történt módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítók** lehetőséget. A CI-folyamatból ágakat is hozzáadhat vagy kizárhat.

1. Válassza a **Megtartást**. Megadhat olyan házirendeket, amelyekkel a forgatókönyvtől függően megtarthatja vagy eltávolíthatja a buildek számát.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

A Deployment Center automatikusan létrehozza és konfigurálja az Azure DevOps-szervezet és az Azure-előfizetés közötti kapcsolatot. Az ehhez szükséges lépések közé tartozik az Azure-szolgáltatási kapcsolatok beállítása az Azure-előfizetés Azure DevOps való hitelesítéséhez. Az automatizált folyamat egy kiadási folyamatot is létrehoz, amely folyamatos kézbesítést biztosít az Azure-hoz.

1. Válassza a **folyamatok** lehetőséget, majd válassza a **kiadások** lehetőséget.

1. A kiadási folyamat szerkesztéséhez válassza a **Szerkesztés** lehetőséget.

1. Válassza a **drop** elemet **az összetevők listából.** Az előző lépésekben a megvizsgált szerkezeti folyamat létrehozza az összetevőhöz használt kimenetet. 

1. Válassza ki a **folyamatos üzembe helyezési** triggert a **drop** kapcsoló jobb oldalán. Ez a kiadási folyamat egy olyan engedélyezett CD-triggerrel rendelkezik, amely egy üzembe helyezést futtat, amikor új Build-összetevő érhető el. Letilthatja az indítást is, ha manuális végrehajtást kíván végrehajtani az üzemelő példányokhoz.

1. A folyamat összes feladatának vizsgálatához válassza a **feladatok** lehetőséget. A kiadás beállítja a kormányrúd környezetet, konfigurálja a (z `imagePullSecrets` ) paramétert, telepíti a Helm-eszközöket, és üzembe helyezi a Helm-diagramokat a Kubernetes-fürtön.

1. A kiadási előzmények megtekintéséhez válassza a **kiadások megtekintése** lehetőséget.

1. Az összefoglalás megtekintéséhez válassza a **kiadás** lehetőséget. Válassza ki bármelyik szakaszt a több menü, például a kiadási összefoglalás, a társított munkaelemek és a tesztek megismeréséhez. 

1. Válassza a **Véglegesítéseket**. Ez a nézet a telepítéssel kapcsolatos kód-véglegesítő kódokat jeleníti meg. A kiadások összehasonlításával tekintheti meg az üzemelő példányok közötti különbségeket.

1. Válassza a **naplók** lehetőséget. A naplók hasznos telepítési információkat tartalmaznak, amelyeket az üzembe helyezések közben és után tekinthet meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott kapcsolódó erőforrásokat törölheti, ha már nincs rájuk szükség. Használja a DELETE funkciót a DevOps Projects irányítópulton.

## <a name="next-steps"></a>Következő lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Másik lehetőségként ezt a CI/CD modellt is használhatja sablonként a többi folyamathoz.
