---
title: 'Oktatóanyag: a Node.js-alkalmazás üzembe helyezése az Azure-webalkalmazásban a DevOps Starter használatával a GitHub-műveletekhez'
description: A DevOps Starter segítségével könnyedén elsajátíthatja az Azure-t, és néhány gyors lépéssel üzembe helyezheti Node.js alkalmazását az Azure-webalkalmazásban.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 4caac943df33177728997c74c9ec812105efd62c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588777"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Oktatóanyag: Node.js alkalmazás üzembe helyezése az Azure-webalkalmazásban a DevOps Starter használatával a GitHub-műveletekhez

A DevOps Starter for GitHub-műveletek egyszerűsített felhasználói élményt nyújtanak, ahol kiválaszthatja a folyamatos integrációs (CI) és folyamatos kézbesítési (CD) munkafolyamatot az Azure-ba való üzembe helyezéshez. 

A DevOps Starter is:
* Automatikusan létrehoz Azure-erőforrásokat, például egy új Azure-webalkalmazást.
* Létrehoz és konfigurál egy olyan munkafolyamatot a GitHubban, amely tartalmazza a CI-hez készült Build feladatot.
* A munkafolyamat a CD-re vonatkozó üzembe helyezési feladatot is tartalmaz. 
* Létrehoz egy Azure Application Insights-erőforrást a figyeléshez.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Node.js-alkalmazás üzembe helyezése a DevOps Starter használatával
> * A GitHub és egy Azure-előfizetés konfigurálása 
> * A GitHub-munkafolyamat vizsgálata
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>Node.js-alkalmazás üzembe helyezése a DevOps Starter használatával

A DevOps Starter létrehoz egy munkafolyamatot a GitHubban. Használhat egy meglévő GitHub-szervezetet. A DevOps Starter emellett Azure-erőforrásokat is létrehoz, például a webalkalmazást az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Győződjön meg arról, hogy a CI/CD-szolgáltató **GitHub-műveletként** van kiválasztva.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Válassza a **Node.js** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **alkalmazás-keretrendszer kiválasztása** területen válassza a **Express.js** lehetőséget, majd kattintson a **tovább** gombra. Az alkalmazás-keretrendszer, amelyet az előző lépésben választott, az itt elérhető Azure-szolgáltatás telepítési célját diktálja be. 

1. Válassza ki a **Windows-webalkalmazást**, majd kattintson a **tovább** gombra.

## <a name="configure-github-and-an-azure-subscription"></a>A GitHub és egy Azure-előfizetés konfigurálása

1. **Engedélyezés** GitHub, és válasszon ki egy meglévő GitHub-szervezetet. 

1. Adja meg a GitHub- **tárház** nevét. 

1. Válassza ki az Azure-előfizetési szolgáltatásokat. Ha szeretné, a **módosítás** lehetőségre kattintva további konfigurációs adatokat is megadhat, például az Azure-erőforrások helyét.
 
1. Adja meg a webalkalmazás nevét, majd kattintson a **kész** gombra. Néhány perc elteltével a rendszer készen áll az Azure-webalkalmazásra. Egy minta Node.js alkalmazás van beállítva egy adattárban a GitHub-szervezetben, a rendszer elindítja a munkafolyamatot, és az alkalmazást üzembe helyezi az újonnan létrehozott Azure-webalkalmazásban.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   A befejezést követően a DevOps Starter irányítópultja megjelenik a Azure Portal. Közvetlenül az irányítópulton is megnyithatja a Azure Portal **összes erőforrását** . 

   Az irányítópulton látható a GitHub-kód tárháza, a CI/CD-munkafolyamat, valamint az Azure-ban futó alkalmazás.   

   ![Irányítópult nézet](_img/azure-devops-project-nodejs/full-dashboard.png)

A DevOps Starter automatikusan konfigurál egy triggert, amely a kód módosítását telepíti a tárházban.
    
## <a name="examine-the-github-workflow"></a>A GitHub-munkafolyamat vizsgálata

Az előző lépésben a DevOps Starter automatikusan egy teljes GitHub-munkafolyamatot konfigurált. Szükség szerint vizsgálja meg és szabja testre a munkafolyamatot. A következő lépésekkel Ismerkedjen meg a munkafolyamattal.

1. A DevOps Starter irányítópult bal oldalán válassza a **GitHub-munkafolyamat** elemet. Ez a hivatkozás egy böngésző fület és a GitHub-munkafolyamatot nyitja meg az új projekthez.
    > [!NOTE]
    > Ne nevezze át a munkafolyamat-fájlt. A munkafolyamat-fájl nevének **devops-Starter-workflow. YML** kell lennie ahhoz, hogy az irányítópult tükrözze a módosításokat

1. A munkafolyamat YAML fájlja tartalmazza az alkalmazás létrehozásához és üzembe helyezéséhez szükséges összes GitHub-műveletet. Kattintson a **fájl szerkesztése** lehetőségre a munkafolyamat-fájl testreszabásához.

1. A tárház **kód** lapján kattintson a **véglegesítés** elemre. Ez a nézet az adott központi telepítéshez társított kód-véglegesítő kódokat jeleníti meg.

1. A tárház **műveletek** lapján megtekintheti az adattár összes munkafolyamat-futtatásának előzményeit.

1. A **legutóbbi Futtatás** lehetőség kiválasztásával megtekintheti a munkafolyamatban futtatott összes feladatot.

1. A **feladatokra** kattintva megtekintheti a munkafolyamat-Futtatás részletes naplóit. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

1. Kattintson a **pull-kérelem** lapra a tárházban lévő összes lekéréses kérelem megtekintéséhez

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Starter létrehoz egy tárházat a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. A DevOps Starter irányítópult bal oldalán válassza ki a fő ág hivatkozását. Ez a hivatkozás egy nézetet nyit meg az újonnan létrehozott GitHub-tárházban.

1. A tárház klónozási URL-címének megtekintéséhez válassza a böngésző jobb felső sarkában található **klónozás** elemet. A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngészővel teheti meg és véglegesítheti a kód módosításait közvetlenül a fő ágra.

1. A böngésző bal oldalán lépjen a **/Application/views/index.PUG** fájlra.

1. Válassza a **Szerkesztés** lehetőséget, majd végezze el a módosítást egy bizonyos szövegre.
    Módosíthatja például az egyik címke szövegét.

1. Válassza **a** végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Starter irányítópultját.   
Ekkor meg kell jelennie a GitHub-munkafolyamat felépítési feladatainak. A rendszer automatikusan létrehoz és telepít egy GitHub-munkafolyamaton keresztül az imént létrehozott módosításokat.

1. A telepítés befejezése után frissítse az alkalmazást a módosítások ellenőrzéséhez.

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
> Az alábbi eljárás véglegesen törli az erőforrásokat. A *delete* funkció megsemmisíti a projekt által a DevOps Starter-ben létrehozott, az Azure-ban található összes adatát, és nem fogja tudni lekérni. Ezt az eljárást csak akkor használja, ha alaposan elolvasta a kérdéseit.

1. A Azure Portal lépjen a DevOps Starter-irányítópultra.
1. A jobb felső sarokban válassza a **Törlés** lehetőséget. 
1. A parancssorban válassza az **Igen** lehetőséget az erőforrások *végleges törléséhez* .

Igény szerint módosíthatja a munkafolyamatot, hogy az megfeleljen a csapat igényeinek. Ezt a CI/CD-mintát sablonként is használhatja a többi tárházhoz. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Node.js-alkalmazás üzembe helyezése a DevOps Starter használatával
> * A GitHub és egy Azure-előfizetés konfigurálása 
> * A GitHub-munkafolyamat vizsgálata
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure Application Insights monitorozásának konfigurálása
> * Az erőforrások eltávolítása

A GitHub-műveletekkel és-munkafolyamatokkal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A GitHub-munkafolyamat testreszabása](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
