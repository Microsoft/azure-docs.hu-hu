---
title: Alapszintű rendszerkép frissítései – feladatok
description: Tudnivalók az alkalmazás-tárolók rendszerképeinek alapképeiről, valamint arról, hogy az alapszintű lemezképek frissítése hogyan indíthat el Azure Container Registry feladatot.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: df33096830cd7b34a288c38c105aff3610315337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97707486"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Az ACR-feladatok alaprendszerképének frissítései

Ez a cikk háttérbeli információkat biztosít az alkalmazás alaprendszerképének frissítéseiről, valamint arról, hogy ezek a frissítések hogyan indíthatnak Azure Container Registry feladatot.

## <a name="what-are-base-images"></a>Mik azok az alapképek?

A legtöbb Dockerfiles meghatározó szülő rendszerképet adjon meg, amelyből a lemezkép alapul, amelyet gyakran az *alaprendszerképnek* is neveznek. Az alapként szolgáló rendszerképek általában az operációs rendszert tartalmazzák (például [Alpine Linux][base-alpine] vagy [Windows Nano Server][base-windows]), amelyre a tároló többi szintje alkalmazva lesz. Alkalmazás-keretrendszereket is tartalmazhatnak, például a [Node.js][base-node] vagy a [.NET Core][base-dotnet] keretrendszert. Ezek az alaplemezképek általában nyilvános upstream lemezképeken alapulnak. Az alkalmazási lemezképek közül több is megoszthat egy közös alapképet.

A rendszerképek kezelői gyakran frissítik az alapként szolgáló rendszerképet, hogy új szolgáltatásokkal és javításokkal bővítsék a rendszerképben található operációs rendszert vagy keretrendszert. A biztonsági javítások jelentik az alapként szolgáló rendszerkép frissítésének egy másik gyakori okát. Ha ezek a felsőbb rétegbeli frissítések bekövetkeznek, az alaplemezképeket is frissítenie kell, hogy tartalmazza a kritikus javítást. Ezután az egyes alkalmazás-rendszerképeket is újra kell építeni, hogy az tartalmazza az alaprendszerkép részét képező felsőbb rétegbeli javításokat.

Bizonyos esetekben, például egy privát fejlesztői csapatnál előfordulhat, hogy egy alaprendszerkép több mint operációs rendszert vagy keretrendszert határoz meg. Egy alaprendszerkép lehet például egy olyan megosztott szolgáltatás-összetevő rendszerképe, amelyet nyomon kell követni. Előfordulhat, hogy egy csapat tagjainak nyomon kell követniük ezt az alapképet a teszteléshez, vagy rendszeresen frissíteniük kell a lemezképet az alkalmazás lemezképének fejlesztésekor.

## <a name="maintain-copies-of-base-images"></a>Alapképek másolatának karbantartása

A beállításjegyzékben lévő olyan tartalmak esetében, amelyek egy nyilvános beállításjegyzékben (például Docker hub) tárolt alaptartalomtól függenek, javasoljuk, hogy másolja a tartalmat egy Azure Container registrybe vagy egy másik privát beállításjegyzékbe. Ezután győződjön meg arról, hogy az alkalmazás lemezképeit a privát alapképekre hivatkozva hozza létre. A Azure Container Registry [Képimportálási](container-registry-import-images.md) funkcióval egyszerűen másolhat tartalmakat a nyilvános vagy más Azure-beli tároló-beállításjegyzékből. A következő szakasz az ACR-feladatok használatát ismerteti az alkalmazás frissítéseinek kiépítésekor az alaprendszerkép frissítéseinek nyomon követéséhez. Nyomon követheti az alaprendszerkép-frissítéseket a saját Azure Container-jegyzékekben, és opcionálisan a felsőbb rétegbeli nyilvános nyilvántartásokban is.

## <a name="track-base-image-updates"></a>Alapszintű rendszerkép frissítéseinek nyomon követése

Az ACR Tasks lehetővé teszi a rendszerképek automatikus összeállítását a tárolók alapként szolgáló rendszerképének frissítésekor. Ezzel a lehetőséggel megtarthatja és frissítheti a nyilvános alaplemezképek példányait az Azure Container-jegyzékekben, majd újraépítheti az alaplemezképtől függő alkalmazás-lemezképeket.

Az ACR-feladatok dinamikusan felfedik az alaprendszerkép-függőségeket, amikor létrehoznak egy tároló-rendszerképet. Ennek eredményeképpen észlelhető, ha az alkalmazás rendszerképének alaprendszerképe frissül. Egy előre konfigurált Build feladattal az ACR-feladatok automatikusan újraépíthetik az alapképre hivatkozó összes alkalmazási képet. Ezzel az Automatikus észleléssel és újjáépítéssel az ACR-feladatok elmentik azt az időt és fáradságot, amelyet a rendszer általában a frissített alaprendszerképre hivatkozó minden egyes rendszerkép manuális nyomon követéséhez és frissítéséhez szükséges.

## <a name="base-image-locations"></a>Alapszintű rendszerkép helyei

A Docker származó lemezképek esetében az ACR-feladat az alábbi helyekről észleli az alaplemezképek függőségeit:

* Ugyanaz az Azure Container Registry, ahol a feladat fut
* Egy másik privát Azure Container Registry ugyanabban vagy egy másik régióban 
* Nyilvános tárház a Docker hub-ban 
* Nyilvános tárház a Microsoft Container Registryban

Ha az utasításban megadott alaprendszerkép az `FROM` egyik helyen található, az ACR-feladat hozzáadja a horgot, hogy a rendszerkép újra legyen építve, amikor az alapja frissül.

## <a name="base-image-notifications"></a>Alapszintű rendszerkép értesítései

Az alaprendszerkép frissítésének és a függő feladat indításának időpontja közötti idő az alaprendszerkép helyétől függ:

* **Nyilvános tárházból származó alaplemezképek a Docker hub-ban vagy a MCR** – a nyilvános adattárakban lévő alaplemezképek esetében egy ACR-feladat 10 és 60 perc közötti véletlenszerű időközönként ellenőrzi a lemezképek frissítéseit. A függő feladatok futtatása ennek megfelelően történik.
* Alaplemezképek az **Azure Container registryből** – az alaplemezképek az Azure Container-jegyzékekben, az ACR-feladatok azonnal elindítja a futtatást, amikor az alaprendszerképe frissül. Előfordulhat, hogy az alaprendszerkép ugyanabban az ACR-ben található, ahol a feladat fut, vagy más ACR-régiókban.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

* **Alkalmazási lemezképek alaprendszerképei** – jelenleg egy ACR-feladat csak az alkalmazás-(*futtatókörnyezet*-) lemezképek alaprendszerkép-frissítéseinek nyomon követésére használható. Nem követ nyomon a többfázisú Dockerfiles használt közbenső (*buildtime*) lemezképek alaprendszerkép-frissítéseit.  

* **Alapértelmezés szerint engedélyezve** – ha egy ACR-feladatot hoz létre az az [ACR Task Create][az-acr-task-create] paranccsal, alapértelmezés szerint a feladat *engedélyezve* van az alaprendszerkép frissítésével. Vagyis a tulajdonság értéke `base-image-trigger-enabled` true (igaz). Ha le szeretné tiltani ezt a viselkedést egy feladatban, frissítse a tulajdonságot hamis értékre. Futtassa például a következő az [ACR Task Update][az-acr-task-update] parancsot:

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* A **függőségek nyomon követése** – egy ACR-feladat engedélyezéséhez, amely meghatározza és nyomon követheti a tárolók rendszerképének függőségeit – ez tartalmazza az alaprendszerképét is – először a feladat elindítását kell elindítania, hogy **legalább egyszer** felkészítse a rendszerképet. Például a feladat manuális elindításához használja az az [ACR Task Run][az-acr-task-run] parancsot.

* **Az alaprendszerkép stabil címkéje** – az alaprendszerkép frissítésére vonatkozó feladat elindításához az alapképnek *stabil* címkével kell rendelkeznie, például: `node:9-alpine` . Ez a címkézés jellemző olyan alaplemezképek esetében, amelyek az operációs rendszer és a keretrendszer javításával frissítve lettek egy legújabb stabil kiadásra. Ha az alaprendszerkép új verzió címkével frissül, nem indít el feladatot. A képcímkézéssel kapcsolatos további információkért tekintse meg az [ajánlott eljárásokat ismertető útmutatót](container-registry-image-tag-version.md). 

* **Egyéb feladat-eseményindítók** – az alaprendszerkép frissítései által aktivált feladatok esetében engedélyezheti a [forráskód](container-registry-tutorial-build-task.md) -végrehajtás vagy [az ütemterv](container-registry-tasks-scheduled.md)alapján történő eseményindítókat is. Egy alapszintű rendszerkép frissítése [több lépésből álló feladatot](container-registry-tasks-multi-step.md)is indíthat.

## <a name="next-steps"></a>Következő lépések

Az alaprendszerkép frissítése után az alkalmazás rendszerkép-buildek automatizálására szolgáló forgatókönyvek az alábbi oktatóanyagokat ismertetik:

* [A tároló rendszerképének automatizálása, ha egy alaprendszerkép frissül ugyanabban a beállításjegyzékben](container-registry-tutorial-base-image-update.md)

* [A tároló rendszerképének automatizálása, ha egy alaprendszerkép frissítése egy másik beállításjegyzékben történik](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
