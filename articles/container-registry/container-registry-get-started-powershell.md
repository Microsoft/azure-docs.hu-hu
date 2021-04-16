---
title: Rövid útmutató – Beállításjegyzék létrehozása – PowerShell
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre privát Docker-beállításjegyzéket Azure Container Registry PowerShell használatával
ms.date: 01/22/2019
ms.topic: quickstart
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: bd9b93e22081c43dfa3fd934f13da3713120aadb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537380"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Rövid útmutató: Privát tároló-beállításjegyzék létrehozása a Azure PowerShell

Az Azure Container Registry egy felügyelt, privát Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a Docker-tárolók rendszerképeinek létrehozására, tárolására és kiszolgálására szolgál. Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Azure-tárolóregisztrációs adatbázist a PowerShell használatával. Ezután Docker-parancsokkal leküld egy tároló rendszerképét a regisztrációs adatbázisba, majd végül leküldi és futtatja a rendszerképet a regisztrációs adatbázisból.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz Azure PowerShell modulra. Futtassa a `Get-Module -ListAvailable Az` parancsot a telepített verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker [macOS][docker-mac], [Windows][docker-windows] és [Linux][docker-linux] operációs rendszerekhez biztosít csomagokat.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [Connect-AzAccount][Connect-AzAccount] paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Miután hitelesítette magát az Azure-ral, hozzon létre egy erőforráscsoportot a [New-AzResourceGroup használatával.][New-AzResourceGroup] Az erőforráscsoport egy olyan logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezután hozzon létre egy tároló-beállításjegyzéket az új erőforráscsoportban a [New-AzContainerRegistry paranccsal.][New-AzContainerRegistry]

A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában egy „myContainerRegistry007” nevű adatbázis jön létre. Írja be a *myContainerRegistry007* kifejezést az alábbi parancsba, majd futtassa azt az adatbázis létrehozásához:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Ebben a rövid útmutatóban létrehoz egy *Alapszintű* beállításjegyzéket, amely egy költségoptimalált lehetőség a fejlesztők számára a Azure Container Registry. Az elérhető szolgáltatásszintekkel kapcsolatos részletekért lásd: [Container Registry service tiers (Tároló-beállításjegyzék szolgáltatási rétegei).][container-registry-skus]

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie a beállításjegyzékbe. A rövid útmutató röviden való tartásához engedélyezze a rendszergazdai felhasználót a beállításjegyzékben a [Get-AzContainerRegistryCredential paranccsal.][Get-AzContainerRegistryCredential] Éles forgatókönyvekben alternatív hitelesítési [](container-registry-authentication.md) módszert kell használnia a beállításjegyzék-hozzáféréshez, például egy szolgáltatásnévhez. 

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Ezután a bejelentkezéshez futtassa a [docker login][docker-login] parancsot:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

> [!TIP]
> Az Azure CLI biztosítja a parancsot, amely kényelmes módja a tároló-beállításjegyzékbe való bejelentkezésnek az egyéni `az acr login` identitásával, docker hitelesítő adatok átadása [](container-registry-authentication.md#individual-login-with-azure-ad)nélkül.


[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett az ebben a rövid útmutatóban létrehozott erőforrásokkal, a [Remove-AzResourceGroup][Remove-AzResourceGroup] paranccsal távolítsa el az erőforráscsoportot, a tároló-beállításjegyzéket és az ott tárolt tároló rendszerképeket:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry a Azure PowerShell, leküldte a tároló rendszerképét, majd lekérte és futtatta a rendszerképet a beállításjegyzékből. Folytassa az Azure Container Registry oktatóanyagokkal, hogy mélyebben is megnézzük az ACR-t.

> [!div class="nextstepaction"]
> [Azure Container Registry oktatóanyagok][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry-feladatok oktatóanyagok][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
