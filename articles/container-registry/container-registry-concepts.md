---
title: A beállításregisztrálók, adattárak, képek és összetevők
description: Bevezetés az Azure-beli tárolóregisztrálók, -adattárak, tároló-rendszerképek és más összetevők alapvető fogalmaiba.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 64ab3812b3f23a7b3a480d3530c82bd39f2d29a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784082"
---
# <a name="about-registries-repositories-and-artifacts"></a>A beállításregisztrálók, adattárak és összetevők

Ez a cikk bemutatja a tárolóregisztrálók, -adattárak, tároló-rendszerképek és a kapcsolódó összetevők legfontosabb fogalmait. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Beállításjegyzék, adattárak és összetevők":::

## <a name="registry"></a>Regisztrációs adatbázis

A *tároló-beállításjegyzék* egy olyan szolgáltatás, amely tároló-rendszerképeket és kapcsolódó összetevőket tárol és terjeszt. Docker Hub példa egy nyilvános tárolójegyzékre, amely a Docker-tároló rendszerképének általános katalógusaként szolgál. Azure Container Registry lehetővé teszi a felhasználók számára a tárolók tartalmának közvetlen vezérlését [integrált](container-registry-geo-replication.md) hitelesítéssel, georeplikációval, amely támogatja a globális terjesztést és megbízhatóságot a hálózati környezetek számára, virtuális hálózati konfigurációt [Private Link,](container-registry-private-link.md)címkezárolást [és](container-registry-image-lock.md)számos egyéb továbbfejlesztett funkciót. 

A Docker-kompatibilis tároló rendszerképei mellett a Azure Container Registry [](container-registry-image-formats.md) számos tartalom-összetevőt támogat, beleértve a Helm-diagramokat és az Open Container Initiative (OCI) képformátumokat.

## <a name="repository"></a>Adattár

Az *adattár tároló-rendszerképek* vagy a regisztrációs adatbázisban található egyéb összetevők gyűjteménye, amelyek neve megegyezik, de különböző címkékkel. Az alábbi három kép például az `acr-helloworld` adattárban van:

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Az adattár nevei névtereket [is tartalmazhatnak.](container-registry-best-practices.md#repository-namespaces) A névterek lehetővé teszik a kapcsolódó adattárak és az összetevők tulajdonjogának azonosítását a szervezetben perjellel tagolt nevek használatával. A beállításjegyzék azonban az összes adattárat egymástól függetlenül kezeli, nem hierarchiaként. Például:

- *marketing/kampány10-18/web:v2*
- *marketing/kampány10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Az adattár nevei csak kisbetűs alfanumerikus karaktereket, pontokat, kötőjeleket, aláhúzásjeleket és perjeleket tartalmazhatnak. 

Az adattár teljes elnevezési szabályait lásd: [Open Container Initiative Distribution Specification (Open Container Initiative Distribution Specification).](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)

## <a name="artifact"></a>Összetevő

A tároló rendszerképe vagy egy regisztrációs adatbázisban található más összetevő egy vagy több címkéhez van társítva, egy vagy több réteggel rendelkezik, és egy jegyzék azonosítja. Az összetevők egymáshoz való viszonyának megértése segíthet a regisztrációs adatbázis hatékony kezelésében.

### <a name="tag"></a>Címke

A *rendszerkép* vagy más összetevő címkéje határozza meg annak verzióját. Egy adattáron belül egyetlen összetevőhöz egy vagy több címke is hozzárendelhető, és "címkézetlen" is lehet. Ez azt jelenti, hogy az összes címkét törölheti egy rendszerképből, míg a rendszerkép adatai (annak rétegei) a beállításjegyzékben maradnak.

A kép nevét az adattár (vagy adattár és névtér) plusz egy címke határozza meg. A rendszerképet leküldéshez és leküldéshez meg kell adnia a nevét a leküldéses vagy leküldési műveletben. A rendszer alapértelmezés szerint a címkét használja, ha nem ad meg egyet `latest` a Docker-parancsokban.

A tároló-rendszerképek címkézésének mikéntjéhez a fejlesztésük és üzembe helyezésük forgatókönyvei stb. Ajánlott például stabil címkéket használni az alap rendszerképek karbantartásához, valamint egyedi címkéket a rendszerképek üzembe helyezéséhez. További információkért lásd: Tároló-rendszerképek címkézésére és [verziószámozásra vonatkozó javaslatok.](container-registry-image-tag-version.md)

A címkék elnevezési szabályait a [Docker dokumentációjában találhatja meg.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Réteg

A tároló rendszerképei és az összetevők egy vagy több rétegből *állnak.* A különböző összetevőtípusok másként határozzák meg a rétegeket. Egy Docker-tároló rendszerképében például minden réteg a Dockerfile egy olyan sorának felel meg, amely meghatározza a rendszerképet:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Tároló rendszerképének rétegei":::

A beállításjegyzékben az összetevők közös rétegekkel osztoznak, ami növeli a tároló hatékonyságát. Előfordulhat például, hogy a különböző adattárakban található számos rendszerkép közös ASP.NET Core alapréteggel, de ennek a rétegnek csak egy példánya van tárolva a beállításjegyzékben. A rétegmegosztás emellett optimalizálja a rétegek csomópontokra való elosztását, és több összetevő osztozik a közös rétegeken. Ha egy csomóponton már található rendszerkép tartalmazza a ASP.NET Core réteget alapként, akkor az ugyanazt a réteget hivatkozó másik rendszerkép későbbi lekért része nem továbbítja a réteget a csomópontra. Ehelyett a csomóponton már meglévő rétegre hivatkozik.

A rétegek lehetséges rétegmanipulációval szembeni biztonságos elkülönítése és védelme érdekében a rétegek nincsenek megosztva a regisztrációs jegyzékek között.

### <a name="manifest"></a>Jegyzék

A tároló-beállításjegyzékbe lekért összes tároló-rendszerkép vagy -összetevő egy jegyzékfájlhoz *van társítva.* A jegyzékfájl, amelyet a beállításjegyzék hoz létre a tartalom leküldtekor, egyedileg azonosítja az összetevőket, és megadja a rétegeket. Az adattár jegyzékfájlját az [az acr repository show-manifests Azure CLI-paranccsal listálhatja.][az-acr-repository-show-manifests] 

A Linux-rendszerkép alapszintű `hello-world` jegyzékfájlja az alábbihoz hasonlóan néz ki:

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

Az adattár jegyzékfájlját az [az acr repository show-manifests Azure CLI-paranccsal listálhatja:][az-acr-repository-show-manifests]

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Listába sorolhatja például az "acr-helloworld" adattár jegyzékfájlját:

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

### <a name="manifest-digest"></a>Jegyzékfájl kivonata

A jegyzékfájlokat egy egyedi SHA-256 kivonat vagy *jegyzékkivonat azonosítja.* Minden képet vagy összetevőt – akár címkézett, akár nem – a kivonat azonosít. A kivonatérték akkor is egyedi, ha az összetevő rétegadatai megegyeznek egy másik összetevő adataival. Ez a mechanizmus teszi lehetővé, hogy ismételten azonos címkével ellátott rendszerképeket leküldsen egy regisztrációs adatbázisba. Például hiba nélkül ismételten leküldhet a regisztrációs adatbázisba, mert minden rendszerképet a rendszerkép `myimage:latest` egyedi kivonata azonosít.

A regisztrációs adatbázisból lekért összetevőt úgy lehet lekérte, hogy megadja annak kivonatát a lekért műveletben. Előfordulhat, hogy egyes rendszerek kivonat alapján történő lekért adatokra vannak konfigurálva, mert ez garantálja a rendszerkép lekért verzióját, még akkor is, ha egy azonos címkével ellátott rendszerképet a rendszer később leküld a beállításjegyzékbe.

> [!IMPORTANT]
> Ha ismételten leküldi a módosított összetevőket azonos címkékkel, létrehozhat "árvákat"– olyan összetevőket, amelyek nincsenek címkézve, de továbbra is helyet foglalnak a beállításjegyzékben. A címkézetlen képek nem jelennek meg az Azure CLI-ban vagy a Azure Portal a képek címke alapján való listázása vagy megtekintésekor. A rétegeik azonban továbbra is léteznek, és helyet foglalnak a regisztrációs adatbázisban. A nem látható rendszerkép törlésével helyet szabadít fel a beállításjegyzékben, ha a jegyzék az egyetlen vagy az utolsó, amely egy adott rétegre mutat. A fel nem használt rendszerképek által használt terület felszabadításával kapcsolatos információkért lásd: [Tároló rendszerképének törlése a Azure Container Registry.](container-registry-delete.md)

## <a name="addressing-an-artifact"></a>Összetevő kezelése

A leküldési és leküldési műveletek beállításjegyzékbeli összetevőjéhez a Docker vagy más ügyféleszközök használatával történő címzéshez kombinálja a regisztrációs adatbázis teljes nevét, az adattár nevét (beleértve a névtér elérési útját, ha van), valamint egy összetevőcímkét vagy jegyzékkivonatot. A kifejezések magyarázatát lásd az előző szakaszokban.

  **Cím címke szerint:**`[loginServerUrl]/[repository][:tag]`
    
  **Cím kivonat alapján:**`[loginServerUrl]/[repository@sha256][:digest]`  

Ha Docker- vagy más ügyféleszközöket használ az összetevők Azure Container Registrybe való leküldéséhez vagy leküldéséhez, használja a regisztrációs adatbázis teljes URL-címét, más néven a bejelentkezési *kiszolgáló* nevét. Az Azure-felhőben az Azure Container Registry teljes URL-címe formátuma `myregistry.azurecr.io` (csak kisbetűk).

> [!NOTE]
> * A beállításjegyzék bejelentkezési kiszolgálójának URL-címében nem adható meg portszám, `myregistry.azurecr.io:443` például: . 
> * Ha a parancsban nem ad meg címkét, a rendszer alapértelmezés szerint `latest` a címkét használja.  

   
### <a name="push-by-tag"></a>Leküldés címke szerint

Angol nyelvű Példák: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Lekért címke alapján

Példa: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Lekért jegyzékfájl kivonata


Példa:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Következő lépések

További információ a [beállításjegyzékbeli tárolásról](container-registry-storage.md) és a támogatott tartalomformátumokról a Azure Container Registry. [](container-registry-image-formats.md)

Ismerje meg, hogyan [lehet rendszerképeket lekért és lekért](container-registry-get-started-docker-cli.md) Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
