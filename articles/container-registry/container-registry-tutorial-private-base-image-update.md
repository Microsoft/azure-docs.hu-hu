---
title: Oktatóanyag – Rendszerkép buildének aktiválása privát alapként való rendszerképfrissítéssel
description: Ebben az oktatóanyagban egy Azure Container Registry-feladatot fog konfigurálni a tároló rendszerkép-buildek automatikus aktiválásához a felhőben, amikor egy másik privát Azure Container Registryben lévő alapként szolgáló rendszerkép frissül.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 27ab7c3fc0f04023c32cfac181d8f8650de23560
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772360"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Oktatóanyag: Tároló rendszerkép-buildek automatizálása egy alapként szolgáló rendszerkép egy másik privát tárolójegyzékben való frissítése esetén 

[ACR-feladatok](container-registry-tasks-overview.md) a tárolók alapként szolgáló rendszerképének frissítése esetén támogatja az automatikus rendszerkép-összeállítást, [](container-registry-tasks-base-images.md)például ha az operációs rendszert vagy az alkalmazás-keretrendszert javítja az egyik alapként szolgáló rendszerképben. 

Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan ACR-feladatot, amely buildelést aktivál a felhőben, amikor egy tároló alapként szolgáló rendszerképét egy másik Azure-beli tároló-beállításjegyzékbe leküldi a rendszer. Megpróbálhat egy oktatóanyagot is létrehozni egy olyan ACR-feladat létrehozásához, amely rendszerkép-összeállítást aktivál, amikor egy alapként szolgáló rendszerképet ad le ugyanoda az [Azure Container Registrybe.](container-registry-tutorial-base-image-update.md)

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Az alap rendszerkép összeállítása egy alap beállításjegyzékben
> * Alkalmazás-összeállítási feladat létrehozása egy másik beállításjegyzékben az alapként használt rendszerkép nyomon követéséhez 
> * Az alapként szolgáló rendszerkép frissítése egy alkalmazásrendszerkép-feladat aktiválásához
> * Az aktivált feladat megjelenítése
> * A frissített alkalmazás-rendszerkép ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

### <a name="complete-the-previous-tutorials"></a>Az előző oktatóanyagok elvégzése

Ez az oktatóanyag feltételezi, hogy már konfigurálta a környezetet, és befejezte a sorozat első két oktatóanyagának lépéseit, amelyekben:

* Azure Container Registry létrehozása
* Mintaadattár leágaztatása
* Mintaadattár klónozása
* Személyes hozzáférési jogkivonat létrehozása a GitHubban

Ha még nem tette meg, a folytatás előtt töltse ki az alábbi oktatóanyagokat:

[Tárolórendszerképek összeállítása a felhőben az Azure Container Registry Tasks használatával](container-registry-tutorial-quick-task.md)

[Tárolórendszerképek összeállításának automatizálása az Azure Container Registry Tasks használatával](container-registry-tutorial-build-task.md)

Az előző oktatóanyagokhoz létrehozott tároló-beállításjegyzéken kívül létre kell hoznia egy regisztrációs adatbázist is az alapként szolgáló rendszerképek tárolására. Ha szeretné, hozza létre a második beállításjegyzéket az eredeti beállításjegyzéktől eltérő helyen.

### <a name="configure-the-environment"></a>A környezet konfigurálása

Láss el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, minden értéket manuálisan kell lecserélnie, amikor az megjelenik a példaparancsok között.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Az alapként szolgáló rendszerkép frissítése forgatókönyv

Ez az oktatóanyag végigvezeti az alapként szolgáló rendszerkép frissítési forgatókönyvén. Ez a forgatókönyv egy fejlesztési munkafolyamatot tükröz, amely alapként szolgáló rendszerképeket kezel egy közös privát tárolóregisztr adatbázisban, amikor más regisztrációs adatbázisban hoz létre alkalmazás-rendszerképeket. Az alapként megadott lemezképek a csapat által használt gyakori operációs rendszereket és keretrendszereket, vagy akár a gyakori szolgáltatás-összetevőket is meghatározhatják.

Például azok a fejlesztők, akik saját regisztrációs adatbázisaikban fejlesztik az alkalmazás-rendszerképeket, hozzáférhetnek a közös alapjegyzékben karbantartott alapként használt rendszerképek készletéhez. Az alap beállításjegyzék lehet egy másik régióban, vagy akár georeplikált is.

A [kódminta][code-sample] két Docker-fájlt tartalmaz: egy alkalmazás-rendszerképet, és egy rendszerképet, amelyet az alkalmazás-rendszerkép az alapként szolgáló rendszerképeként ad meg. A következő szakaszokban egy ACR-feladatot hoz létre, amely automatikusan elindítja az alkalmazás-rendszerkép buildelését, amikor az alapként szolgáló rendszerkép új verzióját egy másik Azure-beli tárolójegyzékbe kell leküldeni.

* [Dockerfile-app][dockerfile-app]: Egy kisméretű Node.js-webalkalmazás, amely az alapjául szolgáló Node.js-verziót jelző statikus weboldalt jelenít meg. A verziósztring szimulált elem: az alapként szolgáló rendszerképben definiált környezeti változó (`NODE_VERSION`) tartalmát jeleníti meg.

* [Dockerfile-base][dockerfile-base]: A rendszerkép, amelyet a `Dockerfile-app` az alapjaként ad meg. Egy [Node][base-node]-rendszerképen alapul, és tartalmazza a `NODE_VERSION` környezeti változót.

A következő szakaszokban létrehozunk egy feladatot, frissítjük a `NODE_VERSION` értékét az alapként szolgáló rendszerkép Docker-fájljában, majd az ACR Tasks használatával összeállítjuk az alapként szolgáló rendszerképet. Amikor az ACR-feladat leküldi az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az automatikusan kiváltja az alkalmazás-rendszerkép összeállítását. Ha kívánja, az alkalmazástároló-rendszerkép helyi futtatásával megtekintheti az összeállított rendszerképek eltérő verzió-sztringjeit.

Ebben az oktatóanyagban az ACR-feladat egy Docker-fájlban megadott alkalmazástároló-rendszerképet buildel és leküld. ACR-feladatok többlépéses feladatokat is futtathat egy [YAML-fájl](container-registry-tasks-multi-step.md)használatával, amely több tároló építési, leküldési és opcionális tesztelési lépéseit határozza meg.

## <a name="build-the-base-image"></a>Az alapként szolgáló rendszerkép összeállítása

Először készítse el az alapként használt rendszerképet egy gyors ACR-feladatok *az* [az acr build használatával.][az-acr-build] Amint azt a sorozat [első részében](container-registry-tutorial-quick-task.md) bemutattuk, ez a folyamat nem csupán összeállítja a rendszerképet, hanem siker esetén le is küldi azt a tárolóregisztrációs adatbázisba. Ebben a példában a rendszerképet lekérte a rendszerkép alapként való regisztrációs adatbázisa.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Feladat létrehozása a privát alapként használt rendszerkép nyomon követéséhez

Ezután hozzon létre egy feladatot az alkalmazás rendszerkép-beállításjegyzékében [az az acr task create segítségével,][az-acr-task-create]amely lehetővé teszi a [felügyelt identitást.](container-registry-tasks-authentication-managed-identity.md) A felügyelt identitást a későbbi lépésekben használjuk, hogy a feladat az alap rendszerkép-beállításjegyzékben hitelesítve lesz. 

Ez a példa egy rendszer által hozzárendelt identitást használ, de bizonyos helyzetekben létrehozhat és engedélyezhet egy felhasználó által hozzárendelt felügyelt identitást. Részletekért lásd: Regisztrációs adatbázisközi hitelesítés egy [ACR-feladatban azure-beli felügyelt identitás használatával.](container-registry-tasks-cross-registry-authentication.md)

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

Ez a feladat hasonló az előző [oktatóanyagban létrehozott feladathoz.](container-registry-tutorial-build-task.md) A feladat utasítja az ACR Tasksot egy rendszerkép-összeállítás aktiválására, amikor a rendszer leküld egy véglegesítést a `--context` által megadott adattárba. Míg az előző oktatóanyagban a rendszerkép felépítéséhez használt Dockerfile meghatároz egy nyilvános alapként használt rendszerképet ( ), a feladatban található `FROM node:15-alpine` Dockerfile, a [Dockerfile-app][dockerfile-app]egy alapként megadott rendszerképet az alapként megadott rendszerkép-beállításjegyzékben:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Ezzel a konfigurációval az oktatóanyag későbbi, az alapként használható rendszerképében egyszerűen szimulálható egy keretrendszer-javítás.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Identitás le pull-engedélyeinek adjuk az alap beállításjegyzéket

Ahhoz, hogy a feladat felügyelt identitásának jogosultságot adjon az alap rendszerkép-beállításjegyzék rendszerképeinek lekért számára, először futtassa az [az acr task show][az-acr-task-show] futtatásával az identitás szolgáltatásnév-azonosítóját. Ezután az [az acr show futtatásával][az-acr-show] szerezze be az alap beállításjegyzék erőforrás-azonosítóját:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Rendelje hozzá a felügyelt identitás lekért engedélyét a regisztrációs adatbázishoz [az az role assignment create futtatásával:][az-role-assignment-create]

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Cél beállításjegyzékbeli hitelesítő adatok hozzáadása a feladathoz

Futtassa [az az acr task credential add et,][az-acr-task-credential-add] hogy hitelesítő adatokat adjon hozzá a feladathoz. Adja át a paramétert annak jelzésére, hogy a tevékenység rendszer által hozzárendelt felügyelt `--use-identity [system]` identitása hozzáférhet a hitelesítő adatokhoz.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Az [az acr task run használatával][az-acr-task-run] manuálisan aktiválhatja a feladatot, és buildelheti az alkalmazás rendszerképét. Erre a lépésre azért van szükség, hogy a tevékenység nyomon követi az alkalmazás rendszerképének az alapként tartozó rendszerképtől való függőségét.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Amint a feladat véget ért, jegyezze fel a **Run ID** (Futtatási azonosító) értékét (például „da6”), ha a következő nem kötelező lépést is végre szeretné hajtani.

### <a name="optional-run-application-container-locally"></a>Nem kötelező: Alkalmazástároló helyi futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, a tároló futtatásakor az alkalmazást megtekintheti egy webböngészőben, mielőtt újraépítené az alapként szolgáló rendszerképét. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja az `az acr login` és a `docker run` parancsot).

Először hitelesítse magát a tároló-beállításjegyzékben [az az acr login:][az-acr-login]

```azurecli
az acr login --name $ACR_NAME
```

Futtassa helyileg a tárolót a `docker run` paranccsal. Cserélje le a helyére az előző lépés kimenetében található **\<run-id\>** futtatásazonosítót (például "da6"). Ez a példa nevet ad a tárolónak, és tartalmazza a paramétert, amely eltávolítja `myapp` `--rm` a tárolót, amikor leállítja.

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

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>Az alapként szolgáló rendszerkép frissítése

Itt most egy keretrendszer-javítást szimulál az alapként szolgáló rendszerképben. Módosítsa a **Dockerfile-base** fájlt, és adja hozzá az „a” tagot a `NODE_VERSION` attribútumban meghatározott verziószám után:

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

Futtasson egy gyors feladatot az alapként szolgáló módosított rendszerkép összeállításához. Jegyezze fel a kimenetben található **Run ID** (Futtatási azonosító) értékét.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

Miután befejeződött az összeállítás, és az ACR-feladat leküldte az alapként szolgáló új rendszerképet a regisztrációs adatbázisba, az aktiválja az alkalmazás-rendszerkép összeállítását. Eltarthat egy ideig, amíg a korábban létrehozott feladat aktiválja az alkalmazás-rendszerkép összeállítását, mivel észlelnie kell az újonnan összeállított és leküldött alapként szolgáló rendszerképet.

## <a name="list-updated-build"></a>Frissített összeállítás listázása

Az alapként szolgáló rendszerkép frissítését követően listázza újra a feladatfuttatásokat, hogy összehasonlíthassa azokat a korábbi listával. Ha a kimenet eleinte nem tér el a korábbitól, időnként futtassa újra a parancsot, amíg az új feladatfuttatás meg nem jelenik a listában.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

A kimenet a következőkhöz hasonló. Az utolsóként végrehajtott összeállítás TRIGGER (Eseményindító) értékének az „Image Update” (Rendszerképfrissítés) értéknek kell lennie, ami jelzi, hogy a feladatot az alapként szolgáló rendszerkép gyors feladata aktiválta.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

Ha szeretné végrehajtani az újonnan létrehozott tároló futtatásának következő nem kötelező lépését a frissített verziószám végrehajtásához, jegyezze fel a rendszerképfrissítés által aktivált build RUN **ID** (Futtatás azonosítója) értékét (az előző kimenetben ez a "ca13").

### <a name="optional-run-newly-built-image"></a>Nem kötelező: Az újonnan összeállított rendszerkép futtatása

Ha helyi környezetben dolgozik (nem a Cloud Shellben), és a Docker telepítve lett, futtassa az új alkalmazás-rendszerképet, amint annak összeállítása befejeződött. A `<run-id>` helyére az előző lépésben beszerzett RUN ID (Futtatási azonosító) értéket írja. A Cloud Shell használata esetén hagyja ki ezt a szakaszt (a Cloud Shell nem támogatja a `docker run` parancsot).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Lépjen a http://localhost:8081 címre a böngészőben, ahol a weboldalon a Node.js frissített verziószámának (az „a” taggal kiegészítve) kell megjelennie:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Képernyőkép a böngészőben frissített mintaalkalmazásról":::

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
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task]: /cli/azure/acr#az_acr_task
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
