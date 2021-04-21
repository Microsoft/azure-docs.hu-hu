---
title: Címkék és jegyzékek végleges kiürítése
description: A végleges törlési paranccsal több címkét és jegyzékfájlt törölhet egy Azure Container Registryből az életkor és a címkeszűrő alapján, valamint igény szerint ütemezheti a végleges törlési műveleteket.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 562d1940459cb1594b7cd9aca2af280b05a4e419
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784190"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Rendszerképek automatikus kiürítása egy Azure-beli tároló-beállításjegyzékből

Ha egy Azure Container Registryt egy fejlesztési munkafolyamat részeként használ, a regisztrációs adatbázis gyorsan feltöltheti a rendszerképeket vagy más, rövid idő után nem szükséges munkaegységeket. Érdemes lehet törölni az összes olyan címkét, amely egy adott időtartamnál régebbi, vagy meg szeretne egyezni egy megadott névszűrővel. Több összetevő gyors törléséhez ez a cikk bemutatja az igény szerinti vagy ütemezett `acr purge` ACR-feladatként futtatható parancsot. [](container-registry-tasks-scheduled.md) 

A parancs jelenleg egy nyilvános tároló rendszerképében () van elosztva, amely `acr purge` a `mcr.microsoft.com/acr/acr-cli:0.4` GitHub [acr-cli adattárának](https://github.com/Azure/acr-cli) forráskódja alapján készült.

Az ebben a cikkben Azure Cloud Shell ACR-feladatok példái az Azure CLI helyi telepítésével vagy használatával futtathatók. Ha helyileg szeretné használni, a 2.0.76-os vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install]. 

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

> [!WARNING]
> A parancsot `acr purge` elővigyázatossággal használja – a törölt képadatok NEM ÁLLÍTHATÓK VISSZA. Ha vannak olyan rendszerei, amelyek jegyzékkivonat alapján (és nem a rendszerkép neve alapján) leküldik a rendszerképeket, ne véglegesen kiürítsen nem látható képeket. A nem látható lemezképek törlésével megakadályozható, hogy ezek a rendszerek leküldhethedhednek a lemezképek a regisztrációs adatbázisból. A jegyzékfájlos lehúzás helyett  érdemes lehet egy egyedi címkézési sémát használni, amely az [ajánlott eljárás.](container-registry-image-tag-version.md)

Ha egyetlen rendszerképcímkét vagy jegyzékfájlt szeretne törölni Azure CLI-parancsokkal, tekintse meg a Tárolólemezképek törlése a [Azure Container Registry.](container-registry-delete.md)

## <a name="use-the-purge-command"></a>A véglegesen véglegesen kiürítés parancs használata

A tárolóparancs címkék alapján törli azokat a képeket egy adattárban, amelyek megfelelnek egy névszűrőnek, és amelyek a megadott időtartamnál `acr purge` régebbiek. Alapértelmezés szerint csak a címkehivatkozások törlődnek, a [mögöttes jegyzékek](container-registry-concepts.md#manifest) és a rétegadatok nem. A parancsban lehetősége van jegyzékfájlokat is törölni. 

> [!NOTE]
> `acr purge` nem töröl olyan képcímkét vagy adattárat, ahol az `write-enabled` attribútum értéke `false` . További információ: [Tároló rendszerképének zárolása egy Azure Container Registryben.](container-registry-image-lock.md)

`acr purge` A úgy lett kialakítva, hogy tárolóparancsként fusson egy [ACR-feladatban,](container-registry-tasks-overview.md)így automatikusan hitelesíti magát a beállításjegyzékben, ahol a feladat fut, és ott hajt végre műveleteket. A cikkben található példafeladatok a parancs aliasát használják egy teljes tároló `acr purge` rendszerkép-parancsa helyére. [](container-registry-tasks-reference-yaml.md#aliases)

A futtatásakor legalább a következőket adja `acr purge` meg:

* `--filter` – Egy adattár és egy *reguláris kifejezés* az adattárban a címkék szűréséhez. Példák: az adattárban az összes címkére illeszkedik, és a kezdettől kezdődő `--filter "hello-world:.*"` `hello-world` `--filter "hello-world:^1.*"` `1` címkékre illeszkedik. Több `--filter` paramétert ad át több adattár kiürítésén.
* `--ago` – Egy Go stílusú [időtartam sztring,](https://golang.org/pkg/time/) amely azt az időtartamot jelzi, amelyen túl a képek törölve vannak. Az időtartam egy vagy több tizedes törtből áll, amelyek mindegyikét egy egység utótagja tartalmazza. Az érvényes időegységek közé tartozik a "d" a napokhoz, a "h" az órákhoz, az "m" pedig a percek száma. Például kiválasztja az összes olyan szűrt képet, amely 2 napnál, 3 óránál és 6 perccel ezelőtt lett utoljára módosítva, és kiválasztja az `--ago 2d3h6m` 1,5 órával ezelőtt utoljára módosított `--ago 1.5h` képeket.

`acr purge` A több választható paramétert is támogat. Ebben a cikkben a következő két példát használjuk:

* `--untagged` – Meghatározza, hogy a társított címkékkel nem *(címkézetlen* jegyzékekkel) nem társított jegyzékek törlődnek.
* `--dry-run` – Azt adja meg, hogy a rendszer nem töröl adatokat, de a kimenet ugyanaz, mintha a parancs a jelző nélkül fut. Ez a paraméter a végleges törlési parancs teszteléséhez hasznos, hogy biztosan ne törölje véletlenül a megőrizni kívánt adatokat.
* `--keep` – Megadja, hogy a rendszer a legfrissebb x számú törölt címkét őrizze meg.
* `--concurrency` – Meghatározza az egyidejűleg feldolgozható véglegesen véglegesen kiüríthető tevékenységek számát. Ha ez a paraméter nincs megadva, a rendszer egy alapértelmezett értéket használ.

További paraméterekért futtassa a `acr purge --help` et. 

`acr purge`A támogatja a ACR-feladatok egyéb [](container-registry-tasks-reference-yaml.md#run-variables) funkcióit, [](container-registry-tasks-logs.md) beleértve a futtatott változókat és a streamként streamelhető és a későbbi lekéréshez mentett feladatfuttassa naplókat.

### <a name="run-in-an-on-demand-task"></a>Futtatás igény szerinti feladatban

Az alábbi példa az [az acr run parancsot][az-acr-run] használja a parancs igény szerinti `acr purge` futtatásához. Ez a példa törli a myregistry adattárában az 1 nappal ezelőtt módosított összes képcímkét és `hello-world` jegyzékfájlt.  A tárolóparancsot a rendszer egy környezeti változó használatával ad át. A feladat forráskörnyezet nélkül fut.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Futtatás ütemezett feladatban

Az alábbi példa az [az acr task create][az-acr-task-create] parancsot használja egy napi ütemezésű [ACR-feladat létrehozásához.](container-registry-tasks-scheduled.md) A tevékenység kiüríti a 7 nappal ezelőtt módosított címkéket az `hello-world` adattárban. A tárolóparancsot a rendszer egy környezeti változó használatával ad át. A feladat forráskörnyezet nélkül fut.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Futtassa [az az acr task show parancsot][az-acr-task-show] annak végrehajtásához, hogy az időzítő eseményindító konfigurálva van-e.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Nagy számú címke és jegyzék végleges végleges kiürítése

Nagy mennyiségű címke és jegyzék végleges végleges kiürítése több percig vagy tovább is tarthat. Címkék és jegyzékek ezreinek kiürítése érdekében előfordulhat, hogy a parancsnak hosszabb ideig kell futnia, mint az alapértelmezett 600 másodperces időtúllépési idő egy igény szerinti feladat esetén, vagy 3600 másodpercet egy ütemezett feladatnál. Ha túllépi az időkorlátot, a rendszer csak a címkék és jegyzékek egy részkészletét törli. A nagy léptékű végleges végleges kiürítés befejezéséhez adja át a paramétert `--timeout` az érték növeléséhez. 

Az alábbi igény szerinti tevékenység például 3600 másodperces (1 órás) időtúllépési időt állít be:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Példa: Egy beállításjegyzék több adattárának ütemezett végleges végleges kiürítés

Ez a példa végigvezeti a beállításjegyzék több adattárának rendszeres tisztításán `acr purge` a használatával. Előfordulhat például, hogy van egy fejlesztési folyamat, amely rendszerképeket ad le a `samples/devimage1` és `samples/devimage2` a adattárba. A fejlesztési lemezképeket rendszeresen importálja az üzemelő példányok éles adattárába, így már nincs szüksége a fejlesztési lemezképre. Heti rendszerességgel kiüríti a és a adattárat a következő heti `samples/devimage1` `samples/devimage2` munkára való felkészüléshez.

### <a name="preview-the-purge"></a>A véglegesen véglegesen véglegesen véglegesen

Az adatok törlése előtt javasoljuk, hogy futtason egy igény szerinti végleges törlési feladatot a `--dry-run` paraméterrel. Ezzel a beállítással az adatok eltávolítása nélkül láthatja a parancs által véglegesen kiüríthető címkéket és jegyzékeket. 

A következő példában az egyes adattárak szűrői az összes címkét kiválasztják. A paraméter megegyezik a szűrőkkel egyező adattárakban az összes `--ago 0d` életkorú képekkel. Módosítsa a forgatókönyvhöz szükséges kiválasztási feltételeket. A `--untagged` paraméter a címkék mellett jegyzékfájlokat is töröl. A tárolóparancsot a rendszer egy környezeti változó használatával továbbadja az [az acr run][az-acr-run] parancsnak.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Tekintse át a parancs kimenetét a kijelölési paramétereknek megfelelő címkék és jegyzékek áttekintésével. Mivel a parancs a paranccsal `--dry-run` fut, nem törlődnek adatok.

Példa a kimenetre:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>A véglegesen véglegesen kiürítés ütemezése

A teszt ellenőrzése után hozzon létre egy ütemezett feladatot a véglegesen véglegesen törölje az automatizálni. Az alábbi példa egy heti feladatot ütemez vasárnap 1:00-kor (UTC) az előző kiürítés parancs futtatásához:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Futtassa [az az acr task show parancsot][az-acr-task-show] annak végrehajtásához, hogy az időzítő eseményindító konfigurálva van-e.

## <a name="next-steps"></a>Következő lépések

További információ a képadatok törlésének egyéb [lehetőségeiről a](container-registry-delete.md) Azure Container Registry.

A rendszerképtárolással kapcsolatos további információkért lásd: [Container Image Storage in Azure Container Registry](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
