---
title: Alapként való rendszerkép-frissítések – Feladatok
description: Megismerheti az alkalmazástároló-rendszerképek alapként szolgáló rendszerképét, valamint azt, hogy az alapként szolgáló rendszerképek frissítése hogyan aktiválhat Azure Container Registry feladatot.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: fc501cc1db654c11675e5a4f0d19a5a56b63a165
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781180"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Az alapként ACR-feladatok

Ez a cikk háttérinformációt nyújt az alkalmazás alapként vonatkozó rendszerképének frissítésről, és arról, hogy ezek a frissítések hogyan aktiválnak Azure Container Registry feladatot.

## <a name="what-are-base-images"></a>Mik azok az alapként használható rendszerképek?

A legtöbb tároló rendszerképet meghatározó Docker-fájlok egy szülő rendszerképet határoznak meg, amelyből a rendszerkép alapul. Ezt gyakran alapként *szolgáló rendszerképnek is nevezik.* Az alapként szolgáló rendszerképek általában az operációs rendszert tartalmazzák (például [Alpine Linux][base-alpine] vagy [Windows Nano Server][base-windows]), amelyre a tároló többi szintje alkalmazva lesz. Alkalmazás-keretrendszereket is tartalmazhatnak, például a [Node.js][base-node] vagy a [.NET Core][base-dotnet] keretrendszert. Ezek az alapként szolgáló rendszerképek általában nyilvános, felfelé irányuló rendszerképeken alapulnak. Előfordulhat, hogy számos alkalmazáskép közös alapként használt rendszerképet tartalmaz.

A rendszerképek kezelői gyakran frissítik az alapként szolgáló rendszerképet, hogy új szolgáltatásokkal és javításokkal bővítsék a rendszerképben található operációs rendszert vagy keretrendszert. A biztonsági javítások jelentik az alapként szolgáló rendszerkép frissítésének egy másik gyakori okát. A fenti frissítések előfordulásakor frissítenie kell az alapként használható rendszerképeket is, hogy tartalmazzák a kritikus javítást. Ezután minden alkalmazás-rendszerképet újra kell építeni, hogy tartalmazza az alapként létrehozott rendszerképbe már beleértő, felfelé irányuló javításokat.

Bizonyos esetekben, például egy privát fejlesztőcsapatnál az alapként megadott rendszerkép több operációs rendszert vagy keretrendszert is megadhat. Az alapként való rendszerkép lehet például egy megosztott szolgáltatás-összetevő rendszerképe, amit nyomon kell követni. Előfordulhat, hogy a csapat tagjainak nyomon kell követnie ezt az alapként használható rendszerképet a teszteléshez, vagy rendszeresen frissítenie kell a rendszerképet az alkalmazás-rendszerképek fejlesztésekor.

## <a name="maintain-copies-of-base-images"></a>Az alapként használható rendszerképek másolatának karbantartása

Javasoljuk, hogy a beállításjegyzékek minden olyan tartalma, amely nyilvános beállításjegyzékben (például Docker Hub) karbantartott alaptartalomtól függ, javasoljuk, hogy a tartalmat egy Azure Container Registrybe vagy egy másik privát beállításjegyzékbe másolja. Ezután a privát alapként használható rendszerképekre való hivatkozással győződjön meg arról, hogy az alkalmazás-rendszerképeket felépíti. Azure Container Registry lehetővé [teszi](container-registry-import-images.md) a képek importálását, hogy könnyedén másolhat tartalmakat a nyilvános beállításregisztrálókból vagy más Azure-beli tárolóregisztrálókból. A következő szakasz azt ismerteti, hogyan ACR-feladatok az alapként használt rendszerkép-frissítések nyomon követésére az alkalmazásfrissítések kiépítésekor. Az alapként szolgáló rendszerkép-frissítéseket a saját Azure-beli tárolóregisztrálóiban és opcionálisan a upstream nyilvános regisztrációs adatbázisában is nyomon követheti.

## <a name="track-base-image-updates"></a>Alapként szükséges rendszerkép-frissítések nyomon követése

Az ACR Tasks lehetővé teszi a rendszerképek automatikus összeállítását a tárolók alapként szolgáló rendszerképének frissítésekor. Ezzel a képességgel megőrizheti és frissítheti a nyilvános alapként szolgáló rendszerképek másolatát az Azure-beli tárolóregisztrálókban, majd újraépítheti az alapként szolgáló rendszerképektől függő alkalmazás-rendszerképeket.

ACR-feladatok a rendszerkép alapfüggőségeit, amikor tároló-rendszerképet hoz létre. Ennek eredményeképpen képes észlelni, ha egy alkalmazáskép alapként kapott rendszerképe frissül. Egy előre konfigurált összeállítási feladattal a ACR-feladatok automatikusan újraépítheti az alapként beállított rendszerképre hivatkozó összes alkalmazás-rendszerképet. Ezzel az automatikus észleléssel és újraépítéssel a ACR-feladatok időt és energiát takarít meg a frissített alapként rendszerképre hivatkozó összes alkalmazás-rendszerkép manuális nyomon követéséhez és frissítéséhez.

## <a name="base-image-locations"></a>Alapként használható rendszerképhelyek

A Dockerfile-ból származó rendszerképek buildelése esetén az ACR-feladat a következő helyeken észleli az alapként megadott rendszerképek függőségeit:

* Ugyanaz az Azure Container Registry, amelyben a feladat fut
* Egy másik privát Azure Container Registry ugyanabban vagy egy másik régióban 
* Egy nyilvános Docker Hub 
* Egy nyilvános Microsoft Container Registry

Ha az utasításban megadott alapként megadott rendszerkép ezen helyek egyikén található, az ACR-feladat hozzáad egy hookot, amely biztosítja a rendszerkép újraépítését az alap frissítése `FROM` után.

## <a name="base-image-notifications"></a>Alapként kapott rendszerkép-értesítések

Az alapkéntkénti rendszerkép frissítése és a függő feladat aktiválása közötti idő az alapként való rendszerkép helyétől függ:

* Alapként használható rendszerképek egy nyilvános adattárból Docker Hub vagy **MCR-ben** – A nyilvános adattárakban található alapként megadott rendszerképeknél az ACR-feladat 10 és 60 perc közötti véletlenszerű időközönként ellenőrzi a képfrissítéseket. A függő tevékenységek ennek megfelelően futnak.
* **Alapként szolgáló** rendszerképek egy Azure Container Registryből – Az Azure container registrykben található alapként szolgáló rendszerképek esetén az ACR-feladatok azonnal elindítják a futtatásokat az alapként szolgáló rendszerkép frissítésekor. Az alapként felhozott rendszerkép lehet ugyanabban az ACR-ban, ahol a feladat fut, vagy egy másik ACR-ban bármely régióban.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

* **Alkalmazás-rendszerképek alapként való** futtatása – Az ACR-feladatok jelenleg csak az alkalmazás-*(futásidejű)* rendszerképek alapként használt rendszerkép-frissítéseit követik nyomon. Nem követi nyomon a többszakaszos Docker-fájlokban használt köztes (*buildtime)* rendszerképek alapszintű rendszerkép-frissítéseit.  

* **Alapértelmezés szerint engedélyezve** – Ha az [az acr task create][az-acr-task-create] paranccsal hoz  létre egy ACR-feladatot, a feladat alapértelmezés szerint engedélyezve van az alapként használt rendszerkép-frissítés által. Ez azt jelenti, `base-image-trigger-enabled` hogy a tulajdonság True (Igaz) értékre van állítva. Ha le szeretné tiltani ezt a viselkedést egy feladatban, frissítse a tulajdonságot False (Hamis) értékre. Futtassa például a következő [az acr task update][az-acr-task-update] parancsot:

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Aktiválás a függőségek** nyomon követéséhez – Ahhoz, hogy az ACR-feladat meghatározhatja és nyomon követ tudja a tároló rendszerképének függőségeit – amelyek az alapként szolgáló rendszerképet is tartalmazzák – először aktiválni kell a feladatot, hogy legalább egyszer buildeljük a rendszerképet.  Például manuálisan aktiválhatja a feladatot az [az acr task run paranccsal.][az-acr-task-run]

* **Alapkéntkénti rendszerkép** stabil címkéje – Ahhoz, hogy az alapként való rendszerképfrissítéskor elindítsa *a* feladatot, az alapként való rendszerképnek stabil címkével kell lennie, `node:9-alpine` például: . Ez a címkézés jellemzően egy olyan alapként megjelölt rendszerképre jellemző, amely az operációs rendszer és a keretrendszer legújabb, stabil kiadásra való javításával frissül. Ha az alap rendszerképet egy új verziócímkével frissítik, az nem indít el feladatot. A képcímkézéssel kapcsolatos további információkért tekintse meg az ajánlott [eljárásokat.](container-registry-image-tag-version.md) 

* **Egyéb feladat-eseményindítók** – Az alapként szolgáló rendszerkép-frissítések által [](container-registry-tutorial-build-task.md) aktivált feladatban a forráskód véglegesítése vagy ütemezése alapján is engedélyezheti [az eseményindítókat.](container-registry-tasks-scheduled.md) Az alapként való rendszerkép-frissítés többlépéses [feladatot is aktiválhat.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Következő lépések

Az alábbi oktatóanyagokban olyan forgatókönyveket láthat, amelyek az alapként használható rendszerkép frissítése után automatizálják az alkalmazás-rendszerkép-összeállításokat:

* [Tároló rendszerkép-összeállításának automatizálása, ha egy alapként szolgáló rendszerképet ugyanabban a regisztrációs adatbázisban frissítenek](container-registry-tutorial-base-image-update.md)

* [Tároló rendszerkép-összeállításának automatizálása, ha egy alapként szolgáló rendszerképet egy másik regisztrációs adatbázisban frissítenek](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
