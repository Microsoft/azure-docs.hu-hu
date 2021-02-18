---
title: ASP.NET Core alkalmazások üzembe helyezése az Azure Kubernetes Service-ben az Azure DevOps Starter használatával
description: Az Azure DevOps Starter megkönnyíti az Azure-ban való ismerkedést. A DevOps Starter segítségével néhány gyors lépéssel üzembe helyezheti a ASP.NET Core alkalmazást az Azure Kubernetes szolgáltatással (ak).
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 9ccf28f5431a92f71b1c18e609639d0abf309c06
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590849"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>ASP.NET Core alkalmazások üzembe helyezése az Azure Kubernetes Service-ben az Azure DevOps Starter használatával

Az Azure DevOps Starter egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamat Azure-ba való létrehozásához. 

A DevOps Starter is:

* Automatikusan létrehozza az Azure-erőforrásokat, például az Azure Kubernetes szolgáltatást (ak).
* Létrehoz és konfigurál egy kiadási folyamatot az Azure DevOps-ben, amely létrehoz egy létrehozási és kiadási folyamatot a CI/CD számára.
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.
* Lehetővé teszi [Azure monitor számára](../azure-monitor/containers/container-insights-overview.md) a tárolók számára a tároló munkaterhelések teljesítményének figyelését az AK-fürtön

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * A DevOps Starter használata egy ASP.NET Core-alkalmazás üzembe helyezéséhez az AK-ban
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>A DevOps Starter használata egy ASP.NET Core-alkalmazás üzembe helyezéséhez az AK-ban

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Starter Azure-erőforrásokat is létrehoz, például egy AK-fürtöt az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Válassza a **.net** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **alkalmazás-keretrendszer kiválasztása** területen válassza a **ASP.net Core** lehetőséget, majd kattintson a **tovább** gombra.

1. Válassza a **Kubernetes szolgáltatás** lehetőséget, majd kattintson a **tovább** gombra. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki Azure-előfizetését.

1. A további Azure-konfigurációs beállítások megtekintéséhez és a csomópontok számának azonosításához az AK-fürthöz válassza a **módosítás** lehetőséget. Ez a panel az Azure-szolgáltatások típusának és helyének konfigurálására szolgáló különböző beállításokat jeleníti meg.
 
1. Lépjen ki az Azure-beli konfigurációs területen, majd válassza a **kész** lehetőséget. Néhány perc elteltével a folyamat befejeződött. Egy minta ASP.NET Core alkalmazás egy git-tárházban van beállítva az Azure DevOps-szervezetben, egy AK-fürt létrehozása, egy CI/CD-folyamat végrehajtása, és az alkalmazás üzembe helyezése az Azure-ban történik. 

    Az összes befejezése után az Azure DevOps Starter irányítópultja megjelenik a Azure Portal. A DevOps Starter-irányítópultot közvetlenül a Azure Portal **összes erőforrásáról** is megtekintheti. 

    Ez az irányítópult az Azure DevOps-adattárba, a CI/CD-folyamatba és az AK-fürtbe is betekintést nyújt. Az Azure DevOps-folyamat további CI/CD-beállításokat is konfigurálhat. A jobb oldalon kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.

## <a name="examine-the-aks-cluster"></a>Az AKS-fürt vizsgálata

A DevOps Starter automatikusan konfigurál egy AK-fürtöt, amelyet felderítheti és testreszabhatja. Az AK-fürt megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Starter irányítópultját.

1. A jobb oldalon válassza ki az AK szolgáltatást. Megnyílik egy ablaktábla az AK-fürthöz. Ebből a nézetből különböző műveleteket hajthat végre, például a tároló állapotának figyelését, a naplók keresését és a Kubernetes irányítópultjának megnyitását.

1. A jobb oldalon válassza az **Kubernetes-irányítópult megtekintése** lehetőséget. Szükség esetén a Kubernetes-irányítópult megnyitásához kövesse a lépéseket.

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A DevOps Starter automatikusan konfigurálja a CI/CD-folyamatot az Azure DevOps-szervezetben. Megvizsgálhatja és testre szabhatja a folyamatot. A következők megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Starter irányítópultját.

1. A DevOps alapszintű irányítópultjának tetején válassza a **folyamatok létrehozása** lehetőséget.  Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...).  A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különféle feladatait. A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíróra, válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítók** lehetőséget. A DevOps Starter automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

## <a name="examine-the-cd-release-pipeline"></a>A CD-kiadási folyamat vizsgálata

A DevOps Starter automatikusan létrehozza és konfigurálja a szükséges lépéseket az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez. Ezek a lépések az Azure-DevOps Azure-előfizetéshez való hitelesítéséhez szükséges Azure-szolgáltatási kapcsolatok konfigurálását is tartalmazzák. Az Automation egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure-hoz. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások** lehetőséget.  A DevOps Starter egy kiadási folyamatot hoz létre az Azure-ban üzemelő példányok kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés** lehetőséget. A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon jobb oldalán válassza a **folyamatos üzembe helyezés trigger** lehetőséget. Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek. 

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza a kiadás melletti három pontot (...), majd kattintson a **Megnyitás** gombra. Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**. Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **naplók** lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban 

 > [!NOTE]
 > Az alábbi eljárással a CI/CD-folyamat egy egyszerű szöveg módosításával tesztelhető.

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén. A git-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést. Kövesse az ebben a szakaszban leírt eljárást, vagy használjon másik technikát a tárház változásainak érvényesítéséhez. Megteheti például, hogy a git-tárházat a kedvenc eszközén vagy IDE helyezi, majd leküldi a módosításokat a tárházban.

1. Az Azure DevOps menüjében válassza a **kódlap** lehetőséget  >  , majd nyissa meg a tárházat.

1. Nyissa meg a *Views\Home* könyvtárat, válassza az *index. cshtml* fájl melletti három pontot (...), majd válassza a **Szerkesztés** lehetőséget.

1. Végezze el a fájl módosítását, például a div-címkék egyikén belüli szöveg hozzáadását. 

1. A jobb felső sarokban válassza a végrehajtás lehetőséget, majd a módosítás elküldéséhez válassza a **véglegesítés** **újra lehetőséget.** Néhány pillanat elteltével a buildek az Azure DevOps-ben kezdődnek, és a kiadásokat a rendszer a módosítások üzembe helyezésével hajtja végre. Figyelje a Build állapotát a DevOps Starter irányítópultján vagy a böngészőben az Azure DevOps-szervezettel.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a számlázási díjak felmerülését az erőforrások tisztításával. Ha már nincs rájuk szükség, törölheti az ebben az oktatóanyagban létrehozott AK-fürtöt és a kapcsolódó erőforrásokat. Ehhez használja a **delete** funkciót a DevOps Starter irányítópultján.

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrásokat. A *delete* funkció megsemmisíti a projekt által a DevOps-ben létrehozott, az Azure-ban és az Azure DevOps-ben létrehozott összes adatát, és nem fogja tudni beolvasni. Ezt az eljárást csak akkor használja, ha alaposan elolvasta a kérdéseit.

1. A Azure Portal lépjen a DevOps Starter-irányítópultra.
1. A jobb felső sarokban válassza a **Törlés** lehetőséget. 
1. A parancssorban válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez* .

## <a name="next-steps"></a>Következő lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps Starter használata egy ASP.NET Core-alkalmazás üzembe helyezéséhez az AK-ban
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * Az AKS-fürt vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

A Kubernetes-irányítópult használatával kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A Kubernetes-irányítópult használata](../aks/kubernetes-dashboard.md)