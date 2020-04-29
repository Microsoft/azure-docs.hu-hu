---
title: Leküldéses és lekéréses OCI-összetevő
description: A nyílt Container Initiative (OCI) összetevők leküldése és lekérése az Azure-beli privát tároló-beállításjegyzék használatával
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371052"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>OCI-összetevő leküldése és lekérése egy Azure Container Registry használatával

Az [Open Container Initiative (OCI)](container-registry-image-formats.md#oci-artifacts) összetevőinek, valamint a Docker és a Docker-kompatibilis tároló lemezképének tárolására és kezelésére használhat Azure Container registryt.

A funkció bemutatásához ez a cikk bemutatja, hogyan használhatja a [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) eszközt egy minta-összetevő leküldésére – egy szövegfájlt az Azure Container registrybe. Ezután húzza le az összetevőt a beállításjegyzékből. Az Azure Container registryben számos különböző OCI-összetevőt kezelhet az egyes összetevőknek megfelelő parancssori eszközök használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t.
* **ORAS eszköz** – letöltheti és telepítheti az operációs rendszerének aktuális ORAS-kiadását a [GitHub](https://github.com/deislabs/oras/releases)-tárházból. Az eszköz tömörített csomagként (`.tar.gz` fájlként) van közzétéve. Bontsa ki és telepítse a fájlt az operációs rendszerének megfelelő általános eljárások használatával.
* **Azure Active Directory egyszerű szolgáltatás (nem kötelező)** – a ORAS való közvetlen hitelesítéshez hozzon létre egy [egyszerű szolgáltatásnevet](container-registry-auth-service-principal.md) a beállításjegyzék eléréséhez. Győződjön meg arról, hogy a szolgáltatásnév hozzá van rendelve egy olyan szerepkörhöz, mint például a AcrPush, hogy rendelkezik az összetevők leküldéséhez és lekéréséhez szükséges engedélyekkel.
* **Azure CLI (opcionális)** – ha egyéni identitást szeretne használni, az Azure CLI helyi telepítésére van szükség. A 2.0.71 vagy újabb verzió használata javasolt. A `az --version `verzió megkereséséhez futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker (nem kötelező)** – ha egyéni identitást szeretne használni, akkor a Docker-t helyileg kell telepíteni a beállításjegyzékben való hitelesítéshez. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.


## <a name="sign-in-to-a-registry"></a>Bejelentkezés egy beállításjegyzékbe

Ez a szakasz két javasolt munkafolyamatot mutat be a beállításjegyzékbe való bejelentkezéshez a használt identitástól függően. Válassza ki a környezetének megfelelő módszert.

### <a name="sign-in-with-oras"></a>Bejelentkezés a ORAS

Ha leküldéses jogokkal rendelkező [szolgáltatást](container-registry-auth-service-principal.md) használ, futtassa `oras login` a parancsot, és jelentkezzen be a beállításjegyzékbe a szolgáltatás egyszerű alkalmazás-azonosítója és jelszava használatával. Adja meg a teljes beállításjegyzékbeli nevet (az összes kisbetűs), ebben az esetben a *myregistry.azurecr.IO*. A szolgáltatás egyszerű alkalmazásának azonosítója a környezeti változóban `$SP_APP_ID`, a változóban `$SP_PASSWD`pedig a jelszót adja át.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

A jelszó stdin-ből való olvasásához használja `--password-stdin`a következőt:.

### <a name="sign-in-with-azure-cli"></a>Bejelentkezés az Azure CLI használatával

[Jelentkezzen](/cli/azure/authenticate-azure-cli) be az Azure CLI-be a személyazonosságával, hogy lekérje és lekérje az összetevők beküldését a tároló-beállításjegyzékből.

Ezután használja az Azure CLI-parancsot az [ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) használatával a beállításjegyzék eléréséhez. Például egy *myregistry*nevű beállításjegyzékbeli hitelesítéshez:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`a a Docker-ügyfél használatával állít be Azure Active Directory tokent `docker.config` a fájlban. Az egyéni hitelesítési folyamat befejezéséhez telepíteni kell a Docker-ügyfelet, és futnia kell rajta.

## <a name="push-an-artifact"></a>Összetevő leküldése

Hozzon létre egy szövegfájlt egy helyi munkakönyvtárban egy adott mintaszöveg alapján. Például egy bash-rendszerhéjban:

```bash
echo "Here is an artifact!" > artifact.txt
```

A `oras push` paranccsal küldje el a szövegfájlt a beállításjegyzékbe. A következő példa leküldi a minta szöveges fájlt a `samples/artifact` tárházba. A beállításjegyzéket a rendszer a teljesen minősített *myregistry.azurecr.IO* (az összes kisbetűs) azonosítja. Az összetevő címkézve `1.0`van. Az összetevő alapértelmezés szerint nem definiált típust tartalmaz, amelyet a rendszer az *adathordozó Type* karakterlánca azonosít a `artifact.txt`fájlnév után. További típusok: [OCI](https://github.com/opencontainers/artifacts) -összetevők. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

A sikeres leküldések kimenete a következőhöz hasonló:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Ha az Azure CLI-t használja a beállításjegyzékben található összetevők kezelésére, futtassa a standard `az acr` parancsokat a lemezképek kezeléséhez. Az összetevő attribútumait például az az [ACR adattár show][az-acr-repository-show] paranccsal szerezheti be:

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

A kimenet a következőkhöz hasonló:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Összetevő lekérése

Futtassa a `oras pull` parancsot az összetevő beállításjegyzékből való lekéréséhez.

Először távolítsa el a szövegfájlt a helyi munkakönyvtárból:

```bash
rm artifact.txt
```

Az `oras pull` összetevő lekéréséhez futtassa a parancsot, és adja meg az összetevő leküldéséhez használt adathordozó típusát:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Ellenőrizze, hogy a lekérés sikeres volt-e:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Az összetevő eltávolítása (nem kötelező)

Az összetevő Azure Container registryből való eltávolításához használja az az [ACR repository delete][az-acr-repository-delete] parancsot. A következő példa eltávolítja az ott tárolt összetevőt:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>További lépések

* További információ [a ORAS-könyvtárról](https://github.com/deislabs/oras/tree/master/docs), beleértve az összetevők jegyzékfájljának konfigurálását is
* Tekintse meg a [OCI](https://github.com/opencontainers/artifacts) -összetevők tárházát, amely az új összetevő-típusokra vonatkozó információkat tartalmaz.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
