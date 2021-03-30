---
title: YAML-hivatkozás – ACR-feladatok
description: Az ACR-feladatok YAML kapcsolatos feladatok definiálásának referenciája, beleértve a feladatok tulajdonságait, a lépések típusát, a lépés tulajdonságait és a beépített változókat.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 042310d29f5561c2cd77b0b9cccfc587ca4aa767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067583"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-feladatok leírása: YAML

Az ACR-feladatok többlépéses feladatának definíciója egy tároló-központú számítási primitívet biztosít a tárolók létrehozásához, teszteléséhez és javításához. Ez a cikk a több lépésből álló feladatokat definiáló YAML-fájlok parancsait, paramétereit, tulajdonságait és szintaxisát ismerteti.

Ez a cikk a többlépéses tevékenységek YAML-fájljainak ACR-feladatokhoz való létrehozását ismerteti. Ha szeretné bevezetni az ACR-feladatokat, tekintse meg az [ACR-feladatok áttekintését](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR-Task. YAML fájlformátum

Az ACR-feladatok támogatják a többlépéses feladatokat a szabványos YAML szintaxisban. Egy YAML-fájlban definiálhatja a feladatok lépéseit. Ezután manuálisan futtathatja a feladatot a fájl az az [ACR Run][az-acr-run] paranccsal való átadásával. Vagy a fájllal hozzon létre egy feladatot az [az ACR Task Create][az-acr-task-create] paranccsal, amely automatikusan aktiválódik egy git-végrehajtás, egy alapszintű rendszerkép frissítése vagy egy ütemterv alapján. Bár ez a cikk a `acr-task.yaml` lépéseket tartalmazó fájlra hivatkozik, az ACR-feladatok a [támogatott kiterjesztésű](#supported-task-filename-extensions)érvényes fájlneveket támogatják.

A legfelső szintű `acr-task.yaml` primitívek a **feladatok tulajdonságai**, a **Step types** és a **Step tulajdonságok**:

* A [Feladat tulajdonságai](#task-properties) a feladat-végrehajtás során minden lépésre érvényesek. A globális feladatok számos tulajdonsága létezik, beleértve a következőket:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* A [feladat lépésének típusai](#task-step-types) a feladatban végrehajtható műveletek típusait jelölik. Három lépésből álló típus létezik:
  * `build`
  * `push`
  * `cmd`
* A [feladat lépésének tulajdonságai](#task-step-properties) olyan paraméterek, amelyek egy adott lépésre vonatkoznak. Több lépésből álló tulajdonság is létezik, beleértve a következőket:
  * `startDelay`
  * `timeout`
  * `when`
  * ... és még sok más.

A fájlok alapformátuma `acr-task.yaml` , beleértve néhány gyakori lépés tulajdonságait is, az alábbiak szerint történik. Noha az összes rendelkezésre álló lépés tulajdonságai vagy a lépés típusának használata nem teljes körű, az egyszerű fájlformátum gyors áttekintést nyújt.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Támogatott feladatok fájlnév-bővítményei

Az ACR-feladatok több fájlnevet foglalnak magukban, például azt `.yaml` , hogy a rendszer feldolgozza a fájlt. Az alábbi listán *nem* szereplő bővítmények az ACR-feladatok Docker:. YAML,. YML,. toml,. JSON,. sh,. bash,. zsh,. ps1,. ps,. cmd,. bat,. TS,. js,. php,. Másolás,. RB,. lua

A YAML az ACR-feladatok által jelenleg támogatott fájlformátum. A többi filename-bővítmény a lehetséges jövőbeli támogatáshoz van fenntartva.

## <a name="run-the-sample-tasks"></a>A minta feladatok futtatása

A jelen cikk következő részeiben több minta feladatsor található. A minta feladatok nyilvános GitHub-tárházban, [Azure-Samples/ACR-feladatokban][acr-tasks]találhatók. Ezeket az Azure CLI parancs az [ACR Run][az-acr-run]paranccsal futtathatja. A minta parancsai a következőhöz hasonlóak:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A minta parancsok formázása feltételezi, hogy konfigurálta az alapértelmezett beállításjegyzéket az Azure CLI-ben, így kihagyják a `--registry` paramétert. Az alapértelmezett beállításjegyzék konfigurálásához használja az az [configure][az-configure] parancsot az `--defaults` paraméterrel, amely egy értéket fogad el `acr=REGISTRY_NAME` .

Ha például az Azure CLI-t egy "myregistry" nevű alapértelmezett beállításjegyzékgel szeretné konfigurálni:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Feladat tulajdonságai

A feladat tulajdonságai általában egy fájl tetején jelennek meg `acr-task.yaml` , és globális tulajdonságok, amelyek a feladat lépéseinek teljes végrehajtása során érvényesek. A globális tulajdonságok némelyike felülbírálható egy adott lépésen belül.

| Tulajdonság | Típus | Választható | Description | Felülbírálás támogatott | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sztring | Yes | Az `acr-task.yaml` ACR Tasks szolgáltatás által elemzett fájl verziója. Míg az ACR-feladatok a visszamenőleges kompatibilitás fenntartására törekednek, ez az érték lehetővé teszi az ACR-feladatok számára a kompatibilitás fenntartását egy meghatározott verzión belül. Ha nincs megadva, az alapértelmezett érték a legújabb verzió. | No | Nincsenek |
| `stepTimeout` | int (másodperc) | Yes | A lépés által futtatható másodpercek maximális száma. Ha a tulajdonság meg van adva egy feladathoz, az az `timeout` összes lépés alapértelmezett tulajdonságát állítja be. Ha a `timeout` tulajdonságot egy lépésben adja meg, a felülbírálja a feladat által megadott tulajdonságot. | Yes | 600 (10 perc) |
| `workingDirectory` | sztring | Yes | A tároló munkakönyvtára a futtatókörnyezetben. Ha a tulajdonság meg van adva egy feladathoz, az az `workingDirectory` összes lépés alapértelmezett tulajdonságát állítja be. Ha egy lépésben meg van adva, a felülbírálja a feladat által megadott tulajdonságot. | Yes | `c:\workspace` Windows vagy `/workspace` Linux rendszeren |
| `env` | [karakterlánc, karakterlánc,...] | Yes |  A `key=value` feladathoz tartozó környezeti változókat meghatározó karakterláncok tömbje. Ha a tulajdonság meg van adva egy feladathoz, az az `env` összes lépés alapértelmezett tulajdonságát állítja be. Ha egy lépésben meg van adva, akkor felülbírálja a feladatból örökölt környezeti változókat. | Yes | Nincsenek |
| `secrets` | [titok, titkos kód,...] | Yes | [Titkos](#secret) objektumok tömbje. | No | Nincsenek |
| `networks` | [hálózat, hálózat,...] | Yes | [Hálózati](#network) objektumok tömbje. | No | Nincsenek |
| `volumes` | [kötet, kötet,...] | Yes | [Mennyiségi](#volume) objektumok tömbje. Egy lépéshez csatlakoztatni kívánt tartalommal rendelkező köteteket határozza meg. | No | Nincsenek |

### <a name="secret"></a>titkos kód

A titkos objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sztring | No | A titok azonosítója. | Nincsenek |
| `keyvault` | sztring | Yes | A Azure Key Vault titkos URL-cím. | Nincsenek |
| `clientID` | sztring | Yes | Az Azure-erőforrások [felhasználó által hozzárendelt felügyelt identitásának ügyfél-](container-registry-tasks-authentication-managed-identity.md) azonosítója. | Nincsenek |

### <a name="network"></a>network

A hálózati objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | No | A hálózat neve. | Nincsenek |
| `driver` | sztring | Yes | A hálózat kezelésére szolgáló illesztőprogram. | Nincsenek |
| `ipv6` | logikai | Yes | Azt jelzi, hogy engedélyezve van-e az IPv6-hálózat. | `false` |
| `skipCreation` | logikai | Yes | Megadhatja, hogy kihagyja-e a hálózat létrehozását. | `false` |
| `isDefault` | logikai | Yes | Azt határozza meg, hogy a hálózat az Azure Container Registry által biztosított alapértelmezett hálózat-e. | `false` |

### <a name="volume"></a>kötet

A kötet objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | No | A csatlakoztatni kívánt kötet neve. Csak alfanumerikus karaktereket, "-" és "_" karaktert tartalmazhat. | Nincsenek |
| `secret` | Térkép [karakterlánc] sztring | No | A Térkép minden kulcsa a köteten létrehozott és kitöltött fájl neve. Minden érték a titok karakterlánc-verziója. A titkos értékeknek Base64 kódolással kell rendelkezniük. | Nincsenek |

## <a name="task-step-types"></a>Feladat lépésének típusai

Az ACR-feladatok három lépésből álló típusokat támogatnak. Minden lépés típusa több tulajdonságot is támogat, részletesen az egyes lépésekhez tartozó szakaszban.

| Lépés típusa | Description |
| --------- | ----------- |
| [`build`](#build) | Egy jól ismert szintaxissal hozza létre a tároló képét `docker build` . |
| [`push`](#push) | Végrehajtja `docker push` az újonnan létrehozott vagy újracímkézett rendszerképeket egy tároló-beállításjegyzékbe. A Azure Container Registry, a többi privát beállításjegyzék és a nyilvános Docker hub is támogatott. |
| [`cmd`](#cmd) | Futtat egy tárolót parancsként, a tárolónak átadott paraméterekkel `[ENTRYPOINT]` . A `cmd` lépés típusa támogatja a ( `env` z `detach` ), és más `docker run` , ismerős parancssori kapcsolókat, amelyek lehetővé teszik az egység és a funkcionális tesztelést egyidejű tároló-végrehajtással. |

## <a name="build"></a>építeni

Hozzon létre egy tároló-rendszerképet. A `build` lépés típusa egy több-bérlős, biztonságos működést jelent a `docker build` felhőben, amely első osztályú primitív.

### <a name="syntax-build"></a>Szintaxis: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

A `build` lépés típusa a következő táblázatban szereplő paramétereket támogatja. A `build` lépés típusa a [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) parancs összes felépítési lehetőségét is támogatja, például `--build-arg` a létrehozási idő változóinak beállítására.

| Paraméter | Leírás | Választható |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Meghatározza a beépített rendszerkép teljes körű minősítését `image:tag` .<br /><br />Mivel a lemezképek belső feladatokhoz használhatók, például a funkcionális tesztek, nem minden rendszerképhez szükséges `push` egy beállításjegyzék. Ahhoz azonban, hogy egy adott rendszerkép egy adott feladat végrehajtásán belül legyen, a képnek hivatkoznia kell erre a névre.<br /><br />`az acr build`Az ACR-feladatok futtatása ellentétben nem biztosítja az alapértelmezett leküldéses viselkedést. Az ACR-feladatok esetében az alapértelmezett forgatókönyv feltételezi, hogy képes felépíteni, érvényesíteni, majd leküldeni egy rendszerképet. Lásd: [leküldés](#push) , hogyan lehet leküldeni a létrehozott rendszerképeket. | Yes |
| `-f` &#124; `--file` | Megadja az átadott Docker `docker build` . Ha nincs megadva, a rendszer a környezet gyökerében lévő alapértelmezett Docker feltételezi. Docker megadásához adja át a fájlnevet a környezet gyökeréhez képest. | Yes |
| `context` | Az átadott gyökérkönyvtár `docker build` . Az egyes feladatok gyökérkönyvtára egy megosztott [workingDirectory](#task-step-properties)van beállítva, és tartalmazza a társított git klónozott könyvtár gyökerét. | No |

### <a name="properties-build"></a>Tulajdonságok: létrehozás

A `build` lépés típusa a következő tulajdonságokat támogatja. A tulajdonságok részleteit a cikk [feladat lépés tulajdonságai](#task-step-properties) szakaszában találja.

| Tulajdonságok | Típus | Kötelező |
| -------- | ---- | -------- |
| `detach` | logikai | Választható |
| `disableWorkingDirectoryOverride` | logikai | Választható |
| `entryPoint` | sztring | Választható |
| `env` | [karakterlánc, karakterlánc,...] | Választható |
| `expose` | [karakterlánc, karakterlánc,...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | logikai | Választható |
| `isolation` | sztring | Választható |
| `keep` | logikai | Választható |
| `network` | object | Választható |
| `ports` | [karakterlánc, karakterlánc,...] | Választható |
| `pull` | logikai | Választható |
| `repeat` | int | Választható |
| `retries` | int | Választható |
| `retryDelay` | int (másodperc) | Választható |
| `secret` | object | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `volumeMount` | object | Választható |
| `when` | [karakterlánc, karakterlánc,...] | Választható |
| `workingDirectory` | sztring | Választható |

### <a name="examples-build"></a>Példák: build

#### <a name="build-image---context-in-root"></a>Rendszerkép-környezet létrehozása a gyökérben

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Rendszerkép létrehozása – környezet az alkönyvtárban

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>nyomja

Egy vagy több létrehozott vagy újracímkézett rendszerkép leküldése egy tároló-beállításjegyzékbe. Támogatja a privát beállításjegyzékek, például a Azure Container Registry vagy a nyilvános Docker hub továbbítását.

### <a name="syntax-push"></a>Szintaxis: leküldés

A `push` lépés típusa támogatja a képek gyűjteményét. A YAML-gyűjtemény szintaxisa beágyazott és beágyazott formátumokat is támogat. Egy adott rendszerkép kitolása jellemzően beágyazott szintaxis használatával történik:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

A jobb olvashatóság érdekében beágyazott szintaxist használjon több rendszerkép leküldésekor:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Tulajdonságok: leküldés

A `push` lépés típusa a következő tulajdonságokat támogatja. A tulajdonságok részleteit a cikk [feladat lépés tulajdonságai](#task-step-properties) szakaszában találja.

| Tulajdonság | Típus | Kötelező |
| -------- | ---- | -------- |
| `env` | [karakterlánc, karakterlánc,...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | logikai | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `when` | [karakterlánc, karakterlánc,...] | Választható |

### <a name="examples-push"></a>Példák: leküldés

#### <a name="push-multiple-images"></a>Több rendszerkép leküldése

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Létrehozás, leküldés és Futtatás

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

A `cmd` lépés típusa egy tárolót futtat.

### <a name="syntax-cmd"></a>Szintaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Tulajdonságok: cmd

A `cmd` lépés típusa a következő tulajdonságokat támogatja:

| Tulajdonság | Típus | Kötelező |
| -------- | ---- | -------- |
| `detach` | logikai | Választható |
| `disableWorkingDirectoryOverride` | logikai | Választható |
| `entryPoint` | sztring | Választható |
| `env` | [karakterlánc, karakterlánc,...] | Választható |
| `expose` | [karakterlánc, karakterlánc,...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | logikai | Választható |
| `isolation` | sztring | Választható |
| `keep` | logikai | Választható |
| `network` | object | Választható |
| `ports` | [karakterlánc, karakterlánc,...] | Választható |
| `pull` | logikai | Választható |
| `repeat` | int | Választható |
| `retries` | int | Választható |
| `retryDelay` | int (másodperc) | Választható |
| `secret` | object | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `volumeMount` | object | Választható |
| `when` | [karakterlánc, karakterlánc,...] | Választható |
| `workingDirectory` | sztring | Választható |

Ezekről a tulajdonságokról a jelen cikk [feladat lépés tulajdonságai](#task-step-properties) szakaszában olvashat bővebben.

### <a name="examples-cmd"></a>Példák: cmd

#### <a name="run-hello-world-image"></a>A Hello-World rendszerkép futtatása

Ez a parancs végrehajtja a `hello-world.yaml` válaszfájlt, amely a Docker hub-on lévő [Hello-World](https://hub.docker.com/_/hello-world/) rendszerképre hivatkozik.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash-rendszerkép és echo "Helló világ" futtatása

Ez a parancs végrehajtja a `bash-echo.yaml` fájlt, amely a Docker hub [bash](https://hub.docker.com/_/bash/) -képére hivatkozik.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Adott bash-Képcímke futtatása

Egy adott rendszerkép-verzió futtatásához adja meg a címkét a következőben: `cmd` .

Ez a parancs végrehajtja a `bash-echo-3.yaml` válaszfájlt, amely a Docker hub [bash: 3.0](https://hub.docker.com/_/bash/) képére hivatkozik.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Egyéni lemezképek futtatása

A `cmd` lépés típusa a szabványos formátum használatával hivatkozik a képekre `docker run` . A beállításjegyzékbe nem tartozó rendszerképeket a rendszer feltételezi, hogy a docker.io származik. Az előző példában a következőképpen lehet megjeleníteni:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

A standard szintű `docker run` képhivatkozási konvenció használatával `cmd` bármilyen titkos beállításjegyzékből vagy a nyilvános Docker hub-ból futtathat képeket. Ha ugyanabban a beállításjegyzékben hivatkozik a lemezképekre, amelyben az ACR-feladat végrehajtása történik, nincs szükség beállításjegyzékbeli hitelesítő adatok megadására.

* Egy Azure Container registryből származó rendszerkép futtatása. Az alábbi példa feltételezi, hogy rendelkezik egy nevű beállításjegyzékgel `myregistry` és egy egyéni képpel `myimage:mytag` .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* A beállításjegyzék-hivatkozás általánosítása futtatási változóval vagy aliassal

    Ahelyett, hogy a beállításjegyzékbeli nevet a fájlban rögzítette `acr-task.yaml` , a [futtatási változó](#run-variables) vagy az [alias](#aliases)használatával megteheti a hordozható számítógépeket. A `Run.Registry` változó vagy az `$Registry` alias futásidőben van kibontva annak a beállításjegyzéknek a nevére, amelyben a feladatot végrehajtja.

    Ha például az előző feladatot szeretné általánosítani úgy, hogy az bármilyen Azure Container registryben működjön, a rendszerkép neve $Registry változóra kell hivatkoznia:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Titkos kötetek elérése

A `volumes` tulajdonság lehetővé teszi a kötetek és a hozzájuk tartozó titkos tartalmak megadását `build` `cmd` egy adott feladatban és lépésekben. Az egyes lépéseken belül egy opcionális `volumeMounts` tulajdonság felsorolja a köteteket és a tároló elérési útjait, amelyeket csatlakoztatni kell a tárolóhoz ebben a lépésben. A titkok az egyes kötetek csatlakoztatási útvonalán található fájlként vannak megadva.

Feladat végrehajtása és két titok csatlakoztatása egy lépéshez: az egyik a Key vaultban, a másik pedig a parancssorban van megadva:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Feladat lépésének tulajdonságai

Az egyes lépésekhez tartozó típusok több, a típusához megfelelő tulajdonságot is támogatnak. A következő táblázat az összes elérhető lépés tulajdonságait meghatározza. Nem minden lépés típus támogatja az összes tulajdonságot. Ha szeretné megtekinteni, hogy mely tulajdonságok érhetők el az egyes lépésekhez, tekintse meg a [cmd](#cmd), a [Build](#build)és a [leküldéses](#push) lépés típusa hivatkozás szakaszokat.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | logikai | Yes | Azt jelzi, hogy a tárolót le kell-e választani a futtatáskor. | `false` |
| `disableWorkingDirectoryOverride` | logikai | Yes | Meghatározza, hogy le kell-e tiltani a `workingDirectory` felülbírálási funkciót. Ezzel együtt a paranccsal `workingDirectory` teljes mértékben szabályozhatja a tároló munkakönyvtárát. | `false` |
| `entryPoint` | sztring | Yes | Felülbírálja `[ENTRYPOINT]` egy lépés tárolóját. | Nincsenek |
| `env` | [karakterlánc, karakterlánc,...] | Yes | A `key=value` lépés környezeti változóit meghatározó karakterláncok tömbje. | Nincsenek |
| `expose` | [karakterlánc, karakterlánc,...] | Yes | A tárolóból kitett portok tömbje. |  Nincsenek |
| [`id`](#example-id) | sztring | Yes | Egyedi módon azonosítja a lépést a tevékenységen belül. A feladat egyéb lépései hivatkozhatnak egy adott lépésre `id` , például a függőségi ellenőrzésre a használatával `when` .<br /><br />A a `id` futó tároló neve is. A feladat más tárolókban futó folyamatai a `id` DNS-állomásnévként, illetve a Docker-naplók [id] használatával való elérésére is hivatkozhatnak, például:. | `acb_step_%d`, ahol a a `%d` YAML-fájlban legfelüli lépés 0 alapú indexe |
| `ignoreErrors` | logikai | Yes | Azt jelzi, hogy a lépés sikeres-e, függetlenül attól, hogy hiba történt-e a tároló végrehajtása során. | `false` |
| `isolation` | sztring | Yes | A tároló elkülönítési szintje. | `default` |
| `keep` | logikai | Yes | Azt határozza meg, hogy a lépés tárolóját a végrehajtás után kell-e megőrizni. | `false` |
| `network` | object | Yes | Azonosítja azt a hálózatot, amelyben a tároló fut. | Nincsenek |
| `ports` | [karakterlánc, karakterlánc,...] | Yes | A tárolóból a gazdagépre közzétett portok tömbje. |  Nincsenek |
| `pull` | logikai | Yes | Azt határozza meg, hogy a tároló lekérését kényszerítse-e a gyorsítótár működésének megakadályozása érdekében. | `false` |
| `privileged` | logikai | Yes | Azt határozza meg, hogy a tárolót emelt szintű módban kívánja-e futtatni. | `false` |
| `repeat` | int | Yes | A tárolók végrehajtásának megismétléséhez szükséges újrapróbálkozások száma. | 0 |
| `retries` | int | Yes | Az újrapróbálkozások száma, ha egy tároló nem tudja végrehajtani a végrehajtását. Egy újrapróbálkozás csak akkor próbálkozik, ha egy tároló kilépési kódja nem nulla. | 0 |
| `retryDelay` | int (másodperc) | Yes | A tároló végrehajtásának újrapróbálkozásai közötti késleltetés másodpercben. | 0 |
| `secret` | object | Yes | Azonosít egy Azure Key Vault titkos vagy [felügyelt identitást az Azure-erőforrásokhoz](container-registry-tasks-authentication-managed-identity.md). | Nincsenek |
| `startDelay` | int (másodperc) | Yes | A tároló végrehajtásának késleltetéséhez szükséges másodpercek száma. | 0 |
| `timeout` | int (másodperc) | Yes | A lépés megszakítása előtt legfeljebb ennyi másodpercig futhat. | 600 |
| [`when`](#example-when) | [karakterlánc, karakterlánc,...] | Yes | A feladat egy vagy több lépésének függőségét konfigurálja. | Nincsenek |
| `user` | sztring | Yes | Egy tároló felhasználóneve vagy UID azonosítója | Nincsenek |
| `workingDirectory` | sztring | Yes | Egy lépés munkakönyvtárának beállítása. Alapértelmezés szerint az ACR-feladatok létrehoznak egy gyökérkönyvtárat munkakönyvtárként. Ha azonban a Build több lépésből áll, a korábbi lépések megoszthatják az összetevőket a későbbi lépésekkel, ha ugyanazt a munkakönyvtárat adja meg. | `c:\workspace` Windows vagy `/workspace` Linux rendszeren |

### <a name="volumemount"></a>volumeMount

A volumeMount objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | No | A csatlakoztatni kívánt kötet neve. Pontosan egyeznie kell a tulajdonság nevével `volumes` . | Nincsenek |
| `mountPath`   | sztring | nem | A tárolóban lévő fájlok csatlakoztatásának abszolút elérési útja.  | Nincsenek |

### <a name="examples-task-step-properties"></a>Példák: tevékenység lépés tulajdonságai

#### <a name="example-id"></a>Példa: azonosító

Hozzon létre két rendszerképet, egypéldányos egy funkcionális tesztet. Minden egyes lépést egy egyedi azonosító határoz meg, `id` amely a tevékenység hivatkozásának más lépéseit a `when` tulajdonságában.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Példa: Ha

A `when` tulajdonság határozza meg a lépés függőségét a feladat egyéb lépésein belül. A két paraméter értékét támogatja:

* `when: ["-"]` – Más lépésektől eltérő függőséget jelez. Egy lépés megadásával `when: ["-"]` azonnal megkezdődik a végrehajtás, és lehetővé teszi az egyidejű lépések végrehajtását.
* `when: ["id1", "id2"]` -Azt jelzi, hogy a lépés a `id` "ID1" és a `id` "ID2" lépésektől függ. Ez a lépés nem hajtható végre, amíg a "ID1" és a "ID2" lépések nem állnak készen.

Ha `when` nincs megadva egy lépésben, ez a lépés a fájl előző lépésének befejeződésétől függ `acr-task.yaml` .

Szekvenciális lépés végrehajtása a következő nélkül `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Szekvenciális lépés végrehajtása a következővel `when` :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

A Parallel images Build:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Párhuzamos rendszerképek létrehozása és függő tesztelés:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Változók futtatása

Az ACR-feladatok olyan alapértelmezett változókat tartalmaznak, amelyek a végrehajtás során elvégzendő feladatok végrehajtásához használhatók. Ezek a változók a formátum használatával érhetők el `{{.Run.VariableName}}` , ahol az a `VariableName` következők egyike:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

A változók nevei általában magától értetődőek. A gyakran használt változók részleteinek követése. A YAML verziótól kezdve a `v1.1.0` legtöbb futtatási változó helyett rövidített, előre definiált [feladat-aliast](#aliases) is használhat. Például a helyett `{{.Run.Registry}}` használja az `$Registry` aliast.

### <a name="runid"></a>Run.ID

`az acr run`A-n keresztül létrehozott feladatok minden futtatása, átkapcsolása vagy trigger-alapú végrehajtása `az acr task create` egyedi azonosítóval rendelkezik. Az azonosító a jelenleg futtatott futtatást jelöli.

Általában egy rendszerkép egyedi címkézéséhez használatos:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Futtassa a. SharedVolume

Egy megosztott kötet egyedi azonosítója, amely az összes feladat lépésével elérhető. A kötet `c:\workspace` Windows vagy Linux rendszerhez van csatlakoztatva `/workspace` . 

### <a name="runregistry"></a>A. Registry futtatása

A beállításjegyzék teljesen minősített kiszolgálójának neve. Általában arra használják, hogy általános referenciául szolgáljon arra a beállításjegyzékre, amelyben a feladat fut.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Futtassa a. RegistryName

A tároló-beállításjegyzék neve. Jellemzően olyan feladatokban használatos, amelyek nem igényelnek teljes kiszolgálónevet, például olyan `cmd` lépéseket, amelyek az Azure CLI-parancsokat futtatják a nyilvántartásokban.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Futtatás. dátum

A Futtatás jelenlegi UTC-ideje.

### <a name="runcommit"></a>Futtatás. commit

Egy GitHub-adattárba való véglegesítés által aktivált feladat esetén a véglegesítő azonosító.

### <a name="runbranch"></a>Futtatás. ág

Egy GitHub-adattárba való véglegesítés által aktivált feladatok esetében az ág neve.

## <a name="aliases"></a>Aliasok

A-től kezdve az `v1.1.0` ACR-feladatok a végrehajtáskor a feladatok lépései számára elérhető aliasokat támogatják. Az aliasok a bashben és néhány más parancs-rendszerhéjban támogatott aliasokhoz (parancssori parancsikonok) hasonlóak. 

Alias használatával egyetlen szót is megadhat bármely parancs vagy parancs (beleértve a beállításokat és a fájlneveket is).

Az ACR-feladatok számos előre definiált aliast és egyéni aliast is támogatnak.

### <a name="predefined-aliases"></a>Előre definiált aliasok

A következő feladat-aliasok használhatók a [futtatási változók](#run-variables)helyett:

| Alias | Változó futtatása |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

A feladat lépéseiben a `$` következő példában szereplő alias előtt a direktíva látható:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Rendszerkép-aliasok

A következő aliasok mindegyike stabil képre mutat a Microsoft Container Registryban (MCR). A `cmd` feladatok fájljának szakasza nem tartalmaz további információt.

| Alias | Kép |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

A következő példában szereplő feladat több aliast használ a 7 napnál régebbi képcímkék [törléséhez](container-registry-auto-purge.md) a `samples/hello-world` futtatási beállításjegyzékben:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Egyéni alias

Definiáljon egy egyéni aliast a YAML-fájlban, és használja azt az alábbi példában látható módon. Az alias csak alfanumerikus karaktereket tartalmazhat. Az alias kibontására szolgáló alapértelmezett direktíva a `$` karakter.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Az egyéni alias-definíciók esetében egy távoli vagy egy helyi YAML-fájlhoz is kapcsolódhat. Az alábbi példa egy YAML-fájlra mutató hivatkozásokat tartalmaz az Azure Blob Storage-ban:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Következő lépések

A többlépéses feladatok áttekintését a [több lépésből álló Build-, tesztelési és javítási feladatok futtatása az ACR-feladatokban](container-registry-tasks-multi-step.md)című témakörben tekintheti meg.

Az egylépéses buildek esetében tekintse meg az [ACR-feladatok áttekintését](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
