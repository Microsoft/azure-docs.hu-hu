---
title: Oktatóanyag – Rendszerkép-összeállítás aktiválása alapként való rendszerképfrissítéssel
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy Azure Container Registry-feladatot úgy, hogy automatikusan aktiválja a tároló rendszerkép-buildeket a felhőben, amikor egy alapként szolgáló rendszerképet ugyanabban a regisztrációs adatbázisban frissítenek.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6d83fb7796cecab686d52d561266bd41daef9e01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780802"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Oktatóanyag: Tároló-rendszerkép-összeállítások automatizálása alapként szolgáló rendszerkép azure-beli tárolójegyzékben való frissítése esetén 

[ACR-feladatok](container-registry-tasks-overview.md) támogatja a tárolórendszerképek automatikus összeállítását a tárolók alapként szolgáló rendszerképének frissítése [esetén,](container-registry-tasks-base-images.md)például ha az operációs rendszert vagy az alkalmazás-keretrendszert javítja az egyik alapként szolgáló rendszerképben. 

Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan ACR-feladatot, amely buildelést aktivál a felhőben, amikor egy tároló alapként szolgáló rendszerképét ugyanoda a regisztrációs adatbázisba leküldi a rendszer. Egy oktatóanyagot is kipróbálhat egy olyan ACR-feladat létrehozásához, amely rendszerkép-összeállítást aktivál, amikor egy alapként szolgáló rendszerképet egy másik [Azure-beli tároló-beállításjegyzékbe () kell leküldeni.](container-registry-tutorial-private-base-image-update.md) 

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Az alapként szolgáló rendszerkép összeállítása
> * Alkalmazás-rendszerkép létrehozása ugyanabban a beállításjegyzékben az alapként használt rendszerkép nyomon követéséhez 
> * Az alapként szolgáló rendszerkép frissítése egy alkalmazásrendszerkép-feladat aktiválásához
> * Az aktivált feladat megjelenítése
> * A frissített alkalmazás-rendszerkép ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

### <a name="complete-the-previous-tutorials"></a>Az előző oktatóanyagok elvégzése

Ez az oktatóanyag feltételezi, hogy már konfigurálta a környezetet, és befejezte a sorozat első két oktatóanyagának lépéseit, amelyekben:

- Azure Container Registry létrehozása
- Mintaadattár leágaztatása
- Mintaadattár klónozása
- Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ha még nem tette meg, a folytatás előtt töltse ki az alábbi oktatóanyagokat:

[Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Tasks használatával](container-registry-tutorial-quick-task.md)

[Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Tasks használatával](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>A környezet konfigurálása

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- Ehhez a cikkhez az Azure CLI 2.0.46-os vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

Láss el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, minden értéket manuálisan kell lecserélnie, amikor az megjelenik a példaparancsok között.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```


### <a name="base-image-update-scenario"></a>Az alapként szolgáló rendszerkép frissítése forgatókönyv

Ez az oktatóanyag végigvezeti egy alapként használható rendszerkép-frissítési forgatókönyven, amelyben egy alap rendszerképet és egy alkalmazás-rendszerképet tart fenn egyetlen regisztrációs adatbázisban. 

A [kódminta][code-sample] két Docker-fájlt tartalmaz: egy alkalmazás-rendszerképet, és egy rendszerképet, amelyet az alkalmazás-rendszerkép az alapként szolgáló rendszerképeként ad meg. A következő szakaszokban egy olyan ACR-feladatot hoz létre, amely automatikusan elindítja az alkalmazás-rendszerkép buildelését, amikor az alapként szolgáló rendszerkép új verziója kerül ugyanoda a tároló-beállításjegyzékbe.

* [Dockerfile-app][dockerfile-app]: Egy kisméretű Node.js-webalkalmazás, amely az alapjául szolgáló Node.js-verziót jelző statikus weboldalt jelenít meg. A verziósztring szimulált elem: az alapként szolgáló rendszerképben definiált környezeti változó (`NODE_VERSION`) tartalmát jeleníti meg.

* [Dockerfile-base][dockerfile-base]: A rendszerkép, amelyet a `Dockerfile-app` az alapjaként ad meg. Egy [Node][base-node]-rendszerképen alapul, és tartalmazza a `NODE_VERSION` környezeti változót.

A következő szakaszokban létrehozunk egy feladatot, frissítjük a `NODE_VERSION` értékét az alapként szolgáló rendszerkép Docker-fájljában, majd az ACR Tasks használatával összeállítjuk az alapként szolgáló rendszerképet. Amikor az ACR-feladat leküldi az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az automatikusan kiváltja az alkalmazás-rendszerkép összeállítását. Ha kívánja, az alkalmazástároló-rendszerkép helyi futtatásával megtekintheti az összeállított rendszerképek eltérő verzió-sztringjeit.

Ebben az oktatóanyagban az ACR-feladat egy Docker-fájlban megadott alkalmazástároló-rendszerképet buildel és leküld. ACR-feladatok többlépéses feladatokat is futtathat egy [YAML-fájl](container-registry-tasks-multi-step.md)használatával, amely több tároló buildelő, leküldési és opcionális tesztelési lépéseit határozza meg.

## <a name="build-the-base-image"></a>Az alapként szolgáló rendszerkép összeállítása

Először készítse el az alapként használt rendszerképet egy gyors ACR-feladatok *az* [az acr build használatával.][az-acr-build] Amint azt a sorozat [első részében](container-registry-tutorial-quick-task.md) bemutattuk, ez a folyamat nem csupán összeállítja a rendszerképet, hanem siker esetén le is küldi azt a tárolóregisztrációs adatbázisba.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Tevékenység létrehozása

Ezután hozzon létre egy feladatot az [az acr task create][az-acr-task-create] paranccsal:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample1 \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

Ez a feladat hasonló az előző [oktatóanyagban létrehozott feladathoz.](container-registry-tutorial-build-task.md) A feladat utasítja az ACR Tasksot egy rendszerkép-összeállítás aktiválására, amikor a rendszer leküld egy véglegesítést a `--context` által megadott adattárba. Míg az előző oktatóanyagban a rendszerkép felépítéséhez használt Dockerfile meghatároz egy nyilvános alapként használt rendszerképet ( ), a feladatban található `FROM node:15-alpine` Dockerfile, a [Dockerfile-app][dockerfile-app]egy alapként használt rendszerképet ad meg ugyanabban a beállításjegyzékben:

```dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Ezzel a konfigurációval az oktatóanyag későbbi, az alapként használható rendszerképében egyszerűen szimulálható egy keretrendszer-javítás.

## <a name="build-the-application-container"></a>Az alkalmazástároló összeállítása

Az [az acr task run használatával][az-acr-task-run] manuálisan aktiválhatja a feladatot, és felépítheti az alkalmazás rendszerképét. Erre a lépésre azért van szükség, hogy a tevékenység nyomon követi az alkalmazás rendszerképének az alapként tartozó rendszerképtől való függőségét.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample1
```

Amint a feladat véget ért, jegyezze fel a **Run ID** (Futtatási azonosító) értékét (például „da6”), ha a következő nem kötelező lépést is végre szeretné hajtani.

### <a name="optional-run-application-container-locally"></a>Nem kötelező: Alkalmazástároló helyi futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, a tároló futtatásakor az alkalmazást megtekintheti egy webböngészőben, mielőtt újraépítené az alapként szolgáló rendszerképét. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja az `az acr login` és a `docker run` parancsot).

Először hitelesítse magát a tároló-beállításjegyzékben [az az acr login:][az-acr-login]

```azurecli
az acr login --name $ACR_NAME
```

Futtassa helyileg a tárolót a `docker run` paranccsal. Cserélje le a helyére az előző lépés kimenetében található **\<run-id\>** futtatásazonosítót (például "da6"). Ez a példa a tárolónak ad nevet, és tartalmazza a leállításkor a tároló `myapp` `--rm` eltávolítására szolgáló paramétert.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Lépjen a `http://localhost:8080` címre a böngészőben, és a weboldalon a Node.js verziószámának kell megjelennie az alábbihoz hasonlóan. Egy későbbi lépésben majd újabbra frissíti a verziót úgy, hogy egy „a” tagot ad hozzá a verziósztringhez.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Képernyőkép a böngészőben látható mintaalkalmazásról":::

A tároló leállításhoz és eltávolításához futtassa a következő parancsot:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Összeállítások listázása

Ezután listázza a feladatfuttatásokat, amelyeket az ACR Tasks elkészített a regisztrációs adatbázis számára az [az acr task list-runs][az-acr-task-list-runs] paranccsal:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Ha elvégezte az előző oktatóanyagot (és nem törölte a regisztrációs adatbázist), az alábbihoz hasonló kimenetnek kell megjelennie. Jegyezze fel a feladatfuttatások számát, valamint a RUN ID oszlop legújabb értékét, hogy összehasonlíthassa a kimenettel, miután frissítette az alapként szolgáló rendszerképet a következő szakaszban.

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cax       baseexample1    linux       Succeeded  Manual     2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit     2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual     2020-11-20T23:07:29Z  00:00:27
```

## <a name="update-the-base-image"></a>Az alapként szolgáló rendszerkép frissítése

Itt most egy keretrendszer-javítást szimulál az alapként szolgáló rendszerképben. Módosítsa a **Dockerfile-base** fájlt, és adja hozzá az „a” tagot a `NODE_VERSION` attribútumban meghatározott verziószám után:

```dockerfile
ENV NODE_VERSION 15.2.1a
```

Futtasson egy gyors feladatot az alapként szolgáló módosított rendszerkép összeállításához. Jegyezze fel a kimenetben található **Run ID** (Futtatási azonosító) értékét.

```azurecli
az acr build --registry $ACR_NAME --image baseimages/node:15-alpine --file Dockerfile-base .
```

Miután befejeződött az összeállítás, és az ACR-feladat leküldte az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az aktiválja az alkalmazás-rendszerkép összeállítását. Eltarthat egy ideig, amíg a korábban létrehozott feladat aktiválja az alkalmazás-rendszerkép összeállítását, mivel észlelnie kell az újonnan összeállított és leküldött alapként szolgáló rendszerképet.

## <a name="list-updated-build"></a>Frissített összeállítás listázása

Az alapként szolgáló rendszerkép frissítését követően listázza újra a feladatfuttatásokat, hogy összehasonlíthassa azokat a korábbi listával. Ha a kimenet eleinte nem tér el a korábbitól, időnként futtassa újra a parancsot, amíg az új feladatfuttatás meg nem jelenik a listában.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

A kimenet a következőkhöz hasonló. Az utolsóként végrehajtott összeállítás TRIGGER (Eseményindító) értékének az „Image Update” (Rendszerképfrissítés) értéknek kell lennie, ami jelzi, hogy a feladatot az alapként szolgáló rendszerkép gyors feladata aktiválta.

```output
Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
cav       example2        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:55
cau       example1        linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:40
cat       taskhelloworld  linux       Succeeded  Manual        2020-11-20T23:07:29Z  00:00:27
```

Ha szeretné végrehajtani a következő nem kötelező lépést az újonnan felépített tároló futtatásához a frissített verziószám végrehajtásához, jegyezze fel a rendszerképfrissítés által aktivált build **RUN ID** (Futtatás azonosítója) értékét (az előző kimenetben ez a "ca11").

### <a name="optional-run-newly-built-image"></a>Nem kötelező: Az újonnan összeállított rendszerkép futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, futtassa az új alkalmazás-rendszerképet, amint annak összeállítása befejeződött. A `<run-id>` helyére az előző lépésben beszerzett RUN ID (Futtatási azonosító) értéket írja. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja a `docker run` parancsot).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Lépjen a http://localhost:8081 címre a böngészőben, ahol a weboldalon a Node.js frissített verziószámának (az „a” taggal kiegészítve) kell megjelennie:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Képernyőkép a frissített mintaalkalmazásról a böngészőben":::


Figyelje meg, hogy miután frissítette az **alapként szolgáló rendszerképet** egy új verziószámmal, az utoljára összeállított **alkalmazás-rendszerkép** az új verziót jeleníti meg. Az ACR Tasks észlelte az alapként szolgáló rendszerkép módosítását, és automatikusan újraépítette az alkalmazás-rendszerképet.

A tároló leállításhoz és eltávolításához futtassa a következő parancsot:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására a rendszerkép alapként szolgáló rendszerképének frissítése esetén. Most lépjen tovább a következő oktatóanyagra, amelyből megtudhatja, hogyan aktiválhat feladatokat egy meghatározott ütemezés szerint.

> [!div class="nextstepaction"]
> [Feladat futtatása ütemezés szerint](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-node]: https://hub.docker.com/_/node/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr
