---
title: Tároló alkalmazás üzembe helyezése CI/CD-vel
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos integrációt és üzembe helyezést egy Azure Service Fabric Container-alkalmazáshoz a Visual Studio Azure DevOps használatával.
ms.topic: tutorial
ms.date: 08/29/2018
ms.custom: mvc
ms.openlocfilehash: bb0eb9226a99f139ff10a8da12a1e22017536c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018851"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Oktatóanyag: Tárolóalkalmazás üzembe helyezése Service Fabric-fürtön CI/CD használatával

Ez az oktatóanyag egy sorozat második része, amely leírja, hogyan állíthat be folyamatos integrációt és üzembe helyezést egy Azure Service Fabric Container alkalmazáshoz a Visual Studio és az Azure DevOps használatával.  Szükség van egy már meglévő Service Fabric-alkalmazásra, így példaként a [Windows-tárolóban lévő .NET-alkalmazás telepítése Azure Service Fabricre](service-fabric-host-app-in-a-container.md) című szakaszban létrehozott alkalmazás szolgál.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Forráskezelés hozzáadása a projekthez
> * Build definíció létrehozása a Visual Studióban Team Explorer
> * Kiadási definíció létrehozása a Visual Studióban Team Explorer
> * Alkalmazás automatikus üzembe helyezése és frissítése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Rendelkeznie kell egy fürttel az Azure-on, vagy [létre kell hoznia egyet ennek az oktatóanyagnak a segítségével](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Üzembe kell helyeznie hozzá egy tárolóba helyezett alkalmazást](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Közzétételi profil előkészítése

Most, hogy [üzembe helyezte a tárolóalkalmazást](service-fabric-host-app-in-a-container.md), minden készen áll a folyamatos integráció beállításához.  Először készítsen elő egy közzétételi profilt az alkalmazáson belül az Azure DevOpsban végrehajtott üzembehelyezési folyamat számára.  A közzétételi profilt úgy kell konfigurálni, hogy a korábban már létrehozott fürtöt célozza.  Indítsa el a Visual Studiót, és nyisson meg egy már meglévő Service Fabric-alkalmazásprojektet.  A **Megoldáskezelőben** kattintson a jobb gombbal az alkalmazásra, majd válassza a **Publish...** (Közzététel) lehetőséget.

Az alkalmazásprojektben válasszon ki egy célprofilt a folyamatos integráció munkafolyamata számára. Ilyen lehet például a Cloud.  Adja meg a fürt csatlakozási végpontját.  Jelölje be az **Upgrade the Application** (Alkalmazás frissítése) jelölőnégyzetet, hogy az alkalmazás az Azure DevOpsban levő összes üzemelő példány esetében frissüljön.  A **Save** (Mentés) hiperhivatkozásra kattintva mentse a beállításokat a közzétételi profilba, majd kattintson a **Cancel** (Mégse) gombra a párbeszédpanel bezárásához.

![Leküldéses profil][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>A Visual Studio-megoldás megosztása egy új Azure DevOps Git-adattárban

Az alkalmazás forrásfájljait megoszthatja az Azure DevOps, így létrehozhat buildeket.

Hozzon létre egy új helyi git-tárházat a projekthez a Visual Studio jobb alsó sarkában lévő állapotsorban a **Hozzáadás a forrás vezérlőelemhez**  ->  **git** elemre kattintva.

A **Team Explorer****Push** (Leküldés) nézetében válassza ki a **Push to Azure DevOps** (Leküldés Azure DevOps szolgáltatásba) alatt található **Publish Git Repo** (Git-adattár közzététele) gombot.

![Képernyőkép a Visual Studióban található Team Explorer-szinkronizációs ablakról. A leküldése az Azure-ba DevOps területen ki van emelve a közzététel a git-tárházban gomb.][push-git-repo]

Ellenőrizze az e-maileket, és válassza ki a szervezetét a **fiók** legördülő menüből. Előfordulhat, hogy be kell állítania egy szervezetet, ha még nem rendelkezik ilyennel. Adja meg az adattár nevét, majd válassza ki a **Publish repository** (Adattár közzététele) lehetőséget.

![Képernyőkép a leküldéses Azure DevOps ablakról. A rendszer kijelöli az e-mailek, a fiók, a tárház neve és az adattár közzététele gomb beállításait.][publish-code]

Az adattár közzétételével egy új csoportprojekt jön létre a fiókjában a helyi adattáréval azonos néven. Ha egy már meglévő csapatprojektben kíván adattárat létrehozni, az **Adattár** neve mellett kattintson az **Advanced** (Speciális) elemre, és válassza ki a csoportprojektet. A kód megtekintéséhez a weben válassza a **See it on the web** (Megtekintés a weben) lehetőséget.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Folyamatos teljesítés konfigurálása az Azure-folyamatokkal

Az Azure DevOps-Build definíciója egy olyan munkafolyamatot ír le, amely egymás után végrehajtott összeállítási lépésekből áll. Hozzon létre egy builddefiníciót, amely létrehozza a Service Fabric-alkalmazáscsomagot és más összetevőket egy Service Fabric-fürtben való üzembe helyezéshez. További információ az Azure DevOps [Build-definíciókkal](https://www.visualstudio.com/docs/build/define/create)kapcsolatban. 

Az Azure DevOps kiadási definíciója olyan munkafolyamatot ír le, amely egy alkalmazáscsomag központi telepítését végzi a fürtön. Együttes használatuk esetén a builddefiníció és a kiadási definíció a teljes munkafolyamatot végrehajtja, a forrásfájloktól kezdve a fürtön futó alkalmazásig bezárólag. További információ az Azure DevOps [kiadási definíciókkal](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)kapcsolatban.

### <a name="create-a-build-definition"></a>Builddefiníció létrehozása

Nyissa meg az új csapat projektjét, és navigáljon a https://dev.azure.com webböngészőben, és válassza ki a szervezetét, majd az új projektet. 

Válassza a **folyamatok** lehetőséget a bal oldali panelen, majd kattintson az **új folyamat** elemre.

>[!NOTE]
>Ha nem látja a builddefiníciós sablont, ellenőrizze, hogy a **New YAML pipeline creation experience** (Új YAML-folyamat létrehozása) funkció ki van-e kapcsolva. Ez a funkció a DevOps-fiók **Preview Features** (Előzetes verziójú funkciók) szakaszában konfigurálható.

![Új folyamat][new-pipeline]

Válassza az **Azure Repos git** forrásként, a csapat projekt neve, a projekt tárháza és a **fő** alapértelmezett ág vagy manuális és ütemezett buildek lehetőséget.  Ezután kattintson a **Continue** (Folytatás) gombra.

A **Select a template** (Sablon kiválasztása) területen válassza az **Azure Service Fabric application with Docker support** (Azure Service Fabric-alkalmazás Docker-támogatással) sablont, majd kattintson az **Apply** (Alkalmaz) gombra.

![Buildsablon kiválasztása][select-build-template]

A **Tasks** (Feladatok) között az **Agent pool** (Ügynökkészlet) megfelelőjeként válassza ki a következőt: **Hosted VS2017**.

![Feladatok kiválasztása][task-agent-pool]

Kattintson a **Képek címkézése** elemre.

A **Container Registry Type** (Tárolóregisztrációs adatbázis típusa) alatt válassza ki az **Azure Container Registry** elemet. Válasszon ki egy **Azure-előfizetést**, majd kattintson az **Authorize** (Engedélyezés) gombra. Válasszon ki egy **Azure-beli tárolóregisztrációs adatbázist**.

![A Docker Tag rendszerképek kiválasztása][select-tag-images]

Kattintson a **Push images** (Képek leküldése) elemre.

A **Container Registry Type** (Tárolóregisztrációs adatbázis típusa) alatt válassza ki az **Azure Container Registry** elemet. Válasszon ki egy **Azure-előfizetést**, majd kattintson az **Authorize** (Engedélyezés) gombra. Válasszon ki egy **Azure-beli tárolóregisztrációs adatbázist**.

![A Docker Push rendszerképek kiválasztása][select-push-images]

Az **Eseményindítók** lapon engedélyezze a folyamatos integrációt a **folyamatos integráció engedélyezése** ellenőrzésével. A **Branch filters** (Ágszűrők) területen kattintson az **+ Add** (+ Hozzáadás) elemre, és az alapértelmezés szerint a **Branch specification** (Ágspecifikáció) **master** lesz.

A build manuális elindításához a **Save build pipeline and queue dialog** (A buildfolyamat és az üzenetsor párbeszédpanelének mentése) menüben kattintson a **Save & queue** (Mentés és üzenetsorba helyezés) elemre.

![Eseményindítók kiválasztása][save-and-queue]

A buildek leküldés vagy bejelentkezés hatására is aktiválódnak. A létrehozási folyamat ellenőrzéséhez váltson a builds ( **buildek** ) lapra.  Miután meggyőződött arról, hogy a Build sikeresen lefut, adjon meg egy kiadási definíciót, amely üzembe helyezi az alkalmazást egy fürtön.

### <a name="create-a-release-definition"></a>Kiadási definíció létrehozása

Válassza a **folyamatok** lehetőséget a bal oldali panelen, majd a **releases**, majd az **+ új folyamat** elemet.  A **Select a template** (Sablon kiválasztása) területen válassza ki az **Azure Service Fabric Deployment** (Üzembe helyezés az Azure Service Fabric használatával) sablont a listából, majd kattintson az **Apply** (Alkalmaz) gombra.

![Kiadási sablon kiválasztása][select-release-template]

Válassza ki a **Tasks** (Feladatok), az **Environment 1** (1. környezet), majd a **+New** (+Új) elemet az új fürtkapcsolat hozzáadásához.

![Fürtkapcsolat hozzáadása][add-cluster-connection]

Az **Add new Service Fabric Connection** (Új Service Fabric-kapcsolat hozzáadása) nézetben válassza a **Certificate Based** (Tanúsítványalapú) vagy az **Azure Active Directory** hitelesítést.  A kapcsolat neve legyen „mysftestcluster”, a fürt végpontja pedig „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000” (vagy az a fürtvégpont, ahová a telepítés történik).

A tanúsítványalapú hitelesítéshez adja meg a fürt létrehozásához használt kiszolgáló tanúsítványának **kiszolgálói tanúsítvány-ujjlenyomatát**.  Az **ügyféltanúsítvány** esetében adja meg az ügyféltanúsítvány-fájl base-64 kódolását. A tanúsítvány base-64 kódolású megfelelőjének beszerzésével kapcsolatos információk a mező felugró súgóablakában találhatók. Adja meg a tanúsítványhoz tartozó **jelszót** is.  Ha nem rendelkezik külön ügyféltanúsítvánnyal, a fürt vagy a kiszolgáló tanúsítványát is használhatja.

Azure Active Directory hitelesítő adatok esetében adja meg a fürt létrehozásakor használt kiszolgálói tanúsítvány **kiszolgálói tanúsítvány-ujjlenyomatát**, illetve a fürthöz történő csatlakozáshoz használni kívánt hitelesítő adatokat a **felhasználónév** és a **jelszó** mezőkben.

Kattintson az **Add** (Hozzáadás) gombra a fürtkapcsolat mentéséhez.



Az Agent Phase (Ügynöki fázis) alatt kattintson a **Deploy Service Fabric Application** (Service Fabric-alkalmazás üzembe helyezése) elemre.
Kattintson a **Docker Settings** (Dockerbeállítások), majd a **Configure Docker settings** (Dockerbeállítások konfigurálása) gombra. A **Registry Credentials Source** (A regisztrációs adatbázis hitelesítő adatainak forrása) területen válassza az **Azure Resource Manager Service Connection** (Azure Resource Manager szolgáltatáskapcsolat) elemet. Válassza ki az **Azure-előfizetését**.

![A kibocsátási folyamat ügynöke][release-pipeline-agent]

Ezután adja hozzá a buildösszetevőt a folyamathoz, hogy a kiadási definíció megtalálhassa a build kimenetét. Válassza a **Pipeline** (Folyamat), majd az **Artifacts**->**+Add** (Összetevők > +Hozzáadás) lehetőséget.  A **Source (Build definíció)** (Forrás (builddefiníció)) területen válassza ki a korábban létrehozott builddefiníciót.  Kattintson az **Add** (Hozzáadás) gombra a buildösszetevő mentéséhez.

![Összetevő hozzáadása][add-artifact]

Engedélyezze a folyamatos üzembe helyezés eseményindítóját, hogy a kiadás automatikusan létrejöjjön a build elkészültekor. Az összetevőnél kattintson a villám ikonra, engedélyezze az eseményindítót, majd kattintson a **Save** (Mentés) elemre a kiadási definíció mentéséhez.

![Eseményindító engedélyezése][enable-trigger]

A kiadás létrehozásához válassza a **+ kiadás**  ->  **Létrehozás egy kiadás**  ->  **létrehozása** lehetőséget. A kiadási folyamatot a **Releases** (Kiadások) lapon követheti nyomon.

Győződjön meg arról, hogy az üzembe helyezés sikeres volt, és ellenőrizze, hogy az alkalmazás megfelelően fut-e a fürtön.  Nyisson meg egy webböngészőt, és lépjen a `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/` lapra.  Vegye figyelembe, hogy a példában szereplő alkalmazásverzió száma „1.0.0.20170616.3”.

## <a name="commit-and-push-changes-trigger-a-release"></a>Módosítások véglegesítse és leküldése, kiadás indítása

A folyamatos integrációs folyamat működésének ellenőrzéséhez adjon be néhány kódmódosítást Azure DevOpsba.

A kód írása közben eszközölt módosításokat a Visual Studio automatikusan követi. A helyi Git-adattár módosításainak véglegesítéséhez válassza ki a függőben lévő módosítások ikonját (![A függőben lévő módosítások ikon egy ceruzát és egy számot jelenít meg.][pending]), amely a képernyő jobb oldalának alján lévő állapotsávon található.

A Team Explorer **Changes** (Módosítások) nézetében adjon meg egy üzenetet a frissítés leírásával, majd véglegesítse a módosításokat.

![Az összes véglegesítése][changes]

Válassza ki a közzé nem tett változások ikonját az állapotsávon (![Unpublished changes][unpublished-changes]) vagy a Sync (Szinkronizálás) nézetet a Team Explorerben. A **Push** (Leküldés) elem kiválasztásával frissítheti a kódot az Azure DevOps szolgáltatásban.

![Módosítások leküldése][push]

Az Azure DevOps szolgáltatásba leküldött módosítások automatikusan aktiválnak egy buildet.  Ha a builddefiníció sikeresen befejeződött, a kiadás automatikusan létrejön, és elindítja a fürtön az alkalmazás frissítését.

A build folyamatának ellenőrzéséhez váltson át a Visual Studio **Team Explorer** területének **Builds** (Buildek) lapjára.  Miután meggyőződött arról, hogy a build végrehajtása sikeresen megtörtént, hozza létre a kiadási definíciót, amely telepíti az alkalmazást egy fürtre.

Győződjön meg arról, hogy az üzembe helyezés sikeres volt, és ellenőrizze, hogy az alkalmazás megfelelően fut-e a fürtön.  Nyisson meg egy webböngészőt, és lépjen a `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/` lapra.  Vegye figyelembe, hogy a példában szereplő alkalmazásverzió száma „1.0.0.20170815.3”.

![Képernyőkép a Service Fabric Explorer szavazási alkalmazásról. Az Essentials (alapok) lapon ki van emelve az alkalmazás "1.0.0.20170815.3" verziója.][sfx1]

## <a name="update-the-application"></a>Az alkalmazás frissítése

Módosítsa a kódokat az alkalmazásban.  Az előző lépések mentén mentse és véglegesítse a módosításokat.

Az alkalmazás frissítésének megkezdése után a frissítési folyamat állapotát a Service Fabric Explorerben lehet megtekinteni:

![Képernyőkép a Service Fabric Explorer szavazási alkalmazásról. A "verziófrissítés folyamatban" üzenet ki van emelve, és az alkalmazás állapota "verziófrissítés".][sfx2]

Az alkalmazásfrissítés több percig is eltarthat. Ha a frissítés befejeződött, az alkalmazás a következő verziót futtatja majd.  Ebben a példában ez az „1.0.0.20170815.4”-es.

![Képernyőkép a Service Fabric Explorer szavazási alkalmazásról. Az Essentials (alapok) lapon a frissített "1.0.0.20170815.4" verziójú alkalmazás van kiemelve.][sfx3]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Forráskezelés hozzáadása a projekthez
> * Builddefiníció létrehozása
> * Kiadási definíció létrehozása
> * Alkalmazás automatikus üzembe helyezése és frissítése

Az oktatóanyag következő részéből megtudhatja, hogyan állíthat be [figyelést a tárolójához](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
