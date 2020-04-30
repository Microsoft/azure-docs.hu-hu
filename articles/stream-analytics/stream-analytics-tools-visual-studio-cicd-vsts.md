---
title: Azure Stream Analytics feladatok üzembe helyezése CI/CD-vel és Azure DevOps
description: A cikk azt írja le, hogyan helyezhet üzembe egy Azure Stream Analytics-feladatot a CI/CD-vel az Azure DevOps Services használatával.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d9360ff64206cdce208f9643cf8ca86515aaeb7e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75354436"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Oktatóanyag: Azure Stream Analytics-feladat üzembe helyezése CI/CD-vel az Azure Pipelines használatával
Ez az oktatóanyag azt ismerteti, hogyan lehet folyamatos integrációt és üzembe helyezést beállítani egy Azure Stream Analytics-feladathoz az Azure Pipelines használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Forráskezelés hozzáadása a projekthez
> * Buildelési folyamat létrehozása az Azure Pipelinesban
> * Kiadási folyamat létrehozása az Azure Pipelinesban
> * Alkalmazás automatikus üzembe helyezése és frissítése

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Visual Studiót](stream-analytics-tools-for-visual-studio-install.md) és az **Azure-fejlesztési** vagy az **Adattárolási és -feldolgozási** számítási feladatokat.
* Hozzon létre egy [Stream Analytics-projektet a Visual Studióban](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Hozzon létre egy [Azure DevOps](https://visualstudio.microsoft.com/team-services/) -szervezetet.

## <a name="configure-nuget-package-dependency"></a>NuGet csomagfüggőség konfigurálása
Amennyiben automatikus létrehozást vagy automatikus telepítést kíván alkalmazni egy tetszőleges gépen, a `Microsoft.Azure.StreamAnalytics.CICD` NuGet-csomagot kell használnia. Ez tartalmazza az MSBuild-, helyi futtatási, valamint a telepítési eszközöket, amelyek támogatják a Stream Analytics Visual Studio-projektek folyamatos integrációs és üzembehelyezési folyamatait. További információ: [Stream Analytics CI/CD-eszközök](stream-analytics-tools-for-visual-studio-cicd.md).

Adja hozzá a **packages.config** fájlt a projekt könyvtárához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>A Visual Studio-megoldás megosztása egy új Azure Repos Git-adattárban

Az alkalmazás forrásfájljait megoszthatja az Azure DevOps egyik projektjében, és ezáltal buildeket hozhat létre.  

1. Hozzon létre egy új helyi Git-adattárat a projekthez. Ehhez válassza ki a Visual Studio jobb alsó sarkában található állapotsoron az **Add to Source Control** (Hozzáadás a forráskezelőhöz), majd a **Git** elemet. 

2. A **Team Explorer****Synchronization** (Szinkronizálás) nézetében válassza ki a **Push to Azure DevOps Services** (Leküldés az Azure DevOps Services szolgáltatásba) alatt található **Publish Git Repo** (Git-adattár közzététele) gombot.

   ![Leküldéses az Azure DevOps Services – git-tárház közzététele gomb](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. Ellenőrizze az e-mail-címet, és válassza ki a saját szervezetét az **Azure DevOps Services Domain** (Azure DevOps Services tartomány) legördülő listájából. Adja meg az adattár nevét, majd válassza ki a **Publish repository** (Adattár közzététele) lehetőséget.

   ![A git-tárház közzétételének leküldése gomb](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    Az adattár közzétételével egy új projekt jön létre a szervezetben a helyi adattáréval azonos néven. Ha egy meglévő projektben szeretné létrehozni a tárházat, kattintson a **repository neve**melletti **speciális** elemre, és válasszon ki egy projektet. A kód böngészőben való megtekintéséhez válassza a **See it on the web** (Megtekintés a weben) lehetőséget.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Folyamatos továbbítás konfigurálása az Azure DevOps használatával
A Team Pipelines buildelési folyamat egy olyan munkafolyamatot ír le, amely egymás után végrehajtott buildelési lépések sorozatából áll. További tudnivalók az [Azure Pipelines buildelési folyamatokról](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Az Azure Pipelines kiadási folyamata olyan munkafolyamatot ír le, amely egy alkalmazáscsomagot telepít egy fürtre. Együttes használatuk esetén a buildelési és a kiadási folyamat a teljes munkafolyamatot végrehajtja, amely a forrásfájlokkal kezdődik, és a fürtön futó alkalmazással ér véget. További információ az Azure Pipelines [kiadási folyamatairól](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts).

### <a name="create-a-build-pipeline"></a>Buildelési folyamat létrehozása
Nyisson meg egy webböngészőt, majd keresse meg az [Azure DevOpsban](https://app.vsaex.visualstudio.com/) létrehozott projektet. 

1. A **Build & Release** (Build és kiadás) lapon válassza a **Builds** (Buildek) és a **+ New** (+ Új) lehetőséget.  Válassza az **Azure DevOps Services Git** és a **Continue** (Folytatás) lehetőséget.
    
    ![DevOps git-forrás kiválasztása az Azure DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. A **Select a template** (Sablon kiválasztása) területen kattintson az **Empty Process** (Üres folyamat) elemre, ha egy üres folyamattal kezdené meg a munkát.
    
    ![Válassza az üres folyamat lehetőséget a sablon beállításaiban a DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. A **Triggers** (Eseményindítók) lehetőségnél engedélyezze a folyamatos integrációt az **Enable continuous integration** (Folyamatos integráció engedélyezése) triggerállapot bejelölésével.  Válassza ki a **Save and queue** (Mentés és üzenetsorba helyezés) elemet a build manuális elindításához. 
    
    ![Folyamatos integrációs trigger állapotának engedélyezése](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. A buildek leküldés vagy bejelentkezés hatására is aktiválódnak. A létrehozási folyamat ellenőrzéséhez váltson a builds ( **buildek** ) lapra.  Miután meggyőződött arról, hogy a Build sikeresen lefut, meg kell adnia egy kiadási folyamatot, amely üzembe helyezi az alkalmazást egy fürtön. Kattintson a jobb gombbal a buildelési folyamat melletti három pontra, és válassza az **Edit** (Szerkesztés) lehetőséget.

5.  A **Tasks** (Feladatok) között az **Agent queue** (Ügynöküzenetsor) megfelelőjeként adja meg a következőt: Hosted.
    
    ![Ügynök üzenetsor kiválasztása a feladatok menüben](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. Kattintson a **Phase 1** (1. fázis) szakasz **+** elemére, majd adjon hozzá egy **NuGet**-feladatot.
    
    ![NuGet feladat hozzáadása az ügynök várólistájában](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. Bontsa ki az **Advanced** (Speciális) pontot, és adja hozzá a `$(Build.SourcesDirectory)\packages` elemet a **célkönyvtárhoz**. Tartsa meg a fennmaradó alapértelmezett NuGet konfigurációs értékeket.

   ![NuGet-visszaállítási feladat konfigurálása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. Kattintson a **Phase 1** (1. fázis) szakasz **+** elemére, majd adjon hozzá egy **MSBuild**-feladatot.

   ![MSBuild-feladat hozzáadása az ügynök várólistájában](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. Cserélje az **MSBuild-argumentumokat** a következőkre:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild-feladat konfigurálása a DevOps-ben](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. Kattintson a **Phase 1** (1. fázis) szakasz **+** elemére, majd adja hozzá egy **Azure-erőforráscsoport üzembehelyezési** feladatát. 
    
    ![Azure-erőforráscsoport üzembehelyezési feladatának hozzáadása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. Bontsa ki az **Azure Details** (Azure – részletek) pontot, és töltse ki a konfiguráció mezőit a következők szerint:
    
    |**Beállítás**  |**Ajánlott érték**  |
    |---------|---------|
    |Előfizetés  |  Válassza ki az előfizetését.   |
    |Műveletek  |  Erőforráscsoport létrehozása vagy frissítése   |
    |Erőforráscsoport  |  Adja meg az erőforráscsoport nevét.   |
    |Sablon  | [Saját megoldás elérési útja]\bin\Debug\Deploy\\[Saját projekt neve].JobTemplate.json   |
    |Sablon paraméterei  | [Saját megoldás elérési útja]\bin\Debug\Deploy\\[Saját projekt neve].JobTemplate.parameters.json   |
    |Sablon paramétereinek felülbírálása  | Írja be a szövegmezőbe a felülbírálni kívánt sablonparamétereket. Például: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Ez a tulajdonság nem kötelező, de a build hibaüzeneteket eredményezhet, ha a paraméterek nincsenek felülbírálva.    |
    
    ![Az Azure erőforráscsoport-telepítés tulajdonságainak beállítása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. A buildelési folyamat teszteléséhez kattintson a **Save & Queue** (Mentés és várakozási sorba helyezés) gombra.
    
    ![Mentés és üzenetsor-létrehozás a DevOps-ben](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Sikertelen buildelési folyamat
Lehet, hogy null értékű üzembehelyezési paraméterekről szóló hibaüzenetet kap, ha nem bírálta felül a sablon paramétereit a buildelési folyamat **Azure-erőforráscsoport üzembe helyezésének** feladatában. A probléma megoldásához térjen vissza a buildelési folyamathoz, és bírálja felül a null értékű paramétereket.

   ![A DevOps Stream Analytics-létrehozási folyamata nem sikerült](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Módosítások véglegesítse és leküldése a kiadás indításához
A folyamatos integrációs folyamat működésének ellenőrzéséhez adjon be néhány kódmódosítást az Azure DevOpsba.    

A kód írása közben eszközölt módosításokat a Visual Studio automatikusan követi. A helyi Git-adattár módosításainak véglegesítéséhez válassza ki a függőben lévő módosítások ikonját, amely a képernyő jobb oldalának alján lévő állapotsoron található.

1. A Team Explorer **Changes** (Módosítások) nézetében adjon meg egy üzenetet a frissítés leírásával, majd véglegesítse a módosításokat.

    ![A tárház módosításainak véglegesítve a Visual studióból](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Válassza ki a közzé nem tett változások ikonját az állapotsávon vagy a Sync (Szinkronizálás) nézetet a Team Explorerben. A **Push** (Leküldés) elem kiválasztásával frissítheti a kódot az Azure DevOps szolgáltatásban.

    ![Változások leküldése a Visual studióból](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

Az Azure DevOps szolgáltatásba leküldött módosítások automatikusan aktiválnak egy buildet.  Ha a buildelési folyamat sikeresen befejeződött, a kiadás automatikusan létrejön, és elindítja a fürtön a feladat frissítését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a jövőben szeretné még használni a feladatot, leállíthatja, és később újraindíthatja, amikor szüksége lesz rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az oktatóanyagban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan használhatja a Visual Studio Azure Stream Analytics-eszközeit egy folyamatos integrációs és üzembehelyezési folyamat létrehozásához, folytassa az olvasást a CI/CD-folyamatot bemutató cikkel:

> [!div class="nextstepaction"]
> [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)
