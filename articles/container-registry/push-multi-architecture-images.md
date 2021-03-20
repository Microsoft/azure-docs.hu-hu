---
title: Több architektúrával rendelkező rendszerképek a beállításjegyzékben
description: Az Azure Container Registry használata többplatformos (Multi-Arch) rendszerképek készítéséhez, importálásához, tárolásához és üzembe helyezéséhez
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: f8467cd3108ae4faea9ecb4c9d9ae339f476c311
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100012311"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Több architektúrát tartalmazó lemezképek az Azure Container registryben

Ez a cikk bemutatja a *többarchitektúrás (többplatformos* ) rendszerképeket, és azt, hogy miként használhatók a Azure Container Registry funkciók a létrehozásához, tárolásához és használatához. 

A több boltíves képek olyan típusú tárolók, amelyek különböző architektúrák variációit kombinálják, és esetenként különböző operációs rendszerekhez. Ha többarchitektúrás támogatással rendelkező rendszerképet futtat, a Container clients automatikusan kiválasztja az operációs rendszerhez és az architektúrához illő képváltozatot.

## <a name="manifests-and-manifest-lists"></a>Jegyzékek és jegyzékek listája

A több boltíves lemezképek a képjegyzékek és a jegyzékek listáján alapulnak.

### <a name="manifest"></a>Jegyzék

Minden egyes tárolói képet egy [jegyzékfájl](container-registry-concepts.md#manifest)képvisel. A manifest egy olyan JSON-fájl, amely egyedileg azonosítja a rendszerképet, a rétegekre és a hozzájuk tartozó méretekre hivatkozva. 

A Linux-rendszerképek alapszintű jegyzékfájlja `hello-world` a következőhöz hasonlóan néz ki:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Azure Container Registry jegyzékfájlt a Azure Portal vagy az eszközök, például az az [ACR repository show-Manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) parancs használatával tekintheti meg az Azure CLI-ben.

### <a name="manifest-list"></a>Jegyzékfájlok listája

Egy több ív (OCI-lemezképek [képindexe](https://github.com/opencontainers/image-spec/blob/master/image-index.md) ) egy több Arch-lemezképhez tartozó *jegyzékfájl-lista* a lemezképek gyűjteménye (indexe), amelyet egy vagy több rendszerkép nevének megadásával hozhat létre. Ez tartalmazza az egyes képek részleteit, például a támogatott operációs rendszert és architektúrát, a méretet és a jegyzékfájlok kivonatát. A manifest-lista ugyanúgy használható, mint a rendszerkép neve a `docker pull` és a `docker run` parancsokban. 

A `docker` CLI a [Docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) parancs használatával kezeli a jegyzékfájlokat és a jegyzékek listáját.

> [!NOTE]
> A `docker manifest` parancs és az alparancsok jelenleg kísérleti jellegűek. A kísérleti parancsok használatával kapcsolatos részletekért tekintse meg a Docker dokumentációját.

A jegyzékfájlok listáját a parancs használatával tekintheti meg `docker manifest inspect` . Az alábbiakban látható a több-boltíves rendszerkép kimenete `mcr.microsoft.com/mcr/hello-world:latest` , amelynek három jegyzékfájlja van: kettő a Linux operációs rendszer architektúrái számára, egy pedig egy Windows-architektúra.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Ha a Azure Container Registry több Arch-alapú jegyzékfájlt tárol, akkor a jegyzékfájlt a Azure Portal használatával vagy olyan eszközökkel is megtekintheti, mint például az az [ACR repository show-Manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) paranccsal.

## <a name="import-a-multi-arch-image"></a>Multi-Arch rendszerkép importálása 

Egy meglévő multi-Arch rendszerkép importálható egy Azure Container registrybe az az [ACR import](/cli/azure/acr#az_acr_import) paranccsal. A Képimportálási szintaxis megegyezik egy egyarchitektúrás képpel. Az egyarchitektúrás rendszerkép importálásához hasonlóan a multi-Arch rendszerkép importálása nem használ Docker-parancsokat. 

Részletekért lásd: [tároló lemezképek importálása tároló-beállításjegyzékbe](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Több ív rendszerképek leküldése

Ha munkafolyamatokat hoz létre a különböző architektúrákat tároló lemezképek létrehozásához, kövesse az alábbi lépéseket egy több ív rendszerképek Azure Container registrybe való leküldéséhez.

1. Címkézheti és leküldheti az egyes architektúra-specifikus rendszerképeket a tároló-beállításjegyzékbe. Az alábbi példa két linuxos architektúrát feltételez: arm64 és amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Futtassa a parancsot `docker manifest create` , és hozzon létre egy jegyzéket, amely az előző képeket több Arch-rendszerképbe egyesíti.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Küldje le a jegyzékfájlt a tároló-beállításjegyzékbe a következő használatával `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Az `docker manifest inspect` paranccsal megtekintheti a jegyzékfájlok listáját. A parancs kimenetének egy példája az előző szakaszban látható.

Miután leküldte a multi-Arch jegyzékfájlt a beállításjegyzékbe, ugyanúgy dolgozhat a multi-Arch rendszerképpel, mint az egyetlen architektúrával ellátott képpel. Például lekérheti a képet a használatával `docker pull` , és az [az ACR adattár](/cli/azure/acr/repository#az_acr_repository) parancsaival megtekintheti a címkék, a jegyzékfájlok és a képek egyéb tulajdonságait.

## <a name="build-and-push-a-multi-arch-image"></a>Több ív rendszerképek létrehozása és leküldése

Az ACR- [feladatok](container-registry-tasks-overview.md)funkcióinak használatával több ív rendszerképet hozhat létre és küldhet az Azure Container registrybe. Definiáljon például egy [többlépéses feladatot](container-registry-tasks-multi-step.md) egy olyan YAML-fájlban, amely egy Linux multi-Arch-rendszerképet hoz létre.

Az alábbi példa azt feltételezi, hogy külön Dockerfiles van két architektúrához, a arm64 és az AMD64-hez. Felépíti és leküldi az architektúra-specifikus rendszerképeket, majd létrehoz és leküld egy, a címkével ellátni kívánt multi-Arch jegyzékfájlt `latest` :

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Következő lépések

* Az [Azure-folyamatok](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) használatával különböző architektúrák számára készíthet tároló-lemezképeket.
* További információ a többplatformos rendszerképek létrehozásáról a kísérleti Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) beépülő modullal.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
