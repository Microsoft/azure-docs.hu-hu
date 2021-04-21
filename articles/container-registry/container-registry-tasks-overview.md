---
title: Az ACR-feladatok áttekintése
description: A ACR-feladatok szolgáltatáscsomag, a Azure Container Registry, amely biztonságos, automatizált tárolórendszerkép-összeállítást, -felügyeletet és -javítást biztosít a felhőben.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: a42a2bfcdc1621689421940c4db2fcf4f5e64b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781000"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Tároló rendszerkép-buildek és karbantartás automatizálása a ACR-feladatok

A tárolók új virtualizálási szinteket biztosítanak, így az alkalmazások és a fejlesztők függőségei el vannak különedve az infrastruktúrától és az üzemeltetési követelményektől. Ami azonban megmarad, az az, hogy kezelni kell az alkalmazás virtualizálásának a tároló életciklusa során való kezelése és javításának a mikéntját.

## <a name="what-is-acr-tasks"></a>Mi az ACR-feladatok?

**ACR-feladatok** egy szolgáltatáscsomag a Azure Container Registry. Felhőalapú tárolórendszerkép-építést biztosít olyan platformokhoz, mint a [](#automate-os-and-framework-patching) Linux, a Windows és az ARM, és automatizálhatja a Docker-tárolók operációsrendszer- és keretrendszer-javítását. [](#image-platforms) ACR-feladatok az igény szerinti tárolórendszerkép-buildekkel kiterjeszti a "belső" fejlesztési ciklust a felhőbe, hanem lehetővé teszi a forráskódfrissítések, a tárolók alapként szolgáló rendszerképének frissítései vagy időzítők által aktivált automatikus buildeket is. Az alapként szolgáló rendszerképfrissítési eseményindítók segítségével például automatizálhatja az operációs rendszer és az alkalmazás keretrendszerének javítási munkafolyamatát, megőrizve a biztonságos környezeteket, miközben a nem módosítható tárolók alapelveit is be lehet tartani.

## <a name="task-scenarios"></a>Feladatforgatókönyvek

ACR-feladatok támogatja a tároló-rendszerképek és más összetevők építésének és karbantartásának számos forgatókönyvét. További részleteket a cikk alábbi szakaszaiban talál.

* **[Gyors feladat](#quick-task)** – Egyetlen tárolólemezkép összeállítása és leküldése egy igény szerinti tároló-beállításjegyzékbe az Azure-ban, helyi Docker Engine telepítése nélkül. Gondoljon `docker build` a `docker push` felhőre.
* **Automatikusan aktivált feladatok** – Engedélyezzen egy vagy több *eseményindítót* egy rendszerkép felépítéséhez:
  * **[Aktiválás forráskódfrissítés során](#trigger-task-on-source-code-update)** 
  * **[Aktiválás alapkéntkénti rendszerkép-frissítéssel](#automate-os-and-framework-patching)** 
  * **[Eseményindító ütemezés szerint](#schedule-a-task)** 
* **[Többlépéses feladat](#multi-step-tasks)** – Kiterjesztheti a többlépéses, többtárolós munkafolyamatokkal ACR-feladatok rendszerkép build-and-push funkcióját. 

Minden ACR-feladat rendelkezik egy társított forráskódkörnyezettel [–](#context-locations) egy tároló-rendszerkép vagy más összetevő felépítéséhez használt forrásfájlok halmazának helye. Ilyen környezet például egy Git-adattár vagy egy helyi fájlrendszer.

A tevékenységek a [](container-registry-tasks-reference-yaml.md#run-variables)változók futtatását is kihasználhatják, így újra felhasználhatja a feladatdefiníciókat, és szabványosíthatja a képek és összetevők címkéit.

## <a name="quick-task"></a>Gyors feladat

A belső ciklus fejlesztési ciklusa, amely a kódírás, az alkalmazás forráskódba való véglegesítés előtti megírásának, építésének és tesztelésének iteratív folyamata, valójában a tárolók életciklus-felügyeletének kezdete.

Mielőtt véglegesítenie kell az első kódsort, ACR-feladatok gyorsfeladat-funkciója integrált fejlesztési élményt nyújthat a tárolórendszerkép-buildek Azure-ba való kiszervezésével. [](container-registry-tutorial-quick-task.md) A gyors feladatokkal ellenőrizheti az automatizált builddefiníciókat, és még a kód véglegesítése előtt megoldhatja a lehetséges problémákat.

A jól ismert formátumot használva az Azure CLI-ben az az acr build parancs egy környezetet (a buildelni kívánt fájlokat) használ, elküldi azt az ACR-feladatok-nek, és alapértelmezés szerint leküldi a beépített rendszerképet a regisztrációs adatbázisba a `docker build` befejezéskor. [][az-acr-build] [](#context-locations)

Bevezetésért tekintse meg a [](container-registry-quickstart-task-cli.md) tároló rendszerképének a tárolókban való buildlének és futtatásának rövid Azure Container Registry.  

ACR-feladatok tároló életciklus-primitívként lett kialakítva. Például integrálja a ACR-feladatok a CI-/CD-megoldásba. Az [az login szolgáltatásnévvel][az-login] történő végrehajtásával [a][az-login-service-principal]CI/CD-megoldás kiadhat az [az acr build][az-acr-build] parancsokat a rendszerkép-buildek futtatásához.

Ismerje meg, hogyan használhatja a gyors feladatokat az első ACR-feladatok: Tároló-rendszerképek összeállítása a felhőben [a Azure Container Registry-feladatok.](container-registry-tutorial-quick-task.md)

> [!TIP]
> Ha közvetlenül a forráskódból szeretne rendszerképet összeépíteni és leküldeni Docker-fájl nélkül, a Azure Container Registry [az az acr pack build][az-acr-pack-build] parancsot biztosítja (előzetes verzió). Ez az eszköz a Cloud [Native Buildpacks](https://buildpacks.io/)használatával buildet és leküldést ad egy rendszerképet az alkalmazás forráskódból.

## <a name="trigger-task-on-source-code-update"></a>Aktiválási feladat forráskódfrissítéskor

Tároló rendszerkép-összeállítási vagy többlépéses feladat aktiválása kód vagy lekéréses kérelem végrehajtásához vagy frissítéshez nyilvános vagy privát Git-adattárba a GitHubon vagy az Azure DevOpsban. Konfigurálhat például egy összeállítási feladatot az [az acr task create][az-acr-task-create] Azure CLI-paranccsal egy Git-adattár és opcionálisan egy ág és Docker-fájl megadásával. Amikor a csapata frissíti a kódot az adattárban, egy ACR-feladatok létrehozott webhook aktiválja az adattárban meghatározott tároló-rendszerkép buildszámát. 

ACR-feladatok a következő eseményindítókat támogatja, ha git-adattárat ad meg a feladat környezeteként:

| Eseményindító | Alapértelmezés szerint engedélyezett |
| ------- | ------------------ |
| Véglegesítés | Yes |
| Lekéréses kérelem | No |

A forráskódfrissítési eseményindító konfiguráláskor személyes hozzáférési jogkivonatot (PAT) kell adnia a feladatnak, hogy beállítsa a webhookot a nyilvános vagy privát GitHub- vagy Azure DevOps-adattárban.

> [!NOTE]
> Jelenleg a ACR-feladatok nem támogatja a véglegesítési vagy lekéréses kérelmek eseményindítóit a GitHub Enterprise-adattárakban.

A következő oktatóanyagban megtudhatja, hogyan aktiválhat buildeket a forráskód véglegesítésére ACR-feladatok, a tároló rendszerkép-buildek automatizálása [a Azure Container Registry-feladatok.](container-registry-tutorial-build-task.md)

## <a name="automate-os-and-framework-patching"></a>Az operációs rendszer és a keretrendszer javításának automatizálása

A tároló ACR-feladatok-build munkafolyamatának valódi továbbfejlesztésében az a képesség áll, hogy képes észlelni az alapként szolgáló *rendszerkép frissítését.* A legtöbb tároló rendszerképének egyik funkciója, az alapként szolgáló rendszerkép egy szülő rendszerkép, amelyen egy vagy több alkalmazás-rendszerkép alapul. Az alapként használt lemezképek általában tartalmazzák az operációs rendszert, és néha alkalmazás-keretrendszereket is. 

Beállíthatja, hogy egy ACR-feladat nyomon kövesse az alapként beállított rendszerképtől való függőséget, amikor alkalmazás-rendszerképet hoz létre. Amikor lekérte a frissített alapként szolgáló rendszerképet a regisztrációs adatbázisba, vagy egy alapként szolgáló rendszerképet frissít egy nyilvános , például az Docker Hub-ban, a ACR-feladatok automatikusan felépíthet rajta alkalmazás-rendszerképeket.
Ezzel az automatikus észleléssel és újraépítéssel a ACR-feladatok időt és energiát takarít meg a frissített alapként rendszerképre hivatkozó minden egyes alkalmazás-rendszerkép manuális nyomon követéséhez és frissítéséhez.

További információ az alap [rendszerképfrissítési eseményindítókról](container-registry-tasks-base-images.md) a ACR-feladatok. A Tároló-rendszerkép-összeállítások automatizálása alapként szolgáló rendszerkép azure container registryben való frissítése esetén oktatóanyagból megtudhatja, hogyan aktiválhat rendszerkép-összeállítást, amikor egy alapként szolgáló rendszerképet leküld egy [tároló-beállításjegyzékbe](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Feladat ütemezése

Ütemezheti a feladatokat egy vagy  több időzítő eseményindító beállításával a feladat létrehozásakor vagy frissítésekkor. A feladatok ütemezése akkor hasznos, ha a tárolók számítási feladatait egy meghatározott ütemezés szerint futtatja, vagy karbantartási műveleteket vagy teszteket futtat a regisztrációs adatbázisba rendszeresen lekért rendszerképeken. Részletekért lásd: [ACR-feladat futtatása meghatározott ütemezés szerint.](container-registry-tasks-scheduled.md)

## <a name="multi-step-tasks"></a>Többlépéses feladatok

A többlépéses feladatok lépésalapú feladatdefiníciót és végrehajtást biztosítanak a tároló rendszerképének a felhőben való építéshez, teszteléshez és javításához. A YAML-fájlban meghatározott feladat [lépései egyedi](container-registry-tasks-reference-yaml.md) build- és leküldéses műveleteket határoznak meg a tároló-rendszerképekhez vagy más összetevőkhez. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

Létrehozhat például egy többlépéses feladatot, amely a következőket automatizálja:

1. Webalkalmazás-rendszerkép összeállítása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztkép összeállítása
1. A webalkalmazás-teszttároló futtatása, amely teszteket végez a futó alkalmazástárolón
1. Ha a tesztek megfelelőek, készítsen egy Helm-diagram archívumcsomagot
1. Művelet végrehajtása `helm upgrade` az új Helm-diagram archívumcsomaggal

A többlépéses feladatok lehetővé teszik, hogy a rendszerképeket több lépésből álló lépésekre ossza fel, futtatva és tesztelve, többlépéses függőségi támogatással. A többlépéses feladatoknak ACR-feladatok a rendszerkép-építés, a tesztelés, valamint az operációs rendszer és a keretrendszer javítási munkafolyamatai fölött.

További információ a többlépéses [feladatokról: Többlépéses build-, tesztelési](container-registry-tasks-multi-step.md)és javítási feladatok futtatása a ACR-feladatok.

## <a name="context-locations"></a>Környezetek helyei

Az alábbi táblázat példákat mutat be a támogatott környezeti helyekre a ACR-feladatok:

| Környezet helye | Leírás | Példa |
| ---------------- | ----------- | ------- |
| Helyi fájlrendszer | A helyi fájlrendszer könyvtárában található fájlok. | `/home/user/projects/myapp` |
| A GitHub főága | Nyilvános vagy privát GitHub-adattár fő (vagy más alapértelmezett) ágában található fájlok.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-ág | Nyilvános vagy privát GitHub-adattár adott ága.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-almappa | Nyilvános vagy privát GitHub-adattár almappában található fájlok. A példa egy ág és egy almappa specifikáció kombinációját mutatja be. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub-véglegesítés | Adott véglegesítés egy nyilvános vagy privát GitHub-adattárban. A példa egy véglegesítési kivonat (SHA) és egy almappa specifikáció kombinációját mutatja be. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps almappa | Nyilvános vagy privát Azure-adattára almappában található fájlok. A példa az ág és az almappa specifikáció kombinációját mutatja be. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Távoli tarball | Egy tömörített archívumban lévő fájlok egy távoli webkiszolgálón. | `http://remoteserver/myapp.tar.gz` |
| Összetevő a tároló-beállításjegyzékben | [OCI-összetevőfájlok](container-registry-oci-artifacts.md) egy tároló-beállításjegyzék-adattárban. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Ha privát Git-adattárat használ egy feladat környezeteként, meg kell adnia egy személyes hozzáférési jogkivonatot (PAT).

## <a name="image-platforms"></a>Rendszerképplatformok

Alapértelmezés szerint a ACR-feladatok rendszerképeket épít fel a Linux operációs rendszerhez és az amd64 architektúrához. Adja meg `--platform` a címkét, ha Windows-rendszerképeket vagy Linux-rendszerképeket hoz létre más architektúrákhoz. Adja meg az operációs rendszert és opcionálisan egy támogatott architektúrát operációs rendszer/architektúra formátumban `--platform Linux/arm` (például). ARM-architektúrák esetén igény szerint megadhat egy változatot operációs rendszer/architektúra/változat formátumban `--platform Linux/arm64/v8` (például: ):

| Operációs rendszer | Architektúra|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

Minden feladatfuttatás naplókimenetet hoz létre, amelyet megvizsgálhat annak megállapításához, hogy a tevékenység lépései sikeresen lefuttak-e. Amikor manuálisan indít el egy feladatot, a rendszer a feladat futtatásának naplókimenetét a konzolra streameli, és a későbbi lekéréshez is tárolja. Amikor egy feladat automatikusan aktiválódik, például forráskód véglegesítése vagy alapként való rendszerkép-frissítés, a rendszer csak a tevékenységnaplókat tárolja. Tekintse meg a futtatás naplóit a Azure Portal, vagy használja [az az acr task logs](/cli/azure/acr/task#az_acr_task_logs) parancsot.

További információ a [feladatnaplók megtekintéséről és kezeléséről.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Következő lépések

Ha készen áll a tároló rendszerkép-buildek automatizálni és a karbantartást a felhőben, tekintse meg a ACR-feladatok [oktatóanyag-sorozatot.](container-registry-tutorial-quick-task.md)

Ha szükséges, telepítse az [Visual Studio Code Docker-bővítményét](https://code.visualstudio.com/docs/azure/docker) és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményt az Azure-tárolóregisztrálókhoz. Rendszerképek leküldése és leküldése egy Azure Container Registrybe, vagy ACR-feladatok futtatása a Code Visual Studio belül.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
