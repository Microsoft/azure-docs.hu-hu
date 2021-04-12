---
title: Beállításjegyzék-hitelesítési beállítások
description: A privát Azure Container Registry hitelesítési lehetőségei, beleértve a Azure Active Directory identitással való bejelentkezést, az egyszerű szolgáltatásnév használatát és a nem kötelező rendszergazdai hitelesítő adatok használatát.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: d12895502ecd30991fbef836903a8ceea445b770
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285501"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Hitelesítés Azure Container registryvel

Az Azure Container Registry szolgáltatással többféleképpen is végezhet hitelesítést, amelyek mindegyike egy vagy több beállításjegyzék-használati forgatókönyvre alkalmazható.

Az ajánlott módszerek közé tartozik a beállításjegyzék közvetlen hitelesítése [Egyéni bejelentkezéssel](#individual-login-with-azure-ad), illetve az alkalmazások és a tároló-rendszerszervezők felügyelet nélküli vagy "fej nélküli" hitelesítést végezhetnek Azure Active Directory (Azure ad) [egyszerű szolgáltatásnév](#service-principal)használatával.

## <a name="authentication-options"></a>Hitelesítési lehetőségek

Az alábbi táblázat az elérhető hitelesítési módszereket és a tipikus forgatókönyveket sorolja fel. További részletekért tekintse meg a csatolt tartalmakat.

| Metódus                               | Hitelesítés                                           | Forgatókönyvek                                                            | Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)                             | Korlátozások                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Egyedi AD-identitás](#individual-login-with-azure-ad)                | `az acr login` Az Azure CLI-ben                             | Interaktív Leküldés/lekérés fejlesztőknek, tesztelőknek                                    | Igen                              | Az AD-jogkivonatot 3 óránként meg kell újítani     |
| [AD egyszerű szolgáltatás](#service-principal)                  | `docker login`<br/><br/>`az acr login` Az Azure CLI-ben<br/><br/> Beállításjegyzék-bejelentkezési beállítások az API-kon vagy az eszközökön<br/><br/> [Kubernetes-lekérési titok](container-registry-auth-kubernetes.md)                                           | Felügyelet nélküli leküldés CI/CD-folyamatból<br/><br/> Felügyelet nélküli lekérés az Azure-ba vagy a külső szolgáltatásokra  | Igen                              | Az SP-jelszó alapértelmezett lejárati ideje 1 év       |                                                           
| [Integrálás az AK-val](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Beállításjegyzék csatolása az AK-fürt létrehozásakor vagy frissítésekor  | Felügyelet nélküli lekérés az AK-fürthöz                                                  | Nem, csak a lekéréses hozzáférés             | Csak ak-fürttel érhető el            |
| [Az Azure-erőforrásokhoz készült felügyelt identitások](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` Az Azure CLI-ben                                       | Felügyelet nélküli leküldés az Azure CI/CD-folyamatból<br/><br/> Felügyelet nélküli lekérés az Azure-szolgáltatásokhoz<br/><br/>   | Igen                              | Kizárólag az [Azure-erőforrások felügyelt identitásait támogató](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) Azure-szolgáltatások kiválasztása              |
| [Rendszergazdai felhasználó](#admin-account)                            | `docker login`                                          | Interaktív leküldéses/lekéréses egyéni fejlesztő vagy teszter<br/><br/>A rendszerképek a beállításjegyzékből Azure App Service vagy Azure Container Instancesba való telepítése                      | Nem, mindig lekéréses és leküldéses hozzáférés  | Egy fiók/beállításjegyzék, nem ajánlott több felhasználó számára         |
| [Tárház – hatókörön belüli hozzáférési jogkivonat](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` Az Azure CLI-ben   | Interaktív leküldéses/lekéréses adattár egyéni fejlesztő vagy teszter által<br/><br/> Felügyelet nélküli Leküldés/lekérés a tárházba egyéni rendszer vagy külső eszköz használatával                  | Igen                              | Jelenleg nincs integrálva az AD Identity szolgáltatással  |

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezés az Azure AD-vel

Ha közvetlenül dolgozik a beállításjegyzékben, például képeket húz át egy fejlesztői munkaállomásról a létrehozott beállításjegyzékbe, és leküldi a képeket a saját Azure-identitásával. Jelentkezzen be az [Azure CLI](/cli/azure/install-azure-cli) -be az az [login](/cli/azure/reference-index#az-login)paranccsal, majd futtassa az az [ACR login](/cli/azure/acr#az-acr-login) parancsot:

```azurecli
az login
az acr login --name <acrName>
```

Amikor bejelentkezik a szolgáltatásba `az acr login` , a CLI a-ben létrehozott jogkivonatot használja, `az login` hogy zökkenőmentesen hitelesítse a munkamenetet a beállításjegyzékben. A hitelesítési folyamat befejezéséhez telepíteni és futtatni kell a Docker CLI-t és a Docker démont a környezetében. `az acr login` a a Docker-ügyfél használatával állít be Azure Active Directory tokent a `docker.config` fájlban. Ha így jelentkezett be, a hitelesítő adatai gyorsítótárazva lesznek, és `docker` a munkamenetben lévő további parancsok nem igénylik a felhasználónevet vagy a jelszót.

> [!TIP]
> Az `az acr login` Egyéni identitások hitelesítésére is használható, ha a Docker-rendszerképektől eltérő összetevőket szeretne leküldeni vagy lekérni a beállításjegyzékbe, például [OCI](container-registry-oci-artifacts.md)-összetevőkre.  

A beállításjegyzékhez való hozzáféréshez a által használt jogkivonat `az acr login` **3 órára** érvényes, ezért javasoljuk, hogy a parancs futtatása előtt mindig jelentkezzen be a beállításjegyzékbe `docker` . Ha a token lejár, akkor a `az acr login` parancs ismételt hitelesítésével frissítheti azt. 

`az acr login`Az Azure-identitások használatával [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)biztosít. Bizonyos esetekben előfordulhat, hogy be kell jelentkeznie egy beállításjegyzékbe a saját egyéni identitásával az Azure AD-ben, vagy más Azure-felhasználókat adott [Azure-szerepkörökkel és-engedélyekkel](container-registry-roles.md)kell konfigurálnia. A szolgáltatások közötti forgatókönyvek esetében, illetve egy munkacsoport vagy egy fejlesztési munkafolyamat igényének kezeléséhez, ahol nem szeretne egyéni hozzáférést kezelni, az [Azure-erőforrások felügyelt identitásával](container-registry-authentication-managed-identity.md)is bejelentkezhet.

### <a name="az-acr-login-with---expose-token"></a>az ACR login with--megvilágít-token

Bizonyos esetekben előfordulhat, hogy hitelesítenie kell a szolgáltatást, `az acr login` Ha a Docker-démon nem fut a környezetben. Előfordulhat például, hogy `az acr login` egy Azure Cloud Shell parancsfájlban kell futtatnia, amely a Docker parancssori felületét biztosítja, de nem futtatja a Docker-démont.

Ehhez a forgatókönyvhöz `az acr login` először futtassa a `--expose-token` paramétert. Ez a lehetőség hozzáférési tokent tesz elérhetővé a Docker parancssori felületén keresztüli bejelentkezés helyett.

```azurecli
az acr login --name <acrName> --expose-token
```

A kimenet az itt rövidített hozzáférési tokent jeleníti meg:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
A beállításjegyzék-hitelesítés esetében javasoljuk, hogy a jogkivonat hitelesítő adatait biztonságos helyen tárolja, és kövesse az ajánlott eljárásokat a [Docker-bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/)azonosítók kezeléséhez. Tárolja például a jogkivonat értékét egy környezeti változóban:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Ezután futtassa `docker login` a parancsot, `00000000-0000-0000-0000-000000000000` és adja át felhasználónevének, és használja a hozzáférési jogkivonatot jelszóként:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Szolgáltatásnév

Ha egy [egyszerű szolgáltatásnevet](../active-directory/develop/app-objects-and-service-principals.md) rendel hozzá a beállításjegyzékhez, az alkalmazás vagy szolgáltatás a fej nélküli hitelesítéshez használhatja azt. Az egyszerű szolgáltatások lehetővé teszik az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) egy beállításjegyzékbe, és több egyszerű szolgáltatásnevet is hozzárendelhet egy beállításjegyzékhez. Több egyszerű szolgáltatás lehetővé teszi különböző alkalmazások eltérő hozzáférésének megadását.

A tároló-beállításjegyzékhez elérhető szerepkörök a következők:

* **AcrPull**: lekérés

* **AcrPush**: lekérés és leküldés

* **Tulajdonos**: szerepkörök lekérése, leküldése és társítása más felhasználóknak

A szerepkörök teljes listáját itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

Ahhoz, hogy CLI-parancsfájlok hozzon létre egy egyszerű szolgáltatást az Azure Container Registry használatával történő hitelesítéshez, és további útmutatást talál, lásd: [Azure Container Registry hitelesítés az egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzék tartalmaz egy rendszergazdai felhasználói fiókot, amely alapértelmezés szerint le van tiltva. Engedélyezheti a rendszergazda felhasználóját, és kezelheti a hitelesítő adatait a Azure Portal, illetve az Azure CLI vagy más Azure-eszközök használatával. A rendszergazdai fiók teljes körű engedélyekkel rendelkezik a beállításjegyzékhez.

A rendszergazdai fiókra jelenleg azért van szükség, hogy egyes esetekben lemezképet helyezzen üzembe egy tároló-beállításjegyzékből bizonyos Azure-szolgáltatásokhoz. Például a rendszergazdai fiókra akkor van szükség, amikor a Azure Portal használatával helyez üzembe egy tároló-lemezképet egy beállításjegyzékből közvetlenül a [Azure Container instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) vagy [Az Azure Web Apps for containers szolgáltatásban](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> A rendszergazdai fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. A rendszergazdai fiók hitelesítő adatait nem ajánlott több felhasználó között megosztani. A rendszergazdai fiókkal hitelesítő összes felhasználó egyetlen felhasználóként jelenik meg a beállításjegyzék leküldéses és lekéréses hozzáférésével. A fiók módosítása vagy letiltása letiltja a beállításjegyzék-hozzáférést minden olyan felhasználó számára, aki hitelesítő adatait használja. A felhasználók és a szolgáltatásokhoz egyéni identitást kell használni a fej nélküli forgatókönyvek esetében.
>

A rendszergazdai fiók két jelszóval van ellátva, amelyek közül mindkettő újra létrehozhatók. Két jelszó lehetővé teszi a beállításjegyzékhez való kapcsolódást úgy, hogy egy jelszót használ a másik létrehozásakor. Ha a rendszergazdai fiók engedélyezve van, a felhasználónevet és a jelszót is átadhatja a `docker login` parancsnak, amikor a rendszer az alapszintű hitelesítést kéri a beállításjegyzékben. Például:

```
docker login myregistry.azurecr.io 
```

A bejelentkezési hitelesítő adatok kezeléséhez ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját.

Ha engedélyezni szeretné a rendszergazda felhasználót egy meglévő beállításjegyzékben, használja az az `--admin-enabled` [ACR Update](/cli/azure/acr#az-acr-update) parancs paraméterét az Azure CLI-ben:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Engedélyezheti a rendszergazda felhasználót a Azure Portal a beállításjegyzékben navigálva, a **hozzáférési kulcsok** elemre kattintva a **Beállítások** területen, majd a **rendszergazda felhasználó** területen **engedélyezheti** .

![Rendszergazdai felhasználói felület engedélyezése a Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Következő lépések

* [Az első rendszerkép leküldése az Azure CLI használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
