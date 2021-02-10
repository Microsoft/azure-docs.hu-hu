---
title: Tudnivalók a tárakról & lemezképekről
description: Bevezetés az Azure Container-nyilvántartások,-adattárak és-tárolók főbb fogalmi alapjaiba.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: 0cc7df22236c60bd473385d92c8db563be68f688
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008519"
---
# <a name="about-registries-repositories-and-images"></a>A jegyzékek, adattárak és lemezképek

Ez a cikk bemutatja a tároló-beállításjegyzékek,-adattárak és-tárolók, valamint a kapcsolódó összetevők főbb fogalmait. 

## <a name="registry"></a>Regisztrációs adatbázis

A tároló- *beállításjegyzék* egy olyan szolgáltatás, amely tárolói lemezképeket tárol és terjeszt. A Docker hub egy nyilvános tároló-nyilvántartó, amely támogatja a nyílt forráskódú közösségét, és általános katalógusként szolgál a lemezképek számára. Azure Container Registry lehetővé teszi a felhasználók számára, hogy a rendszerképek közvetlen vezérlésével, integrált hitelesítéssel, [geo-replikálással](container-registry-geo-replication.md) támogatják a globális elosztást és a megbízhatóságot a hálózattal szoros üzembe helyezések, a [virtuális hálózat és a tűzfal konfigurációja](container-registry-vnet.md), a [címkék zárolása](container-registry-image-lock.md)és számos más továbbfejlesztett funkció között. 

A Docker-tárolók rendszerképein kívül a Azure Container Registry támogatja a kapcsolódó [tartalmi](container-registry-image-formats.md) összetevőket, beleértve a Open Container INITIATIVE (OCI) képformátumokat is.

## <a name="content-addressable-elements-of-an-artifact"></a>Egy összetevő tartalom címezhető elemei

Az Azure Container registryben található összetevők címe a következő elemeket tartalmazza. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** – a beállításjegyzék-gazdagép teljes neve. Az Azure Container registryben található beállításjegyzék-gazdagép formátuma *myregistry*. azurecr.IO (minden kisbetűs). A loginUrl a Docker vagy más ügyféleszközök használatával kell megadnia az összetevők Azure Container registrybe való lekéréséhez vagy leküldéséhez. 
* **adattár** – egy vagy több kapcsolódó kép vagy összetevő logikai csoportosításának neve, például egy alkalmazás vagy egy alapszintű operációs rendszer lemezképei. Tartalmazhat *névtér* elérési útját. 
* egy adattárban tárolt rendszerkép vagy összetevő adott verziójának a **címkéje** .

Az Azure Container registryben található rendszerképek teljes neve például az alábbihoz hasonló lehet:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Ezekről az elemekről az alábbi részekben talál további információkat.

## <a name="repository-name"></a>Adattár neve

A *tárház* tároló-lemezképek vagy más, azonos nevű, de eltérő címkékkel rendelkező összetevők gyűjteménye. Például a következő három kép szerepel az "ACR-HelloWorld" adattárban:


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

## <a name="image"></a>Kép

A beállításjegyzékben található tároló-rendszerkép vagy más összetevő egy vagy több címkével van társítva, egy vagy több réteget tartalmaz, és egy jegyzékfájl azonosítja. Az összetevők egymáshoz való viszonyának megértése segíthet a beállításjegyzék hatékony kezelésében.

### <a name="tag"></a>Címke

A rendszerkép vagy más összetevő *címkéje* a verziószámát adja meg. A tárházon belüli egyetlen összetevőhöz egy vagy több címke rendelhető, és az is lehet, hogy "címkézetlen". Ez azt eredményezheti, hogy az összes címkét törölheti egy képből, míg a rendszerképben lévő adatok (a rétegek) a beállításjegyzékben maradnak.

A tárház (vagy a tárház és a névtér), valamint a címke határozza meg a rendszerkép nevét. A képek leküldéséhez és lekéréséhez adja meg a nevét a leküldéses vagy a lekérési műveletben. Alapértelmezés szerint a címkét akkor `latest` használja a rendszer, ha nem ad meg egyet a Docker-parancsokban.

A tároló-lemezképek címkézésének módját a forgatókönyvek segítségével fejlesztheti vagy helyezheti üzembe. A stabil címkék például az alaplemezképek fenntartásához, valamint a rendszerképek telepítéséhez szükséges egyedi címkékhez ajánlottak. További információ: [a címkézéssel kapcsolatos javaslatok és a tárolók verziószámozása](container-registry-image-tag-version.md).

A címkék elnevezési szabályairól a [Docker dokumentációjában](https://docs.docker.com/engine/reference/commandline/tag/)talál további információt.

### <a name="layer"></a>Réteg

A tárolók képei egy vagy több *rétegből* állnak, amelyek mindegyike a képet definiáló Docker egyik sorához tartozik. A beállításjegyzékben található rendszerképek közös rétegekből állnak, és növelik a tárolási hatékonyságot. Például a különböző adattárakban több rendszerkép is megoszthatja ugyanazt az alpesi Linux alapréteget, de az adott rétegnek csak egy példányát tárolja a beállításjegyzékben.

A réteg megosztása a rétegek eloszlását is optimalizálja a több, közös rétegekkel rendelkező lemezképekkel rendelkező csomópontokra. Ha például egy csomóponton már van egy olyan rendszerkép, amely az alpesi linuxos réteget tartalmazza, akkor egy másik, ugyanarra a rétegre hivatkozó rendszerkép lekérése nem továbbítja a réteget a csomópontra. Ehelyett a csomóponton már meglévő rétegre hivatkozik.

A lehetséges rétegbeli manipulációk biztonságos elkülönítésének és védelmének biztosításához a rétegek nem oszthatók meg a kibocsátásiegység-forgalmi jegyzékek között.

### <a name="manifest"></a>Jegyzék

A tároló-beállításjegyzékbe leküldött összes tároló-rendszerkép vagy-összetevő egy *jegyzékfájlhoz* van társítva. A beállításjegyzék által a rendszerkép leküldésekor generált jegyzékfájl, amely egyedileg azonosítja a rendszerképet, és meghatározza annak rétegeit. 

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

A jegyzékfájlokat egy egyedi SHA-256 kivonat, vagy egy *manifest Digest* azonosítja. Minden rendszerkép vagy összetevő – akár címkézett, akár nem – azonosítja a kivonatát. A kivonatoló érték akkor is egyedi, ha a képrétegbeli adatok megegyeznek egy másik rendszerkép nevével. Ez a mechanizmus lehetővé teszi, hogy az azonos módon címkézett képeket ismételten leküldse egy beállításjegyzékbe. Előfordulhat például, hogy többször is leküldi `myimage:latest` a beállításjegyzéket, mert hiba történt, mivel az egyes rendszerképeket az egyedi kivonata azonosítja.

A rendszerképeket egy beállításjegyzékből is lekérheti, ha megadta a kivonatot a lekéréses műveletben. Előfordulhat, hogy néhány rendszer úgy van konfigurálva, hogy a kivonatoló lekérése miatt lekérje a rendszerkép verziószámát, még akkor is, ha egy azonos címkével ellátott képet küld a beállításjegyzékbe.

Tegyük fel például, hogy egy képet az "ACR-HelloWorld" adattárból a manifest Digest:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Ha a módosított képeket többször is leküldi azonos címkékkel, létrehozhat árva képeket – a nem címkézett képeket, de továbbra is felhasználhat helyet a beállításjegyzékben. A címkézetlen lemezképek nem jelennek meg az Azure CLI-ben vagy a Azure Portalban, ha a képek címkével vannak listázva vagy megtekintve. Azonban a rétegek továbbra is léteznek, és helyet foglalnak el a beállításjegyzékben. Ha töröl egy címkézetlen rendszerképet, a rendszer törli a beállításjegyzék területét, ha a jegyzékfájl az egyetlen, vagy az utolsó, amely egy adott rétegre mutat. A címkézetlen lemezképek által használt lemezterület felszabadításával kapcsolatos további információkért lásd: [tároló lemezképek törlése Azure Container Registryban](container-registry-delete.md).

## <a name="next-steps"></a>Következő lépések

További információ a [rendszerkép-tárolásról](container-registry-storage.md) és a Azure Container Registry [támogatott fájlformátumairól](container-registry-image-formats.md) .

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


