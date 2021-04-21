---
title: Tárolórendszerképek importálása
description: Tároló rendszerképeket importálhat egy Azure Container Registrybe Azure API-k használatával Anélkül, hogy Docker-parancsokat kellene futtatnia.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e7becadab7f23acd7b85d6d82fd8abbfa7608add
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781522"
---
# <a name="import-container-images-to-a-container-registry"></a>Tároló rendszerképének importálása egy tároló-beállításjegyzékbe

Docker-parancsok használata nélkül egyszerűen importálhat (másolhat) tároló rendszerképeket egy Azure Container Registrybe. Importálhat például rendszerképeket egy fejlesztési regisztrációs adatbázisból egy éles regisztrációs adatbázisba, vagy alapként használható rendszerképeket másolhat egy nyilvános regisztrációs adatbázisból.

Azure Container Registry a képek és egyéb összetevők meglévő regisztrációs adatbázisból való másolásának gyakori forgatókönyvét is kezeli:

* Rendszerképek importálása nyilvános beállításjegyzékből

* Rendszerképek vagy OCI-összetevők importálása, beleértve a Helm 3-diagramokat egy másik Azure-beli tároló-beállításjegyzékből, ugyanabban vagy egy másik Azure-előfizetésben vagy -bérlőben

* Importálás nem Azure-beli privát tárolójegyzékből

Az Azure Container Registrybe történő rendszerkép-importálás a következő előnyökkel jár a Docker PARANCSSORi felületi parancsokkal való használata felett:

* Mivel az ügyfélkörnyezetben nincs szükség helyi Docker-telepítésre, a támogatott operációs rendszer típusától függetlenül importálja a tárolórendszerképeket.

* Amikor többarchitektúraű rendszerképeket (például hivatalos Docker-rendszerképeket) importál, a rendszer a jegyzéklistában megadott összes architektúra és platform rendszerképét átmásolja.

* A céljegyzékhez való hozzáférésnek nem kell a regisztrációs adatbázis nyilvános végpontját használnia.

A tároló rendszerképének importálásához ehhez a cikkhez az Azure CLI-t Azure Cloud Shell helyileg kell futtatnia (2.0.55-ös vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!NOTE]
> Ha azonos tároló rendszerképeket kell elosztani több Azure-régióban, a Azure Container Registry támogatja a [georeplikációt is.](container-registry-geo-replication.md) Egy beállításjegyzék georeplikálása (prémium szolgáltatási szint szükséges) több régiót is kiszolgálhat azonos rendszerkép- és címkenevekkel egyetlen beállításjegyzékből.
>

> [!IMPORTANT]
> 2021 januárja óta módosult a rendszerkép importálása két Azure-beli tárolóregisztráló között:
> * A hálózati korlátozás alá tartozó Azure Container Registrybe [](allow-access-trusted-services.md) vagy onnan történő importáláshoz a korlátozott regisztrációs adatbázisra van szükség ahhoz, hogy a megbízható szolgáltatások hozzáférve megkerüljék a hálózatot. Alapértelmezés szerint a beállítás engedélyezve van, ami lehetővé teszi az importálást. Ha a beállítás nincs engedélyezve privát végponttal rendelkező újonnan létrehozott beállításjegyzékben vagy beállításjegyzékbeli tűzfalszabályokkal, az importálás sikertelen lesz. 
> * Importálási forrásként vagy célként használt meglévő, hálózatra korlátozott Azure Container Registryben a hálózati biztonsági funkció engedélyezése nem kötelező, de ajánlott.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure Container Registryvel, hozzon létre egy regisztrációs adatbázist. A lépésekért lásd: Rövid útmutató: Privát tároló-beállításjegyzék létrehozása [az Azure CLI használatával.](container-registry-get-started-azure-cli.md)

Ahhoz, hogy rendszerképet importálni tud egy Azure Container Registrybe, az identitásnak írási engedéllyel kell rendelkeznie a céljegyzékhez (legalább Közreműködő szerepkör, vagy egy egyéni szerepkör, amely engedélyezi az importImage műveletet). Lásd: [Azure Container Registry szerepkörök és engedélyek.](container-registry-roles.md#custom-roles) 

## <a name="import-from-a-public-registry"></a>Importálás nyilvános beállításjegyzékből

### <a name="import-from-docker-hub"></a>Importálás Docker Hub

Az az [acr import][az-acr-import] paranccsal például importálhatja a többarchitektúra-rendszerképet a Docker Hub egy `hello-world:latest` *myregistry* nevű regisztrációs adatbázisba. Mivel a egy hivatalos rendszerkép a Docker Hub, ez a rendszerkép `hello-world` az alapértelmezett `library` adattárban van. A képparaméter értékének tartalmaznia kell az adattár nevét és opcionálisan egy `--source` címkét. (Ha szükséges, a rendszerképet címke helyett jegyzékkivonata alapján is azonosíthatja, ami garantálja a rendszerkép egy adott verzióját.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

A parancs futtatásával ellenőrizheti, hogy több jegyzék van-e társítva ehhez a `az acr repository show-manifests` rendszerképhez:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Ha a [fiókkal Docker Hub,](https://www.docker.com/pricing)javasoljuk, hogy használja a hitelesítő adatokat, amikor lemezképet importál a Docker Hub. Adja át Docker Hub felhasználónevet és a jelszót vagy egy személyes hozzáférési [jogkivonatot](https://docs.docker.com/docker-hub/access-tokens/) paraméterként a `az acr import` számára. Az alábbi példa importál egy nyilvános rendszerképet a Docker Hub `tensorflow` adattárából a Docker Hub használatával:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importálás Microsoft Container Registry

Importálja például a `ltsc2019` Windows Server Core-rendszerképet az `windows` Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importálás azure-beli tároló-beállításjegyzékből ugyanabban az AD-bérlőben

Importálhat egy rendszerképet egy azure-beli tárolójegyzékből ugyanabba az AD-bérlőbe integrált Azure Active Directory engedélyekkel.

* Az identitásnak rendelkeznie kell Azure Active Directory engedéllyel ahhoz, hogy beolvassa a forrás beállításjegyzékből [](container-registry-roles.md#custom-roles) (Olvasó szerepkör), és importálni tudja a cél beállításjegyzékbe (Közreműködő szerepkör, vagy egy egyéni szerepkör, amely engedélyezi az importImage műveletet).

* A regisztrációs adatbázis lehet ugyanabban vagy egy másik Azure-előfizetésben ugyanabban a Active Directory bérlőben.

* [Előfordulhat, hogy](container-registry-access-selected-networks.md#disable-public-network-access) a forrásjegyzékhez való nyilvános hozzáférés le van tiltva. Ha a nyilvános hozzáférés le van tiltva, a beállításjegyzék bejelentkezési kiszolgálójának neve helyett erőforrás-azonosító alapján adja meg a forrás-beállításjegyzéket.

* Ha a forrás-beállításjegyzék és/vagy a céljegyzék privát végponttal vagy beállításjegyzékbeli tűzfalszabályokkal rendelkezik, győződjön meg arról, hogy a korlátozott beállításjegyzék engedélyezi a megbízható szolgáltatások hozzáférését a hálózathoz. [](allow-access-trusted-services.md)

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importálás ugyanabban az előfizetésben egy regisztrációs adatbázisból

Importálhatja például a rendszerképet `aci-helloworld:latest` a *mysourceregistry* forrásjegyzékből a *myregistry* adatbázisba ugyanabban az Azure-előfizetésben.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Az alábbi példa importálja `aci-helloworld:latest` a rendszerképet a *myregistry* forrásjegyzékből a *mysourceregistry* forrásjegyzékbe, ahol a regisztrációs adatbázis nyilvános végpontjának elérése le van tiltva. A paraméterrel meg kell adni a forrás-beállításjegyzék `--registry` erőforrás-azonosítóját. Figyelje meg, hogy a paraméter csak a forrásadattárat és a címkét adja meg, a beállításjegyzék bejelentkezési `--source` kiszolgálójának nevét nem.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Az alábbi példa egy képet importál jegyzékkivonattal (SHA-256 kivonat, ként jelölő ) a `sha256:...` címke helyett:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importálás másik előfizetésben egy beállításjegyzékből

A következő példában a *mysourceregistry* egy másik előfizetésben van, mint a *myregistry* ugyanabban a Active Directory bérlőben. A paraméterrel meg kell adni a forrás-beállításjegyzék `--registry` erőforrás-azonosítóját. Figyelje meg, hogy a paraméter csak a forrásadattárat és a címkét adja meg, a beállításjegyzék bejelentkezési `--source` kiszolgálójának nevét nem.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importálás beállításjegyzékből szolgáltatásnév hitelesítő adataival

Ha integrált Active Directory engedélyekkel nem fér hozzá a regisztrációs adatbázisból való importáláshoz, használja a forrás beállításjegyzék szolgáltatásnévhez való hitelesítő adatait (ha elérhető). A forrásjegyzékhez ACRPull-hozzáféréssel Active Directory szolgáltatásnév appID-ját és jelszavát kell megadnia. [](container-registry-auth-service-principal.md) A szolgáltatásnév használata olyan buildrendszerek és más felügyelet nélküli rendszerek esetében hasznos, amelyek lemezképeket importálnak a regisztrációs adatbázisba.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importálás másik AD-bérlőn található Azure Container Registryből

Ha egy másik azure-bérlőben található Azure Container Registryből szeretné importálni, adja meg Azure Active Directory a forrás-beállításjegyzéket a bejelentkezési kiszolgáló neve alapján, és adjon meg olyan felhasználónevet és jelszót, amelyek lehetővé teszik a lekért hozzáférést a beállításjegyzékhez. Használhat például egy adattárra vonatkozó jogkivonatot és jelszót, vagy egy olyan [Active Directory-szolgáltatásnév](container-registry-repository-scoped-permissions.md) appID-ját és jelszavát, amely ACRPull hozzáféréssel rendelkezik a forrás beállításjegyzékhez. [](container-registry-auth-service-principal.md) 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importálás nem Azure-beli privát tárolójegyzékből

Rendszerkép importálása nem Azure-beli privát beállításjegyzékből olyan hitelesítő adatok megadásával, amelyek lehetővé teszik a regisztrációs adatbázishoz való lekért hozzáférést. Lekért például egy rendszerképet egy privát Docker-regisztrációs adatbázisból: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan importálhat tároló rendszerképeket egy Azure-beli tároló-beállításjegyzékbe egy nyilvános regisztrációs adatbázisból vagy egy másik privát regisztrációs adatbázisból. A rendszerkép-importálás további beállításaiért tekintse meg az [az acr import][az-acr-import] parancsreferenciát. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
