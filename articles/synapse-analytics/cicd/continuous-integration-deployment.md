---
title: Folyamatos integráció és kézbesítés a szinapszis-munkaterülethez
description: Megtudhatja, hogyan használhatja a folyamatos integrációt és a kézbesítést a munkaterületen végrehajtott módosítások egy másik környezetből (fejlesztés, tesztelés, éles környezetben) való üzembe helyezéséhez.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5f82e8b7359b90d5127e2c20a2b89cc5ad739a56
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624759"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Folyamatos integráció és kézbesítés az Azure szinapszis-munkaterülethez

## <a name="overview"></a>Áttekintés

A folyamatos integráció (CI) a kód kiépítésének és tesztelésének automatizálása minden alkalommal, amikor egy csapattag véglegesíti a verziókövetés változásait. A folyamatos üzembe helyezés (CD) az a folyamat, amellyel több tesztelési vagy átmeneti környezetből lehet éles környezetbe felépíteni, tesztelni, konfigurálni és üzembe helyezni.

Az Azure szinapszis munkaterülete esetében a folyamatos integráció és a szállítás (CI/CD) minden entitást az egyik környezetből (fejlesztés, tesztelés, termelés) helyez át egy másikra. Ha a munkaterületet egy másik munkaterületre szeretné előléptetni, két részből áll: a munkaterület-erőforrások (készletek és munkaterületek) létrehozásához vagy frissítéséhez [Azure Resource Manager-sablonok](../../azure-resource-manager/templates/overview.md) használatával. az összetevők (SQL-parancsfájlok, jegyzetfüzetek, Spark-feladattípusok, folyamatok, adatforgalom stb.) áttelepíthetők a szinapszis CI/CD-eszközökkel az Azure DevOps-ben. 

Ebből a cikkből megtudhatja, hogyan automatizálható a szinapszis-munkaterület telepítése több környezetbe az Azure kiadási folyamat használatával.

## <a name="prerequisites"></a>Előfeltételek

-   A fejlesztéshez használt munkaterület a Studio git-tárházával lett konfigurálva, lásd: a [szinapszis Studio verziókövetés](source-control.md).
-   Egy Azure DevOps-projekt készült a kiadási folyamat futtatásához.

## <a name="set-up-a-release-pipelines"></a>Kiadási folyamatok beállítása

1.  Az [Azure DevOps](https://dev.azure.com/)nyissa meg a kiadáshoz létrehozott projektet.

1.  A lap bal oldalán válassza a **folyamatok**, majd a **kiadások** elemet.

    ![Folyamatok, kiadások kiválasztása](media/create-release-1.png)

1.  Válassza az **új folyamat** lehetőséget, vagy ha meglévő folyamatokkal rendelkezik, válassza az **új** , majd az **új kiadási folyamat** lehetőséget.

1.  Válassza ki az **üres** sablont.

    ![Üres feladatok kiválasztása](media/create-release-select-empty.png)

1.  A **szakasz neve** mezőben adja meg a környezet nevét.

1.  Válassza az összetevő **hozzáadása** lehetőséget, majd válassza ki a fejlesztési szinapszis Studióval konfigurált git-tárházat. Válassza ki a készletek és munkaterületek ARM-sablonjának kezeléséhez használt git-tárházat. Ha a GitHubot használja forrásként, létre kell hoznia egy szolgáltatási kapcsolódást a GitHub-fiókjához, és le kell kérnie a tárolókat. További információ a [szolgáltatás kapcsolatairól](/azure/devops/pipelines/library/service-endpoints) 

    ![Közzétételi ág hozzáadása](media/release-creation-github.png)

1.  Válassza ki az ARM-sablon ágat az erőforrások frissítéséhez. Az **alapértelmezett verziónál** válassza a **legutóbbi lehetőséget az alapértelmezett ág** lehetőségnél.

    ![ARM-sablon hozzáadása](media/release-creation-arm-branch.png)

1.  Válassza ki az adattár [közzétételi ágát](source-control.md#configure-publishing-settings) az **alapértelmezett ág** számára. Alapértelmezés szerint ez a közzétételi ág `workspace_publish` . Az **alapértelmezett verziónál** válassza a **legutóbbi lehetőséget az alapértelmezett ág** lehetőségnél.

    ![Összetevő hozzáadása](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Fázis-feladat beállítása az ARM-erőforrások létrehozásához és frissítéséhez 

Azure Resource Manager telepítési feladat hozzáadása erőforrások létrehozásához vagy frissítéséhez, beleértve a munkaterületet és a készleteket:

1. A fázis nézetben válassza a **fázis-feladatok megtekintése** lehetőséget.

    ![Szakasz nézet](media/release-creation-stage-view.png)

1. Hozzon létre egy új feladatot. Keresse meg az **ARM-sablon központi telepítését**, majd válassza a **Hozzáadás** lehetőséget.

1. A központi telepítési feladat területen válassza ki az előfizetést, az erőforráscsoportot és a célhelyet a cél munkaterülethez. Szükség esetén adja meg a hitelesítő adatokat.

1. A **művelet** listában válassza az **erőforráscsoport létrehozása vagy frissítése** lehetőséget.

1. Kattintson a három pont gombra (**...**) a **sablon** mező mellett. Tallózással keresse meg a cél munkaterület Azure Resource Manager sablonját

1. Válassza a **... lehetőséget.** a **sablon paramétereinek** mező mellett válassza ki a paramétereket tartalmazó fájlt.

1. Válassza a **... lehetőséget.** a **felülírási sablon paraméterei** mező mellett adja meg a kívánt paramétereket a cél munkaterülethez. 

1. Válassza a **növekményes** lehetőséget a **központi telepítési módhoz**.
    
    ![munkaterület és készletek üzembe helyezése](media/pools-resource-deploy.png)

1. Választható **Azure PowerShell** hozzáadása a munkaterület szerepkör-hozzárendelésének engedélyezéséhez és frissítéséhez. Ha a kiadási folyamattal hoz létre egy szinapszis-munkaterületet, a rendszer a folyamat egyszerű szolgáltatását adja hozzá alapértelmezett munkaterület-rendszergazdaként. A PowerShell futtatásával más fiókokhoz is hozzáférést biztosíthat a munkaterülethez. 
    
    ![engedély megadása](media/release-creation-grant-permission.png)

 > [!WARNING]
> A teljes üzembe helyezési módban az erőforráscsoporthoz tartozó, de az új Resource Manager-sablonban nem megadott erőforrások **törlődni** fognak. További információkért tekintse meg [Azure Resource Manager telepítési módokat](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Fázis-feladat beállítása az összetevők üzembe helyezéséhez 

A [szinapszis munkaterület üzembe](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) helyezési bővítményével más elemeket helyezhet üzembe a szinapszis munkaterületen, például ADATKÉSZLET, SQL-parancsfájl, jegyzetfüzet, Spark-feladatdefiníció, adatfolyam, folyamat, társított szolgáltatás, hitelesítő adatok és IR (Integration Runtime).  

1. A bővítmény keresése és beszerzése az **Azure DevOps piactéren**(https://marketplace.visualstudio.com/azuredevops) 

     ![Bővítmény beolvasása](media/get-extension-from-market.png)

1. Válasszon ki egy szervezetet a bővítmény telepítéséhez. 

     ![A bővítmény telepítése](media/install-extension.png)

1. Győződjön meg arról, hogy az Azure DevOps-folyamat egyszerű szolgáltatása az előfizetés engedéllyel rendelkezik, és a cél munkaterület munkaterület-rendszergazdája is hozzá van rendelve. 

1. Hozzon létre egy új feladatot. Keressen rá a **szinapszis munkaterület üzembe helyezésére**, majd válassza a **Hozzáadás** lehetőséget.

     ![Bővítmény hozzáadása](media/add-extension-task.png)

1.  A feladatban válassza a **... lehetőséget.** **a sablon mező mellett** válassza ki a sablonfájlt.

1. Válassza a **... lehetőséget.** a **sablon paramétereinek** mező mellett válassza ki a paramétereket tartalmazó fájlt.

1. Válassza ki a kapcsolatokat, az erőforráscsoportot és a cél munkaterület nevét. 

1. Válassza a **... lehetőséget.** a **felülírási sablon paraméterei** mező mellett adja meg a kívánt paramétereket a cél munkaterülethez. 

    ![Szinapszis-munkaterület üzembe helyezése](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> A CI/CD-helyzetekben a különböző környezetekben lévő Integration Runtime (IR) típusnak azonosnak kell lennie. Ha például saját üzemeltetésű integrációs modult használ a fejlesztési környezetben, akkor ugyanazt az IR-t más környezetekben, például tesztelési és üzemi környezetben is önálló üzemeltetéssel kell eltárolni. Hasonlóképpen, ha több fázisban osztja meg az integrációs modulokat, az integrációs modulokat az összes környezetben, például a fejlesztés, a tesztelés és az éles környezetek szerint kell konfigurálni.

## <a name="create-release-for-deployment"></a>Kiadás létrehozása az üzembe helyezéshez 

Az összes módosítás mentése után kiválaszthatja, hogy a kiadás **létrehozása** lehetőséggel manuálisan hozzon létre egy kiadást. A kiadások létrehozásának automatizálásához tekintse meg az [Azure DevOps kiadási eseményindítók](/azure/devops/pipelines/release/triggers) című témakört.

   ![Válassza a kiadás létrehozása lehetőséget](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Ajánlott eljárások CI/CD-hez

Ha git-integrációt használ a szinapszis-munkaterülettel, és rendelkezik egy CI/CD-folyamattal, amely a változásokat a fejlesztésből teszteli, majd éles környezetbe helyezi, javasoljuk az alábbi ajánlott eljárásokat:

-   **Git-integráció**. Csak a fejlesztési szinapszis munkaterületet konfigurálja a git-integrációval. A teszt-és éles munkaterületek változásai a CI/CD-n keresztül telepíthetők, és nincs szükség git-integrációra.
-   **Készletek előkészítése az összetevők migrálása előtt**. Ha a fejlesztői munkaterületen a készletekhez csatolt SQL-parancsfájl vagy jegyzetfüzet van, akkor a rendszer a különböző környezetekben lévő készletek azonos nevét is elvárta. 
-   **Infrastruktúra-kód (IaC)**. Az infrastruktúra (hálózatok, virtuális gépek, terheléselosztó és a kapcsolatok topológiája) kezelése egy leíró modellben ugyanazt a verziószámozást használja, mint a DevOps-csapat a forráskódot használja. 
-   **Mások**. Lásd: [ajánlott eljárások az ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd) -összetevőkhöz

## <a name="troubleshooting-artifacts-deployment"></a>Összetevők telepítésének hibaelhárítása 

### <a name="use-the-synapse-workspace-deployment-task"></a>A szinapszis munkaterület telepítési feladatának használata

A Szinapszisban számos olyan összetevő van, amely nem ARM-erőforrás. Ez eltér a Azure Data Factorytól. Az ARM-sablon telepítési feladata nem fog megfelelően működni a szinapszis-összetevők üzembe helyezéséhez
 
### <a name="unexpected-token-error-in-release"></a>Váratlan jogkivonat-hiba történt a kiadásban

Ha a paraméter értéke nem kerül megmenekülésre, a kiadási folyamat nem fogja elemezni a fájlt, és a "váratlan token" hibaüzenetet eredményezi. Javasoljuk, hogy felülbírálja a paramétereket, vagy használja az Azure kulcstartót a paraméterek értékeinek lekéréséhez. A kettős Escape-karakterek is megkerülő megoldásként is használhatók.
