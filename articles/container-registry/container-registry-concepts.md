---
title: A jegyzékek, adattárak, rendszerképek és összetevők
description: Az Azure Container-nyilvántartások,-tárak,-tároló-lemezképek és egyéb összetevők főbb fogalmai.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578093"
---
# <a name="about-registries-repositories-and-artifacts"></a>A jegyzékek, adattárak és összetevők

Ez a cikk bemutatja a tároló-beállításjegyzékek,-adattárak és-tárolók, valamint a kapcsolódó összetevők főbb fogalmait. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Beállításjegyzék, adattárak és összetevők":::

## <a name="registry"></a>Regisztrációs adatbázis

A Container *Registry* egy olyan szolgáltatás, amely tároló-lemezképek és kapcsolódó összetevők tárolására és terjesztésére szolgál. A Docker hub egy példa egy nyilvános tároló-beállításjegyzékre, amely a Docker-tárolók rendszerképeinek általános katalógusa. Azure Container Registry lehetővé teszi a felhasználók számára, hogy közvetlen hozzáférést biztosítanak a tárolók tartalmához, integrált hitelesítéssel, valamint a globális elosztást és a megbízhatóságot támogató [geo-replikációval](container-registry-geo-replication.md) a hálózattal szoros üzembe helyezések, [a virtuális hálózati konfiguráció, a titkos kapcsolat, a](container-registry-private-link.md) [címkézés](container-registry-image-lock.md)és számos más továbbfejlesztett funkció segítségével 

A Docker-kompatibilis tároló lemezképeken kívül a Azure Container Registry számos [tartalmi](container-registry-image-formats.md) összetevőt támogat, beleértve a Helm-diagramokat és a nyílt tárolós kezdeményezés (OCI) képformátumait.

## <a name="repository"></a>Adattár

A *tárház* tároló-lemezképek vagy más olyan összetevők gyűjteménye, amelyek azonos névvel, de más címkékkel rendelkeznek. Például a következő három rendszerkép található az `acr-helloworld` adattárban:

- *ACR-HelloWorld: legújabb*
- *ACR-HelloWorld: v1*
- *ACR-HelloWorld: v2*

A tárház neve tartalmazhat [névtereket](container-registry-best-practices.md#repository-namespaces)is. A névterek lehetővé teszik a kapcsolódó adattárak és az összetevők tulajdonjogának azonosítását a szervezetben a perjelek által tagolt nevek használatával. A beállításjegyzék azonban egymástól függetlenül kezeli az összes tárházat, nem pedig a hierarchiát. Például:

- *marketing/campaign10-18/Web: v2*
- *marketing/campaign10-18/API: v3*
- *marketing/campaign10-18/e-mail – Feladó: v2*
- *Product-Returns/web-beküldési: 20180604*
- *Product-Returns/Legacy-integrátor: 20180715*

A tárház neve csak kisbetűs alfanumerikus karaktereket, pontokat, kötőjeleket, aláhúzásokat és perjeleket tartalmazhat. 

A Tárházak elnevezési szabályainak elvégzéséhez tekintse [meg a nyílt tároló kezdeményezésének terjesztési specifikációját](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Összetevő

A beállításjegyzékben található tároló-rendszerkép vagy más összetevő egy vagy több címkével van társítva, egy vagy több réteget tartalmaz, és egy jegyzékfájl azonosítja. Az összetevők egymáshoz való viszonyának megértése segíthet a beállításjegyzék hatékony kezelésében.

### <a name="tag"></a>Címke

A rendszerkép vagy más összetevő *címkéje* a verziószámát adja meg. A tárházon belüli egyetlen összetevőhöz egy vagy több címke rendelhető, és az is lehet, hogy "címkézetlen". Ez azt eredményezheti, hogy az összes címkét törölheti egy képből, míg a rendszerképben lévő adatok (a rétegek) a beállításjegyzékben maradnak.

A tárház (vagy a tárház és a névtér), valamint a címke határozza meg a rendszerkép nevét. A képek leküldéséhez és lekéréséhez adja meg a nevét a leküldéses vagy a lekérési műveletben. Alapértelmezés szerint a címkét akkor `latest` használja a rendszer, ha nem ad meg egyet a Docker-parancsokban.

A tároló-lemezképek címkézésének módját a forgatókönyvek segítségével fejlesztheti vagy helyezheti üzembe. A stabil címkék például az alaplemezképek fenntartásához, valamint a rendszerképek telepítéséhez szükséges egyedi címkékhez ajánlottak. További információ: [a címkézéssel kapcsolatos javaslatok és a tárolók verziószámozása](container-registry-image-tag-version.md).

A címkék elnevezési szabályairól a [Docker dokumentációjában](https://docs.docker.com/engine/reference/commandline/tag/)talál további információt.

### <a name="layer"></a>Réteg

A tároló lemezképei és összetevői egy vagy több *rétegből* állnak. A különböző típusú összetevők eltérő módon határozzák meg a rétegeket. Például egy Docker-tároló rendszerképében minden réteg a Docker egy olyan sorának felel meg, amely a rendszerképet definiálja:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Egy tároló képének rétegei":::

A beállításjegyzékben szereplő összetevők közös rétegekkel rendelkeznek, és növelik a tárolási hatékonyságot. Előfordulhat például, hogy a különböző adattárakban több rendszerkép közös ASP.NET Core alapréteggel rendelkezik, de az adott rétegnek csak egy példánya van tárolva a beállításjegyzékben. A réteg megosztása a rétegek közötti eloszlást is optimalizálja a csomópontokra, és több összetevővel közös rétegeket oszt meg. Ha egy csomóponton már található rendszerkép a ASP.NET Core réteget tartalmazza alapértékként, akkor egy másik, ugyanarra a rétegre hivatkozó rendszerkép későbbi lekérése nem továbbítja a réteget a csomópontra. Ehelyett a csomóponton már meglévő rétegre hivatkozik.

A lehetséges rétegbeli manipulációk biztonságos elkülönítésének és védelmének biztosításához a rétegek nem oszthatók meg a kibocsátásiegység-forgalmi jegyzékek között.

### <a name="manifest"></a>Jegyzék

A tároló-beállításjegyzékbe leküldött összes tároló-rendszerkép vagy-összetevő egy *jegyzékfájlhoz* van társítva. A beállításjegyzék által a tartalom leküldésekor generált jegyzékfájl, amely egyedileg azonosítja az összetevőket, és megadja a rétegeket. A tárházhoz tartozó jegyzékfájlokat az az [ACR repository show-Manifests][az-acr-repository-show-manifests]paranccsal listázhatja az Azure CLI parancs használatával. 

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

A tárházhoz tartozó jegyzékfájlok listáját az Azure CLI parancs az [ACR repository show-Manifests][az-acr-repository-show-manifests]paranccsal listázhatja:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Sorolja fel például az "ACR-HelloWorld" adattár jegyzékfájlit:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Jegyzékfájl-kivonat

A jegyzékfájlokat egy egyedi SHA-256 kivonat, vagy egy *manifest Digest* azonosítja. Minden rendszerkép vagy összetevő – akár címkézett, akár nem – azonosítja a kivonatát. A kivonatoló érték akkor is egyedi, ha az összetevő rétegének adatmennyisége megegyezik egy másik összetevővel. Ez a mechanizmus lehetővé teszi, hogy az azonos módon címkézett képeket ismételten leküldse egy beállításjegyzékbe. Előfordulhat például, hogy többször is leküldi `myimage:latest` a beállításjegyzéket, mert hiba történt, mivel az egyes rendszerképeket az egyedi kivonata azonosítja.

A lekéréses műveletben megadhatja a kivonatot a beállításjegyzékből. Előfordulhat, hogy néhány rendszer úgy van konfigurálva, hogy a kivonatoló lekérése miatt lekérje a rendszerkép verziószámát, még akkor is, ha az azonos címkével ellátott képet később leküldik a beállításjegyzékbe.

> [!IMPORTANT]
> Ha a módosított összetevőket többször is leküldi azonos címkékkel, létrehozhat "árvaokat" – a címkézetlen összetevőket, de továbbra is felhasználhat helyet a beállításjegyzékben. A címkézetlen lemezképek nem jelennek meg az Azure CLI-ben vagy a Azure Portalban, ha a képek címkével vannak listázva vagy megtekintve. Azonban a rétegek továbbra is léteznek, és helyet foglalnak el a beállításjegyzékben. Ha töröl egy címkézetlen rendszerképet, a rendszer törli a beállításjegyzék területét, ha a jegyzékfájl az egyetlen, vagy az utolsó, amely egy adott rétegre mutat. A címkézetlen lemezképek által használt lemezterület felszabadításával kapcsolatos további információkért lásd: [tároló lemezképek törlése Azure Container Registryban](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Összetevő kezelése

A Docker vagy más ügyféleszközök leküldéses és lekéréses műveleteihez tartozó beállításjegyzékbeli összetevő megoldásához egyesítse a teljes beállításjegyzék-nevet, a tárház nevét (beleértve a névtér elérési útját, ha van ilyen), valamint egy összetevő-címkét vagy egy jegyzékfájlt. A jelen feltételek magyarázatát az előző szakaszban találja.

  **Címek címke szerint**: `[loginServerUrl]/[repository][:tag]`
    
  **Címek kivonat szerint**: `[loginServerUrl]/[repository@sha256][:digest]`  

Ha a Docker vagy más ügyféleszközök használatával lekéri vagy leküldi az összetevőket egy Azure Container registrybe, használja a beállításjegyzék teljes URL-címét, más néven a *bejelentkezési kiszolgáló* nevét. Az Azure-felhőben az Azure Container Registry teljes URL-címe formátuma `myregistry.azurecr.io` (mind kisbetűs).

> [!NOTE]
> * Nem adhat meg portszámot a beállításjegyzék bejelentkezési kiszolgálójának URL-címében, például: `myregistry.azurecr.io:443` . 
> * `latest`Ha nem ad meg címkét a parancsban, a rendszer alapértelmezés szerint a címkét használja.  

   
### <a name="push-by-tag"></a>Leküldés címke szerint

Angol nyelvű Példák: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Lekérés címke szerint

Példa: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Lekérés a manifest Digest használatával


Példa:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Következő lépések

További információ a [beállításjegyzékbeli tárolásról](container-registry-storage.md) és a Azure Container Registry [támogatott tartalmainak formátumáról](container-registry-image-formats.md) .

Ismerkedjen meg a [rendszerképek leküldésével és lekérésével](container-registry-get-started-docker-cli.md) Azure Container Registryból.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


