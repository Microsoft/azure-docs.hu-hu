---
title: Hitelesítés szolgáltatásnévvel
description: Hozzáférés biztosítása a privát tároló beállításjegyzékében lévő rendszerképekhez egy Azure Active Directory egyszerű szolgáltatásnév használatával.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 8d49628576a1c337efaea3e5286fef00e39def17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259145"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry hitelesítés egyszerű szolgáltatásokkal

Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév használatával biztosíthatja a tárolók rendszerképét `docker push` , és `pull` hozzáférhet a tároló-beállításjegyzékhez. Egyszerű szolgáltatásnév használatával hozzáférést biztosíthat a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz.

## <a name="what-is-a-service-principal"></a>Mi az a szolgáltatásnév?

Az Azure AD- *szolgáltatások* hozzáférést biztosítanak az előfizetésében található Azure-erőforrásokhoz. Azt is megteheti, hogy egy egyszerű szolgáltatás felhasználói identitása egy szolgáltatáshoz, ahol a "szolgáltatás" bármely olyan alkalmazás, szolgáltatás vagy platform, amelynek hozzá kell férnie az erőforrásokhoz. Az egyszerű szolgáltatásnevet csak a megadott erőforrásokra vonatkozó hozzáférési jogosultságokkal konfigurálhatja. Ezután konfigurálja az alkalmazást vagy szolgáltatást úgy, hogy az egyszerű szolgáltatásnév hitelesítő adatait használja az erőforrásokhoz való hozzáféréshez.

Azure Container Registry kontextusában létrehozhat egy Azure AD-szolgáltatást, amely lekéréses, leküldéses és lekéréses, illetve egyéb engedélyekkel rendelkezik az Azure-beli privát beállításjegyzékhez. A teljes listát itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Miért érdemes egyszerű szolgáltatást használni?

Az Azure AD egyszerű szolgáltatásnév használatával hatókörön belüli hozzáférést biztosíthat a saját tároló-beállításjegyzékhez. Hozzon létre különböző egyszerű szolgáltatásokat az egyes alkalmazásokhoz vagy szolgáltatásokhoz, amelyek mindegyike testreszabott hozzáférési jogokkal rendelkezik a beállításjegyzékhez. Továbbá, mivel elkerülheti a hitelesítő adatok megosztását a szolgáltatások és az alkalmazások között, elforgathatja a hitelesítő adatokat, vagy visszavonhatja a hozzáférést csak az egyszerű szolgáltatásnév (és így az alkalmazás) közül.

Például úgy konfigurálhatja a webalkalmazást, hogy olyan egyszerű szolgáltatásnevet használjon `pull` , amely csak képhozzáférést biztosít, míg a Build rendszer olyan szolgáltatásnevet használ, amely egyaránt `push` és hozzáféréssel rendelkezik `pull` . Ha megváltoztatja az alkalmazás fejlesztését, elforgathatja a szolgáltatás egyszerű hitelesítő adatait anélkül, hogy ez hatással lenne a Build rendszerre.

## <a name="when-to-use-a-service-principal"></a>Mikor kell szolgáltatásnevet használni

Egy egyszerű szolgáltatásnév használatával kell megadni a beállításjegyzék-hozzáférést a **fej nélküli forgatókönyvekben**. Ez azt jelenti, hogy minden olyan alkalmazás, szolgáltatás vagy parancsfájl, amely automatizált vagy más módon felügyelet nélkül leküld vagy lehívhatja a tárolók lemezképeit. Például:

  * *Pull*: tárolók üzembe helyezése a beállításjegyzékből a Kubernetes, a DC/os és a Docker Swarm rendszerbe. Lekérheti a tároló-beállításjegyzékből a kapcsolódó Azure-szolgáltatásokat, például az [Azure Kubernetes Service (ak)](../aks/cluster-container-registry-integration.md), a [Azure Container Instances](container-registry-auth-aci.md), a [app Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](../service-fabric/index.yml)és más szolgáltatásait is.

  * *Leküldés*: tároló-lemezképek létrehozása és továbbítása egy beállításjegyzékbe folyamatos integrációs és üzembe helyezési megoldások, például az Azure-folyamatok vagy a Jenkins használatával.

A beállításjegyzékhez való egyéni hozzáféréshez, például amikor manuálisan húz le egy tároló-rendszerképet a fejlesztői munkaállomásra, javasoljuk, hogy a beállításjegyzék-hozzáférés helyett saját [Azure ad-identitást](container-registry-authentication.md#individual-login-with-azure-ad) használjon (például az [az ACR login][az-acr-login]használatával).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Mintaparancsfájlok

Az előző minta parancsfájlokat az Azure CLI-hez, a GitHubon, valamint a Azure PowerShell verzióihoz is megtalálja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatással

Ha már rendelkezik egy olyan egyszerű szolgáltatással, amely hozzáférést kapott a tároló-beállításjegyzékhez, a hitelesítő adatait megadhatja a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz, vagy megadhatja azokat a `docker login` paranccsal. Használja a következő értékeket:

* **Felhasználónév** – egyszerű szolgáltatásnév alkalmazásának azonosítója (más néven *ügyfél-azonosító*)
* **Jelszó** – egyszerű szolgáltatás jelszava (más néven *ügyfél-titok*)

Minden érték az űrlap GUID-azonosítója `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . 

> [!TIP]
> Egy egyszerű szolgáltatásnév jelszavát az az [ad SP reset-hitelesítőadats](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) parancs futtatásával lehet újból előállítani.
>

### <a name="use-credentials-with-azure-services"></a>Hitelesítő adatok használata az Azure-szolgáltatásokkal

A szolgáltatás egyszerű hitelesítő adatait bármely olyan Azure-szolgáltatáshoz használhatja, amely egy Azure Container registryben van hitelesítve.  A beállításjegyzék rendszergazdai hitelesítő adatai helyett használja a szolgáltatás egyszerű hitelesítő adatait a különböző forgatókönyvek esetében.

A hitelesítő adatok használatával például lekérheti a rendszerképet egy Azure Container Registry-ből a [Azure Container Instancesba](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Használat a Docker-bejelentkezéssel

`docker login`Az egyszerű szolgáltatásnév használatával is futtatható. A következő példában a szolgáltatás egyszerű alkalmazásának azonosítója a környezeti változóban `$SP_APP_ID` , a változóban pedig a jelszót adja át `$SP_PASSWD` . A Docker hitelesítő adatainak kezelésével kapcsolatos ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

A bejelentkezést követően a Docker gyorsítótárazza a hitelesítő adatokat.

### <a name="use-with-certificate"></a>Használat tanúsítvánnyal

Ha hozzáadta a tanúsítványt az egyszerű szolgáltatáshoz, bejelentkezhet az Azure CLI-be tanúsítványalapú hitelesítéssel, majd az az [ACR login][az-acr-login] paranccsal elérheti a beállításjegyzéket. Ha a tanúsítványokat jelszó helyett titkosként használja, a parancssori felület használata további biztonságot nyújt. 

Egy önaláírt tanúsítvány hozható létre [egy egyszerű szolgáltatásnév létrehozásakor](/cli/azure/create-an-azure-service-principal-azure-cli). Vagy adjon hozzá egy vagy több tanúsítványt egy meglévő egyszerű szolgáltatáshoz. Ha például a cikkben szereplő parancsfájlok egyikét használja egy olyan egyszerű szolgáltatásnév létrehozásához vagy frissítéséhez, amely jogosult a rendszerképek beállításjegyzékből való lekérésére vagy leküldésére, adjon hozzá egy tanúsítványt az az [ad SP hitelesítőadat-visszaállítási][az-ad-sp-credential-reset] parancs használatával.

Ahhoz, hogy az egyszerű szolgáltatásnév használatával [bejelentkezzen az Azure CLI-be](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), a tanúsítványnak PEM formátumban kell lennie, és tartalmaznia kell a titkos kulcsot. Ha a tanúsítvány nem a szükséges formátumban van, használjon egy eszközt, például `openssl` a konvertáláshoz. Amikor az az [login][az-login] paranccsal jelentkezik be a CLI-be az egyszerű szolgáltatásnév használatával, adja meg a szolgáltatásnév alkalmazás-azonosítóját és a Active Directory bérlő azonosítóját is. A következő példa környezeti változókként jeleníti meg ezeket az értékeket:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Ezután futtassa az [ACR login][az-acr-login] parancsot a beállításjegyzékben való hitelesítéshez:

```azurecli
az acr login --name myregistry
```

A CLI azt a tokent használja, amely akkor jön létre, amikor a `az login` -munkamenetet a beállításjegyzékkel hitelesíti.

## <a name="next-steps"></a>Következő lépések

* Az Azure Container Registry szolgáltatással történő hitelesítéssel kapcsolatos egyéb forgatókönyvek [hitelesítésének áttekintését](container-registry-authentication.md) itt tekintheti meg.

* Az Azure Key Vault egy tároló-beállításjegyzékhez tartozó egyszerű hitelesítő adatok tárolására és lekérésére példaként tekintse meg az oktatóanyagot, amely [egy tároló lemezképének összeépítésére és üzembe helyezésére használható az ACR-feladatokkal](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
