---
title: Beállításjegyzék-hitelesítési beállítások
description: Hitelesítési lehetőségek privát Azure-beli tároló-beállításjegyzékhez, beleértve a Azure Active Directory-identitással való bejelentkezést, a szolgáltatásnév használatát és az opcionális rendszergazdai hitelesítő adatok használatát.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 7ff55d569e2659262ce9f323e4db2ea7ed671d20
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784280"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Hitelesítés Azure Container Registryvel

Az Azure-beli tároló-beállításjegyzékekkel való hitelesítésnek több módja is van, amelyek egy vagy több regisztrációs adatbázis-használati forgatókönyvre vonatkoznak.

Az ajánlott módszerek közé tartozik a [](#individual-login-with-azure-ad)regisztrációs adatbázisba való közvetlen hitelesítés egyéni bejelentkezéssel, vagy az alkalmazások és tárolóvezénylések felügyelet nélküli vagy "fej nélküli" hitelesítést végezhetnek egy Azure Active Directory -szolgáltatásnév (Azure AD) [használatával.](#service-principal)

## <a name="authentication-options"></a>Hitelesítési lehetőségek

Az alábbi táblázat az elérhető hitelesítési módszereket és tipikus forgatókönyveket sorolja fel. További részleteket a csatolt tartalomban talál.

| Metódus                               | Hitelesítés                                           | Forgatókönyvek                                                            | Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)                             | Korlátozások                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Egyéni AD-identitás](#individual-login-with-azure-ad)                | `az acr login` az Azure CLI-ban                             | Interaktív leküldés/leküldés fejlesztők és tesztelők által                                    | Yes                              | Az AD-jogkivonatot 3 óránként kell megújítani     |
| [AD-szolgáltatásnév](#service-principal)                  | `docker login`<br/><br/>`az acr login` az Azure CLI-ban<br/><br/> Regisztrációs adatbázis bejelentkezési beállításai API-kban vagy eszközökben<br/><br/> [Kubernetes – titkos lekért titkos tok](container-registry-auth-kubernetes.md)                                           | Felügyelet nélküli leküldés CI-/CD-folyamatból<br/><br/> Felügyelet nélküli leküldés az Azure-ba vagy külső szolgáltatásokba  | Yes                              | Az SP-jelszó alapértelmezett lejárata 1 év       |                                                           
| [Integrálás az AKS-sel](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Beállításjegyzék csatolása az AKS-fürt létrehozásakor vagy módosításakor  | Felügyelet nélküli leküldés az AKS-fürthöz                                                  | Nem, csak lekért hozzáférés             | Csak AKS-fürtön érhető el            |
| [Az Azure-erőforrásokhoz készült felügyelt identitások](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` az Azure CLI-ban                                       | Felügyelet nélküli leküldés az Azure CI-/CD-folyamatból<br/><br/> Felügyelet nélküli leküldés az Azure-szolgáltatásokba<br/><br/>   | Yes                              | Csak az Azure-erőforrások felügyelt identitását támogató Azure-szolgáltatások [közül használja](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Rendszergazdai felhasználó](#admin-account)                            | `docker login`                                          | Egyéni fejlesztő vagy tesztelő által leküldéses/leküldéses interaktív leküldés<br/><br/>Rendszerkép üzembe helyezése a regisztrációs adatbázisból a Azure App Service vagy a Azure Container Instances                      | Nem, mindig leküldéses és leküldéses hozzáférés  | Beállításjegyzékenként egy fiók, nem ajánlott több felhasználó számára         |
| [Adattárhatókörű hozzáférési jogkivonat](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` az Azure CLI-ban   | Interaktív leküldés/leküldés az adattárba egyéni fejlesztő vagy tesztelő által<br/><br/> Felügyelet nélküli leküldés/leküldés az adattárba önálló rendszer vagy külső eszköz alapján                  | Yes                              | Jelenleg nincs integrálva az AD-identitással  |

## <a name="individual-login-with-azure-ad"></a>Egyéni bejelentkezés az Azure AD-val

Ha közvetlenül a regisztrációs adatbázissal dolgozik, például rendszerképeket kér le egy fejlesztői munkaállomásra, és lekérte a rendszerképeket egy ön által létrehozott regisztrációs adatbázisba, az egyéni Azure-identitásával kell hitelesítenie magát. Jelentkezzen be az [Azure CLI-be](/cli/azure/install-azure-cli) [az az login paranccsal,](/cli/azure/reference-index#az_login)majd futtassa az [az acr login](/cli/azure/acr#az_acr_login) parancsot:

```azurecli
az login
az acr login --name <acrName>
```

Amikor bejelentkezik a használatával, a CLI a végrehajtáskor létrehozott jogkivonatot használja a munkamenet zökkenőmentes hitelesítéséhez a `az acr login` `az login` beállításjegyzékben. A hitelesítési folyamat befejezéséhez telepítenie kell a Docker CLI-t és a Docker-démont, és futnia kell a környezetben. `az acr login` A a Docker-ügyfelet használja egy Azure Active Directory jogkivonat beállításhoz a `docker.config` fájlban. Miután így bejelentkezett, a rendszer gyorsítótárazza a hitelesítő adatokat, és a munkamenet későbbi parancsai nem igényelnek felhasználónevet vagy `docker` jelszót.

> [!TIP]
> Akkor is hitelesítheti az egyéni identitást, ha Docker-rendszerképeken kívül más összetevőt szeretne leküldni a regisztrációs adatbázisba, például `az acr login` [az OCI-összetevőkbe.](container-registry-oci-artifacts.md)  

A beállításjegyzékhez való hozzáféréshez a által használt jogkivonat 3 óráig érvényes, ezért javasoljuk, hogy mindig jelentkezzen be a beállításjegyzékbe, mielőtt `az acr login` futtat egy  `docker` parancsot. Ha a jogkivonat lejár, az újrahitelesítő paranccsal `az acr login` frissítheti. 

Az `az acr login` Azure-identitásokkal való használat [azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít.](../role-based-access-control/role-assignments-portal.md) Bizonyos esetekben előfordulhat, hogy be szeretne jelentkezni egy regisztrációs adatbázisba a saját egyéni identitásával az Azure AD-ban, vagy konfigurálni más Azure-felhasználókat adott Azure-szerepkörökkel és [-engedélyekkel.](container-registry-roles.md) A szolgáltatások közötti forgatókönyvekhez, illetve egy munkacsoport vagy fejlesztési munkafolyamat igényeinek kezeléséhez, ahol nem szeretné kezelni az egyéni hozzáférést, azure-erőforrások felügyelt identitásával is [bejelentkezhet.](container-registry-authentication-managed-identity.md)

### <a name="az-acr-login-with---expose-token"></a>az acr login with --expose-token

Bizonyos esetekben szükség lehet a hitelesítésre a -nal, ha a Docker-démon nem fut a `az acr login` környezetben. Előfordulhat például, hogy egy szkriptben kell futtatnia az Azure Cloud Shell, amely biztosítja a Docker CLI-t, de nem futtatja `az acr login` a Docker-démont.

Ebben a forgatókönyvben először `az acr login` futtassa a(z) `--expose-token` paramétert. Ez a beállítás elérhetővé teszi a hozzáférési jogkivonatot ahelyett, hogy a Docker CLI-ről jelentkezik be.

```azurecli
az acr login --name <acrName> --expose-token
```

A kimenet a hozzáférési jogkivonatot jeleníti meg itt rövidítve:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
A beállításjegyzék-hitelesítéshez javasoljuk, hogy a jogkivonat hitelesítő adatait tárolja biztonságos helyen, és kövesse az ajánlott eljárásokat a [docker login](https://docs.docker.com/engine/reference/commandline/login/)) hitelesítő adatainak kezeléséhez. Például tárolja a jogkivonat értékét egy környezeti változóban:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Ezután futtassa a `docker login` következőt: , adja `00000000-0000-0000-0000-000000000000` meg a felhasználónevet, és használja a hozzáférési jogkivonatot jelszóként:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Szolgáltatásnév

Ha szolgáltatásnévvel [rendeli](../active-directory/develop/app-objects-and-service-principals.md) hozzá a regisztrációs adatbázist, az alkalmazás vagy szolgáltatás használhatja azt a fej nélküli hitelesítéshez. A szolgáltatásnév lehetővé teszi [az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) egy regisztrációs adatbázishoz, és több szolgáltatásnév is hozzárendelható egy beállításjegyzékhez. Több egyszerű szolgáltatás lehetővé teszi különböző alkalmazások különböző hozzáférésének definiálát.

A tároló-beállításjegyzékek elérhető szerepkörei a következők:

* **AcrPull:** lekért

* **AcrPush:** leküldés és leküldés

* **Tulajdonos:** szerepkörök leküldése, leküldése és hozzárendelése más felhasználókhoz

A szerepkörök teljes listáját a szerepkörök és engedélyek [Azure Container Registry lásd:](container-registry-roles.md).

Az Azure Container Registryvel való hitelesítéshez szükséges szolgáltatásnév létrehozására szolgáló CLI-szkriptekkel és további útmutatással kapcsolatos további információkért lásd: Azure Container Registry szolgáltatásnévvel való [hitelesítés.](container-registry-auth-service-principal.md)

## <a name="admin-account"></a>Rendszergazdai fiók

Minden tároló-beállításjegyzék tartalmaz egy rendszergazdai felhasználói fiókot, amely alapértelmezés szerint le van tiltva. Engedélyezheti a rendszergazdai felhasználót, és kezelheti a hitelesítő adatait a Azure Portal vagy az Azure CLI vagy más Azure-eszközök használatával. A rendszergazdai fiók teljes körű engedélyekkel rendelkezik a beállításjegyzékhez.

A rendszergazdai fiók jelenleg bizonyos forgatókönyvekhez szükséges ahhoz, hogy rendszerképet helyez üzembe egy tároló-beállításjegyzékből bizonyos Azure-szolgáltatásokban. A rendszergazdai fiókra például akkor van szükség, amikor a Azure Portal használatával helyez üzembe egy tároló rendszerképét egy regisztrációs adatbázisból közvetlenül a Azure Container Instances [vagy](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) az Azure Web Apps [for Containers szolgáltatásban.](container-registry-tutorial-deploy-app.md)

> [!IMPORTANT]
> A rendszergazdai fiók egyetlen felhasználó számára lett kialakítva a regisztrációs adatbázis eléréséhez, főleg tesztelési célokra. Nem javasoljuk a rendszergazdai fiók hitelesítő adatainak több felhasználó közötti megosztását. A rendszergazdai fiókkal hitelesítő összes felhasználó egyetlen felhasználóként jelenik meg leküldéses és leküldéses hozzáféréssel a regisztrációs adatbázishoz. A fiók módosítása vagy letiltása letiltja a beállításjegyzékhez való hozzáférést az összes olyan felhasználó számára, aki a hitelesítő adatait használja. A felhasználók és szolgáltatásnévk számára javasolt az egyéni identitás használata a fej nélküli forgatókönyvekhez.
>

A rendszergazdai fiók két jelszóval érhető el, mindkettő újragenerálható. Két jelszóval fenntarthatja a kapcsolatot a beállításjegyzékhez úgy, hogy az egyik jelszót használja a másik újragenerálása közben. Ha a rendszergazdai fiók engedélyezve van, a felhasználónév és a jelszó is átadható a parancsnak, amikor a rendszer alapszintű hitelesítést kér a `docker login` beállításjegyzékhez. Például:

```
docker login myregistry.azurecr.io 
```

A bejelentkezési hitelesítő adatok kezeléséhez ajánlott eljárásokért tekintse meg a [docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját.

Ha engedélyezni szeretné a rendszergazdai felhasználót egy meglévő regisztrációs adatbázishoz, használhatja az az acr update parancs paraméterét az `--admin-enabled` Azure CLI-ban: [](/cli/azure/acr#az_acr_update)

```azurecli
az acr update -n <acrName> --admin-enabled true
```

A rendszergazdai felhasználó engedélyezéséhez navigálhat a beállításjegyzékben Azure Portal a BEÁLLÍTÁSOK alatt válassza a Hozzáférési **kulcsok,** majd **az** Engedélyezés lehetőséget a  **Rendszergazdai felhasználó alatt.**

![Rendszergazdai felhasználói felület engedélyezése a Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Következő lépések

* [Az első rendszerkép leküldése az Azure CLI használatával](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
