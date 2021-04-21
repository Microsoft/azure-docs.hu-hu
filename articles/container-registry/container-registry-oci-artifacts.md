---
title: OCI-összetevő leküldése és leküldése
description: Open Container Initiative- (OCI-) összetevők leküldése és leküldése privát tároló-beállításjegyzék használatával az Azure-ban
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 399bb001432759556cd0ba8bf15f7738dd4edb7c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781486"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>OCI-összetevő leküldése és leküldése egy Azure Container Registry használatával

Az Azure Container Registry használatával [Open Container Initiative- (OCI-)](container-registry-image-formats.md#oci-artifacts) összetevők, valamint Docker- és Docker-kompatibilis tároló-rendszerképek tárolhatók és kezelhetők.

Ennek a képességnek a szemléltetését ez a cikk bemutatja, hogyan használható az [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) eszköz egy minta-összetevő – egy szövegfájl – leküldéséhez egy Azure Container Registrybe. Ezután húzza le az összetevőt a beállításjegyzékből. Az Azure Container Registryben számos különböző OCI-összetevőt kezelhet az egyes összetevőknek megfelelő különböző parancssori eszközökkel.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használhatja például a [Azure Portal](container-registry-get-started-portal.md) vagy az Azure [CLI-t.](container-registry-get-started-azure-cli.md)
* **ORAS eszköz** – Töltse le és telepítse az operációs rendszerének aktuális ORAS-kiadását a [GitHub-adattárból.](https://github.com/deislabs/oras/releases) Az eszköz tömörített tarballként (fájlként) van `.tar.gz` kiadva. Bontsa ki és telepítse a fájlt az operációs rendszerének megfelelő szabványos eljárásokkal.
* **Azure Active Directory szolgáltatásnév (nem kötelező)** – Az ORAS-sel való közvetlen hitelesítéshez hozzon létre [egy](container-registry-auth-service-principal.md) egyszerű szolgáltatást a beállításjegyzék eléréséhez. Győződjön meg arról, hogy a szolgáltatásnévhez hozzá van rendelve egy szerepkör, például az AcrPush, hogy rendelkezik a leküldési és leküldési összetevőkre vonatkozó engedélyekkel.
* **Azure CLI (nem kötelező)** – Az egyéni identitások csak az Azure CLI helyi telepítésével használhatók. A 2.0.71-es vagy újabb verzió használata javasolt. A `az --version ` verzió megkereshez futtassa a következőt: . Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker (nem kötelező)** – Az egyéni identitások csak akkor használhatók, ha a Docker helyileg van telepítve a beállításjegyzékben való hitelesítéshez. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.


## <a name="sign-in-to-a-registry"></a>Bejelentkezés egy beállításjegyzékbe

Ez a szakasz két javasolt munkafolyamatot mutat be a regisztrációs adatbázisba való bejelentkezéshez a használt identitástól függően. Válassza ki a környezetének megfelelő módszert.

### <a name="sign-in-with-oras"></a>Bejelentkezés AZ ORAS-sel

[Leküldési jogosultságokkal](container-registry-auth-service-principal.md) egy egyszerű szolgáltatás használatával az paranccsal jelentkezzen be a beállításjegyzékbe a szolgáltatásnév alkalmazásazonosítójával `oras login` és jelszavával. Adja meg a teljes regisztrációs adatbázisnevet (csak kisbetűket), ebben az esetben a *myregistry.azurecr.io.* A szolgáltatásnév alkalmazásazonosítóját a környezeti változóban, a jelszót pedig a `$SP_APP_ID` változóban kell `$SP_PASSWD` megadni.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

A Stdintől származó jelszó beolvassához használja a következőt: `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Bejelentkezés az Azure CLI használatával

[Jelentkezzen be az](/cli/azure/authenticate-azure-cli) Azure CLI-be az identitásával az összetevők tárolójegyzékből való leküldéséhez és leküldéséhez.

Ezután az az acr login Azure [CLI-paranccsal férhet](/cli/azure/acr#az_acr_login) hozzá a regisztrációs adatbázishoz. Például egy *myregistry* nevű beállításjegyzékben való hitelesítéshez:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` A a Docker-ügyfelet használja egy Azure Active Directory jogkivonat beállításhoz a `docker.config` fájlban. Az egyéni hitelesítési folyamat befejezéséhez telepíteni és futnia kell a Docker-ügyfélnek.

## <a name="push-an-artifact"></a>Összetevő leküldése

Hozzon létre egy szövegfájlt egy helyi munkakönyvtárban néhány mintaszöveggel. Például egy Bash-rendszerhéjban:

```bash
echo "Here is an artifact" > artifact.txt
```

A parancs `oras push` használatával ezt a szövegfájlt lekérte a regisztrációs adatbázisba. Az alábbi példa a minta szövegfájlt küld az `samples/artifact` adattára. A beállításjegyzék azonosítása a regisztrációs adatbázis teljes myregistry.azurecr.io *(csak* kisbetűk). Az összetevő címkével van `1.0` megjelölve. Az összetevőnek alapértelmezés szerint nincs meghatározva típusa,  amelyet a fájlnevet követő médiatípus-sztring `artifact.txt` azonosít. További [típusok: OCI Artifacts](https://github.com/opencontainers/artifacts) (OCI-összetevők). 

**Linux vagy macOS**

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

A sikeres leküldések kimenete az alábbihoz hasonló:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Ha az Azure CLI-t használja, a beállításjegyzékben az összetevők kezeléséhez futtassa a szabványos parancsokat a `az acr` rendszerképek kezeléséhez. Az az [acr repository show][az-acr-repository-show] paranccsal például lekérte az összetevő attribútumát:

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

## <a name="pull-an-artifact"></a>Összetevő lekérte

Az `oras pull` paranccsal lekért összetevőt a regisztrációs adatbázisból.

Először távolítsa el a szövegfájlt a helyi munkakönyvtárból:

```bash
rm artifact.txt
```

Az összetevő leküldéshez futtassa a parancsot, és adja meg az összetevő `oras pull` leküldéshez használt adathordozó típusát:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Ellenőrizze, hogy a lekért művelet sikeres volt-e:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Távolítsa el az összetevőt (nem kötelező)

Ha el szeretné távolítani az összetevőt az Azure Container Registryből, használja az [az acr repository delete][az-acr-repository-delete] parancsot. Az alábbi példa eltávolítja az ott tárolt összetevőt:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Példa: Docker-rendszerkép összeállítása OCI-összetevőből

A tároló rendszerképének forráskódja és bináris kódja OCI-összetevőként tárolható az Azure Container Registryben. Egy ACR-feladat buildelési környezeteként hivatkozhat egy [forrás-összetevőre.](container-registry-tasks-overview.md) Ez a példa bemutatja, hogyan tárolhat dockerfile-t OCI-összetevőként, majd hogyan hivatkozhat az összetevőre egy tároló rendszerképének felépítéséhez.

Hozzon létre például egy egysoros Dockerfile-t:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Jelentkezzen be a cél tárolójegyzékbe.

```azurecli
az login
az acr login --name myregistry
```

Hozzon létre és leküld egy új OCI-összetevőt a céljegyzékbe az `oras push` paranccsal. Ez a példa beállítja az összetevő alapértelmezett adathordozó-típusát.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Futtassa [az az acr build](/cli/azure/acr#az_acr_build) parancsot a hello-world rendszerkép buildelési környezetként való használatával az új összetevő használatával:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Következő lépések

* További információ az [ORAS-kódtárról,](https://github.com/deislabs/oras/tree/master/docs)beleértve egy összetevő jegyzékfájl konfigurálását
* Az új összetevőtípusokkal kapcsolatos információkért látogasson el az [OCI Artifacts](https://github.com/opencontainers/artifacts) (OCI-összetevők) adattárba.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
