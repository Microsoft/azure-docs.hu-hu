---
title: Tároló rendszerképének üzembe helyezése a Azure Container Registry
description: Megtudhatja, hogyan helyezhet üzembe tárolókat a Azure Container Instances egy Azure Container Registryből lekért tároló rendszerképekkel.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7aba107ac58538245c16f581afa2c493f546ca01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786944"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Üzembe helyezés az Azure Container Instancesben az Azure Container Registryből

[Azure Container Registry](../container-registry/container-registry-intro.md) egy Azure-alapú, felügyelt tároló-beállításjegyzék-szolgáltatás, amely privát Docker-tárolók rendszerképének tárolására szolgál. Ez a cikk azt ismerteti, hogyan lehet lekért tároló rendszerképeket tárolni egy Azure Container Registryben, amikor üzembe Azure Container Instances. A beállításjegyzék-hozzáférés konfigurálásának ajánlott módja egy Azure Active Directory szolgáltatásnév és jelszó létrehozása, valamint a bejelentkezési hitelesítő adatok tárolása egy Azure Key Vaultban.

## <a name="prerequisites"></a>Előfeltételek

**Azure Container Registry:** A cikkben található lépések befejezéséhez szüksége lesz egy Azure Container Registryre – és legalább egy tároló rendszerképére a beállításjegyzékben. Ha szüksége van egy regisztrációs adatbázisra, tekintse meg [a Tároló-beállításjegyzék](../container-registry/container-registry-get-started-azure-cli.md)létrehozása az Azure CLI használatával való használatával.

**Azure CLI:** A cikkben használt parancssori példák az [Azure CLI-t](/cli/azure/) használják, és a Bash-felülethez vannak formázva. Telepítheti [helyileg az Azure](/cli/azure/install-azure-cli) CLI-t, vagy használhatja a [Azure Cloud Shell.][cloud-shell-bash]

## <a name="limitations"></a>Korlátozások

* A tárolócsoport üzembe helyezése Azure Container Registry lekért rendszerképekhez nem hitelesíthet az ugyanabban [a](container-instances-managed-identity.md) tárolócsoportban konfigurált felügyelt identitással.
* Jelenleg nem lehet lekért rendszerképeket [Azure Container Registry](../container-registry/container-registry-vnet.md) Azure-Virtual Network üzembe helyezett virtuális gépről.

## <a name="configure-registry-authentication"></a>Regisztrációs adatbázis hitelesítésének konfigurálása

Éles környezetben, ahol hozzáférést biztosít a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz, javasoljuk, hogy konfigurálja a beállításjegyzékhez való hozzáférést egy [szolgáltatásnévvel.](../container-registry/container-registry-auth-service-principal.md) A szolgáltatásnév lehetővé [teszi, hogy Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../container-registry/container-registry-roles.md) biztosítson a tároló rendszerképe számára. Konfigurálhat például egy olyan szolgáltatásnevet, amely csak lekérés céljából férhet hozzá a regisztrációs adatbázishoz.

Azure Container Registry további hitelesítési [lehetőségeket biztosít.](../container-registry/container-registry-authentication.md)

A következő szakaszban egy Azure-kulcstartót és egy szolgáltatásnévvel fog létrehozni, és a szolgáltatásnév hitelesítő adatait a tárolóban tárolja.

### <a name="create-key-vault"></a>Kulcstároló létrehozása

Ha még nem rendelkezik tárolóval az [Azure Key Vaultban](../key-vault/general/overview.md), hozzon létre egyet az Azure CLI alábbi parancsaival.

Frissítse a változót annak a meglévő erőforráscsoportnak a nevére, amelyben létre kell hoznia a kulcstartót, valamint a tároló-beállításjegyzék `RES_GROUP` `ACR_NAME` nevét. A rövidség kedvéért a cikkben található parancsok feltételezik, hogy a regisztrációs adatbázis, a kulcstartó és a tárolópéldányok ugyanabban az erőforráscsoportban vannak létrehozva.

 Adja meg az új kulcstartó nevét a `AKV_NAME` fájlban. A tároló nevének egyedinek kell lennie az Azure-ban, és 3–24 alfanumerikus karakter hosszúságúnak kell lennie, betűvel kell kezdődnie, betűvel vagy számmal kell végződnie, és nem tartalmazhat egymást követő kötőjeleket.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Szolgáltatásnév létrehozása és a hitelesítő adatok tárolása

Most hozzon létre egy szolgáltatásnév, és tárolja a hitelesítő adatait a kulcstartóban.

Az alábbi parancs az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] parancsot használja a szolgáltatásnév létrehozásához,  az [az keyvault secret set][az-keyvault-secret-set] paranccsal pedig tárolja a szolgáltatásnév jelszavát a tárolóban.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Az előző parancs argumentuma az acrpull szerepkörrel konfigurálja az egyszerű szolgáltatást, amely csak lekért hozzáférést biztosít a `--role` beállításjegyzékhez.  Leküldési és leküldési hozzáférés megadásához módosítsa az `--role` argumentumot *acrpush -re.*

Ezután tárolja a szolgáltatásnév *appId* azonosítóját a  tárolóban, amely az a felhasználónév, amelyet a Azure Container Registry a hitelesítéshez.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Létrehozott egy Azure Key Vault-tárolót, és tárolt benne két titkos kulcsot:

* `$ACR_NAME-pull-usr`: A szolgáltatásnév azonosítója, amely a tárolóregisztrációs adatbázis **felhasználóneveként** szolgál.
* `$ACR_NAME-pull-pwd`: A szolgáltatásnév jelszava, amely a tárolóregisztrációs adatbázis **jelszavaként** szolgál.

Innentől ezekre a titkos kulcsokra név alapján hivatkozhat, amikor Ön vagy az alkalmazások és szolgáltatások rendszerképeket kérnek le a regisztrációs adatbázisból.

## <a name="deploy-container-with-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Most, hogy a szolgáltatásnév hitelesítő adatait titkos Azure Key Vault tárolja, az alkalmazások és a szolgáltatások felhasználhatja őket a privát beállításjegyzék eléréséhez.

Először szerezze be a regisztrációs adatbázis bejelentkezési kiszolgálójának nevét az [az acr show paranccsal.][az-acr-show] A bejelentkezési kiszolgáló neve csak kisbetűkből áll, és a következőhez `myregistry.azurecr.io` hasonló: .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

A következő [az container create][az-container-create] parancs végrehajtásával helyezzen üzembe egy tárolópéldányt. A parancs az Azure Key Vault-ban tárolt szolgáltatásnév hitelesítő adatait használja a tároló-beállításjegyzékben való hitelesítéshez, és feltételezi, hogy korábban már leküldte az [aci-helloworld](container-instances-quickstart.md) rendszerképet a regisztrációs adatbázisba. Frissítse az értéket, ha a `--image` beállításjegyzéktől eltérő rendszerképet szeretne használni.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Az értéknek egyedinek kell lennie az Azure-ban, ezért az előző parancs véletlenszerű számot fűz a tároló `--dns-name-label` DNS-névcímkéhez. A parancs kimenete a tároló teljes tartománynevét jeleníti meg, például:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Miután a tároló sikeresen elindult, a böngészőben a teljes tartomány tartományához navigálva ellenőrizheti, hogy az alkalmazás sikeresen fut-e.

## <a name="deploy-with-azure-resource-manager-template"></a>Üzembe helyezés Azure Resource Manager sablonnal

Az Azure Container Registry tulajdonságait egy sablonban Azure Resource Manager meg, ha a tulajdonságot a tárolócsoport `imageRegistryCredentials` definíciójában adja meg. Megadhatja például közvetlenül a beállításjegyzék hitelesítő adatait:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

A tárolócsoport teljes beállításaiért tekintse meg a Resource Manager [sablonreferenciáját.](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups)    

A titkos kulcsokra a Azure Key Vault sablonban való hivatkozásának részletei Resource Manager ért lásd: Use Azure Key Vault to pass secure parameter value during deployment (Biztonságos Azure Key Vault paraméterértékek megadása az üzembe helyezés [során).](../azure-resource-manager/templates/key-vault-parameter.md)

## <a name="deploy-with-azure-portal"></a>Üzembe helyezés Azure Portal

Ha tároló rendszerképeket tart fenn egy Azure Container Registryben, a tárolókban egyszerűen létrehozhat egy tárolót a Azure Container Instances a Azure Portal. Ha a portál használatával helyez üzembe egy tárolópéldányt egy tároló-beállításjegyzékből, engedélyeznie kell a regisztrációs adatbázis [rendszergazdai fiókját.](../container-registry/container-registry-authentication.md#admin-account) A rendszergazdai fiók egyetlen felhasználó számára lett kialakítva a regisztrációs adatbázis eléréséhez, főleg tesztelési célokra. 

1. A Azure Portal lépjen a tároló-beállításjegyzékhez.

1. A rendszergazdai fiók engedélyezésének megerősítéséhez válassza a Hozzáférési kulcsok **lehetőséget,** majd a **Rendszergazdai felhasználó alatt** válassza az Engedélyezés **lehetőséget.**

1. Válassza **az Adattárak** lehetőséget, majd válassza ki azt az adattárat, amelyből üzembe szeretné helyezni, kattintson a jobb gombbal az üzembe helyezni kívánt tároló rendszerképének címkéjére, és válassza a Példány futtatása **lehetőséget.**

    !["Példány futtatása" a Azure Container Registry a Azure Portal][acr-runinstance-contextmenu]

1. Adja meg a tároló nevét és az erőforráscsoport nevét. Ha szeretné, módosíthatja az alapértelmezett értékeket is.

    ![Létrehozás menü a Azure Container Instances][acr-create-deeplink]

1. Az üzembe helyezés befejezése után az értesítési panelen a tárolócsoportra navigálva megkeresheti annak IP-címét és egyéb tulajdonságait.

    ![A tárolócsoport Azure Container Instances nézete][aci-detailsview]

## <a name="next-steps"></a>Következő lépések

További információ a hitelesítés Azure Container Registry: [Hitelesítés Azure Container Registryvel.](../container-registry/container-registry-authentication.md)

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
