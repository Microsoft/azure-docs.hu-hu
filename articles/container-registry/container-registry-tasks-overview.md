---
title: Az ACR-feladatok áttekintése
description: Az ACR-feladatok, a Azure Container Registry szolgáltatásainak egyik csomagja, amely biztonságos, automatizált tároló-rendszerképek készítését, kezelését és javítását teszi lehetővé a felhőben.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 1cf9faf934cebfb5abe0d2e1b26ffd7da2d6c549
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606786"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>A tárolók rendszerképének fejlesztése és karbantartása ACR-feladatokkal

A tárolók új virtualizációs szinteket biztosítanak, az alkalmazások és a fejlesztői függőségek elkülönítését az infrastruktúra és az üzemeltetési követelmények alapján. A továbbra is szükség van arra, hogy az alkalmazás virtualizálása hogyan legyen kezelve, és hogyan történik a tároló életciklusa.

## <a name="what-is-acr-tasks"></a>Mi az az ACR-feladat?

Az **ACR-feladatok** a Azure Container Registryon belüli szolgáltatások egyik csomagja. Felhőalapú tároló-rendszerképeket kínál a platformok, például a Linux, a Windows és az ARM [platformokhoz](#image-platforms) , és automatizálja az [operációs rendszer és a keretrendszer javítását](#automate-os-and-framework-patching) a Docker-tárolók számára. Az ACR-feladatok nem csupán a "belső hurkos" fejlesztési ciklust bővítik a felhőre az igény szerinti tárolók rendszerképének létrehozásával, hanem lehetővé teszik a forráskód frissítései által aktivált automatizált buildek, a tárolók alaprendszerképének vagy időzítőinak frissítését is. Például az alaprendszerkép-frissítési eseményindítók segítségével automatizálhatja az operációs rendszer és az alkalmazás-keretrendszer javítási munkafolyamatát, és megőrizheti a biztonságos környezeteket, miközben betartja a nem változtatható tárolók alapelveit.

## <a name="task-scenarios"></a>Feladatok forgatókönyvei

Az ACR-feladatok több forgatókönyvet is támogatnak a tároló-lemezképek és egyéb összetevők kiépítéséhez és karbantartásához. A részletekért tekintse meg a cikk következő részeit.

* **[Gyors feladat](#quick-task)** – egyetlen tároló-rendszerkép létrehozása és leküldése igény szerint egy tároló-beállításjegyzékbe az Azure-ban anélkül, hogy helyi Docker-motort kellene telepítenie. Szerintem `docker build` `docker push` a felhőben.
* **Automatikusan aktivált feladatok** – egy vagy több *eseményindító* létrehozása rendszerkép létrehozásához:
  * **[A forráskód frissítésének indítása](#trigger-task-on-source-code-update)** 
  * **[Aktiválás az alapszintű rendszerkép frissítése során](#automate-os-and-framework-patching)** 
  * **[Indítás ütemezett](#schedule-a-task)** 
* **[Többlépéses feladat](#multi-step-tasks)** – a több lépésből álló, többtárolós alapú munkafolyamatokkal kiterjesztheti az ACR-feladatok egyetlen Rendszerképbeli felépítését és leküldési képességeit. 

Minden ACR-feladathoz tartozik egy társított [forráskód-környezet](#context-locations) – egy tároló-rendszerkép vagy más összetevő létrehozásához használt forrásfájlok helye. Például a környezetek git-tárházat vagy helyi fájlrendszert tartalmaznak.

A feladatok a [futtatási változók](container-registry-tasks-reference-yaml.md#run-variables)előnyeit is kihasználhatják, így felhasználhatja a feladatokhoz és az összetevőkhöz tartozó feladatokhoz tartozó és szabványosított címkéket.

## <a name="quick-task"></a>Gyors feladat

A belső hurok fejlesztési ciklusa, az alkalmazás kódjának írásához, kiépítéséhez és teszteléséhez szükséges iterációs folyamat valóban a tároló életciklus-felügyeletének kezdete.

Mielőtt elvégezte a kód első sorát, az ACR-feladatok [gyors feladatával](container-registry-tutorial-quick-task.md) integrált fejlesztési élmény biztosítható a tároló rendszerképének az Azure-ba való kiszervezésével. A gyors feladatokkal ellenőrizheti az automatizált Build-definíciókat, és elvégezheti a lehetséges problémákat a kód véglegesítése előtt.

A megszokott formátum használatával az `docker build` Azure CLI-ben található az [ACR Build][az-acr-build] parancs egy [kontextust](#context-locations) (a felépített fájlok készletét), elküldi az ACR-feladatoknak, és alapértelmezés szerint leküldi a létrehozott rendszerképet a beállításjegyzékbe a befejezés után.

Bevezetésért tekintse meg a Azure Container Registryban található [tároló rendszerképének elkészítéséhez és futtatásához](container-registry-quickstart-task-cli.md) szükséges rövid útmutatót.  

Az ACR-feladatok tároló-életciklus primitívek. Például integrálhatja az ACR-feladatokat a CI/CD-megoldásba. Ha az [az login][az-login] [szolgáltatást egy egyszerű szolgáltatással][az-login-service-principal]hajtja végre, akkor a CI/CD-megoldás ezután kiadhatja [az ACR Build][az-acr-build] -parancsokat a rendszerkép-buildek elindításához.

Megtudhatja, hogyan használhatja a gyors feladatokat az első ACR-feladatok oktatóanyagban, [Azure Container Registry feladatokkal felépítheti a Felhőbeli tároló lemezképeit](container-registry-tutorial-quick-task.md).

> [!TIP]
> Ha közvetlenül a forráskódból szeretne létrehozni és leküldeni egy képet, Docker nélkül, Azure Container Registry biztosítja az az [ACR Pack Build][az-acr-pack-build] parancsot (előzetes verzió). Ez az eszköz létrehoz egy rendszerképet, és leküldi az alkalmazás forráskódját a [felhőalapú natív Buildpacks](https://buildpacks.io/)használatával.

## <a name="trigger-task-on-source-code-update"></a>Feladat elindítása a forráskód frissítésekor

Hozzon létre egy tároló-rendszerkép Build vagy többlépéses feladatot a kód véglegesítése, vagy egy lekéréses kérelem vagy frissítés után egy nyilvános vagy privát git-tárházban a GitHubon vagy az Azure DevOps. Például konfiguráljon egy Build feladatot az Azure CLI-parancs az [ACR Task Create][az-acr-task-create] paranccsal egy git-tárház és opcionálisan egy ág-és Docker megadásával. Amikor a csapat frissíti a tárházban lévő kódot, az ACR-feladatok által létrehozott webhook elindítja a tárházban definiált tároló-rendszerkép összeállítását. 

Az ACR-feladatok a következő eseményindítókat támogatják, amikor a git-tárházat a feladat kontextusa szerint állítja be:

| Eseményindító | Alapértelmezés szerint engedélyezett |
| ------- | ------------------ |
| Véglegesítés | Yes |
| Lekéréses kérelem | No |

A forráskód-frissítési trigger konfigurálásához meg kell adnia a feladatot egy személyes hozzáférési jogkivonat (PAT) számára, hogy a webhookot a nyilvános vagy privát GitHub vagy az Azure DevOps-tárházban állítsa be.

> [!NOTE]
> Az ACR-feladatok jelenleg nem támogatják a commit vagy a pull kérelem eseményindítóit a GitHub Enterprise reposban.

Ismerje meg, hogyan indíthat triggert forráskód-végrehajtásra a második ACR-feladatok oktatóanyagban, és [automatizálja a tárolók rendszerképét Azure Container Registry feladatokkal](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Az operációs rendszer és a keretrendszer javításának automatizálása

Az ACR-feladatok hatékonyságának növelése a tároló-Build munkafolyamattal való hatékony feladatokból származik, és képes az *alaprendszerkép* frissítésének észlelésére. A legtöbb tároló lemezképének egyik funkciója, az alaprendszerkép egy szülő lemezkép, amelyen egy vagy több alkalmazás-lemezkép alapul. Az alaplemezképek általában tartalmazzák az operációs rendszert, és esetenként az alkalmazás-keretrendszereket is. 

Beállíthat egy ACR-feladatot, amellyel nyomon követheti az alaprendszerképtől való függőséget, amikor létrehoz egy alkalmazás rendszerképét. Ha a frissített alaplemezképet leküldi a beállításjegyzékbe, vagy egy alaprendszerkép frissül egy nyilvános tárházban (például a Docker hub-ban), az ACR-feladatok automatikusan létrehoznak egy alkalmazás-lemezképet az alapján.
Ezzel az Automatikus észleléssel és újjáépítéssel az ACR-feladatok elmentik azt az időt és fáradságot, amelyet a rendszer általában a frissített alaprendszerképre hivatkozó minden egyes rendszerkép manuális nyomon követéséhez és frissítéséhez szükséges.

További információ az ACR-feladatokhoz tartozó [alaprendszerkép-frissítési eseményindítókkal](container-registry-tasks-base-images.md) kapcsolatban. És megtudhatja, hogyan indíthat el egy rendszerképet, ha alaprendszerképet küld egy tároló-beállításjegyzékbe az oktatóanyag automatizálható tároló-rendszerképének létrehozásakor, [Amikor egy alaprendszerkép frissül egy Azure Container registryben](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Feladat ütemezése

A feladat létrehozásakor vagy frissítésekor egy vagy több *időzítő eseményindító* beállításával is ütemezhet egy feladatot. A feladatok ütemezése hasznos lehet a tároló számítási feladatainak meghatározott ütemezés szerinti futtatásához, illetve karbantartási műveletek vagy tesztek futtatására a beállításjegyzékbe rendszeresen leküldve. Részletekért lásd: [ACR-feladat futtatása meghatározott ütemterven](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Többlépéses feladatok

A többlépéses tevékenységek a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához szükséges lépéseken alapuló feladatok meghatározását és végrehajtását teszik lehetővé. A [YAML-fájlokban](container-registry-tasks-reference-yaml.md) meghatározott feladatok lépései egyedi Build-és leküldéses műveleteket határoznak meg a tároló lemezképei vagy más összetevői számára. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

Létrehozhat például egy több lépésből álló feladatot, amely automatizálja a következőket:

1. Webalkalmazás-rendszerkép összeállítása
1. A webalkalmazás-tároló futtatása
1. Webalkalmazás-tesztelési rendszerkép összeállítása
1. Futtassa a webalkalmazás-teszt tárolót, amely teszteket hajt végre a futó alkalmazás tárolóján.
1. Ha a tesztek sikeresek, hozzon létre egy Helm diagram archiválási csomagot
1. `helm upgrade`Az új Helm chart Archive csomag használata

A többlépéses feladatok lehetővé teszik a rendszerképek kiépítése, futtatása és tesztelése több megkomponálható lépésre, a lépésenkénti függőségek támogatásával. Az ACR-feladatok több lépésből álló feladatai esetében részletesebben szabályozható a rendszerképek kiépítése, tesztelése, valamint az operációs rendszer és a keretrendszer javítási folyamatai.

Ismerje meg a többlépéses feladatokat a [több lépésből álló Build-, tesztelési és javítási feladatok futtatása az ACR-feladatokban](container-registry-tasks-multi-step.md)című témakörben.

## <a name="context-locations"></a>Környezet helyei

A következő táblázat példákat mutat be az ACR-feladatok támogatott környezeti helyeire:

| Környezet helye | Leírás | Példa |
| ---------------- | ----------- | ------- |
| Helyi fájlrendszer | A helyi fájlrendszer egyik könyvtárában található fájlok. | `/home/user/projects/myapp` |
| GitHub fő ága | Egy nyilvános vagy privát GitHub-tárház fő (vagy más alapértelmezett) ágában található fájlok.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-ág | Nyilvános vagy privát GitHub-tárház adott ága.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-almappa | Egy nyilvános vagy privát GitHub-tárház almappájába tartozó fájlok. Példa egy ág és almappa specifikációjának kombinációját mutatja be. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub-véglegesítés | Konkrét véglegesítés nyilvános vagy privát GitHub-tárházban. Példa a commit hash (SHA) és az almappák specifikációjának kombinációját mutatja be. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps almappa | Egy nyilvános vagy privát Azure-tárház almappájába tartozó fájlok. Példa az ág és az almappa specifikációjának kombinációját mutatja be. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Távoli Fez | Tömörített archívumban található fájlok egy távoli webkiszolgálón. | `http://remoteserver/myapp.tar.gz` |
| Összetevő a tároló beállításjegyzékében | [OCI](container-registry-oci-artifacts.md) egy tároló beállításjegyzék-adattárában. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Ha privát git-tárházat használ egy tevékenység környezetében, meg kell adnia egy személyes hozzáférési jogkivonatot (PAT).

## <a name="image-platforms"></a>Képplatformok

Alapértelmezés szerint az ACR-feladatok lemezképeket készítenek a Linux operációs rendszerhez és az AMD64 architektúrához. Határozza meg a `--platform` címkét, hogy Windows-lemezképeket vagy Linux-rendszerképeket hozzon létre más architektúrák számára. Itt adhatja meg az operációs rendszer és az architektúra (például) által támogatott architektúrát `--platform Linux/arm` . ARM architektúrák esetén opcionálisan megadhatja az operációs rendszer/architektúra/Variant formátumú változatot (például `--platform Linux/arm64/v8` ):

| Operációs rendszer | Architektúra|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

Minden feladat futtatásakor a rendszer naplózza a napló kimenetét, amely alapján megállapíthatja, hogy a feladat lépései sikeresen futottak-e. Ha a feladatot manuálisan indítja el, a rendszer a feladat futtatásának naplózását továbbítja a konzolon, és a későbbi lekéréshez is tárolja. Ha egy feladat automatikusan aktiválódik, például a forráskód-végrehajtás vagy egy alaprendszerkép frissítése esetén, a rendszer csak a feladat naplóit tárolja. Tekintse meg a futtatási naplókat a Azure Portal, vagy használja az az [ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) parancsot.

További információ a [naplók megtekintéséről és kezeléséről](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Következő lépések

Ha készen áll a tárolók rendszerképének és a Felhőbeli karbantartásának automatizálására, tekintse meg az [ACR feladatok oktatóanyag-sorozatát](container-registry-tutorial-quick-task.md).

Telepítse a [Docker-bővítményt a Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) -hoz, és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményét az Azure-beli tároló-beállításjegyzékkel való együttműködéshez. Lemezképek lekérése és leküldése egy Azure Container registrybe, vagy az ACR-feladatok futtatása a Visual Studio Code-ban.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
