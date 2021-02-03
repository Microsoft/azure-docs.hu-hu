---
title: Tárolórendszerképek importálása
description: A tároló lemezképeit az Azure API-k használatával importálhatja egy Azure Container registrybe anélkül, hogy a Docker-parancsokat kellene futtatnia.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: 364c90b857d0d7d479152e2aa56db4d80041f037
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524498"
---
# <a name="import-container-images-to-a-container-registry"></a>Tároló-lemezképek importálása egy tároló-beállításjegyzékbe

A tárolók lemezképeit egyszerűen importálhatja egy Azure Container registrybe Docker-parancsok használata nélkül. Például importálhatja a lemezképeket egy fejlesztői beállításjegyzékből egy éles beállításjegyzékbe, vagy átmásolhatja az alaplemezképeket egy nyilvános beállításjegyzékből.

Azure Container Registry a lemezképek meglévő beállításjegyzékből való másolásának számos gyakori forgatókönyvét kezeli:

* Importálás nyilvános beállításjegyzékből

* Importálás másik Azure-beli tároló-beállításjegyzékből ugyanazon vagy egy másik Azure-előfizetésben vagy-bérlőben

* Importálás nem Azure Private Container registryből

A rendszerkép importálása az Azure Container registrybe az alábbi előnyökkel jár a Docker CLI-parancsok használatakor:

* Mivel az ügyfél-környezet nem igényel helyi Docker-telepítést, importálja a tárolók rendszerképét a támogatott operációsrendszer-típustól függetlenül.

* Ha többarchitektúrás képeket (például hivatalos Docker-lemezképeket) importál, a jegyzékben megadott összes architektúrához és platformhoz tartozó lemezképet másolja a rendszer.

* A cél-beállításjegyzék eléréséhez nem szükséges a beállításjegyzék nyilvános végpontjának használata.

A tároló-lemezképek importálásához ehhez a cikkhez az Azure CLI-t Azure Cloud Shell vagy helyileg kell futtatni (2.0.55 vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!NOTE]
> Ha azonos tároló-lemezképeket kell terjeszteni több Azure-régióban, Azure Container Registry a [geo-replikációt](container-registry-geo-replication.md)is támogatja. Ha földrajzilag replikál egy beállításjegyzéket (prémium szintű szolgáltatási szint szükséges), egyszerre több régiót is felhasználhat, amelyek azonos képpel és címkékkel rendelkeznek egyetlen beállításjegyzékből.
>

> [!IMPORTANT]
> A két Azure Container-nyilvántartó közötti Képimportálási változás a 2021-as januári értékkel lett bevezetve:
> * A hálózatra korlátozott Azure Container Registry-be vagy abból történő importáláshoz a korlátozott beállításjegyzéknek engedélyeznie kell a [**megbízható szolgáltatások hozzáférését**](allow-access-trusted-services.md) a hálózat megkerüléséhez. Alapértelmezés szerint a beállítás engedélyezve van, ami lehetővé teszi az importálást. Ha a beállítás nincs engedélyezve egy olyan újonnan létrehozott beállításjegyzékben, amely privát végponttal vagy a beállításjegyzék tűzfalszabályok szabályaival rendelkezik, az importálás sikertelen lesz. 
> * Egy meglévő, az importálás forrásaként vagy célként használt, hálózatra korlátozott Azure Container registryben ez a hálózati biztonsági funkció nem kötelező, de ajánlott.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure Container registryvel, hozzon létre egy beállításjegyzéket. A lépéseket a rövid útmutató [: privát tároló beállításjegyzékének létrehozása az Azure CLI használatával](container-registry-get-started-azure-cli.md)című témakörben tekintheti meg.

Egy rendszerkép Azure Container registrybe való importálásához az identitásnak írási engedéllyel kell rendelkeznie a célként megadott beállításjegyzékhez (legalább közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely engedélyezi a importImage műveletet). Lásd: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importálás nyilvános beállításjegyzékből

### <a name="import-from-docker-hub"></a>Importálás a Docker hub-ból

Például az az [ACR import][az-acr-import] paranccsal importálhatja a multi-Architecture `hello-world:latest` rendszerképet a Docker hub-ból egy *myregistry* nevű beállításjegyzékbe. Mivel a `hello-world` a Docker hub hivatalos rendszerképe, ez a rendszerkép az alapértelmezett `library` tárházban található. Adja meg az adattár nevét és opcionálisan egy címkét a `--source` rendszerkép paraméter értékében. (A képet a jegyzékfájlja alapján is azonosíthatja, a címke alapján, amely a rendszerkép egy adott verzióját garantálja.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

A következő parancs futtatásával ellenőrizheti, hogy a képhez több jegyzékfájl is társítva van-e `az acr repository show-manifests` :

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Az alábbi példa egy nyilvános rendszerképet importál a `tensorflow` tárházból a Docker hub-ban:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importálás a Microsoft Container Registryból

Importálja például a `ltsc2019` Windows Server Core rendszerképet a `windows` tárházból a Microsoft Container Registryban.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importálás egy Azure Container registryből ugyanabban az AD-bérlőben

Az Azure Container registryből is importálhat rendszerképet ugyanabban az AD-bérlőn, integrált Azure Active Directory engedélyek használatával.

* Az identitásnak Azure Active Directory engedélyekkel kell rendelkeznie a forrás-beállításjegyzékből (olvasói szerepkör) való olvasáshoz, illetve a cél beállításjegyzékbe való importáláshoz (közreműködő szerepkör vagy egy [Egyéni szerepkör](container-registry-roles.md#custom-roles) , amely engedélyezi a importImage műveletet).

* A beállításjegyzék lehet ugyanabban a Active Directory-bérlőben vagy egy másik Azure-előfizetésben.

* Előfordulhat, hogy a forrás beállításjegyzékhez [való nyilvános hozzáférés](container-registry-access-selected-networks.md#disable-public-network-access) le van tiltva. Ha a nyilvános hozzáférés le van tiltva, adja meg a forrás beállításjegyzéket erőforrás-azonosító alapján a beállításjegyzék bejelentkezési kiszolgálójának neve helyett.

* Ha a forrás-és/vagy a célként megadott beállításjegyzékben van egy magánhálózati vagy beállításjegyzékbeli tűzfalszabály, győződjön meg arról, hogy a korlátozott beállításjegyzék [lehetővé teszi a megbízható szolgáltatások](allow-access-trusted-services.md) számára a hálózat elérését.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importálás ugyanabba az előfizetésbe tartozó beállításjegyzékből

Importálhatja például a `aci-helloworld:latest` rendszerképet egy forrás-beállításjegyzék *mysourceregistry* , hogy ugyanabba az Azure-előfizetésbe *myregistry* .

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

A következő példa importálja a `aci-helloworld:latest` rendszerképet egy olyan *myregistry* - *mysourceregistry* , amelyben a beállításjegyzék nyilvános végpontjának hozzáférése le van tiltva. Adja meg a forrás-beállításjegyzék erőforrás-AZONOSÍTÓját a (z `--registry` ) paraméterrel. Figyelje `--source` meg, hogy a paraméter csak a forrás tárházat és a címkét adja meg, nem a beállításjegyzék bejelentkezési kiszolgálójának nevét.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Az alábbi példa egy képet a manifest Digest (SHA-256 kivonat, amely a következőképpen jelenik meg) alapján importál a `sha256:...` címke helyett:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importálás egy másik előfizetésben lévő beállításjegyzékből

A következő példában a *mysourceregistry* egy másik előfizetésben található a *myregistry* ugyanazon a Active Directory bérlőn. Adja meg a forrás-beállításjegyzék erőforrás-AZONOSÍTÓját a (z `--registry` ) paraméterrel. Figyelje `--source` meg, hogy a paraméter csak a forrás tárházat és a címkét adja meg, nem a beállításjegyzék bejelentkezési kiszolgálójának nevét.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importálás beállításjegyzékből az egyszerű szolgáltatásnév hitelesítő adataival

Ha olyan beállításjegyzékből szeretne importálni, amely nem fér hozzá az integrált Active Directory engedélyek használatával, az egyszerű szolgáltatás hitelesítő adatait (ha elérhető) is használhatja a forrás-beállításjegyzékbe. Adja meg egy olyan Active Directory [egyszerű szolgáltatásnév](container-registry-auth-service-principal.md) appID és jelszavát, amely ACRPull hozzáféréssel rendelkezik a forrás-beállításjegyzékhez. Egyszerű szolgáltatásnév használata olyan rendszerek és más felügyelet nélküli rendszerek esetében hasznos, amelyeknek lemezképeket kell importálni a beállításjegyzékbe.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importálás egy Azure Container registryből egy másik AD-bérlőben

Ha egy másik Azure Active Directory bérlőben lévő Azure Container registryből szeretne importálni, adja meg a forrás beállításjegyzéket a bejelentkezési kiszolgáló neve alapján, és adja meg a felhasználónevet és a jelszót hitelesítő adatokat, amelyek engedélyezik a beállításjegyzékhez való lekéréses hozzáférést. Használjon például egy [adattárház-hatókörű tokent](container-registry-repository-scoped-permissions.md) és jelszót, vagy egy Active Directory [egyszerű](container-registry-auth-service-principal.md) appID és jelszavát, amely ACRPull hozzáféréssel rendelkezik a forrás-beállításjegyzékhez. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importálás nem Azure Private Container registryből

Rendszerkép importálása nem Azure-beli privát beállításjegyzékből olyan hitelesítő adatok megadásával, amelyek lehetővé teszik a lekéréses hozzáférést a beállításjegyzékhez. Például egy privát Docker-beállításjegyzékből származó rendszerkép lekérése: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan importálhatja a tároló lemezképeit egy nyilvános beállításjegyzékből vagy egy másik privát beállításjegyzékből egy Azure Container registrybe. További Képimportálási lehetőségekért tekintse meg az az [ACR import][az-acr-import] parancs-referenciát. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
