---
title: Környezetek integrálása Azure-folyamatokra Azure DevTest Labs
description: Ismerje meg, hogyan integrálhatja Azure DevTest Labs környezeteit az Azure DevOps folyamatos integrációs (CI) és a folyamatos továbbítási (CD-) folyamatokba.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483023"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Környezetek integrálása az Azure DevOps CI/CD-folyamatokba
Az Azure DevOps Services szolgáltatásban (korábbi nevén Visual Studio Team Services) telepített Azure DevTest Labs Tasks bővítmény használatával egyszerűen integrálhatja a folyamatos integrációs (CI)/folyamatos kézbesítés (CD) build-és kiadási folyamatát Azure DevTest Labsokkal. Ezek a bővítmények megkönnyítik a [környezet](devtest-lab-test-env.md) gyors üzembe helyezését egy adott teszt feladathoz, majd a teszt befejezésekor törli azt. 

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy környezetet, majd törölheti a környezetet egy teljes folyamaton belül. Ezeket a feladatokat általában a saját egyéni Build-test-üzembe helyezési folyamata során egyénileg hajthatja végre. A cikkben használt bővítmények a DTL virtuálisgép- [feladatok létrehozásához és törléséhez](devtest-lab-integrate-ci-cd.md)szükségesek:

- Környezet létrehozása
- Környezet törlése

## <a name="before-you-begin"></a>Előkészületek
A CI/CD-folyamat Azure DevTest Labs használatával történő integrálásához telepítse [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) bővítményt a Visual Studio Piactérről. 

## <a name="create-and-configure-the-lab-for-environments"></a>Tesztkörnyezet létrehozása és konfigurálása környezetekhez
Ez a szakasz azt ismerteti, hogyan lehet létrehozni és konfigurálni egy olyan labort, amelyben az Azure-környezet üzembe lesz helyezve.

1. [Hozzon létre egy labort](devtest-lab-create-lab.md) , ha még nem rendelkezik ilyennel. 
2. Konfigurálja a labort, és hozzon létre egy környezeti sablont a jelen cikk utasításait követve: [hozzon létre több virtuális gépre kiterjedő környezeteket és a Pásti-erőforrásokat Azure Resource Manager sablonokkal](devtest-lab-create-environment-from-arm.md).
3. Ebben a példában egy meglévő Azure gyorsindítási sablont használunk [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) .
4. Másolja a **201-Web-App-Redis-cache-SQL-Database** mappát a 2. lépésben konfigurált adattár **ArmTemplate** mappájába.

## <a name="create-a-release-definition"></a>Kiadási definíció létrehozása
A kiadás definíciójának létrehozásához tegye a következőket:

1.  A **Build & kiadási központ** **kiadások** lapján válassza a **pluszjel (+)** gombot.
2.  A **kiadás definíciójának létrehozása** ablakban válassza ki az **üres** sablont, majd válassza a **tovább** lehetőséget.
3.  Válassza a **később** kiválasztása lehetőséget, majd a **Létrehozás** lehetőséget választva hozzon létre egy új kiadási definíciót egyetlen alapértelmezett környezettel, és ne legyen csatolt összetevők.
4.  A helyi menü megnyitásához az új kiadás definíciójában válassza a környezet neve melletti **három pontot (...)** , majd válassza a **változók konfigurálása** lehetőséget.
5.  A **Konfigurálás – környezet** ablakban a kiadási definíciós feladatokban használt változóknál adja meg a következő értékeket:
1.  A **administratorLogin** mezőben adja meg az SQL-rendszergazda bejelentkezési nevét.
2.  A **administratorLoginPassword** mezőben adja meg az SQL-rendszergazdai bejelentkezés által használandó jelszót. A jelszó elrejtéséhez és védelméhez használja a "lakat" ikont.
3.  A **databaseName** mezőben adja meg a SQL Database nevét.
4.  Ezek a változók a példában szereplő környezetekre jellemzőek, a különböző környezetek eltérő változókkal rendelkezhetnek.

## <a name="create-an-environment"></a>Környezet létrehozása
Az üzembe helyezés következő lépése a fejlesztési vagy tesztelési célokra használandó környezet létrehozása.

1. A kiadás definíciójában válassza a **feladatok hozzáadása** elemet.
2. A **feladatok** lapon vegyen fel egy Azure DevTest Labs környezet létrehozása feladatot. Konfigurálja a feladatot a következőképpen:
    1. Az **Azure RM-előfizetés** esetében válasszon ki egy kapcsolatot az **elérhető Azure-szolgáltatások kapcsolatai** listán, vagy hozzon létre egy korlátozott engedélyeket az Azure-előfizetéshez való kapcsolódáshoz. További információ: [Azure Resource Manager szolgáltatási végpont](/azure/devops/pipelines/library/service-endpoints).
2. A **labor neve** mezőben válassza ki a korábban létrehozott példány nevét *.
3. A **tárház neve** mezőben válassza ki azt a tárházat, amelyben a Resource Manager-sablont (201) leküldte a * rendszerbe.
4. A **sablon neve** mezőben válassza ki a forráskód adattárba mentett környezet nevét. 
5. A **labor neve**, a **tárház neve** és a **sablon neve** az Azure-erőforrás-azonosítók felhasználóbarát ábrázolása. Ha manuálisan adja meg a rövid nevet, a rendszer hibákat fog okozni, a legördülő lista segítségével pedig kiválaszthatja az adatokat.
6. A **környezet neve** mezőben adjon meg egy nevet, amely egyedileg azonosítja a környezeti példányt a laboron belül.  Egyedinek kell lennie a laboron belül.
7. A **paraméter fájl** és a **Paraméterek** lehetővé teszik az egyéni paraméterek átadását a környezetnek. A paraméterek értékeinek beállításához bármelyik vagy mindkettő használható. Ebben a példában a parameters (paraméterek) szakaszt fogjuk használni. Használja a környezetben meghatározott változók nevét, például: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. A környezeti sablonban található információk a sablon kimenet szakaszában adhatók át. A **környezeti sablon kimenete alapján tekintse meg a kimeneti változók létrehozását** , hogy más feladatok is használhassák az adatokat. `$(Reference name.Output Name)` a követendő minta. Például, ha a hivatkozás neve DTL volt, és a sablonban lévő kimenet neve a változó lenne `$(DTL.location)` .

## <a name="delete-the-environment"></a>A környezet törlése
Az utolsó lépés az Azure DevTest Labs-példányban üzembe helyezett környezet törlése. Általában törli a környezetet a fejlesztői feladatok végrehajtása vagy a szükséges tesztek futtatása után az üzembe helyezett erőforrásokon.

A kiadás definíciójában válassza a **feladatok hozzáadása** lehetőséget, majd a **telepítés** lapon adjon hozzá egy **Azure DevTest Labs törlés környezeti** feladatot. Konfigurálja az alábbiak szerint:

1. A virtuális gép törléséhez tekintse meg a [Azure DevTest Labs feladatokat](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Az **Azure RM-előfizetés** esetében válasszon ki egy kapcsolatot az **elérhető Azure-szolgáltatások kapcsolatai** listán, vagy hozzon létre egy korlátozott engedélyeket az Azure-előfizetéshez való kapcsolódáshoz. További információ: [Azure Resource Manager szolgáltatási végpont](/azure/devops/pipelines/library/service-endpoints).
    2. A **labor neve** mezőben válassza ki azt a labort, ahol a környezet létezik.
    3. A **környezet neve** mezőben adja meg az eltávolítandó környezet nevét.
2. Adja meg a kiadás definíciójának nevét, majd mentse azt.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 
- [Több virtuális gépre kiterjedő környezetek létrehozása Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md).
- Gyors útmutató Resource Manager-sablonok a DevTest Labs automatizálásához a [DevTest Labs GitHub-adattárból](https://github.com/Azure/azure-quickstart-templates).
- [VSTS hibaelhárítási oldala](/azure/devops/pipelines/troubleshooting)

