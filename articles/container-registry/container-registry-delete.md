---
title: Rendszerkép-erőforrások törlése
description: A beállításjegyzék méretének hatékony kezeléséről a tároló képadatainak Azure CLI-parancsokkal történő törlésével foglalkozó témakörben olvashat.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78403337"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Azure Container Registry tároló lemezképének törlése az Azure CLI használatával

Az Azure Container Registry méretének megőrzése érdekében rendszeresen törölnie kell az elavult képadatokat. Míg az éles környezetben üzembe helyezett egyes tároló-lemezképek hosszabb távú tárolást igényelhetnek, másokat általában gyorsabban lehet törölni. Egy automatizált Build-és tesztelési forgatókönyvben például a beállításjegyzék gyorsan kitöltheti azokat a lemezképeket, amelyek soha nem helyezhetők üzembe, és a létrehozási és tesztelési menet befejezése után nem sokkal törölhetők.

Mivel számos különböző módon törölheti a képadatokat, fontos tisztában lennie azzal, hogy az egyes törlési műveletek milyen hatással vannak a tárterület használatára. Ez a cikk a képadatok törlésének számos módszerét ismerteti:

* [Adattár](#delete-repository)törlése: törli az összes képet és a tárházban lévő összes egyedi réteget.
* Törlés [címke](#delete-by-tag)szerint: törli a képet, a címkét, a rendszerkép által hivatkozott összes egyedi réteget, valamint a képhez társított összes többi címkét.
* Törlés a [manifest Digest](#delete-by-manifest-digest)használatával: törli a képet, a rendszerkép által hivatkozott összes egyedi réteget és a képhez társított összes címkét.

A törlési műveletek automatizálása érdekében a rendszer parancsfájlokat biztosít.

A fogalmak bevezetését lásd: a beállításjegyzékek [, adattárak és rendszerképek](container-registry-concepts.md).

## <a name="delete-repository"></a>Adattár törlése

A tárház törlése törli az adattár összes lemezképét, beleértve az összes címkét, az egyedi réteget és a jegyzékfájlokat is. Amikor töröl egy tárházat, az adott adattár egyedi rétegeire hivatkozó lemezképek által használt tárterületet fogja helyreállítani.

A következő Azure CLI-parancs törli az "ACR-HelloWorld" tárházat és az adattárban található összes címkét és jegyzékfájlt. Ha a törölt jegyzékfájlok által hivatkozott rétegek nem hivatkoznak a beállításjegyzékben található többi rendszerképre, a rétegbeli adatait is törli a rendszer, és helyreállítja a tárolóhelyet.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Törlés címke szerint

Az egyes lemezképeket törölheti egy adattárból, ha megadja az adattár nevét és a címkét a törlési műveletben. Ha a címke alapján törli a címkét, akkor a rendszerképben lévő egyedi rétegek által használt tárolóhelyet is helyreállítja

A címke alapján történő törléshez használja az [az ACR adattár törlése][az-acr-repository-delete] lehetőséget, és adja meg a rendszerkép nevét a `--image` paraméterben. A rendszer minden, a képhez egyedi réteget töröl, és a képhez társított egyéb címkéket is törli.

Például törölje az "ACR-HelloWorld: Latest" rendszerképet a "myregistry" beállításjegyzékből:

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> A *címkével való törlés nem* tévesztendő össze a címkék törlésével (címkézés törlése). A címkét törölheti az Azure CLI-parancs az [ACR repository jelölését][az-acr-repository-untag]használatával. A rendszer nem szabadít fel lemezterületet, ha jelölését egy képet, mert a [jegyzékfájlja](container-registry-concepts.md#manifest) és a rétegbeli adatok megmaradnak a beállításjegyzékben. Csak a címke hivatkozását törli a rendszer.

## <a name="delete-by-manifest-digest"></a>Törlés a manifest Digest használatával

A [jegyzékfájlok kivonata](container-registry-concepts.md#manifest-digest) egy, none vagy több címkével is társítható. Ha kivonatoló törlést végez, a jegyzékfájlban hivatkozott összes címkét törli a rendszer, ahogy a rétegben lévő adatok a képhez egyediek. A megosztott rétegbeli adatértékek nem törlődnek.

A kivonatoló törléshez először sorolja fel a törölni kívánt képeket tartalmazó adattár jegyzékfájl-kivonatait. Például:

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
  }
]
```

Ezután határozza meg a törölni kívánt kivonatot az az [ACR adattár törlése][az-acr-repository-delete] paranccsal. A parancs formátuma:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Ha például törölni szeretné az előző kimenetben felsorolt utolsó jegyzékfájlt (a "v2" címkével):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

A `acr-helloworld:v2` rendszer törli a rendszerképet a beállításjegyzékből, ahogy az adott képhez egyedi adatrétegi adatok is. Ha egy jegyzékfájl több címkével van társítva, az összes hozzá tartozó címke is törlődik.

## <a name="delete-digests-by-timestamp"></a>Kivonatok törlése timestamp alapján

A tárház vagy a beállításjegyzék méretének megőrzése érdekében előfordulhat, hogy rendszeres időközönként törölni kell az adott dátumnál régebbi jegyzékfájl-kivonatokat.

A következő Azure CLI-parancs felsorolja az összes manifest-kivonatot egy megadott időbélyegnél régebbi tárházban, növekvő sorrendben. Cserélje le a `<acrName>` és `<repositoryName>` a értéket a környezetének megfelelő értékekre. Az időbélyeg lehet egy teljes dátum-idő kifejezés vagy egy dátum, ahogy az ebben a példában is látható.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Az elavult jegyzékfájl-kivonatok azonosítása után a következő bash-szkripttel törölheti a megadott időbélyegnél régebbi jegyzékfájl-kivonatokat. Ehhez az Azure CLI és a **xargs** szükséges. Alapértelmezés szerint a parancsfájl nem végez törlést. A `ENABLE_DELETE` `true` rendszerkép törlésének engedélyezéséhez módosítsa az értéket.

> [!WARNING]
> A következő minta-parancsfájlt körültekintően kell használni – a törölt képadatok nem állíthatók helyre. Ha olyan rendszerekkel rendelkezik, amelyekben a manifest Digest (a rendszerkép neve helyett) lekéri a képeket, ne futtassa ezeket a parancsfájlokat. A jegyzékfájl-kivonatok törlésével megakadályozhatja, hogy ezek a rendszerek a lemezképeket a beállításjegyzékből húzza. A jegyzékfájlok helyett érdemes lehet egy *egyedi címkézési* sémát alkalmazni, amely [ajánlott eljárás](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Címkézetlen lemezképek törlése

Ahogy azt a [manifest Digest](container-registry-concepts.md#manifest-digest) szakaszban is említettük, a módosított rendszerkép egy **meglévő címkével való** kihagyása a korábban lenyomott képpel, amely árva (vagy "lógó") képet eredményezett. A korábban leküldett rendszerkép jegyzékfájlja – és a rétegbeli adatok – a beállításjegyzékben maradnak. Vegye figyelembe a következő eseménysorozat-sorozatot:

1. Leküldéses képek *ACR – HelloWorld* a címkével **legújabb**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. A repository *ACR-HelloWorld* ellenőrzési jegyzékfájljának megtekintése:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. *ACR módosítása – HelloWorld* Docker
1. Leküldéses képek *ACR – HelloWorld* a címkével **legújabb**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. A repository *ACR-HelloWorld* ellenőrzési jegyzékfájljának megtekintése:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Ahogy az a sorozatban az utolsó lépés kimenetében is látható, már létezik egy árva jegyzékfájl, amelynek a `"tags"` tulajdonsága egy üres lista. Ez a jegyzékfájl továbbra is létezik a beállításjegyzékben, valamint az általa hivatkozott egyedi rétegbeli adatokkal együtt. **Az ilyen árva rendszerképek és a hozzájuk tartozó adatrétegek törléséhez a manifest Digest utasítással kell törölnie**.

## <a name="delete-all-untagged-images"></a>Az összes címkézetlen rendszerkép törlése

Az adattár összes címkézetlen lemezképét az alábbi Azure CLI-paranccsal listázhatja. Cserélje le a `<acrName>` és `<repositoryName>` a értéket a környezetének megfelelő értékekre.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Ha ezt a parancsot egy parancsfájlban használja, törölheti az összes címkézetlen lemezképet egy adattárból.

> [!WARNING]
> A következő minta-parancsfájlok körültekintő használata – a törölt képadatok nem állíthatók helyre. Ha olyan rendszerekkel rendelkezik, amelyekben a manifest Digest (a rendszerkép neve helyett) lekéri a képeket, ne futtassa ezeket a parancsfájlokat. A címkézetlen lemezképek törlésével megakadályozhatja, hogy ezek a rendszerek kihúzzanak a lemezképeket a beállításjegyzékből. A jegyzékfájlok helyett érdemes lehet egy *egyedi címkézési* sémát alkalmazni, amely [ajánlott eljárás](container-registry-image-tag-version.md).

**Azure CLI a Bashben**

A következő bash-szkript törli az összes címkézetlen lemezképet egy adattárból. Ehhez az Azure CLI és a **xargs** szükséges. Alapértelmezés szerint a parancsfájl nem végez törlést. A `ENABLE_DELETE` `true` rendszerkép törlésének engedélyezéséhez módosítsa az értéket.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI a PowerShellben**

A következő PowerShell-szkript törli az összes címkézetlen lemezképet egy adattárból. Ehhez a PowerShell és az Azure CLI szükséges. Alapértelmezés szerint a parancsfájl nem végez törlést. A `$enableDelete` `$TRUE` rendszerkép törlésének engedélyezéséhez módosítsa az értéket.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Címkék és jegyzékfájlok automatikus végleges törlése (előzetes verzió)

Az Azure parancssori felület parancsainak alternatívájaként egy igény szerinti vagy ütemezett ACR-feladat futtatásával törölheti az összes olyan címkét, amely egy adott időtartamnál régebbi, vagy megfelel a megadott szűrőnek. További információ: [lemezképek automatikus törlése az Azure Container registryből](container-registry-auto-purge.md).

Opcionálisan beállíthat egy [adatmegőrzési szabályt](container-registry-retention-policy.md) minden beállításjegyzékhez a címkézetlen jegyzékfájlok kezeléséhez. Ha engedélyez egy adatmegőrzési szabályzatot, a rendszer a beállításjegyzékben olyan rendszerképeket tartalmaz, amelyek nem rendelkeznek társított címkékkel, és az alapul szolgáló réteg adatai automatikusan törlődnek egy meghatározott időszak után.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Container Registry található képtárolóról: [tároló képtárolója Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
