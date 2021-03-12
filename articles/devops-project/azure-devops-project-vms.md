---
title: 'Oktatóanyag: a ASP.NET-alkalmazás üzembe helyezése az Azure Virtual Machines szolgáltatásban az Azure DevOps Starter használatával'
description: A DevOps Starter segítségével könnyedén elsajátíthatja az Azure-t, és néhány gyors lépéssel üzembe helyezheti a ASP.NET alkalmazást az Azure-beli virtuális gépeken.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 3495d0bd2a446b6b3255887d9b4523eb5a70ac53
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557318"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Oktatóanyag: a ASP.NET-alkalmazás üzembe helyezése az Azure Virtual Machines szolgáltatásban az Azure DevOps Starter használatával

Az Azure DevOps Starter egy egyszerűsített felhasználói felülettel rendelkezik, ahol meglévő kód-és git-tárházat hozhat létre, vagy kiválaszthat egy minta alkalmazást egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamat Azure-ba való létrehozásához. 

A DevOps Starter is:
* Automatikusan létrehoz Azure-erőforrásokat, például egy új Azure-beli virtuális gépet (VM).
* Létrehoz és konfigurál egy kiadási folyamatot az Azure DevOps-ben, amely tartalmazza a CI-hez készült Build folyamatát.
* Egy kiadási folyamat beállítása a CD-hez. 
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * A ASP.NET-alkalmazás üzembe helyezése a DevOps Starter használatával
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>A ASP.NET-alkalmazás üzembe helyezése a DevOps Starter használatával

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Projects Azure-erőforrásokat is létrehoz, például virtuális gépeket az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Válassza a **.net** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **alkalmazás-keretrendszer kiválasztása** területen válassza a **ASP.net** lehetőséget, majd kattintson a **tovább** gombra. Az alkalmazás-keretrendszer, amelyet az előző lépésben választott, az itt elérhető Azure-szolgáltatás telepítési célját diktálja be. 

1. Válassza ki a virtuális gépet, majd kattintson a **tovább** gombra.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

1. Adja meg az Azure DevOps-projekt nevét. 

1. Válassza ki az Azure-előfizetési szolgáltatásokat. Ha szeretné, a **módosítás** lehetőségre kattintva további konfigurációs adatokat is megadhat, például az Azure-erőforrások helyét.
 
1. Adja meg a virtuális gép nevét, felhasználónevét és jelszavát az új Azure-beli virtuálisgép-erőforráshoz, majd válassza a **kész** lehetőséget. Néhány perc elteltével az Azure-beli virtuális gép készen áll. A rendszer egy minta ASP.NET-alkalmazást állít be egy adattárban az Azure DevOps-szervezetben, létrehoz egy összeállítást és egy kiadást, és az alkalmazást üzembe helyezi az újonnan létrehozott Azure-beli virtuális gépen. 

   A befejezést követően a DevOps Starter irányítópultja megjelenik a Azure Portal. Közvetlenül az irányítópulton is megnyithatja a Azure Portal **összes erőforrását** . 

   Az irányítópulton látható az Azure DevOps-kódjának tárháza, a CI/CD-folyamat, valamint az Azure-ban futó alkalmazás.   

   ![Irányítópult nézet](_img/azure-devops-project-vms/vm-starter-dashboard.png)

A DevOps Starter automatikusan konfigurálja a CI-Build és a kiadási triggert, amely programkód-módosításokat helyez üzembe a tárházban. További beállításokat is konfigurálhat az Azure DevOpsban. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.
    
## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata
 
A DevOps Starter automatikusan konfigurálta az Azure-folyamatok CI/CD-folyamatát. Megvizsgálhatja és testre szabhatja a folyamatot. A létrehozási folyamat megismeréséhez tegye a következőket:

1. A DevOps alapszintű irányítópultjának tetején válassza a **folyamatok létrehozása** lehetőséget. Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...). A menüben számos lehetőség látható, például az új buildek várólistába helyezése, egy Build felfüggesztése és a build folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különféle feladatait. A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíróra, válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítók** lehetőséget. A DevOps Starter automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Lehetőség van arra is, hogy az ágakat belefoglalja vagy kizárja a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

## <a name="examine-the-cd-pipeline"></a>A CD-folyamat vizsgálata

A DevOps Starter automatikusan létrehozza és konfigurálja a szükséges lépéseket az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez. Ezek a lépések az Azure-DevOps Azure-előfizetéshez való hitelesítéséhez szükséges Azure-szolgáltatási kapcsolatok konfigurálását is tartalmazzák. Az Automation egy CD-folyamatot is létrehoz, amely biztosítja a CD-t az Azure-beli virtuális géphez. Ha többet szeretne megtudni az Azure DevOps CD-folyamatáról, tegye a következőket:

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások** lehetőséget.  A DevOps Starter egy kiadási folyamatot hoz létre az Azure-ban üzemelő példányok kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés** lehetőséget. A kiadási folyamat tartalmaz egy *folyamatot*, amely meghatározza a kiadási folyamatot.

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon mellett válassza a **folyamatos üzembe helyezési trigger** lehetőséget. Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek. 

1. A bal oldalon válassza a **feladatok** lehetőséget, majd válassza ki a környezetet. A tevékenységek a telepítési folyamat által végrehajtandó tevékenységek, és fázisokban vannak csoportosítva. Ez a kiadási folyamat két fázisban történik:

    - Az első fázis egy Azure erőforráscsoport-telepítési feladatot tartalmaz, amely két dolgot tesz:
     
        - A virtuális gép konfigurálása üzembe helyezéshez
        -   Hozzáadja az új virtuális gépet egy Azure DevOps üzembe helyezési csoportjához. Az Azure DevOps virtuálisgép-telepítési csoportja felügyeli a telepítési cél gépek logikai csoportjait
     
    - A második fázisban az IIS-webalkalmazások kezelése feladat létrehoz egy IIS-webhelyet a virtuális gépen. A rendszer létrehoz egy második IIS-webalkalmazás-üzembe helyezési feladatot a hely telepítéséhez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza a kiadás melletti három pontot (...), majd kattintson a **Megnyitás** gombra. Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**. Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **naplók** lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban 

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkát a webhelyén. A git-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést. Kövesse az ebben a szakaszban leírt eljárást, vagy használjon másik technikát a tárház változásainak érvényesítéséhez. A kód módosításai kezdeményezik a CI/CD folyamatot, és automatikusan telepítik a módosításokat az IIS-webhelyre az Azure-beli virtuális gépen.

1. A bal oldali panelen válassza a **kód** lehetőséget, majd lépjen a tárházba.

1. Nyissa meg a *Views\Home* könyvtárat, válassza az *index. cshtml* fájl melletti három pontot (...), majd válassza a **Szerkesztés** lehetőséget.

1. Végezze el a fájl módosítását, például a div-címkék egyikén belüli szöveg hozzáadását. 

1. A jobb felső sarokban válassza a végrehajtás lehetőséget, majd a módosítás elküldéséhez válassza a **véglegesítés** **újra lehetőséget.** Néhány pillanat elteltével a buildek az Azure DevOps-ben kezdődnek, és a kiadásokat a rendszer a módosítások üzembe helyezésével hajtja végre. Figyelje a Build állapotát a DevOps Starter irányítópultján vagy a böngészőben az Azure DevOps-szervezettel.

1. A kiadás befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

## <a name="configure-azure-application-insights-monitoring"></a>Az Azure Application Insights monitorozásának konfigurálása

Az Azure Application Insights segítségével egyszerűen monitorozhatja alkalmazása teljesítményét és használatát. A DevOps Starter automatikusan konfigurálja Application Insights erőforrást az alkalmazáshoz. Szükség szerint további riasztásokat és monitorozási lehetőségeket is konfigurálhat.

1. A Azure Portal lépjen a DevOps Starter-irányítópultra. 

1. A jobb alsó sarokban válassza ki a **Application Insights** hivatkozást az alkalmazáshoz. Megnyílik a **Application Insights** panel. Ebben a nézetben az alkalmazás használatára, teljesítményére és rendelkezésre állásának monitorozására vonatkozó információk találhatók.

   ![A Application Insights panel](_img/azure-devops-project-github/appinsights.png) 

1. Válassza ki az **időtartomány** elemet, majd válassza az **előző óra** lehetőséget. Az eredmények szűréséhez válassza a **frissítés** lehetőséget. Mostantól az elmúlt 60 percben megtekintheti az összes tevékenységet. 
    
1. Az időtartományból való kilépéshez válassza az **x** elemet.

1. Válassza a **riasztások** lehetőséget, majd kattintson a **metrika riasztás hozzáadása** lehetőségre. 

1. Adja meg a riasztás nevét.

1. A **metrika** legördülő listában vizsgálja meg a különböző riasztási metrikákat. Az alapértelmezett riasztás az **1 másodpercnél hosszabb kiszolgáló-válaszidőre** vonatkozik. Egyszerűen konfigurálhat különböző riasztásokat az alkalmazás monitorozási képességeinek fejlesztéséhez.

1. Jelölje be az **értesítés e-mailben tulajdonosok, közreműködők és olvasók** számára jelölőnégyzetet. Szükség esetén további műveleteket is végrehajthat, ha egy Azure logikai alkalmazás végrehajtásával riasztás jelenik meg.

1. A riasztás létrehozásához kattintson **az OK gombra** . Néhány pillanat elteltével a riasztás aktívként jelenik meg az irányítópulton. 

1. Lépjen ki a **riasztások** területről, és térjen vissza a **Application Insights** ablaktáblára.

1. Válassza a **rendelkezésre állás** lehetőséget, majd kattintson a **teszt hozzáadása** lehetőségre. 

1. Adja meg a teszt nevét, majd válassza a **Létrehozás** lehetőséget. Létrejön egy egyszerű ping-teszt az alkalmazás rendelkezésre állásának ellenőrzéséhez. Néhány perc elteltével elérhetővé válnak a teszteredmények, és az Application Insights-irányítópulton megjelenik a rendelkezésre állás állapota.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha teszteli, elkerülheti a számlázási díjak felmerülését az erőforrások tisztításával. Ha már nincs rájuk szükség, törölheti az oktatóanyagban létrehozott Azure-beli virtuális gépet és az ahhoz kapcsolódó erőforrásokat. Ehhez használja a **delete** funkciót a DevOps Starter irányítópultján. 

> [!IMPORTANT]
> Az alábbi eljárás véglegesen törli az erőforrásokat. A *delete* funkció megsemmisíti a projekt által a DevOps-ben létrehozott, az Azure-ban és az Azure DevOps-ben létrehozott összes adatát, és nem fogja tudni beolvasni. Ezt az eljárást csak akkor használja, ha alaposan elolvasta a kérdéseit.

1. A Azure Portal lépjen a DevOps Starter-irányítópultra.
1. A jobb felső sarokban válassza a **Törlés** lehetőséget. 
1. A parancssorban válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez* .

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A ASP.NET-alkalmazás üzembe helyezése a DevOps Starter használatával
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Az Azure Repos változásainak érvényesítése és automatikus üzembe helyezése az Azure-ban
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](/azure/devops/pipelines/release/define-multistage-release-process)