---
title: Rendszerkép-erőforrások törlése
description: A tároló rendszerkép-adatainak Azure CLI-parancsokkal való hatékony kezelésével kapcsolatos részletek.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: af277d0c02960c989b4e9119f2ecbfd8f6d7ce07
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783988"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Tároló rendszerképének törlése Azure Container Registry Azure CLI használatával

Az Azure Container Registry méretének fenntartása érdekében rendszeresen törölje az elavult rendszerképadatokat. Míg egyes éles környezetben üzembe helyezett tároló-rendszerképek hosszabb távú tárolást igényelnek, mások általában gyorsabban törölhetők. Egy automatizált build- és tesztelési forgatókönyvben például a regisztrációs adatbázis gyorsan feltölthet olyan rendszerképeket, amelyek esetleg soha nem lesznek üzembe helyezhetők, és a build és a tesztelési folyamat befejezése után röviddel kiüríthető.

Mivel a képadatokat többféleképpen is törölheti, fontos megérteni, hogy az egyes törlési műveletek milyen hatással vannak a tárterület-használatra. Ez a cikk a képadatok törlésének több módszerét is beemeli:

* Adattár [törlése:](#delete-repository)Törli az adattárban található összes képet és minden egyedi réteget.
* Törlés [címke alapján:](#delete-by-tag)Törli a képet, a címkét, a kép által hivatkozott összes egyedi réteget és a képhez társított összes többi címkét.
* Törlés [jegyzékkivonat alapján:](#delete-by-manifest-digest)Töröl egy képet, a kép által hivatkozott összes egyedi réteget és a képhez társított összes címkét.

A törlési műveletek automatizálásához mintaszkprogramokat biztosítunk.

Ezen fogalmak bemutatásáért lásd: [About registries, repositories, and images (A](container-registry-concepts.md)beállításregisztrálók, adattárak és rendszerképek bemutatása).

## <a name="delete-repository"></a>Adattár törlése

Az adattár törlésével az adattár összes lemezképe törölve lesz, beleértve az összes címkét, egyedi réteget és jegyzékfájlt. Amikor töröl egy adattárat, helyreállítja az adattár egyedi rétegeire hivatkozó képek által használt tárhelyet.

A következő Azure CLI-parancs törli az "acr-helloworld" adattárat, valamint az adattárban található összes címkét és jegyzékfájlt. Ha a törölt jegyzékek által hivatkozott rétegekre nem hivatkoznak a beállításjegyzékben található egyéb lemezképek, akkor azok rétegbeli adatai is törlődnek, így helyreállítják a tárterületet.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Törlés címke szerint

Az egyes rendszerképeket a törlési műveletben az adattár nevének és címkének a megadásával törölheti az adattárból. Ha címkével törli a adatokat, a rendszerkép bármely egyedi rétege által használt tárterületet helyreállítja (a beállításjegyzék más lemezképei által nem megosztott rétegeket).

A címke alapján való törléshez használja [az az acr repository delete][az-acr-repository-delete] parancsot, és adja meg a rendszerkép nevét a `--image` paraméterben. A rendszer a képhez tartozó összes réteget, valamint a képhez társított egyéb címkéket is törli.

Például törölje az "acr-helloworld:latest" rendszerképet a "myregistry" beállításjegyzékből:

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> A címke *szerinti törlés* nem tévesztendő össze a címkék törlésével (a címkézés törlésével). A címkéket az [az acr repository untag][az-acr-repository-untag]Azure CLI-paranccsal törölheti. A lemezképek felcímkézését nem szabad [](container-registry-concepts.md#manifest) megszabadítja, mert a jegyzékfájl és a réteg adatai a beállításjegyzékben maradnak. Csak maga a címkehivatkozás törlődik.

## <a name="delete-by-manifest-digest"></a>Törlés jegyzékfájl-kivonat alapján

A [jegyzékkivonat](container-registry-concepts.md#manifest-digest) egy, nincs vagy több címkével társítható. A kivonatolással történő törléskor a jegyzékfájl által hivatkozott összes címke törlődik, ahogy a képhez egyedi rétegek rétegadata is. A megosztott réteg adatai nem törlődnek.

A kivonat alapján történő törléshez először listába kell sorolni a törölni kívánt képeket tartalmazó adattár jegyzékkivonatát. Például:

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

Ezután adja meg a törölni kívánt kivonatot az [az acr repository delete parancsban.][az-acr-repository-delete] A parancs formátuma:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Például az előző kimenetben felsorolt utolsó jegyzékfájl törléséhez (a "v2" címkével):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

A rendszerképet a rendszer törli a beállításjegyzékből, ahogy az adott rendszerképre `acr-helloworld:v2` vonatkozó összes rétegadatot is. Ha egy jegyzékfájl több címkéhez van társítva, az összes társított címke is törlődik.

## <a name="delete-digests-by-timestamp"></a>Kivonatok törlése időbélyeg szerint

Egy adattár vagy beállításjegyzék méretének fenntartása érdekében előfordulhat, hogy rendszeres időközönként törölnie kell egy adott dátumnál régebbi jegyzékkivonatokat.

A következő Azure CLI-parancs a megadott időbélyegnél régebbi adattár összes jegyzékkivonatát listázza növekvő sorrendben. Cserélje le `<acrName>` a és `<repositoryName>` a értékeket a környezetének megfelelő értékekre. Az időbélyegző lehet egy teljes dátum-idő kifejezés vagy egy dátum, ahogy ebben a példában is.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Az elavult jegyzékkivonatok azonosítása után a következő Bash-szkript futtatásával törölheti a megadott időbélyegnél régebbi jegyzékkivonatokat. Ehhez az Azure CLI és **az xargs szükséges.** Alapértelmezés szerint a szkript nem végez törlést. Módosítsa a `ENABLE_DELETE` értékét értékre `true` a rendszerkép törlésének engedélyezéséhez.

> [!WARNING]
> A következő példaszkprogramot elővigyázatossággal használja – a törölt képadatok NEM ÁLLÍTHATÓK VISSZA. Ha vannak olyan rendszerei, amelyek jegyzékkivonat alapján (és nem a rendszerképnévvel) leküldik a rendszerképeket, ne futtassa ezeket a szkripteket. A jegyzékkivonatok törlésével megakadályozhatja, hogy ezek a rendszerek lekérték a rendszerképeket a regisztrációs adatbázisból. A jegyzékfájlos lehúzás helyett  érdemes lehet egy egyedi címkézési sémát használni, amely az [ajánlott eljárás.](container-registry-image-tag-version.md) 

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

## <a name="delete-untagged-images"></a>Nem látható képek törlése

Ahogy a [Manifest digest](container-registry-concepts.md#manifest-digest) (Jegyzékkivonat) szakaszban említettük, a módosított rendszerkép meglévő címkével való leküldetésével a korábban leküldett rendszerkép címkéinek megszétleszthetők, ami árva (vagy "gúnár") rendszerképet eredményez.  A korábban lekért rendszerkép jegyzékfájlja – és a rétegbeli adatai – a beállításjegyzékben marad. Vegyük a következő eseménysorozatot:

1. Az *acr-helloworld rendszerkép leküldése* a legújabb **címkével:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Ellenőrizze az *acr-helloworld adattár jegyzékfájlját:*

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

1. Az *acr-helloworld* Dockerfile módosítása
1. Az *acr-helloworld rendszerkép leküldése* a legújabb **címkével:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Ellenőrizze az *acr-helloworld adattár jegyzékfájlját:*

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

Ahogy a sorozat utolsó lépésének kimenetében láthatja, most már van egy árva jegyzékfájl, amelynek tulajdonsága `"tags"` egy üres lista. Ez a jegyzékfájl továbbra is megtalálható a beállításjegyzékben, valamint minden olyan egyedi rétegadat, amelyre hivatkozik. Az ilyen árva képek és a rétegadatok törléséhez **a jegyzékkivonatot kell törölnie.**

## <a name="delete-all-untagged-images"></a>Az összes nem látható kép törlése

Az alábbi Azure CLI-paranccsal listázhatja az adattárban található összes fel nem sorolt rendszerképet. Cserélje le `<acrName>` a és `<repositoryName>` a értékeket a környezetének megfelelő értékekre.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Ezzel a paranccsal egy szkriptben törölheti az adattár összes fel nem használt lemezképét.

> [!WARNING]
> A következő példaszk szkripteket körültekintően használja – a törölt képadatok NEM ÁLLÍTHATÓK VISSZA. Ha vannak olyan rendszerei, amelyek jegyzékkivonat alapján (és nem a rendszerkép neve alapján) leküldik a rendszerképeket, ne futtassa ezeket a szkripteket. A nem látható lemezképek törlésével megakadályozható, hogy ezek a rendszerek leküldhethedhednek a lemezképek a regisztrációs adatbázisból. A jegyzékfájlos lehúzás helyett  érdemes lehet egy egyedi címkézési sémát használni, amely az [ajánlott eljárás.](container-registry-image-tag-version.md)

**Azure CLI a Bashben**

A következő Bash-szkript törli az összes fel nem megjelölt rendszerképet az adattárból. Ehhez az Azure CLI és az **xargs szükséges.** Alapértelmezés szerint a szkript nem végez törlést. Módosítsa a `ENABLE_DELETE` értékét értékre `true` a rendszerkép törlésének engedélyezéséhez.

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

Az alábbi PowerShell-szkript törli az összes fel nem megjelölt lemezképet az adattárból. Ehhez a PowerShell és az Azure CLI szükséges. Alapértelmezés szerint a szkript nem végez törlést. Módosítsa a `$enableDelete` értékét értékre `$TRUE` a rendszerkép törlésének engedélyezéséhez.

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

Az Azure CLI-parancsok szkriptelésének alternatívájaként futtason egy igény szerinti vagy ütemezett ACR-feladatot az összes olyan címke törléséhez, amely egy adott időtartamnál régebbi, vagy megfelel egy megadott névszűrőnek. További információ: [Lemezképek automatikus kiürítása egy Azure-beli tároló-beállításjegyzékből.](container-registry-auto-purge.md)

Ha szükséges, állítson be [egy adatmegőrzési](container-registry-retention-policy.md) szabályzatot minden beállításjegyzékhez a nem megjelölt jegyzékek kezeléséhez. Ha engedélyezi a megőrzési szabályzatot, a rendszer automatikusan törli a regisztrációs adatbázisban azokat a rendszerképjegyzékbeli jegyzékeket, amelyek nem tartalmaznak társított címkéket, és a mögöttes rétegbeli adatokat a rendszer automatikusan törli egy adott időszak után.

## <a name="next-steps"></a>Következő lépések

A tárolókban található rendszerképtárolással kapcsolatos további Azure Container Registry tároló [rendszerképtárolása a Azure Container Registry.](container-registry-storage.md)

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
