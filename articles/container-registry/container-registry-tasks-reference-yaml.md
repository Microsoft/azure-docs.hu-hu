---
title: YAML-referencia – ACR-feladatok
description: Referencia a feladatok YAML-ban történő meghatározásához a ACR-feladatok, beleértve a feladattulajdonságokat, a lépéstípusokat, a lépéstulajdonságokat és a beépített változókat.
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 126fcbce0569b2be6d9302cbbb718fa11e3e8046
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780946"
---
# <a name="acr-tasks-reference-yaml"></a>ACR-feladatok referencia: YAML

A többlépéses feladatdefiníció a ACR-feladatok tárolóközpontú számítási primitívet biztosít, amely a tárolók építésére, tesztelésére és javítására összpontosít. Ez a cikk a többlépéses feladatokat meghatározó YAML-fájlokhoz használható parancsokat, paramétereket, tulajdonságokat és szintaxist tartalmazza.

Ez a cikk a többlépéses feladat YAML-fájljainak létrehozására vonatkozó referenciát tartalmaz ACR-feladatok. Ha szeretné bemutatni a ACR-feladatok, tekintse meg a ACR-feladatok [áttekintését.](container-registry-tasks-overview.md)

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml fájlformátum

ACR-feladatok támogatja a többlépéses feladatdeklarációt a szabványos YAML-szintaxisban. A tevékenységek lépéseit YAML-fájlban definiálhatja. Ezután manuálisan futtathatja a feladatot, ha átküldi a fájlt az [az acr run parancsnak.][az-acr-run] Vagy a fájl használatával létrehozhat egy feladatot az [az acr task create][az-acr-task-create] használatával, amely automatikusan aktiválódik egy Git-véglegesítés, egy alapként használt rendszerkép frissítése vagy ütemezés során. Bár ez a cikk a lépéseket tartalmazó fájlként hivatkozik, a ACR-feladatok a támogatott kiterjesztésű érvényes `acr-task.yaml` [fájlnevet támogatja.](#supported-task-filename-extensions)

A legfelső szintű `acr-task.yaml` primitívek a **tevékenységtulajdonságok,** a **lépéstípusok** és a **lépéstulajdonságok:**

* [A feladattulajdonságok](#task-properties) a feladat végrehajtásának összes lépésére vonatkoznak. Számos globális feladattulajdonság van, például:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [A feladatlépés-típusok](#task-step-types) a tevékenységekben elvégezhető műveletek típusait képviselik. Három lépéstípus létezik:
  * `build`
  * `push`
  * `cmd`
* [A feladatlépés tulajdonságai](#task-step-properties) olyan paraméterek, amelyek egy adott lépésre vonatkoznak. Több lépéstulajdonság is van, például:
  * `startDelay`
  * `timeout`
  * `when`
  * ... és még sok más.

A fájl alapformátuma, beleértve a gyakori lépéstulajdonságokat `acr-task.yaml` is, a következő. Bár nem az összes elérhető lépéstulajdonság vagy lépéstípus-használat teljes körű ábrázolása, gyors áttekintést nyújt az alapszintű fájlformátumról.

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

### <a name="supported-task-filename-extensions"></a>Támogatott fájlnévkiterjesztések a feladathoz

ACR-feladatok fájlnévkiterjesztést foglalt le, köztük a fájlt is, amelyek `.yaml` feladatfájlként lesznek feldolgozva. Az  alábbi listában nem található kiterjesztéseket a ACR-feladatok Dockerfile-nak tekintjük: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

Jelenleg a YAML az egyetlen fájlformátum, amelyet a ACR-feladatok. A többi fájlnévkiterjesztés a jövőbeli támogatáshoz van fenntartva.

## <a name="run-the-sample-tasks"></a>A mintafeladatok futtatása

A cikk következő szakaszaiban több minta feladatfájlra is hivatkozunk. A mintafeladatok egy nyilvános GitHub-adattárban vannak, az [Azure-Samples/acr-tasks adattárban.][acr-tasks] Ezeket az az acr run Azure CLI-paranccsal [futtathatja.][az-acr-run] A mintaparancsok a következőre hasonlítanak:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

A mintaparancsok formázása feltételezi, hogy konfigurált egy alapértelmezett beállításjegyzéket az Azure CLI-ban, ezért kihagyják a `--registry` paramétert. Az alapértelmezett beállításjegyzék konfigurálához használja az [az configure][az-configure] parancsot a paraméterrel, `--defaults` amely elfogad egy `acr=REGISTRY_NAME` értéket.

Ha például az Azure CLI-t egy "myregistry" nevű alapértelmezett regisztrációs adatbázissal konfigurálja:

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Feladat tulajdonságai

A feladattulajdonságok általában egy fájl tetején jelennek meg, és olyan globális tulajdonságok, amelyek a feladat lépésének teljes `acr-task.yaml` végrehajtására érvényesek. Ezen globális tulajdonságok némelyike felülbírálható egy adott lépésen belül.

| Tulajdonság | Típus | Választható | Description | Támogatott felülbírálás | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | sztring | Yes | A fájl verziószáma, amelyet a ACR-feladatok `acr-task.yaml` elemez. Bár ACR-feladatok célja a visszamenőleges kompatibilitás fenntartása, ez az érték ACR-feladatok a kompatibilitást egy meghatározott verzión belül. Ha nincs meghatározva, az alapértelmezése a legújabb verzió. | No | Nincsenek |
| `stepTimeout` | int (másodperc) | Yes | Egy lépés futtatásának maximális száma másodpercben. Ha a tulajdonság meg van adva egy feladathoz, akkor beállítja az összes lépés `timeout` alapértelmezett tulajdonságát. Ha `timeout` a tulajdonság meg van adva egy lépésnél, felülírja a feladat által megadott tulajdonságot. | Yes | 600 (10 perc) |
| `workingDirectory` | sztring | Yes | A tároló munkakönyvtára futásidőben. Ha a tulajdonság meg van adva egy feladathoz, akkor beállítja az összes lépés `workingDirectory` alapértelmezett tulajdonságát. Ha egy lépésnél meg van adva, felülírja a feladat által megadott tulajdonságot. | Yes | `c:\workspace` Windows vagy `/workspace` Linux rendszeren |
| `env` | [sztring, sztring, ...] | Yes |  Sztringek tömbje `key=value` formátumban, amely meghatározza a feladat környezeti változóit. Ha a tulajdonság meg van adva egy feladathoz, akkor beállítja az összes lépés `env` alapértelmezett tulajdonságát. Ha egy lépésnél meg van adva, felülírja a feladatból örökölt környezeti változókat. | Yes | Nincsenek |
| `secrets` | [secret, secret, ...] | Yes | Titkos objektumok [tömbje.](#secret) | No | Nincsenek |
| `networks` | [hálózat, hálózat, ...] | Yes | Hálózati objektumok [tömbje.](#network) | No | Nincsenek |
| `volumes` | [kötet, kötet, ...] | Yes | [Kötetobjektumok tömbje.](#volume) Olyan köteteket ad meg, amelyek forrástartalmat csatlakoztatnak egy lépéshez. | No | Nincsenek |

### <a name="secret"></a>titkos kód

A titkos objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | sztring | No | A titkos adat azonosítója. | Nincsenek |
| `keyvault` | sztring | Yes | A Azure Key Vault URL-címe. | Nincsenek |
| `clientID` | sztring | Yes | Az [Azure-erőforrások](container-registry-tasks-authentication-managed-identity.md) felhasználó által hozzárendelt felügyelt identitásának ügyfél-azonosítója. | Nincsenek |

### <a name="network"></a>network

A hálózati objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | No | A hálózat neve. | Nincsenek |
| `driver` | sztring | Yes | A hálózat kezeléséhez szükséges illesztőprogram. | Nincsenek |
| `ipv6` | logikai | Yes | Azt határozza meg, hogy engedélyezve van-e az IPv6-hálózat. | `false` |
| `skipCreation` | logikai | Yes | Ki kell-e hagyni a hálózat létrehozását. | `false` |
| `isDefault` | logikai | Yes | Azt határozza meg, hogy a hálózat egy alapértelmezett hálózat-e, amelyet a Azure Container Registry. | `false` |

### <a name="volume"></a>Kötet

A kötetobjektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | No | A csatlakoztatnia kell a kötet nevét. Csak alfanumerikus karaktereket tartalmazhat( '-') és '_'. | Nincsenek |
| `secret` | map[string]string | No | A térkép minden kulcsa egy, a kötetben létrehozott és kitöltve fájl neve. Minden érték a titkos titkos érték sztringverziója. A titkos értékeknek Base64 kódolásúnak kell lennie. | Nincsenek |

## <a name="task-step-types"></a>Feladatlépés-típusok

ACR-feladatok három lépéstípust támogat. Minden lépéstípus számos tulajdonságot támogat, amelyek részletesen az egyes lépéstípusokat részletező szakaszban találhatóak.

| Lépés típusa | Description |
| --------- | ----------- |
| [`build`](#build) | Egy tároló rendszerképét építi ki az ismerős szintaxis `docker build` használatával. |
| [`push`](#push) | Végrehajt egy újonnan létrehozott vagy retagged rendszerképet `docker push` egy tároló-beállításjegyzékben. Azure Container Registry, más privát beállításregisztrálók és a nyilvános Docker Hub támogatottak. |
| [`cmd`](#cmd) | Parancsként futtat egy tárolót, és paramétereket ad át a `[ENTRYPOINT]` tárolónak. A lépéstípus olyan paramétereket támogat, mint a , és más ismert parancsbeállítások, amelyek lehetővé teszik az egység- és funkcionális tesztelést `cmd` `env` egyidejű `detach` `docker run` tároló-végrehajtással. |

## <a name="build"></a>Épít

Tároló rendszerképének összeállítása. A lépéstípus egy több-bérlős, biztonságos lehetőséget jelent a felhőben első osztályú `build` `docker build` primitívként való futtatáshoz.

### <a name="syntax-build"></a>Szintaxis: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

A `build` lépéstípus az alábbi táblázatban található paramétereket támogatja. A lépéstípus a docker build parancs összes build beállítását is támogatja, például a `build` [](https://docs.docker.com/engine/reference/commandline/build/) `--build-arg` build-time változók beállítását.

| Paraméter | Leírás | Választható |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | A beépített rendszerkép `image:tag` teljes minősítettét határozza meg.<br /><br />Mivel a képek felhasználhatók belső feladatérvényesítéshez, például funkcionális tesztekhez, nem minden rendszerképre van szükség `push` a beállításjegyzékhez. Ahhoz azonban, hogy a rendszerképet egy feladat-végrehajtáson belül példányosodják, a rendszerképnek szüksége van egy névre, amelyre hivatkozni kell.<br /><br />A `az acr build` -ACR-feladatok nem biztosít alapértelmezett leküldési viselkedést. A ACR-feladatok az alapértelmezett forgatókönyv feltételezi, hogy képes rendszerképet összeépíteni, ellenőrizni, majd leküldést használni. A [push (leküldés)](#push) lehetőség az előre felépített rendszerképek opcionális leküldését is bemutatja. | Yes |
| `-f` &#124; `--file` | A számára átadott Docker-fájl `docker build` megadása. Ha nincs megadva, a rendszer a környezet gyökerében található alapértelmezett Dockerfile-t használja. Docker-fájl megadásához adja át a fájlnevet a környezet gyökerének viszonyítva. | Yes |
| `context` | A számára átadott `docker build` gyökérkönyvtár. Az egyes feladatok gyökérkönyvtára egy megosztott [workingDirectory](#task-step-properties)könyvtárra van beállítva, és tartalmazza a társított Git klónozott könyvtár gyökerét. | No |

### <a name="properties-build"></a>Tulajdonságok: build

A `build` lépéstípus az alábbi tulajdonságokat támogatja. A tulajdonságok részleteit a cikk [Feladatlépés tulajdonságai](#task-step-properties) szakaszában találja.

| Tulajdonságok | Típus | Kötelező |
| -------- | ---- | -------- |
| `detach` | logikai | Választható |
| `disableWorkingDirectoryOverride` | logikai | Választható |
| `entryPoint` | sztring | Választható |
| `env` | [sztring, sztring, ...] | Választható |
| `expose` | [sztring, sztring, ...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | logikai | Választható |
| `isolation` | sztring | Választható |
| `keep` | logikai | Választható |
| `network` | object | Választható |
| `ports` | [sztring, sztring, ...] | Választható |
| `pull` | logikai | Választható |
| `repeat` | int | Választható |
| `retries` | int | Választható |
| `retryDelay` | int (másodperc) | Választható |
| `secret` | object | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `volumeMount` | object | Választható |
| `when` | [sztring, sztring, ...] | Választható |
| `workingDirectory` | sztring | Választható |

### <a name="examples-build"></a>Például: build

#### <a name="build-image---context-in-root"></a>Rendszerkép összeállítása – környezet a gyökérben

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Rendszerkép összeállítása – környezet az alkönyvtárban

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>Nyomja

Egy vagy több felépített vagy retagged rendszerkép leküldése egy tároló-beállításjegyzékbe. Támogatja a privát beállításregisztrálókba, például Azure Container Registry vagy a nyilvános regisztrációs Docker Hub.

### <a name="syntax-push"></a>Szintaxis: leküldés

A `push` lépéstípus rendszerképek gyűjteményét támogatja. A YAML-gyűjtemény szintaxisa támogatja a beágyazott és beágyazott formátumokat. Az egyetlen rendszerkép lekért szöveg általában beágyazott szintaxissal van ábrázolva:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

A jobb olvashatóság érdekében használjon beágyazott szintaxist, ha több képet is leküld:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Tulajdonságok: leküldés

A `push` lépéstípus a következő tulajdonságokat támogatja. A tulajdonságok részleteit a cikk [Feladatlépés tulajdonságai](#task-step-properties) szakaszában találja.

| Tulajdonság | Típus | Kötelező |
| -------- | ---- | -------- |
| `env` | [sztring, sztring, ...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | logikai | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `when` | [sztring, sztring, ...] | Választható |

### <a name="examples-push"></a>Példák: leküldés

#### <a name="push-multiple-images"></a>Több rendszerkép leküldése

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Összeállítás, leküldés és futtatás

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>Cmd

A `cmd` lépéstípus egy tárolót futtat.

### <a name="syntax-cmd"></a>Szintaxis: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Tulajdonságok: cmd

A `cmd` lépéstípus a következő tulajdonságokat támogatja:

| Tulajdonság | Típus | Kötelező |
| -------- | ---- | -------- |
| `detach` | logikai | Választható |
| `disableWorkingDirectoryOverride` | logikai | Választható |
| `entryPoint` | sztring | Választható |
| `env` | [sztring, sztring, ...] | Választható |
| `expose` | [sztring, sztring, ...] | Választható |
| `id` | sztring | Választható |
| `ignoreErrors` | logikai | Választható |
| `isolation` | sztring | Választható |
| `keep` | logikai | Választható |
| `network` | object | Választható |
| `ports` | [sztring, sztring, ...] | Választható |
| `pull` | logikai | Választható |
| `repeat` | int | Választható |
| `retries` | int | Választható |
| `retryDelay` | int (másodperc) | Választható |
| `secret` | object | Választható |
| `startDelay` | int (másodperc) | Választható |
| `timeout` | int (másodperc) | Választható |
| `volumeMount` | object | Választható |
| `when` | [sztring, sztring, ...] | Választható |
| `workingDirectory` | sztring | Választható |

Ezeknek a tulajdonságoknak a részleteit a cikk [Feladatlépés tulajdonságai](#task-step-properties) szakaszában találja.

### <a name="examples-cmd"></a>Példák: cmd

#### <a name="run-hello-world-image"></a>A hello-world rendszerkép futtatása

Ez a parancs végrehajtja `hello-world.yaml` a feladatfájlt, amely a [hello-world](https://hub.docker.com/_/hello-world/) képre hivatkozik a Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Bash-rendszerkép futtatása és echo "hello world"

Ez a parancs végrehajtja a feladatfájlt, amely a bash-lemezképre `bash-echo.yaml` hivatkozik a [](https://hub.docker.com/_/bash/) Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Adott Bash-rendszerképcímke futtatása

Egy adott rendszerképverzió futtatásához adja meg a címkét a `cmd` fájlban.

Ez a parancs végrehajtja `bash-echo-3.yaml` a feladatfájlt, amely a [bash:3.0](https://hub.docker.com/_/bash/) rendszerképre hivatkozik a Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Egyéni rendszerképek futtatása

A `cmd` lépéstípus a szabványos formátumot használó képekre `docker run` hivatkozik. A beállításjegyzékben előtaggal nem előtagú rendszerképek erednek a docker.io. Az előző példa a következőként is ábrázolható:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

A szabványos rendszerkép-referencia konvenció használatával a bármely privát regisztrációs adatbázisból vagy a nyilvános adatbázisból `docker run` `cmd` Docker Hub. Ha ugyanabban a beállításjegyzékben, amelyben az ACR-feladat fut, lemezképre hivatkozik, nem kell megadnia a beállításjegyzék hitelesítő adatait.

* Futtatassa az Azure Container Registryből származó rendszerképet. Az alábbi példa feltételezi, hogy van egy nevű regisztrációs adatbázisa és `myregistry` egy egyéni rendszerképe. `myimage:mytag`

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* A beállításjegyzék-hivatkozás általánosításához futtassa a változót vagy aliast

    Ahelyett, hogy a beállításjegyzék nevét egy fájlban kódolódnál, hordozhatóbb lehet `acr-task.yaml` egy Run változó [vagy](#run-variables) alias [használatával.](#aliases) A `Run.Registry` változó vagy alias futásidőben annak a beállításjegyzéknek a nevére bővül, amelyben a feladat `$Registry` fut.

    Ha például az előző feladatot úgy kell általánosítani, hogy az bármely Azure-beli tároló-beállításjegyzékben is működik, hivatkozhat a $Registry változóra a rendszerkép nevében:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Titkos kötetek elérése

A tulajdonság lehetővé teszi a kötetek és azok titkos tartalmának és lépésének `volumes` `build` `cmd` beállítását egy feladatban. Minden lépésen belül egy opcionális tulajdonság listázza a köteteket és a megfelelő tárolóútvonalat, amelyek a tárolóhoz `volumeMounts` csatlakoztatva vannak az adott lépésben. A titkos kulcsok fájlként vannak megtéve az egyes kötetek csatlakoztatási útvonalán.

Hajtson végre egy feladatot, és csatlakoztassa a két titkos kódot egy lépéshez: egyet egy kulcstartóban, egyet pedig a parancssorban tárol:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Feladatlépés tulajdonságai

Minden lépéstípus számos, a típusának megfelelő tulajdonságot támogat. Az alábbi táblázat az összes elérhető lépéstulajdonság meghatározására használható. Nem minden lépéstípus támogatja az összes tulajdonságot. Az egyes lépéstípushoz elérhető tulajdonságokat a [cmd,](#cmd) [build](#build), [és](#push) leküldéses lépéstípus referenciaszakaszában láthatja.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | logikai | Yes | Azt határozza meg, hogy a tárolót le kell-e leválasztani a futtatáskor. | `false` |
| `disableWorkingDirectoryOverride` | logikai | Yes | Letiltja-e `workingDirectory` a felülbírálási funkciót. Ezt a és a együttes használatával teljes körűen szabályozhatja a tároló `workingDirectory` munkakönyvtára felett. | `false` |
| `entryPoint` | sztring | Yes | Felülbírálja `[ENTRYPOINT]` egy lépés tárolóját. | Nincsenek |
| `env` | [sztring, sztring, ...] | Yes | Sztringek tömbje `key=value` formátumban, amely meghatározza a lépés környezeti változóit. | Nincsenek |
| `expose` | [sztring, sztring, ...] | Yes | A tárolóból elérhető portok tömbje. |  Nincsenek |
| [`id`](#example-id) | sztring | Yes | Egyedileg azonosítja a lépést a feladaton belül. A feladat további lépései hivatkozni tudnak a lépésre, például a függőségek a-val `id` való `when` ellenőrzéshez.<br /><br />A `id` a futó tároló neve is. A feladat más tárolóiban futó folyamatok hivatkoznak a névre DNS-állomásnévként, vagy a `id` docker-naplók [id] használatával való elérésére, például. | `acb_step_%d`, ahol a a YAML-fájl fentről lefelé mutató lépésének `%d` 0-alapú indexe |
| `ignoreErrors` | logikai | Yes | A lépés sikeresként való megjelölése attól függetlenül, hogy hiba történt-e a tároló végrehajtása során. | `false` |
| `isolation` | sztring | Yes | A tároló elkülönítési szintje. | `default` |
| `keep` | logikai | Yes | Meg kell-e tartani a lépés tárolóját a végrehajtás után. | `false` |
| `network` | object | Yes | Azonosítja a hálózatot, amelyen a tároló fut. | Nincsenek |
| `ports` | [sztring, sztring, ...] | Yes | A tárolóból a gazdagépre közzétett portok tömbje. |  Nincsenek |
| `pull` | logikai | Yes | Kényszerítse-e a tároló lekényszerését a végrehajtása előtt, hogy megakadályozza a gyorsítótárazás működését. | `false` |
| `privileged` | logikai | Yes | Azt határozza meg, hogy a tároló emelt szintű módban fusson-e. | `false` |
| `repeat` | int | Yes | A tároló végrehajtásának megismétlhető újrakontrasztott száma. | 0 |
| `retries` | int | Yes | A megkísérelni kívánt újra megkísérelt próbálkozások száma, ha egy tároló végrehajtása meghiúsul. A rendszer csak akkor kísérel meg újrapróbálkozásokat, ha a tároló kilépési kódja nem nulla. | 0 |
| `retryDelay` | int (másodperc) | Yes | A tároló végrehajtásának újrakontrasztása közötti késleltetés másodpercben. | 0 |
| `secret` | object | Yes | Egy titkos Azure Key Vault vagy [felügyelt identitást azonosít az Azure-erőforrásokhoz.](container-registry-tasks-authentication-managed-identity.md) | Nincsenek |
| `startDelay` | int (másodperc) | Yes | A tároló végrehajtásának késleltetése másodpercben. | 0 |
| `timeout` | int (másodperc) | Yes | Egy lépés végrehajtása előtt végrehajtható másodpercek maximális száma. | 600 |
| [`when`](#example-when) | [sztring, sztring, ...] | Yes | Konfigurálja egy lépés függőségét a feladat egy vagy több másik lépése alapján. | Nincsenek |
| `user` | sztring | Yes | Egy tároló felhasználóneve vagy UID-ja | Nincsenek |
| `workingDirectory` | sztring | Yes | Beállítja egy lépés munkakönyvtárát. Alapértelmezés szerint a ACR-feladatok létrehoz egy gyökérkönyvtárat munkakönyvtárként. Ha azonban a build több lépésből áll, a korábbi lépések megoszthatják az összetevőket a későbbi lépésekkel, ha ugyanazt a munkakönyvtárat határozzák meg. | `c:\workspace` Windows vagy `/workspace` Linux rendszeren |

### <a name="volumemount"></a>volumeMount (kötet mennyisége)

A volumeMount objektum a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Típus | Választható | Description | Alapértelmezett érték |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | sztring | No | A csatlakoztatnia kell a kötet nevét. Pontosan meg kell egyeznie egy tulajdonság `volumes` nevével. | Nincsenek |
| `mountPath`   | sztring | nem | A fájlok tárolóban való csatlakoztatásának abszolút elérési útja.  | Nincsenek |

### <a name="examples-task-step-properties"></a>Példák: Feladatlépés tulajdonságai

#### <a name="example-id"></a>Például: id

Két rendszerképet kell összeépítenie, amelyek egy funkcionális tesztképet instabilizálnak. Az egyes lépéseket egy egyedi azonosítja, amely a feladathivatkozásban található további lépések `id` `when` tulajdonságában található.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Például: when

A tulajdonság határozza meg egy lépés függőségét a feladat `when` más lépéseitől. Két paraméterértéket támogat:

* `when: ["-"]` – Azt jelzi, hogy a többi lépéstől nem függ. A megadásával egy lépés azonnal megkezdi a `when: ["-"]` végrehajtást, és lehetővé teszi az egyidejű lépésvégrehajtást.
* `when: ["id1", "id2"]` – Azt jelzi, hogy a lépés az `id` "id1" és `id` az "id2" értékekkel való lépésektől függ. Ez a lépés addig nem lesz végrehajtva, amíg az "id1" és az "id2" lépés be nem fejeződik.

Ha nincs megadva egy lépésben, akkor az a fájl előző lépésének `when` befejezésétől `acr-task.yaml` függ.

Szekvenciális lépés végrehajtása `when` nélkül:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Szekvenciális lépés végrehajtása a `when` következővel:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Párhuzamos rendszerképek összeállítása:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Párhuzamos rendszerkép-összeállítás és függő tesztelés:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Változók futtatása

ACR-feladatok alapértelmezett változókészletet tartalmaz, amelyek a végrehajtásukkor a feladat lépései számára érhetők el. Ezek a változók a következő formátumban érhetők `{{.Run.VariableName}}` `VariableName` el:

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

A változónevek általában magától értetődőek. A gyakran használt változók részletei a következők. A YAML verziójától függően a legtöbb futtatás változója helyén használhat rövidített, előre definiált tevékenység `v1.1.0` aliast. [](#aliases) A helyén például `{{.Run.Registry}}` használja az `$Registry` aliast.

### <a name="runid"></a>Run.ID

A (futtatás) és a (eseményindítók) segítségével létrehozott tevékenységek minden egyes futtatása egyedi `az acr run` `az acr task create` azonosítóval rendelkezik. Az azonosító az aktuálisan végrehajtott futtatásnak megfelelő.

Általában képek egyedi címkézésére használatos:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

Egy megosztott kötet egyedi azonosítója, amely minden feladat lépése számára elérhető. A kötet Windows vagy Linux rendszeren csatlakozik a `c:\workspace` `/workspace` kötethez. 

### <a name="runregistry"></a>Run.Registry

A beállításjegyzék teljes kiszolgálóneve. Általában arra a beállításjegyzékre való általános hivatkozáshoz használatos, ahol a feladat fut.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

A tároló-beállításjegyzék neve. Általában olyan lépésekben használatos, amelyek nem igényelnek teljes kiszolgálónevet, például olyan lépésekben, amelyek Azure CLI-parancsokat futtatnak a `cmd` regisztrációs adatbázison.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date (Futtatás dátuma)

A futtatás aktuális UTC-ideje.

### <a name="runcommit"></a>Run.Commit

A GitHub-adattárban való véglegesítés által aktivált feladatokhoz a véglegesítés azonosítóját.

### <a name="runbranch"></a>Run.Branch

GitHub-adattárban való véglegesítés által aktivált feladat esetén az ág neve.

## <a name="aliases"></a>Aliasok

A -ACR-feladatok támogatja azokat az aliasokat, amelyek a végrehajtásukkor a tevékenység `v1.1.0` lépései számára elérhetők. Az aliasok a koncepciójukban hasonlóak a Bashben és más parancs-rendszerhéjakban támogatott aliasokhoz (parancsparancsokhoz). 

Alias használatával egyetlen szó beírásával bármilyen parancsot vagy parancscsoportot (beleértve a beállításokat és a fájlneveket) elindíthat.

ACR-feladatok előre definiált aliasokat és a létrehozott egyéni aliasokat is támogatja.

### <a name="predefined-aliases"></a>Előre definiált aliasok

Futtatás változók helyére a következő tevékenység-aliasok [használhatók:](#run-variables)

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

A feladat lépéseinél megelőzheti az aliast a (ebben a `$` példában):

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Kép aliasok

Az alábbi aliasok mind egy stabil rendszerképre mutatnak a Microsoft Container Registry (MCR). Mindegyikre hivatkozhat `cmd` egy feladatfájl szakaszában, egy irányelv használata nélkül.

| Alias | Kép |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Az alábbi példafeladat több [](container-registry-auto-purge.md) aliast használ a 7 napnál régebbi képcímkék kiürítéséhez a futtatás beállításjegyzékében a `samples/hello-world` repoban:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Egyéni alias

Definiálhat egy egyéni aliast a YAML-fájlban, és használhatja az alábbi példában látható módon. Az aliasok csak alfanumerikus karaktereket tartalmazhatnak. Az aliasok kibontásához használt alapértelmezett irányelv a `$` karakter.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Az egyéni aliasdefiníciókhoz távoli vagy helyi YAML-fájlra hivatkozhat. Az alábbi példa egy YAML-fájlra mutat az Azure Blob Storage-ban:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Következő lépések

A többlépéses feladatok áttekintését lásd: Többlépéses build, tesztelés és javítás futtatása a [ACR-feladatok.](container-registry-tasks-multi-step.md)

Az egylépéses buildekért lásd a ACR-feladatok [áttekintését.](container-registry-tasks-overview.md)



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-configure]: /cli/azure/reference-index#az_configure
